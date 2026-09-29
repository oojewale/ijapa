
# Epic generation

When I ask you to generate an Epic, always use this exact structure:

---

**Title:** [short imperative title, e.g. "Add password reset flow"]

**Type:** Epic

**Overview:**
[1-3 sentences explaining the overview of what the epic is about]

**Problem**
[2–3 sentences explaining the problem we're solving and, and how this fits the bigger picture]

**Objective:**
[What we want to get done in this epic]

**Solution:**
- [How we want to solve the problem]

**Assumptions:**
- [Assumptions we made]

**Risks & Mitigations:**
- [List the risks and mitigation steps]

**Open Questions:**
- [Any open questions]

---

Rules:
- Gather project general context from `.claude/resources/project-brief.md`
- Use multi-turn clarification (max 3 turns): ask all questions upfront in Turn 1, follow up once more if needed in Turn 2, generate in Turn 3 (or earlier if you have enough). Never generate before at least one clarification round.
- If something is still ambiguous after clarification, add it as an open question under **Open Questions**
- Output only the epic, no preamble
- Write the generated epic to the location specified by the user, only if a location is specified.
  - Otherwise write the generated epic to `.claude/artifacts/epic-temp.md`, replacing any existing content.


# Tone

- The tone of the product is human, clear, friendly, confident, and most importantly, helpful.
