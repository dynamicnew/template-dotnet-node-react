---
name: verification-before-completion
description: Run fresh scoped checks and inspect runtime health before claiming work is fixed, healthy, passing, or complete, distinguishing container fixtures from native/installed acceptance.
---

# Verification before completion

Evidence before claims. A passing test command is evidence about that command,
not proof that the running application or every native workflow works.

## Match evidence to the claim

Before editing, define the user-visible outcome, target environment, and
smallest relevant verification. Run all automated checks inside Docker
containers; never fall back to host-native tests or claim unrun suites passed.

| Claim | Required evidence |
|---|---|
| Build / typecheck passed | Fresh build output, exit code `0`, and synced generated contracts |
| Selected tests passed | Exact Docker command/selection, pass/fail/skip counts |
| UI fixture worked | Isolated container browser run with zero unexpected console/request errors |
| Dev-host workflow worked | Real read-only dev host, gateway, and worker (not installed qualification) |
| Installed / native worked | Installed UI, gateway, and worker in an approved disposable VM (not dev or fixture) |
| Fault verifier passed (if present) | Injected faults were caught (passing negative checks proves detection, not healthy runtime behavior) |
| Task complete | Requested outcomes verified and persisted, with no concealed remaining work |

## Check runtime health alongside assertions

- Start console, uncaught-exception, page-crash, failed-request, and HTTP
  status observation before page navigation and keep it through follow-up
  polling.
- Inspect gateway/worker stderr and unexpected process exits, not only
  screenshots or a connected badge.
- Unexpected console errors, required request failures, crashes, or lost
  readiness fail health acceptance even when assertions pass.
- Expected refusals require narrow, explicit assertions matching the real
  operation and resulting state; never globally suppress errors.

## Verify, clean up, and report

1. Run the relevant Docker commands after the final meaningful edit.
2. Read full output, exit codes, failures, and skips. Never assume exit `0`
   implies runtime health.
3. For delegated work, verify subagent diffs and exact command outputs yourself
   before accepting or committing.
4. Clean up temporary files, test fixtures, and owned helpers. Keep automated
   browser audio muted and never copy private launch URLs or secrets into docs.
5. Report exact commands, pass/fail/skip counts, environment exercised, and
   anything unverified or skipped (such as native Windows/UAC/tray checks).
6. If runtime evidence contradicts green tests, report the contradiction first
   and switch to [systematic-debugging](../systematic-debugging/SKILL.md).
