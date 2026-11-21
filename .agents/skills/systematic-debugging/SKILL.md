---
name: systematic-debugging
description: Investigate real application failures, crashes, console errors, failed requests, disconnects, or failing tests across browser UI, local Node gateway, and authoritative .NET worker before changing code.
---

# Systematic debugging

Find the root cause of the observed failure, not just a way to make assertions
pass. Follow [AGENTS.md](../../../AGENTS.md) and control-safety boundaries. This
skill grants no permission to elevate privileges, change installed services, or
act on unrelated processes or services.

## Establish the actual environment

- Read `#current-handoff` in [docs/ai/handoff.md](../../../docs/ai/handoff.md)
  and relevant source before interpreting symptoms.
- Identify which surface and runtime failed (Docker fixture, development host,
  or installed app) and which built assets, gateway, and `.NET` worker it
  actually reached. Do not silently switch modes when an installed workflow fails.
- Preserve user state and unrelated processes. Prefer read-only observation;
  ask before any consequential or elevated action.

## Collect evidence before editing

1. Reproduce the reported symptom safely where feasible. If permissions or
   environment access block reproduction, report the blocker instead of
   inventing data.
2. Observe browser console errors, uncaught exceptions, page crashes, failed
   requests, and HTTP status codes before navigation. Inspect startup and
   gateway/worker stderr too.
3. Correlate the browser symptom with the HTTP request, gateway decision, and
   worker outcome. A rendered shell or listening port does not prove worker
   readiness.
4. Read full error messages and stacks, inspect recent diffs, and trace the
   faulty value backward through callers and generated contracts.

## Classify errors honestly

- An uncaught exception, crash, unexpected process exit, broken connection, or
  failed required operation blocks a healthy-app claim even if assertions pass.
- HTTP 4xx/5xx responses may not trigger `requestfailed`; inspect status codes
  and structured failure payloads directly.
- Distinguish warnings, expected security refusals, and real defects using
  evidence. Never globally suppress console/request errors or bypass worker
  security refusals with synthetic success.

## Test one hypothesis at a time

- Compare a working path with the failing path.
- State one evidence-backed hypothesis and run the smallest safe Docker check
  (or approved VM check) that disproves it. Do not stack speculative edits.
- Use bounded, condition-based waits rather than arbitrary sleeps or
  `networkidle` on polling UIs.
- Fix the root cause without weakening assertions or disabling safety gates.
- If three distinct attempts fail, stop and report what was ruled out and what
  remains unknown.

## Clean up and verify

Remove temporary diagnostics, stop only owned disposable helpers, and redact
credentials, launch links, cookies, and personal paths from durable reports.
Follow [verification-before-completion](../verification-before-completion/SKILL.md)
before claiming the fix is complete.
