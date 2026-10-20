# dotnet-skills (dsh bundle)

English | [中文](README.zh-CN.md)

This directory is a [DeepSeek Harness (DSH)](https://deepseek-harness.github.io/deepseek-harness/)
bundle that makes the **full** skill catalog of this repository installable into
a DSH profile. The skills themselves are a static snapshot of all 17 plugins
(105 skills) from [`plugins/`](../plugins/) of the forked
[dotnet/skills](https://github.com/dotnet/skills) repository.

## Layout

| File | Purpose |
|------|---------|
| `package.json` | Declares `"dsh": { "bundle": { "patch": "./cordis.patch.yml" } }` per the [official bundle contract](https://deepseek-harness.github.io/deepseek-harness/guide/publish). |
| `cordis.patch.yml` | One insert row mounting this package after `@deepseek-ai/dsh-base`. |
| `index.js` | Bridges the `plugin.json`-shaped skill packs onto `ctx.skills` via `registerProvider` (DSH has no native skills loader for that shape). |
| `skills/` | Snapshot of all 105 `<name>/SKILL.md` bundles, one directory per skill. |

## Install

Install by package name from npm (recommended):

```sh
dsh plugin --profile <name> add dotnet-skills
```

Or install from this repository directly, without npm:

```sh
dsh plugin --profile <name> add git+https://github.com/flyneth/dotnet-skills&path:dsh/
```

All 105 skills are then available through the model-facing skill loader on
every profile that lists the bundle. Compatibility is declared as
`"engines": { "dsh": ">=0.2.0-rc.2" }`.

The skills directory is a snapshot: refresh it by re-copying from `plugins/`
when the upstream skills change.
