# Persona and Command Reference

> **Purpose**: Where personas and commands live, how they are discovered, and when to invoke which persona or command so AI assistants and developers use the right capability at the right time.

---

## Overview

The Cursor Agentic Framework uses a **multi-persona AI workflow**. Personas are role-based skills; commands are explicit workflows. This document explains locations, discovery, and when-to-use guidance.

- **For AI assistants**: Use to reliably discover and reference personas and commands when the user's task matches their purpose.
- **For developers**: Use to understand how to place or name new personas/commands so they are discoverable.

---

## Where Personas and Commands Live

### Personas

| Path | Purpose |
|------|---------|
| `.cursor/skills/[persona]/SKILL.md` | Role-based instructions; first-class Cursor skills, auto-discovered when relevant |
| `.cursor/skills/examples/[persona]/SKILL.md` | Example specialist personas — templates for building your own domain-specific skills |

**Core personas** (`.cursor/skills/`):

- `product-manager` — Planning, user stories, acceptance criteria, backlog overlap
- `architect` — Design decisions, system boundaries, design-principles
- `dev-lead` — Task breakdown, estimation, coordination
- `backend-specialist` — Backend services, APIs, handlers, serverless functions, service integration
- `test-engineer` — Unit, integration, e2e tests
- `security-expert` — Security review, Tier 1/2 findings
- `orchestrator` — Workflow stuck, deadlock, meta-view of state

**Example personas** (`.cursor/skills/examples/` — templates; adapt for your domain):

- `graph-db-specialist` — RDF/OWL ontology, Neo4j schema, SHACL shapes, ODP, knowledge graph / graph RAG
- `app-builder-actions-developer` — Platform-specific backend actions, APIs, serverless (Adobe App Builder / I/O Runtime example)
- `app-builder-frontend-developer` — Platform-specific frontend, React, web-src, UI (Adobe App Builder / Unified Shell example)
- `app-builder-mcp-developer` — MCP server design and deployment on a specific platform (Adobe App Builder example)
- `ims-login` — OAuth/SSO login, token handling, identity provider integration (Adobe IMS example)

> See `.cursor/skills/examples/README.md` for how to adapt an example persona for your project.

### Commands

| Path | Purpose |
|------|---------|
| `.cursor/commands/*.md` | User-invoked workflows |

**Current commands** (organized by group):

**Plan group** (story planning lifecycle):
- `/plan-1` — PM discovery interview + write story-plan; chains to `/plan-2`
- `/plan-2` — Architect + Dev Lead + Security review story-plan
- `/plan-3` — Story complete: ratify, push to issue tracker, archive, branch/PR
- `/plan-debate` — Resolve persona disagreements at story level
- `/plan-resolve-tech-team-story-debate` — Facilitate tech team debate rounds until resolved
- `/plan-story-complete` — Complete story, push to issue tracker, archive story artifacts

**Dev group** (implementation lifecycle):
- `/dev-1` — Implementation plan kickoff; chains to `/dev-2` for first task
- `/dev-2` — Task kickoff; pause for human approval
- `/dev-3` — Task complete; chains to log-entry + index-update, then archives task folder
- `/dev-debate` — Specialist-level debate on impl plan
- `/dev-start-implementation-plan` — Write implementation plan; chains to first task plan
- `/dev-start-implementation-task-plan` — Write task-plan for a single task; pause for human approval
- `/dev-start-implementation` — Begin executing tasks (after plan approved)
- `/dev-start-implementation-task` — Start work on a specific sub-task
- `/dev-review-implementation-plan` — Trigger debate review of an implementation plan
- `/dev-implementation-plan-status` — Roll-up status report on implementation plan (no side effects)
- `/dev-completed-implementation-task` — Mark a task done; chains to log-entry + index-update, archives task folder
- `/dev-completed-implementation` — Close out story implementation; chains per task, then to `/plan-story-complete`

**Epic group** (epic lifecycle):
- `/epic-1` — Epic plan (discovery or import from issue tracker)
- `/epic-2` — Epic review + kickoff (create branch from `stage`)
- `/epic-3` — Epic complete: verify all stories done, PR to `stage`
- `/epic-debate` — Cross-story scope debate at epic level

**Log commands:**
- `/dev-write-impl-log-entry` — Add an impl-log entry for a completed task
- `/dev-update-impl-log-index` — Update the impl-log index in-place

**Support commands:**
- `/review-persona-skills` — Quality review of persona skills; can scope to one skill, open file, or changed skills

**Setup commands:**
- `/setup-dev-process-new` — Bootstrap a new project with full workflow structure (phased, interactive)
- `/setup-dev-process-existing` — Retrofit an existing project (inspection-driven, phased, interactive)

> **Internal commands** (called automatically by workflow commands, not invoked directly):
> `/_pre-flight-constitution-check` — Constitution guard run before all workflow commands

### Other Context

- **Constitution**: [docs/design-principles/](../design-principles/) — invariants, conventions, architecture
- **System state**: [docs/impl-log/](../impl-log/) — current state (index) + history (log)
- **Rules**: `.cursor/rules/` — workflow and code standards:
  - `multi-persona-workflow.mdc` — workflow constraints, story/task/epic flow
  - `[tech-stack-standards].mdc` — Add technology-specific rule files for your stack (e.g. `typescript-standards.mdc`, `python-standards.mdc`)

---

## How Discovery Works

1. **AGENTS.md**: `.cursor/AGENTS.md` is the lightweight map. It lists persona paths and commands so Cursor knows where to look. Personas load only what their role requires; do not load everything by default.

2. **First-class Cursor discovery**: Personas live in `.cursor/skills/`, the standard Cursor skill directory. Cursor auto-discovers and loads them when relevant.

3. **Description-based matching**: Persona **descriptions** in SKILL.md frontmatter drive relevance. Clear third-person descriptions with **WHAT** and **WHEN** improve discoverability.

4. **Explicit invocation**: Commands are invoked by the user (e.g. `/plan-1`, `/dev-3`, `/epic-1`). Personas may be invoked by name (e.g. `/architect`) or matched when the user's request aligns with the persona's description.

5. **Cursor behavior**: Skills in `.cursor/skills/` follow Cursor's [Agent Skills](https://cursor.com/docs/context/skills) standard. Rely on clear descriptions and the paths above for consistent behavior.

---

## When to Use Which Persona

| Scenario | Persona(s) |
|----------|------------|
| Planning new features, user stories, acceptance criteria | **product-manager** |
| Design decisions, system boundaries, conflicts with design-principles | **architect** |
| Task breakdown, estimation, coordination | **dev-lead** |
| Backend services, APIs, handlers, serverless, service integration | **backend-specialist** |
| Unit, integration, e2e tests | **test-engineer** |
| Security review, vulnerability assessment | **security-expert** |
| Workflow stuck, deadlock, need meta-view | **orchestrator** |
| RDF/OWL, Neo4j, SHACL, ODP, knowledge graph, graph RAG | **graph-db-specialist** (example) |
| Platform-specific backend actions, APIs, serverless | **app-builder-actions-developer** (example) |
| Platform-specific frontend, React, web-src, UI components | **app-builder-frontend-developer** (example) |
| MCP server design and deployment | **app-builder-mcp-developer** (example) |
| OAuth/SSO login, token handling, identity provider integration | **ims-login** (example) |

---

## When to Use Which Command

| Scenario | Command |
|----------|---------|
| New story from idea or issue tracker ticket | `/plan-1` |
| Review story-plan (Architect + Dev Lead + Security) | `/plan-2` |
| Story complete, push to issue tracker, archive | `/plan-3` or `/plan-story-complete` |
| Resolve persona disagreements at story level | `/plan-debate` |
| Facilitate tech team rounds of debate | `/plan-resolve-tech-team-story-debate` |
| Break story into implementation tasks | `/dev-1` or `/dev-start-implementation-plan` |
| Write or review a single task plan | `/dev-2` or `/dev-start-implementation-task-plan` |
| Begin executing tasks after plan approved | `/dev-start-implementation` |
| Start a specific sub-task | `/dev-start-implementation-task` |
| Check status of implementation plan | `/dev-implementation-plan-status` |
| Trigger debate review of impl plan | `/dev-review-implementation-plan` |
| Task done, reviewed and passing | `/dev-3` or `/dev-completed-implementation-task` |
| Close out all tasks and story implementation | `/dev-completed-implementation` |
| Specialist conflict on implementation plan | `/dev-debate` |
| New epic from idea or import from issue tracker | `/epic-1` |
| Review epic + create branch from stage | `/epic-2` |
| All epic stories done, PR to stage | `/epic-3` |
| Cross-story scope debate at epic level | `/epic-debate` |
| Add impl-log entry | `/dev-write-impl-log-entry` |
| Update impl-log index | `/dev-update-impl-log-index` |
| Quality review of persona skills | `/review-persona-skills` |
| Bootstrap new project | `/setup-dev-process-new` |
| Retrofit existing project | `/setup-dev-process-existing` |

---

## Leverage Patterns

These patterns describe **when in a workflow** to use personas or commands.

| Pattern | When | What to do |
|---------|------|------------|
| **Before marking task complete** | About to mark a task done (close issue, merge PR). | Run `/dev-3` or `/dev-completed-implementation-task`. Chains to `/dev-write-impl-log-entry` and `/dev-update-impl-log-index`. Consider AGENTS.md for learnings, patterns, gotchas — update if needed. |
| **Before marking story complete** | All tasks done, story ready. | Run `/plan-3` or `/plan-story-complete`. Pre-check: no open Tier 1 findings; Tier 2 have human Disposition. |
| **Before marking epic complete** | All stories done within epic. | Run `/epic-3`. Verifies all stories complete, creates PR to `stage`. |
| **Workflow deadlock** | Stuck or unclear next step. | Invoke **orchestrator**: load impl-log indexes and current artifact, assess state, recommend next command. |
| **Design conflict** | Changes conflict with design-principles. | Escalate to **architect**; conflicts go to design-principles or Debate Log. |
| **Security finding** | Tier 1 or Tier 2 security issue. | Tier 1 blocks; Tier 2 requires human Disposition (see rules). |
| **New story** | User has idea or issue tracker ticket. | Start with `/plan-1`. |
| **New epic** | User has epic-scope idea or issue tracker epic. | Start with `/epic-1`. |
| **New project** | Setting up multi-persona workflow from scratch. | Run `/setup-dev-process-new`. |
| **Porting workflow** | Adding multi-persona workflow to existing project. | Run `/setup-dev-process-existing`. |
| **Before/after editing skills** | Changing or adding a persona under `.cursor/skills/`. | Run `/review-persona-skills` (optionally scoped to the changed or open skill). |

---

## References

- [Persona skill authoring](persona-skill-authoring.md) — How to create or update persona SKILL.md and commands
- [Design principles](../design-principles/) — Constitution and invariants
- [Implementation log](../impl-log/) — Current state and history
- [Developer setup](README.md) — Quick start, MCP setup, process
- [AGENTS.md](../../.cursor/AGENTS.md) — Lightweight context map (project root)
- [Rules](../../.cursor/rules/) — multi-persona-workflow and technology-specific standards
