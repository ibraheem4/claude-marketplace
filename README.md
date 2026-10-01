# ibraheem4/claude-marketplace

Claude Code plugin marketplace.

    /plugin marketplace add ibraheem4/claude-marketplace
    /plugin install agent-skills@ibraheem4
    /plugin install marketing-skills@ibraheem4

## Plugins

| Plugin | Covers | Path |
|---|---|---|
| `agent-skills` | Engineering practice: how to work, what to check, what to refuse; workstation ops | `plugins/agent-skills` |
| `frontend-skills` | UI engineering, accessibility, Tailwind v4, design contracts | `plugins/frontend-skills` |
| `infra-skills` | AWS, GCP, DNS, identity, preview environments | `plugins/infra-skills` |
| `delivery-skills` | Deploy, QA, release, and the trust and delivery governance chains | `plugins/delivery-skills` |
| `marketing-skills` | Copy, SEO, CRO, email, analytics, competitor teardown | `plugins/marketing-skills` |
| `vendor-ops` | Issue trackers and knowledge wikis | `plugins/vendor-ops` |

## How a change reaches an install

Every plugin lives in this repo under `plugins/<name>`, and its entry above uses
`"source": "./plugins/<name>"`. Each was imported from its own repo on 2026-09-29 with
`git subtree`, history kept; the old per-plugin repos are no longer the source.

The install cache is keyed by the **version string**:
`~/.claude/plugins/cache/<marketplace>/<plugin>/<version>/`. Pushing alone does not re-fetch.

1. Change the skill, and in the **same commit** bump the version in both
   `plugins/<name>/.claude-plugin/plugin.json` and its entry in `.claude-plugin/marketplace.json`.
2. Push `main`.
3. `claude plugin marketplace update ibraheem4`, `claude plugin update <name>@ibraheem4`,
   then restart. Merged is not installed.

## Adding a plugin

Add `plugins/<name>/` with `skills/<skill>/SKILL.md` and `.claude-plugin/plugin.json`, then
an entry above. Keep project-specific skills out of `agent-skills` — that plugin is for
things that apply everywhere.

## Codex

Claude Code plugins do not serve the Codex runtime. `~/.codex/skills/` holds symlinks into
`plugins/<name>/skills/` in the local checkout (`~/Projects/skills/claude-marketplace`) and
must not be torn down. Moving this repo on disk breaks them; moving it between GitHub owners
does not.
