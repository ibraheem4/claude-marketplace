---
name: reset
description: "Bring a multi-repo workspace back to a known state: report uncommitted and unpushed work, prune worktrees, sync submodules, and check each repo's GitHub Actions status on its working and release branches. Use when starting a session, switching context, or when the user says 'reset' or things feel out of sync."
user_invocable: true
argument-hint: "[<workspace-dir>] [--dry-run]"
---

# Reset the Workspace

Report first, then act only on what is safe to do without asking. Uncommitted work is never
touched without the user's say-so.

## 1. Find the repos

The workspace is the directory given, or the current one. Its repos are every direct child with a
`.git`, plus submodules of any of them. Note each repo's working branch (`dev` if it has one,
otherwise the default) and its release branch (`main`).

## 2. Uncommitted and unpushed work

For each repo:

```bash
git -C <repo> status --short
git -C <repo> fetch -q origin
git -C <repo> rev-list --count @{u}..HEAD 2>/dev/null   # unpushed
git -C <repo> rev-list --count HEAD..@{u} 2>/dev/null   # behind
```

- Uncommitted changes: **report them, and ask** whether to commit, stash or leave them. Never
  discard, and never commit them on your own.
- Unpushed commits: list them and ask before pushing. Push with an explicit refspec,
  `git push origin <branch>:refs/heads/<branch>`, dry-run first. A bare push can target the
  upstream branch, which may be `main`.
- Behind: fast-forward only (`git pull --ff-only`) and only on a clean tree.

## 3. Worktrees

```bash
git -C <repo> worktree list
git -C <repo> worktree prune     # drops entries whose directory is already gone
```

A worktree directory that exists but isn't registered can still hold uncommitted work. Report it
with its `git status`. Don't remove it.

## 4. Submodules (only where `.gitmodules` exists)

```bash
git -C <repo> submodule update --init --recursive
git -C <repo> submodule status   # "+" = checked out at a different commit than recorded
```

Report pointer drift. Advancing a pointer is a commit in the parent repo, so ask first, stage
the submodule path explicitly, and push it with the parent.

## 5. GitHub Actions status

For each repo with a GitHub remote, the latest run per workflow on the working and release
branches:

```bash
gh run list -R <owner>/<repo> --branch <branch> --limit 10 \
  --json workflowName,conclusion,status,headSha,createdAt
```

Keep the newest run per workflow. Flag failures and runs still in progress. A red release branch
is the first thing to report. For a failing deploy, point at `deploy-status`.

## 6. Report

```
Workspace reset: <dir>
Repos:       N checked · N clean · N with uncommitted work (listed)
Unpushed:    N repos (listed, pushed only if approved)
Worktrees:   N pruned · N unregistered dirs (listed, kept)
Submodules:  N drifted (listed)
Actions:     N green · N failing (repo/branch/workflow) · N running
```

## Rules

- Never discard or auto-commit work. Never force-push.
- Stage explicit paths, never `git add -A`. Other sessions may share the checkout.
- Idempotent: a second run on a clean workspace changes nothing.
