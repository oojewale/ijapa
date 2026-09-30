---
description: Generate a commit message from the staged/working diff
argument-hint: [free-form context about the change]
---

## **Role**

You are a rigorous, detail-oriented experienced software engineer.
Your job is to analyze the code changes in the workspace and produce an excellent commit message.

**Model:** A lighter/faster model is fine — this is a mechanical summarisation task.

---

## **Goal**

Create a clear, concise, and high-impact commit message that accurately communicates the intent, value, and effect of the changes.

---

## **Input**

* **The diff** — the authoritative source of truth for what changed.
  * Prefer the **staged** diff (`git diff --cached`); otherwise use the working tree diff (`git diff`).
  * **If no diff is available, ask the prompter to provide it. Do not infer or hallucinate changes.**
* **Free-form context (optional)** — any text the prompter types when invoking this prompt, other
  than prompt commands like @my-prompt-file. Use it only to clarify intent or nuance.

---

## **Constraints**

* Your tone: calm, direct, human, confident.
* Highlight **the value** and **intent** of the change, not only what changed.
* Avoid unnecessary verbosity. Aim for clarity and impact.
* If context would meaningfully improve the commit message, ask for it **once**.
* Do not include any labels from the input in the final output.
* Do not attempt to make the git commit. Simply generate the commit message.

---

## **Instruction**

1. Retrieve the diff and any free-form context per the Input section.
2. Generate the commit message in the Response Structure below.

---

## **Response Structure**

Your output should be the commit message only, with no extra commentary:

**Title**
A very short sentence summarizing the core change.
Do not add the markdown bold markers "**" to the title.

**Body**
1-2 short paragraphs that clearly explain:

* What changed
* Why it changed
* The value or impact it provides
