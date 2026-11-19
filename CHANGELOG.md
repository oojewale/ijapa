# Changelog

## devcycle

### 0.2.0 — 2026-11-19

- New `parallel` command: works several tickets at once, one subagent and git worktree
  per ticket. Plans are drafted in parallel and approved in the user's session before
  any code is written; each approved plan starts building right away, without waiting
  for the rest. Every branch gets an independent review, and blocking findings go back
  to its agent to fix. `Blocked By` orders tickets into waves, with dependents planned
  and stacked on their blocker's branch once it passes review. Branches stay local
  unless `--pr` is passed. Agents run the project's documented worktree setup (e.g. a
  per-worktree test database). Plan agents read a read-only checkout of the base branch,
  so you can switch branches in your main checkout mid-run. Preflight checks whether the
  local base branch is behind its remote and asks before updating it.
  Each ticket's QA script and PR description are copied to the main checkout's
  `.claude/artifacts/`, next to its plan in `.claude/plans/`, so they outlive the worktree.
- Acceptance scenarios drive the cycle (BDD): `plan` lists the ticket's Given/When/Then
  as numbered scenarios, `test` writes at least one test per scenario with a
  scenario → test map, and `review` checks every scenario is covered.
- `dev` adds Phase 5 (QA) and Phase 6 (PR description); parallel agents write both in
  their worktree, running QA only when the app can run several copies at once.
- PR descriptions draw on the ticket, plan and QA run. After a QA run the section is
  titled "QA Steps (Run by Claude)" and passed steps are ticked.
- `plan` lists the files it expects to change (parallel flags overlapping tickets before
  building) and flags changes over ~400 lines with a split proposal.
- `execute` treats "green" as the project's CI checks passing, scoped to the changed
  files and adjacent areas — not the whole suite — and not just the new tests.
- `review` runs in a fresh subagent when possible, so the author doesn't review itself.
- `prune --worktrees` removes worktrees (and local branches) whose branch is merged,
  including squash merges detected via `gh`, after a dry run.
- Every command: the project's own guidance wins over plugin defaults, and generated
  files are reported by path only, never printed.

### 0.1.0 — 2026-09-30

- Initial release: a stack-agnostic, phase-gated dev workflow plugin.
- Commands: `dev`, `plan`, `test`, `execute`, `review`, `qa`, `pr`, `commit`,
  `squash`, `ticket`, `epic`, `prune`.
- Each command discovers a project's own conventions at run time via
  `resources/project-context.md` (`CLAUDE.md`, `.claude/resources/*.md`, lint/style
  config, detected stack) rather than assuming any one language or framework.
- Plans are date-stamped in `.claude/plans/`. Epics and ticket sets overwrite
  `.claude/artifacts/epic-temp.md` and `tickets-temp.md`; other outputs are date-stamped
  in `.claude/artifacts/`. `prune` cleans up aged date-stamped files.
- Command frontmatter (`description`, `argument-hint`) added for the `/` menu.
- Commands specify a capability tier ("most capable" / "mid-tier" / "lighter/faster").
