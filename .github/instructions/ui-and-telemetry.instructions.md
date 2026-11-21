---
applyTo: "web/**, docs/product/**"
description: Design, accessibility, motion, audio, and telemetry honesty rules for browser UI and machine-monitoring views.
---

- Read [docs/product/customer-brief.md](../../docs/product/customer-brief.md)
  before any UI, copy, or feature change and check its UI checklist.
- Build a compact, responsive local control console: prioritize readable tables,
  clear actions, keyboard access, accessible text alternatives, reduced-motion
  support, and narrow-screen usability. Apply visual polish selectively without
  sacrificing legibility, truthful data, or stable hit targets.
- Press feedback must never shrink or shift an interactive hit target (use an
  opacity/surface overlay instead of scale/translation that moves bounds).
- Keep user-facing buttons looking and behaving like buttons; avoid turning
  whole cards into hidden buttons.
- Keep automated browser audio output muted. Stop/mute actions must cancel
  active playback cleanly and prevent stale callbacks.
- Keep monitoring and telemetry strictly honest:
  - Distinguish unavailable or missing measurements from `0` and label what
    each metric actually measures (for example, disk capacity is not disk
    activity; link utilization is not internet congestion; resource usage is
    not responsiveness).
  - Plot actual sample timestamps and preserve missing-data gaps rather than
    interpolating unmeasured values.
  - Describe alert thresholds and status badges as heuristics, not diagnoses,
    and verify threshold numbers against current code.
