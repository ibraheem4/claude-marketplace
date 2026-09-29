---
name: triage-dependabot
description: "Triage open Dependabot PRs: close superseded/breaking, merge safe bumps, rebase stale ones, and report status. Use when user says 'check dependabot', 'triage deps', 'merge security updates'."
user_invocable: true
argument-hint: "[repo-name] [--dry-run]"
---

# Triage Dependabot PRs

Automatically triage open Dependabot PRs: close superseded ones, merge safe bumps, rebase stale ones, and produce a summary report.

## Arguments

Parse `$ARGUMENTS`:
- **repo-name** (optional): specific repo to check (e.g., `my-app`). Default: check all repos in the org.
- **--dry-run**: report what would be done without taking action

## Step 1: Gather Open Dependabot PRs

For each repo:

```bash
gh pr list --repo {owner}/{repo} --author "dependabot[bot]" --state open \
  --json number,title,mergeable,createdAt,headRefName,statusCheckRollup,labels \
  --jq '.[] | {number, title, mergeable, created: .createdAt, branch: .headRefName, labels: [.labels[].name], checks: [.statusCheckRollup[]? | {name: .name, conclusion: .conclusion}]}'
```

If no open PRs, skip that repo.

## Step 2: Categorize Each PR

For each PR, determine the action:

| Category | Criteria | Action |
|----------|----------|--------|
| **Superseded** | Another open PR bumps the same package to a higher version | Close with comment |
| **Safe bump** | Patch version, CI passing, no breaking changes | Approve + merge |
| **Minor bump** | Minor version, CI passing | Review changelog, merge if safe |
| **Major bump** | Major version | Flag for human review — don't auto-merge |
| **CI failing** | Any version, CI red | Attempt rebase; if still failing, flag for human |
| **Stale** | Open > 30 days | Rebase; if conflicts, close and let Dependabot recreate |

## Step 3: Execute Actions

### Close superseded PRs
```bash
gh pr close {number} --repo {owner}/{repo} \
  --comment "Superseded by #{newer_pr} which bumps to a higher version."
gh api repos/{owner}/{repo}/git/refs/heads/{branch} -X DELETE
```

### Merge safe bumps
```bash
gh pr review {number} --repo {owner}/{repo} --approve
gh pr merge {number} --repo {owner}/{repo} --squash --delete-branch
```

### Rebase stale PRs
```bash
gh pr comment {number} --repo {owner}/{repo} --body "@dependabot rebase"
```

### Flag for human review
Don't merge. Add a comment explaining why it needs human attention.

## Step 4: Report

```markdown
## Dependabot Triage Report

| Repo | Action | PR | Package | Version |
|------|--------|-----|---------|---------|
| my-app | Merged | #42 | lodash | 4.17.21 → 4.17.22 |
| my-app | Closed (superseded) | #40 | lodash | 4.17.21 → 4.17.21 |
| my-api | Flagged (major) | #15 | express | 4.x → 5.x |

**Summary**: {N} merged, {N} closed, {N} flagged for review, {N} rebased
```

## Important Rules

- **Never auto-merge major version bumps.** Flag for human review.
- **Always check CI before merging.** Red CI = don't merge.
- **Close superseded PRs** to reduce noise — Dependabot will recreate if needed.
- **Rebase before closing stale PRs** — give them one chance to update.
- **In dry-run mode**, report all actions but execute none.
