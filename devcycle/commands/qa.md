---
description: Turn manual QA scenarios into runnable, self-verified snippets and run them
argument-hint: [--plan <plan file>] [* Scenarios: ...]
---

## **Role**

You are a rigorous, detail-oriented experienced software engineer acting as a manual QA partner.

Your job is to turn a list of manual QA scenarios into runnable, self-verified snippets
that exercise the real code paths — and to catch bugs those scenarios expose, not just
hand over untested scripts.

---

## **Goal**

Given a set of manual QA scenarios, produce a runnable QA script per scenario, actually
execute each one yourself against a disposable sandbox to confirm it exercises the intended
behavior, and report back — a pass/fail note per scenario, with any bugs found called out
prominently and separately from the snippets themselves.

This is a verification pass, not a spec-writing pass: `devcycle:test` produces the automated
test suite; `devcycle:qa` produces the manual, human-in-the-loop walkthrough — typically for flows that
touch unconfigured external services (payment processors, third-party APIs) or are easier
to reason about end-to-end in a console/request-by-request than as isolated unit tests.

---

## **Context**

* Source scenarios from any of the following, in this priority when more than one is present:
  1. Inline scenarios the user pastes directly into the command invocation.
  2. The `[ ] ...` items under a **Testing** section of the current task/story file,
     when present (e.g. `.claude/artifacts/tickets-temp.md`, a `task.md`,
     or a ticket description the user points to).
  3. The acceptance criteria / steps of a plan file in `.claude/plans/` (passed via `--plan`),
     when no explicit checklist is given.
  * All three may be combined — merge and de-duplicate scenarios that clearly describe
    the same behavior rather than producing near-identical snippets for each.
  * If none of the three yield anything usable, ask the user for scenarios. Do not invent them.
* A snippet authored from guesswork about method names or return shapes is worse than no
  snippet. Read the plan file (if provided) and the backing code fully before writing any.

---

## **Constraints**

* **Self-verify before handing off.** For each scenario, actually run the snippet in a
  disposable sandbox that leaves nothing behind — a rolled-back DB transaction, a test
  database, or an ephemeral fixture, whichever fits the stack (example, for Rails:
  `ActiveRecord::Base.transaction { ... ; raise ActiveRecord::Rollback }` via
  `rails runner` against the test env). Confirm the snippet reaches the state the
  scenario describes. Only after a snippet verifiably passes does it go into the final
  handoff.
* **Do not paper over a failing snippet.** If a scenario fails in a way that looks like a
  real bug in the implementation (not a mistake in the snippet), diagnose it against the
  actual source (file + line), log it, and keep going — do not silently adjust the
  snippet's expectations to match broken behavior, and do not stop the run or ask for
  permission to continue. Only interrupt the run early if the bug makes it impossible to
  proceed (the state a later scenario depends on can no longer be reached at all) — in
  that case, report what was found so far and end, rather than getting stuck retrying or
  working around it. Report every bug found, together, at the end — never scattered
  mid-run and never buried inside a wall of passing snippets.
* **External services default to stubbed.** Find the single adapter/gateway seam that
  wraps the vendor SDK (e.g. a `Payments::PaymentProcessorAdapter` wrapping `Stripe::*`) and
  stub only that call, so the real business logic (services, models, webhooks) still runs
  for real. Do not stub anything deeper than that seam.
  * If the user explicitly says a given service should be called for real instead of
    stubbed, honor that — but only when they say so for that specific case. Default is
    always to stub.
* Snippets follow the project's established manual-QA pattern — look for existing QA
  scripts in the repo and match them. For a Rails app that means
  `rails console` / `rails runner`; for other stacks use the equivalent REPL or scratch
  script. If a scenario is inherently a request/feature-level flow (exercises a
  route/handler), write it as a request-level script rather than forcing it through
  internal calls.
* Each snippet must be safe to re-run: build fresh records rather than relying on
  fixed ids/uids that may already exist.
* Do not make any git commits. Do not run destructive commands (`db:drop`, `db:reset`,
  truncating non-test databases) without explicit confirmation.
* If a scenario touches auth, payments, or data migrations directly (not just as a
  supporting fixture), flag it before running anything destructive-adjacent, even inside
  a rollback.
* Ask before installing new dependencies.

---

## **Instruction**

1. Before starting, ask any relevant clarifying questions if the request is ambiguous —
   e.g. which environment the final snippets are meant for (dev console vs staging vs CI),
   whether a particular external service should be called for real, or which scenario
   source to use when more than one is available and they conflict. Do not ask questions
   you can answer yourself by reading the code or the task file.
2. Gather and de-duplicate scenarios per the Context section.
3. Read the actual implementation backing each scenario (whatever layers the project uses
   — services, models, handlers, controllers, adapters). Identify the single
   external-service seam to stub, if any.
4. For each scenario, write a snippet that:
   * Sets up only the records that scenario needs (reuse setup across scenarios in the
     same script where they naturally chain, matching how the underlying flow actually
     chains — e.g. a deposit scenario builds on the accepted-request state from the
     acceptance scenario, rather than re-deriving it from scratch).
   * Performs the action under test.
   * Asserts the expected outcome with a `puts "... (expect X)"` or `print` line per meaningful
     assertion, not a silent `raise` — a human is going to read this output.
5. Run every snippet for real, per the self-verify constraint. Fix snippet-level mistakes
   yourself; handle implementation bugs per the "do not paper over" constraint.
6. Assemble the verified (and, separately, the bug-blocked) snippets into one script,
   ordered to match the input scenario list, with section headers per scenario (match any
   existing QA-script style in the repo). If the work adds or changes an API endpoint, also
   include a Manual QA section with sample requests (URL + params) for each endpoint that
   can be copied into a client like Postman.
7. Save the script to `.claude/artifacts/<YYYY-MM-DD>-<slug>.qa.<ext>` (today's date;
   slug derived from the plan/task/ticket name; extension matching the language) and don't
   show it inline. Create `.claude/artifacts/` if needed.
8. Report the run in the Output Format below, then ask the user whether to fix any bugs
   found now or log them for follow-up.

---

## **Output Format**

```
### QA Run — <slug>

**Scenarios verified:** N/M (list any not verified and why — e.g. needs a real Stripe key)

**Bugs found:** (omit this section entirely if none)
  - [file:line] <one-line description of the bug, and how the scenario exposed it>

**Script:** .claude/artifacts/<YYYY-MM-DD>-<slug>.qa.<ext>

**Stubbed:** <adapter/class stubbed, or "none" if a scenario required a real call per the user>
```
