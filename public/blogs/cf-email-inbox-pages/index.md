# cf-email-inbox-pages：邮件工具的静态前端

创建时间：2026-03-16 20:22:10 UTC  
仓库：<https://github.com/wwwqqq001/cf-email-inbox-pages>  
状态：公开仓库

这是一个很小但结构清楚的静态前端项目，用来连接 Cloudflare Worker 邮件 API。

它的定位不是完整后端，而是把邮件管理工具的展示层拆出来，用 GitHub Pages 发布。

## 项目目标

页面打开后，用户填入 Worker API Base URL 和 Bearer Token，就可以读取邮件列表和详情。

这个拆法有几个好处：

- 前端可以完全静态发布。
- 后端 Token 不进仓库。
- 使用者可以自己填写 API 地址。
- GitHub Actions 可以自动发布页面。

## 文件结构

项目很克制：

- `index.html`：页面结构。
- `styles.css`：界面样式。
- `app.js`：请求 Worker API、管理 Token、渲染邮件列表。
- `.github`：自动发布工作流。

没有复杂框架，也不需要构建步骤。这个选择对一个工具型前端来说是合理的。

## 安全边界

我最看重的是 Token 的位置。

Token 只由用户在浏览器里输入，并保存在本地 `localStorage`。仓库里不放 API Token，也不把 Token 写入页面源码。

这不是绝对安全方案，但它符合这个项目的定位：个人工具、低维护、低成本、静态发布。

## 和 Cloudflare 的关系

这个项目本身是 GitHub Pages，但它服务的是 Cloudflare Worker 后端。

所以它属于“Cloudflare 周边工具”：

- Cloudflare Worker 负责邮件 API。
- 静态页面负责读取和展示。
- 用户本地保存访问凭据。

## 后续可以做什么

如果继续完善，我会加：

- 邮件搜索。
- 多 API 地址配置。
- Token 过期提示。
- 错误状态更明确的空页面。
- 移动端列表和详情切换。

## 展示说明

这是公开仓库，可以直接打开 GitHub 链接查看源码。
