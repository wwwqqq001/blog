# Hyperdown Workers：把签到做成可观测边缘服务

创建时间：2026-06-25 起，持续维护
仓库：<https://github.com/wwwqqq001/hyper-checkin-cloudflare>
运行位置：Cloudflare Workers Python / KV
状态：私有仓库，已接入统一调度平台

Hyperdown 签到最初可以是一个 GitHub Actions 脚本。迁到 Cloudflare Workers 之后，我更关心三件事：到点能跑、页面能看状态、结果能被平台统一汇总。这个项目现在是调度平台的一个业务 Handler，而不是各自为政的独立 Cron。

![Hyperdown Python Worker 架构](/blogs/hyper-checkin-cloudflare/architecture.svg)

## 快速了解

| 项目项 | 内容 |
| --- | --- |
| 类型 | 自动化脚本 / 边缘业务 Handler |
| 核心技术 | Python Workers、KV、Secrets、Service Binding |
| 触发方式 | 统一平台优先；保留手动面板调试入口 |
| 状态存储 | KV 保存展示状态与公告相关数据 |
| 结果反馈 | 标准 payload 回平台，通知由平台发送 |

## 任务目标

这个项目只做 Hyperdown 相关的固定动作：

- 多账号签到。
- 读取并展示账号状态。
- 监控公告变化。
- 把结果整理成平台能聚合的结构。

它不负责全局调度，也不负责企业微信正式通知。调度和通知上收到 `checkin-cron-worker` 之后，这个仓库可以更专注业务本身。

## 我做了什么

核心拆分：

- `entry.py`：HTTP 路由、面板渲染、平台接入入口。
- `checkin.py`：登录态请求、签到、公告、结果整理。
- `wrangler.toml`：Worker 名称、兼容标志、KV 绑定。

选择 Python Workers，是因为业务逻辑更像脚本：顺序请求、解析 JSON、拼结果。代价是要接受 Python 边缘运行时的限制，例如依赖打包和 Windows 上 `pywrangler` 不便用，需要手动准备 `python_modules/`。

面板方面，我把最近状态写进 KV，让页面打开时不必每次都打完整业务 API。这对“看一眼今天有没有成功”这种高频操作更省事。

## 配置与安全边界

- 账号配置放在 Secrets。
- Webhook 相关密钥只给平台或本地调试使用，不写进仓库。
- 博客只写配置类别，不写真实值。
- 平台通过 Service Binding 调用时，业务应直接返回结果，避免重复回调造成双写。

## 失败处理

业务失败要能被平台理解：

- 可重试：网络抖动、短暂接口错误。
- 不可重试：凭据失效、明确业务拒绝。
- 警告：公告更新等需要即时提醒、但不算签到失败的情况。

公告任务和签到任务拆开，是为了避免“看公告失败”污染“签到成功”的日报语义。

## 适合继续改进的地方

- 更细的账号级错误码。
- 面板上的最近 N 次运行摘要。
- 与平台 compact 日报字段继续对齐。

## 展示边界

仓库是私有的。文章不公开账号、密码、Cookie、Webhook、Token 或真实运行日志。
