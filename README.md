# ijapa

Named after **Ìjàpá**, the tortoise of Yoruba folktales — a trickster who wins not by
force but by reading the situation and working resourcefully with what's already there.
This marketplace holds Claude Code plugins built in that spirit: nimble, and adaptive
to whatever project they land in rather than assuming one fixed way of working.

Currently one plugin, `devcycle`, which is stack-agnostic — it drives a phase-gated
dev process and discovers each project's own conventions at run time, so it works in any
repo.

## Plugins

### devcycle

| Command | Purpose |
|---------|---------|
| `/devcycle:dev` | Orchestrator for the full phase-gated workflow |
| `/devcycle:plan` | Phase 1 — planning |
| `/devcycle:test` | Phase 2 — failing tests that define the contract |
| `/devcycle:execute` | Phase 3 — implementation (TDD green) |
| `/devcycle:review` | Phase 4 — first-pass code review |
| `/devcycle:qa` | Turn manual QA scenarios into self-verified snippets |
| `/devcycle:pr` | PR description from the branch diff + repo template |
| `/devcycle:commit` | Commit message from the diff |
| `/devcycle:squash` | Squash-and-merge commit message |
| `/devcycle:ticket` | Project management tool Story creation (e.g JIRA, Linear, Trello) |
| `/devcycle:epic` | Project management tool Epic creation (e.g JIRA, Linear, Trello) |
| `/devcycle:prune` | Remove aged generated artifacts |

## How it adapts to a project

Every command runs the discovery protocol in
`devcycle/resources/project-context.md` before doing work:

1. Reads `CLAUDE.md`, `.claude/resources/*.md`, `.ai/best-practices/`, `README`/`CONTRIBUTING`,
   and the repo's lint/style config.
2. Detects the stack from `Gemfile` / `package.json` / `go.mod` / etc.
3. If context is missing, asks targeted questions or proceeds on stated assumptions.

### What a consuming repo should provide (all optional but recommended)

In `.claude/resources/`:

- `project-brief.md` — product and architecture context (used by `plan`, `dev`, `epic`, `ticket`)
- `backend.md` / `frontend.md` / `testing.md` — stack-specific conventions
- `copywrite.md` or `voice.md` — user-facing copy rules and tone

The plugin ships only generic templates: `epic.md`, `ticket.md`, and the
discovery protocol itself.

## Generated files

| Kind | Location | Tracked? |
|------|----------|----------|
| Plans | `.claude/plans/<YYYY-MM-DD>-<ticket>-<slug>.md` | no |
| Epics / ticket sets | `.claude/artifacts/epic-temp.md`, `.claude/artifacts/tickets-temp.md` (overwritten each run) | no |
| PR drafts / QA scripts | `.claude/artifacts/<YYYY-MM-DD>-<slug>.<kind>.<ext>` | no |

`prune` deletes date-stamped files in `.claude/artifacts/` older than a cutoff (default 8
weeks); `--plans` extends it to `.claude/plans/`. It dry-runs and asks before deleting, and
never touches files without a `YYYY-MM-DD` name prefix or files with uncommitted changes.

### Recommended `.gitignore`

Everything devcycle generates is a local working file. Add these to the consuming repo's
`.gitignore` so running the commands never leaves the working tree dirty:

```gitignore
# devcycle generated files
.claude/artifacts/
# if you don't want plans tracked
.claude/plans/
```

Keep `.claude/resources/` and `.claude/settings.json` tracked: they hold the shared project
context and plugin setup the whole team relies on.

## Installing

Add the marketplace, then install the plugin:

```
/plugin marketplace add oojewale/ijapa
/plugin install devcycle@ijapa
```

When asked for a scope, choose:

- **User** — just for you, in every repo.
- **Project** — for everyone who works on this repo. Claude Code writes the marketplace and
  plugin into the repo's `.claude/settings.json`; commit that file.

Run `/reload-plugins` (or start a new session) and the `/devcycle:*` commands appear.

### Teammates on a project-scoped repo

After pulling the committed `.claude/settings.json`, a teammate opens the repo in Claude Code,
trusts the folder, and accepts the prompt to add the ijapa marketplace. If the
`/devcycle:*` commands still don't show up, running `/plugin install devcycle@ijapa` once
fixes it.

For reference, the settings Claude Code writes look like this:

```json
{
  "enabledPlugins": { "devcycle@ijapa": true },
  "extraKnownMarketplaces": {
    "ijapa": { "source": { "source": "github", "repo": "oojewale/ijapa" } }
  }
}
```

### From a local clone (for plugin development)

```
/plugin marketplace add ../ijapa        # or an absolute path
/plugin install devcycle@ijapa
```

## Updating

Bump `version` in `devcycle/.claude-plugin/plugin.json`, note it in `CHANGELOG.md`,
push, and consumers run `/plugin update devcycle`.

## License

MIT — see `LICENSE`. Use it, fork it, adapt it to your own team's workflow.
