---
trigger: model_decision
description: Guardrails for creating, editing, or reviewing a project-local handoff canvas panel (.github/extensions/<canvas-id>/ or .agents/plugins/<canvas-id>/).
---

- Load the `generate-handoff-canvas` skill before creating a handoff canvas
  panel from scratch.
- Keep `docs/ai/handoff.md` and `docs/ai/todo.md` as the sole sources of truth;
  the panel never creates a parallel handoff record or executes TODO text.
- For Copilot extensions (`.github/extensions/<canvas-id>/extension.mjs`), keep
  a direct child folder under `.github/extensions/` (3 parent hops back to Git
  root; no `package.json`/VSIX), bind only
  `127.0.0.1:0` per `instanceId`, close connections/controllers in `onClose`,
  enforce `Host` (`403`) + `timingSafeEqual` capability (`404`) + same-origin
  `x-handoff-token` on writes (`403`), build DOM via `textContent` only (no
  `innerHTML`, no remote assets), scrub Git remote credentials, validate OS
  links with `realpath` + non-executable allowlist, and strip `ELECTRON_*`
  before spawning link handlers.
- Optional action/ClarifyDoc drawers require explicit allowlists, fresh user
  confirmation, Docker-only test commands, lock-cleanup preview (never kill by
  image name), and redaction of raw launcher output containing private URLs.
- For Antigravity side panes (`.agents/plugins/<canvas-id>/`), use an MCP App
  (`ui://` `text/html;profile=mcp-app`, `@modelcontextprotocol/ext-apps`,
  `aux_pane` entrypoint) with no secrets or machine-local paths.
- Verify panel security guards and dark/light/`380px` layout inside Docker
  containers; never fall back to host-native tests.
