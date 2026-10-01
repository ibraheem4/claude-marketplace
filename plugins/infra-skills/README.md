# Infra Skills

Infrastructure operations for AI coding agents. Split out of
[agent-skills](../agent-skills), which keeps the engineering practice —
these are the ones that touch real infrastructure, where a mistake costs
something outside the repository.

## Cloud and identity

| Skill | Use when |
|-------|----------|
| `aws-infra` | Terraform, ECS, Route 53, ACM, IAM and OIDC deploy roles. Pin the AZ; delegate subdomains rather than repointing an apex |
| `gcp-resource-sweep` | Deleting cloud resources — a resource's name tells you nothing about whether it is used |
| `mail-authentication-records` | SPF, DKIM and DMARC as live production controls; a wrong record drops real mail silently |
| `google-workspace-sso-cutover` | Moving a tenant onto SSO without locking everyone out, including yourself |
| `oauth-social-providers` | Google or Microsoft sign-in for AuthKit — the secret shown exactly once, and three gates that reject sign-ins for unrelated reasons |
| `pr-preview-environments` | Per-PR ephemeral environments: the OIDC trust split, the isolated database, and teardown that reports success while orphaning billable resources |

Three references point at `agent-skills` skills and are written plugin-qualified, so they resolve
when both are installed and read as an external pointer when they are not.

## Install

```
/plugin marketplace add ibraheem4/claude-marketplace
/plugin install infra-skills@ibraheem4
```

## License

MIT. See [LICENSE](LICENSE).
