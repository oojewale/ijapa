# Project Context Discovery

## Rules that apply to every devcycle command

- **The project's guidance wins.** Whatever this protocol finds (`CLAUDE.md`,
  `.claude/resources/*.md`, best-practice docs, lint/style config, existing code
  patterns) overrides this plugin's generic defaults, examples and thresholds. Look
  before you write, and when the project and the plugin disagree, follow the project and
  say so.
- **Never print a generated file.** When a command writes a file (plan, QA script, PR
  description, epic, tickets, anything else), report only its path. Do not echo its
  contents to the conversation unless the user asks to see it.

Before doing the work, discover how *this* project wants things done.

## 1. Gather conventions (in this order, use everything that exists)

1. **`CLAUDE.md`** at the repo root and any nested `CLAUDE.md` files.
2. **`.claude/resources/project-brief.md`** — product and architecture context.
3. **`.claude/resources/*.md`** matching the task domain, e.g. `backend.md`,
   `frontend.md`, `testing.md`, `api.md`, `voice.md`.
4. **`.ai/best-practices/*.md`** if present.
5. **`README.md`**, **`CONTRIBUTING.md`**, `docs/` at the repo root.
6. Lint/style config actually present in the repo: `.rubocop.yml`, `.eslintrc*`,
   `biome.json`, `.editorconfig`, `ruff.toml`, `.golangci.yml`, etc.

## 2. Detect the stack

Infer it from the repo's manifest files; don't assume one. Apply its idioms, tooling
and test framework where the project sets no rule.

## 3. Degrade gracefully

If steps 1–2 leave a real gap that would change the output:

- **Prefer asking.** Pose 3–5 targeted questions in one numbered list.
- If asking is not appropriate for the command (or the user chose auto mode),
  proceed using widely accepted best practice for the detected stack, and state
  the assumptions you made at the top of your output.

Never invent a project convention. If you are guessing, say so.

## 4. Generated-file locations

- **Plans:** `.claude/plans/<YYYY-MM-DD>-<ticket-or-slug>.md`
- **Epics and ticket sets:** `.claude/artifacts/epic-temp.md` and
  `.claude/artifacts/tickets-temp.md`, overwritten on each run unless the user specifies
  another location
- **Everything else** (PR drafts, QA scripts): `.claude/artifacts/`, named
  `<YYYY-MM-DD>-<slug>.<kind>.<ext>`

Use today's date for `<YYYY-MM-DD>`. Create the directory if it does not exist.

Never stage or commit plans or artifacts.
