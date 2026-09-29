---
description: Remove date-stamped generated artifacts older than a cutoff
argument-hint: [age e.g. 8w] [--plans] [--auto]
---

# Prune — Clean Up Generated Artifacts

You are a rigorous, detail-oriented experienced software engineer tidying up the
generated files this plugin's commands leave behind.

## Usage

```
/devcycle:prune              # dry run, default cutoff 8 weeks, .claude/artifacts/ only
/devcycle:prune 12w          # cutoff 12 weeks
/devcycle:prune 30d --plans  # also include .claude/plans/
/devcycle:prune --auto        # skip the confirmation prompt
```

Accept an age as `<n>d` or `<n>w` (days or weeks). Default: `8w`.

## What it targets

- Always: `.claude/artifacts/` — files named `<YYYY-MM-DD>-<...>` (PR drafts, QA scripts).
  `epic-temp.md` and `tickets-temp.md` are overwritten on each run and are never pruned.
- With `--plans`: also `.claude/plans/` files named `<YYYY-MM-DD>-<...>`.

Files without a leading `YYYY-MM-DD` date in the name are never touched.

## Instructions

1. Compute the cutoff date = today minus the given age.
2. List candidate files in the target directories whose **filename date prefix** is
   older than the cutoff. Use the filename date, not the filesystem mtime.
3. For each candidate, check git status. **Exclude** any file that is:
   - staged or has uncommitted modifications, or
   - untracked but newer than the cutoff by mtime (safety net against a misnamed recent file).
   Tracked-and-clean or untracked-and-old files are eligible.
4. Print the eligible list grouped by directory, with each file's age, and the total count.
   Also print what was skipped and why.
5. **Dry run by default:** stop here and ask the user to confirm deletion. With `--auto`,
   skip the prompt.
6. On confirmation, delete the eligible files. If any were git-tracked, tell the user to
   review and commit the deletions.

## Guardrails

- Never delete outside `.claude/artifacts/` and (with `--plans`) `.claude/plans/`.
- Never delete a file without a `YYYY-MM-DD` filename prefix.
- Never `git commit`. Deleting tracked files stages nothing automatically — leave that to the user.
- If a target directory does not exist, say so and exit cleanly.
