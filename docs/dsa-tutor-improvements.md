# dsa-tutor v0.1.0 — adversarial review & improvements table

Produced by five parallel adversarial reviewers (write-safety, instruction ambiguity, bloat,
cross-file consistency, and a design debate) run against the **published** v0.1.0 skill. v0.1.0
ships and passes its eval suite (3/3 @ 1.00); the items below are follow-up hardening for v0.1.x.
Severity reflects the worst realistic outcome (silent data loss / wrong-directory writes rank
highest). "Src" = which reviewer(s) raised it.

## A. Filesystem safety

| # | Sev | Issue | Recommendation | Src |
|---|-----|-------|----------------|-----|
| A1 | **High** | `~/dsa-tutor/` is used as a literal string; if `$HOME` is unset or the Write tool doesn't expand `~`, a literal `~/dsa-tutor/` dir is created **in the cwd (the work repo)** — the safety guard silently causes the pollution it exists to prevent. | Resolve `~` to an absolute path via `$HOME` before any write; abort with a message if `$HOME` is empty; echo the fully-expanded absolute path in the confirmation prompt. | write-safety |
| A2 | **High** | The write-safety guard fires only on the *default fall-through*. A learner-named path (rule 1) — or naming the default itself — gets writes with **no confirmation and no check** of what the dir is. Guard is keyed to *how* the path resolved, not *what* it is. | Base the guard on "does this `.dsa-tutor/` marker already exist on disk?" Confirm before the first write whenever the marker is absent, regardless of resolution rule. Warn if the root contains `.git`/`package.json`. | write-safety |
| A3 | **High** | Branch-ordering hole: New-Area Setup writes a roadmap, and dual-coding generates visuals eagerly, either of which can be the **first write on an unconfirmed path**, defeating the guard. | Make the write-safety confirmation an explicit precondition for **all** writes (profile, progress, roadmap, visual); First-Session Setup must strictly precede New-Area Setup. | write-safety |
| A4 | Med | Ancestor scan (rule 2): once a workspace is ever created inside a project tree, every future launch anywhere under that tree resolves it as "established" and writes freely into the work repo — permanent, silent. | Warn/confirm when the resolved established root (or an ancestor) also contains project markers; consider not treating a marker under a VCS repo root as established. | write-safety |
| A5 | Med | "Stated" ≠ "confirmed": an inferred (rule 2) not-yet-existing folder is written after a one-line announcement; a wrong inference creates a stray `wrong-area/.dsa-tutor/`. | Require the same one-line confirmation for inferred, not-yet-existing folders; only pre-existing folders are write-on-state. | write-safety |
| A6 | Low | Cross-topic "quiz me across everything" globs `*/.dsa-tutor/progress.json`; if the root is mis-resolved (home/VCS root) it reads unrelated dirs and skips the exclusion list. | Constrain the glob to folders listed in `profile.json`'s `areas`; never run it at a home/VCS root. | write-safety |

## B. Data durability

| # | Sev | Issue | Recommendation | Src |
|---|-----|-------|----------------|-----|
| B1 | **High** | Every checkpoint overwrites the whole file, regenerated from the model's memory. Any omitted concept / `past_mistakes` / `calibration` counter is **permanently and invisibly lost** — and `past_mistakes` is "keep forever." | Re-read the file immediately before each write and **append+update, carrying every prior entry forward verbatim** — never rebuild from memory. Write a `.bak` (or temp-then-rename) before overwriting. | write-safety, design |
| B2 | **High** | No concurrency model. Two sessions (tabs / machines on synced storage) last-write-wins. `profile.json` is worst: its `areas` array is rewritten wholesale, so two same-day sessions clobber each other's roll-up and streak. | Read-modify-write `profile.json`, merging only the one changed `areas` entry; document the single-session assumption and/or add a lock file. | write-safety, design |
| B3 | Med | A crash / token-limit mid-write leaves truncated invalid JSON → the area's entire history becomes unreadable next session. No corruption recovery. | Write to a temp file and atomically rename; on read, if JSON parse fails, fall back to `.bak` and warn instead of proceeding. | write-safety, design |
| B4 | Med | The 0→1 legacy migration deletes the monolith after non-atomic per-area splits; an interrupted/mis-split migration destroys the only complete copy. | Rename the monolith to `.bak` (don't delete); verify all split files parse before removal; make it idempotent. | write-safety |
| B5 | Low | `schema_version` handling assumes a well-formed integer; a malformed value (string/negative/float) is unspecified. | Treat a non-integer/negative `schema_version` as corrupt → do not write, fall back to `.bak`, warn. | write-safety |

## C. Instruction ambiguity (behavioral determinism)

| # | Sev | Issue | Recommendation | Src |
|---|-----|-------|----------------|-----|
| C1 | **High** | The pre-test is step 4 of "Session opening" and step 1 of "the teaching cycle," both "before teaching" — unclear whether a second topic in one session gets a second pre-test. | State that cycle step 1 = opening step 4 for the first topic; each new topic gets its own fresh pre-test; warm-up/Band A/Band B run once per session. | ambiguity |
| C2 | **High** | Band A/Band B are hard-coded as "~7 days ago"/"~30 days ago" but the interval ladder is 1/3/7/16/30 — concepts due at 3 or 16 fit neither band and can be silently dropped from the opening. | Redefine bands by the due-check (`today - last_reviewed >= review_interval`) so all ladder values are covered; drop the "~7/~30 days ago" framing. | ambiguity |
| C3 | **High** | Spaced-rep ladder edge cases undefined: cap at 30? Correct+Unsure already at 16 (next rung 30 is forbidden)? "reset to 1" / initial interval for a brand-new concept? | Specify: initial interval 1; Correct+Confident caps at 30 (stay); Correct+Unsure-at-16 stays at 16; "reset to 1" = set to 1 when no prior interval. | ambiguity |
| C4 | Med | "today" / due-date math never defines timezone; two agents (or one near midnight) classify the same concept differently. | Define "today" as the local calendar date; compare in whole days; state it in SKILL.md, not just the schema ref. | ambiguity |
| C5 | Med | `mastery` is raised "when earned" / lowered, but the scale and the "earned" criterion aren't in SKILL.md. | Define the scale (`learning`/`shaky`/`solid`, per schema) and the explicit "earned" condition, or point to a precise rule and say so. | ambiguity |
| C6 | Med | "capped at roughly 10 minutes" is not observable/enforceable by an LLM. | Convert to a countable budget (e.g. "no more than N retrieval questions before new material"). | ambiguity |
| C7 | Med | "One question per turn, always" appears to conflict with warm-up "1–2 questions", pre-test "2–3", quiz "3–5". | State globally that all question counts mean sequential, one-per-turn prompts; repeat "one at a time" in warm-up/pre-test. | ambiguity |
| C8 | Med | Several `record X` verbs name a field but not the file/JSON shape inline (mnemonic, taught_back, calibration, practice_done). | Add one mapping table (recorded item → field + file); defer shape to `progress_schema.md` consistently. | ambiguity |
| C9 | Med | New-Area Setup / First-Session Setup / folder-creation (detection rule 3) overlap on a cold start; the confirmation sequence isn't specified as one flow. | Give one ordered cold-start checklist merging the branches (confirm workspace → confirm folder → explain how you work → roadmap → pre-test). | ambiguity |
| C10 | Low | "Interleave" is impossible on day-1 (single concept) and unreconciled with the linear per-topic teach loop. | Scope interleaving to review/quiz retrieval where multiple concepts exist; exempt single-concept sessions. | ambiguity |
| C11 | Low | "Feed it back periodically" (calibration) has no trigger; "last session's material" is ambiguous on multi-topic prior sessions. | Pick a concrete trigger (e.g. ≥2 high-confidence errors); define "last session's material" via the previous `last_session_date`. | ambiguity |

## D. Consistency & external dependencies

| # | Sev | Issue | Recommendation | Src |
|---|-----|-------|----------------|-----|
| D1 | Med | SKILL.md's "Wrong/Confident" spaced-rep row omits "lower `mastery`" that `progress_schema.md` includes — the two tables disagree behaviorally. | Add "lower `mastery`" to the SKILL.md row to match the schema. | consistency |
| D2 | Med | Verify-before-cite is applied only to dsapanicle.com; LeetCode/HackerRank problem IDs (the most hallucination-prone) are unguarded, and the schema even seeds `LC-207`/`LC-200`. | Caution that LC/HR IDs must be given only when confident; prefer problem **title + pattern** over a bare `LC-###`. | consistency, design |
| D3 | Med | dsapanicle.com is a single third-party domain promoted co-equal with LC/HR; "verify reachable" assumes a network capability the skill doesn't declare. | Downgrade dsapanicle.com to explicitly optional; state the no-network fallback (LC/HR only) explicitly. | consistency |
| D4 | Med | Default `~/dsa-tutor/` is machine-local & un-versioned, contradicting the marketplace's "usable on any machine" value prop. | Add a one-line note that the learner can place the workspace in a git-synced/cloud dir (named via resolution rule 1) so progress survives machine changes. | design |
| D5 | Low | `visual_authoring.md` slug example is self-contradictory (`breadth-first-search` → `bfs.html`). | Fix to a true slug, or state that common acronyms are allowed, and keep it consistent across files. | consistency |
| D6 | Low | `marketplace.json` duplicates version/description/author from `plugin.json` → drift hazard on release. | Keep the marketplace entry minimal (name + source) or document "bump both together." | consistency |

## E. Bloat / token cost (SKILL.md is always-loaded)

Estimated ~30–40 lines / ~350–500 tokens recoverable, ~20–25 lines from the always-loaded file.

| # | Sev | Issue | Recommendation | Src |
|---|-----|-------|----------------|-----|
| E1 | High-value | Spaced-rep table is reproduced almost verbatim in SKILL.md and `progress_schema.md`. | Keep a 2-line behavioral summary in SKILL.md; move the mechanical table to the schema reference only. (~12 lines) | bloat |
| E2 | High-value | Frontmatter `description` is ~150 words; its second half (architecture) and capability list add no trigger signal and are re-explained in the body. | Trim to topics + trigger phrases + one purpose clause. (~70–90 words of recurring cost) | bloat |
| E3 | Med | Workspace/topic-folder definitions duplicated in SKILL.md and the schema. | Collapse the schema copy to a pointer ("resolution lives in SKILL.md"). | bloat |
| E4 | Med | Dual-coding "handed over at the end does nothing" appears twice; the visual path is stated 2–3×. | Keep the behavioral instruction once; drop the repeated moral and extra path mention. | bloat |
| E5 | Low | Write-safety closing rationale, second praise-process example, 5 interrogation stems, quiz-scope exception, roadmap colour recap — all mild repeats. | Trim each to one statement. | bloat |

> **Note on interaction:** several C-fixes add lines while E-cuts remove them — apply the correctness
> fixes (A/B/C/D) first, then do the E cleanup so nothing needed is lost.

## Design debate verdicts (summary)

- **State location** (`~/dsa-tutor/` + confirm vs beside-the-code): **keep the workspace model** for a
  globally-installed plugin; add D4 so portability isn't silently lost.
- **Whole-file JSON persistence**: risk is real (B1/B2/B3) but a full DB is over-engineering for
  v0.1.x — mitigate with `.bak` + read-merge + atomic rename, not a rewrite of the storage model.
- **Model-computed spaced repetition**: fragile; store a precomputed `next_due` ISO date per concept
  at write time so "due" is a string comparison, and collapse the redundant two-band clock onto it.
