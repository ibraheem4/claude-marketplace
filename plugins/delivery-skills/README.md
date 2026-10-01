# Delivery Skills

Executable agent workflows for tasks that go beyond code patterns — deployment, QA testing, dependency triage, and release management. Builds on the public [agent-skills](../agent-skills) repo for core engineering patterns.

## Skills

| Skill | Description |
|-------|-------------|
| **create-plan** | Design implementation plans (PRD → Architecture → Stories) |
| **qa** | Systematic QA testing with screenshots and interaction |
| **retro** | Generate engineering retrospective from git history |
| **npm-publish** | Version bump, build, publish, tag, and push |
| **triage-dependabot** | Triage Dependabot PRs: close superseded, merge safe, rebase stale |
| **reset** | Workspace reset: uncommitted and unpushed work, worktrees, submodules, GitHub Actions status per repo |
| **deploy** | Deploy through the repo's GitHub Actions workflow: confirm the trigger, promote or dispatch, hand off to deploy-status |
| **deploy-status** | GitHub Actions deploy runs: status, failed-step logs, watch, and confirm the target is serving |
| **sunset** | Decommission a service: stop its deploys, retire infra through IaC, archive the repo, clear references |

## Setup

```bash
for skill in skills/*/; do
  name=$(basename "$skill")
  ln -sf "$(pwd)/$skill" ~/.claude/skills/"$name"
done
```

## Governance

Two chains, each with separation of duties. Neither replaces the judgement at the end of it;
both exist so that judgement is made on evidence somebody else can re-check.

**Trust** — `trust-posture-review` → `evidence-investigation` → `remediation-review` →
`founder-brief`. Each step cites the one before it and is verified by someone other than its
author.

**Delivery** — `shape-work` → `repo-orient` → `implement-story` → `delivery-review` →
`release-readiness`. A request becomes a bounded work order, the repository is read before it is
changed, the change is made in an isolated worktree, reviewed read-only against the work order,
and only then judged fit to ship.

Both moved here from [agent-skills](../agent-skills) — they are
delivery operations, which is what this repo is for.
