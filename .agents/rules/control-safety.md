---
trigger: model_decision
description: Safety rules for process/service control, gateway boundaries, worker execution, locked files, and disposable destructive test fixtures.
---

- Keep target validation, protected-target checks, and execution authority in
  the authoritative `.NET` worker. The Node gateway validates authentication,
  CSRF, method, and payload shape, but must never implement an independent
  enforcement engine.
- Preserve exact process identity and service-name verification, protected
  target lists, explicit per-target outcomes, and confirmation/preview paths.
- Exercise destructive operations only against explicitly created, owned
  disposable fixtures (for example, disposable child processes or opt-in
  fixture services) and assert both the resulting state and cleanup. Never stop
  pre-existing or unrelated processes or services during testing.
- Distinguish refusal, unavailable worker, timeout/unknown outcome, and partial
  success explicitly; never silently report an ambiguous or failed action as
  successful, and never auto-retry a mutation whose remote outcome is unknown.
- Resolve locked build outputs without blocking builds or tests: if the
  repository provides a lock-inspection script, run it with `-WhatIf` first,
  then stop only the confirmed offending PID or owned service. Never kill
  processes by image name, never stop OS-critical processes, and report
  everything stopped.
- Keep contract changes synchronized with generated `shared/contracts.ts` and
  boundary tests. Never claim privileged service, UAC, signing, or tray
  lifecycle readiness without real environment evidence.
