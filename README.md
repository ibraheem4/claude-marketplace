# lucitra/claude-marketplace

Claude Code plugin marketplace for Lucitra.

    /plugin marketplace add lucitra/claude-marketplace
    /plugin install agent-skills@lucitra
    /plugin install lucitra-skills@lucitra
    /plugin install lucitra-wiki-skills@lucitra

## Scope

| Plugin | Scope | Source |
|---|---|---|
| `agent-skills` | Truly global — engineering practice, no project coupling | `lucitra/agent-skills` |
| `lucitra-skills` | Org delivery operations | `lucitra/lucitra-skills` |
| `lucitra-wiki-skills` | Org skills that must stay beside wiki decisions | `lucitra/lucitra-wiki` |

Personal skills are deliberately **not** here — see `ibraheem4/arcana-skills`.

## Adding a new project's skills

Give the project its own repo with `skills/<name>/SKILL.md` and a
`.claude-plugin/plugin.json`, then add an entry above. Keep project-specific
skills out of `agent-skills` — that plugin is for things that apply everywhere.

## Note on Codex

Claude Code plugins do not serve the Codex runtime. `~/.codex/skills/`
symlinks into `~/Projects/wikis/lucitra/skills/` are still required and must
not be torn down.
