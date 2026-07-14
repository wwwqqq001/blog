# checkin-cron-worker：把分散签到收成统一调度平台

创建时间：2026-07-02 10:49 UTC（后续持续迭代）
仓库：<https://github.com/wwwqqq001/checkin-cron-worker>
运行位置：Cloudflare Workers / D1 / Cron Triggers
状态：私有仓库，线上运行中

多个签到和续期任务曾经分散在 GitHub Actions 与不同 Worker 里，通知各自推送，状态无法统一对账。`checkin-cron-worker` 的目标不是再写一个签到脚本，而是做一个调度中枢：负责到期扫描、触发执行、接收结果、重试失败，以及生成每日聚合日报。

![统一调度平台主链路](/blogs/checkin-cron-worker/architecture.svg)

图里只展示公开可说的边界：Cron 触发平台，平台写 D1，再分别调度 GitHub Actions 或业务 Worker，最后用 Webhook 回传结构化结果。图中不含真实 Token、Webhook 或账号。

## 快速了解

| 项目项 | 内容 |
| --- | --- |
| 类型 | 原创项目复盘 / 平台工程 |
| 核心技术 | TypeScript、Cloudflare Workers、D1、Cron、Service Binding |
| 触发方式 | 每 5 分钟 Cron 扫描 + 管理后台手动触发 |
| 执行通道 | `github_workflow` / `cloudflare_handler` / `webhook_only` |
| 结果反馈 | 统一 Webhook payload → 企业微信即时通知与日报 |
| 协作方式 | 规格文档驱动，AI 辅助实现，本人负责架构取舍与线上验收 |

## 问题背景

个人自动化做多了之后，真正拖累维护的不是“某个接口怎么调”，而是这些横切问题：

- 每个仓库各自管 Cron，时间一乱就不知道谁跑了、谁没跑。
- 企业微信机器人密钥散落在多个 Secrets 里，文案格式也不统一。
- 失败要靠翻 Actions 日志，缺少统一的运行状态表。
- 同一任务当天手动测一次、定时再跑一次，日报会被重复内容淹没。

所以平台第一版定了几条硬边界：

- 只用一个 Cloudflare Cron Trigger。
- 默认 `DISPATCH_MODE=direct`，不引入 Queue 或 Durable Object。
- D1 作为唯一业务状态中心。
- 业务脚本不再直接推送正式通知。

## 我的实现

平台是单 Worker 双入口：

- `scheduled()`：扫到期任务、到期重试、超时检查、日报发送。
- `fetch()`：管理 API、Webhook 接收、Admin 页面、Wiki。

任务类型分三条执行通道：

1. **GitHub Actions**：适合 Selenium、复杂依赖、长时脚本。
2. **Cloudflare Handler**：适合短时 HTTP 签到；优先 Service Binding，公网 URL 作备用。
3. **Webhook Only**：只接收外部上报，不主动触发。

D1 至少覆盖这些对象：

- `jobs`：任务定义、调度、执行器、重试与通知策略。
- `job_runs`：每次运行、attempt、超时与重试链。
- `webhook_events`：业务回传事件。
- `daily_reports`：按日聚合日报。
- `notification_logs`：通知发送记录与去重。

业务侧需要返回结构化字段，而不是一大段自由文本。常见字段包括 `summary`、`accounts`、`reward`、`balance`、`roles` 和脱敏后的 `details_markdown`。这样日报可以直接算成功数和收益，而不是再解析聊天文案。

## 关键取舍

**为什么不用 Queue？**  
当前线上任务大约十个量级。Queue 会把链路变长，调试时还要同时看生产者、消费者和死信。第一版优先保证“看 D1 就能定位”。

**为什么通知上收？**  
如果每个业务仓库都推企业微信，密钥复制、文案风格、失败告警规则都会分叉。平台统一通知后，业务只关心“执行是否成功、结果如何结构化”。

**为什么保留 GitHub Actions？**  
不是所有任务都适合 Workers。浏览器自动化、长依赖、重试截图这类任务，继续放在 Actions 更合适；平台只负责触发和收结果。

## 线上接入现状

当前由平台统一调度的任务包括：

| 任务 | 执行方式 | 说明 |
| --- | --- | --- |
| 天翼云盘 | GitHub Actions | 容量奖励签到 |
| 雨云 | GitHub Actions | 浏览器自动化 |
| 土豆公益 | GitHub Actions | 社区任务 + 题库维护 |
| Katabump 续期 | GitHub Actions | 服务器续期 |
| 米游社 | Service Binding | 原神 / 绝区零 |
| Hyperdown 签到 | Service Binding | Python Worker |
| GLaDOS | Service Binding | 签到 + 兑换策略 |
| Hyperdown 公告 | Service Binding | 新公告即时告警 |

这些业务文章会单独记录；本篇只讲平台本身。

## 工程化与 AI 协作

这个仓库从一开始就按“可被 AI 继续改”来组织：

- `docs/` 下放架构、Webhook、状态机、错误码、脚本适配提示词等规格。
- 业务改造优先喂 `SCRIPT_ADAPTATION_PROMPT.md`，而不是让模型自由改中枢。
- 本地有 typecheck 与 Vitest；默认 `DRY_RUN` 避免误触发真实任务。
- 私有备份仓用分支隔离各业务代码，并用 `age` 加密 env 包，明文不进 Git。

我的分工是：定边界、审契约、跑验收、看线上日报和重试链。实现阶段大量使用 AI 辅助编码与文档迭代，不把“是否手写每一行”当成价值标准。

## 运行结果

平台上线后，日常维护从“打开多个仓库看 Actions”变成：

1. 看 Admin 概览：启用任务、运行中、异常数。
2. 看运行反馈：目标、HTTP 状态、耗时、脱敏响应摘要。
3. 看日报：同一任务当天多结果会择优展示，失败后恢复成功也会体现。
4. 看诊断：重试链和通知记录。

这套结果对简历和面试的意义，不只是“自动签到”，而是证明我能把横切能力做成可运维的小平台。

## 后续计划

- 把 compact 日报继续打磨到“账号级收益一眼可读”。
- 给公开演示准备脱敏数据与架构图，避免只能看私有仓。
- 若任务量和 Webhook 流量继续增长，再评估是否拆分 scheduler 与 webhook Worker。

## 展示边界

仓库是私有的。文章不公开 Admin Token、Webhook Token、GitHub Token、Cookie、账号、企业微信完整 URL 或任何 Cloudflare 控制台配置值。若 GitHub 链接返回 404，说明仓库未公开。
