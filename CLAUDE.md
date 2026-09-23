# Project Instructions for AI Agents

This file provides instructions and context for AI coding agents working on this project.

## Beads Issue Tracker

This project uses **bd (beads)** for issue tracking. Run `bd prime` to see full workflow context and commands.

### Quick Reference

```bash
bd ready              # Find available work
bd show <id>          # View issue details
bd update <id> --claim  # Claim work
bd close <id>         # Complete work
```

### Rules

- Use `bd` for ALL task tracking — do NOT use TodoWrite, TaskCreate, or markdown TODO lists
- Run `bd prime` for detailed command reference and session close protocol

**Architecture in one line:** issues live in a local Dolt DB; sync uses `refs/dolt/data` on your git remote; `.beads/issues.jsonl` is a passive export. See https://github.com/gastownhall/beads/blob/main/docs/SYNC_CONCEPTS.md for details and anti-patterns.

## Agent Context Profiles

The managed Beads block is task-tracking guidance, not permission to override repository, user, or orchestrator instructions.

- **Conservative (default)**: Use `bd` for task tracking. Do not run git commits, git pushes, or Dolt remote sync unless explicitly asked. At handoff, report changed files, validation, and suggested next commands.
- **Minimal**: Keep tool instruction files as pointers to `bd prime`; use the same conservative git policy unless active instructions say otherwise.
- **Team-maintainer**: Only when the repository explicitly opts in, agents may close beads, run quality gates, commit, and push as part of session close. A current "do not commit" or "do not push" instruction still wins.

## Session Completion

This protocol applies when ending a Beads implementation workflow. It is subordinate to explicit user, repository, and orchestrator instructions.

1. **File issues for remaining work** - Create beads for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **Handle git/sync by active profile**:
   ```bash
   # Conservative/minimal/default: report status and proposed commands; wait for approval.
   git status

   # Team-maintainer opt-in only, unless current instructions forbid it:
   git pull --rebase
   git push
   git status
   ```
5. **Hand off** - Summarize changes, validation, issue status, and any blocked sync/commit/push step

**Critical rules:**
- Explicit user or orchestrator instructions override this Beads block.
- Do not commit or push without clear authority from the active profile or the current user request.
- If a required sync or push is blocked, stop and report the exact command and error.

## Architecture Overview

`augments` is a git-based **Claude Code plugin marketplace** — there is no runtime or compiled
artifact. `.claude-plugin/marketplace.json` is the index that lists every plugin; each plugin is a
self-contained top-level directory (e.g. `dsa-tutor/`) carrying its own `.claude-plugin/plugin.json`
and its skills/agents. Distribution is git: users run `/plugin marketplace add …` then
`/plugin install <plugin>@augments`, and Claude Code pins each install to the manifest `version`.

## Build & Test

No build step — the repo is plain Markdown + JSON consumed directly by Claude Code. "Testing" means
validation:

- **Manifests are valid JSON** and marketplace entries match the on-disk plugin directories/versions
  (`bd doctor` for tracker health; validate the JSON before bumping a `version`).
- **Plugin skills ship evals** under `<plugin>/evals/`, runnable with `claude plugin eval`.

## Conventions & Patterns

### How this repo is laid out

```
augments/
  .claude-plugin/marketplace.json   # lists every plugin in this repo
  dsa-tutor/                        # one self-contained plugin
    .claude-plugin/plugin.json
    skills/dsa-tutor/
      SKILL.md
      references/
    evals/                          # plugin evals (claude plugin eval)
  docs/
    dsa-tutor.md                    # one lean reference per plugin
  README.md
  LICENSE
```

Adding a plugin later is a drop-in: a new top-level directory with its own
`.claude-plugin/plugin.json`, appended to `marketplace.json`. No existing plugin changes.

### Documentation

`docs/` holds exactly one lean reference per plugin, named `docs/<plugin-name>.md` (what it is,
install, how to invoke, where state lives). Specs and design docs do **not** live in `docs/`. Keep
the README plugin blurbs to a few lines that link out to the full `docs/<plugin>.md` reference.
