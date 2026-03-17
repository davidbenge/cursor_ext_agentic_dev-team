# Backend — [Your Backend Platform]

<!-- Replace "[Your Backend Platform]" with e.g. "Express / Node.js", "FastAPI", "App Builder Actions", "Lambda", etc. -->

## Platform

<!-- Describe the runtime and key platform constraints -->
[Runtime, e.g., Node.js 22 / Python 3.12]. [Key constraint, e.g., "Stateless. Config via environment variables."]. [Additional constraint].

## Service Structure

<!-- Define the standard shape of a backend service/handler/action -->
- [Structural rule 1 — e.g., "Export a `handler` function as the entry point"]
- [Structural rule 2 — e.g., "Return `{ statusCode, body, headers? }` — never throw"]
- [Structural rule 3 — e.g., "Use structured logging via the platform logger"]

## Timeouts

<!-- Define timeout tiers if applicable -->
- **[Tier 1]**: [Duration] — [Use cases]
- **[Tier 2]**: [Duration] — [Use cases]

## Error Handling

- [Error rule 1 — e.g., "Validate input; return 400 for bad params"]
- [Error rule 2 — e.g., "Catch all errors; return 500; never let uncaught exceptions escape"]

## Heavy Work

<!-- Define patterns for long-running or async work -->
- [Pattern 1 — e.g., "Queue heavy work via message broker"]
- [Pattern 2 — e.g., "Return 202 Accepted with a job ID"]
- [Pattern 3 — e.g., "Poll or use webhooks for completion"]

## Ownership

The [backend specialist persona name] is responsible for maintaining **docs/impl-log/backend/index.md** (the canonical backend implementation log and summary).

**API Documentation Policy:**
Whenever a new API endpoint is added, the corresponding API spec **must also be updated** to reflect the new endpoint. Keeping the API spec in sync with implemented endpoints is required for all additions and changes.
