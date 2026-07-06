# Beyond Storage Addon All：管理 Minecraft 模组发布矩阵

创建时间：2026-03-23 19:01:22 UTC
仓库：<https://github.com/wwwqqq001/beyond_storage_addon_all>
状态：私有仓库，已归档

这个仓库不是单个模组源码仓库，而是 Beyond Storage Addon 的发布管理仓库。它负责把多个 Minecraft 版本、多个 Loader 分支和批量构建流程整理在一起，避免多版本发布完全依赖记忆。

![Beyond Storage Addon 多版本发布矩阵](/blogs/beyond-storage-addon-all/release-matrix.svg)

图里展示的是这个仓库真正要解决的问题：同一个模组能力需要落到不同 Minecraft、Forge 和 NeoForge 组合上。每个版本都有自己的依赖、构建约束和发布产物，必须把关系写进仓库。

## 快速了解

| 项目项 | 内容 |
| --- | --- |
| 类型 | Minecraft 模组发布管理 |
| 核心对象 | Beyond Storage Addon 多版本分支 |
| 管理方式 | release branch / worktree / 批量构建脚本 |
| 构建脚本 | `BUILD_ALL.ps1` |
| 状态 | 私有归档，作为维护经验记录 |

## 问题背景

Minecraft 模组维护最消耗精力的地方，经常不是单个功能，而是版本矩阵。

这个项目涉及多个发布方向：

- Forge 1.20.1
- NeoForge 1.20.1
- NeoForge 1.21.1
- NeoForge 1.21.11
- NeoForge 26.1.2
- NeoForge 26.2

不同版本的 Java 要求、加载器 API、依赖版本和元数据格式都有差异。如果所有内容混在一个分支里，后期会很难判断哪些改动属于哪个版本。

## 我做了什么

`main` 分支只放管理文件，不直接承担某个版本的可发布源码。具体发布源码放在独立 release branch 或 worktree 中，每个版本独立构建、独立提交、独立发布。

这个组织方式让管理仓库承担三件事：

- 记录版本分支和 Loader 对应关系。
- 固定批量构建入口。
- 保留发布命名和维护说明。

## 分支为什么这样拆

我没有把所有版本强行合并到同一个源码分支。单分支看起来更集中，但会让兼容逻辑不断堆叠，尤其在 Forge 和 NeoForge 之间切换时更容易混乱。

多分支方案的成本是管理复杂度更高，所以仓库必须用文档和脚本把规则固定下来。

## 构建产物怎么识别

发布产物使用固定命名：

```text
BS_addon_{loader}-{minecraft_version}-{addon_version}.jar
```

这个规则比口头说明可靠。文件名本身就能说明 Loader、Minecraft 版本和 Addon 版本，适合后续上传、归档和排错。

## 维护经验

这个项目让我确认：多版本项目不能靠记忆维护。仓库里额外保留了：

- `AI_MAINTENANCE.md`
- `VERSION_BRANCHES.md`
- Modrinth changelog

这些不是装饰文档，而是给后续维护者和自动化工具看的操作说明。

## 展示边界

仓库是私有且归档状态。文章只展示组织方式和维护经验，不展示本地 worktree、构建产物或任何未公开发布文件。
