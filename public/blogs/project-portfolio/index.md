# 项目作品集

这个博客先作为项目作品集使用，记录我做过、改过、部署过的工具和服务。

目前重点放三类内容：

- **Cloudflare 与自动化**：Workers、Cron、KV、Pages、GitHub Actions、消息通知。
- **Minecraft 模组**：Forge / NeoForge、多版本维护、构建发布和兼容适配。
- **个人工具与自托管实践**：静态前端、文件管理、邮件管理和运行状态整理。

## 重点项目

### Beyond Storage Addon All

Beyond Dimensions 附属模组的多版本发布管理仓库。它不是单一源码目录，而是通过多个发布分支和 worktree 维护 Forge / NeoForge 的不同版本。

看点：

- 一个管理仓库维护多个发布分支。
- 使用 `BUILD_ALL.ps1` 批量构建不同 Minecraft / Loader 版本。
- 通过维护文档记录 Java、NeoForge、BeyondDimensions 的兼容关系。
- 私有仓库展示，访客可能无法打开 GitHub 链接。

### Rainyun

雨云自动签到工具。它用 Selenium 处理网页流程，用 ddddocr 辅助验证码识别，并提供 Docker、GitHub Actions 和多通道通知。

看点：

- Docker / docker-compose 开箱运行。
- 多账号、代理池、随机浏览器指纹。
- 签到结果截图留证。
- PushPlus、WXPusher、钉钉、邮件、企业微信等通知链路。

### Cloud189CheckinJs

天翼云盘自动签到脚本。这个项目适合记录 GitHub Actions 自动任务、凭据隔离和通知配置经验。

看点：

- 支持 GitHub Actions 和本地运行。
- 支持多种通知渠道。
- 项目中涉及 `.env`，后续公开展示时只写架构，不展示敏感配置。

### GLaDOS Heckin Cloudflare

部署在 Cloudflare Workers Python 运行时上的自动签到服务，通过 Cron 定时触发，并用 Secrets 保存敏感信息。

看点：

- Cloudflare Workers Python。
- Cron 定时任务。
- Secrets / Vars 分层配置。
- 企业微信机器人推送。

### F-Sweep

Minecraft 1.21.1 NeoForge 功能模组，在容器界面提供一键存入能力，减少重复整理背包的操作。

看点：

- 容器界面按钮和快捷键触发。
- 自动跳过不应转移的槽位。
- 对维度网络和精妙背包做适配。
- 通过屏幕提示反馈成功、已满或无可转移物品。

### cf-email-inbox-pages

Cloudflare 邮件管理工具的静态前端，通过 GitHub Pages 发布，连接 Worker API 获取邮件列表和详情。

看点：

- 静态页面独立发布。
- Bearer Token 只保存在浏览器本地。
- 前端仓库不包含后端 Token。

### CloudPaste

CloudPaste 是自托管文件管理与 Markdown 分享工具。我把它作为 Cloudflare 文件管理实践记录，重点关注部署、存储和预览能力。

看点：

- Cloudflare Workers / D1 / R2。
- 多存储聚合与 WebDAV。
- Markdown 编辑和文件预览。
- 这是 fork 项目，展示时会标注为实践记录。

## 展示规则

私有项目也会放 GitHub 链接，并标注“私有仓库”。公开访客无法访问时，这是 GitHub 权限行为，不代表链接失效。

敏感信息不会写进博客，包括账号、Cookie、Webhook、Token、密钥、环境变量和真实运行凭据。
