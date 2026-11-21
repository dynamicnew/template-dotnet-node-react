# Codebase maps (optional, read on demand)

Add concise, evidence-backed reference maps here only as the project grows, so
agents can inspect a single concern without reading large source trees or
inflating always-on `AGENTS.md`:

- `ARCHITECTURE.md` — process/layer boundaries (UI -> Node gateway -> `.NET` worker) and system flow.
- `STACK.md` — runtimes, build systems, manifests, and key commands.
- `STRUCTURE.md` — entry points and folder navigation (navigation only, not duplicate rules).
- `CONVENTIONS.md` — naming, imports, generated contracts, error handling, and doc comments.
- `TESTING.md` — Docker suites, scope matrix, and native/VM evidence boundaries.
- `INTEGRATIONS.md` — runtime integrations kept distinct from developer-only MCP/editor tools.
- `CONCERNS.md` — open engineering/tooling observations and audit leads.

Keep maps factual, cite supporting files instead of repeating lockfile versions
or historical test totals, and avoid duplicating rules owned by `.agents/rules/`
and `.github/instructions/`.
