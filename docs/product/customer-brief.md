# Customer brief (fill in)

Read this before any UI, copy, or feature change. If a change does not help the
target user, do not ship it.

## Who we build for

- *(Fill in: primary user persona, technical sophistication, workflow context.)*

## What they want

- *(Fill in: speed, control, observability, or reliability outcomes.)*

## What they fear

- *(Fill in: resource overhead, silent actions, confusing jargon, system damage.)*

## How the UI should feel

- **Fast:** minimal latency; avoid spinners where direct state updates work.
- **Dense and scannable:** tables, drawers, side panels, and stacked sections
  over oversized empty cards.
- **In control:** clear actions, explicit confirmation before risky operations,
  and honest per-target outcomes.
- **Stable:** calm transitions without layout shift under the pointer; respect
  `prefers-reduced-motion`.
- **Plain user-facing copy:** name the real effect without losing accuracy.
  Keep full engineering detail in developer docs and agent communication.

## Checklist for every UI or copy change

- [ ] Does it directly help the user stay fast and in control?
- [ ] Is user-facing copy clear, accurate, and free of raw internal limits?
- [ ] Are buttons consistent, real buttons with stable hit targets?
- [ ] Is the layout compact and usable at narrow widths and via keyboard?
- [ ] Do animations respect reduced-motion preferences without jumping?
- [ ] Are risky actions confirmed and protected targets safeguarded?
- [ ] Are unavailable metrics distinguished from `0` and labeled honestly?
