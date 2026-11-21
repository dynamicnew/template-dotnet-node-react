---
name: generate-handoff-canvas
description: Generate or recreate a project-local handoff canvas panel (GitHub Copilot extension under .github/extensions/<canvas-id>/ or Antigravity MCP App side pane under .agents/plugins/<canvas-id>/) tailored for .NET + Node + React + Docker projects without shipping prebuilt panel code in the template.
---

# Generate a project handoff canvas panel (.NET + Node + React + Docker)

Build a self-contained, source-backed handoff panel that reads Git state and
`docs/ai/handoff.md`, optionally edits `docs/ai/todo.md`, and optionally exposes
explicitly confirmed build/Docker-test/launcher workflows or a read-only
ClarifyDoc MCP workbench. The panel is a view of repository docs — never a
second handoff record.

Ask which host target and optional drawers are wanted if unclear: **Copilot
canvas extension** (`.github/extensions/<canvas-id>/`), **Antigravity MCP App
side pane** (`.agents/plugins/<canvas-id>/`), or both; read-only core only vs
optional TODO editor, confirmed project actions, and ClarifyDoc workbench.

## Target A: Copilot project-local canvas (`.github/extensions/<canvas-id>/`)

Place all files directly in a child folder under `.github/extensions/`
(`.github/extensions/<canvas-id>/extension.mjs` — 3 parent hops back to the Git
root; `<canvas-id>` is lowercase kebab-case, e.g. `<app>-handoff`). No
`package.json`, `npm install`, VSIX, or manifest is required: the host
discovers `extension.mjs` automatically and provides
`@github/copilot-sdk/extension`. Git must be on `PATH`.

Resolve the repository root from `extension.mjs` as:
`const ROOT = path.resolve(path.dirname(fileURLToPath(import.meta.url)), "..", "..", "..");`

### Core files to generate

| File | Responsibility |
|---|---|
| `settings.mjs` | Exports `CANVAS_ID`, `PROJECT_NAME`, `BASE_BRANCH` (default `"main"`), `HANDOFF_PATH` (`"docs/ai/handoff.md"`), `TODO_PATH` (`"docs/ai/todo.md"`), and `MAX_HANDOFF_BYTES` (`256 * 1024`). |
| `extension.mjs` | Imports `{ CanvasError, createCanvas, joinSession } from "@github/copilot-sdk/extension"`. Tracks `instances = new Map()` (`instanceId -> { server, url }`). Starts one ephemeral loopback server (`server.listen(0, "127.0.0.1")`) per instance, reuses it on reopen, and closes server + `closeAllConnections()` (plus any shared optional controllers when the last panel closes) in `onClose`. Registers `createCanvas({ id, displayName, description, actions: [get_status], open, onClose })` and calls `await joinSession({ canvases: [canvas], commands })`. |
| `status.mjs` | Concurrent read-only `execFile("git", ..., { cwd: root, timeout: 10_000, windowsHide: true, maxBuffer: 1024 * 1024 })` (no shell): branch, `status --short`, `status --porcelain=v1 -z` (skip the extra rename/copy path after `[RC]`), recent commits, `@{upstream}`, `remote get-url origin` scrubbed to `https://github.com/<owner>/<repo>` (discard credentials/non-GitHub URLs), ahead/behind vs `BASE_BRANCH`, and handoff freshness (`log -1` + `rev-list --count <last>..HEAD`). Reads `HANDOFF_PATH` up to `MAX_HANDOFF_BYTES`; returns diagnostics instead of throwing. |
| `links.mjs` | Validated OS link opener for `POST /api/open` (`{ url }`). Allows only `http:`, `https:`, `mailto:`, `vscode://file/...`, and `file://` for safe non-executable binaries (`png|jpg|jpeg|gif|webp|ico|pdf|zip|mp3|mp4|wav`). Verifies `realpath(root)` + `realpath(target)` containment and `stat().isFile()`; rejects control chars, double-encoding, credentials, and Windows reserved device names (`con`, `prn`, `aux`, `nul`, `com1-9`, `lpt1-9`). **Critical:** strip `ELECTRON_*` env vars (`ELECTRON_RUN_AS_NODE`) before spawning `shell: false` (Windows: PowerShell `-EncodedCommand` `Start-Process`; macOS: `/usr/bin/open`; Linux: `xdg-open`, 10 s timeout). |
| `ui.mjs` | Exports `PAGE = String.raw\`<!doctype html>...\`` with zero remote fonts/scripts and `__HANDOFF_ACTION_TOKEN__` replaced once per instance (avoid raw backticks inside the template literal). Build all DOM exclusively via `document.createElement` + `textContent` (never `innerHTML` on repo/Git text). Render sticky header + Refresh, snapshot cards (Branch, Working tree, Origin, Handoff file), freshness banner (green `0`, amber `1-4`, alert `5+`), collapsible **Progress notes** with searchable Date/Section disclosures + full text view (skipping fenced code headings), collapsible commits/changes lists, quick-open buttons, light/dark + reduced-motion + forced-colors support, and no horizontal overflow at `380px`. |
| `README.md` | `/canvases` usage, safety model, optional drawers, file table, and Docker verification commands. |
| `verify/panel-smoke.mjs` | Runs inside the project's network-isolated Docker Playwright container (`--network none`, repo `:ro`) with a stub `@github/copilot-sdk/extension` and canned `git` shim. Checks status parsing, credential scrubbing, `404` wrong capability, `403` bad host, `403` missing token, dark/light/`380px` layout, and `onClose`. |

### Optional stack-tailored modules (add only when requested)

- `canvas-launcher.mjs`: Registers `/canvases` via `joinSession({ canvases, commands })`
  using `session.rpc.canvas.list()`, `session.capabilities.ui?.elicitation`,
  `session.ui.select(...)`, and `session.rpc.canvas.open(...)` with a
  deterministic `instanceId` (`base64url(extensionId + "\0" + canvasId)`) so
  reopening focuses the existing panel.
- `notes.mjs`: Fixed-path **Developer TODO notes** editor for `TODO_PATH`
  (`docs/ai/todo.md`, max 64 KiB) at `GET`/`PUT /api/notes`. Enforces:
  - Rejects symlinks and hardlinks (`lstat`, `nlink === 1n`, inode check).
  - SHA-256 optimistic version tokens (HTTP `409` on stale save; warns before
    discarding unsaved edits on reload/close).
  - Serialized in-process save queue + cross-host exclusive lockfile
    (`docs/ai/.todo.lock`; never auto-stolen on crash) + atomic temp rename.
  - Never executes TODO text or writes to the handoff.
- `actions.mjs`: Collapsed **Project actions** drawer (`GET`/`POST /api/actions`)
  for explicitly allowlisted Build / Run / Server / Docker-Testing batches:
  - One extension-wide batch at a time; checkbox confirmation cleared on any
    selection or access-mode change; server independently validates every batch.
  - Before Clean/Build, runs the checkout's lock-cleanup helper (`-WhatIf`
    preview, then exact PID/start-time stop — never kill by image name).
  - Test actions run **only in Docker** (never host-native test fallbacks).
  - Tracks retained panel-started launchers by process exit (not stdout pipe
    closure), sends graceful stdin quit rather than force killing, caps console
    output at 64 KiB, and **withholds raw launcher output** so private runtime
    URLs/tokens never leak into the panel.
- `clarifydoc.mjs`: Collapsed **ClarifyDoc** MCP workbench (`/api/clarifydoc`)
  using a fixed allowlist, live schema validation, `--no-auto-analyze --no-cold-start`
  reads, repo-path confinement, and fresh confirmation before `analyze`/`diff`.
- `appearance.mjs` + `design-tokens.css` / `design-effects.css`: Optional
  read-only sync with saved app appearance tokens (`/api/appearance`).

### Non-negotiable security invariants

1. Generate two 32-byte random hex secrets (`randomBytes(32).toString("hex")`)
   per instance: URL `capability` (`/<capability>/`) and same-origin `token`
   (`x-handoff-token`), validated with `timingSafeEqual`.
2. Enforce `req.headers.host` is `127.0.0.1:<port>` or `localhost:<port>`
   (`403` otherwise) and valid capability (`404` otherwise) before any route.
3. Require `x-handoff-token`, `origin === "http://" + host`, and
   `content-type === "application/json"` on every `POST`/`PUT` endpoint (`403`
   otherwise) with strict body caps (`<= 8192` bytes for opens/actions).
4. Serve HTML with `cache-control: no-store`, `x-content-type-options: nosniff`,
   `referrer-policy: no-referrer`, and
   `content-security-policy: frame-ancestors 'self' vscode-webview: vscode-file: https://*.vscode-cdn.net`.

## Target B: Antigravity MCP App side-pane panel (`.agents/plugins/<canvas-id>/`)

Antigravity does not load `.github/extensions/*/extension.mjs`. Follow
Antigravity's built-in `mcp-app-development` skill to build a workspace plugin
in `.agents/plugins/<canvas-id>/` (`plugin.json`, `mcp_config.json` with a
Stdio server using `${PLUGIN_ROOT}` in `args` and `${PLUGIN_DATA}` for any
writable state — never relative CWD, literal secrets, or hardcoded user paths).
Reuse `status.mjs` (and optional `notes.mjs`), placing those modules directly
inside `.agents/plugins/<canvas-id>/` when generating Target B standalone
without Target A. Serve one self-contained `ui://<canvas-id>/handoff.html`
(`text/html;profile=mcp-app`, built with `@modelcontextprotocol/ext-apps`), and
expose an empty-arg entry tool with
`_meta["com.google.antigravity/ui"].entrypoints` (`aux_pane` / `global`).

## Verification before completion

1. Run `verify/panel-smoke.mjs` inside Docker (`--network none`, repo `:ro`);
   never fall back to host-native tests as equivalent Docker acceptance. If the
   Docker Linux engine or Playwright container image is not yet available (for
   example, during initial pre-Docker bootstrap), report Docker verification as
   blocked/pending rather than claiming a pass.
2. Verify security rejections (`404`/`403`), credential scrubbing, optional
   notes stale-save (`409`) and lock behavior, dark/light/`380px` rendering with
   zero console/page errors, and clean `onClose` teardown.
3. Report live-host behavior (VS Code registration, native OS link opens, real
   launcher lifecycle) as unverified unless directly tested.
