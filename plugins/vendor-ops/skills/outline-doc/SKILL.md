---
name: outline-doc
description: Use when writing, updating, reviewing or filing documentation — a plan, runbook, handoff, decision doc or research write-up. The wiki is the system of record, not repo markdown. Covers collection choice, the patch calls that silently no-op, and what to do when a write is refused.
---

# Wiki docs

## Profile

Every workspace value here comes from a profile, never from this skill. Resolve one first:
`~/<workspace>/.claude/workspace.config.md` — the workspace the caller named, or the single
match of `~/*/.claude/workspace.config.md`. Several matches: ask which. None: say which keys
are needed and stop.
A key the profile leaves out means the workspace has none of that thing: say so, and skip
what depends on it — unless skipping leaves a write unguarded, then stop.

| Key | Used for |
|---|---|
| `{{wiki_tool}}` | which wiki is the system of record |
| `{{wiki_collections}}` | which collection a subject files under |
| `{{wiki_default_collection}}` | where to file when the subject is unclear |
| `{{exclude_orgs}}` | other orgs whose names must never reach a doc |

§"Updating" below records **Outline's** write behaviour. If `{{wiki_tool}}` is not Outline,
re-derive that section against the actual tool rather than trusting it.

## Overview

Documentation belongs in the wiki, not in markdown beside the code, because a repo copy
forks silently and nobody learns it has. The write calls are the other hazard: one returns a
bare authorization error, and one returns *success* having changed nothing.

## When to Use

- Writing or updating a plan, runbook, handoff, decision doc or research write-up
- Deciding where a document should be filed
- Reviewing a doc someone else wrote

## Core Process

### The rule

🔴 **All documentation goes in `{{wiki_tool}}`, not in markdown beside the code.** Repo
markdown forks silently: two copies of one runbook drifted within a day — the wiki copy
reached Appendix A.12 while the repo copy stopped at A.9, and the repo copy was untracked, so
it existed on exactly one machine.

Never offer "I'll put it in the repo too" as a convenience.

⚠️ **Sequencing.** When a repo copy is *ahead* of the wiki, deleting it first loses the
delta. Land the content in the wiki, confirm the write returned, then delete.

**Not documentation, leave in the repo:** `CLAUDE.md`, `CLAUDE.local.md`, `AGENTS.md` —
config read off disk by tooling, a wiki copy would never be read. **Ask first** about
`README.md`, `CONTRIBUTING.md`, `ONBOARDING.md` and local-dev runbooks, which are
conventionally repo-resident and linked relatively from other repo files.

### Filing

File by `{{wiki_collections}}`; when the subject is unclear, `{{wiki_default_collection}}`.

Tech Platform's own description disclaims broker work: *"Not here — see instead:
Broker-facing software → GTM – Broker ▸ Broker Platform"*. Four broker AWS migration docs
are currently misfiled there. Read the target collection's description before creating.

`move_document` **does work** — verified 2026-09-03, reparenting a runbook with
`parentDocumentId`. An earlier version of this file said it was unavailable and that a
misfile was a by-hand sidebar fix; that was wrong. Still read the target collection's
description before creating — a misfile is cheap to fix but it is read by people in between.

⚠️ The move response's `breadcrumb` echoes the **old** path. Confirm with a fresh `fetch`
on the collection, not the move's own output.

### Writing

- `create_document` **works** — just do it. Do not ask permission to create.
- Content must **not** start with an H1; the title is a separate field.
- @mentions: `@[Display Name](mention://user/<userId>)`, ids from `list_users`.
- Link the Linear tickets at the **top** of a plan doc, not buried.
- A plan doc states the plan. Corrections and history are not the focus — if a
  correction matters, give it a short §0 and move on.
- Decision docs: as short as the decision allows. Reviewers asked for this repeatedly.

### Updating — two distinct failure modes

⚠️ **`update_document` may return a bare `Authorization error`** while `fetch` on the same
document succeeds. Seen 2026-08-26 on the AWS Migration Execution Runbook; unclear whether
per-document or general.

🔴 **`editMode: "patch"` fails *silently* inside blockquotes** — returns success, bumps the
revision, changes nothing. The discriminator is the `>` blockquote, not the markdown:
headings, fenced code, links and table rows all match fine; plain prose inside a blockquote
does not.

So:

1. Anchor `findText` on a non-blockquote block — nearest heading, code block or table.
2. To insert a section, patch the **following** heading and re-emit it at the end of the
   replacement.
3. **Verify every patch by character count.** A no-op returns byte-identical content at a
   bumped revision, which is otherwise indistinguishable from success. The response echoes
   the whole document, which for a long doc exceeds the token cap and gets written to a file
   instead — so compare counts with python or jq against the previous call, not by eye.
4. If the update returns `Authorization error`, do not rework anchors. Hand back verbatim
   **FIND / REPLACE** pairs and say plainly that the document was not modified.

⚠️ Outline normalises the markdown it stores — `**` wrapped around a code span comes back
inverted. Cosmetic, renders fine, but it means text you wrote returns shaped differently, so
never reuse your own source as a later `findText`. Re-fetch first.

🔴 Never claim a wiki change before the write has returned successfully.

### Voice

Corrected repeatedly, so it is a rule and not a preference: **more succinct, way less
text.** Matter of fact.

- Lead with the answer. Context after, and only if it changes what the reader would do.
- Say which claims are **verified** and which are **inferred**, unprompted. When unsure,
  flag that specific line rather than hedging everything around it.
- A decision doc is as short as the decision allows.
- No preamble, no closing summary, no restating the brief back.
- Bullets over prose. One clause per bullet.

## Common Rationalizations

| Excuse | Why It's Wrong |
|---|---|
| "I'll put it in the repo too, for convenience" | Two copies drift within a day and only one has readers |
| "The patch returned success" | A no-op returns success and bumps the revision. Compare the character count |
| "I'll reuse the text I just wrote as the next anchor" | The wiki normalises what it stores; re-fetch first |
| "The write failed, I'll leave a markdown file instead" | That is the fork this skill exists to prevent |
| "It is close enough to the right collection" | Filing is how anyone finds it, and moving it may be a by-hand fix |

## Verification

- [ ] The document is in the wiki, and the write returned before it was described as done
- [ ] Collection chosen against the profile, and the target's own description was read
- [ ] Every patch verified by character count, not by eye
- [ ] Content does not start with an H1
- [ ] No org from the profile's exclusion list appears in the document

## Never

- Never fall back to writing a repo `.md` because the wiki write failed.
- Never name an org from `{{exclude_orgs}}` in anything that reaches a doc. Separate
  companies — filter any cross-workspace sweep before it lands.
