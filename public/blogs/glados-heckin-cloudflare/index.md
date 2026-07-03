# GLaDOS Heckin Cloudflare：把签到任务放到边缘网络

创建时间：2026-06-27 09:07:24 UTC  
仓库：<https://github.com/wwwqqq001/Glados-heckin-cloudflare>  
状态：私有仓库

这是一个部署在 Cloudflare Workers 上的 Python 自动签到服务。

我把它看成一个“小型边缘定时任务”实验：不需要服务器，不需要常驻进程，不需要自己维护 crontab，只要把核心逻辑放进 Worker，再让 Cloudflare Cron 定时触发。

## 项目定位

这个项目解决的是一件固定频率的小任务：

- 定时触发签到。
- 调用目标 API。
- 汇总账号状态。
- 通过企业微信机器人推送结果。

它的价值不在页面有多复杂，而在于把一个需要持续运行的小服务，压缩到 Cloudflare 的免费或低成本边缘环境里。

## 架构

核心结构可以拆成三层：

- `entry.py`：Worker 入口，负责 `fetch()` 和 `scheduled()`。
- `checkin.py`：签到、状态查询、兑换和推送逻辑。
- `wrangler.toml`：Worker 名称、兼容日期、Cron 和变量配置。

敏感配置使用 Cloudflare Secrets，而不是写进仓库：

- Cookie
- Webhook Key
- 账号相关配置

## 为什么用 Python Workers

Cloudflare Workers 常见写法是 TypeScript，但 Python Workers 很适合这种脚本类任务。原本在本地写脚本的逻辑，可以用更少的心智成本迁移到边缘环境。

我在这个项目里更关注这些问题：

- Python 运行时的启动和依赖边界。
- Worker 里如何发起 HTTP 请求。
- Cron 触发和手动触发如何复用同一套逻辑。
- Secrets 和 Vars 的职责怎么分。

## 实践收获

这个项目让我确认了一件事：很多个人自动化服务不需要完整后端。

只要任务满足这些条件，就很适合放在 Workers：

- 运行时间短。
- 状态很少。
- 可以通过环境变量或 KV 管理配置。
- 失败后能通过通知发现。

## 展示说明

仓库是私有的，链接只作为项目记录。文章不会公开任何 Cookie、Webhook、Token 或真实账号信息。
