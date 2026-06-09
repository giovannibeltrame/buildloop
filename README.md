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

## References

BuildLoop wires together established methods:

- **DDD** — Domain-Driven Design: model the problem in the domain's own language before writing code (Eric Evans, *Domain-Driven Design*, 2003).
- **BDD** — Behavior-Driven Development: specify behavior as concrete, executable scenarios (Dan North, *Introducing BDD*, 2006).
- **TDD** — Test-Driven Development: red → green → refactor, one failing test at a time (Kent Beck, *Test-Driven Development: By Example*, 2002).
- **DRY** — Don't Repeat Yourself: every piece of knowledge has one authoritative representation (Hunt & Thomas, *The Pragmatic Programmer*, 1999).
- **Spotify's product-building workflow** — the Think it · Build it · Ship it · Tweak it loop that BuildLoop's four phases mirror.[^spotify]

Also shaped by KISS, YAGNI, spec-driven development, and harness engineering.

[^spotify]: Henrik Kniberg, *How Spotify Builds Products*, Crisp, 2013 — <https://blog.crisp.se/wp-content/uploads/2013/01/HowSpotifyBuildsProducts.pdf>

