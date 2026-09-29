---
name: theme-audit
description: Audit and repair an interface against its own design contract — theme modes, typography, elevation, density, shared-component usage, hierarchy, semantic status roles, accessibility, responsive fallbacks and prohibited legacy references. Use when a surface drifts visually, contains local design foundations copied from somewhere else, or has incomplete theme and state behavior.
---

# Theme audit

Audit against the repository's contract, its generated token output, and its shared UI package.
**Do not recreate foundation values inside an application** — that is the drift this skill exists
to find.

## Profile

Every workspace value here comes from a profile, never from this skill. Resolve one first:
`~/<workspace>/.claude/workspace.config.md` — the workspace the caller named, or the single
match of `~/*/.claude/workspace.config.md`. Several matches: ask which. None: say which keys
are needed and stop.
A key the profile leaves out means the workspace has none of that thing: say so, and skip
what depends on it — unless skipping leaves a write unguarded, then stop.

| Key | Used for |
|---|---|
| `{{design_contract}}` | The authoritative contract |
| `{{design_reference}}` | Generated token manifest, if one is produced |
| `{{token_package}}` | The semantic token package |
| `{{ui_package}}` | The shared component package |

Resolution and the contract's shape are in `interface-design` — this skill verifies what that one
builds. See `interface-design/references/contract-example.md` if the workspace has no contract.

## Audit

1. Read `{{design_contract}}` and `{{design_reference}}`. Record version and digest. Confirm the
   theme preferences and the resolved palettes are the sets the contract names — they are not the
   same set, and conflating them is a common defect.
2. Locate `{{token_package}}` and `{{ui_package}}` imports. Flag copied controls, shells, panels,
   statuses, empty states, toasts and tiles — a local copy is drift even when it looks identical
   today.
3. Find hardcoded foundation values: raw colors, font stacks, spacing and radii that should come
   from tokens.
4. Check hierarchy: one dominant structure per screen, not an equal-weight grid.
5. Check typography against the contract's families and their reservations — a monospace family
   reserved for machine facts, used for prose, is a violation even though it renders.
6. Check semantic meaning. Every status color must carry only the meaning the contract assigns
   it. A status color spent on emphasis stops working as a status everywhere else.
7. Check elevation and density against the named tiers and spacing base.
8. Search for **superseded references** — a retired accent, a renamed token, an old contract name
   still cited in comments or docs. These are the ones that silently outlive a redesign.
9. Check accessibility: contrast in every mode, focus visibility, target size, 200% zoom,
   reduced motion.
10. Check responsive fallbacks actually render at mobile widths with realistic content.

## Repair

Fix by promoting to the shared layer, never by copying a replacement into the screen. If a role
or primitive is missing from `{{ui_package}}`, add it there and consume it — a local fix is the
next audit's finding.

## Report

Contract version and digest, files audited, violations by category, what was repaired, what was
promoted to the shared layer, and what drift remains with the reason it was left.
