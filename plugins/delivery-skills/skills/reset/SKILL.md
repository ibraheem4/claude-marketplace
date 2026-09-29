---
name: reset
description: "Reset all repos to a clean state: commit/push pending work, sync submodules, prune worktrees, sync skills, and verify everything is up to date. Use when starting a new session, switching context, or when things feel messy."
user_invocable: true
argument-hint: "[--dry-run]"
---

# Reset: Full Workspace Reset

Bring every repo, submodule, worktree, and skill symlink back to a clean, synced state. Run this at the start of a session, after a big feature lands, or when things feel out of sync.

## Arguments

- **--dry-run**: Report what would be done without taking action

## Step 1: Check for Uncommitted Work

For each repo and submodule in the workspace, check `git status`:

```bash
# From the root of the monorepo/workspace
git submodule foreach --recursive 'echo "=== $name ===" && git status --short'
```

- If a repo has uncommitted changes, **report them** — don't auto-commit
- If changes exist, ask the user: commit, stash, or skip?
- Never silently discard uncommitted work

## Step 2: Push Unpushed Commits

For each repo with committed but unpushed work:

```bash
git submodule foreach --recursive '
  branch=$(git branch --show-current)
  ahead=$(git rev-list --count origin/$branch..HEAD 2>/dev/null || echo 0)
  [ "$ahead" -gt 0 ] && echo "$name: $ahead unpushed commits on $branch"
'
```

Push each with explicit ref:
```bash
git push origin {branch}
```

## Step 3: Update Submodules

Fetch latest from tracked branches and update all submodules:

```bash
git fetch origin
git submodule update --init --recursive --remote
```

Check for submodules that are ahead of what the parent repo tracks (`+` in `git submodule status`). For each:
- If the submodule is on its tracked branch and has new commits, update the parent pointer
- Stage the submodule pointer update for a commit in Step 6

## Step 4: Prune Worktrees

For every repo (including submodules), list and clean up worktrees:

```bash
git submodule foreach --recursive 'git worktree prune 2>/dev/null'
git worktree prune
```

Also check for worktree directories that exist but aren't registered:
```bash
find . -path '*/.claude/worktrees/*' -maxdepth 5 -type d 2>/dev/null
```

Remove any orphaned worktree directories.

## Step 5: Sync Skills

If the skills sync script exists, run it:

```bash
if [ -x scripts/sync-skills.sh ]; then
  ./scripts/sync-skills.sh
elif [ -x ../acme-agent-company/scripts/sync-skills.sh ]; then
  ../acme-agent-company/scripts/sync-skills.sh
fi
```

This syncs:
- Public skills (agent-skills) → company package + ~/.claude/skills symlinks
- Private skills (delivery-skills) → company package + ~/.claude/skills symlinks
- Removes broken symlinks

## Step 6: Commit Submodule Pointer Updates

If submodule pointers changed in Step 3:

```bash
git add -A
git status --short
```

If there are staged submodule pointer changes, commit:
```
chore: update submodule pointers

Co-Authored-By: Claude <noreply@anthropic.com>
```

Push the parent repo.

## Step 7: Report

```
=== Workspace Reset Complete ===

Repos:        {N} checked, {N} clean, {N} had changes
Submodules:   {N} updated, {N} already current
Worktrees:    {N} pruned, {N} remaining
Skills:       {N} synced, {N} symlinks
Unpushed:     {N} repos pushed

All repos on tracked branches. Workspace is clean.
```

## Important Rules

- **Never discard uncommitted work** — always ask the user first
- **Never force-push** — regular push only
- **Report before acting** — show what will change, then do it
- **Submodule pushes use explicit refs** — bare `git push` in submodules goes to the tracked branch, which may not be what you want
- **Idempotent** — running reset twice should produce the same result
