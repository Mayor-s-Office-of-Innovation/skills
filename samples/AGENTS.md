# Agent Instructions

## Orientation — read order

New here? Get your bearings in this order:

1. **This file (AGENTS.md)** — standing choices, SDLC rules, and current state (below).
2. **[docs/README.md](docs/README.md)** — the reference-docs map: architecture, data model,
   ADRs, runbooks. Go here to find the authoritative doc for the area you're in.

## Agent Rules

- **Never push or commit code.** The human pushes and commits code changes themselves; agents stop after code changes and say what's ready to review.
- **Before building anything that writes data** (save, edit, delete, import, sync, migration), run the brief in the `write-path-review` skill and wait for the owner's confirmation.
- **Before building anything the outside world can reach** (page, route, endpoint, form, upload, login/role change, new personal data), run the brief in the `exposure-review` skill and wait for the owner's confirmation.
- **Before a PR is opened**, run the review mode of every skill above that applies, write the findings doc, and put the routing label in the PR description. A PR labeled `NEEDS TECHNICAL REVIEWER` is not merged on owner sign-off alone.

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
- `Mayor-s-Office-of-Innovation/skills/exposure-review`
- `Mayor-s-Office-of-Innovation/skills/web-dev`
- `Mayor-s-Office-of-Innovation/skills/write-path-review`

Use the `web-dev` skill for frontend work.

Use the `dashboard-review` skill only after dashboards or data visualizations exist and need source, denominator, tone, and clarity review.

Use the `write-path-review` skill whenever a change creates, updates, deletes, imports, syncs, or migrates data. Use the `exposure-review` skill whenever a change adds or alters something the outside world can reach. Both run twice: a short brief before building, and a review before the PR (see Agent Rules).
