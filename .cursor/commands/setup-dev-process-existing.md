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
- Report: "Based on your dependencies, I believe your tech stack is: Frontend: [X], Backend: [Y], Database: [Z], Testing: [W]"

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

### Propose — Architecture and Domain Principles

- Draft `docs/design-principles/architecture.md` based on detected module structure, patterns, and boundaries
- Draft `docs/design-principles/db.md` based on detected database usage (e.g. "I see Neo4j driver in dependencies and these Cypher files; here is a proposed db.md")
- Draft `docs/design-principles/frontend.md` based on detected UI framework, component patterns
- Draft `docs/design-principles/backend.md` based on detected API patterns, service structure
- Present each to user: "Based on these files and patterns, here is what I propose. Correct anything."
- Adjust, confirm each before proceeding

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
