# augments — Central, shareable plugin marketplace for Claude Code

**Date:** 2026-09-22
**Status:** Approved design, ready for implementation planning
**Author:** Kushal Krishnappa

## Overview

`augments` is a single, public, git-based **Claude Code plugin marketplace** that
centralizes the user's own skills and agents so they are installable on any
machine (laptop, workstation, remote server) and shareable with anyone. It
replaces the current situation where skills are coupled to individual project
repos (e.g. `dsa-tutor` living in the `dsa` project) or sit in an un-versioned,
un-shareable local directory (`~/.agents/skills`).

The distribution model mirrors the `superpowers` marketplace the user already
runs: add the marketplace once, then install à-la-carte plugins from it.

## Goals

- One central git repo as the source of truth for the user's authored skills/agents.
- Publicly shareable: anyone can add the marketplace and install pieces with two commands.
- Edit-central-then-pull: update by editing the repo, **bumping the plugin's `version`**,
  pushing, and running `/plugin update`. The version bump is required — see the note under
  Non-Goals: Claude Code only delivers updates when `version` changes.
- **Granular installs** — a consumer who only wants `dsa-tutor` installs only that.
- Open for extension: adding a new skill or agent group later is a drop-in, no restructuring.

## Non-Goals (for now / YAGNI)

- Not shipping anything besides `dsa-tutor` in the first version.
- Not migrating third-party/ecosystem skills currently in `~/.agents/skills`
  (e.g. `find-skills`, `diagnosing-bugs`) — only the user's own authored work belongs here.
- Not a live-symlink or dotfiles setup (explicitly declined in favor of `/plugin update`).
- Not shipping global `settings.json` (permissions/model/env) — see Constraints.
- No tagged/versioned-release ceremony (git tags, changelogs, release notes) beyond the
  per-plugin `version` field. **Note:** the `version` field is not decorative — Claude Code
  *pins* an installed plugin to its manifest `version` and only delivers updates when that
  number is bumped. So every change that should reach machines/consumers **requires a version
  bump**; `version` is the update-delivery mechanism, and it is what this project deliberately
  keeps. Only the surrounding release *ceremony* is out of scope.

## Hard Constraint: the `dsa` repo is untouched

The existing `dsa` project repo (`~/Playground/dsa`) is **not modified and nothing
is moved out of it**. It was only the starting point where `dsa-tutor` was first
authored. The skill's files are **copied** from
`~/Playground/dsa/.claude/skills/dsa-tutor/` into `augments`; the originals stay
in place. The `dsa` repo continues to work exactly as before.

## Chosen Approach: one marketplace, several focused plugins

`augments` is the marketplace repo. Each shareable unit is its own small plugin in
its own subdirectory with its own `plugin.json` and independent `version`.
Consumers add the marketplace once and then install only the plugins they want.

### Repository layout

```
augments/
  .claude-plugin/
    marketplace.json          # lists every plugin in this repo
  dsa-tutor/                   # first (and only, for v0.1.0) plugin
    .claude-plugin/
      plugin.json
    skills/
      dsa-tutor/
        SKILL.md               # copied from the dsa repo
        references/
          visual_authoring.md
          progress_schema.md
  README.md                    # what this is + install instructions for anyone
  LICENSE                      # MIT (required so others may freely use it)
  docs/
    specs/
      2026-09-22-augments-plugin-marketplace-design.md   # this file
```

Future plugins (agents, more skills) are added as sibling top-level directories
(e.g. `dev-agents/`, `<future-skill>/`), each with its own `.claude-plugin/plugin.json`,
and appended to `marketplace.json`. No existing plugin changes when a new one is added.

### `marketplace.json` (shape)

```json
{
  "name": "augments",
  "description": "Kushal's shareable Claude Code skills and agents",
  "owner": { "name": "Kushal Krishnappa" },
  "plugins": [
    {
      "name": "dsa-tutor",
      "description": "Socratic tutor for deep, durable understanding of data structures & algorithms",
      "version": "0.1.0",
      "source": "./dsa-tutor",
      "author": { "name": "Kushal Krishnappa" }
    }
  ]
}
```

Adding a plugin later = append one object to `plugins[]` with its own `source`
subdir. That is the entire extension mechanism.

### `dsa-tutor/.claude-plugin/plugin.json` (shape)

```json
{
  "name": "dsa-tutor",
  "description": "Socratic tutor for deep, durable understanding of data structures & algorithms",
  "version": "0.1.0",
  "author": { "name": "Kushal Krishnappa" },
  "license": "MIT",
  "keywords": ["dsa", "tutor", "learning", "algorithms", "data-structures"]
}
```

## Constraint: plugins cannot set global settings

A Claude Code plugin can ship **skills, agents, hooks, slash commands, MCP servers,
LSP servers, and output styles** (hooks via a `hooks/hooks.json` referencing
`${CLAUDE_PLUGIN_ROOT}`; MCP servers via `.mcp.json`). On install these become
**available**, not auto-executed: skills and agents are *model-invoked* — Claude
decides to use them at runtime by matching their `description` — so they don't run
silently. Only **hooks** actually execute automatically (on their configured events);
that is the real auto-execution surface to reason about for trust. A plugin **cannot**
rewrite a user's global `settings.json` (permissions, model, env vars) — Claude Code
keeps that under the user's control.

Implication for this project: `dsa-tutor` needs no hooks and no settings, so this
is a non-issue for v0.1.0. If a future plugin needs shared permissions/model/env,
the pattern is: ship a **documented, reviewable JSON settings fragment** in the repo
that the user reads and **merges into their own `settings.json` by hand** (or via a
copy-paste snippet in the README) — never an executable `bootstrap.sh`, and never a
`curl | sh` installer. For a public, no-review marketplace an executable bootstrap is
a supply-chain footgun: a repo compromise or malicious fork would then run arbitrary
code with the user's privileges, defeating the very safeguard that keeps
`settings.json` under user control. A settings fragment is inert data the user
inspects before applying. Documented now so the pattern is decided; not built yet.

## Install / update / share workflow

**Any machine (yours or anyone's):**
```
/plugin marketplace add kushalkrishnappa/augments
/plugin install dsa-tutor@augments
```

**Update everywhere:** `/plugin update dsa-tutor` (git pull under the hood).

**Authoring loop:** edit files in the `augments` repo → bump the plugin's `version`
→ commit → push → `/plugin update` on each machine.

**Sharing:** publish the repo publicly on GitHub; the two install commands are the
whole onboarding. No Anthropic review, no marketplace fees — distribution is
decentralized and git-based.

## Publishing process (reference)

1. Initialize `augments` as a git repo and push to **public** GitHub.
2. Ensure `marketplace.json`, the `dsa-tutor` plugin, `README.md`, and `LICENSE` are present.
3. Consumers run `/plugin marketplace add kushalkrishnappa/augments` then install.

There is no application, review queue, or developer fee. Because there is no
gatekeeping, a clear README and an explicit LICENSE are what establish trust.

## Verification / testing

- `marketplace.json` and every `plugin.json` are valid JSON with required fields.
- **Test-install from a local path first** (`/plugin marketplace add ~/Playground/augments`)
  before making the GitHub repo public.
- Confirm `dsa-tutor` appears via the Skill tool and triggers on its described
  phrases ("teach me", "quiz me", etc.), reading/writing topic progress correctly.
- Optionally run `claude plugin eval` on the skill to check triggering accuracy.
- Confirm the original `dsa` repo is unchanged (`git status` clean there).

## Open questions / deferred decisions

- Exact GitHub repo name/visibility (assumed: public, `augments`, under the user's account).
- Whether to add an `augments-all` convenience meta-plugin later (deferred; not needed for one plugin).
- Which, if any, of the user's other `~/.agents/skills` are their own and worth adding next (deferred to future versions).
