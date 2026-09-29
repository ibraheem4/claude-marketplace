# Design contract — example

A filled-in example of what `{{design_contract}}` contains. **Every value here is invented.**
Copy the structure, replace all of it, and keep the real one in your own repository where the
build can read it.

A contract exists so that a screen is not an opinion. When this file and the repository's
generated reference disagree, the repository wins — it is what actually ships.

## Foundations

- **Voice.** One line naming the register and what it refuses. *"Direct and specific; no
  promotional filler, no manufactured certainty."* A voice nobody can violate is not a voice.
- **Typography.** One interface family with its weight range, and one monospace family reserved
  for machine facts — identifiers, checks, timestamps, code. Naming the *reservation* matters
  more than naming the font.
- **Theme modes.** Which preferences exist, which resolved palettes exist, and the default. These
  differ: "System" is a preference, not a palette. Say how a stored legacy value migrates.
- **Canvas and surfaces.** Per mode: the canvas, the primary surface, and each raised tier.
- **Signal color.** One, with an exhaustive list of what it means — focus, selection, active
  work, links, primary action. Anything outside that list must not use it.
- **Status colors.** Each with the condition that earns it. *"Green only where completion is
  independently verified"* is a contract; "green means good" is not.
- **Geometry.** A spacing base and the functional radii. Say where pills are allowed, or they
  become buttons.
- **Elevation.** The named tiers in order, and whether depth comes from surface shifts and
  borders before shadows.
- **Signature structure.** If your product has a recurring spine — `source → work → decision →
  evidence` — name it, so every screen renders it the same way.

## Required state language

The contract's job here is to make omission visible. See the skill's §3 table; the contract adds
what each state looks like *in your system* — which component renders it, what copy pattern it
follows, and what it must never do.

## Prohibited patterns

The specific ones that cost you something. Examples of the shape:

- A competing font, palette, theme mode or local foundation value.
- *(a superseded accent)* — name any color the product has deliberately moved away from, so it
  cannot drift back in. This is the line that ages fastest and matters most.
- A status color used for emphasis.
- Generic equal-weight card grids with no dominant structure.
- Decorative gradients, glow, glass, oversized radii, or shadow-heavy grouping.
- Fake sample data presented as live product state.

## Versioning

Give the contract a version and a digest, and have the skill record both in its report. Without
that you cannot tell whether a screen was built against the current contract or one from six
months ago — and "it looked fine" is not an answer to that question.

⚠️ **Contracts get superseded.** When the product's direction changes, the contract changes with
it and the old one is deleted, not archived in place. A skill that hardcodes contract values
instead of reading this file breaks silently on that day and keeps confidently applying a dead
design.
