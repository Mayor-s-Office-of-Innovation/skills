# Agent Instructions

## Orientation — read order

New here? Get your bearings in this order:

1. **This file (AGENTS.md)** — standing choices, SDLC rules, and current state (below).
2. **[docs/README.md](docs/README.md)** — the reference-docs map: architecture, data model,
   ADRs, runbooks. Go here to find the authoritative doc for the area you're in.

## Agent Rules

- **Never push or commit code.** The human pushes and commits code changes themselves; agents stop after code changes and say what's ready to review.

## SDLC Rules

- Keep application code, infrastructure, CI/CD, docs, and configuration in Git.
- Do not add secrets to source control. Use AWS Secrets Manager or SSM Parameter Store references.
- Terraform state must use a remote backend with S3 versioning and DynamoDB locking.
- Terraform plan/apply runs in CI, not from developer laptops.
- Prefer managed services and least-privilege IAM.
- Public-facing web surfaces must use TLS 1.2 minimum, HSTS, CSP, secure headers, CAA records, CloudFront, WAF, and rate limits.

## Skills

Project-local skills are vendored under `.claude/skills` from:

- `Mayor-s-Office-of-Innovation/skills/dashboard-review`
- `Mayor-s-Office-of-Innovation/skills/web-dev`

Use the `web-dev` skill for frontend work. 

Use the `dashboard-review` skill only after dashboards or data visualizations exist and need source, denominator, tone, and clarity review.
