---
name: creative-direction
description: Develop an original creative direction for a marketing page's signature moment (hero animation, illustration, interaction, colour story) from rendered inspiration, then prototype it live. Use when the user asks for something "more creative", "better", "more unique", a new hero animation, mouse interaction, or inspiration from Awwwards, Godly, Dribbble, Behance, Siteinspire, Landingfolio, Mobbin, Screenlane, UI Sources, Figma Community or CodePen. Do not use to tear down one named competitor (see site-teardown), to extract tokens and a component spec from a site (see analyze-website-style), or to write copy (see copywriting).
metadata:
  version: 1.1.0
---

# Creative direction

A hero that could sit on any site is the default failure. The fix is not more effects; it is one
idea that belongs to this business, executed well, with everything around it kept quiet.

**The rule: every concept must answer "what does this show about the business?" in one sentence.
If the answer is "it looks nice", it is decoration, and it will be replaced within a week.**

## Failure modes this prevents

Each of these happened in one session, in this order, and each was rejected by the founder.

| Failure | What it looked like | The fix |
|---|---|---|
| Abstract motif, repeated | A dot field in the hero, then dots in icons, flows and patterns: *"We're overusing dots."* | One signature motif per site. Once it is chosen, nothing else on the page borrows its shape |
| The pointer takes over | Cursor added heat and the field froze under it | Motion runs on its own; the pointer only **distorts** (bend, displace, drag). *"Keep going, not stop when user mouses. User should just distort"* |
| Loud first | Saturated, banded, high-contrast | Start subtle: low opacity, few bands, capped peak colour. Turn it up only if asked |
| Pretty but irrelevant | A thermal field that said nothing about the work | Depict the business: the work moving (Convey's live product diagram), not a mood |
| Text hidden until JavaScript | Awwwards-style fade-ins parked at `opacity:0` | Motion starts from a readable state. Crawlers and screenshots must see all copy |
| Logos as decoration | A strip of integrations nobody had built | Only marks for things actually supported; gate the rest behind a feature flag |

## Workflow

### 1. Pin the brief (before looking at anything)

Write four lines: the business in one sentence, the page's single job, the one thing a visitor
should *feel*, and the existing brand constraints (palette, type, motion rules, banned devices).
Read the project's copy and brand rules first; creative direction does not override them.

### 2. Gather references by rendering, not by grepping

See `references/inspiration-sources.md` for which galleries answer, how, and which are walled.
Two rules from it:

- **Open the winning sites, not the gallery grid.** A grid of thumbnails shows composition, never
  motion. Take 6–10 sites from the gallery and render each one's hero, at rest and again after
  3 seconds.
- **Prefer references in the same business shape** (B2B, AI, infrastructure, services) over the
  most-awarded. An Awwwards portfolio site teaches craft, not positioning.

### 3. Name the patterns

Map each reference to a pattern in `references/hero-patterns.md`: what it says, what it costs,
and how it fails. Add new patterns there when a reference does something the file does not cover.

### 4. Propose 3–5 concepts, each grounded

For each concept give: a name, the one-sentence answer to "what does this show about the
business?", the pattern it adapts, how the pointer affects it, what it does under reduced motion,
and what it would cost to build. Lead with a recommendation. Mark which concept reuses an existing
brand motif and which introduces one.

### 5. Prototype the chosen concept in the real page

Build it in the page the user already reviews, not a separate demo. Then verify:

- **At rest:** a screenshot must already show the finished composition; no loader, no blank.
- **Motion:** screenshots cannot show it. Capture two frames seconds apart, and simulate the
  pointer by dispatching `PointerEvent`s along a path, then screenshot the result.
- **Phone:** render at 390px **with a viewport meta tag**; without it Chrome lays out at 980px and
  the screenshots are wrong.
- **Scripts parse** (`node --check` on the extracted scripts) and nothing else on the page changed.
- **Name collisions:** new class names must not match existing selectors (a `.split` helper once
  drew borders around every headline because a diagram already used `.split`).

State plainly what was verified by screenshot and what was not (the motion's feel always needs a
human in a browser).

## Rules

- **One signature moment per page.** Everything else stays quiet so it reads.
- **Relevance beats spectacle.** The best B2B heroes show the product or the work (live diagrams,
  real UI, the actual artefact), not abstract art.
- **Autonomous motion, distorting pointer.** Never let interaction stop, replace or dominate the
  animation.
- **Visible at rest, readable without JavaScript, still under `prefers-reduced-motion`, paused off
  screen.**
- **Draw it in code** (SVG, Canvas, CSS) when the idea allows; it stays sharp, on-brand and
  animatable. Generated raster art is a fallback, not a default.
- **Never recreate a reference's distinctive branded work.** Take the decision, not the artwork.

## Output

A short creative brief, the reference board (site, pattern, what to take, what to reject), the
concepts with a recommendation, and, once one is chosen, the prototype with a verified /
unverified list.
