# Read-list: implement

Use for a bounded change the coordinator has already planned.

- **Read:** the files the task owns, their tests, and the section of the plan
  or handoff the task cites by heading.
- **Do not read:** other sections of the handoff, history archives, or files
  owned by another subagent.
- **Do:** make the change, keep `.NET`/gateway/UI boundaries and generated
  contracts aligned, run targeted checks inside Docker containers (never
  host-native test fallbacks), and remove temporary files you created.
- **Do not:** refactor files outside the task, commit, elevate privileges, or
  change settings.
- **Return:** at most 15 lines: files changed, Docker commands run with exact
  results, and anything unverified.
