# Local vs. plugin `dsa-tutor` — name-collision behavior

`dsa-tutor` was first authored as a **project-level skill** inside the `dsa` study repo
(`~/Playground/dsa/.claude/skills/dsa-tutor/`). It is now also published here as an installable
**plugin skill** (`dsa-tutor@augments`). If both exist on the same machine at once — e.g. you open a
Claude Code session *inside* the `dsa` repo while the plugin is installed globally — two skills share
the name `dsa-tutor`. This note documents what happens and how to avoid confusion.

## What Claude Code does

- **Both skills are available; they do not silently overwrite each other.** Plugin skills are
  namespaced, so no data is lost to the collision.
- **The non-plugin (project/local) skill takes precedence** over the plugin skill for the bare name
  `dsa-tutor`. Precedence, highest to lowest: enterprise > personal > **project/local** > plugin >
  claude.ai-synced. So inside the `dsa` repo, the repo's own copy wins.
- **Plugin skills are namespaced as `plugin:skill`.** The published skill can always be addressed
  explicitly as **`dsa-tutor:dsa-tutor`** (`<plugin-name>:<skill-name>`), which bypasses the
  collision and guarantees you get the plugin version.

Sources: Claude Code docs — [Skills](https://code.claude.com/docs/en/skills) (skill naming,
invocation, and command-collision precedence) and [Plugins](https://code.claude.com/docs/en/plugins)
(plugin namespacing).

## Why this repo is the source of truth

Per the design doc, **`augments` is the single source of truth** for the *published* `dsa-tutor`; the
`dsa` repo copy is a one-time authoring reference and is intentionally left untouched. The two copies
can therefore drift — the `dsa` repo copy is the older, repo-coupled version; the plugin copy here is
the de-coupled, install-safe one.

## How to avoid ambiguity

Pick one of these depending on how you work:

1. **Rely on the plugin everywhere (recommended for consumers).** Don't keep a project copy in any
   repo you study in. Install `dsa-tutor@augments` and just talk to it; there is no collision.
2. **Studying inside the old `dsa` repo?** Be aware the repo's local copy shadows the plugin there.
   Either (a) accept that and treat the repo copy as canonical *in that repo only*, or (b) address
   the plugin explicitly with `dsa-tutor:dsa-tutor`, or (c) remove
   `~/Playground/dsa/.claude/skills/dsa-tutor/` so only the plugin remains.
3. **Editing the skill?** Edit it **here** in `augments`, bump the plugin `version`, and
   `/plugin update dsa-tutor`. Do not edit the `dsa` repo copy expecting it to reach other machines —
   it won't; only the plugin is distributed.
