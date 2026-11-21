---
trigger: model_decision
description: Rules for creating, editing, or reviewing files under docs/ (including docs/ai/handoff.md and docs/ai/todo.md).
---

- Keep one canonical source per concern; consolidate overlapping docs rather
  than maintaining competing overviews or checklists.
- Keep `docs/ai/handoff.md` factual and under about 20 KB; move dated sections
  unchanged to `docs/ai/handoff-history/`, repairing relative links without
  rewriting the historical narrative. Keep `#current-handoff`,
  `#archived-history`, and `#documentation-maintenance` stable.
- Link to files and headings (or symbols in link text), not line numbers (no
  `#L123` or `:123` suffixes in Markdown).
- When a document changes or moves, verify all incoming and outgoing relative
  links and anchors in the same change.
- Separate committed work, uncommitted work, plans, assumptions, and unknowns,
  and label historical check results so they cannot be mistaken for fresh
  validation.
- Treat `docs/ai/todo.md` as developer-owned planning notes: preserve developer
  text and unrelated entries, and never treat notes as authorization for
  destructive or privileged actions.
- Keep generated-file notices intact and update generators, not their outputs.
