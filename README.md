# augments

Kushal's shareable [Claude Code](https://claude.com/claude-code) plugin marketplace — a single,
public, git-based source of truth for skills and agents that can be installed on any machine and
shared with anyone.

Distribution mirrors the plugin-marketplace model: add the marketplace once, then install only the
plugins you want.

## Install

```
/plugin marketplace add kushalkrishnappa/augments
/plugin install dsa-tutor@augments
```

Update to the latest version everywhere with:

```
/plugin update dsa-tutor
```

Claude Code pins an installed plugin to its manifest `version` and only delivers updates when that
number is bumped.

## Plugins

### `dsa-tutor`

A Socratic tutor for **deep, durable understanding** of data structures & algorithms. It pre-tests
before teaching, builds mental models and mnemonics, works one guiding question at a time, generates
self-contained interactive HTML visualizations, has you teach concepts back, and schedules review by
spaced repetition.

Just talk to it — "teach me graphs", "quiz me on DP", "I keep forgetting how Dijkstra works",
"let's continue where we left off", "interview prep". Claude decides when to use the skill by
matching what you ask.

**Where your progress lives.** `dsa-tutor` keeps all state inside a **dedicated study workspace** —
a directory that exists only for DSA study, with one subdirectory per topic area. It **never** writes
into an arbitrary or work repo you happen to launch from: the first time it needs to create state, it
tells you the path (default `~/dsa-tutor/`) and asks you to confirm. Progress and generated visuals
live under each area's `.dsa-tutor/` folder and are the tutor's memory across sessions.

## How this repo is laid out

```
augments/
  .claude-plugin/marketplace.json   # lists every plugin in this repo
  dsa-tutor/                        # one self-contained plugin
    .claude-plugin/plugin.json
    skills/dsa-tutor/
      SKILL.md
      references/
  README.md
  LICENSE
```

Adding a plugin later is a drop-in: a new top-level directory with its own
`.claude-plugin/plugin.json`, appended to `marketplace.json`. No existing plugin changes.

## License

[MIT](./LICENSE) — free to use, modify, and share.
