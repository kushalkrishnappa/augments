# Progress File Schema

Learner state is split across **two kinds of file** so that a session only ever loads the topic it
is working on. Read them at session start; write after every checkpoint and at session end. Keep
the structures below stable — append and update, don't restructure.

| File | Scope | Size |
|---|---|---|
| `<workspace>/.dsa-tutor/profile.json` | learner-global | tiny (a few hundred bytes) |
| `<topic-folder>/.dsa-tutor/progress.json` | **one area only** | grows with that area |

- **Study workspace** — the dedicated study directory (default `~/dsa-tutor/`); the one that holds
  a `.dsa-tutor/profile.json`. See SKILL.md for how it is resolved and the write-safety guard.
- **Topic folder** — an immediate child directory of the workspace, excluding dotfolders and
  `docs`, `__pycache__`, `node_modules`, `venv`. **The area name is the folder name.**

Never put concepts, curricula, or notes in `profile.json`. If it grows past a kilobyte, something
that belongs in a topic folder has leaked into it.

## `schema_version` (every file)

Both file kinds carry a top-level integer `schema_version`. **The current version is `1`.** It
exists so a schema change shipped via `/plugin update` can migrate a learner's on-disk files
instead of silently misreading or corrupting them.

- **Read:** before using any file, check `schema_version`. If it is **missing**, treat the file as
  **version 0** (pre-versioning) and migrate it (below). If it is **lower** than the current
  version, run each migration step in order up to current. If it is **higher** than the version
  this plugin knows, do **not** write to it — tell the learner their `dsa-tutor` plugin is older
  than their data and to `/plugin update`, so a stale plugin can't clobber newer files.
- **Write:** always stamp the current `schema_version` on every file you write.
- **"Breaking" means an on-disk schema change** — any change to the shape, meaning, or required
  fields of what is stored in `profile.json` / `progress.json`. Prose, pedagogy, or visual-template
  changes are **not** breaking and do **not** bump `schema_version`. A breaking change bumps
  `schema_version` by one and adds a numbered migration step below.

## `profile.json` (workspace root)

```json
{
  "schema_version": 1,
  "learner_level": "intermediate",
  "last_session_date": "2026-08-11",
  "streak": 8,
  "areas": [
    { "folder": "graphs", "last_session_date": "2026-07-30" }
  ],
  "notes": "Prefers Python."
}
```

- `schema_version` — integer; current is `1`.
- `learner_level` — e.g. `"intermediate"`.
- `last_session_date` — ISO date. Set to today on every write.
- `streak` — distinct days studied; increment when today differs from the previous value.
- `areas` — a deliberately minimal two-field roll-up, rewritten whenever an area is written. A
  directory listing is the source of truth for which topic folders exist; this is only a convenience.
- `notes` — cross-cutting preferences (language, session length). Topic-specific notes do **not**
  go here.

## `progress.json` (one per topic folder)

```json
{
  "schema_version": 1,
  "area": "graphs",
  "last_session_date": "2026-07-30",
  "roadmap": ".dsa-tutor/roadmap.html",
  "current": "Topological Sort",
  "topics": [
    { "name": "Graph Terminology", "status": "done" },
    { "name": "Topological Sort", "status": "in-progress" },
    { "name": "Bipartite Check", "status": "not-started" }
  ],
  "concepts": [ /* see below */ ],
  "notes": "",
  "session_recap": ""
}
```

- `schema_version` — integer; current is `1`.
- `area` — the folder name. Every concept in this file belongs to it, so concepts carry no `area`.
- `roadmap` — path **relative to the topic folder**, always `.dsa-tutor/roadmap.html`.
- `topics` — ordered array of `{ name, status }`, `status` ∈ `not-started | in-progress | done`.
- `current` — name of the topic in progress, or `null`.
- `notes` / `session_recap` — freeform, scoped to this area.

### Concept objects

```json
{
  "name": "Cycle Detection",
  "mental_model": "A pan on the stove: GRAY = still cooking on my current path.",
  "mnemonic": "Back edge = you met someone still on your own path. Cross edge = already served.",
  "mastery": "solid",
  "last_reviewed": "2026-07-29",
  "review_interval": 3,
  "aces": 2,
  "past_mistakes": [
    { "note": "Treated any already-visited node as a cycle, which flags cross edges too.",
      "high_confidence": true }
  ],
  "visual": [
    ".dsa-tutor/visuals/cycle-detection.html",
    ".dsa-tutor/visuals/cycle-edge-classification.html"
  ],
  "practice_done": ["LC-207"],
  "calibration": {
    "confident_right": 3, "confident_wrong": 1,
    "unsure_right": 2, "unsure_wrong": 0
  },
  "taught_back": "2026-07-29"
}
```

- `mastery` — `"learning" | "shaky" | "solid"`.
- `review_interval` — integer days; see the schedule below.
- `aces` — count of clean, confident correct answers.
- `past_mistakes` — array of **objects**: `note` is the exact misconception, phrased so it can be
  re-tested; `high_confidence: true` marks one the learner got wrong *while sure they were right*.
  Keep these forever; they feed targeted quizzes.
- `visual` — **array** of paths relative to the topic folder; `[]` when none. A concept may have
  several.
- `practice_done` — solved problem identifiers, e.g. `["LC-200", "HR-bfs-shortest-reach"]`.
- `calibration` — four counters pairing correctness with stated confidence. Powers the schedule
  below and lets you tell the learner where they're systematically overconfident.
- `taught_back` — ISO date of the last successful Feynman explanation, or `null`.

## Confidence-weighted review schedule

Every graded answer pairs **correctness** with the learner's **stated confidence**. Update the
matching `calibration` counter, then set the interval:

| Answer | Confidence | `review_interval` | Also |
|---|---|---|---|
| Correct | Confident | advance: 1 → 3 → 7 → 16 → 30 | `aces++`, raise `mastery` when earned |
| Correct | Unsure | advance **one notch only**, never to 30 | leave `mastery` where it is — it's fragile |
| Wrong | Unsure | reset to 1 | append a `past_mistakes` entry, lower `mastery` |
| Wrong | **Confident** | reset to 1 | append with `high_confidence: true`, lower `mastery`, **surface first next session and re-teach rather than re-quiz** |

A concept is **due** when `today - last_reviewed >= review_interval`. Surface due concepts first,
high-confidence errors before everything else.

## Migrations

Run these in order, from the file's stored version up to current, whenever you open a file whose
`schema_version` is lower than current (a missing field means version 0). After migrating in memory,
the next write stamps the new `schema_version`. Never edit a file whose version is *higher* than you
know — ask the learner to `/plugin update` first.

### 0 → 1 — introduce `schema_version` (and split any legacy monolith)

Version 0 is any file with no `schema_version` field.

- **Per-file stamp:** add `schema_version: 1` to each `profile.json` / `progress.json` on next write;
  no other change is required for files already in the per-area shape.
- **Legacy monolith:** if a single root `.dsa-tutor/dsa_progress.json` is found, split it — each
  `curriculum.<area>` plus its matching concepts becomes `<area>/.dsa-tutor/progress.json` (drop each
  concept's `area`, arrayify `visual`, zero-init `calibration`, `taught_back: null`, add
  `schema_version: 1`); globals go to `profile.json` (with `schema_version: 1`); then delete the old
  file.

### Adding future migrations

When a change alters the on-disk shape: bump the current version to N, and add a `N-1 → N` step here
describing exactly what to add, rename, or transform, keyed off the old version's fields. Keep steps
idempotent and never destructive without first preserving the old data.
