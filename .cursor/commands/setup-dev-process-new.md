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
- Database (e.g. Neo4j, PostgreSQL, MongoDB)
- Deployment platform (e.g. Adobe I/O Runtime, AWS, Azure)
- UI component library (e.g. React Spectrum, Material UI, custom)

### Generate

- `docs/design-principles/architecture.md` — system mandates, patterns, boundaries (seeded from tech stack)
- `docs/design-principles/db.md` — data philosophy, naming conventions, schema rules (seeded from DB choice)
- `docs/design-principles/frontend.md` — UI principles, component philosophy (seeded from frontend/UI answers)
- `docs/design-principles/backend.md` — API design philosophy, service boundaries (seeded from backend answers)

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
