# Practice habits that make fog rarer

## The habits

| Habit | What | How often | How to do it with TAP / drill mode | Evidence |
|---|---|---|---|---|
| **1. Retrieve the skeleton cold** | Open a blank file and type the three labels, the A line and the fog rule from memory. Don't re-read them first. | Start of every practice session (~1 min) | Type `# T:` `# A: shrink? \| stretch? \| twist?` `# P:` plus the fog-rule sentence, then check against framework.md's "At a glance". Early on, write the skeleton and read it as you go. Once it's automatic, do the moves from memory and confirm against the page afterwards. | Retrieval beat re-study at one week (61% vs 40%), and re-reading inflated confidence. Recall beats recognition, and the gains show up after days, not minutes [S71][S72][S36] |
| **2. Interleave DSA and OOD** | Mix formats and look-alike patterns within one session instead of blocking by type. | Every drill session | Ask drill mode for a mixed set (for example DSA → OOD → DSA). Pick problems whose patterns look alike (sliding window vs two pointers vs prefix sums) so the `?` and twist have to do real work. | Mixed practice improved one-week test scores sharply even though it felt harder. Interleaving helps most when the categories are similar to each other [S73][S74] |
| **3. Practise aloud, mildly watched** | Run the opening out loud, with someone watching or a recorder running. | 2–3 times a week; at least one live mock a week | Say your read-back, your questions and the bundled sentence aloud during drills. Better still, have a friend play the interviewer while you type T/A/P on a shared screen. Drill mode itself only presents a prompt and scores your answer. | Being watched is the stressor that halves performance. Practising under mild anxiety kept athletes from choking later. Rehearsing with others predicted feeling prepared, and 5+ mocks went with about 2× pass odds (observational vendor data) [S38][S75][S76][S77][S18] |
| **4. Rehearse the if–then plans** | Say the plans from cold-start.md aloud. | Before each session (~1 min) | Read each "If …, then I …" once, then act out the fog rule on the current drill's T. | If–then plans help most with *starting* an action. Action plans helped anxious students, while "focus harder" plans hurt [S58][S59] |
| **5. Use unseen problems** | Drill the opening on problems you haven't solved. | Most drills | Ask drill mode for a problem you haven't done. The `?` has to work without a remembered answer. | Having seen a question raised hirability in observational data, which means familiarity hides whether the opening itself works [S17] |
| **6. Drill familiar problems for the skipped questions** | On a problem you know, do `?` + shrink deliberately. | 1 drill a week | Pick Two Sum / LRU / merge intervals and score yourself only on Outputs and Bad input. | Familiarity is exactly when people skip the contract and bad-input questions [S45][S46] |
| **7. Read the feedback by category** | After each drill, note which category you missed and which cue would have found it. | Every drill | Drill feedback reads "Missed: Inputs → twist". On the next drill, do that cue first. | Novice elicitors didn't improve across three interviews without targeted feedback [S47] |
| **8. Ask "which label am I on?"** | Say or point to your current label when you pause. | Whenever you stall in practice | The first empty label is where you are. Practise resuming from it after a deliberate interruption. | Explicit stage tracking made novice programmers more independent [S64][S80] |
| **9. Keep sessions short** | 20–40 focused minutes beats a long grind. | Every session | One skeleton retrieval, 2–4 drills, one feedback pass. | Practice volume explains little variance outside games, music and sports, and hours or LeetCode counts didn't predict feeling prepared [S78][S77] |
| **10. Reappraise arousal (optional, low cost)** | When your heart races before or during practice, tell yourself "racing heart = getting ready" instead of "calm down". | Optional; whenever you notice it | One silent line, then type the next label. It never replaces a typed move. | Small studies found better math scores and a healthier stress response, and excitement beat calming down. **But a 2025 direct replication reproduced the felt excitement with no difference in observer-rated performance.** It's cheap, but the performance benefit is unproven [S52][S53][S54][S55] |

## A weekly template

About 2 hours a week, in short sessions. "Drill" means one drill-mode round: you get a problem, run the opening, and receive per-category feedback.

| Day | Session (~20–30 min) |
|---|---|
| Mon | Type the skeleton cold (1 min) → say the if–then plans (1 min) → 3 drills, mixed DSA/OOD, unseen → note your missed categories |
| Tue | Skeleton cold → 2 drills aloud with a timer (≤ 3 min DSA / ≤ 5 min OOD) → for any miss, redo just that cue on the same T |
| Wed | Rest, or 1 familiar-problem drill scored only on Outputs + Bad input (habit 6) |
| Thu | Skeleton cold → 2 drills, starting each with the cue you missed most this week → on one drill, deliberately stop at P and run the fog rule (hand-solve T, type a brute force) |
| Fri | Skeleton cold → 3 drills, interleaved, look-alike patterns |
| Sat | One live mock with a friend or peer platform (observed). Afterwards, tag your own questions by category with the tagging rubric in framework.md. |
| Sun | Off |

Retrieval gains show up at one-week delays, not within the session [S71][S72].

## How to know it's working

- You type the skeleton (three labels plus the A line) cold, without errors, in under 30 s.
- Your first question (from the `?`) comes out within ~45 s of the prompt ending.
- On a timer, the whole opening fits ≤ 3 min DSA / ≤ 5 min OOD with one bundled assumption sentence.
- Across your last 3 drills, no category was missed twice. Misses move around instead of repeating.
- All three move checks are done in every drill: **Restated**, **Example confirmed**, **Assumptions declared** (in drill mode, use the proxies in framework.md's "Move checks").
- Under observation (a mock or a friend), your opening matches what you do alone.
- When you lose the thread, you resume from the first empty label without restarting.
- When you trigger the fog rule, a hand-trace and a typed brute force appear within ~2 min.

If a signal stalls, go back to the matching habit. Repeated misses in one category mean you should do that cue first (habit 7). A slow skeleton means more cold retrieval (habit 1). If the opening falls apart under observation, add more observed reps (habit 3) [S47][S71][S38].
