# Project guidance

The only file loaded every session; read everything else on demand. Keep this
file under 70 lines.

## Project essentials (fill in)

- **Stack & layout:** <!-- React + TypeScript + Vite (`web/`), Node gateway (`server/`), framework-neutral `shared/` (`shared/contracts.ts`), authoritative `.NET` Windows worker (`src/`), `tests/`, `docker/` -->
- **Commands:** <!-- build: `npm run build` | ui: `npm run build:ui` | typecheck: `npm run typecheck` | contracts: `npm run generate:contracts` | Docker tests: `pwsh -NoProfile -File scripts/Manage-Docker.ps1 -Action Test` -->
- **Do not edit:** <!-- generated outputs (`shared/contracts.ts`), packaged installer outputs, or unrelated repos in a multi-root workspace -->

## When to read what

- Small task (one file, question, quick fix): read nothing extra.
- Substantial work: consult `find-skills` (if installed) once before planning.
- Multi-file or cross-session work: read `#current-handoff` in
  [docs/ai/handoff.md](docs/ai/handoff.md) and [docs/ai/todo.md](docs/ai/todo.md),
  then verify against Git and source (read `todo.md` for TODO questions).
- UI, copy or feature work: read [docs/product/customer-brief.md](docs/product/customer-brief.md).
- Large docs: locate the relevant heading and read only that section.
- Procedures (coordination, debugging, verification, memory, canvas, or
  `setup-clarifydoc` on adoption/init): load from `.agents/skills/`.
- Explicit user direction overrides any standing rule here.

## Rules

- **Truthful.** Run checks before claiming success. Distinguish unit/fixture
  passes from installed/native qualification. Report failures, skips and
  unknowns plainly. Never record secrets, personal data or launch URLs.
- **Docker-only tests.** Run all automated and subagent tests in Docker; never
  fall back to host-native tests. Native Windows/UAC/tray qualification needs
  an approved disposable Windows VM.
- **Safety & clean.** Preserve uncommitted work. Test destructive paths only on
  owned disposable fixtures; extra editor/browser profiles need dedicated
  `--user-data-dir` and `--extensions-dir`. Clear locked build files by exact
  PID or lock script, never image name. Remove own scratch files and dead code.
- **Commits.** Commit verified batches (main agent / coordinator only;
  subagents do not commit). Do not push or rewrite history unless asked.
- **Ask first.** Stop and ask before changing IDE, user, account or billing
  settings; accessing signed-in accounts; deploying; elevating; or destructive
  actions.
- **Cost & communication.** Read [docs/ai/cost.md](docs/ai/cost.md) for model
  tiers and delegation. Be terse; no progress narration; end with one short
  report (files changed, exact checks, unverified).
- **Memory.** Record verified lessons with `project-remember`. Keep shared
  decisions in `docs/ai/decisions.md`, not only in expiring memory.

## Subagents

Give each subagent only its bounded task, exclusive file ownership and read-list
from [docs/ai/read-lists/](docs/ai/read-lists/) (`research`/`Explorer` ->
`explore.md`, `implementer`/`Implementer` -> `implement.md`,
`reviewer`/`Reviewer` -> `review.md`). `handoff`/`Project Handoff` reads
`docs/ai/handoff.md` and `docs/ai/todo.md` directly.

## Map

| Need | Where |
|---|---|
| State, TODOs, decisions, lessons, cost | [docs/ai/](docs/ai/) (`handoff.md`, `todo.md`, `decisions.md`, `memory.md`, `cost.md`) |
| Product brief & optional codebase maps | [docs/product/customer-brief.md](docs/product/customer-brief.md), [docs/codebase/README.md](docs/codebase/README.md) |
| Conditional rules (Antigravity / Copilot) | [.agents/rules/](.agents/rules/), [.github/instructions/](.github/instructions/) |
| Subagent roles (Antigravity / Copilot) | [.agents/agents/](.agents/agents/), [.github/agents/](.github/agents/) |
| Shared skills (Antigravity + Copilot) | [.agents/skills/](.agents/skills/) |
