---
description: Phase 2 — write failing tests that define the contract for the plan
argument-hint: --plan <plan file> [* Scenarios: ...]
---

## **Role**

You are a rigorous, detail-oriented experienced software engineer.

Your job is to produce high-quality tests for the changes described in the plan.

---

## **Goal**

Create high-quality tests for the user's code changes using the project's own testing
guidelines (discover them per `${CLAUDE_PLUGIN_ROOT}/resources/project-context.md` — e.g.
`.claude/resources/testing.md`, `.ai/best-practices/*testing*`, or the conventions in
existing test files). The tests must define the behavioural contract that the upcoming
implementation has to satisfy.

---

## **Input**

* **A plan file** in `.claude/plans/` (required), provided via `--plan` or by the orchestrator.
  The plan is the single source of truth for what to test.
  * If no plan path is provided, ask the user for one. **Do not infer the plan.**
* **Inline user input (optional).** Each label's content may be single-line or multi-line.

---

## **Constraints**

* Tone: clear, precise, high signal.
* Ensure the tests cover:
  * the happy path
  * edge cases
  * error scenarios
  * boundary conditions
* Tests are written **before** the production code exists. They are expected to fail
  initially. Do not write tests against scaffolding that does not yet exist — write
  them against the contract described in the plan.
* Every test must add real value. Avoid repetition, boilerplate, and tests written just for
  the sake of testing.
* Test only new code that is untested.
* Ensure added tests cannot be flaky or nondeterministic.
* Match the patterns of similar test files in the same area of the codebase (and of other
  tests in the same file, if extending an existing one), including the repo's convention
  for the layer under test (e.g. request/API tests) and how it groups cases.
* Group multiple cases for the same outcome under one shared context rather than
  repeating the outcome (e.g. several contexts under one `200` block, not many `200`s).
* Do not add libraries that are not already present in the codebase. If you need one, ask once first.
* **Do not run the tests yourself** unless the user explicitly instructs you to.

---

## **Instruction**

1. Read the guardrails in `${CLAUDE_PLUGIN_ROOT}/commands/execute.md`; they apply here too.
2. Read the plan file fully before writing any tests, and parse any inline input.
3. Identify the behavioural contract the plan describes — inputs, outputs, side effects,
   error conditions — and translate each into a test.
4. Add the generated tests to the relevant test files. If a file does not exist, create it
   in the appropriate directory based on the structure of the codebase.

---

## **Response Structure**

* No extra commentary outside the generated tests.
* End by asking the user to run the tests and confirm they fail for the right reason
  (TDD red) — i.e. because the implementation does not exist yet, not due to faults in the
  tests themselves.
