# asdqe：早期 Cloudflare Pages 部署记录

Cloudflare Pages：<https://asdqe.pages.dev>
状态：项目存在，公开域名当前返回 404
来源：Wrangler Pages 项目列表，最近更新约 1 年前

`asdqe` 是 Cloudflare Pages 项目列表里保留下来的早期部署。公开访问 `https://asdqe.pages.dev` 当前返回 404，所以这篇只把它作为部署历史记录，不把它包装成正在运行的网站。

![asdqe 当前状态示意图](/blogs/cf-pages-asdqe/status.svg)

这张图是状态示意图，不是正常页面截图。它的作用是说明当前事实：项目还在 Pages 列表中，但公开域名没有有效首页。

## 当前状态

- Cloudflare Pages 项目仍然存在。
- 生产部署记录仍然能在 Wrangler 中看到。
- 默认 `pages.dev` 域名当前返回 404。
- 没有可展示的公开产品界面。

## 保留原因

我把它放进博客，是为了把 Cloudflare 部署资产也纳入项目清单。作品集不只需要展示完成项目，也应该标记哪些项目已经停用、占位或需要清理。

这能避免以后看到 Pages 项目列表时不知道它是否还在使用。

## 后续处理建议

后续有两个选择：

- 如果项目仍有用途，补一个极简归档页，写清状态、最后更新时间和替代入口。
- 如果项目已经不用，在 Cloudflare 控制台删除，避免项目列表继续膨胀。

## 展示边界

文章只记录公开域名和当前 HTTP 状态，不公开 Cloudflare 控制台链接、账号 ID、部署日志或任何访问凭据。
