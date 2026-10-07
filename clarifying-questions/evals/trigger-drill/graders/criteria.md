---
type: llm
weight: 1
---

The learner asked to be drilled on clarifying questions. Grade the final message as the opening of
a TAP drill.

PASS if the final message does ALL of:
- Presents exactly one deliberately underspecified interview problem, phrased as an interviewer would say it.
- Asks the learner to write a T line — one concrete example call with the expected output left
  as `?` (shape: `f(<input>) == ?`) — AND to list the clarifying questions they would ask, then
  stops for their answer. A generic "work through an example first" does not count.
- Does NOT reveal the problem's hidden ambiguities, model questions, or an answer key, and does not
  solve the problem.

FAIL if it presents several problems, never asks for a `== ?`-style T line, starts answering
the learner's questions for them, lists the questions they should ask, or solves the problem.
