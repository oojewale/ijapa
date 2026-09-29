
# Story generation

When I ask you to generate a story or task, always use this exact structure:

---

**Title:** [short imperative title, e.g. "Add password reset flow"]

**User Story:**
As a [persona], I want to [action], so that [benefit].

**Context:**
[1–2 sentences explaining the why, any relevant product or technical context, and how this fits the bigger picture]

**Acceptance Criteria:**
- [ ] Given [context], when [action], then [outcome]
- [ ] (add as many as needed to fully define done)

**Out of Scope:**
- [Anything explicitly NOT included in this story. Only if genuinely worth mentioning]

**Dependencies / Notes:**
- [Related tickets, tech considerations, edge cases, open questions]

**Blocked By:**
- [Blocking ticket(s)]

**Testing:**
- [ ] Steps to manually test that the objective is met.
- [ ] Including edge cases
- [ ] Use "Confirm that ...." for the validation parts. E.g "Fill the sign up form. Confirm that you receive a client side error if the password does not match the password confirmation"

---

Rules:
- Infer the persona from context (e.g. "logged-in user", "admin", "new visitor") if not specified
- Use numbers for Acceptance Criteria, Out of Scope, Dependency & Blocked By sections
- Use the checkboxes for Testing section
- Use multi-turn clarification (max 3 turns): ask all questions upfront in Turn 1, follow up once more if needed in Turn 2, generate in Turn 3 (or earlier if you have enough). Never generate before at least one clarification round.
- Expand acceptance criteria beyond what is listed — think through edge cases and unhappy paths
- Make stories non-blocking as much as possible. So multiple tickets can be worked on in parallel by different people.
- Keep titles concise and action-oriented
- If something is still ambiguous after clarification, add it as an open question under **Dependencies / Notes**
- Output only the stories, no preamble
- Write all generated tickets to the location specified by the user, only if a location is specified.
  - Otherwise, write all generated tickets to `.claude/artifacts/tickets-temp.md`, replacing any existing content. Separate multiple tickets with `---`


# Tone

- The tone of the product is human, clear, friendly, confident, and most importantly, helpful.
