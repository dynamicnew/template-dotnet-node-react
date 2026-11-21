---
trigger: model_decision
description: Code element and API documentation requirements for TypeScript, JavaScript, C#, and PowerShell source files.
---

- Add at least a one-line purpose comment for every main maintained code
  element: classes, functions, methods, hooks, validators, and nontrivial
  helpers. Explain contracts, state transitions, and safety constraints rather
  than narrating obvious syntax.
- Document parameters and return values on every parameterized function,
  method, constructor, hook, component, or nontrivial helper using the
  language's native format:
  - TypeScript / JavaScript: JSDoc/TSDoc `@param name -` (including
    `props.field` for destructured props) and `@returns` when non-void.
  - C#: XML docs `<summary>`, `<param>`, and `<returns>`.
  - PowerShell: comment-based help with `.SYNOPSIS` and `.PARAMETER`.
- Include usage specifics that affect callers: null/empty semantics, units,
  side effects, cancellation, caching, `@throws`, and trust-boundary or
  read-only constraints. Update docs whenever signatures or behavior change.
- Preserve generated-file headers and update generators instead of hand-editing
  generated outputs.
