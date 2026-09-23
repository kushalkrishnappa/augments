---
type: llm
weight: 1
---

The phrases "keep forgetting" and "quiz me" should trigger the `dsa-tutor` skill's on-demand quiz
behavior.

PASS if the response:
- Invokes the `dsa-tutor` skill, AND
- Behaves like a quiz: asks the learner a question (one at a time) rather than lecturing, or first
  establishes/confirms which topic folder and study workspace to use before quizzing.

FAIL if the skill is not invoked, or if the response answers with a passive explanation/summary of
dynamic programming instead of quizzing the learner.
