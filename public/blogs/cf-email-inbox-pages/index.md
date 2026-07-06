# cf-email-inbox-pages：Cloudflare 邮件工具的静态前端

创建时间：2026-03-16 20:22:10 UTC
仓库：<https://github.com/wwwqqq001/cf-email-inbox-pages>
部署位置：GitHub Pages
状态：公开仓库

cf-email-inbox-pages 是一个静态前端，用来连接 Cloudflare Worker 邮件 API。它不承担邮件后端逻辑，只负责让用户在浏览器里填写 API 地址和 Bearer Token，然后读取邮件列表与详情。

![cf-email-inbox-pages 前端交互流程](/blogs/cf-email-inbox-pages/ui-flow.svg)

图里展示的是它的边界：GitHub Pages 只托管静态文件，浏览器本地保存 Token，Cloudflare Worker 提供邮件接口。文章不展示真实 Token，也不展示任何私有邮件内容。

## 快速了解

| 项目项 | 内容 |
| --- | --- |
| 类型 | 静态前端 / Cloudflare 周边工具 |
| 核心技术 | HTML、CSS、JavaScript、GitHub Pages |
| 后端依赖 | Cloudflare Worker 邮件 API |
| 凭据位置 | 浏览器本地输入并保存 |
| 主要功能 | 邮件列表、邮件详情、API 地址配置 |

## 问题背景

Cloudflare 邮件工具如果只提供 API，日常查看会比较不顺手。为此我把展示层拆出来，做成一个不需要服务器的静态页面。

这个拆法的重点是轻量：

- 不引入复杂框架。
- 不需要构建服务。
- 不需要后端代理。
- 使用者自己填写 API 地址和 Token。

## 我做了什么

项目结构很直接：

- `index.html`：页面结构。
- `styles.css`：布局和界面样式。
- `app.js`：读取配置、请求 Worker API、渲染邮件列表和详情。
- `.github`：自动发布到 GitHub Pages。

这种写法适合小工具。它牺牲了一部分复杂工程能力，但换来低维护成本和更清晰的部署方式。

## 为什么保持静态化

Token 保存在浏览器本地，而不是放在仓库或静态页面源码里。这不是绝对安全的企业方案，但符合个人工具的定位：使用者自己控制 API 地址和访问凭据，前端只负责调用。

如果把 Token 放到远端代理里，确实可以隐藏浏览器侧凭据，但又会引入一个需要维护的后端。这个项目选择保持静态化。

## 页面打开后能做什么

页面打开后，用户填写 Worker API Base URL 和 Bearer Token，就可以读取邮件列表，并在右侧查看邮件详情。发布层使用 GitHub Pages，后端仍由 Cloudflare Worker 承担。

这种结构让项目边界很清楚：

- GitHub Pages：发布页面。
- 浏览器：保存用户输入的配置。
- Cloudflare Worker：提供邮件接口。

## 后续计划

后续可以补这些能力：

- 邮件搜索。
- 多 API 地址配置。
- Token 过期提示。
- 错误状态和空状态页面。
- 移动端列表与详情切换。

## 展示边界

这是公开仓库，可以查看源码。文章只展示前端结构和交互边界，不公开任何真实 Token、邮件内容、Worker 管理后台或私有 API 配置。
