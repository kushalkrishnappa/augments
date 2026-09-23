# dsa-tutor — full reference

A Socratic tutor for **deep, durable understanding** of data structures & algorithms. It is a Claude
Code *skill*: you don't run commands, you just talk to Claude, and it decides to use the tutor by
matching what you say.

## Install

```
/plugin marketplace add kushalkrishnappa/augments
/plugin install dsa-tutor@augments
```

Update with `/plugin update dsa-tutor` (Claude Code delivers an update only when the plugin's
`version` is bumped).

## What it does

- **Pre-tests before teaching** — probes what you already know so it can aim correctly.
- **Builds mental models and mnemonics** — anchors each concept to something concrete before the
  formal definition.
- **One guiding question at a time** — teaching is a dialogue, not a lecture; it asks why/how/
  what-breaks, not bare definitions.
- **Interactive visualizations** — generates self-contained HTML animations (BFS, DP tables, two
  pointers, …) you open locally and it narrates while you watch.
- **Feynman teach-back** — has you explain concepts back to expose gaps no quiz finds.
- **Immediate, concrete correction** — names the exact misconception and gives a counterexample.
- **Spaced repetition** — schedules reviews by pairing your correctness with your stated confidence,
  surfacing confidently-wrong items first.

## How to trigger it

Just say what you want, e.g.:

- "teach me graphs" / "help me understand Dijkstra"
- "quiz me on dynamic programming" / "test my memory"
- "I keep forgetting how BFS works"
- "let's continue where we left off" / "review" / "interview prep"

## Where your progress lives

All state lives in a dedicated **study workspace** — a directory that exists only for DSA study, with
one subdirectory per topic area (`graphs/`, `dp/`, …), each holding a `.dsa-tutor/` folder:

| Path | Holds |
|---|---|
| `<workspace>/.dsa-tutor/profile.json` | learner level, streak, last session, per-area roll-up |
| `<area>/.dsa-tutor/progress.json` | that area's curriculum, concepts, mnemonics, mistakes, schedule |
| `<area>/.dsa-tutor/roadmap.html` | that area's prerequisite roadmap |
| `<area>/.dsa-tutor/visuals/*.html` | generated concept animations |

**It never writes into an arbitrary or work repo.** The default workspace is `~/dsa-tutor/`, and the
first time the tutor needs to create state it tells you the path and asks you to confirm (or name
your own). If you want your progress to travel across machines, point it at a git-synced or
cloud-backed directory.

Progress files carry a `schema_version` so the tutor can migrate your data safely across plugin
updates. See `dsa-tutor/skills/dsa-tutor/references/progress_schema.md` for the full schema.

## Note on the local vs plugin copy

`dsa-tutor` was first authored inside a study repo; this plugin is the de-coupled, install-safe
source of truth. If you also keep a project-level copy in some repo's `.claude/skills/`, that local
copy shadows the plugin for the bare name — see
[local-vs-plugin-dsa-tutor.md](./local-vs-plugin-dsa-tutor.md).

## Known follow-ups

Post-release hardening (data-durability, workspace-safety edge cases, and instruction
disambiguation) is tracked in [dsa-tutor-improvements.md](./dsa-tutor-improvements.md).
