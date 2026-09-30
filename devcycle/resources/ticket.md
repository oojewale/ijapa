
# Story generation

When I ask you to generate a story or task, always use this exact structure:

---

**[Ticket number, e.g. 001]**

**Title:** [short imperative title, e.g. "Add password reset flow"]

**User Story:**
As a [persona], I want to [action], so that [benefit].

**Context:**
[1–2 sentences explaining the why, any relevant product or technical context, and how this fits the bigger picture]

**Acceptance Criteria:**
1. Given [context], when [action], then [outcome]
2. (add as many as needed to fully define done)

**Out of Scope:**
- [Anything explicitly NOT included in this story. Only if genuinely worth mentioning]

**Dependencies / Notes:**
- [Related tickets, tech considerations, edge cases, open questions]

**Blocked By:**
- [Number(s) of the ticket(s) this one depends on]

**Testing:**
- [ ] Steps to manually test that the objective is met.
- [ ] Including edge cases
- [ ] Use "Confirm that ...." for the validation parts. E.g "Fill the sign up form. Confirm that you receive a client side error if the password does not match the password confirmation"

---

Rules:
- Infer the persona from context (e.g. "logged-in user", "admin", "new visitor") if not specified
- Use numbers for Acceptance Criteria, Out of Scope, Dependencies / Notes and Blocked By sections
- Use checkboxes for the Testing section
- Expand acceptance criteria beyond what is listed — think through edge cases and unhappy paths
- Make stories non-blocking as much as possible. So multiple tickets can be worked on in parallel by different people.
- Keep titles concise and action-oriented
- Output only the stories, no preamble


# Tone

- The tone of the product is human, clear, friendly, confident, and most importantly, helpful.
