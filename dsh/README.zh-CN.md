# dotnet-skills（dsh bundle）

[English](README.md) | 中文

本目录是一个 [DeepSeek Harness (DSH)](https://deepseek-harness.github.io/deepseek-harness/)
bundle，使本仓库的**完整**技能目录可以安装进 DSH profile。技能本身是本仓库
[`plugins/`](../plugins/)（fork 自 [dotnet/skills](https://github.com/dotnet/skills)）
全部 17 个插件（105 个技能）的静态快照。

## 目录结构

| 文件 | 用途 |
|------|---------|
| `package.json` | 按[官方 bundle 规范](https://deepseek-harness.github.io/deepseek-harness/guide/publish)声明 `"dsh": { "bundle": { "patch": "./cordis.patch.yml" } }`。 |
| `cordis.patch.yml` | 一条 insert 行，把本包挂载到 `@deepseek-ai/dsh-base` 之后。 |
| `index.js` | 把 `plugin.json` 形式的技能包桥接到 `ctx.skills`（通过 `registerProvider`；DSH 原生不加载该格式）。 |
| `skills/` | 全部 105 个 `<name>/SKILL.md` 技能包的快照，每个技能一个目录。 |

## 安装

从 npm 按包名安装（推荐）：

```sh
dsh plugin --profile <name> add dotnet-skills
```

或不经 npm，直接从本仓库安装：

```sh
dsh plugin --profile <name> add git+https://github.com/flyneth/dotnet-skills&path:dsh/
```

安装后，列出该 bundle 的每个 profile 中，全部 105 个技能都可通过面向模型的
skill loader 使用。兼容性声明为 `"engines": { "dsh": ">=0.2.0-rc.2" }`。

`skills/` 是静态快照：上游 skills 变更后，从 `plugins/` 重新复制以刷新。
