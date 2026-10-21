# .NET Agent Skills

[English](README.md) | 中文

> [!NOTE]
> **Fork 声明：** 本仓库 fork 自官方 [dotnet/skills](https://github.com/dotnet/skills)
> 仓库。所有 skills、插件及工具版权归 © .NET Team at Microsoft 所有，采用
> [MIT 许可证](LICENSE)。本 fork 唯一的改动是新增了一层 DeepSeek Harness（DSH）
> 打包，使完整技能目录可以作为 DSH 插件安装——见下文 [DSH 章节](#deepseek-harness-dsh)。其余内容与上游完全一致，未做修改。

[![Dashboard](https://github.com/dotnet/skills/actions/workflows/pages/pages-build-deployment/badge.svg)](https://dotnet.github.io/skills/)

本仓库包含 .NET 团队为编码智能体精心整理的可移植 skills 与特定宿主的自定义 agents。
关于 Agent Skills 标准，见 [agentskills.io](https://agentskills.io)。

> [!TIP]
> **对比技能价值：** [打开 Skill Value 仪表板](https://dotnet.github.io/skills/)
> 按插件、技能、执行模型与评审模型查看 token 消耗、耗时、激活率与未通过率。

各组件的插件支持情况：

- Skills 与 MCP server 是 [Agent Plugins 1.0](https://agent-plugins.org/specification)
  定义的可移植组件类型。
- `agents/*.agent.md` 文件遵循 GitHub Copilot 自定义 agent 约定，不会作为
  OpenAI Codex 原生 agent 安装。
- Codex 原生 agent 使用 `.codex/agents/*.toml`。本仓库目前不提供这类文件，
  因为 Codex 的插件安装机制不会把 agent 文件放到对应的发现位置。

## 收录内容

| 插件 | 说明 |
|--------|-------------|
| [dotnet](plugins/dotnet/) | 高层 .NET 开发 skills，并为支持的宿主提供 C# 语言服务器（LSP）集成。 |
| [dotnet-advanced](plugins/dotnet-advanced/) | 处理特殊场景 .NET 任务的 skills 集合。 |
| [dotnet-data](plugins/dotnet-data/) | .NET 数据访问与 Entity Framework 相关任务的 skills。 |
| [dotnet-diag](plugins/dotnet-diag/) | .NET 性能排查、调试与事故分析的 skills。 |
| [dotnet-msbuild](plugins/dotnet-msbuild/) | 全面的 MSBuild 与 .NET 构建 skills：故障诊断、性能优化、代码质量与现代化。 |
| [dotnet-nuget](plugins/dotnet-nuget/) | NuGet 与 .NET 包管理：依赖管理与现代化。 |
| [dotnet-upgrade](plugins/dotnet-upgrade/) | 跨框架版本、语言特性与兼容性目标迁移升级 .NET 项目的 skills。 |
| [dotnet-maui](plugins/dotnet-maui/) | .NET MAUI 开发 skills：环境搭建、诊断与故障排查。 |
| [dotnet-ai](plugins/dotnet-ai/) | 面向 .NET 的 AI 与 ML skills：技术选型、LLM 集成、agentic 工作流、RAG 管道、MCP 及基于 ML.NET 的经典机器学习。 |
| [dotnet-template-engine](plugins/dotnet-template-engine/) | .NET Template Engine skills：模板发现、项目脚手架与模板编写。 |
| [dotnet-test](plugins/dotnet-test/) | 运行、生成、分析与改进 .NET 测试的 skills：测试执行、筛选、平台检测、覆盖率、可测试性与 MSTest 工作流。 |
| [dotnet-test-migration](plugins/dotnet-test-migration/) | 用于迁移 .NET 测试框架与平台的 skills 及 GitHub Copilot 编排 agent：MSTest 与 xUnit 版本升级、xUnit 转 MSTest，以及迁移到 Microsoft.Testing.Platform。 |
| [dotnet-aspnetcore](plugins/dotnet-aspnetcore/) | ASP.NET Core Web 开发 skills，包括中间件、端点、实时通信与 API 模式。 |
| [dotnet-blazor](plugins/dotnet-blazor/) | Blazor 开发 skills：组件编写、交互性与 Web 应用模式。 |
| [dotnet-winforms](plugins/dotnet-winforms/) | Windows Forms 开发 skills：项目搭建、UI 设计、数据绑定、无障碍与现代 .NET 实践。 |
| [dotnet11](plugins/dotnet11/) | .NET 11 新 API 与语言特性的 skills。 |

## Agentic Workflows

可复用的 GitHub Agentic Workflows 发布在
[`agentic-workflows/`](agentic-workflows/) 下。使用
`gh aw add-wizard dotnet/skills/agentic-workflows@main` 安装完整集合，
或按各包 README 中的命令安装单个包。

## DeepSeek Harness (DSH)

本 fork 在仓库根目录提供一个符合官方规范的
[DeepSeek Harness](https://deepseek-harness.github.io/deepseek-harness/) bundle——`package.json`
声明 `dsh.bundle`，`cordis.patch.yml` 挂载入口，`index.mjs` 把技能包桥接到 `ctx.skills`——
把全部 17 个插件、共 105 个 .NET skills 打包为一个可安装的 DSH 组合包。

```sh
dsh plugin --profile <name> add dotnet-skills
```

也可不经 npm，直接从本仓库安装：

```sh
dsh plugin --profile <name> add github:flyneth/dotnet-skills
```

- 安装后，所有列出该 bundle 的 profile 中全部 skills 均可用；
- 兼容性声明：`"engines": { "dsh": ">=0.2.0-rc.2" }`；
- 技能在加载时直接从 `plugins/<插件>/skills/<技能>/SKILL.md` 读取——无需快照，上游更新自动生效。

## 安装

### 🚀 插件 - Copilot CLI / Claude Code

1. 启动 Copilot CLI 或 Claude Code
2. 添加 marketplace：
   ```
   /plugin marketplace add dotnet/skills
   ```
3. 安装插件：
   ```
   /plugin install <plugin>@dotnet-agent-skills
   ```
4. 重启以加载新插件
5. 查看可用 skills：
   ```
   /skills
   ```
6. 查看可用 agents：
   ```
   /agents
   ```
7. 按需更新插件：
   ```
   /plugin update <plugin>@dotnet-agent-skills
   ```

### VS Code / VS Code Insiders（预览）

> [!IMPORTANT]  
> VS Code 插件支持为预览功能，可能随时变化。可能需要先手动启用。

```jsonc
// settings.json
{
  "chat.plugins.enabled": true,
  "chat.plugins.marketplaces": ["dotnet/skills"]
}
```

配置后，在 Copilot Chat 中输入 `/plugins`，或在扩展面板使用 `@agentPlugins` 筛选器浏览并安装 marketplace 中的插件。

### Cursor

本仓库是一个 [Cursor 插件市场](https://cursor.com/docs/plugins)。可以直接在 Cursor 中发现并安装已发布的插件：

1. 打开 Cursor 的 marketplace 面板
2. 搜索 `.NET`，或浏览 [cursor.com/marketplace](https://cursor.com/marketplace)
3. 安装所需插件

本地开发或未发布的改动，可从本地 checkout 导入插件：

1. 把本地 checkout 复制或软链接到 `~/.cursor/plugins/local/dotnet-agent-skills`
2. 重启 Cursor 或运行 **Developer: Reload Window**

### Codex CLI

本仓库的 skills 遵循 [agentskills.io](https://agentskills.io) 开放标准，
与 [OpenAI Codex](https://developers.openai.com/codex/skills) 兼容。

Codex 插件安装会暴露 skills 及插件声明的 Codex 兼容 MCP server，但不会暴露
本仓库的 GitHub Copilot `.agent.md` 文件、其静态 handoff 或特定宿主的 LSP 声明。
请改用 Codex 内置的动态子智能体委托。Codex 原生自定义 agent 是独立的配置机制，
见[多智能体工作流](https://developers.openai.com/codex/multi-agent/)文档。OpenAI
[Agents SDK handoffs](https://openai.github.io/openai-agents-python/handoffs/) 是
SDK 定义的 agent 之间的应用层转移；它们与 Copilot 的静态 UI handoff 元数据、
Codex 插件及自定义 agent 打包是相互独立的机制。

#### 插件市场（推荐）

Codex CLI v0.121.0 及以上版本支持[插件市场](https://developers.openai.com/codex/plugins)。
本仓库在 `.agents/plugins/marketplace.json` 提供了 Codex 原生的 marketplace 清单，
可直接把 `dotnet/skills` 注册为 marketplace 并从中安装插件。

1. 添加 marketplace：
   ```bash
   codex plugin marketplace add dotnet/skills
   ```
2. 启动 Codex 并打开插件浏览器：
   ```
   /plugins
   ```
3. 浏览 `dotnet-agent-skills` 标签页并安装所需插件。
4. 按需更新插件：
   ```bash
   codex plugin marketplace upgrade dotnet-agent-skills
   ```

#### 单个 skills

也可以用 `skill-installer` CLI 通过 GitHub URL 安装单个 skill：

```bash
$ skill-installer install https://github.com/dotnet/skills/tree/main/plugins/<plugin>/skills/<skill-name>
```

## 参与贡献

贡献指南及如何新增插件见 [CONTRIBUTING.md](CONTRIBUTING.md)。

## 许可证

详见 [LICENSE](LICENSE)。
