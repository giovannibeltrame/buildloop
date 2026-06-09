# BuildLoop

A simple tool for building software with AI.

> **(Re)Think it → Plan it → Build it → Ship it. Loop again 🔄**

Less complexity leaves more tokens for the work itself.

## What's here

BuildLoop is a **Claude Code plugin marketplace**. It currently ships one plugin:

- **[`buildloop`](plugins/buildloop/)** — the loop above, made operational: skills that drive four phases through three gates, four review agents, a zero-dependency `buildloop` helper, and a bundled `AGENTS.md` methodology its skills and agents read.

```
.claude-plugin/marketplace.json   ← marketplace manifest
plugins/buildloop/                ← the plugin (skills, agents, bin, templates)
```

See the [plugin README](plugins/buildloop/) for the full inventory, the loop map, and the adoption guide.

## Install in any project

From inside a project's Claude Code session:

```
/plugin marketplace add giovannibeltrame/buildloop
/plugin install buildloop@buildloop
```

Or commit it so everyone who clones the project gets it, by adding to the project's `.claude/settings.json`:

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
