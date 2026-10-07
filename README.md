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
/plugin install clarifying-questions@augments
```

Update to the latest version everywhere with:

```
/plugin update dsa-tutor
```

Claude Code pins an installed plugin to its manifest `version` and only delivers updates when that
number is bumped.

## Plugins

### `dsa-tutor`

A Socratic tutor for **deep, durable understanding** of data structures & algorithms — pre-tests
before teaching, works one guiding question at a time, generates interactive visualizations, and
schedules review by spaced repetition. Just talk to it: "teach me graphs", "quiz me on DP",
"interview prep". Progress lives in a dedicated study workspace (default `~/dsa-tutor/`).

Full reference: [docs/dsa-tutor.md](./docs/dsa-tutor.md)

### `clarifying-questions`

Teach + drill **TAP (Test · Ask · Plan)** — a memorable structure for the first minutes of a coding
interview: which clarifying questions to ask about constraints and edge cases, and what to do when your
mind goes blank. Works for DSA, whiteboard, and OOD problems. Stateless. "Teach me how to start a
problem", "drill me on clarifying questions".

Full reference: [docs/clarifying-questions.md](./docs/clarifying-questions.md)

## License

[MIT](./LICENSE) — free to use, modify, and share.
