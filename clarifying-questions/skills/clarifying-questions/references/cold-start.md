# Cold start — the first 2–5 minutes when your mind is blank

## Why this happens

- In a randomised trial, solving at a whiteboard while watched cut correctness by more than half compared with solving alone, and raised stress and cognitive load [S38].
- Anxiety reliably reduces working memory [S48]. Pressure hits hardest at the people with the most working memory, on the problems that need it most [S49].
- Anxiety costs efficiency (time and effort) before accuracy [S51].

So a blank isn't missing knowledge. It's attention knocked off track, with less room to think. The fix is to move thinking out of your head onto the page and give yourself a first step that needs no ideas [S62].

## Blank on a new problem

You've never seen this problem and nothing comes. Do the moves that are pure transcription first:

1. **Start cue.** When the interviewer stops talking, type `# T:` `# A:` `# P:` on three lines and say "Let me jot down what I'm hearing." This needs no ideas, and it doubles as a natural buy-time line [S80][S15].
2. **Copy T.** `# T: f(<copied input>) == ?`. Copy their example. If there isn't one, make a three-item example from the prompt's nouns ("meetings" → `[[1,3],[2,6],[8,10]]`). For OOD, write `# NOT: …` and then `# T: c = Cls(args); c.op(x) -> ?`: a constructor plus one call sequence with `-> ?` outputs. This is copying, not solving [S6][S19].
3. **The `?` hands you your first question.** Try to fill it in. Whatever you can't fill confidently ("touching meetings: merge or not?") is your first clarifying question, asked as a guess: "I'd return `[[1,6],[8,10]]`. Right?" [S6][S63].
4. **A acts on T.** Type `shrink? | stretch? | twist?` and change your T example once per cue. Each change is concrete, so you don't need to invent questions from nothing [S65][S66].
5. **At P, if no approach comes ~60 s after A, use the fog rule** (below). Don't sit and wait for insight [S58].

Experts spend longer on scoping and gather information across more categories than novices do [S43]. The first three moves need no algorithm knowledge, which is why they work when you're blank.

Short silence is fine if you say what you're doing ("I'll hand-trace this example for a minute, then share") [S3][S15].

## Stuck starting a familiar problem

You *know* this one (Two Sum, LRU cache, merge intervals) but the start won't come, or you're about to skip ahead.

- **Never skip the `?` or shrink "because it's obvious."** Familiarity is exactly when people drop the contract and bad-input questions. Analysts who know a domain skip "trivial" questions and miss unstated assumptions, and across 12 quasi-experiments, experience *hurt* elicitation in familiar domains [S45][S46].
  - Two Sum: the `?` asks indices or values? Can I use the same element twice? What if no pair works?
  - LRU: shrink asks about capacity 0. The `?` asks what `get` returns on a miss and whether `put` on an existing key counts as a use [S23].
- **Name the pattern in P and write its template as comments**, then fill it in on your T example. The template cues recall and takes load off your working memory [S50][S6][S62].
- **Don't narrate every micro-step of the parts you know.** Over-monitoring a well-practised skill makes it worse [S50].
- **If the template won't come back, that is the fog rule's trigger.** Hand-solve T as comments and type a brute force. The pattern usually reappears from your own trace.
- If you're truly stuck, ask a specific question or say what you're leaning toward. That's better than spinning in silence [S15][S39].

## The fog rule

> **"If no approach — or the template won't come back — ~60 s after A, then hand-solve T as comments and type a brute force."**
>
> **Resume cue:** the first empty label is where I am.

It's one rule for both paths (blank on a new problem, stuck on a familiar problem) and for DSA and OOD alike. The "then" is a typed action, which is the part of an if–then plan that does the work [S58][S59].

**Hand-solve T as comments.** Write down what you did in your head when you filled the `?`. For merge-meetings:

```python
# T: merge([[1,3],[2,6],[8,10]]) == [[1,6],[8,10]]   # touching merges (<=)
# [1,3]          → start: [1,3]
# [2,6]: 2 <= 3  → merge → [1,6]
# [8,10]: 8 > 6  → new   → [1,6], [8,10]
```

Often the algorithm is now on the page: walk in order and compare each meeting with the last merged one. Solving one concrete example by hand and then reverse-engineering the steps spares the working memory that goal-driven searching burns [S6][S66].

**Then type a brute force, even if the hand-solve already showed you the real approach.** It's the safe artefact on the page, and you improve from it:

```python
def merge(meetings):
    # brute force: repeatedly merge any overlapping pair until nothing changes. O(n^2) per pass
    ms = [list(m) for m in meetings]
    changed = True
    while changed:
        changed = False
        for i in range(len(ms)):
            for j in range(i + 1, len(ms)):
                a, b = ms[i], ms[j]
                if a[0] <= b[1] and b[0] <= a[1]:  # [lo, hi] intervals touch or overlap
                    ms[i] = [min(a[0], b[0]), max(a[1], b[1])]
                    ms.pop(j)
                    changed = True
                    break
            if changed:
                break
    return sorted(ms)
```

Brute force is "fine… an initial benchmark" [S3]. Outside the fog rule, just say it in one sentence. Inside the fog rule, type it, so you have something written to resume from [S6][S34].

**Resume cue.** If you're interrupted or lose the thread, look at your labels. The first empty or unfinished one is where you are. Tracking which stage you're in made novice programmers more independent [S64][S80].

## If–then plans to rehearse

Say these aloud before practice sessions. Every "then" is something you do, never "focus harder" [S58][S59].

- If the interviewer stops talking, then I type `# T:` `# A:` `# P:` and say "Let me jot down what I'm hearing."
- If no example was given, then I make a three-item one from the prompt's nouns and type it into T with `== ?`.
- If I can't fill the `?` confidently, then I ask the part I can't fill as a guess: "I'd return X. Right?"
- If T is filled, then I type `shrink? | stretch? | twist?` and change T once per cue.
- If an answer wouldn't change my code, then I fold it into the "I'll assume …, OK?" sentence.
- If the interviewer says "your call", then I state the assumption, type it after `# A:`, and move on.
- If ~3 min (DSA) or ~5 min (OOD) have passed, then I say the bundled assumption sentence and type `# P:`.
- If no approach — or the template won't come back — ~60 s after A, then I hand-solve T as comments and type a brute force.
- If I lose my place, then I find the first empty label and do that line.
- If the thought "I'm going to freeze" shows up, then I type the next character on the first empty label.

The last plan sets the distracting thought aside by acting, the kind of plan that helped anxious students, instead of fighting it [S59].

## What not to do

| Don't | Why | Do instead |
|---|---|---|
| Tell yourself "just relax / calm down" | No evidence it helps in the moment. Suppressing feelings impaired verbal memory, while reappraisal did not, and reframing as excitement did as well or better than calming down [S54][S55][S68]. | Type the next label. |
| Try "don't think about failing" | Suppressed thoughts rebound, with an immediate boost specifically under cognitive load [S69][S70]. | Use an if–then plan whose "then" is a typed action. |
| Plan to "focus harder" or "try harder" | Effort-escalating plans cut anxious students' performance (54.4 vs 77.7). Anxious people already over-spend effort [S59][S51]. | "Then I hand-solve T as comments." |
| Read "blank" as "I don't know this" | Anxiety costs speed and effort first, not knowledge [S51]. | Use the fog rule; the knowledge comes back through the trace. |
| Talk the whole time | Communication is the weakest predictor in platform data. Short silence is allowed, and talking while thinking while coding was reported as extra load [S16][S3][S38]. | Narrate decisions, not every thought. |
| Ask a set number of questions, or clarify for 10 minutes | Question quotas are folklore. Long clarifying conflicts with a 35-minute two-problem block and reads as stalling [S10][S14][S25][S2][S27]. | Done-test + one bundled "I'll assume …, OK?" |
| Fish: "Hash map or sort?" / "What's the target complexity?" | It reads as fishing for the answer [S3][S24]. | State a choice: "I'm leaning toward sorting first. OK?" |
| Rely on box breathing or a pre-interview worry-writing session to fix the blank | Breathing evidence is about daily practice for mood, with no performance outcome [S67]. The worry-writing effect failed in a 21-study meta-analysis [S56][S57]. | Type the next label; the fix is the typed action. |
| Say the framework's name aloud | Scripts sound scripted [S33]. | Just do the moves. |
