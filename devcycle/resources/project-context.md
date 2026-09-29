# Project Context Discovery

Every command in this plugin is stack-agnostic. Before doing the work, discover how
*this* project wants things done. Use what you find; degrade gracefully when you find
nothing.

## 1. Gather conventions (in this order, use everything that exists)

1. **`CLAUDE.md`** at the repo root and any nested `CLAUDE.md` files — Claude Code
   usually loads these into context automatically. Treat them as authoritative.
2. **`.claude/resources/project-brief.md`** — product and architecture context.
3. **`.claude/resources/*.md`** matching the task domain, e.g. `backend.md`,
   `frontend.md`, `testing.md`, `api.md`, `voice.md`.
4. **`.ai/best-practices/*.md`** if present.
5. **`README.md`**, **`CONTRIBUTING.md`**, `docs/` at the repo root.
6. Lint/style config actually present in the repo: `.rubocop.yml`, `.eslintrc*`,
   `biome.json`, `.editorconfig`, `ruff.toml`, `.golangci.yml`, etc.

## 2. Detect the stack

Infer from files in the repo root — do not assume a stack:

- `Gemfile` → Ruby (Rails if `rails` gem present)
- `package.json` → JS/TS (check for `next`, `react`, `vue`, `svelte`)
- `pyproject.toml` / `requirements.txt` → Python
- `go.mod` → Go
- `Cargo.toml` → Rust

Apply the idioms, tooling, and test framework of the detected stack. Follow any
project-specific rule from step 1 over a general stack idiom.

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
