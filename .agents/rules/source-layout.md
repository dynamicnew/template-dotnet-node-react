---
trigger: model_decision
description: Rules for organizing code, naming files, managing shared limits, and synchronizing generated .NET-to-TypeScript contracts under web/, server/, shared/, or src/.
---

- Keep clear architectural boundaries: the React UI (`web/`) talks to the local
  Node gateway (`server/`), and the `.NET` Windows worker (`src/`) is the
  authoritative execution engine.
- Organize browser code in shallow feature groups (`web/src/features/`), app
  composition/global styles in `web/src/app/`, browser-only helpers in
  `web/src/shared/`, and framework-neutral cross-runtime contracts/helpers in
  root `shared/`. Do not import React, browser, or Node APIs into root `shared/`.
- Generate `shared/contracts.ts` from the `.NET` protocol bridge rather than
  hand-editing generated output; preserve generated-file notices and keep
  `.NET`, Node, build, and test paths synchronized when moving modules.
- Use PascalCase for React component filenames (`FeaturePage.tsx`) and C# files,
  `useName.ts` for React hooks, kebab-case for helpers and Node tests, and
  concrete imports rather than barrel exports.
- Do not repeat numeric limits across files or runtimes. Define shared limits
  once in a named, documented constant (worker limits generated into
  `shared/contracts.ts`; other cross-runtime limits in root `shared/`) and
  import everywhere. Never expose raw internal limits in user-facing copy.
- Keep a concise purpose header comment in maintained code files.
