---
name: interface-design
description: Design, implement or review a product interface against the repository's own design contract rather than improvising one. Use for new screens, UI refactors, onboarding, dashboards, workflows, graphs, responsive behavior, content design or visual QA. Covers the state language most interfaces omit — partial, stale, denied, approval-waiting, reconnecting — and what to verify before claiming a screen is done.
---

# Interface design

Build software that is specific and evidence-led. **Do not improvise a palette, type system,
component language or product voice.** One already exists, or one needs to be written down
before any screen is designed.

## Profile

Workspace values come from a profile, never from this skill. Resolve one first:
`~/<workspace>/.claude/workspace.config.md` — the workspace the caller named, or the single
match of `~/*/.claude/workspace.config.md`. None: say which keys are needed and stop.
A key the profile leaves out means the workspace has none of that thing: say so, and skip
what depends on it — unless skipping leaves a write unguarded, then stop.

| Key | Used for |
|---|---|
| `{{design_contract}}` | Path to the authoritative contract — the file that wins every disagreement |
| `{{design_reference}}` | Generated token manifest, if the contract produces one |
| `{{token_package}}` | The semantic token package a consumer imports |
| `{{ui_package}}` | The shared component package |

`references/contract-example.md` shows what a contract contains and how to write one when the
workspace has none. **It is an example, not the contract** — never treat its values as defaults.

## 1. Establish authority

1. Read `{{design_contract}}` and `{{design_reference}}` before proposing or changing UI. Record
   their version and digest; you will cite them in the report.
2. Locate the consumer's `{{token_package}}` and `{{ui_package}}` imports. Treat missing imports
   or local foundation overrides as migration work, not as the local style.
3. Identify the screen's user, decision, source data, primary task and canonical implementation
   point before editing anything.

If no contract exists, stop and write one first. A screen designed against nothing becomes the
de facto contract, and it will be wrong in ways nobody can point at.

## 2. Shape the experience

Define these in the work record before building:

- the user outcome, and which structure gets visual priority;
- the provenance path from source data through to the decision and its evidence;
- desktop and mobile behavior, including any graph-to-list fallback;
- every state below that applies;
- keyboard order, landmarks, accessible names, live regions, reduced motion, zoom and target size;
- realistic data density, and the next action available in each state.

Do not begin from a generic equal-weight card grid. Start from the decision the operator has to
make and give one structure priority.

## 3. The state language

This is the part most interfaces omit, and the part users meet on their worst day.

| State | Must say |
|---|---|
| Loading | What is being retrieved |
| Empty | Whether zero is desirable or the thing is not yet configured — never the same treatment |
| Partial | What is shown, and what is missing from it |
| Stale | The last valid information, and its age |
| Denied | Which grant is missing, without implying failure |
| Failed | What failed, and one bounded recovery action |
| Approval-waiting | The actor, the action, and the boundary of what was requested |
| Reconnecting | The last valid view, quietly — do not blank the screen |
| Recovered | What resumed, and whether any work was duplicated |

## 4. Build

1. Consume semantic tokens and shared components at the canonical boundary. Promote a missing
   shared role or primitive; do not copy a replacement into one screen.
2. Apply the contract's type, color, radius and elevation rules. Where the contract gives a color
   a meaning, use it only for that meaning — a status color spent on decoration stops being a
   status.
3. Write copy that is specific and honest about missing state. No promotional filler, no
   manufactured certainty. **REQUIRED SUB-SKILL for the words themselves:** use
   frontend-skills:ui-content-design — it carries the section contract that keeps a group of
   controls to one shared guarantee, and the rule that an unverified claim about what the
   system does never ships unmarked.
4. Preserve third-party brand colors only inside official provider marks.
5. Use motion to explain a state transition, and honor reduced motion.

## 5. Verify

1. Run `theme-audit` for token, mode, shared-component, hierarchy and legacy-reference checks.
2. Inspect every theme mode at laptop and mobile widths with realistic content.
3. Verify the states from §3 that apply, plus keyboard navigation, focus order, screen-reader
   structure, contrast, 200% zoom, target size, reduced motion and any fallback.
4. Capture screenshots or visual-regression artifacts. **State plainly when visual verification
   was not possible** rather than implying it happened.
5. Run the consumer's typecheck, build, tests and `git diff --check`.

## 6. Report

Contract version and digest, screens and states changed, shared components used or added,
viewports and modes inspected, checks run, visual evidence, and any drift left behind.

## Never

- A competing font, palette, theme mode or local foundation value.
- Generic equal-weight card grids with no dominant decision structure.
- Decorative gradients, glow, glass or shadow-heavy grouping used instead of hierarchy.
- A status color used for emphasis.
- Fake sample data presented as live product state.
- Hiding a missing, stale, denied, failed or partial state because it is inconvenient to design.
- A cramped mobile graph where an ordered list is clearer.
- Claiming visual verification you did not do.
