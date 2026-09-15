# ibraheem4/claude-marketplace

Claude Code plugin marketplace.

    /plugin marketplace add ibraheem4/claude-marketplace
    /plugin install agent-skills@ibraheem4
    /plugin install marketing-skills@ibraheem4

## Plugins

| Plugin | Scope | Source |
|---|---|---|
| `agent-skills` | Engineering practice, project-agnostic — no project coupling | `ibraheem4/agent-skills` |
| `marketing-skills` | Marketing and content: copy, landing pages, SEO, structured data | `ibraheem4/marketing-skills` |
| `vendor-ops` | Vendor and tool operations — issue trackers, knowledge wikis | `ibraheem4/ops-skills` |
| `delivery-skills` | Delivery operations — deploy, QA, dependency triage, release | `ibraheem4/delivery-skills` |
| `venture-operations` | Company operations and knowledge base | `ibraheem4/brain` |

## How a version reaches an install

An install directory is keyed by the **version string** in this manifest:
`~/.claude/plugins/cache/<marketplace>/<plugin>/<version>/`. A version bump is what
triggers a re-fetch — pushing alone does not.

So, in order:

1. Merge the plugin repo and bump its `.claude-plugin/plugin.json`.
2. Then bump the version here. Every `source` is a plain git URL with no ref, so it
   resolves the default branch — bumping this first caches stale content under a fresh
   version number, which then reads as current and never re-fetches.
3. `/plugin update` and restart. Merged is not installed.

## Adding a plugin

Give it its own repo with `skills/<name>/SKILL.md` and a `.claude-plugin/plugin.json`,
then add an entry above. Keep project-specific skills out of `agent-skills` — that plugin
is for things that apply everywhere.

## Codex

Claude Code plugins do not serve the Codex runtime. `~/.codex/skills/` holds symlinks into
the local checkouts and must not be torn down. Those are filesystem paths, so moving a repo
between GitHub owners does not affect them.
