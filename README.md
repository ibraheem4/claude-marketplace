# ibraheem4/claude-marketplace

Claude Code plugin marketplace for Acme.

    /plugin marketplace add ibraheem4/claude-marketplace
    /plugin install agent-skills@ibraheem4
    /plugin install delivery-skills@ibraheem4
    /plugin install wiki-skills@ibraheem4

## Scope

| Plugin | Scope | Source |
|---|---|---|
| `agent-skills` | Truly global — engineering practice, no project coupling | `ibraheem4/agent-skills` |
| `delivery-skills` | Org delivery operations | `ibraheem4/delivery-skills` |
| `wiki-skills` | Org skills that must stay beside wiki decisions | `ibraheem4/wiki` |

Personal skills are deliberately **not** here — see `ibraheem4/arcana-skills`.

## Adding a new project's skills

Give the project its own repo with `skills/<name>/SKILL.md` and a
`.claude-plugin/plugin.json`, then add an entry above. Keep project-specific
skills out of `agent-skills` — that plugin is for things that apply everywhere.

## Note on Codex

Claude Code plugins do not serve the Codex runtime. `~/.codex/skills/`
symlinks into `~/Projects/wikis/ibraheem4/skills/` are still required and must
not be torn down.
