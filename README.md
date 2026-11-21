# Agent project template: .NET + Node + React + Docker (Dual-Platform)

A reusable, dual-platform (**Google Antigravity** and **GitHub Copilot**)
agent scaffolding template for local Windows control applications built with a
React + TypeScript + Vite UI, a local Node.js gateway/coordinator, shared
cross-runtime contracts, an authoritative `.NET` Windows worker, and Docker
Linux containers for portable build/test verification. Contains no application
code, project lore, machine-specific paths, or secrets.

## How loading works

Files on disk cost zero tokens until loaded. Both Antigravity and GitHub Copilot
automatically discover root `AGENTS.md` and portable skills under
`.agents/skills/` (the open Agent Skills standard). Keep only `AGENTS.md` at the
root (no `GEMINI.md`). If you use GitHub Copilot Code Review or Visual Studio,
add a one-line `.github/copilot-instructions.md` containing:
`Follow [AGENTS.md](../AGENTS.md).` Do not use `@path` or `@[label](path)`
includes in either file.

| Layer | Loaded when | Platform | Cost |
|---|---|---|---|
| `AGENTS.md` | Every session | Both | Small and fixed (the only always-on file, <= ~70 lines) |
| `.agents/rules/*.md` | `trigger: model_decision` | Antigravity | Only when relevant |
| `.github/instructions/*.instructions.md` | Matching file (`applyTo`) | GitHub Copilot | Only when matching files are touched |
| `.agents/skills/*` | Names/descriptions at startup; `SKILL.md` on use | Both (portable) | Progressive disclosure |
| `.agents/agents/*` / `.github/agents/*` | When invoked / delegated | Platform-native | Only when used |
| `docs/ai/*` & `docs/product/*` | When `AGENTS.md` or rules point to them | Both | Only on demand, by heading |
| `docs/ai/read-lists/*` | When a subagent starts | Both | One small file per subagent role |

> **VS Code note:** Keep `.agents/skills` enabled in `chat.agentSkillsLocations`
> so GitHub Copilot loads the single canonical skill tree in `.agents/skills/`
> without duplicating skills into `.github/skills/`.

## Adopt it

1. Click **Use this template** on GitHub (or copy the contents of `dotnet-node-react/` into your repository root).
2. Fill in `Project essentials` in [AGENTS.md](AGENTS.md) with your exact paths
   and build/Docker commands (keep `AGENTS.md` under ~70 lines).
3. Fill in [docs/product/customer-brief.md](docs/product/customer-brief.md) with
   your target user, UX standards, and UI checklist.
4. Update [docs/ai/cost.md](docs/ai/cost.md) with your Antigravity and/or GitHub
   Copilot plan limits and preferred models.
5. If this repository supports ClarifyDoc, run `setup-clarifydoc` during
   adoption or initial setup to generate the ClarifyDoc rule, skills,
   `.gitignore` entry, and MCP guidance on demand (otherwise create none).
6. Delete anything you do not use (fewer files means less startup metadata).
7. Run `pwsh -NoProfile -File scripts/check-context-size.ps1` to verify
   always-on budgets, Antigravity rule caps, and skill deduplication.

## Curated skills included

Stored once in `.agents/skills/` for both platforms:

- **Core workflow:** `coordinate-task` (Antigravity main-agent coordination),
  `project-remember`, `systematic-debugging` (UI -> gateway -> .NET worker),
  `verification-before-completion` (evidence matrix separating fixtures,
  development host, and installed/VM acceptance).
- **On-demand generators:** `generate-handoff-canvas` (recreates a Copilot
  `.github/extensions/<id>/` canvas or Antigravity MCP App side-pane panel),
  `setup-clarifydoc` (detects ClarifyDoc support at template adoption/init and
  scaffolds only the needed ClarifyDoc config, rules, and workflow skills).

## What is deliberately omitted

- **No application code, secrets, or machine-local paths.** MCP configs live as
  empty `.example` templates (`.agents/mcp-config.json.example` and
  `.mcp.json.example`); never commit literal user paths or credentials.
- **No enabled hooks.** See `.agents/hooks/README.md` and
  `.github/hooks/README.md`.
- **No bloated generic agent catalogs or heavy third-party skill bundles.** If
  your team wants external UI/React/xUnit skills (for example, React best
  practices or motion guidelines), install them intentionally and verify they do
  not contradict `.NET`/xUnit or read-only live acceptance rules.
- **No prebuilt canvas extension or ClarifyDoc skill trees.** Use
  `generate-handoff-canvas` and `setup-clarifydoc` to scaffold those assets only
  in repositories that actually need them.

## Layout

```text
AGENTS.md                                  always-loaded dual-platform router (<= ~70 lines)
.agents/
  rules/                                   8 Antigravity conditional rules (model_decision)
  agents/                                  implementer, reviewer, handoff (explore uses built-in research)
  skills/                                  6 shared portable skills (core + canvas & ClarifyDoc generators)
  hooks/README.md                          hook policy (none enabled)
  mcp-config.json.example                  empty MCP server list (Antigravity format)
.github/
  instructions/                            8 Copilot path-scoped instructions (applyTo)
  agents/                                  coordinator, explorer, implementer, reviewer, handoff
  hooks/README.md                          hook policy (none enabled)
docs/
  ai/                                      handoff.md, handoff-history/, todo.md, decisions.md, memory.md, cost.md, read-lists/
  product/customer-brief.md                target-user & UI quality checklist template
  codebase/README.md                       index for optional on-demand architecture maps
scripts/check-context-size.ps1             dual-platform context-budget & hygiene check
.mcp.json.example                          empty MCP server list (Copilot CLI / portable format)
.gitignore  .gitattributes                 secret/artifact hygiene & consistent LF endings
```
