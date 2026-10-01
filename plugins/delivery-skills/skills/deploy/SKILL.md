---
name: deploy
description: "Deploy a service through its GitHub Actions workflow: confirm the change will actually trigger the deploy, promote or dispatch it, then hand off to deploy-status. Use when the user says 'deploy', 'ship to production', 'redeploy', or 'release to staging'."
disable-model-invocation: true
argument-hint: "<service> [<branch>]"
---

# Deploy a Service

Deploys run in GitHub Actions from a clean checkout. This skill gets a change to the workflow
that deploys it and confirms the workflow will act on it. It does not deploy from the laptop.

## 1. Read the workflow before touching git

Find the workflow that deploys `<service>` on the deploy branch (`git show origin/<branch>:…`,
not the working tree; see `deploy-status`, "Find the deploy workflow"), then read four things
off it:

- **Trigger:** which branches, and which `paths:`. A change outside `paths:` deploys nothing.
- **Guard:** a job-level `if:` such as `github.ref == 'refs/heads/main'`. A `workflow_dispatch`
  can be offered on every branch and still deploy only from one.
- **Selection:** how it picks what to deploy (a changed-files diff, a matrix, a `service` input).
- **Concurrency:** queued or cancelled. A cancelled source deploy can leave a build running with
  nobody watching it.

Say what the workflow will do with this change before doing anything.

## 2. Pre-flight

- The change is committed and pushed. Run `git status` and `git log origin/<branch>..HEAD`.
- It was checked where the repo checks things first (a preview branch, CI on the PR). A deploy is
  not a test loop.
- The repo's own verification passes locally: whatever its CI runs (`npm test`, `make check`, …).

## 3. Get it deployed

**Normal path: promotion.** The deploy fires when the change reaches the deploy branch. Follow the
repo's way of getting there: merge the PR, or promote `dev` to `main`. For production, confirm
with the user first: "This deploys `<service>` to production. Go?"

**Redeploy without a code change:**

```bash
gh workflow run <file>.yml --ref <deploy-branch> -f service=<service>
gh run list --workflow <file>.yml --limit 1   # pick up the new run id
```

Use the input names the workflow declares. Check `workflow_dispatch.inputs`; don't assume
`service`.

**Push with an explicit refspec.** `git push origin <branch>:refs/heads/<branch>`, dry-run first.
A branch whose upstream is `origin/main` can otherwise push to `main` under `push.default = upstream`.

## 4. Watch it

Hand off to `deploy-status`: watch the run, then confirm the target serves the new version.

## Hand deploy (fallback only)

Only when the workflow can't run (an outage, a broken workflow being fixed) and the user asked for it:

- Build from a clean export, never the working checkout, which can carry another session's
  uncommitted edits: `git archive origin/<deploy-branch> | tar -x -C "$(mktemp -d)"`.
- Run the same deploy command the workflow runs, with the same flags. Read it from the workflow
  file rather than retyping it.
- Say in the result that it was a hand deploy, and why.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Push landed, no run started | change outside `paths:`, or branch not in `on.push.branches` | read the trigger; dispatch if a redeploy is intended |
| Run started, deploy job `skipped` | job-level `if:` guard (often branch) or an empty selection | dispatch with `--ref` set to the deploy branch, or the explicit service input |
| `Unable to exchange token` | OIDC trust doesn't cover this repo/branch, or `id-token: write` missing | fix the trust/permissions in IaC; don't swap to a long-lived key |
| Deploy step denied | the deploy identity lacks a role | name the missing permission; grant the narrowest role in IaC |
