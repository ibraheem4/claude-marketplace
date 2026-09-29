---
name: ui-content-design
description: Write or review the words inside a product interface — control labels, helper and description text, empty and error states, consent and permission prompts, confirmation dialogs, onboarding, tooltips, toasts. Use when adding a setting, toggle, checkbox or form field and deciding what text sits around it, when a screen has accumulated a paragraph under every feature, or when a section's copy needs a shape rather than a rewrite. Product UI, not marketing surfaces — for a landing page, pricing page, headline, CTA or email, use the marketing copywriting and copy-editing skills instead.
---

# UI content design

Marketing copy earns attention. Product copy spends it. A user reading a settings row has
already decided to be there, so every word is a tax on a decision they came to make — and the
job is to answer the one question they actually have, not to be thorough.

**The failure this skill exists to prevent is not verbose copy. It is copy with no shape** —
every feature explained freshly by whoever shipped it, so a screen reads as a stack of
unrelated authors.

## The section contract

A group of related controls produces these parts, in this order. Nothing else.

```
Section heading

One guarantee line            ← what is true of EVERY control here. Stated once.

[control]  Label              ← verb + object, sentence case, no period
           Difference line    ← what makes THIS control unlike its siblings. Optional.

One link                      ← names what it shows, not where it goes
```

**The load-bearing rule: a sentence true of every control belongs to the group, once.** Copy
bloat is almost never one bad sentence — it is a group-level fact copied into each row until
the section reads as protesting too much.

To apply it, draft every control's description, then read the descriptions as a column. Any
clause appearing more than once is the group's, not the control's. What remains in each row is
the difference — and if nothing remains, the row needs no description at all.

| Part | Carries | Never carries |
|---|---|---|
| Guarantee line | The shared promise, the recipient, the default | A benefit pitch |
| Label | The action and its object | The consequence, the default, a caveat |
| Difference line | Only what distinguishes this control from its siblings | Anything repeated in a sibling |
| Link | The specifics, and a promise of what it shows | "Learn more" |

A control's own state and location are not content. An unchecked box already says it is off;
a user in Settings already knows where Settings is.

## Claims are not copy

Any string asserting what the system actually does is a claim: *anonymous*, *encrypted*,
*not linked to your account*, *deleted after 30 days*, *removed before it leaves your device*.

Write it in the draft as `[CLAIM — needs <owner>]` and leave the marker in. It comes out when
the owner confirms it, not when the draft ships.

**Prefer the checkable mechanism over the reassuring adjective.** "Labeled with a random ID for
this install" survives scrutiny; "anonymous" is a term of art most telemetry pipelines cannot
honor, and a privacy claim that turns out to be false costs more than the feature earned.

| Rationalization | Reality |
|---|---|
| "I flagged it in my summary" | The flag lives in chat. The string ships. Mark it in the draft. |
| "Legal reviews this anyway" | Legal reviews the policy, not a toggle's helper text. |
| "It's obviously anonymous" | Then naming the mechanism costs you one clause. |
| "It's a placeholder, I'll fix it" | Unmarked placeholders ship. `[CLAIM — needs …]` does not. |

## Register: when a marketing copy skill fires on product UI

`marketing-skills:copy-editing` triggers on "review my copy" and will fire on a product screen.
Its seven sweeps do not all transfer.

| Sweep | On product UI |
|---|---|
| Clarity, Specificity, word-level | Apply |
| Prove It, Heightened Emotion, Zero Risk | **Do not apply** |

Those three are conversion moves. On a consent control, a permission prompt or a destructive
confirmation, copy that sells the yes is a dark pattern — and a user who was persuaded into an
opt-in is a user who revokes it, loudly, later. Take the sweeps that sharpen; leave the ones
that sell.

## Growing this skill

This skill converges on a shape; it does not know your product's shape yet. Every copy decision
that took real argument to settle appends one row to [cases.md](references/cases.md) — the
before, the after, and the one sentence of why. Append; do not rewrite this file. Reference
files grow safely, skill bodies do not.

A case earns its row when the reasoning was not obvious in advance. A case that merely applies
the section contract above is already covered.
