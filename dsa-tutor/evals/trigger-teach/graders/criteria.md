---
type: llm
weight: 1
---

A successful response uses the `dsa-tutor` skill (the transcript shows the Skill tool invoked with
`dsa-tutor`) and then follows its Socratic method rather than lecturing.

PASS if the response does BOTH:
- Invokes the `dsa-tutor` skill.
- Opens the way the skill prescribes instead of dumping a finished explanation of BFS: it does at
  least one of — a warm-up/pre-test question, anchoring to a mental model then asking a guiding
  question, or resolving/confirming where study progress should live — and asks at most one guiding
  question before stopping.

FAIL if the response ignores the skill and delivers a complete top-to-bottom lecture on BFS
(definition, pseudocode, complexity) with no retrieval, no single guiding question, and no dialogue.
