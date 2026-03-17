# Persona and Command Reference

> **Purpose**: Where personas and commands live, how they are discovered, and when to invoke which persona or command so AI assistants and developers use the right capability at the right time.

---

## Overview

{{PROJECT_NAME}} Publishing uses a **multi-persona AI workflow**. Personas are role-based skills; commands are explicit workflows. This document explains locations, discovery, and when-to-use guidance.

- **For AI assistants**: Use to reliably discover and reference personas and commands when the user's task matches their purpose.
- **For developers**: Use to understand how to place or name new personas/commands so they are discoverable.

---

## Where Personas and Commands Live

### Personas

| Path | Purpose |
|------|---------|
| `.cursor/skills/[persona]/SKILL.md` | Role-based instructions; first-class Cursor skills, auto-discovered when relevant |

**Current personas**:

- `product-manager` — Planning, user stories, acceptance criteria, backlog overlap
- `architect` — Design decisions, system boundaries, design-principles
- `dev-lead` — Task breakdown, estimation, coordination
- `graph-db-specialist` — RDF/OWL ontology, Neo4j schema, SHACL shapes, ODP, knowledge graph / graph RAG; impl-log db index; `data/RDF/` (ontology, phases, activities)
- `app-builder-actions-developer` — App Builder actions (OpenWhisk), APIs, serverless, {{APP_EXTENSION_POINT}}/actions
- `app-builder-frontend-developer` — App Builder frontend (unified shell), React, web-src, UI
- `app-builder-mcp-developer` — MCP server design/deployment on App Builder, MCP tools/resources
- `test-engineer` — Unit, integration, e2e tests
- `security-expert` — Security review, Tier 1/2 findings
- `orchestrator` — Workflow stuck, deadlock, meta-view of state

### Commands

| Path | Purpose |
|------|---------|
| `.cursor/commands/*.md` | User-invoked workflows (plan-1, dev-1, epic-1, etc.) |

**Current commands** (organized by numbered groups):

**Plan group** (story planning lifecycle):
- `/plan-1` — PM discovery interview + write story-plan
- `/plan-2` — Architect + Dev Lead + Security review story-plan
- `/plan-3` — Ratify, push to Jira, archive, branch/PR to `stage`
- `/plan-debate` — Resolve persona disagreements at story level

**Dev group** (implementation lifecycle):
- `/dev-1` — Break story into tasks, write impl plan
- `/dev-2` — Specialists write task-plan sections
- `/dev-3` — Task complete + story-complete at impl level
- `/dev-debate` — Specialist-level debate on impl plan

**Epic group** (epic lifecycle):
- `/epic-1` — Epic plan (discovery or import from Jira)
- `/epic-2` — Epic review + kickoff (create branch from `stage`)
- `/epic-3` — Epic complete (verify all stories done, PR to `stage`)
- `/epic-debate` — Cross-story scope debate at epic level

**Support commands:**
- `/log-entry` — Add impl-log entry
- `/index-update` — Update impl-log index in-place
- `/skill-review` — Quality review of persona skills; can scope to one skill, open file, or changed skills

**Setup commands:**
- `/setup-dev-process-new` — Bootstrap new project with full workflow structure (phased, interactive)
- `/setup-dev-process-existing` — Retrofit existing project (inspection-driven, phased, interactive)

### Other Context

- **Constitution**: [docs/design-principles/](../design-principles/) — invariants, conventions, architecture
- **System state**: [docs/impl-log/](../impl-log/) — current state (index) + history (log)
- **Rules**: `.cursor/rules/` — workflow and code standards:
  - `multi-persona-workflow.mdc` — workflow constraints, story/task/epic flow
  - `typescript-standards.mdc` — TypeScript (strict mode, dual config for actions vs web)
  - `react-spectrum-ui.mdc` — Adobe React Spectrum only; no other UI frameworks

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
| RDF/OWL, Neo4j, SHACL, ODP, knowledge graph, graph RAG; impl-log db index; data/RDF | **graph-db-specialist** |
| App Builder actions (OpenWhisk), APIs, serverless | **app-builder-actions-developer** |
| App Builder frontend (unified shell), React, web-src, UI components | **app-builder-frontend-developer** |
| MCP server design, App Builder MCP deployment, MCP tools/resources | **app-builder-mcp-developer** |
| Unit, integration, e2e tests | **test-engineer** |
| Security review, vulnerability assessment | **security-expert** |
| Workflow stuck, deadlock, need meta-view | **orchestrator** |

---

## When to Use Which Command

| Scenario | Command |
|----------|---------|
| New story from idea or Jira ticket | `/plan-1` |
| Review story-plan (Architect + Dev Lead + Security) | `/plan-2` |
| Resolve persona disagreements at story level | `/plan-debate` |
| Story complete, push to Jira, archive | `/plan-3` |
| Break story into implementation tasks | `/dev-1` |
| Specialists write task-plan sections | `/dev-2` |
| Task done, reviewed and passing | `/dev-3` |
| Specialist conflict on implementation plan | `/dev-debate` |
| New epic from idea or import from Jira | `/epic-1` |
| Review epic + create branch from stage | `/epic-2` |
| All epic stories done, PR to stage | `/epic-3` |
| Cross-story scope debate at epic level | `/epic-debate` |
| Add impl-log entry | `/log-entry` |
| Update impl-log index | `/index-update` |
| Quality review of persona skills | `/skill-review` |
| Bootstrap new project | `/setup-dev-process-new` |
| Retrofit existing project | `/setup-dev-process-existing` |

---

## Leverage Patterns

These patterns describe **when in a workflow** to use personas or commands.

| Pattern | When | What to do |
|---------|------|------------|
| **Before marking task complete** | About to mark a task done (close issue, merge PR). | Run `/dev-3`. Chains to `/log-entry` and `/index-update`. Consider AGENTS.md for learnings, patterns, gotchas — update if needed. |
| **Before marking story complete** | All tasks done, story ready. | Run `/plan-3`. Pre-check: no open Tier 1 findings; Tier 2 have human Disposition. |
| **Before marking epic complete** | All stories done within epic. | Run `/epic-3`. Verifies all stories complete, creates PR to `stage`. |
| **Workflow deadlock** | Stuck or unclear next step. | Invoke **orchestrator**: load impl-log indexes and current artifact, assess state, recommend next command. |
| **Design conflict** | Changes conflict with design-principles. | Escalate to **architect**; conflicts go to design-principles or Debate Log. |
| **Security finding** | Tier 1 or Tier 2 security issue. | Tier 1 blocks; Tier 2 requires human Disposition (see rules). |
| **New story** | User has idea or Jira ticket. | Start with `/plan-1`. |
| **New epic** | User has epic-scope idea or Jira epic. | Start with `/epic-1`. |
| **New project** | Setting up multi-persona workflow from scratch. | Run `/setup-dev-process-new`. |
| **Porting workflow** | Adding multi-persona workflow to existing project. | Run `/setup-dev-process-existing`. |
| **Before/after editing skills** | Changing or adding a persona under `.cursor/skills/`. | Run `/skill-review` (optionally scoped to the changed or open skill). |

---

## References

- [Persona skill authoring](persona-skill-authoring.md) — How to create or update persona SKILL.md and commands
- [Design principles](../design-principles/) — Constitution and invariants
- [Implementation log](../impl-log/) — Current state and history
- [Developer setup](README.md) — Quick start, MCP, ODP quick reference, process
- [AGENTS.md](../../.cursor/AGENTS.md) — Lightweight context map (project root)
- [Rules](../../.cursor/rules/) — multi-persona-workflow, typescript-standards, react-spectrum-ui
