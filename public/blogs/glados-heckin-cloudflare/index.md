# GLaDOS Heckin Cloudflare：把签到任务放进 Workers Cron

创建时间：2026-06-27 09:07:24 UTC
仓库：<https://github.com/wwwqqq001/Glados-heckin-cloudflare>
运行位置：Cloudflare Workers Python / Cron Triggers
状态：私有仓库

这是一个运行在 Cloudflare Workers 上的 Python 自动签到服务。原本这类脚本通常要放在服务器、NAS 或本地定时器里跑；我把它迁到 Workers Cron，是为了减少常驻环境，把任务变成“到点触发、执行完退出”的边缘函数。

![GLaDOS Cloudflare Workers 架构图](/blogs/glados-heckin-cloudflare/architecture.svg)

图里展示的是项目边界：触发可以来自 Cron，也可以来自手动请求；业务逻辑在 Python Worker 里执行；敏感配置不进入仓库；结果通过通知渠道返回。图中不包含真实 Cookie、Webhook 或账号信息。

## 快速了解

| 项目项 | 内容 |
| --- | --- |
| 类型 | 自动化脚本 / 边缘定时任务 |
| 核心技术 | Cloudflare Workers Python、Cron、Secrets |
| 触发方式 | 定时触发 / 手动请求 |
| 配置方式 | Cloudflare Secrets 和 Vars |
| 结果反馈 | 企业微信机器人推送 |

## 任务目标

这个项目只解决一件固定频率的小任务，但它把触发、配置和通知边界拆得比较清楚：

- 按计划触发签到。
- 调用目标接口。
- 汇总账号状态。
- 把运行结果推送出来。

它的价值不在界面，而在部署模型：不需要传统服务器，不需要常驻进程，也不需要自己维护 crontab。

## 我做了什么

项目核心结构可以拆成三层：

- `entry.py`：Worker 入口，处理 `fetch()` 和 `scheduled()`。
- `checkin.py`：签到、状态查询、兑换和推送逻辑。
- `wrangler.toml`：Worker 名称、兼容日期、Cron 和环境变量配置。

`scheduled()` 和手动请求复用同一套业务逻辑。这样定时运行和临时调试不会分裂成两套代码。

## 凭据怎么放

敏感配置放在 Cloudflare Secrets，而不是写进仓库：

- Cookie
- Webhook Key
- 账号相关配置

普通变量和敏感变量分开管理。博客只写配置类别，不写真实值。

## 为什么用 Workers Python

Cloudflare Workers 常见写法是 TypeScript，但这个项目选择 Python，是因为签到逻辑更接近脚本任务。Python Workers 能让本地脚本迁移到边缘环境时少改一些心智模型。

代价是要关注 Python 运行时边界：依赖支持、启动速度、请求限制和 Cron 调试方式都需要提前验证。

## 跑下来得到的结论

这个项目验证了一个判断：很多个人自动化服务不需要完整后端。只要任务运行时间短、状态很少、配置能放进环境变量或 Secrets，Workers Cron 就足够承载。

## 展示边界

仓库是私有的，链接只作为项目记录。文章不会公开任何 Cookie、Webhook、Token、账号信息或 Cloudflare 控制台配置。
