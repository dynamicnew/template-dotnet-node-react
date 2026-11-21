---
name: setup-clarifydoc
description: Detect whether a .NET + Node + React + Docker project supports ClarifyDoc when adopting this template, initializing a repository, or preparing an initial commit, and scaffold only the needed ClarifyDoc MCP guidance, dual-platform rules, workflow skills, and ignore rules on demand.
---

# Bootstrap ClarifyDoc support (on demand)

Run this procedure when this template is applied, a repository is initialized
or committed for the first time, or the user asks to enable ClarifyDoc. **If
the project does not support or use ClarifyDoc, make no changes and create no
ClarifyDoc files.**

## 1. Detect whether ClarifyDoc applies

Check for any of these signals:
- `clarifydoc_*` MCP tools are available in the active session (confirm with
  one read-only `clarifydoc_status` call if present).
- A `.clarifydoc/` folder or existing ClarifyDoc MCP entry already exists in
  the workspace or user/editor config.
- The user explicitly requests ClarifyDoc setup.

If none apply, stop — do not pre-create unused ClarifyDoc assets.

## 2. Configure MCP safely (no secrets or machine-local paths)

- Reuse a single already-configured ClarifyDoc MCP provider when available;
  never configure duplicate providers (for example `clarifydoc` and
  `ClarifyDoc`), which double the 23 tools into 46.
- Remember that the ClarifyDoc MCP server's target directory is fixed at server
  startup (`clarifydoc mcp <path>`); neither `clarifydoc_status` nor
  `clarifydoc_analyze` takes a `path` argument.
- Never commit machine-local absolute binary paths (such as VS Code
  `globalStorage` paths) or secrets into tracked `.mcp.json`,
  `.vscode/mcp.json`, or `.agents/mcp_config.json`.
- If MCP setup is needed, ask the user before writing local MCP config or guide
  them to configure user-level MCP (Antigravity `~/.gemini/config/mcp_config.json`
  or VS Code **ClarifyDoc: Set Up MCP for an AI Agent...** / **Copy MCP Server
  Config**). Remember `.mcp.json` and `.agents/mcp_config.json` use
  `"mcpServers"` while `.vscode/mcp.json` uses `"servers"`.

## 3. Scaffold dual-platform repository assets tailored to this stack

Create only the assets appropriate for this repository:

1. **`.gitignore`:** ensure `.clarifydoc/` is ignored so generated analysis
   caches are never committed or hand-edited.
2. **Conditional rules (both platforms, keeping `AGENTS.md` <= ~70 lines):**
   - Antigravity: `.agents/rules/clarifydoc.md` (`trigger: model_decision`
     and a clear `description:` so Antigravity knows when to load it).
   - GitHub Copilot: `.github/instructions/clarifydoc.instructions.md`
     (`applyTo: "web/**,server/**,shared/**,src/**"`).
   - Rules to include:
     - Call `clarifydoc_status` first via one provider and verify the server is
       bound to this repository root. If `clarifydoc_status` reports a
       home-directory error or a different project path, **stop and never call
       `clarifydoc_analyze`** (`clarifydoc_analyze` will scan a home directory
       instead of rejecting it; cancel immediately with `clarifydoc_analyze(cancel=true)`
       if ever triggered on the wrong root) and instruct the user to start the
       server for this repo (`clarifydoc mcp <repo-path>`).
     - Never run parallel refreshes or silently trigger `clarifydoc_analyze`.
     - Use `clarifydoc_explain`, `clarifydoc_blast_radius`, or
       `clarifydoc_impact` before editing routes, screens, or symbols; use
       `clarifydoc_search` before adding new helpers.
     - Always verify `inferred`/`ambiguous` rows and static "unreachable"
       findings in source — especially dynamically spawned Node child
       processes, `.NET` protocol/bridge IPC hops, and generated
       `shared/contracts.ts` outputs. Distinguish indexed handlers
       (`clarifydoc_file`) from recognized routes (`clarifydoc_routes`).
     - Require user confirmation before `clarifydoc_analyze` or
       `clarifydoc_diff` baseline/cache writes; never hand-edit `.clarifydoc/`.
       If a read-only MCP server (for example, started with `--no-auto-analyze`)
       rejects `clarifydoc_analyze`, fall back (after user approval) to
       `clarifydoc analyze <repo-path>` if `clarifydoc` is on `PATH` (or via
       its installed launcher), or direct the user to VS Code's **ClarifyDoc:
       Analyze Workspace** (LSP enhancement needs Docker or **Resolve Calls
       with the Editor's Language Servers**).
     - Review diffs if a ClarifyDoc guidance command tries to inject a large
       boilerplate block into `AGENTS.md`.
3. **Shared portable workflow skills under `.agents/skills/`** (single copy
   loaded by both Antigravity and GitHub Copilot; do not duplicate into
   `.github/skills/`; set frontmatter `name:` equal to each folder name and use
   real paths from this repository):
   - `clarifydoc-review/SKILL.md` — analysis-only bounded query workflow.
   - `clarifydoc-before-editing/SKILL.md` — `file`, `blast_radius`, `explain`,
     `trace` callers, `context_docs`, `impact`.
   - `clarifydoc-before-writing/SKILL.md` — `search`, `search body=true`,
     `explain`, `extract` installed package APIs.
   - `clarifydoc-refactor/SKILL.md` — 4-phase map -> plan -> edit -> verify
     preserving `web/` -> `server/` -> `.NET` worker boundaries and keeping
     `shared/` free of React/browser/Node imports.
   - `clarifydoc-review-branch/SKILL.md` — `impact`, `contract` on gateway
     routes, `health`.
   - `clarifydoc-explain-screen/SKILL.md` — `screens`, `routes`, `data_flows`
     across UI and gateway.
   - `clarifydoc-trace-feature/SKILL.md` — end-to-end `UI -> gateway ->
     protocol/bridge -> .NET worker` chain (`search`, `screens`, `routes`,
     `data_flows`, `trace`).
4. **Optional canvas drawer:** if the project also generates a handoff canvas
   via `generate-handoff-canvas`, wire its optional `clarifydoc.mjs` drawer
   only if requested (`--no-auto-analyze --no-cold-start` reads, confirmed
   writes).

## 4. Verify before committing

1. Run `pwsh -NoProfile -File scripts/check-context-size.ps1 -FailOnWarning`
   and confirm exit `0`, zero duplicate skills across `.agents/skills/` and
   `.github/skills/`, `AGENTS.md` <= ~70 lines, and zero active MCP configs
   with machine-local paths.
2. Verify all relative links, frontmatter, and `.gitignore` coverage for
   `.clarifydoc/`.
3. Check `clarifydoc_status` first: only if it confirms the MCP server is bound
   to this repository root (not a home directory or another repo), ask before
   running an initial `clarifydoc_analyze`, then re-check `clarifydoc_status`.
   If the MCP server is read-only and rejects `clarifydoc_analyze`, ask before
   running `clarifydoc analyze <repo-path>` via the CLI (or **ClarifyDoc:
   Analyze Workspace**) and report CLI vs MCP evidence separately. If bound to
   a different root, do not call `clarifydoc_analyze`; report the root mismatch
   and instruct the user to launch `clarifydoc mcp <repo-path>`.
4. Note that newly created skills may require a fresh session to verify live
   discovery/activation.
