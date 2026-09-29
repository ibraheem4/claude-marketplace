# Frontend Skills

Frontend and UI engineering for AI coding agents. Split out of
[agent-skills](../agent-skills), which keeps the language-agnostic
engineering practice — these are the ones that only apply when you are building an interface.

| Skill | Use when |
|-------|----------|
| `frontend-ui-engineering` | Building components, pages or interactive features |
| `better-accessibility` | Focus, keyboard, ARIA, forms, screen readers — at component level |
| `web-design-guidelines` | Reviewing a finished surface against the Web Interface Guidelines |
| `tailwind-v4` | CSS-first `@theme` config, v3→v4 migration, classes that silently don't generate |
| `ui-component-lookup` | A quick reference card for any component: anatomy, props, a11y, anti-patterns |
| `scroll-driven-animation` | Scroll-linked motion — pinning, reveal-on-enter, and why it renders blank |
| `interface-design` | Designing or reviewing a screen against the repo's own design contract |
| `ui-content-design` | The words inside a product UI — labels, helper text, empty and error states, consent prompts |
| `theme-audit` | Finding drift: local foundation values, copied components, superseded references |

`better-accessibility` and `web-design-guidelines` are deliberately distinct: the first is
component-level engineering, the second reviews a surface that already exists.

`interface-design` and `theme-audit` are a pair: one builds against a contract, the other finds
where a screen has drifted from it. Both read the contract from a profile rather than carrying
one, so they survive a redesign — see `interface-design/references/contract-example.md`.

## Install

```
/plugin marketplace add ibraheem4/claude-marketplace
/plugin install frontend-skills@ibraheem4
```

## License

MIT. See [LICENSE](LICENSE) — this repository carries notices from the upstream authors of
skills that originated elsewhere.
