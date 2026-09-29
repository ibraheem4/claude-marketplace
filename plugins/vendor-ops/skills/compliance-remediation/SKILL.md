---
name: compliance-remediation
description: Use when auditing a compliance platform (Drata, Vanta, Secureframe) against the real cloud accounts, turning its failing tests into tickets and a plan, or writing the infra fix for one. Covers what the platform actually reads, why "merged" does not move a test, who can apply an org-level fix, and the audit readings that are most often wrong.
---

# Compliance remediation

## Profile

Every workspace value here comes from a profile, never from this skill. Resolve one first:
`~/<workspace>/.claude/workspace.config.md` — the workspace the caller named, or the single
match of `~/*/.claude/workspace.config.md`. Several matches: ask which. None: say which keys
are needed and stop.
A key the profile leaves out means the workspace has none of that thing: say so, and skip
what depends on it — unless skipping leaves a write unguarded, then stop.

| Key | Used for |
|---|---|
| `{{compliance_platform}}` | which platform, and how to reach it (browser session, API) |
| `{{compliance_frameworks}}` | the frameworks in scope; a required control is planned, not debated |
| `{{compliance_projects}}` | tracker projects remediation files into, and which team they accept |
| `{{compliance_default_assignee}}` | who a ticket goes to when the work is not yours |
| `{{compliance_plan_doc}}` | the living plan doc; update it, don't start a second one |
| `{{org_accounts}}` | every cloud account the platform monitors, prod and management included |
| `{{org_access}}` | what access you hold in each, and who holds the rest |
| `{{org_iac}}` | where org-level infra lives and who applies it |
| `{{env_promotion}}` | how an infra change reaches each environment |

## Overview

The platform reads **live cloud state**, not code. A test passes only when **every monitored
account** passes, prod and management included. So the question for every fix is not "is it
merged" but "who can apply it to every account, and when".

## When to use

- "Audit Drata", "why is this test failing", "what's blocking the audit"
- Turning failing tests into tickets or a remediation plan
- Writing Terraform (or other IaC) whose purpose is to make a control pass

## Core process

### 1. Audit — the platform, then the accounts

- Read `{{compliance_platform}}`'s failing **and erroring** tests separately. Errors are usually the
  platform being unable to read, not the control failing.
- 🔴 **Many tests erroring at once → check org policies first.** A region-deny SCP that
  doesn't exempt the platform's read-only role errors every test that scans other regions.
  One narrow exemption fixes all of them.
- Confirm every finding against the live account, in each of `{{org_accounts}}`, before writing it down. The usual
  misreadings: a standard enabled on some accounts read as enabled nowhere; a webhook
  subscription read as an alarm subscriber; a live system read as a leftover.
- Read the IaC too. A control can be **in code and unapplied** — drift, not a gap. A
  read-only `plan` against each env shows it.
- Parallel subagents per area (identity, logging, data, network, vendors) are worth it for a
  full back-to-front audit — ask first.

### 2. Known shapes

- **Org `auto_enable` reaches only accounts added after it was set.** Security Hub,
  Inspector and similar leave pre-existing accounts unsubscribed; add explicit member
  resources for them. Before adding standards subscriptions too, check whether the account has
  Security Hub at all (`describe-hub`). Where it's off, AWS's `CreateMembers` enables it *and*
  the default standards (FSBP and CIS 1.2), so a separate subscription resource is redundant.
  An account that already has Security Hub keeps its standards unchanged. Name the
  post-apply check (`get-enabled-standards`) in the PR.
- **Box-ticking controls still get done.** An IAM password policy with no IAM console users
  changes nobody's sign-in. Apply it, and say in a comment why it exists.
- **Management account counts.** Baseline modules usually cover members only; add the
  management account's copy by hand.
- Evidence screenshots: confirm the capture tool actually writes a file before building a
  report around it; some browser-automation screenshots never reach disk.

### 3. Map every fix to who can apply it

For each ticket, record: the repo, the environment path (`{{env_promotion}}`), and the
person who can apply it (`{{org_access}}`). Then rank **for the audit**, not for effort:

1. Fixes one org-level apply lands in every account (`{{org_iac}}`) — highest yield
2. Access blockers that gate every prod apply
3. Per-environment resource fixes, which need prod to move the test at all

A fix that only reaches staging moves nothing in the platform. Say so in the plan.

### 4. Tickets and plan

- File into `{{compliance_projects}}`, respecting the team each accepts.
- Not yours → `{{compliance_default_assignee}}`; name the person with access in the body.
- Required by `{{compliance_frameworks}}` → plan how, don't ask whether.
- Keep one plan in `{{compliance_plan_doc}}`: tickets at the top, the priority order, the
  access map, and a **verified vs inferred** section. Link every artifact from it.

### 5. The fix PR

- When you cannot `plan` (no access to the account that applies it), say so in the PR and
  write the exact apply steps for the person who can.
- Name the post-apply check that proves it didn't break something silently — e.g. an audit
  trail moved to a KMS key stops writing if the key policy is wrong; check its delivery
  status right after.
- Name anything the apply could collide with that you could not see (an existing delegated
  admin, a pre-set org config).

## Common rationalizations

| Excuse | Why it's wrong |
|---|---|
| "It's merged, the test will pass" | The platform reads live state; nothing moves until it is applied to every account |
| "Staging is fixed, that's progress" | For the audit, a test with prod failing is still failing |
| "The dashboard says X, so X" | First-pass readings are often wrong; confirm in the account |
| "No one uses passwords, skip the policy" | The auditor checks it anyway; apply it and note why |
| "Twenty failures, twenty tickets" | Check for one shared cause first — usually a policy |

## Verification

- [ ] Every finding in the plan was confirmed in the live account, or is marked inferred
- [ ] Each ticket names its environment path and who can apply it
- [ ] The priority order is by audit yield, prod included
- [ ] Every IaC PR states whether `plan` ran, and the post-apply check
- [ ] The plan doc was updated, not forked

## Never

- Never weaken an auth, permission or tenant-isolation control to make a test pass; exempt
  a read-only principal narrowly instead.
- Never mark a control remediated before the platform shows it passing.
- Never comment on or reassign tickets you did not create; draft the comment and hand it back.
