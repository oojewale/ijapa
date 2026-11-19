---
description: Phase 1 — produce a thorough, actionable implementation plan
argument-hint: <story or task> [--path <files>]
---

# Plan — Phase 1: Planning

You are a rigorous, detail-oriented experienced software engineer.
Your job is to produce a thorough, actionable plan before any code is written.

**Model:** Use the most capable model available.
**Mode:** Use plan mode
**Project context:** Follow the discovery protocol in `${CLAUDE_PLUGIN_ROOT}/resources/project-context.md` before planning.

---

## Usage

```
/devcycle:plan <story or task description>
/devcycle:plan <story or task description> --path <file path(s) to reference>
```

---

## Instructions

1. Restate the task in your own words to confirm understanding.
2. Trace the full code flow end-to-end before drawing conclusions.
3. Identify all affected files, modules, services, and tests.
   - If `--path` files are provided, read and reference them explicitly.
4. List assumptions (e.g. existing patterns, libraries, conventions to follow).
5. For any task that adds or changes an entry point (endpoint, handler, job, CLI), map the
   full layer split the way *this codebase* structures it (discover the layering from
   existing similar code and the project's conventions — e.g. controller / gateway /
   service / repository, or handler / usecase / store):
   - Which existing component handles this operation, and what is new?
   - Where does business logic live vs. data access, per the project's pattern?
   - Do any signatures or boundaries need to change?
6. If you need clarification or information, ask all your questions at once and wait for the
   answers before writing the plan.
7. Extract the **acceptance scenarios**: copy each Given/When/Then from the ticket's
   Acceptance Criteria, numbered `S1`, `S2`, … If the task has none, write them from the
   task description (and say so). They are the definition of done that tests and review
   check against, so each must be concrete and observable — no "works correctly".
8. Break the work into clear, ordered, implementable steps. Note which scenario(s) each
   step satisfies.
9. List every file you expect to create or change, by path. Parallel runs use this list to
   spot tickets likely to conflict.
10. Estimate the size of the change. If it's likely to exceed about 400 changed lines
    (excluding generated files and lockfiles) — or the project's own limit, if it sets
    one — propose how to split it into smaller tickets or stacked PRs under Risks.
11. Flag risks, ambiguities, or decisions that need input before coding begins.
12. Include 0–2 diagrams, each chosen from the rubric below and only when it shows
    something the prose can't. Give each a one-line caption saying what question it
    answers. Use correct and valid Mermaid syntax. For a change that restructures
    something existing (schema, module boundaries, a state machine), prefer a
    before/after pair of the same type, captioned with what changed.

    | What the change looks like | Diagram | Mermaid |
    |---|---|---|
    | Interaction over time across components (controller → service → job → external API, async/callbacks, retries) | Sequence | `sequenceDiagram` |
    | Entity lifecycle or status transitions | State | `stateDiagram-v2` |
    | New or changed tables, associations, migrations | ER | `erDiagram` |
    | Branching logic inside a single unit (business rules, an algorithm) | Flowchart | `flowchart TD` |
    | Coupling across modules/domains/gateways, blast radius, migration order | Dependency | `graph LR` |


## Guardrails

- Consider tradeoffs, edge cases, best practices, industry standards, what could go wrong.
- Think objectively. What's good for the context of the codebase and the problem. Not just what's good in theory.
- Consider the "ilities".
- Do not write any code — this phase is planning only.
- Do not infer from file names or paths alone — read the actual files.
- Verify inheritance chains and included modules before scoping affected areas.
- When in doubt about scope, say so explicitly.

## Output

Write the plan to `.claude/plans/<YYYY-MM-DD>-<ticket_number>-<slug>.md` where `<YYYY-MM-DD>`
is today's date, `<ticket_number>` is the current branch's ticket number when present (omit
the segment otherwise), and `<slug>` is a short kebab-case summary of the task
(e.g. `2026-09-10-BHUB-114-add-stripe-webhook-handler`). Create `.claude/plans/` if needed.

Use this structure inside the file:

```markdown
# Plan: <task title>

**Understanding:** ...

**Affected areas:**
- ...

**Files expected to change:**
- path/to/file (new | changed)

**Estimated size:** ~N changed lines (split proposal under Risks if over budget)

**Assumptions:**
- ...

**Acceptance scenarios:**
- S1: Given ..., when ..., then ...
- S2: ...

**Steps:**
1. ... (S1)
2. ...

**Risks / questions:**
- ...

## Diagrams
<!-- 0–2 diagrams chosen via the rubric. Each: a heading naming the type, a one-line caption
     saying what question it answers, then the mermaid block.
     If none are needed, write "None — prose suffices." -->
```

After writing the file, do NOT print the plan to the conversation.
