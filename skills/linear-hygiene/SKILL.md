---
name: linear-hygiene
description: Use before creating, reading, commenting on, closing or linking a Linear ticket, and when connecting tickets to a PR. Confirms the MCP is pointed at the right workspace and enforces the rule that you only touch tickets the profile's own user created.
---

# Linear hygiene

## Overview

Mostly a skill about restraint. The two failure modes are writing into the wrong workspace —
which looks completely normal while it happens — and touching records that belong to someone
else. Neither produces an error.

## Profile

Every workspace value here comes from a profile, never from this skill. Resolve one first:
`~/<workspace>/.claude/workspace.config.md` — the workspace the caller named, or the single
match of `~/*/.claude/workspace.config.md`. Several matches: ask which. None: say which keys
are needed and stop.

| Key | Used for |
|---|---|
| `{{tracker_workspace}}` | the workspace name every read and write must land in |
| `{{tracker_workspace_id}}` | confirming it by id, not by display name |
| `{{tracker_team}}` | the team tickets belong to |
| `{{tracker}}` | which tracker, and how it is reached |
| `{{ticket_prefix}}` | the ticket identifier prefix, e.g. `ABC-123` |
| `{{tracker_user}}` | whose tickets you may touch |
| `{{tracker_admin}}` | who owns the repo↔tracker integration |
| `{{exclude_orgs}}` | other orgs whose names must never reach a ticket |

## When to Use

- Before creating, reading, commenting on, closing or linking a ticket
- When connecting a ticket to a PR
- After any reconnection or reauth of the tracker

## Core Process

### 1. Confirm the workspace, every time

🔴 **Call `get_workspace` before any ticket read or write.**

The claude.ai Linear MCP does not stay pointed at one workspace. On 2026-08-26 it was
connected to one org in the morning and to a different one that afternoon after a `/mcp`
reauth — the same session, no warning. `{{ticket_prefix}}-###` exists in exactly one of
them.

The wrong connection looks completely normal: "make a ticket" lands silently in the wrong
org, and `get_issue("{{ticket_prefix}}-612")` returns `Could not find referenced Issue`,
which reads as a deleted ticket rather than a wrong connection. One real request was lost
this way and had to be re-filed a session later.

Confirm against `{{tracker_workspace_id}}`, not the display name — and that the team is
`{{tracker_team}}`. If it does not match,
**ask for a `/mcp` reauth** — do not file anyway.

### 2. Whose ticket is it

- **Only update tickets `{{tracker_user}}` created.** Never comment on anyone else's.
- If the right ticket does not exist, **create one** rather than repurposing someone's.
- **Never mark anything done unless certain.** When unsure, say what you found and leave
  the state alone.
- Scan for an existing ticket before creating a duplicate.

### 3. Three API gotchas

- `project` does **not** resolve a name containing `&`. A name like `Employer Facts & API
  Layer` fails silently — the issue is created with no project and no error. Pass the
  project **UUID**.
- `list_issues` `fields` has no `identifier`; the `{{ticket_prefix}}-###` comes back in `id`.
- Send real newlines in markdown content, not literal `\n`.

### 4. Linking to PRs

Use Linear's GitHub magic words deliberately, and note the repo↔tracker GitHub integration
may not be connected — if links are not appearing, that is the first thing to check, and it
is an admin action (`{{tracker_admin}}`), not something to work around.

⚠️ Do not write `Closes {{ticket_prefix}}-###` until the PR actually closes the ticket.
Reference it plainly otherwise.

## Common Rationalizations

| Excuse | Why It's Wrong |
|---|---|
| "It was the right workspace last session" | A reauth moves it, silently, mid-session |
| "Issue not found — it must have been deleted" | Far more often you are connected to the wrong org |
| "This ticket is basically about my work" | If someone else created it, it is theirs. File your own |
| "I'll mark it done, it looks finished" | Say what you found and leave the state alone |
| "The project name didn't resolve, I'll pass it as text" | It fails silently and creates the issue with no project. Pass the UUID |

## Verification

- [ ] `get_workspace` called and matched against the profile's id before any read or write
- [ ] Every ticket touched was created by the profile's own user
- [ ] No state marked done without certainty
- [ ] Scanned for an existing ticket before creating one
- [ ] No org from the profile's exclusion list appears anywhere in the ticket

## Never

- Never file a ticket without `get_workspace` first.
- Never comment on, reassign or close a ticket `{{tracker_user}}` did not create.
- Never name an org from `{{exclude_orgs}}` in a ticket. Separate companies — filter any
  cross-workspace sweep before anything reaches a doc or ticket.
- Never create, close or comment without being asked. Filing a ticket is outward-facing;
  approval once is not approval always.
  A profile `closeout` that pre-approves filing follow-ups at session close is a standing ask
  for exactly that: creating them, never closing or commenting on anything.
