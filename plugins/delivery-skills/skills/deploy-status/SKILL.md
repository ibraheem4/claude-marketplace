---
name: deploy-status
description: Use when checking a GitHub Actions deploy — is it running, why did it fail, show the failed step's log, watch a run to completion, or confirm the deployed service is actually serving the new version. Triggers on "did the deploy go through", "why did the deploy fail", "watch the deploy".
---

# Deploy Status

A deploy here is a GitHub Actions workflow run. This skill finds the run, reads why it failed, and
then checks the target, because a green run is not a serving release.

## Find the deploy workflow

Don't guess the name. Read the workflows as they are on the deploy branch, not in the working
tree. A checkout that is behind can be missing the workflow that deploys the thing in question.

```bash
git fetch -q origin
git ls-tree --name-only origin/main .github/workflows/
git show origin/main:.github/workflows/<file>.yml
```

For each match, note its `on:` triggers (branches, `paths:`), any job-level `if:` guard, and what
the deploy step runs. Those three decide whether a given push deploys anything.

## Recent runs

```bash
gh run list --workflow <file>.yml --limit 5
gh run list --workflow <file>.yml --branch main --limit 5
```

Report: run id, status/conclusion, branch, the commit's subject, and age. A `skipped` deploy job
is a result too. It usually means a `paths:` filter or an `if:` guard excluded the push, so say
which one rather than "nothing happened".

## Why it failed

```bash
gh run view <run-id> --log-failed
```

Read the failed step, not the whole log. Common shapes:

| Log says | Usually means |
|---|---|
| `npm ERR!` / `pnpm ERR!` / `ERR_PNPM_` | dependency or lockfile drift |
| `error TS` | type error the local run didn't hit (stale build cache, different Node) |
| `Unable to exchange token`, `missing id-token` | OIDC: `permissions: id-token: write` missing, or the provider/role trust doesn't name this repo or branch |
| `PERMISSION_DENIED` / `AccessDenied` on the deploy call | the deploy identity lacks a role. Read which one; don't widen it blindly |
| `Revision ... is not ready`, health check timeout | the build shipped and the new version won't start. Read the runtime logs, not the CI log |

## Watch a run

```bash
gh run watch <run-id> --exit-status
```

Report each job's transition and the final conclusion. On failure, go straight to `--log-failed`.

## Confirm the target

After success, check the thing that was deployed. Use whatever the workflow deploys to, for example:

```bash
# Cloud Run
gcloud run services describe <service> --region <region> \
  --format='value(status.conditions[0].status,status.latestReadyRevisionName)'
curl -fsS "$(gcloud run services describe <service> --region <region> --format='value(status.url)')/health"
```

Tie the serving version back to the run's commit where the service exposes it (an env var, a
`/version` route, an image tag). "Ready" alone can be the previous revision.

## Rules

- Read-only. This skill reports; re-running or dispatching a deploy belongs to `deploy`.
- Never re-run a failed deploy to "see if it passes" before reading why it failed.
