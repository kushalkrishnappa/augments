# The TAP framework

## At a glance

**TAP = Test · Ask · Plan.** Three comment labels you type before any code. They stay on the page, so you never have to remember the order [S80][S83].

```python
# T: f(<copied input>) == ?        # copy one input; read it back aloud; work out the ?
# A: shrink? | stretch? | twist?   # change T once per cue; ask only if the answer changes code
# P: <approach>; <cost>            # say it, type it in one line, get a go-ahead
```

**`?` = the expected return value for this one input.** Work it out by hand. The part you can't decide (touching meetings: merge?) is your first question, asked as a guess: "I'd return X, right?"

| What you do | What it finds | Category (drill feedback) |
|---|---|---|
| fill T's `?` | what a correct answer looks like | **Outputs** |
| **shrink**: empty · one · invalid | edge cases and failure behaviour | **Bad input** |
| **stretch**: huge n · [OOD] threads, servers, scope | how fast it must be, concurrency, what's in or out of scope | **Scale & scope** |
| **twist**: duplicate · reorder · negate/retype | properties of valid input | **Inputs** |

- **Budget:** the whole opening takes ≤ 3 min for DSA and ≤ 5 min for OOD [S2][S19][S21].
- **Close A with one sentence:** "I'll assume …, OK?" [S27].
- **Fog rule:** "If no approach — or the template won't come back — ~60 s after A, then hand-solve T as comments and type a brute force." Lost your place? The first empty label is where you are [S58][S80].

Never say "TAP" or any framework name aloud. Just do the moves [S33].

## Opening sequence

| # | Element | What you do / say / type | Time |
|---|---|---|---|
| 0 | **Start cue** | When the interviewer stops talking, type `# T:` `# A:` `# P:` on three lines (on a whiteboard, the same three labels in a corner box). Say: "Let me jot down what I'm hearing." This step needs no ideas, and it puts the order on the page [S36][S62][S80]. | ~5 s |
| 1 | **T — Test** | **DSA:** `# T: f(<copied input>) == ?`. Copy the given example. If there isn't one, make a three-item example from the prompt's nouns. **OOD:** write a `# NOT: <out of scope>` line, then the constructor and one call sequence with `?` outputs: `# T: c = Cls(args); c.op(x) -> ?`. Read T back aloud; that is your restatement. Whatever you can't fill in confidently at the `?` is your first question (Outputs) [S6][S19][S20][S63]. | 30–45 s |
| 2 | **A — Ask** | Type `# A: shrink? \| stretch? \| twist?` and change T once per cue: **shrink** (empty · one · invalid), **stretch** (huge n or a tight target; [OOD] threads, servers, scope), **twist** (duplicate · reorder · negate/retype). Ask only the questions whose answers change your code. Close with one "I'll assume …, OK?" [S3][S24][S27][S65]. | ≤ 60–90 s (OOD ≤ 3 min) |
| 3 | **P — Plan** | Say the approach and its cost, type it as a one-line `# P:`, and get a go-ahead. Brute force: mention it in one sentence, or skip it if the optimal approach is already clear. If you're torn between approaches, offer a choice for the interviewer to confirm ("I'm leaning toward sorting first. OK?"). Never ask "what's the target complexity?" If you have no approach ~60 s after A, use the fog rule [S3][S6][S34][S58]. | ~45 s |

**Why ≤ 3–5 min:** published advice ranges from 2 to 10 minutes, and Meta's 35-minute two-problem block can't afford more [S2][S21][S24].

## Question categories

Each category is one cue acting on T. Drill feedback names the category you missed, so a miss always points back to the one typed move you skipped. The names are for feedback only. In the room you just do the moves [S79][S71].

### Bad input

**Cue: shrink** (make T empty · one item · invalid).

- **Definition:** what the code does when the input is empty, minimal, invalid, when the current state rules the request out (overdraw, a full structure), or when something it depends on fails.
- **What it uncovers:** early returns, validation, raising vs returning a sentinel, capacity ≥ 1, the default when config is missing, and fail-open vs fail-closed.
- **DSA example:** `merge([])`: return `[]` or raise? `merge([[4,2]])`: can a meeting end before it starts?
- **OOD example:** `LRUCache(capacity=0)` or `RateLimiter(limit=0)`: reject the config, or deny everything? If the shared counter store is down, does the limiter allow (fail-open) or deny (fail-closed)?
- **Natural phrasings:**
  - "If it's empty, should I return `[]` or raise?"
  - "Can I assume start ≤ end, or should I validate?"
  - "If capacity is 0, should the constructor raise?"
- **Evidence:** Google's guide models "What happens with bad inputs?". Amazon recruiters say to make sure no bad input slips through. OOD frameworks list error handling as a core requirement theme [S3][S4][S14][S19][S20][S22].

### Scale & scope

**Cue: stretch** (make T huge · [OOD] add threads, servers, more scope).

- **Definition:** how much input there is, how often the code runs, how much concurrency it sees, and where the edge of what you're building lies.
- **What it uncovers:** the complexity budget (n ~ 10⁶ rules out O(n²)), memory limits, whether to preprocess for repeated calls, locks, shared state across servers, and what's in or out of scope.
- **DSA example:** 10 million meetings means an O(n²) pairwise merge is out. Called once, or many times on the same data (worth preprocessing)?
- **OOD example:** two threads call `allow("u1")` at the same moment, so you need a lock. Three servers behind a load balancer need a shared store. Is persistence in scope?
- **Natural phrasings:**
  - "Roughly how big can n get?"
  - "Will this run once, or many times on the same data?"
  - "Is this one process, or shared across servers? I've put multiple servers out of scope. OK?"
- **Evidence:** "How big could the input be?" and "How often will we run this?" are two of Google's three model clarifications. OOD sources add concurrency and quantified requirements ("100 ms" beats "fast") [S3][S5][S14][S21][S22][S23].

### Outputs

**Cue: T's `?`** (fill it in; whatever you can't fill confidently is the question).

- **Definition:** what a correct answer looks like for valid input: the return shape, the value when there's no answer, order and ties, boundary (touching) behaviour, in-place vs new, and what each operation returns.
- **What it uncovers:** `<` vs `<=`, the none-case return value, the sort order of the result, indices vs values, one answer vs all of them, and the result of each method call.
- **DSA example:** `merge([[1,3],[2,6],[8,10]]) == ?` forces the rule for touching meetings: do `[1,3]` and `[3,5]` merge? Two Sum: return indices or values? What if no pair works?
- **OOD example:** `cache.get(k)` on a miss returns `None`, `-1`, or raises? Does `put` on an existing key count as a use? Does `allow` return a bool, or also a retry-after?
- **Natural phrasings:**
  - "For this input I'd return `[[1,6],[8,10]]`. Right?"
  - "If no pair works, should I return `[]` or `None`?"
  - "Should I return the indices or the values?"
- **Evidence:** contract questions map directly to code branches. CtCI's "Example" step is the same move: work an example and confirm the expected output [S6][S20][S23][S24][S30].

### Inputs

**Cue: twist** (duplicate · reorder · negate/retype an item in T).

- **Definition:** the properties of valid input: type, range, sign, order, duplicates, and what a request carries.
- **What it uncovers:** whether you must sort first (or can use two pointers / binary search), set vs counter, whether negatives break a positive-only sliding window, int vs float vs str, and the key a limiter or cache uses.
- **DSA example:** reorder T to `[[8,10],[1,3],[2,6]]`: is the input sorted by start? Duplicate an item: can values repeat? Negate one: can the numbers be negative?
- **OOD example:** is the limit per user ID, per IP, or per (user, endpoint)? Are cache keys always hashable strings?
- **Natural phrasings:**
  - "Is the input sorted by start time?"
  - "Can values repeat?"
  - "Is the limit per user, or per user and endpoint?"
- **Evidence:** each input property selects an algorithm (sorted → two pointers or binary search; n → complexity budget). OOD guides ask "what does a request carry?" [S3][S5][S14][S20][S24][S30].

## Tagging rubric

**First match wins.** Walk the categories in this order and stop at the first one that fits:

1. **Bad input**: is the question about empty, minimal, invalid or out-of-range input, a request the current state rules out, or a failing dependency?
2. **Scale & scope**: is it about how much, how often, how concurrent, or what's in or out of scope?
3. **Outputs**: is it about what to return, or what an operation does, for valid input?
4. **Inputs**: is it about the type, range, order or duplicates of valid input?

**Done-test (ask before every question):** *would the answer change a line of code or a design decision?* If not, don't ask it. Fold it into the "I'll assume …, OK?" sentence or drop it [S24][S3]. The rubric scores observable narrowing and reasonable assumptions, never a count of questions [S10][S12].

| Question | Category | Why |
|---|---|---|
| "If two meetings just touch, like `[1,3]` and `[3,5]`, do they merge?" | Outputs | Both inputs are valid. The question is what the result looks like at the boundary (`<` vs `<=`), which you find by filling the `?`. |
| "Can I modify the input list in place, or should I return a new one?" | Outputs | It decides what the function delivers (mutated input vs new list). That's the contract, not a property of the input. |
| "Can the numbers be negative?" | Inputs | Negatives are valid values; you find this one with twist (negate). |
| "What if `capacity` is negative?" | Bad input | The value is invalid for the API, so it's a shrink question. Bad input comes first in the order. |
| "What if `k > n`?" | Bad input | `k` is out of range for this input. The question also asks "return what?", but Bad input matches first. |
| "Does `put` on an existing key count as a use?" | Outputs | It asks for the result of one operation on a valid call. |
| A request the problem's precondition or current state rules out: overdraw, a cycle in a dependency order, inserting into a full structure with no eviction rule given | Bad input | Shrink the state to empty/full. The call is well-formed, but the state can't satisfy it, so it's failure behaviour. |
| No pair sums to target / key not found on `get` | Outputs | A normal result of a valid call: a lookup that finds nothing is the none-case of the contract. Contrast "What if it's empty?" and an overdraw, which are Bad input. |
| "Will multiple threads call this at once?" | Scale & scope | It's about concurrency; you find this one with stretch [OOD]. |
| "Is the input sorted?" | Inputs | It's a property of valid input; you find this one with twist (reorder). |
| "Can there be duplicates?" | Inputs | It's a property of valid input; you find this one with twist (duplicate). |
| "Should I return indices or values?" | Outputs | It's about the return shape. |
| "If it's empty, return `[]`?" | Bad input | The input is degenerate, so Bad input matches first, even though the question names a return value. |
| "If the counter store is down, allow or deny?" (fail-open vs fail-closed) | Bad input | A dependency failing is failure behaviour, so it's a shrink question. |
| "Is persistence in scope?" | Scale & scope | It's about the boundary of what you're building. |
| "Can I assume it fits in memory?" | Scale & scope | It's about how much input there is, not what it looks like. |
| "Are times integers or floats?" | Inputs | It's the type of valid input; you find this one with twist (retype). |
| "Is format X accepted (surrounding spaces, a leading `+`, hex)?" | Inputs | It defines what valid input looks like; you find this one with twist (retype). |
| "What about malformed input, like `"12abc"`?" | Bad input | The input is invalid under any accepted format, so it's a shrink question. |
| "Is the input a list or a stream?" | Scale & scope, or Inputs | Scale & scope when it's about memory or one pass (n too big to hold, so you must stream). Inputs when it's only about the type or interface (can I index it, or only iterate?). |

## Move checks

Three moves are scored **done / not done**, separately from the categories. They are moves you make, not questions you ask [S6][S27][S30].

| Move check | Done when | Artefact |
|---|---|---|
| **Restated** | You read T back aloud in your own words ("So I get a list of `[start, end]` pairs, like these three, and I return… what?") | The `# T:` line |
| **Example confirmed** | The `?` is replaced with a value the interviewer agreed to | `== [[1,6],[8,10]]` on the T line |
| **Assumptions declared** | You said one bundled "I'll assume …, OK?" sentence and typed the answers after `# A:` | The `# A: … → …` tail |

**In drill mode (no interviewer):** **Restated** = a T line with a concrete input. **Example confirmed** = you proposed a guess for the `?` ("I'd return X, right?") or flagged the part you can't decide. **Assumptions declared** = you wrote the one bundled "I'll assume …, OK?" sentence.

## Enough questions — move on

Stop asking when the next question fails the **done-test**: *would the answer change a line of code or a design decision?* Then close with **one bundled sentence** that turns everything left over into stated assumptions:

> "I'll assume it's unsorted, integer times, and I return a new list sorted by start. OK?"

- **Budget:** whole opening ≤ 3 min DSA, ≤ 5 min OOD. Asking a twelfth question six minutes in reads as stalling [S21][S24][S27].
- **There is no question quota.** "Ask 2–3 questions" is folklore, and question-count rubric items leak bias [S10][S14][S24].
- **If the interviewer won't answer** ("your call", or a grunt): state the assumption, type it after `# A:`, and move on. Some interviewers brush questions off, and declaring assumptions is the shared fix [S27][S40][S15].
- **Clarifying is welcome everywhere, but solving decides outcomes.** Protect your solving time [S16][S8].

## Worked example: DSA

Copying an input takes zero insight, and working out the `?` by hand forces you to pin down what the problem actually wants [S6][S62].

**Problem.** The interviewer says: "Given a list of meeting times, merge the ones that overlap." No example, no constraints.

### 0. Start cue (~5 s)

Say "Let me jot down what I'm hearing." Then type:

```python
# T:
# A:
# P:
```

### 1. T (30–45 s)

Copy the given example, or make a tiny one from the prompt's nouns. "Meetings" means `[start, end]` pairs, so pick three:

```python
# T: merge([[1,3],[2,6],[8,10]]) == ?
```

Read it back aloud. That's your restatement: "So I get a list of `[start, end]` pairs, like these three, and I return… what?"

**Fill the `?`.** `[1,3]` and `[2,6]`: 2 comes before 3 ends, so they overlap and become `[1,6]`. `[8,10]`: 8 is after 6, so it stays separate. The answer is `[[1,6],[8,10]]`.

Filling it forced a rule on you: is it an overlap when `next.start <= last.end`, or only when `<`? That's a real question, because it changes one character of the code.

> **You:** "For this I'd return `[[1,6],[8,10]]`. And if two meetings just touch, like `[1,3]` and `[3,5]`, should they merge?"
> **Interviewer:** "Yes, merge them."

```python
# T: merge([[1,3],[2,6],[8,10]]) == [[1,6],[8,10]]   # touching merges (<=)
```

This question is in the **Outputs** category, and it came from the `?`.

### 2. A (60–90 s)

Type `# A: shrink? | stretch? | twist?` and change T once per cue:

| Cue | Change T to… | Changes my code? | Say |
|---|---|---|---|
| shrink | `merge([])` | yes (early return) | "Empty list: return `[]`?" |
| shrink | `merge([[5,7]])` | no | (skip) |
| shrink | `merge([[4,2]])`, start > end | yes (validation) | "Can a meeting end before it starts?" |
| stretch | 10 million meetings | yes (rules out O(n²)) | "Roughly how many meetings?" |
| twist (duplicate) | `[[1,3],[1,3]]` | no | (skip) |
| twist (reorder) | `[[8,10],[1,3],[2,6]]` | **YES**: must sort first | "Is the input sorted by start time?" |
| twist (negate/retype) | `[[-2,1.5]]` | no | (fold into the assumption sentence) |

The interviewer's answers: empty returns `[]`. Assume start ≤ end. Up to about a million meetings. Not necessarily sorted.

Ask only the questions whose answers change your code. Then close with **one** sentence:

> **You:** "I'll assume it's unsorted, integer times, and I return a new list sorted by start. OK?"
> **Interviewer:** "Sounds good."

### 3. P (~45 s)

> **You:** "Sort by start, then sweep: if the next meeting starts at or before the last one ends, extend it; otherwise start a new one. O(n log n) for the sort."

```python
# P: sort by start; sweep: if cur.start <= last.end → extend last, else append. O(n log n)
```

> **Interviewer:** "Go ahead." (~2.5 min total)

### Final editor state

```python
# T: merge([[1,3],[2,6],[8,10]]) == [[1,6],[8,10]]   # touching merges (<=)
# A: shrink? | stretch? | twist?   → empty→[], start≤end, n~1e6, unsorted
# P: sort by start; sweep: if cur.start <= last.end → extend last, else append. O(n log n)
```

The T line is already your first test case.

### Fog-rule variant

Suppose you're blank at P, ~60 s after A. Hand-solve T as comments: write down what you did in your head while filling the `?` [S6][S66].

```python
# [1,3]          → start: [1,3]
# [2,6]: 2 <= 3  → merge → [1,6]
# [8,10]: 8 > 6  → new   → [1,6], [8,10]
```

Often the algorithm is now visible: walk in order and compare each meeting with the last merged one. Either way, type a brute force next (repeatedly merge any overlapping pair until nothing changes, worst case O(n³)). It's the safe artefact on the page, and you improve from it, using whatever the hand-solve revealed. Lost your place? The first empty or unfinished label is where you are [S58][S80].

### Scoring

- Outputs (`?`) ✅ touching
- Bad input (shrink) ✅ empty, start > end
- Scale & scope (stretch) ✅ how many
- Inputs (twist) ✅ sorted?
- Move checks: Restated ✅ · Example confirmed ✅ · Assumptions declared ✅

If "sorted?" had been missed, the feedback would be: *"Missed: Inputs. That's what twist finds. Reorder your T example and ask whether order is guaranteed."*

## Worked example: OOD

**Problem.** The interviewer says: "Design a rate limiter that allows each user at most N requests per time window."

Same three labels, same order. Only T's form changes: a `# NOT:` scope line, then the constructor and one call sequence with `?` outputs, written before you assume any behaviour [S19][S20][S21].

### 0. Start cue (~5 s)

Say "Let me jot down what I'm hearing." Type `# T:` `# A:` `# P:`.

### 1. T (~60 s)

Write a draft of what's out of scope from the prompt's nouns, then pick small numbers and copy a call sequence:

```python
# NOT: multiple servers, HTTP layer, persistence, config reload
# T: rl = RateLimiter(limit=2, window_s=1)
#    rl.allow("u1")   # t=0.0 -> ?
#    rl.allow("u1")   # t=0.1 -> ?
#    rl.allow("u1")   # t=0.5 -> ?
#    rl.allow("u1")   # t=1.0 -> ?
```

Read it back: "So each user gets two calls per second. Here's one user calling four times, and `allow` tells me yes or no… right?"

**Fill the `?`s.** t=0.0 is the first call: `True`. t=0.1 is the second call within 1 s: `True`. t=0.5 would be a third call within 1 s: `False`. t=1.0: **can't fill confidently.** If the window is the last second as the interval [t−1, t], the t=0.0 call still counts and the answer is `False`. If it's (t−1, t], that call has expired and the answer is `True`. Fixed clock buckets ([0, 1), [1, 2)) would give `True` for a different reason. Each option is different code.

> **You:** "For these I'd return True, True, False. At t=1.0, is the window a rolling last second, and does a call exactly one second old still count?"
> **Interviewer:** "Rolling. A call exactly one second old has expired."

```python
#    rl.allow("u1")   # t=1.0 -> True   # window is (t-1, t]: 1 s old = expired
```

This is an **Outputs** question from the `?`. It's the same touching-boundary question as merge-meetings, but on a time window.

### 2. A (≤ 3 min)

Type `# A: shrink? | stretch? | twist?` and change T once per cue:

| Cue | Change T to… | Changes my code? | Say |
|---|---|---|---|
| shrink | `RateLimiter(limit=0, window_s=1)` | yes (validate or always deny) | "Can limit be 0? Should the constructor reject it?" |
| shrink | `rl.allow("")`, empty user ID | no | (fold into the assumption sentence) |
| stretch | 1 million distinct users | yes (may need to evict idle users) | "Roughly how many active users?" |
| stretch [OOD] threads | two threads call `allow("u1")` at t=0.1 | yes (lock) | "Will `allow` be called from multiple threads?" |
| stretch [OOD] servers / scope | 3 servers behind a load balancer | yes (shared store) | "I've put multiple servers out of scope. OK?" |
| twist (duplicate) | two calls at the same t=0.1 | no (each counts) | (skip) |
| twist (reorder) | caller passes t=0.5 before t=0.1 | yes, only if callers pass timestamps | (fold into the assumption sentence: I read a monotonic clock) |
| twist (negate/retype) | key `("u1", "/search")` instead of `"u1"` | yes (key shape) | "Is the limit per user, or per user and endpoint?" |

The interviewer's answers: reject limit < 1. About 100k active users, so memory is fine. Yes, multiple threads. One server is fine. Per user only.

> **You:** "I'll assume one process, thread-safe, limit ≥ 1 checked in the constructor, keyed by user ID, denied calls don't count against the limit, and `allow` returns a bool. OK?"
> **Interviewer:** "Good."

### 3. P (~45 s)

> **You:** "Per user, keep a deque of allowed timestamps. On each call, pop from the left while the oldest is at or before now minus the window, then allow if fewer than `limit` remain and append now. One lock around it. O(1) amortised per call, O(users × limit) memory."

```python
# P: dict user→deque of allowed ts; pop while ts <= now - w; allow iff len < limit (append now); lock. O(1) amortised
```

### Final editor state

```python
# NOT: multiple servers, HTTP layer, persistence, config reload
# T: rl = RateLimiter(limit=2, window_s=1)
#    rl.allow("u1")   # t=0.0 -> True
#    rl.allow("u1")   # t=0.1 -> True
#    rl.allow("u1")   # t=0.5 -> False
#    rl.allow("u1")   # t=1.0 -> True    # window is (t-1, t]: 1 s old = expired
# A: shrink? | stretch? | twist?   → limit>=1 (raise), ~100k users, threads→lock, 1 process, key=user_id, bool
# P: dict user→deque of allowed ts; pop while ts <= now - w; allow iff len < limit (append now); lock. O(1) amortised
```

The T block is already your first unit test (inject the clock so the test can set t).

### Scoring

- Outputs (`?`) ✅ window edge
- Bad input (shrink) ✅ limit 0
- Scale & scope (stretch) ✅ users, threads, servers
- Inputs (twist) ✅ key per user vs endpoint
- Move checks: Restated ✅ · Example confirmed ✅ · Assumptions declared ✅

If "per user or per endpoint?" had been missed, the feedback would be: *"Missed: Inputs. That's what twist finds. Retype the key in T and ask what a request carries."*

## Whiteboard variant

Meta evaluates in-person whiteboard rounds the same way as the shared editor [S2]. Draw a small box in the top corner before anything else, and keep the rest of the board for the code [S29]:

```
+--------------------------------------------------------+
| T: merge([[1,3],[2,6],[8,10]]) = [[1,6],[8,10]]  (<=)  |
| A: shrink? stretch? twist?  → []→[], s≤e, n~1e6, unsorted |
| P: sort by start; sweep, extend if s<=last.e. O(n log n) |
+--------------------------------------------------------+
```

The box does the same job as the comment lines: it carries the order and shows where you are. For OOD, put the `NOT:` line above T inside the same box.

## Why it is built this way

- **Three typed labels.** Working memory holds about four chunks and anxiety shrinks it, so three labels leave margin, and typing them carries the order and the resume point; external checklists cut missed steps under stress from 23% to 6% [S79][S48][S80][S81].
- **A zero-insight start.** Copying an input with `== ?` needs no idea of the solution, the same first move as CtCI's "use an example" [S6][S63].
- **One cue per category.** A short explicit heuristic list beats practice without one, and novices only improve with targeted feedback, so every miss names one typed move [S65][S47].
- **The done-test and a time box, not a count.** Clarifying is scored as observable narrowing plus reasonable assumptions; past ~60–90 s a checklist becomes a distraction [S24][S10][S36].
- **Run it silently.** Reciting a script sounds scripted [S33].
