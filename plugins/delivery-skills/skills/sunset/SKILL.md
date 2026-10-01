---
name: sunset
description: "Decommission a service or repo: stop its GitHub Actions deploys first, retire its cloud resources through infrastructure-as-code, archive the repo, and clear every reference. Use when the user says 'sunset X', 'decommission X', 'retire X', or 'shut down X'."
argument-hint: "<service-or-repo> [--dry-run] [--skip-infra] [--skip-archive]"
---

# Sunset a Service or Repo

Order matters. Stop the deploys first, so a push mid-sunset can't bring the service back. Then
retire the infrastructure, then archive, then clean up. One service at a time.

**Arguments:** `<service-or-repo>`; `--dry-run` stops after the inventory; `--skip-infra`;
`--skip-archive`.

## 1. Inventory (read-only)

Find everything the service touches, then present it as a list before changing anything:

- **Deploys:** workflows under `.github/workflows/` that build or deploy it. Check its own repo
  *and* any monorepo that deploys it from a subdirectory: a `paths:` filter, a matrix entry, a
  `workflow_dispatch` option.
- **CI identities:** the deploy service account / role it uses via OIDC, and the trust binding
  for this repo.
- **Cloud resources:** grep the IaC repo for the service name and `module.<name>`. Look for compute
  (Cloud Run, ECS, functions), scheduled jobs, queues, secrets, domain mappings, DNS records, IAM
  bindings, and outputs that other modules read.
- **Code:** a submodule entry, a directory in a monorepo, `docker-compose*`, `Makefile` targets,
  `.mcp.json`, and the repo's instruction file (`CLAUDE.md` / `AGENTS.md`).
- **Callers:** other services whose env vars or config point at its URL.

`--dry-run` ends here.

## 2. Stop the deploys

- Own repo: `gh workflow disable <file>.yml`. It's reversible, and history stays.
- Monorepo: remove the service from the workflow's `paths:`, matrix and dispatch options, in the
  same PR that removes its directory.

Confirm with `gh workflow list` / a re-read of the workflow that nothing will deploy it now.

## 3. Retire the infrastructure (unless `--skip-infra`)

Through the IaC repo, on a branch, through its normal review. Not by deleting things by hand and
leaving the code behind.

- Remove or comment out every module and resource found in step 1, its outputs, and the entries
  in any shared lists (IAM member lists, IAP accessor lists, domain maps). Commented blocks get a
  `# Sunset <date>` line.
- Fix callers found in step 1 before applying, or the apply breaks them.
- Keep artifact/image registries. They're cheap and preserve history.
- Remove the CI deploy identity's trust binding for this repo once nothing else uses it.

**Cloud Run v2 with `deletion_protection = true`:** Terraform can't flip it off and destroy in
the same apply. It plans the destroy first, so the apply always fails. Instead:

1. Delete the service directly: `gcloud run services delete <name> --region <region> --quiet`.
2. `terraform state list | grep <name>`, then `terraform state rm '<address>'` for each service.
3. `terraform apply` then removes the rest cleanly.

Applying is the user's call. Hand them the exact commands rather than running a destroy yourself.

## 4. Archive the repo (unless `--skip-archive`)

`gh repo archive <owner>/<repo> --yes`. It becomes read-only, and history is kept and reversible.
**Never delete a repository.** If deletion is wanted, the owner does it in the web UI.

## 5. Clean up references

Remove the submodule (`git submodule deinit -f <path>`, `git rm -f <path>`, then
`rm -rf .git/modules/<path>`) or the directory. Then remove the `Makefile`, `docker-compose`,
`.mcp.json` and instruction-file references from step 1. Commit on a branch with a PR. Stage the
paths explicitly, not `git add -A`.

## 6. Summary

List what was done and what is left for the user, each with its exact command:

```
## Sunset: <name>
Done:      deploys disabled · IaC PR <link> · repo archived · references removed
For you:   delete Cloud Run services · terraform state rm … · terraform apply
           DNS records · third-party OAuth/webhook configs pointing at it
```

## Rules

- Confirm before every destructive step, even after a dry run.
- Deploys off before infrastructure goes, and never the other way round.
- Archive, never delete. Keep registries.
- Grep for `module.<name>` across every `.tf` file before applying.
