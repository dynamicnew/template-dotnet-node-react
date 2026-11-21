# AI project handoff

Updated YYYY-MM-DD. Read [todo.md](todo.md) with this file.

## Current handoff

### Scope and repository state

- Branch, last verified commit, uncommitted work:
- What is built and verified (Docker portable / unit / UI fixture):
- What is built but unverified (installed / native Windows / UAC / tray / VM):

### Blockers and next actions

1.

### Checks and evidence limits

- Docker commands run, with exact pass/fail/skip results:
- Native / VM / installed checks not run, and why:

## Archived history

Dated archives, not current validation: see [handoff-history/](handoff-history/).

## Documentation maintenance

- Keep this file factual and under about 20 KB. Separate committed work,
  uncommitted work, fixture/container passes, native/VM qualification, and
  unknowns. Label historical results so they cannot be mistaken for fresh
  validation. Never include secrets, personal data, private launch URLs,
  session tokens, raw transcripts, or guesses.
- Keep durable shared decisions in reviewed docs (`docs/ai/decisions.md` or
  project instructions), not only in session or expiring memory; memory is not
  verified recall.
- Move dated sections unchanged to `handoff-history/handoff-<date>.md`,
  repairing relative links without rewriting the historical narrative and
  leaving a one-paragraph summary and link. Keep `#current-handoff`,
  `#archived-history`, and `#documentation-maintenance` stable.
- At milestones and before interruption, update this file and any document it
  links to in the same change, and check the links.
