# Delivery Skills

Codex guidance for project-specific executable agent workflows.

## Purpose

Reusable skills for deployment, QA, dependency triage, release management, planning, and other workflows that go beyond ordinary code patterns.

## Rules

- Keep each skill self-contained with clear trigger conditions and step-by-step workflow.
- Avoid provider-specific assumptions unless the skill is explicitly provider-specific.
- Keep commands and paths current with the target repos.
- Do not install or symlink skills globally unless explicitly asked.
- When updating a skill, preserve compatibility with agents that already reference it.
