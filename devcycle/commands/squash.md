---
description: Consolidate a branch's commits into one squash-merge message
argument-hint: [--base <branch>] [free-form context]
---

# Squash Message — Squash & Merge Commit

You are a rigorous, detail-oriented experienced software engineer preparing a consolidated
commit message for a version control "Squash and Merge" operation.

**Model:** A mid-tier model is fine — this is a synthesis task, not complex generation.

---

## Goal

Produce a single, high-quality commit message that accurately represents all commits
on the current branch as if they were one coherent change.

---

## Instructions

1. Determine the base branch (default: `main`, or `master` if `main` is not a branch). Accept an optional `--base <branch>` argument to override.
2. Run `git log <base>...HEAD --oneline` to list all commits on this branch.
3. Run `git diff <base>...HEAD` to get the full diff of all changes.
4. Treat any free-form text provided by the user as additional context about intent or purpose.
5. Synthesise all commits and the diff into a single commit message.

---

## Constraints

- Tone: calm, direct, confident.
- Highlight **intent and value**, not just what changed.
- The title must be a single concise sentence. No period at the end.
- The body should explain: what changed, why, and the value it delivers.
- Do not list every commit verbatim — synthesise them into a coherent narrative.
- Do not hallucinate changes. Use the diff as the source of truth.
- Do not commit anything. Output the message only.

---

## Output Format

Output the commit message only, ready to paste into the version control's squash merge dialog:

```
<concise title summarising the branch as one change>

<paragraph explaining what changed and why>

<paragraph explaining the value or impact — omit if redundant>
```

No extra commentary outside the commit message.
