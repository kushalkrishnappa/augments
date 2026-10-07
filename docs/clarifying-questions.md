# clarifying-questions

Teaches and drills **TAP (Test · Ask · Plan)** — a memorable structure for the first 2–5 minutes of a
coding interview (live DSA, whiteboard, or OOD/practical) — so you ask the right clarifying questions
about constraints and edge cases, and still have a first move when your mind goes blank.

## Install

    /plugin marketplace add kushalkrishnappa/augments
    /plugin install clarifying-questions@augments

## Use

Just ask:

- "Teach me how to start a coding interview problem" → **teach mode** (Socratic, one question at a time)
- "Drill me on clarifying questions" → **drill mode** (underspecified problem → you write your T line and
  questions → scored by category, each miss tied back to the cue that finds it)
- "I blank when I see a new problem" → teach mode, including the cold-start moves

## TAP in one glance

```python
# T: merge([[1,3],[2,6],[8,10]]) == ?    # copy an input; ? = the expected return value — work it out
# A: shrink? | stretch? | twist?         # mutate T; ask only if the answer changes code
# P: sort by start; sweep and merge — O(n log n)
```

`?` → Outputs · shrink → Bad input · stretch → Scale & scope · twist → Inputs. Working out the `?` by
hand is the point: the part you can't decide is your first clarifying question.

## What's inside

| File | Contents |
|---|---|
| `references/framework.md` | TAP, question categories, tagging rubric, natural phrasings, DSA + OOD worked examples |
| `references/cold-start.md` | Moves for "blank on a new problem" and "stuck starting a familiar problem", the fog rule |
| `references/practice-habits.md` | Long-term habits that make fog rarer |
| `references/drill-bank.md` | Underspecified drill problems with hidden ambiguities |
| `references/sources.md` | Citations, each tagged [research] or [practitioner] |

## State

None. The skill is stateless and never writes files. For spaced-repetition tracking of DSA topics,
use `dsa-tutor`.

## Scope

Covers the opening minutes (read → clarify → plan) and long-term practice habits. Does not cover
solving the problem, mid-solution recovery, pre-interview routines, or online assessments.
