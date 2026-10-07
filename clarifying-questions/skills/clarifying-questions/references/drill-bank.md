# Drill bank

Underspecified problems for drill mode. Show the learner only **Prompt**. Keep Hidden ambiguities and
Model questions hidden until they have answered. Categories match framework.md exactly.
**Most-missed** is each drill's designed trap — the category a learner is most likely to skip on it.
**Framing:** whiteboard (optional) — present the prompt as a whiteboard round.

## D01 — Pair that hits a target

**Format:** dsa
**Most-missed:** Outputs
**Prompt:** "Find two numbers in a list that add up to a target."
**Hidden ambiguities:**
- [Outputs] Return the two values or their indices? — *changes code because indices need a value→index map; values only need a set.*
- [Outputs] Several valid pairs: any one, the first found, or all of them? None: return `None`, `[]`, or raise? — *changes code because "all pairs" needs a result list and duplicate-pair handling instead of an early return.*
- [Bad input] Fewer than two numbers — *changes code because it needs a guard (or a decision that "no pair" covers it).*
- [Scale & scope] How long can the list be? — *changes code because large n rules out the O(n²) double loop; a hash map gives O(n).*
- [Outputs] May the same element be used twice, e.g. `[3]` with target 6? — *changes code because it decides whether you check the map before inserting the current value (no reuse) or after (reuse, so one `3` pairs with itself).*
- [Inputs] Can values repeat, e.g. `[3, 3]` with target 6? — *changes code because a plain set of seen values can't tell `[3]` from `[3, 3]`, and in a value→index map a repeat overwrites the earlier index, so the lookup has to happen before the insert.*
- [Inputs] Is the list sorted? — *changes code because sorted input allows two pointers with O(1) extra space.*
**Model T line:** `# T: two_sum([2, 7, 11, 15], 9) == ?`
**Model questions:**
- "For this one, should I return the indices `[0, 1]` or the values `[2, 7]`?"
- "If more than one pair works, is any pair fine? And if none works, what should I return?"
- "Can I use one element twice — say `[3]` with target 6?"
- "Can the same number appear twice, like `[3, 3]`?"
- "Roughly how long can the list get?"
**Model assumption sentence:** "I'll assume I return the indices of any one valid pair, `None` if there's none, the list is unsorted and can contain duplicates, and I can't reuse an element — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: two_sum([2, 7, 11, 15], 9) == ?`)

## D02 — Where is the target?

**Format:** dsa
**Most-missed:** Outputs
**Prompt:** "Given a sorted list and a target value, tell me where the target is."
**Hidden ambiguities:**
- [Outputs] With duplicates, which index: first, last, or any? — *changes code because "first" means moving `hi` left on a match instead of returning immediately.*
- [Outputs] Target absent: return `-1`, `None`, or the index where it would be inserted? — *changes code because insertion point means searching the half-open [0, len(nums)) and returning `lo`, not `-1`.*
- [Bad input] Empty list — *changes code because with insertion-point semantics the answer is `0`, so the initial bounds must not assume `nums[0]` exists.*
- [Scale & scope] Is this searched repeatedly on the same list, or is the list huge / streamed? — *changes the design because many queries on the same list justify precomputing a dict of first positions (O(1) per lookup), while a list too big to hold or only streamed can't be indexed for binary search.*
- [Inputs] Sorted ascending or descending? — *changes code because the comparison that moves `lo` or `hi` flips.*
**Model T line:** `# T: find([1, 2, 2, 2, 5], 2) == ?`
**Model questions:**
- "Here 2 is at indices 1, 2 and 3 — which one do you want back?"
- "If the target isn't there, should I return -1 or the position where it would go?"
- "Is it always sorted ascending?"
**Model assumption sentence:** "I'll assume ascending order, I return the first index of the target, and -1 if it's absent — including for an empty list — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: find([1, 2, 2, 2, 5], 2) == ?`)

## D03 — Text to integer

**Format:** dsa
**Most-missed:** Bad input
**Prompt:** "Write a function that turns a string into an integer."
**Hidden ambiguities:**
- [Bad input] Empty string, whitespace only, or a lone sign like `"-"` — *changes code because each needs an explicit check before the digit loop, plus a decision to raise or return a default.*
- [Bad input] Trailing junk, as in `"12abc"`: stop at the first non-digit and return 12, or reject the whole string? — *changes code because "stop" is a `break` and "reject" is a `raise` in the same branch.*
- [Inputs] Leading or trailing spaces, e.g. `"  42 "` — *changes code because it decides whether to `strip()` or treat spaces as invalid.*
- [Outputs] Must the result fit a range, e.g. clamp to 32-bit signed, or is a Python big int fine? — *changes code because clamping needs an overflow check in the loop.*
- [Inputs] Which formats count: a leading `+`, leading zeros, `"0x1A"`, underscores like `"1_000"`? — *changes code because each accepted format is another parsing branch.*
**Model T line:** `# T: to_int("42") == ?`
**Model questions:**
- "If the string is empty or just a minus sign, should I raise or return something?"
- "For `"12abc"`, do I return 12 or treat it as invalid?"
- "Should I allow surrounding spaces?"
- "Do I need to clamp to 32-bit, or can I return any size int?"
- "Is a leading plus sign allowed? Any other bases, like hex?"
**Model assumption sentence:** "I'll assume optional surrounding spaces and one optional sign, base 10 only, I raise `ValueError` on empty or junk input, and no clamping — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: to_int("42") == ?`)

## D04 — Window averages

**Format:** dsa
**Framing:** whiteboard
**Most-missed:** Bad input
**Prompt:** "Given a list of numbers and a window size, return the average of every window."
**Hidden ambiguities:**
- [Bad input] `k == 0` — *changes code because dividing by k raises `ZeroDivisionError`, so it needs a guard: raise or return `[]`.*
- [Bad input] `k > len(nums)`, or an empty list — *changes code because the window never fills, so the function must return `[]` or raise instead of reading past the end.*
- [Outputs] Only full windows (n − k + 1 results), or partial windows at the start too? Float averages, or rounded? — *changes code because partial windows need a different divisor and a different loop start.*
- [Scale & scope] How big are n and k? — *changes code because large k makes re-summing each window O(n·k); a running sum is O(n).*
- [Inputs] A list I can index, or an iterator that can only be read once (a question about the interface, not size)? — *changes code because `nums[i - k]` isn't available on a stream, so a deque must hold the window.*
**Model T line:** `# T: window_avg([1, 3, 2, 6], 2) == ?`
**Model questions:**
- "Here I'd get `[2.0, 2.5, 4.0]` — only full windows, right?"
- "What should happen if k is 0, or bigger than the list?"
- "Roughly how large can k get relative to the list?"
- "Is the input a list I can index, or a stream?"
**Model assumption sentence:** "I'll assume a list, full windows only, float averages, and I raise if k is not in [1, len(nums)] — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: window_avg([1, 3, 2, 6], 2) == ?`)

## D05 — The k smallest

**Format:** dsa
**Most-missed:** Scale & scope
**Prompt:** "Return the k smallest numbers from a big list of numbers."
**Hidden ambiguities:**
- [Scale & scope] How big is "big": does it fit in memory, or must it be streamed in one pass from disk? How does k compare with n? — *changes code because `sorted(nums)[:k]` needs all n in memory; a size-k max-heap streams in O(n log k), and quickselect is O(n) on average if it fits.*
- [Scale & scope] Is it asked once, or repeatedly as new numbers arrive? — *changes the design because repeated queries want a heap that persists between calls.*
- [Outputs] Must the k results come back sorted? — *changes code because heap order is not sorted order, so it needs a final sort.*
- [Bad input] `k <= 0` or `k > n` — *changes code because it needs a guard: return everything, return `[]`, or raise.*
- [Inputs] Can values repeat, e.g. `[1, 1, 2]` with k = 2? — *changes code because "k smallest distinct" needs a set or skip logic; with repeats allowed the answer is `[1, 1]`.*
**Model T line:** `# T: k_smallest([5, 1, 4, 2, 3], 2) == ?`
**Model questions:**
- "When you say big — could it be too big to fit in memory, like a file I stream through?"
- "Is k usually small compared with n?"
- "Should the k numbers come back in sorted order?"
- "If there are repeats, do they count separately?"
**Model assumption sentence:** "I'll assume the numbers are streamed and k is much smaller than n, so I'll keep a max-heap of size k, return the result sorted, count repeats separately, and raise if k is not in [1, n] — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: k_smallest([5, 1, 4, 2, 3], 2) == ?`)

## D06 — Count the islands

**Format:** dsa
**Most-missed:** Scale & scope
**Prompt:** "Given a grid map of land and water, count the islands."
**Hidden ambiguities:**
- [Scale & scope] How large can the grid be, e.g. 5000 × 5000? — *changes code because recursive DFS on one huge island blows past Python's recursion limit (~1000), so it needs an explicit stack or BFS.*
- [Scale & scope] Is memory tight? — *changes code because a separate `visited` set of size rows × cols can be replaced by marking cells in place, if that's allowed.*
- [Outputs] Do diagonal neighbours join an island? — *changes code because the neighbour list is 4 directions vs 8; the T example is 2 islands with 4 directions, 1 with 8.*
- [Outputs] May I modify the grid, or must it be unchanged afterwards? — *changes code because in-place marking sinks the land; otherwise a `visited` set is needed.*
- [Bad input] Empty grid `[]`, `[[]]`, or rows of different lengths — *changes code because `len(grid[0])` raises on `[]`, and ragged rows need per-row bounds checks.*
- [Inputs] Are cells `'1'`/`'0'` strings or `1`/`0` ints? — *changes code because the land test compares against the right type.*
**Model T line:** `# T: count_islands([['1','1','0'], ['0','1','0'], ['0','0','1']]) == ?`
**Model questions:**
- "In this grid, does the bottom-right `'1'` touch the middle one diagonally — one island or two?"
- "How big can the grid get? Could one island cover most of it?"
- "Am I allowed to modify the grid?"
- "Could the grid be empty?"
**Model assumption sentence:** "I'll assume 4-directional, string cells, the grid can be large so I'll use an iterative BFS, I may mark cells in place, and an empty grid returns 0 — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: count_islands([['1','1','0'], ['0','1','0'], ['0','0','1']]) == ?`)

## D07 — Best contiguous run

**Format:** dsa
**Framing:** whiteboard
**Most-missed:** Inputs
**Prompt:** "Find the largest sum you can get from a contiguous run of numbers in a list."
**Hidden ambiguities:**
- [Inputs] Can numbers be negative? (Negate your T example.) — *changes code because with no negatives the answer is just `sum(nums)`; negatives need Kadane's "drop the run when it goes negative" step.*
- [Outputs] Return the sum, or the run itself as [lo, hi] indices? — *changes code because returning [lo, hi] means tracking a start index that resets with the running sum.*
- [Outputs] Is an empty run allowed, so an all-negative list returns 0? — *changes code because it sets `best = 0` vs `best = nums[0]`.*
- [Bad input] Empty list — *changes code because `nums[0]` raises, so it needs a guard: raise, or return 0.*
- [Scale & scope] How long is the list? — *changes the approach because O(n²) over all [lo, hi] pairs is fine for a few thousand but not for millions.*
**Model T line:** `# T: best_run([2, 3, 1]) == ?`
**Model questions:**
- "Can the list contain negative numbers?"
- "If everything is negative, do you want the largest single number, or 0 for an empty run?"
- "Do you want just the sum, or also where the run starts and ends?"
- "How long can the list be?"
**Model assumption sentence:** "I'll assume integers that can be negative, a run must be non-empty, I return just the sum, and an empty list raises — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: best_run([2, 3, 1]) == ?`)

## D08 — Combine two lists

**Format:** dsa
**Most-missed:** Inputs
**Prompt:** "Combine two lists of numbers into a single sorted list."
**Hidden ambiguities:**
- [Inputs] Are the two lists already sorted? (Reorder your T example.) — *changes code because sorted inputs allow an O(n + m) two-pointer merge; unsorted inputs mean concatenate and sort, O((n + m) log(n + m)).*
- [Inputs] If sorted, are both ascending, or could one arrive descending, e.g. `[9, 3, 2]`? — *changes code because a descending list must be walked from its end (or reversed) before the two-pointer merge.*
- [Outputs] Keep duplicates that appear in both lists, or de-duplicate? — *changes code because de-duplication needs an equality branch that advances both pointers.*
- [Outputs] Return a new list, or merge in place into the first list? — *changes code because in-place merging fills from the back to avoid overwriting.*
- [Bad input] One list is `None` rather than `[]`, or a `None` element inside a list — *changes code because a `None` list needs a guard before reading lengths, and a `None` element makes `<` raise `TypeError`, so it must be rejected or skipped.*
- [Scale & scope] Could the lists be too big to hold in memory, e.g. two sorted files read in one pass? — *changes code because the result must be a generator that yields as it goes, not a built list.*
**Model T line:** `# T: combine([1, 4, 7], [2, 3, 9]) == ?`
**Model questions:**
- "Can I count on each input already being sorted, or could I get `[7, 1]`?"
- "If a number is in both lists, should it appear twice?"
- "New list, or should I merge into the first one?"
**Model assumption sentence:** "I'll assume both inputs are already sorted ascending with no `None`s, I keep duplicates, and I return a new list — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: any concrete input with `== ?` (e.g. `# T: combine([1, 4, 7], [2, 3, 9]) == ?`)

## D09 — Least-recently-used cache

**Format:** ood
**Most-missed:** Outputs
**Prompt:** "Design a cache with a fixed capacity that evicts the least recently used item when it's full."
**Hidden ambiguities:**
- [Outputs] `get` on a missing key: return `-1`, `None`, or raise `KeyError`? — *changes code because it picks the sentinel or exception in `get`.*
- [Outputs] `put` on an existing key: update the value and mark it most recent, or only update the value? — *changes code because marking it most recent is a move-to-end in the ordered structure.*
- [Bad input] Capacity 0 or negative — *changes code because capacity 0 means every `put` is a no-op (or raise), and the eviction loop must not pop from an empty structure.*
- [Scale & scope] Must `get`/`put` be O(1)? Roughly how large is the capacity? — *changes the design because O(1) needs a dict + doubly linked list (or `OrderedDict`); a list scanned for the oldest key is O(capacity) per operation, fine for 10 but not for 100,000.*
- [Inputs] Can a stored value be `None`? — *changes code because `None` then can't double as the miss sentinel.*
**Model T line:**
```python
# NOT: thread safety, persistence, TTL
# T: c = LRUCache(2); c.put(1, 'a'); c.put(2, 'b'); c.get(1) -> ?; c.put(3, 'c'); c.get(2) -> ?; c.get(9) -> ?
```
**Model questions:**
- "After I read key 1 and then add key 3, key 2 is the one evicted — so `get(2)` misses. What should a miss return?"
- "If I `put` a key that's already there, does that count as a use?"
- "Can capacity be zero?"
- "Do both operations need to be O(1)? Roughly how big can the capacity get?"
**Model assumption sentence:** "I'll assume a miss returns `None`, values are never `None`, `put` on an existing key updates it and counts as a use, O(1) single-threaded, and capacity of at least 1 — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: thread safety, persistence, TTL` then `# T: c = LRUCache(2); c.put(1, 'a'); c.put(2, 'b'); c.get(1) -> ?; c.put(3, 'c'); c.get(2) -> ?`)

## D10 — Game leaderboard

**Format:** ood
**Most-missed:** Outputs
**Prompt:** "Design a leaderboard for a game: players earn scores, and we can ask for the top players."
**Hidden ambiguities:**
- [Outputs] Ties: how are equal scores ordered in `top`, and is `rank` dense (1, 1, 2) or competition-style (1, 1, 3)? — *changes code because it sets the sort key (score, then name or time) and how rank is counted.*
- [Outputs] Does `add_score` add to a running total, replace the score, or keep the best? — *changes code because each is a different update rule.*
- [Outputs] Does `top(k)` return names, or `(name, score)` pairs? What does `rank` return for a player who has never scored: `None`, or raise? — *changes the method's return type and adds a miss branch in `rank`.*
- [Bad input] `top(k)` with k larger than the number of players, or `k <= 0` — *changes code because each needs a guard: return fewer, `[]`, or raise.*
- [Scale & scope] How many players, and is `top` called far more often than `add_score` (e.g. every frame)? — *changes the design because frequent reads want a structure that stays sorted (e.g. a sorted list); rare reads can use `heapq.nlargest` on demand.*
- [Inputs] Can scores go down (penalties), or be floats? — *changes code because scores that can drop rule out update tricks that assume a player only moves up.*
**Model T line:**
```python
# NOT: persistence, multiple games
# T: lb = Leaderboard(); lb.add_score('ann', 50); lb.add_score('bob', 50); lb.add_score('ann', 20); lb.top(2) -> ?; lb.rank('bob') -> ?
```
**Model questions:**
- "When Ann scores 20 more, is she at 70, or does 20 replace her 50?"
- "If two players tie, who comes first, and do they share a rank?"
- "Should `top` return just names, or names with scores?"
- "How many players are we talking about, and how often is `top` called?"
**Model assumption sentence:** "I'll assume scores accumulate and never go negative, ties break by name and share a competition-style rank, `top` returns `(name, score)` pairs and fewer than k if there aren't enough players — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: persistence, multiple games` then `# T: lb = Leaderboard(); lb.add_score('ann', 50); lb.add_score('bob', 50); lb.add_score('ann', 20); lb.top(2) -> ?; lb.rank('bob') -> ?`)

## D11 — Bank accounts and transfers

**Format:** ood
**Most-missed:** Bad input
**Prompt:** "Design a bank system where customers can open accounts, deposit, withdraw, and transfer money between accounts."
**Hidden ambiguities:**
- [Bad input] A withdrawal or transfer larger than the balance — *changes code because it means raise, return `False`, or allow an overdraft down to a limit, and a failed transfer must leave both balances unchanged (check before debiting).*
- [Bad input] A zero or negative amount, or an unknown account id — *changes code because each needs validation before any balance moves: raise `ValueError`, or return `False`.*
- [Bad input] A transfer from an account to itself — *changes code because it means reject it up front, or let it through as a no-op (and not lock the same account twice).*
- [Outputs] What do `withdraw` and `transfer` return: the new balance, a transaction id, or a bool? — *changes each method's return type.*
- [Outputs] Is there a statement of past transactions, and do failed attempts appear in it? — *changes the design because a statement needs an append-only list of transactions per account, not just a balance.*
- [Scale & scope] Can two transfers touch the same account at the same time? — *changes the design because a transfer must lock both accounts, taken in a fixed order (e.g. by id) to avoid deadlock.*
- [Inputs] Are amounts integer cents, or floats like `10.10`? — *changes code because floats accumulate rounding error (`0.1 + 0.2 != 0.3`), so money is stored as int cents or `Decimal`.*
**Model T line:**
```python
# NOT: interest, multiple currencies, persistence
# T: b = Bank(); a = b.open(100); c = b.open(0); b.withdraw(a, 30) -> ?; b.transfer(a, c, 500) -> ?; b.balance(a) -> ?
```
**Model questions:**
- "Here the transfer of 500 is more than `a` has — should it raise, return `False`, or is there an overdraft?"
- "What about a zero or negative amount, or an account id that doesn't exist?"
- "Can someone transfer to their own account?"
- "What should `withdraw` give back — the new balance?"
- "Can two transfers hit the same account at the same time?"
- "Are amounts whole cents?"
**Model assumption sentence:** "I'll assume integer cents, no overdraft, I raise `ValueError` on a non-positive amount, an unknown account or a self-transfer, and `InsufficientFunds` when the balance is short with both balances left unchanged, `withdraw` returns the new balance, and transfers lock both accounts in id order — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: interest, multiple currencies, persistence` then `# T: b = Bank(); a = b.open(100); c = b.open(0); b.withdraw(a, 30) -> ?; b.transfer(a, c, 500) -> ?`)

## D12 — Dependency-aware task scheduler

**Format:** ood
**Most-missed:** Bad input
**Prompt:** "Design a scheduler where tasks can depend on other tasks, and it tells me an order to run them in."
**Hidden ambiguities:**
- [Bad input] A dependency cycle (`a` needs `b`, `b` needs `a`) — *changes code because Kahn's algorithm must detect "processed fewer than all tasks" and then raise, return `None`, or return a partial order.*
- [Bad input] A dependency on a task that was never added — *changes code because it means raise at `add` (which forbids adding `build` before `fetch`, as the T line does), raise at `order()`, or treat it as already done.*
- [Outputs] `add` with a task name that already exists: overwrite its dependencies, merge them, or raise? — *changes code because each is a different branch in `add`.*
- [Outputs] When several orders are valid, which one: insertion order, alphabetical, any? Or batches that can run in parallel, like `[['fetch', 'lint'], ['build'], ['test']]`? — *changes code because it picks a FIFO queue, a min-heap, or level-by-level output.*
- [Scale & scope] Is `order()` called once after all `add`s, or repeatedly as tasks keep arriving and finishing? — *changes the design because repeated calls want incremental in-degree tracking rather than a rebuild each time.*
- [Inputs] Do tasks carry a priority or duration? — *changes code because ready tasks then come off a heap keyed on priority instead of a plain queue.*
**Model T line:**
```python
# NOT: actually executing tasks, retries
# T: s = Scheduler(); s.add('build', deps=['fetch']); s.add('fetch'); s.add('test', deps=['build']); s.add('lint'); s.order() -> ?
```
**Model questions:**
- "What should happen if the dependencies form a cycle?"
- "Can a task depend on something that hasn't been added yet, or ever?"
- "If two tasks are both ready, does it matter which comes first?"
- "Is this a one-shot ordering, or do tasks keep arriving?"
**Model assumption sentence:** "I'll assume a one-shot `order()` that returns a flat list, ties broken by insertion order, re-adding a name raises, and I raise at `order()` on a cycle or an unknown dependency — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: actually executing tasks, retries` then `# T: s = Scheduler(); s.add('build', deps=['fetch']); s.add('fetch'); s.add('test', deps=['build']); s.add('lint'); s.order() -> ?`)

## D13 — Parking lot

**Format:** ood
**Most-missed:** Scale & scope
**Prompt:** "Design a parking lot."
**Hidden ambiguities:**
- [Scale & scope] What is in scope: payments, reservations, multiple levels, display boards? — *changes the design because each feature adds classes (Ticket, Payment, Level) and methods; scoping out early keeps it to a few.*
- [Scale & scope] Several entry gates assigning spots at the same time? — *changes the design because two gates can hand out the same spot, so allocation needs a lock or an atomic pop from a free-spot structure.*
- [Scale & scope] How many spots? — *changes the design because a linear scan for a free spot is fine for 50 but not 50,000, which wants a free list or heap per spot size.*
- [Outputs] `park` returns a ticket or spot id, or a bool? — *changes the method's return type and what `leave` takes back.*
- [Bad input] `park` when the lot is full (no spot fits): return `None` or raise? `leave` with an unknown or already-used ticket, or the same plate parked twice? — *changes code because each needs a check before allocating or freeing a spot: raise, return `None`, or ignore.*
- [Inputs] What does a vehicle carry: just a plate, or a type (motorcycle, car, truck) with spot-size rules, e.g. can a motorcycle use a car spot? — *changes the design because size rules drive the spot-matching logic.*
**Model T line:**
```python
# NOT: payments, reservations
# T: lot = ParkingLot(levels=1, spots_per_level=2); t = lot.park('car', 'ABC123') -> ?; lot.park('truck', 'XYZ999') -> ?; lot.leave(t) -> ?
```
**Model questions:**
- "Should I leave out payments and reservations and focus on parking and leaving?"
- "Can cars come in through several gates at once?"
- "Roughly how many spots — tens or thousands?"
- "What does `park` give back — a ticket? And if it's full?"
- "Are there vehicle sizes? Can a small vehicle take a bigger spot?"
**Model assumption sentence:** "I'll assume one lot, no payments or reservations, three vehicle sizes where smaller can take larger, `park` returns a ticket or `None` when full, and a lock around allocation for multiple gates — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: payments, reservations` then `# T: lot = ParkingLot(levels=1, spots_per_level=2); t = lot.park('car', 'ABC123') -> ?; lot.leave(t) -> ?`)

## D14 — Key-value store with expiry

**Format:** ood
**Most-missed:** Scale & scope
**Prompt:** "Build an in-memory key-value store where keys can expire."
**Hidden ambiguities:**
- [Scale & scope] How many keys, and are many written once and never read again? — *changes the design because expiring only on read leaks memory for keys nobody reads; it needs an active sweep (e.g. a min-heap of expiry times).*
- [Scale & scope] Will several threads call it? — *changes the design because every operation then needs a lock, or the store has to be sharded.*
- [Outputs] At exactly `now == set_time + ttl`, is the key still alive? — *changes code because alive on [set, set + ttl) is `now < expiry`; on [set, set + ttl] it's `now <= expiry`.*
- [Outputs] `get` on a missing or expired key: `None` or raise? Does `set` on an existing key reset its TTL? — *changes code because it picks the miss branch and whether `set` overwrites the expiry.*
- [Outputs] `ttl=None` (or omitted): does the key never expire? — *changes code because "never expires" needs its own branch: no expiry time, no heap entry.*
- [Bad input] `ttl=0` or a negative TTL — *changes code because it means reject vs store-already-expired.*
- [Inputs] Is TTL in seconds or milliseconds, int or float, and does the caller pass `now` or do I read the clock? — *changes code because it sets the units in the expiry comparison and whether time is injected (testable) or read from `time.monotonic()`.*
**Model T line:**
```python
# NOT: persistence, replication
# T: kv = KVStore(); kv.set('a', 1, ttl=10, now=0); kv.get('a', now=5) -> ?; kv.get('a', now=10) -> ?; kv.get('b', now=0) -> ?
```
**Model questions:**
- "Roughly how many keys? Could lots of them expire without ever being read?"
- "Will this be used from multiple threads?"
- "At exactly t = 10, is `'a'` expired?"
- "What should `get` return for a missing or expired key?"
- "What does a TTL of 0 or `None` mean?"
**Model assumption sentence:** "I'll assume a key is alive on [set, set + ttl), `get` returns `None` when missing or expired, `ttl=None` never expires and `ttl <= 0` raises, TTL in seconds with an injected `now`, single-threaded, plus a heap-based sweep so unread keys don't pile up — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: persistence, replication` then `# T: kv = KVStore(); kv.set('a', 1, ttl=10, now=0); kv.get('a', now=5) -> ?; kv.get('a', now=10) -> ?`)

## D15 — In-memory file system

**Format:** ood
**Most-missed:** Inputs
**Prompt:** "Design an in-memory file system where I can create directories, write files, and read them back by path."
**Hidden ambiguities:**
- [Inputs] Which path forms can arrive: relative paths, trailing slashes (`'/a/b/'`), double slashes, `.` and `..`? (Retype your T path.) — *changes code because anything beyond clean absolute paths needs a normalise step before splitting on `/`.*
- [Inputs] Are names case-sensitive? Which characters are allowed? — *changes code because case-insensitive lookup means lower-casing keys in every directory dict.*
- [Outputs] Does `mkdir('/a/b')` create missing parents (like `mkdir -p`) or fail? Does `write` overwrite or append? — *changes code because each picks a branch in the path walk and the write.*
- [Outputs] What does `ls` return: sorted names? And for a file path, just that file's name? — *changes the return value and adds a file-vs-directory branch.*
- [Outputs] `read` of a missing path: raise `FileNotFoundError`, or return `None`? — *changes code because it picks the miss branch in `read`.*
- [Bad input] `mkdir` where a file already exists, or an empty path `''` — *changes code because each needs a check before the path walk: raise, or ignore.*
- [Scale & scope] Several writers at once? Very deep trees? — *changes the design because concurrent writers need locking, and deep paths favour an iterative walk over recursion.*
**Model T line:**
```python
# NOT: permissions, symlinks
# T: fs = FileSystem(); fs.mkdir('/a/b') -> ?; fs.write('/a/b/c.txt', 'hi') -> ?; fs.read('/a/b/c.txt') -> ?; fs.ls('/a') -> ?
```
**Model questions:**
- "Will paths always be clean and absolute, or could I get things like `'/a/../b/'` or relative paths?"
- "Are names case-sensitive?"
- "If `/a` doesn't exist yet, should `mkdir('/a/b')` create it?"
- "Should `ls` return names sorted?"
- "What should reading a missing file do?"
**Model assumption sentence:** "I'll assume absolute paths with no `.` or `..`, case-sensitive names, `mkdir` creates parents, `write` overwrites, `ls` returns sorted names, and missing paths raise `FileNotFoundError` — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: permissions, symlinks` then `# T: fs = FileSystem(); fs.mkdir('/a/b') -> ?; fs.write('/a/b/c.txt', 'hi') -> ?; fs.read('/a/b/c.txt') -> ?`)

## D16 — Recent hit counter

**Format:** ood
**Most-missed:** Inputs
**Prompt:** "Design a counter that records page hits and can tell me how many hits happened in the last five minutes."
**Hidden ambiguities:**
- [Inputs] Do timestamps arrive in non-decreasing order? (Reorder your T calls.) — *changes the design because in-order hits allow a deque that drops old hits from the left; out-of-order hits need a sorted structure or per-second buckets.*
- [Inputs] Can many hits share one timestamp, and are timestamps whole seconds? — *changes code because repeated seconds suggest storing `(second, count)` pairs instead of one entry per hit.*
- [Outputs] Is the window (t − 300, t] or [t − 300, t]? — *changes code because it's one comparison: `>` vs `>=` when dropping old hits; in the T line, `count(t=301)` includes or excludes the hit at t=1.*
- [Scale & scope] How many hits per second, and from several threads? — *changes the design because heavy traffic favours a fixed ring of 300 per-second buckets (O(1) memory) over a deque of every hit, and threads need a lock.*
- [Bad input] `count(t)` with t earlier than the latest hit (a query back in time) — *changes code because it means raise, or keep the history that a deque would already have dropped.*
**Model T line:**
```python
# NOT: per-page counts, persistence
# T: hc = HitCounter(); hc.hit(t=1); hc.hit(t=2); hc.hit(t=300); hc.count(t=300) -> ?; hc.count(t=301) -> ?
```
**Model questions:**
- "Can I count on timestamps arriving in order, or could I see t=5 after t=7?"
- "Can several hits share the same second?"
- "At t=301, does the hit at t=1 still count — is the 300-second boundary inclusive?"
- "Roughly how many hits per second?"
**Model assumption sentence:** "I'll assume timestamps are whole seconds in non-decreasing order, the window is (t − 300, t], counts never query the past, and traffic is high enough to use 300 per-second buckets — OK?"
**Cold-start drill:** "You've read this and your mind is blank. What's the first thing you type?" → expected: a `# NOT:` line, then `# T:` with a call sequence ending in `-> ?` (e.g. `# NOT: per-page counts, persistence` then `# T: hc = HitCounter(); hc.hit(t=1); hc.hit(t=2); hc.hit(t=300); hc.count(t=300) -> ?; hc.count(t=301) -> ?`)
