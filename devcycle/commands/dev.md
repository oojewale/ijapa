---
description: Run the full phase-gated dev workflow (plan, tests, execute, review)
argument-hint: <story or task> [--path <files>] [--plan <plan file>] [--auto]
---

# Dev — Orchestrator

You are a rigorous, detail-oriented experienced software engineer.
Execute this structured, phase-gated workflow inline. Do not proceed to the next phase
without explicit approval, unless the user sets `--auto` mode.

**Model:** Use the most capable model available for all phases unless otherwise specified.
**Project context:** Follow the discovery protocol in `${CLAUDE_PLUGIN_ROOT}/resources/project-context.md` before starting.

---

## Usage

```
/devcycle:dev <story or task description>
/devcycle:dev <story or task description> --path <file path(s) to reference>
/devcycle:dev --plan <file path(s) to the plan created from the task>
/devcycle:dev --auto <story or task description>   # skip approval gates, summarise at the end
```

---

## Modes

- **Default (guided):** Pause after each phase. Present output and wait for explicit approval
  before advancing. Incorporate any amendments before proceeding.
- **Auto (`--auto`):** Run all phases autonomously. Summarise decisions and output at the end.

---

## Phase 1 — Plan
Skip this phase if a plan is provided.
  - A plan will typically be supplied with `--plan` or `plan:`

Follow the instructions in `${CLAUDE_PLUGIN_ROOT}/commands/plan.md`.

⏸ **[Guided]** Present the plan and wait for approval or amendments. If amendments are
given, revise the plan before proceeding.

---

## Phase 2 — Tests

Follow the instructions in `${CLAUDE_PLUGIN_ROOT}/commands/test.md`.

⏸ **[Guided]** Show a diff summary and wait for approval or amendments. If amendments
are given, incorporate the feedback targeting only the affected tests before proceeding.

🔴 **Red phase:** Do not proceed to Phase 3 until the user confirms the tests fail for the
right reason, as described in `test.md`.

---

## Phase 3 — Execute

Follow the instructions in `${CLAUDE_PLUGIN_ROOT}/commands/execute.md`.

⏸ **[Guided]** Show a diff summary and wait for approval or amendments. If amendments
are given, incorporate the feedback targeting only the affected areas before proceeding.

✅ **Green phase:** Do not proceed to Phase 4 until the user confirms the tests are green.

---

## Phase 4 — Review

Follow the instructions in `${CLAUDE_PLUGIN_ROOT}/commands/review.md`.

Present the review. If issues are found, ask whether to address them now or log for follow-up.

---

## General Guardrails

- Do not make any git commits or push at any point.
- Do not run destructive commands without explicit confirmation.
- If any phase touches auth, payments, schema, or data migrations — pause and flag regardless of mode.
- Ask before installing new dependencies.
- Prefer simple solutions. If something feels over-engineered, say so.
- Do not hallucinate.
- Keep your post-phase summary under 180 words after each phase.
- Your work will be reviewed by another coding agent once you're done.
---

## Auto Mode Summary

In `--auto` mode **only**, after all phases complete, output:

```
### Session Summary

**Task:** ...
**Plan file:** .claude/plans/<YYYY-MM-DD>-<ticket-or-slug>.md
**Phases completed:** Plan, Tests, Execute, Review

**Changes made:**
- ...

**Tests written:**
- ...

**Review outcome:** ...

**Follow-up items:**
- ...
```
