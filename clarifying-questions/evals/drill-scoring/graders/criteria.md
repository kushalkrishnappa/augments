---
type: llm
weight: 1
---

The learner submitted a TAP drill answer that covers Outputs (no-pair value; indices vs values) and
Scale & scope (list length), but misses Bad input (e.g. empty or one-element list) and Inputs (e.g.
can values repeat, is the list sorted, negatives).

PASS if the final message does ALL of:
- Scores the answer category by category, marking Outputs and Scale & scope as covered.
- Names **Bad input** as missed AND ties it to the cue **shrink** (e.g. "shrink T to an empty list").
- Names **Inputs** as missed AND ties it to the cue **twist** (duplicate / reorder / negate).
- Gives at least one model question for each missed category.

Extra suggestions inside a covered category (e.g. "one more Outputs question you could ask") are
fine and do not count as marking that category missed.

FAIL if it does not organise feedback by category, misses either of the two gaps, attributes a gap
to the wrong cue, or scores Outputs or Scale & scope as not covered.
