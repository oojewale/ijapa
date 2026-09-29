# Changelog

## devcycle

### 0.1.0 — unreleased

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
