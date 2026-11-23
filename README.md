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
| `/devcycle:dev` | Orchestrator for the full phase-gated workflow (plan → tests → execute → review → QA → PR description) |
| `/devcycle:parallel` | Several tickets at once — one agent and git worktree each, plans approved first |
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
| `/devcycle:prune` | Remove aged generated artifacts and, with `--worktrees`, merged worktrees |

### Typical workflow

1. `/devcycle:epic` and `/devcycle:ticket` — turn a feature into tickets with
   Given/When/Then acceptance criteria (written to `.claude/artifacts/tickets-temp.md`).
2. Build:
   - one ticket: `/devcycle:dev <ticket>` — plan → failing tests → code → review → QA →
     PR description, pausing for your approval after each phase (`--auto` runs straight
     through);
   - several tickets: `/devcycle:parallel` — see [Running tickets in parallel](#running-tickets-in-parallel-devcycleparallel).
3. `/devcycle:commit` for the commit message; open the PR with the generated description.
4. After merging: `/devcycle:prune` (add `--worktrees` after a parallel run).

Each phase command (`plan`, `test`, `execute`, `review`, `qa`, `pr`) also runs on its own.

## How it adapts to a project

Every command runs the discovery protocol in
`devcycle/resources/project-context.md` before doing work:

1. Reads `CLAUDE.md`, `.claude/resources/*.md`, `.ai/best-practices/`, `README`/`CONTRIBUTING`,
   and the repo's lint/style config.
2. Detects the stack from `Gemfile` / `package.json` / `go.mod` / etc.
3. If context is missing, asks targeted questions or proceeds on stated assumptions.

Your project's guidance always wins over the plugin's defaults, examples and thresholds.
Generated files (plans, QA scripts, PR descriptions, tickets) are written to disk and
reported by path.

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
| `parallel` branches | one git worktree + local branch per ticket under `.claude/worktrees/`, kept until you remove it | branch only |

`prune` deletes date-stamped files in `.claude/artifacts/` older than a cutoff (default 8
weeks); `--plans` extends it to `.claude/plans/`. It dry-runs and asks before deleting, and
never touches files without a `YYYY-MM-DD` name prefix or files with uncommitted changes.
`--worktrees` also removes worktrees under `.claude/worktrees/` whose branch is merged
(squash merges included, via `gh`), plus their local branches — never unmerged work.

### Recommended `.gitignore`

Everything devcycle generates is a local working file. Add these to the consuming repo's
`.gitignore` so running the commands never leaves the working tree dirty:

```gitignore
# devcycle generated files
.claude/artifacts/
# if you don't want plans tracked
.claude/plans/
# worktrees created by /devcycle:parallel
.claude/worktrees/
```

Keep `.claude/resources/` and `.claude/settings.json` tracked: they hold the shared project
context and plugin setup the whole team relies on.

### Running tickets in parallel (`/devcycle:parallel`)

```
/devcycle:parallel                         # every ticket in .claude/artifacts/tickets-temp.md
/devcycle:parallel path/to/tickets.md      # another tickets file
/devcycle:parallel --tickets 001,003       # only these tickets
/devcycle:parallel --max 2                 # at most 2 agents at once (default 4)
/devcycle:parallel --base develop          # start from develop (default: main, else master)
/devcycle:parallel --pr                    # push and open a PR for each branch once reviewed
```

What a run looks like:

1. **Preflight.** It checks whether your base branch is behind its remote and asks
   before updating it. It orders tickets into waves from their **Blocked By** lists, then
   asks you to confirm the waves, whether your tests and app can run several copies at
   once, and the branch names.
2. **Plans.** One agent per ticket drafts a plan in `.claude/plans/`. Nothing is built
   until you approve that ticket's plan. Approve, amend or drop each one as it arrives;
   an approved plan starts building straight away. Tickets that would touch the same
   files are flagged first.
3. **Build.** Each ticket gets its own agent, git worktree and local branch: tests, code,
   the checks for what changed, a local commit, a QA script and a PR description. If your
   tests can't run concurrently, they're run one branch at a time instead.
4. **Review.** A separate agent reviews each branch; blocking findings go back to the
   build agent to fix. A ticket that depends on another is planned and stacked on its
   branch only after that one passes review.
5. **Report.** A summary table, plus the paths of each ticket's plan, QA script and PR
   description (all in your main checkout's `.claude/`).

Run files (the shared project facts, and each build agent's brief and progress) go in
`.claude/artifacts/parallel/`, replaced on the next run.

Branches stay local unless you pass `--pr`. To push one yourself:
`git push -u origin <branch>`, then `gh pr create --head <branch> --body-file <PR description path>`.
You don't need to check out the worktree; it shares your repository.

#### Setting up a project for parallel runs

Each ticket runs in its own git worktree, which contains only tracked files. To let
agents run your tests there:

1. **Copy gitignored config** into every worktree with a `.worktreeinclude` file at the
   repo root (`.gitignore` syntax), e.g.

   ```text
   .env
   config/master.key
   ```

2. **Document worktree setup** in `.claude/resources/testing.md` under a
   `## Worktree setup` heading — the commands a fresh worktree needs. For a Rails app on
   Postgres or MySQL, give each worktree its own test database:

   ```yaml
   # config/database.yml
   test:
     database: myapp_test<%= ENV["DB_SUFFIX"] %>
   ```

   ```markdown
   ## Worktree setup
   - `bundle install`
   - `DB_SUFFIX=_<ticket> bin/rails db:test:prepare`, and prefix test runs with the same
     `DB_SUFFIX`
   - Clean up after removing the worktree: `DB_SUFFIX=_<ticket> RAILS_ENV=test bin/rails db:drop`
   ```

   SQLite apps need no database step: each worktree has its own database file.

3. Add `.claude/worktrees/` to `.gitignore` (see above).

## Installing

### Prerequisites

- **git** 2.5+ (worktrees).
- **GitHub CLI (`gh`)** — optional, needed for `/devcycle:parallel --pr` and for
  `/devcycle:prune --worktrees` to spot squash-merged branches. Install it, then sign in:

  ```bash
  brew install gh            # macOS; see https://cli.github.com for Linux and Windows
  gh auth login
  ```

### Install the plugin

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

Bump `version` in `devcycle/.claude-plugin/plugin.json`, note it in `CHANGELOG.md`, and
push. Consumers only receive a release when `version` changes.

Auto-update is off by default for third-party marketplaces. Consumers either:

- turn it on once: `/plugin` → **Marketplaces** → `ijapa` → **Enable auto-update**, or
- update manually: `/plugin marketplace update ijapa` in a session, or
  `claude plugin update devcycle@ijapa` in the shell.

## License

MIT — see `LICENSE`. Use it, fork it, adapt it to your own team's workflow.

The devcycle icon is the 🐢 emoji from [Noto Emoji](https://github.com/googlefonts/noto-emoji)
by Google, used under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).
