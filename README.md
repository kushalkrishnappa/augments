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

A Socratic tutor for **deep, durable understanding** of data structures & algorithms — pre-tests
before teaching, works one guiding question at a time, generates interactive visualizations, and
schedules review by spaced repetition. Just talk to it: "teach me graphs", "quiz me on DP",
"interview prep". Progress lives in a dedicated study workspace (default `~/dsa-tutor/`).

Full reference: [docs/dsa-tutor.md](./docs/dsa-tutor.md)

## License

[MIT](./LICENSE) — free to use, modify, and share.
