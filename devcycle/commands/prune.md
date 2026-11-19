---
description: Remove date-stamped generated artifacts older than a cutoff, and merged worktrees
argument-hint: [age e.g. 8w] [--plans] [--worktrees] [--auto]
---

# Prune — Clean Up Generated Artifacts

You are a rigorous, detail-oriented experienced software engineer tidying up the
generated files this plugin's commands leave behind.

## Usage

```
/devcycle:prune              # dry run, default cutoff 8 weeks, .claude/artifacts/ only
/devcycle:prune 12w          # cutoff 12 weeks
/devcycle:prune 30d --plans  # also include .claude/plans/
/devcycle:prune --worktrees  # also remove worktrees whose branch is merged
/devcycle:prune --auto        # skip the confirmation prompt
```

Accept an age as `<n>d` or `<n>w` (days or weeks). Default: `8w`.

## What it targets

- Always: `.claude/artifacts/` — files named `<YYYY-MM-DD>-<...>` (PR drafts, QA scripts).
  `epic-temp.md` and `tickets-temp.md` are overwritten on each run and are never pruned.
- With `--plans`: also `.claude/plans/` files named `<YYYY-MM-DD>-<...>`.

- With `--worktrees`: also git worktrees under `.claude/worktrees/` (as left by
  `/devcycle:parallel`) whose branch is merged, and those branches. See below.

Files without a leading `YYYY-MM-DD` date in the name are never touched.

## Instructions

1. Compute the cutoff date = today minus the given age.
2. List candidate files in the target directories whose **filename date prefix** is
   older than the cutoff. Use the filename date, not the filesystem mtime.
3. For each candidate, check git status, including ignored files (e.g.
   `git status --porcelain --ignored`), since these directories are usually git-ignored.
   **Exclude** any file that is:
   - staged or has uncommitted modifications, or
   - untracked or git-ignored, but newer than the cutoff by mtime (safety net against a
     misnamed recent file).
   Tracked-and-clean files, and untracked or git-ignored files older than the cutoff by
   mtime, are eligible.
4. Print the eligible list grouped by directory, with each file's age, and the total count.
   Also print what was skipped and why.
5. **Dry run by default:** stop here and ask the user to confirm deletion. With `--auto`,
   skip the prompt.
6. On confirmation, delete the eligible files. If any were git-tracked, tell the user to
   review and commit the deletions.

## Worktrees (`--worktrees`)

The age cutoff doesn't apply; a worktree is eligible only once its branch is merged.

1. `git fetch --prune`, then list worktrees with `git worktree list --porcelain`. Keep
   only those whose path is under `<repo root>/.claude/worktrees/`.
2. A worktree's branch counts as **merged** if any of these hold:
   - `gh pr view <branch> --json state` reports `MERGED` (catches squash and rebase
     merges; skip this check if `gh` is unavailable);
   - the branch is listed by `git branch --merged <base>` (`<base>` = `main`, else `master`).
   Otherwise it's not merged — skip it.
3. **Exclude** a worktree that has uncommitted changes to tracked files, or untracked
   files that aren't git-ignored (`git -C <path> status --porcelain`). Also exclude one
   with commits added after the merge: for a merged PR, the branch tip must equal the
   PR's `headRefOid` (`gh pr view <branch> --json headRefOid`).
4. List the eligible worktrees with branch, merge evidence (PR merged / merged into base),
   and any git-ignored generated files inside them (PR description, QA script), which
   are deleted with the worktree — note whether a copy exists in `.claude/artifacts/`. List what was skipped and why.
5. Ask for confirmation, as in step 5 of **Instructions** (skipped with `--auto`). On confirmation, for each one:
   `git worktree remove <path>` (add `--force` only when the sole leftovers are
   git-ignored files), then `git branch -D <branch>` (`-D`, since a squash-merged branch
   isn't seen as merged by git). Finish with `git worktree prune`.

## Guardrails

- Never delete outside `.claude/artifacts/`, (with `--plans`) `.claude/plans/` and (with
  `--worktrees`) merged worktrees under `.claude/worktrees/` and their local branches.
- Never remove the main worktree, a worktree outside `.claude/worktrees/`, or an
  unmerged branch. Never delete remote branches.
- Never delete a file without a `YYYY-MM-DD` filename prefix.
- Never `git commit`. Deleting tracked files stages nothing automatically — leave that to the user.
- If a target directory does not exist, say so and exit cleanly.
