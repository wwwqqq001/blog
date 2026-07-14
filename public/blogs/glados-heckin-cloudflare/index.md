# GLaDOS Heckin Cloudflare：边缘签到 Handler 与兑换策略

创建时间：2026-06-27 09:07:24 UTC
仓库：<https://github.com/wwwqqq001/Glados-heckin-cloudflare>
运行位置：Cloudflare Workers Python
状态：私有仓库，已接入统一调度平台

这是一个运行在 Cloudflare Workers Python 上的 GLaDOS 签到服务。第一版用 Workers Cron 独立触发；接入 `checkin-cron-worker` 之后，线上主调度改由平台 Service Binding 触发，本仓库专注签到、积分流水解析和兑换策略。

![GLaDOS Cloudflare Workers 架构图](/blogs/glados-heckin-cloudflare/architecture.svg)

图里展示的是项目边界：触发可以来自平台或兼容入口；业务逻辑在 Python Worker 里执行；敏感配置不进入仓库；正式通知由平台统一发送。图中不包含真实 Cookie、Webhook 或账号信息。

## 快速了解

| 项目项 | 内容 |
| --- | --- |
| 类型 | 自动化脚本 / 边缘业务 Handler |
| 核心技术 | Cloudflare Workers Python、Secrets、Service Binding |
| 触发方式 | 统一平台优先；保留兼容入口 |
| 配置方式 | Cloudflare Secrets 和 Vars |
| 结果反馈 | 标准 payload 回平台，通知由平台发送 |

## 任务目标

这个项目只解决 GLaDOS 侧的固定动作：

- 按账号执行签到。
- 从积分流水提取本次奖励。
- 在剩余天数较低时按积分自动选择兑换计划。
- 把多账号结果整理成平台可汇总的结构。

它的价值不在界面，而在部署模型和策略边界：短任务、低状态、配置进 Secrets、结果可对账。

## 我做了什么

项目核心结构可以拆成三层：

- `entry.py`：Worker 入口，处理平台调用和兼容触发。
- `checkin.py`：签到、状态查询、积分流水、兑换决策。
- `wrangler.toml`：Worker 名称、兼容日期和环境配置。

兑换策略示例：

- 剩余天数较高时跳过兑换。
- 剩余天数较低时按积分从高到低尝试可选计划。
- 积分不足则明确记录“跳过原因”，而不是静默失败。

奖励优先从积分流水里读取签到对应记录；取不到时再用签到前后积分差兜底。这样日报里的“+N 积分”更接近真实流水，而不是猜测。

## 凭据怎么放

敏感配置放在 Cloudflare Secrets，而不是写进仓库：

- Cookie
- 平台 Webhook 相关配置
- 账号相关配置

普通变量和敏感变量分开管理。博客只写配置类别，不写真实值。

## 为什么用 Workers Python

Cloudflare Workers 常见写法是 TypeScript，但这个项目选择 Python，是因为签到逻辑更接近脚本任务。Python Workers 能让本地脚本迁移到边缘环境时少改一些心智模型。

代价是要关注 Python 运行时边界：依赖支持、启动速度、请求限制和调试方式都需要提前验证。当前业务保持零外部依赖，降低打包复杂度。

## 和统一平台的关系

接入平台之后：

- 不再把业务 Worker 自己的 Cron 当作线上主调度。
- 不再由业务 Worker 直接发送正式企业微信通知。
- 平台负责触发、重试、去重和日报。
- 业务负责把 `summary`、`accounts`、`reward`、`balance` 等字段填完整。

完整平台说明见：[checkin-cron-worker](/blog/checkin-cron-worker)。

## 跑下来得到的结论

很多个人自动化服务不需要完整后端。只要任务运行时间短、状态很少、配置能放进 Secrets，Workers 就足够承载。真正决定可维护性的，是结果能不能结构化，以及调度和通知会不会散落在每个仓库里。

## 展示边界

仓库是私有的，链接只作为项目记录。文章不会公开任何 Cookie、Webhook、Token、账号信息或 Cloudflare 控制台配置。
