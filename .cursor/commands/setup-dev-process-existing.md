# /setup-dev-process-existing — Retrofit Existing Project

**Invoked by**: Cursor User when porting the multi-persona AI development workflow to an existing project  
**Chains to**: /skill-review (auto, at end)

This command is **phased and interactive**, with heavy emphasis on **inspection first**. It scans the codebase before asking questions, so questions are informed by what it found. Each phase shows findings, asks for confirmation/correction, generates artifacts, and presents them for review before moving on.

This command is written to be **very verbose** — it explains what it is doing and why at each step, in plain language accessible to non-developer personas.

---

## Phase 1 — Codebase Inspection

**What this phase does:** Scans the existing project to understand what is already in place before asking any questions. This ensures questions are targeted and findings are grounded in reality.

### Scan — Directory Structure

- Scan top-level directory structure, file types, nesting patterns
- Report: "Here is your project structure: [tree summary]"

### Scan — Dependencies and Tech Stack

- Read package.json (or equivalent: requirements.txt, Cargo.toml, go.mod, etc.)
- Identify frameworks, libraries, database drivers, test frameworks, build tools
- For Adobe App Builder projects, specifically look for: `@adobe/aio-lib-state`, `@adobe/aio-lib-files`, `@adobe/aio-lib-db`, `@adobe/aio-lib-core-ims`, `@adobe/aio-sdk`, `@adobe/react-spectrum`
- Report: "Based on your dependencies, I believe your tech stack is: Frontend: [X], Backend: [Y], Storage: [Z — include App Builder State/Files/Database if detected], Testing: [W]"

### Scan — Source Code Patterns

- Look for API route definitions (Express routes, OpenWhisk actions, REST endpoints)
- Look for DB models, schemas, migration files
- Look for component directories, UI patterns
- Look for test files and test configuration
- Look for CI/CD configuration (CircleCI, GitHub Actions, etc.)
- Look for auth/security patterns (middleware, token handling, env vars)
- Report findings for each area

### Scan — Existing Cursor/AI Config

- Check for existing `.cursorrules`, `.cursor/rules/`, `.cursor/skills/`, `.cursor/AGENTS.md`
- Check for existing `docs/` structure
- Report: "I found these existing configurations: [list]"

### Ask

- Confirm or correct the tech stack assessment: "Is this accurate? Anything to add or change?"

### Archive

- Move any existing `.cursorrules`, `.cursor/rules/`, `.cursor/skills/` to `.cursor/archive/`
- Add a one-line annotation at the top of each archived file explaining what it was and why it is being replaced
- Report what was archived

---

## Phase 2 — Vision and Constitution from Existing Patterns

**What this phase does:** Proposes design-principles files based on patterns detected in the code, rather than asking from scratch.

### Scan

- Read README and any existing documentation
- Analyze code comments, module boundaries, naming conventions, import patterns
- Detect architectural patterns (monolith vs. microservices, layered vs. flat, etc.)

### Propose — Vision

- Draft `docs/design-principles/vision.md` based on what the project appears to do
- Present to user: "Based on what I see, here is a proposed vision for this project. Correct anything that is wrong or incomplete."
- Adjust, confirm before proceeding

### Detect App Builder Extension Points

**What this step does:** If the project appears to be an App Builder project (detected `@adobe/aio-sdk` dependency, `app.config.yaml`, or `src/*/ext.config.yaml`), scan `src/` for known extension point directory signatures and pre-select any matches. Skip this step entirely if the project is not an App Builder project.

Scan the `src/` directory for subdirectories matching any known extension point directory signature. Pre-select any that are found:

| Extension Point | XP Path | `src/` directory |
|---|---|---|
| Unified Shell / Firefly Experience Cloud Shell | `dx/excshell/1` | `src/dx-excshell-1/` |
| Asset Compute Worker | `dx/asset-compute/worker/1` | `src/dx-asset-compute-worker-1/` |
| AEM Content Fragment Console Extension | `aem/cf-console-admin/1` | `src/aem-cf-console-admin-1/` |
| Adobe Commerce UI extensions (admin panel) | `commerce/backend-ui/1` | `src/commerce-backend-ui-1/` |
| AEM Content Fragment Editor Extension | `aem/cf-editor/1` | `src/aem-cf-editor-1/` |
| Universal Editor Extension | `universal-editor/ui/1` | `src/universal-editor-ui-1/` |
| Workfront Document Details | `workfront/doc-details/1` | `src/workfront-doc-details-1/` |
| Workfront UI | `workfront/ui/1` | `src/workfront-ui-1/` |
| AEM Experience Success Studio | `aem/experience-success-studio/1` | `src/aem-experience-success-studio-1/` |
| AEM Assets Details View Extension | `aem/assets/details/1` | `src/aem-assets-details-1/` |
| AEM Assets Browse Extension | `aem/assets/browse/1` | `src/aem-assets-browse-1/` |
| AEM Assets Collections Extension | `aem/assets/collections/1` | `src/aem-assets-collections-1/` |
| GenStudio for Performance Marketing | `dx_genstudio/genstudiopem/1` | `src/dx_genstudio-genstudiopem-1/` |
| GenStudio Translation | `dx_genstudio/translation/1` | `src/dx_genstudio-translation-1/` |
| AEM Content Hub Asset Details | `aem/contenthub/assets/details/1` | `src/aem-contenthub-assets-details-1/` |
| AEM Launchpad Extension | `aem/launchpad/1` | `src/aem-launchpad-1/` |
| AEM Content Fragment Model Editor | `aem/cf-model-editor/1` | `src/aem-cf-model-editor-1/` |
| Adobe Commerce configuration (admin panel) | `commerce/configuration/1` | `src/commerce-configuration-1/` |
| Adobe Commerce extensibility (app management) | `commerce/extensibility/1` | `src/commerce-extensibility-1/` |

Report: "Based on your `src/` directory structure, I detected these extension points in use: [list]. Does this look right? Are there any you want to add or remove?"

**If `src/workfront-ui-1/` is detected (or selected), also scan `ExtensionRegistration.js` for registered hooks and pre-select accordingly, then confirm:**

- **Main Menu** (`mainMenu`) — detected if `mainMenu:` key is present in `ExtensionRegistration.js`
- **Secondary Navigation / Left Panel** (`secondaryNav`) — detected if `secondaryNav:` key is present; identify which object types (`PROJECT`, `TASK`, `ISSUE`, `PORTFOLIO`, `PROGRAM`) are registered
- **Forms Widget** (`widgets`) — detected if `widgets:` key is present

Report: "I detected these Workfront hooks in use: [list]. Does this look right?" Allow the user to correct. Record the confirmed hooks in `docs/design-principles/architecture.md`.

Once confirmed, the selected extension points are written into `docs/design-principles/architecture.md` under an **App Builder Extension Points** section. If multiple extension points are selected, include the note: "When running locally with multiple extension points, use `aio app run -e <dir-name>` to target a specific one."

### Propose — Architecture and Domain Principles

- Draft `docs/design-principles/architecture.md` based on detected module structure, patterns, and boundaries. If App Builder is in use, include the confirmed **App Builder Extension Points** section from the step above.
- Draft `docs/design-principles/db.md` based on detected database usage (e.g. "I see Neo4j driver in dependencies and these Cypher files; here is a proposed db.md")
- Draft `docs/design-principles/frontend.md` based on detected UI framework, component patterns
- Draft `docs/design-principles/backend.md` based on detected API patterns, service structure
- Present each to user: "Based on these files and patterns, here is what I propose. Correct anything."
- Adjust, confirm each before proceeding

### Select Backend MCP Domain Skills

**What this step does:** Populates the MCP Domain Skills checklist in `docs/design-principles/backend.md` so the `backend-specialist` persona knows exactly which domain skills to load.

First, based on the tech stack scan from Phase 1, pre-select any domains that are clearly present (e.g. if App Builder dependencies are detected, pre-check `app-builder-actions`; if Workfront API calls are detected, pre-check the relevant Workfront domains). Report: "Based on what I found in your codebase, I suggest these domains are already in use: [list]. Does this look right?"

Then present the user with two options for finalizing the selection:

**Option A — Checklist (confirm or adjust the detected selection)**

Show the full list with pre-checked items and ask the user to confirm, add, or remove:

- `app-builder-actions` — App Builder serverless actions and I/O Runtime
- `workfront-tasks-api` — Workfront task operations
- `workfront-issues-api` — Workfront issue/request handling
- `workfront-forms-api` — Workfront custom form fields
- `workfront-projects-api` — Workfront project lifecycle
- `workfront-events-api` — Workfront event subscriptions and webhooks
- `workfront-documents-api` — Workfront document management
- `workfront-approvals-api` — Workfront approval routing
- `workfront-extension` — Workfront UI extension backend hooks

**Option B — Describe your needs (I'm not sure)**

Ask: "Describe what your backend does — what external systems does it talk to, what operations does it perform, what events does it respond to?" Then recommend which domains apply, explain why each was chosen, and ask the user to confirm before committing.

Once selections are confirmed (via either path), update `docs/design-principles/backend.md`: change `- [ ]` to `- [x]` for each selected domain and remove unchecked items.

### Select Frontend MCP Domain Skills

**What this step does:** Populates the MCP Domain Skills checklist in `docs/design-principles/frontend.md` so the `frontend-specialist` persona knows exactly which domain skills to load. Skip this step if the project has no frontend.

First, based on the tech stack scan from Phase 1, pre-select any domains that are clearly present (e.g. if App Builder `web-src/` is detected or `@adobe/react-spectrum` is in dependencies, pre-check `app-builder-frontend`; if `workfront-ui-1` extension point patterns are detected, pre-check `workfront-extension`). Report: "Based on what I found in your codebase, I suggest these frontend domains are already in use: [list]. Does this look right?"

Then present the user with two options for finalizing the selection:

**Option A — Checklist (confirm or adjust the detected selection)**

Show the full list with pre-checked items and ask the user to confirm, add, or remove:

- `app-builder-frontend` — App Builder frontend apps (Unified Shell / dx-excshell-1, React Spectrum)
- `workfront-extension` — Workfront UI extensions (workfront-ui-1 extension point)

**Option B — Describe your needs (I'm not sure)**

Ask: "Describe your frontend — what UI surface does it run in, what component library does it use, and does it extend any Adobe product?" Then recommend which domains apply, explain why each was chosen, and ask the user to confirm before committing.

Once selections are confirmed (via either path), update `docs/design-principles/frontend.md`: change `- [ ]` to `- [x]` for each selected domain and remove unchecked items.

### Select DB / Storage MCP Domain Skills

**What this step does:** Populates the MCP Domain Skills checklist in `docs/design-principles/db.md` and records which storage services this project uses. Skip this step if the project has no persistent data layer.

First, based on the Phase 1 dependency and source code scan, identify storage patterns already in use:
- If `@adobe/aio-lib-state` is detected → pre-select **App Builder State**
- If `@adobe/aio-lib-files` is detected → pre-select **App Builder Files**
- If `@adobe/aio-lib-db` is detected → pre-select **App Builder Database**
- If Neo4j, Postgres, MongoDB, DynamoDB drivers are detected → pre-select **External database** and name it

Report: "Based on what I found in your codebase, I believe you are using these storage services: [list]. Does this look right?"

Explain the three Adobe App Builder storage services if any are in use:

| Service | Best for | Max size |
|---|---|---|
| **State** (`@adobe/aio-lib-state`) | Session data, caching, TTL-expiring config | 1 MB/value |
| **Files** (`@adobe/aio-lib-files`) | Images, documents, large payloads, shareable URLs | 200 GB |
| **Database** (`@adobe/aio-lib-db`) | Complex queries, relationships, aggregations | 16 MB/document |

Reference: [Adobe App Builder Storage Options](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/storage/)

Then present the user with two options for finalizing the selection:

**Option A — Checklist (confirm or adjust the detected selection)**

Ask the user to confirm, add, or remove:

- **App Builder State** — key-value, TTL support, best for sessions/cache/config under 1 MB
- **App Builder Files** — blob storage, shareable URLs, best for images/documents/exports
- **App Builder Database** — MongoDB-compatible document store, best for complex queries and relationships
- **External database** (Neo4j, PostgreSQL, MongoDB Atlas, DynamoDB, etc.) — specify which

**Option B — Describe your needs (I'm not sure)**

Ask: "Describe the data your project stores — what kind of data, how it's queried, and how large it gets." Then recommend which App Builder storage service(s) or external database applies, explain why, and ask the user to confirm before committing.

Once confirmed, update `docs/design-principles/db.md`:
- Fill in the Tech Stack section with the chosen storage services and region (inferred from codebase where possible)
- If App Builder storage is used, mark `app-builder-actions` as `- [x]` in the MCP Domain Skills checklist and remove unchecked items
- If only an external database is used, remove the MCP checklist items and note it in the Tech Stack section

---

## Phase 3 — Quality and Security Assessment

**What this phase does:** Proposes testing and security standards based on what actually exists in the codebase.

### Scan

- Analyze existing test files: unit tests, integration tests, e2e tests
- Read test configuration (jest.config, mocha, pytest, etc.)
- Check CI pipeline configs for test stages
- Identify security-related code: auth middleware, CSRF protection, input validation, env var usage, token handling

### Propose

- Draft `docs/design-principles/testing.md` based on existing test patterns: "I see you use Jest with this configuration, have [N] test files covering [these areas]. Here is a proposed testing standard."
- Draft `docs/design-principles/security.md` based on detected auth/security patterns: "I see IMS token handling here, CSRF middleware there. Here is a proposed security standard."
- Present each to user. Adjust, confirm before proceeding.

---

## Phase 4 — System Memory Seeding

**What this phase does:** Creates impl-log index files pre-filled with the current state of the codebase, so personas have accurate context from day one.

### Scan — Current State

- **DB**: Read migration files, model definitions, schema files, or query a running DB if accessible. Build a current schema inventory.
- **Backend**: Read route definitions, action manifests, API contracts. Build a current service/API surface map.
- **Frontend**: Read component directories, state management patterns, page/view structure. Build a component inventory.
- **Architecture**: Read module structure, dependency graph, integration points. Build a system topology map.
- **Security**: Read auth config, middleware chain, known exposure points. Build a threat surface assessment.
- **Test**: Read test file inventory, coverage config. Build a coverage state map.

### Generate

Create `docs/impl-log/` with all domain directories. Pre-fill each `index.md` from scan results:

- `architecture/index.md` — system topology from module scan
- `db/index.md` — schema inventory from model/migration scan
- `backend/index.md` — service map from route/action scan
- `frontend/index.md` — component inventory from component scan
- `test/index.md` — coverage state from test scan
- `security/index.md` — threat surface from security scan
- `security/risk-register.md` — empty table with headers
- All `log.md` files — empty, ready for entries
- `stories/` and `epics/` — empty archive directories

### Present

Show each index file to user: "Here is what I think your current DB schema looks like. Here is your current service map. Correct anything that is wrong."

Adjust, confirm each before proceeding.

---

## Phase 5 — Personas and Skills

**What this phase does:** Creates persona skill files, incorporating knowledge from the existing codebase and any archived configuration.

### Categorize Archived Config

For each item found in Phase 1 archive, classify it:

- **Rule** (unconditional invariant) → will go in `.cursor/rules/`
- **Skill** (domain expertise) → will be incorporated into the relevant persona's SKILL.md
- **Command** (workflow sequence) → will inform command file content
- **Design-principle** (project context) → already captured in Phase 2

Report classification to user: "I categorized your existing config as follows: [list]. Does this look right?"

### Ask

Present the full persona roster and let the user select which roles are needed (suggest based on tech stack):

- **product-manager** — Planning, user stories, acceptance criteria, backlog overlap
- **architect** — Design decisions, system boundaries, design-principles governance
- **dev-lead** — Task breakdown, estimation, coordination
- **graph-db-specialist** — RDF/OWL, Neo4j, SHACL, knowledge graph (suggested if Neo4j detected)
- **app-builder-actions-developer** — App Builder actions, OpenWhisk, APIs (suggested if App Builder detected)
- **app-builder-frontend-developer** — App Builder frontend, unified shell, React (suggested if App Builder frontend detected)
- **app-builder-mcp-developer** — MCP server design and deployment (suggested if MCP patterns detected)
- **test-engineer** — Unit, integration, e2e tests
- **security-expert** — Security review, vulnerability assessment, risk tiering
- **orchestrator** — Workflow stuck, deadlock, meta-view of state

### Generate

For each selected persona, create `.cursor/skills/[persona]/SKILL.md`:

- Seeded with references to the design-principles created in Phase 2-3
- Seeded with references to the impl-log indexes created in Phase 4
- Incorporating categorized items from archived config
- Technology-specific instructions based on detected tech stack

### Present

Show each skill to user for review. Adjust and confirm before proceeding.

---

## Phase 6 — Commands, Rules, and Workflow

**What this phase does:** Creates the command files, rule files, and developer-setup documentation.

### Ask

- Branching strategy: Detect from git history if possible (e.g. "I see branches merging into `stage`"). Ask user to confirm. Default: `stage` as integration branch.
- MCP servers to configure
- Any additional domain-specific code rules

### Generate

- `.cursor/commands/` — all numbered command files (plan-1/2/3, dev-1/2/3, epic-1/2/3, debates, log-entry, index-update, skill-review, and both setup commands)
- `.cursor/rules/multi-persona-workflow.mdc` — workflow invariants (incorporating any Rule items from Phase 5 categorization)
- Domain rules as needed
- `docs/developer-setup/developer-process.md` — full developer process documentation
- `docs/developer-setup/persona-and-command-reference.md` — persona/command reference
- `docs/developer-setup/README.md` — developer setup quick start

### Present

Show to user. Confirm.

### Validate

Run `/skill-review` on all created skills as a quality check.

### Complete

Report: "Setup complete. Here is what was created, what was archived, and your recommended first steps:

1. Review the design-principles files — they are your constitution.
2. Review the impl-log index files — they are your system memory.
3. Run `/plan-1` with your first story idea, or `/epic-1` with an epic."
