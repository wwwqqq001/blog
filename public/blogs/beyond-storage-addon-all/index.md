# Beyond Storage Addon All：多版本模组发布管理

创建时间：2026-03-23 19:01:22 UTC  
仓库：<https://github.com/wwwqqq001/beyond_storage_addon_all>  
状态：私有仓库，已归档

这个项目不是普通意义上的单个模组源码仓库，而是一个发布管理仓库。

它负责维护 Beyond Storage Addon 在多个 Minecraft、Forge、NeoForge 版本上的构建、发布和兼容记录。

![Beyond Storage Addon 多版本发布矩阵](/blogs/beyond-storage-addon-all/release-matrix.svg)

这张图把项目最难解释的部分可视化了：它不是一个单分支源码项目，而是围绕多个加载器和 Minecraft 版本维护发布矩阵。每个分支都对应不同的构建约束、依赖版本和发布产物，管理仓库负责把这些关系固定下来。

对 Minecraft 模组来说，多版本维护经常比功能开发本身更消耗精力。图示能让读者一眼看到为什么需要 `VERSION_BRANCHES.md`、`BUILD_ALL.ps1` 和固定命名规则，也能看出这个项目的重点是发布治理，而不是单次功能演示。

## 项目目标

Beyond Storage Addon 的核心功能是给 Beyond Dimensions 增加一个顺手的一键存入能力。

但真正麻烦的不是功能本身，而是 Minecraft 模组生态里的版本矩阵：

- Forge 1.20.1
- NeoForge 1.20.1
- NeoForge 1.21.1
- NeoForge 1.21.11
- NeoForge 26.1.2
- NeoForge 26.2

这些版本的 Java、加载器、依赖 API 和元数据格式都不完全一样，所以我把重点放在“怎样长期维护多个发布分支”。

## 仓库组织

管理仓库的 `main` 分支只维护管理文件，不直接维护可发布源码。

发布源码放在不同 release branch / worktree 中：

- `forge-1.20.1`
- `neoforge-1.20.1`
- `neoforge-1.21.1`
- `neoforge-1.21.11`
- `neoforge-26.1.2`
- `neoforge-26.2`

这种组织方式的好处是每个版本都可以独立构建、独立提交、独立发布，不会为了兼容某个旧版本把主线代码变得越来越乱。

## 构建方式

仓库里有一个 `BUILD_ALL.ps1`，用于批量构建所有发布 worktree。

发布产物按固定命名：

```text
BS_addon_{loader}-{minecraft_version}-{addon_version}.jar
```

这个命名规则很重要。发布版本多起来之后，文件名本身就是最直接的版本说明。

## 维护经验

这个项目让我确认：多版本项目最怕“靠记忆维护”。

所以我额外写了：

- `AI_MAINTENANCE.md`
- `VERSION_BRANCHES.md`
- Modrinth changelog

这些文档不是装饰，而是为了让后续接手时能知道从哪里构建、哪个目录不能用、哪个版本依赖什么 Java。

## 展示说明

仓库是私有且归档状态。博客只展示组织方式和维护经验，不展示本地 worktree、构建产物或任何未公开发布文件。
