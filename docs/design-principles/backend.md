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

## Tech Stack

<!-- Define the exact technologies this project's backend uses.
     The backend-specialist reads this to know which MCP domain skills to load and what patterns to follow. -->

- **Runtime**: [e.g., Node.js 22 on Adobe I/O Runtime / Python 3.12 on Lambda / Express on Cloud Run]
- **Primary framework**: [e.g., App Builder Actions (OpenWhisk) / Fastify / FastAPI]
- **External services**: [e.g., Workfront API, Adobe IMS, AEM Assets API]
- **Auth**: [e.g., Adobe IMS service-to-service / OAuth 2.0 / API key]
- **Data layer**: [e.g., Firestore / PostgreSQL / Redis — link to db.md for full conventions]

<!-- After filling in the stack above, update the MCP Domain Skills section below
     to reflect exactly which domains apply to this project. -->

## MCP Domain Skills

The `backend-specialist` persona **must** check the `adobe-extensibility-mcp` MCP server before starting any domain-specific task. Call `list_skills` to confirm available domains, then `load_skill` for each that applies to this project.

**Domains relevant to this project:**

> Not sure which domains you need? You can simply prompt the agent for guidance. Explain the type of backend work, or describe your use case, and the agent will recommend which MCP domain skills apply.

> During project setup, the relevant skills can be selected interactively via the setup commands.
> - When running setup, you will be given the option to either:
>   - Select the domains from a checklist, **or**
>   - Describe your backend needs and let the AI recommend the correct domain skills for you.

If you're unsure, just ask in natural language—AI will help you select the right MCP domain skills.



<!-- Check all that apply and remove the rest -->
- [ ] `app-builder-actions` — App Builder serverless actions and I/O Runtime
- [ ] `workfront-tasks-api` — Workfront task operations
- [ ] `workfront-issues-api` — Workfront issue/request handling
- [ ] `workfront-forms-api` — Workfront custom form fields
- [ ] `workfront-projects-api` — Workfront project lifecycle
- [ ] `workfront-events-api` — Workfront event subscriptions and webhooks
- [ ] `workfront-documents-api` — Workfront document management
- [ ] `workfront-approvals-api` — Workfront approval routing
- [ ] `workfront-extension` — Workfront UI extension backend hooks

Source: [github.com/davidbenge/adobe_extensibility_mcp — skills](https://github.com/davidbenge/adobe_extensibility_mcp/tree/stage/actions/skills-mcp/skills)

## Ownership

The `backend-specialist` persona is responsible for maintaining **docs/impl-log/backend/index.md** (the canonical backend implementation log and summary).

**API Documentation Policy:**
Whenever a new API endpoint is added, the corresponding API spec **must also be updated** to reflect the new endpoint. Keeping the API spec in sync with implemented endpoints is required for all additions and changes.
