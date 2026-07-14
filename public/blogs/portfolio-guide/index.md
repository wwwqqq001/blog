# 作品集导读：这些项目怎么读、怎么连在一起

创建时间：2026-07-14
状态：公开导读，持续更新

博客里既有平台、也有脚本、也有游戏小工具。如果按发布时间乱读，很容易看成“一堆零散自动化”。这篇只做地图：先看哪篇、它们如何分层、什么不在作品集主叙事里。

## 建议阅读顺序

1. **本篇导读**  
   建立分层，不先陷入单个站点细节。
2. **[checkin-cron-worker](/blog/checkin-cron-worker)**  
   主项目。统一调度、状态、重试、日报。
3. **业务 Handler 三选一或全看**  
   - [Hyperdown Workers](/blog/hyper-checkin-cloudflare)  
   - [米游社自动签到](/blog/mihoyo-auto-checkin)  
   - [GLaDOS Heckin Cloudflare](/blog/glados-heckin-cloudflare)
4. **算法与产品小品**  
   - [异色生蛋助手](/blog/rocokingdom-egg)  
   - [PocketLedger](/blog/pocket-ledger-ios)
5. **历史与部署记录**  
   Cloudflare Pages 占位页、早期入口、Minecraft 模组等，作为资产清单，不抢主叙事。

## 分层方式

```text
平台层
  checkin-cron-worker
    ├── 调度 / D1 状态 / Webhook / 日报 / Admin

业务层
  GitHub Actions 任务：天翼云盘、雨云、土豆、Katabump...
  Cloudflare Handler：米游社、Hyperdown、GLaDOS、公告监控

作品层
  算法工具：异色生蛋助手
  移动应用：PocketLedger
  邮件 / Pages / 模组：补充工程广度
```

主线要证明的能力是：

- 把横切问题抽成平台。
- 用结构化结果而不是聊天文本做汇总。
- 在 Serverless 约束下完成可运维交付。

支线要证明的能力是：

- 约束优化与可视化。
- 本地数据统计。
- 不同运行时（TS Worker、Python Worker、Actions、iOS）都能落地。

## 关于 AI 协作

这些项目的实现大量使用 AI 辅助编码。博客和简历统一采用这个口径：

> 本人负责目标、架构边界、密钥与线上运维；核心模块在 AI 辅助下迭代；由我对取舍、联调和生产稳定性负责。

这不是免责声明，而是工作方式说明。规格文档、适配提示词、dry-run、日报验收，都是为了让 AI 改动可约束、可回滚、可检查。

## 安全与合规边界

公开文章遵守这些规则：

- 不写真实 Cookie、Token、Webhook、账号、完整邮箱或手机号。
- 私有仓链接若 404，属于预期。
- 自动化项目写成工程结构与边界，不写成教人绕过风控的教程。
- Fork 项目会标明 fork / 自托管实践，不把上游功能说成完全自研。

## 和简历怎么对应

| 简历位置 | 对应文章 |
| --- | --- |
| 主项目 | checkin-cron-worker |
| 支撑项目 | Hyperdown / 米游社 / GLaDOS |
| 算法副项目 | 异色生蛋助手 |
| 可选小品 | PocketLedger |
| 工程化补充 | 本导读 + 各文“展示边界” |

如果你只看一篇，看调度平台。  
如果你看两篇，再加异色生蛋助手。  
如果你要理解系统全貌，按上面的顺序读完平台层和业务层即可。
