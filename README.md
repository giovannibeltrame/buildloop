# BuildLoop
A simple tool for building software with AI.

## Purpose

Simplicity is key. Less complexity means more tokens for being used.

## Workflow

(Re)Think it. Plan it. Build it. Ship it. Loop again 🔄

## Core principles, alphabet soup

- BDD
- DDD
- DRY
- Harness engineering
- KISS
- SDD
- TDD
- YAGNI

## Some challenges on dev with AI

1. over-generation
2. slop
3. hallucinations
4. one-shot hero
5. premature victory
6. fake-tests

## What's here

BuildLoop is a **Claude Code plugin marketplace**. It currently ships one plugin:

- **[`buildloop`](plugins/buildloop/)** is the loop above, made operational: twelve skills that drive four phases (①→②→③→④) through three gates, four review agents (think-gate, plan-gate, code-checker, ux-checker), a zero-dependency `buildloop` helper, and an `AGENTS.md` operating-manual template you adapt to your repo. State lives in each doc's `## Log` table, not a status field.

```
.claude-plugin/marketplace.json   ← marketplace manifest
plugins/buildloop/              ← the plugin (skills, agents, bin, template)
```

## Install in any project

From inside a project's Claude Code session:

```
/plugin marketplace add giovannibeltrame/buildloop
/plugin install buildloop@buildloop
```

Or commit it so everyone who clones the project gets it. Add it to the project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "buildloop": {
      "source": { "source": "github", "repo": "giovannibeltrame/buildloop" }
    }
  },
  "enabledPlugins": {
    "buildloop@buildloop": true
  }
}
```

Then follow the plugin's [adoption guide](plugins/buildloop/README.md): drop `templates/AGENTS.md` at your repo root, create `docs/buildloop/{working,working/archive,living}/` (and a root `CHANGELOG.md`), and fill in the `[PROJECT: …]` notes.
