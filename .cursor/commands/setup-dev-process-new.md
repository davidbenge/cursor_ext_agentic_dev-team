# /setup-dev-process-new — Bootstrap New Project

**Invoked by**: Cursor User when setting up the multi-persona AI development workflow on a new project  
**Chains to**: /skill-review (auto, at end)

This command is **phased and interactive**. It does NOT ask all questions up front and generate everything at once. Instead, it works through one area at a time: ask targeted questions, generate artifacts for that area, present for review and adjustment, then move to the next area. Every phase pauses for the user.

This command is written to be **very verbose** — it explains what it is doing and why at each step, in plain language accessible to non-developer personas.

---

## Phase 1 — Project Identity

**What this phase does:** Establishes the basic identity of the project so all generated artifacts reference the correct names, URLs, and systems.

### Inspect

- Scan repo for existing package.json, README, .gitignore to understand the starting point
- Report what was found: "I see a package.json with name X, a README, and a .gitignore. Here is what I know so far."

### Ask

- Project name (human-readable)
- Repository URL (e.g. GitHub enterprise URL)
- Jira project key and component name (e.g. project {{JIRA_PREFIX}}, component "{{PROJECT_NAME}} Publishing")
- Team composition: Who will be using this system? (developers only, or also PMs, architects, etc.)

### Generate

- Top-level `README.md` stub (if one does not exist)
- `.cursor/AGENTS.md` — lightweight context map pointing to all locations

### Present

Show generated files to user. Adjust and confirm before proceeding.

---

## Phase 2 — Vision and Constitution

**What this phase does:** Creates the design-principles files — the "constitution" that every persona and command references. These are the non-negotiable standards for the project.

### Ask — Vision

- What problem does this project solve?
- Who is the user? Are there multiple user types?
- What does success look like? How would you measure it?
- What is this project explicitly NOT? (scope boundaries)

### Generate

- `docs/design-principles/vision.md` — filled from answers, not an empty template

### Present

Show vision.md to user. Adjust and confirm before proceeding.

### Ask — Technology Stack

- Frontend framework (e.g. React, Vue, none)
- Backend runtime / framework (e.g. Node.js, Python, App Builder / OpenWhisk)
- Data storage (e.g. App Builder State/Files/Database, Neo4j, PostgreSQL, MongoDB — see [Adobe storage options](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/storage/) if on App Builder)
- Deployment platform (e.g. Adobe I/O Runtime, AWS, Azure)
- UI component library (e.g. React Spectrum, Material UI, custom)

### Select App Builder Extension Points

**What this step does:** If the user selected Adobe I/O Runtime / App Builder as the deployment platform, ask which Adobe product surfaces this app extends. This determines the `src/` directory layout and is written into `docs/design-principles/architecture.md`. Skip this step entirely if the project is not an App Builder project.

Explain: "Each App Builder extension point gets its own directory under `src/`. The directory name is derived from the XP path (slashes become hyphens). You can extend multiple surfaces in one app."

Present the full list and ask the user to select all that apply:

| # | Extension Point | XP Path | `src/` directory |
|---|---|---|---|
| 1 | Unified Shell / Firefly Experience Cloud Shell (experience.adobe.com) | `dx/excshell/1` | `src/dx-excshell-1/` |
| 2 | Asset Compute Worker | `dx/asset-compute/worker/1` | `src/dx-asset-compute-worker-1/` |
| 3 | AEM Content Fragment Console Extension | `aem/cf-console-admin/1` | `src/aem-cf-console-admin-1/` |
| 4 | Adobe Commerce UI extensions (admin panel) | `commerce/backend-ui/1` | `src/commerce-backend-ui-1/` |
| 5 | AEM Content Fragment Editor Extension | `aem/cf-editor/1` | `src/aem-cf-editor-1/` |
| 6 | Universal Editor Extension | `universal-editor/ui/1` | `src/universal-editor-ui-1/` |
| 7 | Workfront Document Details | `workfront/doc-details/1` | `src/workfront-doc-details-1/` |
| 8 | Workfront UI | `workfront/ui/1` | `src/workfront-ui-1/` |
| 9 | AEM Experience Success Studio | `aem/experience-success-studio/1` | `src/aem-experience-success-studio-1/` |
| 10 | AEM Assets Details View Extension | `aem/assets/details/1` | `src/aem-assets-details-1/` |
| 11 | AEM Assets Browse Extension | `aem/assets/browse/1` | `src/aem-assets-browse-1/` |
| 12 | AEM Assets Collections Extension | `aem/assets/collections/1` | `src/aem-assets-collections-1/` |
| 13 | GenStudio for Performance Marketing | `dx_genstudio/genstudiopem/1` | `src/dx_genstudio-genstudiopem-1/` |
| 14 | GenStudio Translation | `dx_genstudio/translation/1` | `src/dx_genstudio-translation-1/` |
| 15 | AEM Content Hub Asset Details | `aem/contenthub/assets/details/1` | `src/aem-contenthub-assets-details-1/` |
| 16 | AEM Launchpad Extension | `aem/launchpad/1` | `src/aem-launchpad-1/` |
| 17 | AEM Content Fragment Model Editor | `aem/cf-model-editor/1` | `src/aem-cf-model-editor-1/` |
| 18 | Adobe Commerce configuration (admin panel) | `commerce/configuration/1` | `src/commerce-configuration-1/` |
| 19 | Adobe Commerce extensibility (app management) | `commerce/extensibility/1` | `src/commerce-extensibility-1/` |

Also ask: "Are you unsure or planning to add extension points later? If so, I'll leave a placeholder section."

**If the user selects Workfront UI (`workfront/ui/1`), ask a follow-up:**

"Workfront UI supports three extension hooks inside `ExtensionRegistration.js`. Which will this project use? (Select all that apply)"

- **Main Menu** (`mainMenu`) — adds items to the Workfront top navigation bar; surfaces via layout templates
- **Secondary Navigation / Left Panel** (`secondaryNav`) — adds left-panel items for specific object types; ask: "Which object types? (PROJECT, TASK, ISSUE, PORTFOLIO, PROGRAM)"
- **Forms Widget** (`widgets`) — embeds custom UI panels inside Workfront custom form fields

Record the confirmed hook selections in `docs/design-principles/architecture.md` alongside the extension point entry.

Once confirmed, the selected extension points will be written into `docs/design-principles/architecture.md` under an **App Builder Extension Points** section (see Generate step below).

### Generate

- `docs/design-principles/architecture.md` — system mandates, patterns, boundaries (seeded from tech stack). If App Builder is the platform, include an **App Builder Extension Points** section listing each selected extension point with its XP path, `src/` directory, and whether it has actions/frontend. If multiple extension points are selected, include the note: "When running locally with multiple extension points, use `aio app run -e <dir-name>` to target a specific one."
- `docs/design-principles/db.md` — data philosophy, naming conventions, schema rules (seeded from DB choice)
- `docs/design-principles/frontend.md` — UI principles, component philosophy (seeded from frontend/UI answers)
- `docs/design-principles/backend.md` — API design philosophy, service boundaries (seeded from backend answers)

### Select Backend MCP Domain Skills

**What this step does:** Populates the MCP Domain Skills checklist in `docs/design-principles/backend.md` so the `backend-specialist` persona knows exactly which domain skills to load.

Present the user with two options:

**Option A — Checklist (I know what I need)**

Show this list and ask the user to select all that apply:

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

Present the user with two options:

**Option A — Checklist (I know what I need)**

Show this list and ask the user to select all that apply:

- `app-builder-frontend` — App Builder frontend apps (Unified Shell / dx-excshell-1, React Spectrum)
- `workfront-extension` — Workfront UI extensions (workfront-ui-1 extension point)

**Option B — Describe your needs (I'm not sure)**

Ask: "Describe your frontend — what UI surface does it run in, what component library does it use, and does it extend any Adobe product?" Then recommend which domains apply, explain why each was chosen, and ask the user to confirm before committing.

Once selections are confirmed (via either path), update `docs/design-principles/frontend.md`: change `- [ ]` to `- [x]` for each selected domain and remove unchecked items.

### Select DB / Storage MCP Domain Skills

**What this step does:** Populates the MCP Domain Skills checklist in `docs/design-principles/db.md` and records which Adobe App Builder storage services (State, Files, Database) this project uses. Skip this step if the project has no persistent data layer.

First, explain the three Adobe App Builder storage services if the project is on App Builder:

| Service | Best for | Max size |
|---|---|---|
| **State** (`@adobe/aio-lib-state`) | Session data, caching, TTL-expiring config | 1 MB/value |
| **Files** (`@adobe/aio-lib-files`) | Images, documents, large payloads, shareable URLs | 200 GB |
| **Database** (`@adobe/aio-lib-db`) | Complex queries, relationships, aggregations | 16 MB/document |

Reference: [Adobe App Builder Storage Options](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/storage/)

Present the user with two options:

**Option A — Checklist (I know what I need)**

Ask the user to select all that apply:

- **App Builder State** — key-value, TTL support, best for sessions/cache/config under 1 MB
- **App Builder Files** — blob storage, shareable URLs, best for images/documents/exports
- **App Builder Database** — MongoDB-compatible document store, best for complex queries and relationships
- **External database** (Neo4j, PostgreSQL, MongoDB Atlas, DynamoDB, etc.) — specify which

**Option B — Describe your needs (I'm not sure)**

Ask: "Describe the data your project stores — what kind of data, how it's queried, and how large it gets." Then recommend which App Builder storage service(s) or external database applies, explain why, and ask the user to confirm before committing.

Once confirmed, update `docs/design-principles/db.md`:
- Fill in the Tech Stack section with the chosen storage services and region
- If App Builder storage is used, mark `app-builder-actions` as `- [x]` in the MCP Domain Skills checklist and remove unchecked items
- If only an external database is used, remove the MCP checklist items and note it in the Tech Stack section

### Present

Show each file to user one at a time. Adjust and confirm each before proceeding.

---

## Phase 3 — Quality and Security Standards

**What this phase does:** Defines the testing and security standards that the Test Engineer and Security Expert personas will enforce.

### Ask

- Testing philosophy: What level of coverage do you expect? (unit, integration, e2e)
- CI/CD platform (e.g. CircleCI, GitHub Actions, Jenkins)
- Security non-negotiables: Authentication standard (e.g. IMS/OAuth, JWT), data sensitivity level, compliance requirements
- Are there specific security patterns required? (e.g. CSRF protection, rate limiting, input validation)

### Generate

- `docs/design-principles/testing.md` — quality philosophy, coverage expectations, testing pyramid
- `docs/design-principles/security.md` — non-negotiables, risk tier definitions (Tier 1/2/3), auth standards

### Present

Show each file to user. Adjust and confirm before proceeding.

---

## Phase 4 — System Memory

**What this phase does:** Creates the impl-log structure — the "system memory" that personas use to understand the current state of the codebase. For a new project, these start mostly empty but with the correct structure and section headers so personas know where to write.

### Explain

Tell the user: "The impl-log is the system's memory. It has two types of files per domain: `index.md` (current state — updated in-place, never appended) and `log.md` (history — reverse-chronological narrative). Personas read index files to understand the system quickly. They write to log files when completing work."

### Generate

```
docs/impl-log/
  architecture/
    index.md        ← system brain / topology map (starter sections from architecture.md)
    log.md          ← empty, ready for entries
  db/
    index.md        ← current schema state (empty, structured with section headers)
    log.md
  backend/
    index.md        ← current service/API surface map (empty, structured)
    log.md
  frontend/
    index.md        ← component inventory (empty, structured)
    log.md
  test/
    index.md        ← test coverage state (empty, structured)
    log.md
  security/
    index.md        ← current threat surface map (empty, structured)
    log.md
    risk-register.md ← empty table with headers
  stories/          ← archived story artifacts (empty, will be populated)
  epics/            ← archived epic artifacts (empty, will be populated)
```

### Present

Show the structure to user. Confirm before proceeding.

---

## Phase 5 — Personas and Skills

**What this phase does:** Creates the persona skill files that define each AI specialist's expertise, boundaries, and procedures.

### Ask

Present the full persona roster and let the user select which roles are needed:

- **product-manager** — Planning, user stories, acceptance criteria, backlog overlap
- **architect** — Design decisions, system boundaries, design-principles governance
- **dev-lead** — Task breakdown, estimation, coordination
- **graph-db-specialist** — RDF/OWL, Neo4j, SHACL, knowledge graph (select if using graph DB)
- **app-builder-actions-developer** — App Builder actions, OpenWhisk, APIs (select if using App Builder)
- **app-builder-frontend-developer** — App Builder frontend, unified shell, React (select if using App Builder)
- **app-builder-mcp-developer** — MCP server design and deployment (select if building MCP servers)
- **test-engineer** — Unit, integration, e2e tests
- **security-expert** — Security review, vulnerability assessment, risk tiering
- **orchestrator** — Workflow stuck, deadlock, meta-view of state

### Generate

For each selected persona, create `.cursor/skills/[persona]/SKILL.md` seeded with:

- References to the design-principles files created in Phases 2-3
- References to the impl-log index files created in Phase 4
- Technology-specific instructions based on the tech stack answers
- Role boundary (does / does not)
- Output contract

### Present

Show each skill to user for review. Adjust and confirm before proceeding.

---

## Phase 6 — Commands, Rules, and Workflow

**What this phase does:** Creates the command files, rule files, and developer-setup documentation that orchestrate the workflow.

### Ask

- Branching strategy: What is your integration branch? (default: `stage`)
- MCP servers to configure (e.g. GitHub, Jira, Neo4j, Context7)
- Any domain-specific code rules? (e.g. TypeScript strict mode, specific UI framework rules)

### Generate

- `.cursor/commands/` — all numbered command files (plan-1/2/3, dev-1/2/3, epic-1/2/3, debates, log-entry, index-update, skill-review, and both setup commands)
- `.cursor/rules/multi-persona-workflow.mdc` — workflow invariants
- Domain rules as needed (e.g. `typescript-standards.mdc`, `react-spectrum-ui.mdc`)
- `docs/developer-setup/developer-process.md` — full developer process documentation
- `docs/developer-setup/persona-and-command-reference.md` — persona/command reference
- `docs/developer-setup/README.md` — developer setup quick start

### Present

Show to user. Confirm.

### Validate

Run `/skill-review` on all created skills as a quality check.

### Complete

Report: "Setup complete. Here is a summary of everything that was created. Your recommended first steps are: 1) Review the design-principles files. 2) Run `/plan-1` with your first story idea."
