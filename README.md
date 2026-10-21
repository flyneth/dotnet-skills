# .NET Agent Skills

English | [中文](README.zh-CN.md)

> [!NOTE]
> **Fork notice:** This repository is a fork of the official
> [dotnet/skills](https://github.com/dotnet/skills) repository. All skills,
> plugins, and tooling are Copyright © the .NET Team at Microsoft, licensed
> under the [MIT license](LICENSE). The only change made in this fork is a
> DeepSeek Harness (DSH) packaging layer so the full catalog can be installed
> as a DSH plugin — see the [DSH section](#deepseek-harness-dsh) below. In the other install
> methods below, replace `dotnet/skills` with `flyneth/dotnet-skills` to install this fork's
> version. Everything else is unchanged
> upstream content.

[![Dashboard](https://github.com/dotnet/skills/actions/workflows/pages/pages-build-deployment/badge.svg)](https://dotnet.github.io/skills/)

This repository contains the .NET team's curated set of portable skills and host-specific custom
agents for coding agents. For information about the Agent Skills standard, see
[agentskills.io](https://agentskills.io).

> [!TIP]
> **Compare skill value:** [Open the Skill Value dashboard](https://dotnet.github.io/skills/)
> See token use, elapsed time, activation, and not-passed rates by plugin, skill,
> executor model, and judge model.

Plugin support is component-specific:

- Skills and MCP servers are the portable component types defined by
  [Agent Plugins 1.0](https://agent-plugins.org/specification).
- Files under `agents/*.agent.md` use GitHub Copilot custom-agent conventions. They are not
  installed as native OpenAI Codex agents.
- Native Codex agents use `.codex/agents/*.toml`. This repository does not currently ship them
  because Codex plugin installation does not place agent files in those discovery locations.

## What's Included

| Plugin | Description |
|--------|-------------|
| [dotnet](plugins/dotnet/) | High-level .NET development skills plus C# language server (LSP) integration for hosts that support it. |
| [dotnet-advanced](plugins/dotnet-advanced/) | Collection of .NET skills for handling specific .NET tasks for special scenarios. |
| [dotnet-data](plugins/dotnet-data/) | Skills for .NET data access and Entity Framework related tasks. |
| [dotnet-diag](plugins/dotnet-diag/) | Skills for .NET performance investigations, debugging, and incident analysis. |
| [dotnet-msbuild](plugins/dotnet-msbuild/) | Comprehensive MSBuild and .NET build skills: failure diagnosis, performance optimization, code quality, and modernization. |
| [dotnet-nuget](plugins/dotnet-nuget/) | NuGet and .NET package management: dependency management and modernization. |
| [dotnet-upgrade](plugins/dotnet-upgrade/) | Skills for migrating and upgrading .NET projects across framework versions, language features, and compatibility targets. |
| [dotnet-maui](plugins/dotnet-maui/) | Skills for .NET MAUI development: environment setup, diagnostics, and troubleshooting. |
| [dotnet-ai](plugins/dotnet-ai/) | AI and ML skills for .NET: technology selection, LLM integration, agentic workflows, RAG pipelines, MCP, and classic ML with ML.NET. |
| [dotnet-template-engine](plugins/dotnet-template-engine/) | .NET Template Engine skills: template discovery, project scaffolding, and template authoring. |
| [dotnet-test](plugins/dotnet-test/) | Skills for running, generating, analyzing, and improving .NET tests: test execution, filtering, platform detection, coverage, testability, and MSTest workflows. |
| [dotnet-test-migration](plugins/dotnet-test-migration/) | Skills and a GitHub Copilot orchestrator agent for migrating .NET test frameworks and platforms: MSTest and xUnit version upgrades, xUnit-to-MSTest conversion, and VSTest to Microsoft.Testing.Platform. |
| [dotnet-aspnetcore](plugins/dotnet-aspnetcore/) | ASP.NET Core web development skills including middleware, endpoints, real-time communication, and API patterns. |
| [dotnet-blazor](plugins/dotnet-blazor/) | Skills for Blazor development: component authoring, interactivity, and web application patterns. |
| [dotnet-winforms](plugins/dotnet-winforms/) | Skills for Windows Forms development: project setup, UI design, data binding, accessibility, and modern .NET practices. |
| [dotnet11](plugins/dotnet11/) | Skills for new .NET 11 APIs and language features. |

## Agentic Workflows

Reusable GitHub Agentic Workflows are published under
[`agentic-workflows/`](agentic-workflows/). Install the complete collection with
`gh aw add-wizard dotnet/skills/agentic-workflows@main`, or install an individual
package using the command in its README.

## DeepSeek Harness (DSH)

This fork ships an official-spec
[DeepSeek Harness](https://deepseek-harness.github.io/deepseek-harness/) bundle at the repository
root — `package.json` declares `dsh.bundle`, `cordis.patch.yml` mounts the entry, and `index.mjs`
bridges the skill packs onto `ctx.skills` — packaging all 17 plugins, 105 .NET skills in total,
as one installable DSH bundle.

**Option 1 · npm (recommended)**

```sh
dsh plugin --profile <name> add dotnet-skills
```

`<name>` is the target profile; for the default web profile use `--profile web`:

```sh
dsh plugin --profile web add dotnet-skills
```

**Option 2 · dsh-market (one-click, once listed)**

Once listed in the [dsh-market](https://github.com/dsh-market/dsh-market) catalog: open the plugin
market in the web UI, search `dotnet-skills`, and install with one click.

**Option 3 · from this repository (no npm)**

```sh
dsh plugin --profile <name> add github:flyneth/dotnet-skills
```

Same profile rule applies — `--profile web` for the default web profile.

- After installation, all skills are available in every profile that lists the bundle.
- Compatibility: `"engines: { "dsh": ">=0.2.0-rc.2" }`.
- Skills are read straight from `plugins/<plugin>/skills/<skill>/SKILL.md` at load time — no snapshot, so upstream updates flow through automatically."

<details>
<summary><b>Other host installation methods (inherited from upstream: Copilot CLI / Claude Code / VS Code / Cursor / Codex CLI)</b></summary>

Replace `dotnet/skills` with `flyneth/dotnet-skills` in the commands below to install this fork's version.

### 🚀 Plugins - Copilot CLI / Claude Code

1. Launch Copilot CLI or Claude Code
2. Add the marketplace:
   ```
   /plugin marketplace add dotnet/skills
   ```
3. Install a plugin:
   ```
   /plugin install <plugin>@dotnet-agent-skills
   ```
4. Restart to load the new plugins
5. View available skills:
   ```
   /skills
   ```
6. View available agents:
   ```
   /agents
   ```
7. Update plugin (on demand):
   ```
   /plugin update <plugin>@dotnet-agent-skills
   ```

### VS Code / VS Code Insiders (Preview)

> [!IMPORTANT]  
> VS Code plugin support is a preview feature and subject to change. You may need to enable it first.

```jsonc
// settings.json
{
  "chat.plugins.enabled": true,
  "chat.plugins.marketplaces": ["dotnet/skills"]
}
```

Once configured, type `/plugins` in Copilot Chat or use the `@agentPlugins` filter in Extensions to browse and install plugins from the marketplace.

### Cursor

This repository is a [Cursor plugin marketplace](https://cursor.com/docs/plugins). You can discover and install published plugins directly in Cursor:

1. Open the marketplace panel in Cursor
2. Search for `.NET` or browse [cursor.com/marketplace](https://cursor.com/marketplace)
3. Install the desired plugins

For local development or unpublished changes, import plugins from a local checkout:

1. Copy or symlink your local checkout to `~/.cursor/plugins/local/dotnet-agent-skills`
2. Restart Cursor or run **Developer: Reload Window**

### Codex CLI

Skills in this repository follow the [agentskills.io](https://agentskills.io) open standard
and are compatible with [OpenAI Codex](https://developers.openai.com/codex/skills).

Codex plugin installs expose the skills and any Codex-compatible MCP servers declared by the
plugin. They do not expose the repository's GitHub Copilot `.agent.md` files, their static
handoffs, or host-specific LSP declarations. Use Codex's built-in dynamic subagent delegation
instead. Native Codex custom agents are a separate configuration mechanism documented under
[multi-agent workflows](https://developers.openai.com/codex/multi-agent/). OpenAI
[Agents SDK handoffs](https://openai.github.io/openai-agents-python/handoffs/) are an
application-level transfer between SDK-defined agents; they are separate from both Copilot's
static UI handoff metadata and Codex plugin or custom-agent packaging.

#### Plugin marketplace (recommended)

Codex CLI v0.121.0 and later supports a [plugin marketplace](https://developers.openai.com/codex/plugins).
This repository ships a Codex-native marketplace manifest at `.agents/plugins/marketplace.json`,
so you can register `dotnet/skills` as a marketplace and install plugins from it directly.

1. Add the marketplace:
   ```bash
   codex plugin marketplace add dotnet/skills
   ```
2. Launch Codex and open the plugin browser:
   ```
   /plugins
   ```
3. Browse the `dotnet-agent-skills` tab and install the desired plugins.
4. Update plugins on demand:
   ```bash
   codex plugin marketplace upgrade dotnet-agent-skills
   ```

#### Individual skills

You can also install individual skills using the `skill-installer` CLI with the GitHub URL:

```bash
$ skill-installer install https://github.com/dotnet/skills/tree/main/plugins/<plugin>/skills/<skill-name>
```

</details>

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines and how to add a new plugin.

## License

See [LICENSE](LICENSE) for details.
