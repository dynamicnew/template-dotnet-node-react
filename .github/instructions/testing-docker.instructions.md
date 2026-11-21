---
applyTo: "tests/**, docker/**, compose.yaml, package.json, playwright*.config.ts, vite.config.ts"
description: Docker-only test execution, test layout, evidence tiers, Playwright acceptance, and Windows VM qualification boundaries.
---

- Keep all tests and test-only helpers under `tests/`, grouped by runtime/suite
  (for example, `tests/node/` mirroring server/web source paths, `.NET` xUnit
  tests, Playwright fixture/live suites, and PowerShell stub tests).
- Run all automated tests, including subagent tests, only inside Docker
  containers (`network_mode: none`, non-root, dropped capabilities, no host or
  Docker-socket mounts). Never fall back to host-native test runs.
- Linux containers cover portable TypeScript builds, shared/browser Node tests,
  isolated Chromium UI fixtures, and script doubles. They cannot run native
  Windows worker, UAC, tray, or installed-app qualification. Interactive
  Windows qualification requires an approved disposable Windows VM and an
  explicit user exception to Docker-only testing; never substitute fixtures.
- Distinguish evidence tiers honestly: unit/mock passes, isolated browser
  fixtures, development-host runs, fault-injection verifier runs (pass means
  faults were caught, not healthy app), and real installed acceptance are
  separate gates.
- Real read-only UI acceptance must attach to an explicit readiness target,
  fail clearly when readiness is missing (never fall back to fake data), and
  observe console errors, uncaught exceptions, failed requests, and backend
  readiness.
- Keep automated browser audio output muted. Never persist private launch URLs,
  session tokens, cookies, or raw machine payloads in test reports.
- When running focused Node tests, pass explicit file paths
  (`node --test path/to/file.test.ts`); `node --test <directory>` fails.
