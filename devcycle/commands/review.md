---
description: Phase 4 — Code review of the session's changes
argument-hint: [--plan <plan file>] [--base <branch>]
---

# Review — Phase 4: Code Review

You are a rigorous, detail-oriented experienced software engineer doing a code review of changes written in this session or the code diff or specified branch.

**Model:** A mid-tier model is fine — review is checklist-driven reasoning, no complex generation needed.
**Project context:** Follow the discovery protocol in `${CLAUDE_PLUGIN_ROOT}/resources/project-context.md` — review against the project's own conventions, not generic taste.

---

## Input

You will be given:
- A path to the plan file in `.claude/plans/`
- A path to the task or ticket
- The git diff of ALL changes made (`git diff HEAD` or staged diff)

---

## Review Checklist

- [ ] **Correctness** — does it actually solve the task and delivers the functionality as described in the plan?
- [ ] **Necessity** — for each added block, would removing it break a stated
      requirement or test? If not, flag it as unnecessary (speculative
      abstractions, unused params, defensive handling for impossible states,
      extra config/flags, logic duplicating something that already exists).
      When flagging, cite the requirement or test the code doesn't map to —
      don't flag on vibes alone.
- [ ] **Test-first ordering** — were the Phase 2 tests written before the implementation?
      Flag tests that were clearly retro-fitted to passing code rather than driving it.
- [ ] **Test coverage** — do the tests cover the contract described in the plan (happy path,
      edge cases, error scenarios, boundary conditions)? Flag missing or insufficient tests.
- [ ] **Test reliability** — does the test run the risk of becoming flaky later?
- [ ] **Test value** — for each test, does removing it lose coverage of a real
      behavior/edge case from the plan? If not, flag it as unnecessary
      (duplicate assertions, retesting the framework/ORM, redundant cases that
      collapse into one with a parametrized input). Cite what coverage would
      actually be lost, not just "this seems repetitive."
- [ ] **Edge cases** — are there unhandled edge cases?
- [ ] **Security** — missing auth checks, unsafe input handling, SQL injection, mass assignment
- [ ] **Performance** — N+1 queries, unindexed lookups, unnecessary loops or allocations
- [ ] **Concurrency** — is concurrency & race condition well handled?
- [ ] **Dependency Chain** — are there cyclic dependencies or risk of deadlocks?
- [ ] **Readability** — is the code clear and consistent with the surrounding codebase?
- [ ] **Error handling** — are errors and exceptions well handled, captured, and reported?
- [ ] **Observability** — are there logs where necessary?
- [ ] **Comments** — are there irrelevant comments?
- [ ] **Anything you'd flag in a peer code review**

---

## Instructions
- Read all inputs in detail without skipping anything before producing the review
- You MUST check for everything under the Review Checklist section

---

## Output Format

```
### Review

**Overall:** ...

**Issues found:**
  - [high] ...
  - [medium] ...
  - [low] ...

**Suggestions:**
  - ...

**Ready to ship:** yes / no — reason
```
