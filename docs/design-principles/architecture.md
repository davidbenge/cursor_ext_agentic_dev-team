# Architecture — System Mandates

## Approved Patterns

<!-- List the approved architectural patterns and technologies for this project.
     Be explicit — personas use this list to make implementation decisions. -->
- **[Technology/Pattern 1]** — [What it is used for and any key constraints]
- **[Technology/Pattern 2]** — [What it is used for and any key constraints]
- **[Technology/Pattern 3]** — [What it is used for and any key constraints]

## Jira

<!-- Configure your project management system details -->
- **Project**: {{JIRA_PREFIX}}
- **Default component** (for new issues): {{PROJECT_NAME}}

## Banned Patterns

<!-- Explicitly forbidden approaches — prevents AI personas from suggesting them -->
- [Banned pattern 1] — [Reason why it is banned]
- [Banned pattern 2] — [Reason why it is banned]
- [Banned pattern 3] — [Reason why it is banned]

## Branching and Release

<!-- Define your branching model -->
- **No direct push to `main`.** All changes reach `main` via `stage`. Work is merged into `stage` first (feature branches → `stage`); after validation, `stage` is promoted to `main`. This keeps `main` stable and ensures a single, auditable path to production.

## ADR Format

Record architectural decisions as: Context, Decision, Rationale, Trade-offs, Reference.

## System State

<!-- Describe the maturity/phase of the system for AI personas to calibrate decisions -->
The system is currently in [POC / Alpha / Beta / Production] phase. [Any constraints this phase imposes — e.g., "Do not build migration tooling until Beta."]

## Scalability

<!-- Define concurrency, timeout, and scale constraints for the system -->
- [Constraint 1 — e.g., "API calls: 30s timeout"]
- [Constraint 2 — e.g., "Batch jobs: fan out to workers"]

## Scripts

All project scripts live under `scripts/` and follow the folder structure below.

### Folder Structure

| Directory | Purpose |
|-----------|---------|
| `scripts/setup/` | One-time or environment setup |
| `scripts/migrations/` | Schema/data migrations; run in order; prefer idempotent |
| `scripts/deploy/` | Deployment and infrastructure |
| `scripts/generate/` | Code or data generation |
| `scripts/verify/` | Verification, validation, sanity checks |
| `scripts/dev/` | Day-to-day dev utilities |
| `scripts/lib/` | Shared helpers used by multiple scripts |

### Best Practices

- **One concern per script** — No mega-scripts; split by purpose.
- **Document at top** — Purpose, required env vars, and usage example.
- **Migrations** — Prefer versioned or timestamped names; idempotent where possible; note rollback if applicable.
- **No secrets in scripts** — Use environment variables; document prerequisites in file header.
