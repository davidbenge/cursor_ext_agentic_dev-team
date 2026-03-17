# Developer Process — {{PROJECT_NAME}} Publishing

> **Confluence**: This document is the source of truth for the Developer Process. Update the Confluence page to match:  
> [Developer Process](https://wiki.corp.adobe.com/pages/viewpage.action?spaceKey=dssea&title=Developer+Process) (space: dssea).

**Cursor Multi-Persona AI Development Workflow** — Skills · Commands · Rules · Agents · Personas

---

## Links

**Enterprise GitHub**

- {{PROJECT_NAME}} ecosystem list: [pattern-atlas-ecosystem](https://github.com/stars/dbenge_adobe/lists/pattern-atlas-ecosystem)
- This repo: [OneAdobe/pattern_atlas_publishing](https://github.com/OneAdobe/pattern_atlas_publishing)
- Diagramming (monorepo sibling): [OneAdobe/pattern_atlas_diagramming](https://github.com/OneAdobe/pattern_atlas_diagramming)

**Jira**

- Project **{{JIRA_PREFIX}}** — Components: [{{PROJECT_NAME}} Publishing](https://jira.corp.adobe.com/issues/?jql=project%20%3D%20{{JIRA_PREFIX}}%20AND%20component%20%3D%20%22Pattern%20Atlas%20Publishing%22) | [{{PROJECT_NAME}} Diagrams](https://jira.corp.adobe.com/issues/?jql=project%20%3D%20{{JIRA_PREFIX}}%20AND%20component%20%3D%20%22Pattern%20Atlas%20Diagrams%22)
- When creating Jira tickets for this project, use component: **{{PROJECT_NAME}} Publishing**.

---

## Table of Contents

1. [Philosophy & Guiding Principles](#1-philosophy--guiding-principles)
2. [The Constitution — Design Principles](#2-the-constitution--design-principles)
3. [Persona Roster & Skill Definitions](#3-persona-roster--skill-definitions)
4. [File System Architecture](#4-file-system-architecture)
5. [Rules — The Non-Negotiables](#5-rules--the-non-negotiables)
6. [Commands — Process Orchestration](#6-commands--process-orchestration)
7. [End-to-End Workflow](#7-end-to-end-workflow)
8. [Human Touch Points Reference](#8-human-touch-points-reference)
9. [Refactoring an Existing Codebase Into This System](#9-refactoring-an-existing-codebase-into-this-system)
10. [Quick Reference](#10-quick-reference)
11. [In-Repo References & Confluence Sync](#11-in-repo-references--confluence-sync)

---

## 1. Philosophy & Guiding Principles

This system treats Cursor as a multi-agent development team, not a code autocomplete tool. Each AI persona is a scoped specialist with defined expertise, bounded context, and a clear role in the workflow. The **human controls the gates**. The **AI does the cognitive and process work** within those gates. This separation is the entire foundation.

### 1.1 Core Tenets

- **Focused context produces better work.** An agent that knows everything about the system reasons poorly about any specific part of it. Each persona loads only what their role requires; deep knowledge in a narrow lane outperforms shallow knowledge across everything.
- **Personas are specialists, not generalists.** A DB specialist does not opine on frontend design. A PM does not write schema migrations.
- **The human controls transitions.** Automation runs within phases. Humans approve movement between phases.
- **The filesystem is ephemeral for stories.** Working files under `docs/stories/` are temporary. Only the impl-log and design principles are permanent.
- **The constitution is law.** `docs/design-principles/` files are non-negotiable. No debate resolution, task-plan, or implementation may contradict them.
- **Risk is explicit.** Accepted risks are named, reasoned, and tracked. Silent omission is not acceptance.
- **Memory is indexed.** Specialists understand the system through maintained index files in `docs/impl-log/`, not by searching history.

### 1.2 The Four Primitives

| Primitive | Role |
|-----------|------|
| **Rules** | Unconditional invariants. Always on, always applied. Guardrails only. No process logic. Stored in `.cursor/rules/multi-persona-workflow.mdc`. |
| **Skills** | Persona expertise. One `SKILL.md` per persona in `.cursor/skills/[persona]/`. The agent routes to skills based on description metadata. Skills load only when relevant to the current task, never ambient. |
| **Commands** | Process orchestration. Sequence, handoffs, file lifecycle, Jira integration (via MCP when configured), debate rounds, completion gates. If the workflow changes, edit a command. If a persona's expertise changes, edit a skill. |
| **Agents** | Isolated execution environments. Separate Composer sessions with different system prompts, tool access, and optionally different models. Used for true role isolation when skills alone are insufficient. |

**Note:** Skills change what the agent *knows*. Agents change *who* is doing the work. Use skills first. Escalate to separate agents only when you need true isolation or a different model configuration.

### 1.3 Human vs. AI Responsibility

**The Cursor User (human)** is responsible for:

- Providing the creative seed and business context
- Engaging authentically in the PM discovery interview
- Reading and evaluating persona outputs at each phase gate
- Making risk disposition decisions on Tier 2 security findings
- Resolving deadlocked debates between personas
- Formal ratification before any work goes to Jira
- Approving impl plans and task plans before execution
- Deliberate sign-off on task and story completion

**The AI personas** are responsible for:

- Researching, analyzing, and drafting within their domain
- Loading and honoring the design principles
- Surfacing conflicts, risks, and concerns
- Maintaining the impl-log and index files
- Chaining automatically between phases when no human decision is needed

### 1.4 What This Looks Like Day to Day

This system has two primary human roles. The same person may play both, but the workflows are distinct enough to describe separately.

**The Story Planner**

Your day starts with an idea — a feature request, a stakeholder ask, something you noticed in user behavior. Run **`/plan-1 "your idea or feature brief"`**. The PM skill then runs a discovery interview: it asks questions, pushes back on assumptions, and asks uncomfortable clarifying questions. **Answer in chat.** Your job is to think out loud honestly, not to have all the answers. The better you engage, the better the story.

**Updating the plan and answering questions:** All of this happens in the same Cursor conversation. If the PM or a reviewer (Architect, Dev Lead, Security) has left open questions or something is wrong, reply in chat — e.g. "Clarify that we only support single-tenant" or "Add acceptance criterion: user can export CSV." The agent updates `docs/stories/[id]/plan-1.md` from your answers. You do not edit the file by hand unless you want to; the conversation is the source of truth and the agent applies it to the plan.

**More debate or discussion:** If personas disagree or you want structured back-and-forth, run **`/plan-debate [id]`**. You can add a note in the same message, e.g. `/plan-debate {{JIRA_PREFIX}}-73 Architect and Dev Lead disagree on caching — get Architect to respond first`. That’s a **command** (with optional context); the system runs threaded debate rounds and stops when there’s resolution or a deadlock for you to decide.

After the interview, story-plan writing and reviews run automatically. You get a notification when the Architect, Dev Lead, and Security Expert have all weighed in. Read their sections; this is real reading, not rubber-stamping. If the Security Expert flagged a Tier 2 risk, fill the Disposition fields. If there’s a deadlock, make the call. When everything looks right, run **`/plan-3 [id]`** and the story lands in Jira.

Your total active time on a typical story is two to four focused interactions spread across maybe twenty minutes. The AI does the research, drafting, and structured debate. You provide direction and judgment.

---

**The Developer**

You have a Jira story assigned to you, already planned and ratified. Your first step is to **create the implementation plan** — the task breakdown and per-task plans that specialists will execute against. Run **`/dev-1 [story-id]`** (e.g. `/dev-1 {{JIRA_PREFIX}}-74`). That command creates `docs/stories/[id]/impl/plan.md` and, for each task, a `task-plan.md`; the Architect and Dev Lead define tasks and ordering, then DB, backend, frontend, test, and security each write their sections in sequence. When all task-plans are written and security-reviewed, the workflow **pauses for you**.

**Review and questions:** Open `impl/plan.md` and each task’s `task-plan.md`. Check that the plan makes sense, interfaces are explicit, and nothing was missed. If the Security Expert flagged Tier 2 items, fill the Disposition fields. If specialists conflict or something is wrong, run **`/dev-debate [id] [task]`** to trigger another round, or tell the agent in chat how to adjust the plan. There is no separate “approve” command — when you’re satisfied, you say so in chat and move to execution.

**Starting execution:** Once the plan is approved, you tell the agent how you want to run:

- **Run everything and notify me at the end:** In chat, say something like: *“Plan approved. Execute all tasks in order. When the whole story is done, tell me so I can run `/plan-3`.”* The agent (or specialist sessions) implement against the task-plans; when all tasks are done, you review the work, run tests if you want, then run **`/plan-3`** to close the story and update the impl-log.
- **Run one task at a time and check in with me after each:** In chat, say something like: *“Plan approved. Execute one task at a time. After each task, stop and tell me so I can review and run `/dev-3 [id] [task]` before you start the next.”* You then get a checkpoint after every task to review and run **`/dev-3 [id] [task]`** before execution continues.

Either way, your energy goes into plan approval and final validation, not into micromanaging execution. The specialist agents work against the task-plans as their contract. Deviations surface for your decision; they are not silently absorbed.

**What Both Roles Share**

Neither role involves writing prose specifications from scratch, manually searching history to understand the system, or loading large amounts of context before being able to act. The system surfaces exactly what is needed, when it is needed. The human's energy goes into judgment calls, not information retrieval.

---

## 2. The Constitution — Design Principles

The design principles directory is the north star of the entire system. It is written before any story is planned, any skill is authored, or any command is defined. Every specialist skill loads its relevant principle file before doing anything else.

**Rule:** No task-plan, debate resolution, command output, or implementation may contradict `docs/design-principles/`. Conflicts escalate to the Architect persona. This is enforced by the workflow rule and by the ratification gate in every completion command.

### 2.1 File Structure

```
docs/design-principles/
  vision.md                    ← project north star, problem being solved, success metrics
  architecture.md              ← system mandates, patterns, boundaries, ADR conventions
  db.md                        ← data philosophy, Neo4j, naming conventions, schema rules, migration policy
  frontend.md                  ← UI principles, React Spectrum, component philosophy, design system rules
  backend.md                   ← API design philosophy, OpenWhisk, service boundaries, error handling patterns
  react-spectrum-ui-patterns.md   ← React Spectrum components, layout, theming, a11y
  testing.md                   ← quality philosophy, coverage expectations, testing pyramid
  security.md                  ← non-negotiables, risk tier definitions, auth standards
```

(App Builder implementation details — action structure, I/O Runtime, unified shell, application points — live under `.cursor/skills/app-builder-actions-developer/references/`, `app-builder-frontend-developer/references/`, and `app-builder-mcp-developer/`; see architecture.md for platform choice.)

### 2.2 What Each File Contains

**vision.md**

- The problem being solved and for whom
- What success looks like, measurable outcomes
- What this system is explicitly NOT
- Core user journeys that must always be protected
- Non-negotiable product principles

**architecture.md**

- Approved architectural patterns and those explicitly banned
- Module boundary rules, what can know about what
- How architectural decisions are recorded (ADR format)
- Scalability and performance mandates
- Technology choices that are fixed vs. open

**security.md — Risk Tier Definitions**

This file contains the canonical definition of the three risk tiers used throughout the workflow:

- **Tier 1 — Must Fix:** Blocks all workflow progression. Cannot be ratified or executed until resolved in the impl plan with a concrete mitigation.
- **Tier 2 — Should Address:** Must appear in the impl plan with either a mitigation approach or a formal risk acceptance. Human must explicitly decide; cannot be silently skipped.
- **Tier 3 — Noted:** Logged to the security impl-log for posture tracking. No immediate action required.

---

## 3. Persona Roster & Skill Definitions

Each persona lives in `.cursor/skills/[persona]/SKILL.md`. The description field in the SKILL.md frontmatter is written for agent routing: tight, keyword-dense, specific about when this skill should be loaded. The body is procedural, not encyclopedic.

### 3.1 Skill File Anatomy

```markdown
---
name: [persona-name]
description: [routing description, keywords the agent will match against tasks]
---

## Principles
- Load: docs/design-principles/[relevant].md first
- Load: docs/impl-log/[domain]/index.md for system state (when applicable)

## Role Boundary
- What this persona does
- What this persona explicitly does NOT do

## Phase 1: [First Phase Name]
[procedural steps]

## Phase 2: [Second Phase Name]
[procedural steps]

## Output Contract
- What this persona produces
- Where it writes output
- Status line it sets

## Success Criteria
[how to know the work is done correctly]
```

Keep SKILL.md under ~500 lines; link to design-principles or impl-log for detail.

### 3.2 The Personas

**Product Manager**

- **Skill file:** `.cursor/skills/product-manager/SKILL.md`
- **Routing description:** *"Use when planning new features, writing user stories, defining acceptance criteria, assessing backlog overlap, or interviewing stakeholders about product needs."*
- **Principles loaded:** `vision.md`

**Phase 1 — Discovery Interview:** Enters planning mode before loading any files. Interviews the Cursor User using the discovery question framework. Does not write anything during this phase. Pushes back on vague answers. Continues until problem, user, value, success measure, and minimum viable scope are clearly established. Summarizes understanding back to user and confirms before proceeding.

**Phase 2 — Research:** Loads `vision.md`. Checks Jira backlog via MCP (when configured) for overlap and conflicts.

**Phase 3 — Write:** Writes `docs/stories/[id]/plan-1.md` including Discovery Summary, Backlog Conflict Assessment, and Acceptance Criteria. Sets status: Ready for Architect Review. Chains automatically to `/plan-2`.

**Discovery question framework (embedded in skill):**

- What problem does this solve and how does the user experience it today?
- What does success look like, how would we know this worked?
- Who specifically is the user, are there multiple affected types?
- What is the cost of not doing this?
- Is this a workaround for something broken upstream?
- What is the simplest version that delivers core value?
- Are there adjacent features or flows this might break?

**Architect**

- **Skill file:** `.cursor/skills/architect/SKILL.md`
- **Routing description:** *"Use when reviewing story technical impact, making architectural decisions, assessing system boundaries, writing ADRs, breaking stories into tasks, or evaluating cross-cutting concerns."*
- **Principles loaded:** `architecture.md` + `vision.md`
- **Impl-log loaded:** `architecture/index.md` + all domain `index.md` files

**Role:** Reviews story-plans for technical soundness, system impact, and principle alignment. Breaks impl plans into tasks. Reviews cross-specialist conflicts. Writes `architecture/log.md` entries on story completion. Updates `architecture/index.md` (system brain).

**Does NOT:** Write implementation code. Make frontend or DB decisions unilaterally. Override security findings.

**Dev Lead**

- **Skill file:** `.cursor/skills/dev-lead/SKILL.md`
- **Routing description:** *"Use when estimating implementation effort, identifying technical risks in stories, reviewing task dependency ordering, assessing implementation feasibility, or coordinating between specialists."*
- **Principles loaded:** `architecture.md` + `backend.md`

**Role:** Reviews story-plans for implementation feasibility, effort signals, and dependency risks. Collaborates with Architect on impl plan task breakdown. Coordinates specialist sections in task-plans for interface alignment.

**DB Specialist**

- **Skill file:** `.cursor/skills/graph-db-specialist/SKILL.md`
- **Principles loaded:** `db.md`
- **Impl-log loaded:** `db/index.md`

**Role:** Writes DB section of task-plans. Schema design, migration strategy, index planning, Neo4j/Cypher query patterns. **Maintains** `docs/impl-log/db/index.md` (current schema/Neo4j state) and **ODP SHACL shapes** in `data/RDF/shapes/*.ttl` (notably `odp-shapes.ttl`) as schema or shape changes happen. Writes `db/log.md` entries and updates `db/index.md` on completion.

**App Builder Actions Developer**

- **Skill file:** `.cursor/skills/app-builder-actions-developer/SKILL.md`
- **References loaded:** `.cursor/skills/app-builder-actions-developer/references/` (platform.md, solution-patterns.md, application-points.md), `backend.md` (constitution)
- **Impl-log loaded:** `backend/index.md`

**Role:** Writes backend section of task-plans. App Builder actions (OpenWhisk), API design, action structure, integration patterns, error handling. Knows application points and directory signatures (e.g. `src/{{APP_EXTENSION_POINT}}/actions` for unified shell). Must read DB section before writing to ensure schema alignment.

**App Builder Frontend Developer**

- **Skill file:** `.cursor/skills/app-builder-frontend-developer/SKILL.md`
- **References loaded:** `.cursor/skills/app-builder-frontend-developer/references/` (unified-shell.md, application-points.md), `frontend.md` (constitution)
- **Impl-log loaded:** `frontend/index.md`

**Role:** Writes frontend section of task-plans. App Builder frontend (unified shell), component design, state management, API consumption contract. Knows application points and directory signatures (e.g. `src/{{APP_EXTENSION_POINT}}` for unified shell). Must read backend section before writing to ensure API contract alignment.

**App Builder MCP Developer**

- **Skill file:** `.cursor/skills/app-builder-mcp-developer/SKILL.md`
- **Principles loaded:** `mcp-setup.md`; archive MCP story when task involves {{PROJECT_NAME}} MCP or generator-app-remote-mcp-server-generic.
- **Impl-log loaded:** (as applicable)

**Role:** MCP server design and deployment on App Builder/Runtime; MCP tool, prompt, and resource contracts. Use when task is MCP server, App Builder MCP deployment, or MCP tools/resources. Does not implement non-MCP backend actions.

**Test Engineer**

- **Skill file:** `.cursor/skills/test-engineer/SKILL.md`
- **Principles loaded:** `testing.md`
- **Impl-log loaded:** `test/index.md`

**Role:** Writes test section of task-plans. Reads all specialist sections before writing. Identifies edge cases, integration test requirements, and coverage gaps. Writes `test/log.md` entries on completion.

**Security Expert**

- **Skill file:** `.cursor/skills/security-expert/SKILL.md`
- **Principles loaded:** `security.md`
- **Impl-log loaded:** `security/index.md` + `security/risk-register.md`

**Role:** Reviews story-plans and task-plans for threat surface, vulnerability patterns, auth concerns, data exposure risks, and dependency issues. Assigns risk tiers per `security.md` definitions. Writes `security/log.md` entries and maintains `risk-register.md`.

**Risk item format:**

| Risk ID | Description | Tier | Recommendation | Disposition |
|---------|-------------|------|----------------|-------------|
| SEC-001 | User data exposed in API response | 2 | Filter PII fields | [human fills] |

**Rule:** Tier 1 findings block all progression commands. Tier 2 findings require a human-filled Disposition field before any progression command will execute. The Security Expert is a gating voice, not an advisory one.

**Orchestrator**

- **Skill file:** `.cursor/skills/orchestrator/SKILL.md`

**Role:** Loaded by the developer when the workflow is stuck, a deadlock has occurred, or the human needs a meta-view of where things stand. Reads the full current artifact, assesses state, and recommends the next command. Does not write code, specs, or domain content. Manages process only.

---

## 4. File System Architecture

### 4.1 Complete Structure

```
docs/
  design-principles/          ← PERMANENT. The constitution.
    vision.md
    architecture.md
    db.md
    frontend.md
    backend.md
    react-spectrum-ui-patterns.md
    testing.md
    security.md

  stories/                    ← EPHEMERAL. Deleted on completion.
    [story-id]/
      story-plan.md           ← deleted on Jira push
      impl/
        plan.md               ← deleted on story completion
        [task-id]/
          task-plan.md        ← one file, all specialist sections
          [artifact files]    ← extracted large artifacts, linked from task-plan

  epics/                      ← EPHEMERAL. Deleted on epic completion.
    [epic-id]/
      epic-plan.md            ← epic summary, story breakdown, cross-story contracts

  impl-log/                   ← PERMANENT. System memory.
    architecture/
      log.md                  ← running reverse-chron narrative
      index.md                ← system brain / topology map
    db/
      log.md
      index.md                ← current schema state inventory
    backend/
      log.md
      index.md                ← current service/API surface map
    frontend/
      log.md
      index.md                ← component inventory and pattern decisions
    test/
      log.md
      index.md
    security/
      log.md
      index.md                ← current threat surface map
      risk-register.md        ← all accepted risks, open and monitored
    stories/                  ← Archived story artifacts (moved from docs/stories/ on completion)
      [story-id]/
        story-plan.md
        [task-id]/
          task-plan.md
          [artifact files]
    epics/                    ← Archived epic artifacts (moved from docs/epics/ on completion)
      [epic-id]/
        epic-plan.md

  developer-setup/            ← Developer process, persona/command ref, MCP, ODP ref.
    developer-process.md
    persona-and-command-reference.md
    persona-skill-authoring.md
    mcp-setup.md
    odp-quick-reference.md
    app-builder-application-points.md
    app-builder-frontend-unified-shell.md
    RELATIONSHIP_VOCABULARY.md
    HTML_GENERATION_COMPLETE.md
    README-frameio-upload.md
    README.md

.cursor/
  skills/
    product-manager/SKILL.md
    architect/SKILL.md
    dev-lead/SKILL.md
    graph-db-specialist/SKILL.md
    app-builder-actions-developer/SKILL.md
    app-builder-frontend-developer/SKILL.md
    app-builder-mcp-developer/SKILL.md
    test-engineer/SKILL.md
    security-expert/SKILL.md
    orchestrator/SKILL.md
  commands/
    plan-1.md                  ← story plan (PM discovery + write)
    plan-2.md                  ← story review (Architect + Dev Lead + Security)
    plan-3.md                  ← story complete (ratify, Jira, archive, branch/PR)
    plan-debate.md             ← story-level persona debate
    dev-1.md                   ← impl plan kickoff (break story into tasks)
    dev-2.md                   ← task kickoff (specialists write task-plan)
    dev-3.md                   ← task complete (log, index, archive)
    dev-debate.md              ← impl-level specialist debate
    epic-1.md                  ← epic plan (discovery or import from Jira)
    epic-2.md                  ← epic review + kickoff (branch from stage)
    epic-3.md                  ← epic complete (PR to stage, archive)
    epic-debate.md             ← epic-level cross-story debate
    skill-review.md
    log-entry.md
    index-update.md
    setup-dev-process-new.md   ← bootstrap new project
    setup-dev-process-existing.md ← retrofit existing project
  rules/
    multi-persona-workflow.mdc
    typescript-standards.mdc
    react-spectrum-ui.mdc
  AGENTS.md                   ← Lightweight context map (~50 lines). Not a dump.
```

### 4.2 The Ephemeral vs. Permanent Split

Everything in `docs/stories/` and `docs/epics/` is explicitly temporary. It is working memory, in-flight artifacts that live only as long as the work is active. When a story completes, the story-plan and task-plans are **archived** to `docs/impl-log/stories/$STORY_ID/` (not deleted), then the working folder is removed. When an epic completes, the epic-plan is archived to `docs/impl-log/epics/$EPIC_ID/`. Completed work lives in three places: Jira (story/epic details and acceptance criteria), impl-log domain files (log.md narrative and index.md current state), and impl-log/stories or impl-log/epics (archived plans and specialist sections for deep reference).

The impl-log is permanent and treated like production code. It is reviewed, kept accurate, and never allowed to bloat. Index files are updated in-place, not appended. Index entries that were added or materially changed by a story carry a Jira ID breadcrumb (e.g. `({{JIRA_PREFIX}}-13)`) so personas can find related history in log.md. Log entries link to archived task-plans under `impl-log/stories/` for full detail. A new story planner loads roughly 300 lines of current system state from the index files; archived details are pull-on-demand only when a persona detects relevant prior history.

### 4.3 Story-Plan File Structure

```markdown
# Story: [Story Title]

## Discovery Summary
[PM's narrative of what the interview surfaced, pivots from original idea, reasoning]

## User Story
As a [user], I want [capability] so that [value].

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Backlog Conflict Assessment
[Jira overlap findings, related stories, dependencies]

## Architect Review
[Technical concerns, system impact, open questions]

## Dev Lead Review
[Effort signals, implementation risks, dependency flags]

## Security Expert Review
### Threat Assessment
| Risk ID | Description | Tier | Recommendation | Disposition |

## Debate Log
[Threaded debate entries if needed]

## Status
[Current status line — drives what command is valid next]
```

### 4.4 Task-Plan File Structure

```markdown
# Task: [Task Title] | Story: [Story ID]

## Task Overview
[What this task accomplishes, its boundaries, dependencies on other tasks]

## DB Specialist
[Schema changes, migrations, index strategy, query patterns]

## App Builder Actions Developer
[API design, App Builder action structure, service logic, integration contracts, reads DB section first]

## App Builder Frontend Developer
[Component design, state, API consumption, reads backend section first]

## Test Engineer
[Coverage plan, edge cases, integration tests, reads all sections first]

## Security Expert Review
### Threat Assessment
| Risk ID | Description | Tier | Recommendation | Disposition |

## Integration Notes
[Cross-specialist decisions, interface contracts, shared assumptions]

## Debate Log
[Threaded debate entries between specialists]

## Status
[Current status line]
```

### 4.5 Impl-Log Entry Format

```markdown
## [JIRA-ID] | [Story Title] | [Date]
**What**: 1-2 sentence summary of change
**Why**: Problem or motivation
**Key decisions**: Non-obvious choices, trade-offs, alternatives rejected
**Contracts**: API shapes, data contracts, or interfaces other domains depend on
**See**: [task details](../stories/$STORY_ID/$TASK_ID/task-plan.md)
Jira: [JIRA-ID]
```

The `index.md` files are not narrative — they are current state inventories. DB index has current schema. Backend index has current service map. Architecture index has system topology. These are the files loaded for new story planning. They must stay accurate and concise. Entries carry Jira ID breadcrumbs (e.g. `({{JIRA_PREFIX}}-13)`) so personas can trace history via log.md and archived details.

### 4.6 Risk Register Format

| Risk ID | Story | Description | Tier | Accepted By | Date | Review Trigger |
|---------|-------|-------------|------|-------------|------|----------------|
| SEC-001 | PROJ-42 | Session tokens not rotated on role change | 2 | [initials] | 2026-01-15 | Re-evaluate if admin roles added |

The Review Trigger field is key — it says under what conditions this accepted risk should be re-evaluated. Accepted risks have a named owner, a reason, and a condition for review. They do not silently persist forever.

---

## 5. Rules — The Non-Negotiables

**Location:** `.cursor/rules/` — workflow: `multi-persona-workflow.mdc`; domain rules: `typescript-standards.mdc`, `react-spectrum-ui.mdc`.

The workflow rule holds the complete process invariants. Domain rules (TypeScript, React Spectrum UI) apply when working in those areas. No process logic in any rule file. Rules are applied unconditionally when relevant to the agent interaction.

1. Never modify another persona's section in a story-plan or task-plan. Personas append their own section only. Disagreements go in Debate Log.
2. `docs/stories/` files are ephemeral. Never commit them to version control.
3. impl-log index files are updated in-place. Never append new entries to index files. index.md = current state. log.md = history.
4. `docs/design-principles/` is non-negotiable. No debate resolution, task-plan, or implementation may contradict it. Conflicts escalate to the Architect.
5. A Tier 1 security finding blocks all progression commands until resolved in the impl plan with a concrete mitigation.
6. A Tier 2 security finding requires an explicit human Disposition entry before any progression command executes.
7. Large artifacts (schema files, migration scripts, component specs over 50 lines) are extracted to separate files and linked from the task-plan section. The task-plan body stays as summaries and decisions.

---

## 6. Commands — Process Orchestration

Commands live in `.cursor/commands/` as `.md` files. They encode workflow sequence, not domain expertise. Commands call skills. Skills provide the expertise. The human invokes commands at gate points. Everything else chains automatically. Jira integration is via MCP when configured.

### 6.1 Command Index

Commands are organized into numbered groups that make the workflow sequence self-evident:

**Plan group** (story planning lifecycle):

| Command | Invoked By | Chains To | Purpose |
|---------|------------|-----------|---------|
| `/plan-1 "idea"` | Cursor User | `/plan-2` (auto) | PM discovery interview + write story-plan |
| `/plan-2 [id]` | Auto (from plan-1) or User | Security review (auto) | Architect + Dev Lead + Security review story-plan |
| `/plan-3 [id]` | Cursor User | Jira push → archive → branch/PR | Ratify story, push to Jira, archive, branch/PR to `stage` |
| `/plan-debate [id]` | Cursor User if needed | Self (rounds) → pause | Resolve persona disagreements at story level |

**Dev group** (implementation lifecycle):

| Command | Invoked By | Chains To | Purpose |
|---------|------------|-----------|---------|
| `/dev-1 [id]` | Cursor User | `/dev-2` (auto) | Break story into tasks, write impl plan |
| `/dev-2 [id] [task]` | Auto (from dev-1) or User | Pause for human approval | Specialists write task-plan sections |
| `/dev-3 [id] [task]` | Cursor User | log-entry + index-update (auto) | Task complete + story-complete at impl level |
| `/dev-debate [id] [task]` | Cursor User if needed | Pause for human | Specialist-level debate on impl plan |

**Epic group** (epic lifecycle):

| Command | Invoked By | Chains To | Purpose |
|---------|------------|-----------|---------|
| `/epic-1 [id or "idea"]` | Cursor User | `/epic-2` (auto) | Epic plan — discovery or import from Jira |
| `/epic-2 [id]` | Auto (from epic-1) or User | Pause for user | Epic review + kickoff (create branch from `stage`) |
| `/epic-3 [id]` | Cursor User | Jira update → archive → PR to `stage` | Epic complete — verify all stories done, PR to `stage` |
| `/epic-debate [id]` | Cursor User if needed | Self (rounds) → pause | Cross-story scope debate at epic level |

**Support commands:**

| Command | Invoked By | Chains To | Purpose |
|---------|------------|-----------|---------|
| `/skill-review [scope]` | Cursor User | None | Quality review of persona skills (all, named, open/changed) |
| `/log-entry [domain] [id]` | Auto (from dev-3) or User | index-update (auto) | Specialist writes impl-log entry |
| `/index-update [domain]` | Auto (from log-entry) or User | End | Update domain index.md in-place |

**Setup commands:**

| Command | Invoked By | Chains To | Purpose |
|---------|------------|-----------|---------|
| `/setup-dev-process-new` | Cursor User | /skill-review (auto) | Bootstrap new project with full workflow structure |
| `/setup-dev-process-existing` | Cursor User | /skill-review (auto) | Retrofit existing project — inspect, interview, generate |

### 6.2 Command Definitions

**/plan-1** (was /plan-1)

- **Load:** product-manager skill
- **Epic awareness:** If story is part of an epic, load `docs/epics/$EPIC_ID/epic-plan.md` for cross-story contracts. For imported stories (epic Mode B), shift to review-and-refine mode: pull Jira content, present, interview for gaps only.
- **Phase 1:** Enter discovery interview mode. Do not load files. Interview Cursor User per discovery question framework. Confirm understanding before proceeding.
- **Phase 2:** Load `docs/design-principles/vision.md`, `architecture.md`. Query Jira via MCP (when configured) for backlog overlap.
- **Phase 3:** Create `docs/stories/$STORY_ID/plan-1.md`. Include: Discovery Summary, User Story, Acceptance Criteria, Backlog Conflict Assessment. Set status: Ready for Review.
- **Auto-chain:** `/plan-2 $STORY_ID`

**/plan-2** (was /plan-2)

- **Load:** architect skill — Read `architecture.md`, `architecture/index.md`, story-plan. Write Architect Review section.
- **Load:** dev-lead skill — Read story-plan including Architect Review. Write Dev Lead Review section.
- **Load:** security-expert skill — Read `security.md`, `security/index.md` and `risk-register.md`. Read full story-plan. Write Security Expert Review section with risk tier table.
- **Evaluate:** If Tier 1 finding exists → set status: BLOCKED, Tier 1 Security Finding; notify Cursor User; stop. If Tier 2 finding exists → set status: Awaiting Risk Disposition; notify Cursor User to fill Disposition fields; stop. If clean → set status: Ready for Ratification; notify Cursor User to review and run `/plan-3`.

**/plan-3** (was /plan-3)

- **Pre-check:** Verify no open Tier 1 security findings; verify all Tier 2 findings have human Disposition entries; verify status is Ready for Ratification. If any check fails → stop, report what is blocking.
- **Execute:** Push story-plan content to Jira via MCP; append story summary to impl-log/architecture/log.md; update impl-log/architecture/index.md in-place; archive docs/stories/$STORY_ID/ to docs/impl-log/stories/$STORY_ID/.
- **Branching (standalone stories):** Branch from `stage`, commit with `feat(<STORY_ID>): <summary>`, push, PR to `stage`.
- **Branching (epic stories):** Skip branch/PR — all work is on the `epic/<EPIC_ID>` branch. Commits use `feat(<STORY_ID>): <summary>`. PR deferred to `/epic-3`.

**/plan-debate** (was /plan-debate)

- **Load:** specified persona skill (or orchestrator if no persona specified). Read full story-plan including all existing debate log entries. Write new threaded entry to Debate Log. Format: [Persona] [round N]: [position].
- **Evaluate:** If resolution reached → update relevant section, set status: Resolution Reached; write precedent to impl-log/architecture/log.md; notify Cursor User. If deadlock → set status: Deadlocked - Human Decision Required; summarize positions for Cursor User; stop.

**/dev-1** (was /dev-1)

- **Load:** architect skill + dev-lead skill. Read Jira story (via MCP) for accepted story details. Read docs/design-principles/architecture.md and all impl-log index files. If part of an epic, load epic-plan cross-story contracts.
- **Create:** docs/stories/$STORY_ID/impl/plan.md — break story into tasks, define task dependency ordering, note cross-task interface contracts, reference relevant design principles.
- **Auto-chain:** /dev-2 $STORY_ID [first task in dependency order]. Subsequent tasks chain automatically in order.
- **Pause:** When all task-plans are written and security reviewed. Notify Cursor User to review task-plans and fill any risk dispositions.

**/dev-2** (was /dev-2)

- **Create:** docs/stories/$STORY_ID/impl/$TASK_ID/task-plan.md
- **Load:** graph-db-specialist — Read db.md, db/index.md. Write DB Specialist section.
- **Load:** app-builder-actions-developer — Read skill references (platform, solution-patterns, application-points), backend.md, backend/index.md, DB section. Write Backend Specialist section.
- **Load:** app-builder-frontend-developer — Read skill references (unified-shell, application-points), frontend.md, frontend/index.md, Backend section. Write Frontend Specialist section.
- **Load:** test-engineer — Read testing.md, all specialist sections. Write Test Engineer section.
- **Load:** security-expert — Read security.md, all specialist sections. Write Security Expert Review section.
- **Evaluate:** Same security logic as /plan-2. Pause: notify Cursor User to review task-plan.

**/dev-3** (was /dev-3)

- **Pre-check:** Cursor User confirms work is reviewed and passing.
- **Auto-chain:** /log-entry (db, backend, frontend, test, security); /index-update (each domain); move docs/stories/$STORY_ID/impl/$TASK_ID/ to docs/impl-log/stories/$STORY_ID/$TASK_ID/; confirm to Cursor User. If all tasks complete: prompt Cursor User to run /plan-3 at impl level.

**/dev-debate** (was /dev-debate)

- **Load:** architect skill. Formal debate on impl plan. Threaded entries to Debate Log. Pause for human approval.

**/epic-1** (new)

- **Mode A (Discovery):** PM + Architect discovery at epic scope. Interview, research, decompose into stories with dependency ordering and cross-story contracts. Write `docs/epics/$EPIC_ID/epic-plan.md`.
- **Mode B (Import):** Pull existing epic + child stories from Jira. Present inventory, assess completeness, flag gaps. Research alignment with vision/architecture. Write epic-plan with gap analysis and per-story readiness assessment.
- **Mode detection:** Jira epic with child stories → Mode B. Brief or idea → Mode A. Jira epic with zero children → Mode A using epic description.
- **Auto-chain:** `/epic-2 $EPIC_ID`

**/epic-2** (new)

- **Load:** architect, dev-lead, security-expert skills (sequence). Review epic for system-level impact, cross-story coherence, effort, coordination risks, and security surface.
- **For imported epics (Mode B):** Explicitly call out concerns with pre-existing Jira story definitions.
- **On ratification:** Create epic branch from `stage`. Push/update stories in Jira. Set status: Epic In Progress.
- **Pause:** User begins story work with `/plan-1` for individual stories. All work commits to `epic/<EPIC_ID>` branch.

**/epic-3** (new)

- **Pre-check:** All child stories complete, no open Tier 1, all Tier 2 disposed.
- **Execute:** Write epic-level summary to impl-log/architecture/log.md; update architecture/index.md; archive docs/epics/$EPIC_ID/ to docs/impl-log/epics/$EPIC_ID/; create PR from `epic/<EPIC_ID>` to `stage`; update Jira epic status.

**/epic-debate** (new)

- **Load:** specified persona skill (or orchestrator). Read full epic-plan and Debate Log. Write threaded entry. Resolution or deadlock handling same as /plan-debate.

**/log-entry**

- **Load:** Relevant specialist skill for domain. Specialist writes 3-5 sentence narrative to impl-log/[domain]/log.md. Chain to /index-update for that domain.

**/index-update**

- **Load:** Relevant specialist skill. Update impl-log/[domain]/index.md in-place (current state only). End.

**/setup-dev-process-new**

- Phased, interactive bootstrap for new projects. Six phases: Project Identity → Vision & Constitution → Quality & Security → System Memory → Personas & Skills → Commands, Rules & Workflow. Each phase asks, generates, presents for review, confirms before proceeding. See `.cursor/commands/setup-dev-process-new.md` for full detail.

**/setup-dev-process-existing**

- Phased, interactive retrofit for existing projects. Six phases: Codebase Inspection → Vision & Constitution from Patterns → Quality & Security Assessment → System Memory Seeding → Personas & Skills → Commands, Rules & Workflow. Inspection-driven: scans first, proposes based on findings, user corrects. See `.cursor/commands/setup-dev-process-existing.md` for full detail.

---

## 7. End-to-End Workflow

This section traces a complete story from idea to archived implementation. Human touch points are called out explicitly. Everything between them runs automatically.

### 7.1 Story Planning Phase

> **CURSOR USER ACTION:** Run `/plan-1 "rough idea or feature brief"`. This single command starts the entire story planning workflow.

> **AUTO:** PM skill enters discovery interview mode. No files loaded yet. No writing yet.

> **CURSOR USER ACTION:** Engage the PM interview authentically. Expect 4-8 exchanges. The PM skill will push back on vague answers; this is intentional. The quality of the story is directly proportional to the quality of this conversation. Do not shortcut it.

> **AUTO:** PM skill confirms understanding, then loads `vision.md` and `architecture.md`. Queries Jira via MCP (when configured) for backlog overlap. Writes `docs/stories/[id]/plan-1.md` with Discovery Summary, User Story, Acceptance Criteria, and Backlog Conflict Assessment. Status: Ready for Architect Review. Chains to `/plan-2`.

> **AUTO:** Architect skill loads `architecture.md` and `architecture/index.md`. Reads story-plan. Writes Architect Review section.

> **AUTO:** Dev Lead skill reads story-plan including Architect section. Writes Dev Lead Review section.

> **AUTO:** Security Expert skill loads `security.md` and `risk-register.md`. Reads full story-plan. Writes Security Expert Review with risk tier table. Evaluates findings.

> **AUTO:** If Tier 1: workflow stops and blocks with explanation. If Tier 2: workflow stops and requests human disposition. If clean: sets status Ready for Ratification and notifies Cursor User.

> **BLOCKED (Tier 1):** Workflow stops completely. Cursor User must resolve the finding in the impl plan before any progression is possible. This cannot be accepted or bypassed.

> **CURSOR USER ACTION (if Tier 2 risks present):** Read the Security Expert section. For each Tier 2 item, fill in the Disposition field: either "Mitigate in impl plan: [approach]" or "Risk Accepted: [reason]". This is a named, reasoned decision, not a checkbox.

> **CURSOR USER ACTION (if debate needed):** Run `/plan-debate [id]` with a note about what is in conflict. Specify which persona should respond first if known.

> **AUTO:** Debate rounds run between persona skills. Each round adds a threaded entry to the Debate Log. Continues until resolution or deadlock.

> **CURSOR USER ACTION (if deadlocked):** Read the deadlock summary. Both positions are presented clearly. Make the call. This is genuine human judgment — the personas have genuinely disagreed and need a deciding voice.

> **CURSOR USER ACTION:** When all sections look right and no open risks remain, run `/plan-3 [id]`. This is the formal approval gate, deliberate and intentional before anything goes to Jira.

> **AUTO:** `/plan-3` verifies no open Tier 1 findings, no undisposed Tier 2 findings, and status is Ready for Ratification. Pushes story to Jira via MCP. Writes summary to `architecture/log.md`. Updates `architecture/index.md`. Deletes story-plan and story folder. Reports clean state to Cursor User.

### 7.2 Implementation Planning Phase

> **CURSOR USER ACTION:** Run `/dev-1 [id]`. The human controls when implementation begins. This should not happen automatically after story ratification.

> **AUTO:** Architect and Dev Lead skills load the ratified story from Jira via MCP and all impl-log index files. Create `docs/stories/[id]/impl/plan.md`. Break story into tasks with dependency ordering and cross-task interface contracts.

> **AUTO:** Chain to `/dev-2` for each task in dependency order. All specialist skills write their sections in sequence, each reading adjacent sections for interface alignment. Security Expert reviews each task-plan. Workflow pauses when all task-plans are complete.

> **CURSOR USER ACTION:** Review the `impl/plan.md` task breakdown. Confirm dependency ordering makes sense. Review each `task-plan.md`. Fill in any Tier 2 risk dispositions. Approve or trigger `/dev-debate` if a specialist conflict needs another round.

**Note:** Specialists read adjacent sections before writing their own. DB writes first. Backend reads DB before writing. Frontend reads Backend before writing. Test reads all. Security reads all. This ordering is enforced by the `/dev-2` command sequence.

### 7.3 Task Execution Phase

> **AUTO:** Specialist skills execute against their task-plan sections in focused agent sessions. Each session loads only its skill and its section. The task-plan is the contract.

> **CURSOR USER ACTION:** Monitor as needed. If a specialist hits something that contradicts the task-plan — an unexpected dependency, a schema conflict, a missing API — the Cursor User decides: update the plan, or course-correct the agent. Deviations do not get silently absorbed.

### 7.4 Completion & Memory Phase

> **CURSOR USER ACTION:** Run `/dev-3 [id] [task]` when the task's work is reviewed and looks right. Completion is deliberate, not automatic.

> **AUTO:** Each specialist skill writes a 3-5 sentence log entry to their `impl-log/[domain]/log.md` with Jira link. Each specialist skill updates their `impl-log/[domain]/index.md` in-place — current state only, no append. Task folder deleted. Clean state reported.

When all tasks complete:

> **CURSOR USER ACTION:** Run `/plan-3` at impl level. Review the Architect's summary entry into `architecture/log.md` and the updated `architecture/index.md` before confirming. Sanity-check that the system brain reflects reality.

> **AUTO:** `impl/` folder deleted. Story fully archived in Jira with log pointers. Filesystem is clean.

### 7.5 New Story Planning — The Payoff

> **CURSOR USER ACTION:** Run `/plan-1` with the next idea. Same entry point as always.

> **AUTO:** PM and Architect load only the index files — approximately 300 lines of current system state across all domains. No story archaeology. No context explosion. The system understands itself.

The constitution keeps every decision aligned to the north star. The impl-log gives every specialist the domain memory they need without carrying anyone else's. The filesystem is clean. The risk register is honest. The system brain is current.

### 7.6 Epic Workflow

Epics are collections of related stories that share a common goal, cross-story contracts, and coordinated delivery. The epic workflow wraps the story workflow with additional planning and branching structure.

> **CURSOR USER ACTION:** Run `/epic-1 [epic-id or "feature brief"]`. If you provide a Jira epic ID that already has child stories, the system imports and validates them (Mode B). If you provide a brief or idea, the system plans from scratch (Mode A).

> **AUTO (Mode A):** PM skill enters epic-scope discovery interview — broader than a story interview, covering the full problem space and phased delivery. Architect + Dev Lead decompose into stories with dependency ordering and cross-story interface contracts. Writes `docs/epics/[id]/epic-plan.md`. Chains to `/epic-2`.

> **AUTO (Mode B):** PM + Architect pull epic and all child stories from Jira. Present inventory with completeness assessment. Flag thin stories, missing contracts, and gaps. Research alignment with vision and architecture. Write epic-plan with gap analysis and per-story readiness. Chains to `/epic-2`.

> **AUTO:** `/epic-2` runs Architect, Dev Lead, and Security reviews at epic scope. For imported epics, reviews explicitly call out concerns with pre-existing Jira definitions.

> **CURSOR USER ACTION (if Tier 2 risks):** Fill Disposition fields. If debate needed, run `/epic-debate [id]`.

> **CURSOR USER ACTION:** When satisfied, ratify the epic. The system creates the epic branch from `stage` (`epic/[id]`), pushes stories to Jira (or updates existing ones), and sets status: Epic In Progress.

> **CURSOR USER ACTION:** Begin story work with `/plan-1` for individual stories within the epic. All story and task work commits directly to the `epic/[id]` branch using commit messages `feat([STORY-ID]): [summary]`.

> **AUTO:** Stories flow through the normal plan-1 → plan-2 → dev-1 → dev-2 → dev-3 → plan-3 cycle. When `/plan-3` runs for a story within an epic, it skips branch/PR creation — it only archives artifacts, updates impl-log, and marks the story done in Jira.

> **CURSOR USER ACTION:** When all stories are done, run `/epic-3 [id]`. The system verifies all stories are complete, writes an epic-level summary to the impl-log, archives the epic-plan, and creates a single PR from `epic/[id]` to `stage`.

**Branching model:** Epic branches are flat — no per-story sub-branches. All work happens on `epic/[id]`. Story boundaries are tracked through commit naming (`feat(STORY-ID): summary`) and via Jira, not git branches. This avoids stale branch risks when multiple agents work concurrently.

---

## 8. Human Touch Points Reference

Every remaining manual step has a clear reason: input only the human can provide, a decision with real consequences, or a formal accountability gate.

| Stage | Cursor User Action | Why Human — Not Automation |
|-------|--------------------|----------------------------|
| Story seed | Run `/plan-1 "idea"` | Entry point. Creative and business judgment. |
| PM interview | Engage 4-8 exchanges | Input only the human has. |
| Tier 2 disposition (story) | Fill Disposition fields | Decision; formal named risk acceptance. |
| Debate trigger | Run `/plan-debate [id]` | Judgment; surface and frame the conflict. |
| Deadlock resolution | Read summary, make the call | Judgment; personas genuinely disagree. |
| Story ratification | Run `/plan-3 [id]` | Accountability; formal gate before Jira. |
| Impl kickoff | Run `/dev-1 [id]` | Timing — human controls when impl begins. |
| Task-plan approval | Review + fill Tier 2 dispositions | Quality gate before execution begins. |
| Task-level debate | Run `/dev-debate [id] [task]` | Judgment — specialist conflict resolution. |
| Execution monitoring | Monitor, catch deviations | Quality — no silent plan contradictions. |
| Task sign-off | Run `/dev-3 [id] [task]` | Accountability — deliberate completion. |
| Story close (impl) | Run `/plan-3` at impl level | Accountability — sanity check system brain. |
| Epic seed | Run `/epic-1 [id or "idea"]` | Entry point. Epic-scope creative and business judgment. |
| Epic Tier 2 disposition | Fill Disposition fields | Decision; formal risk acceptance at epic scope. |
| Epic ratification | Ratify in `/epic-2` | Accountability; formal gate before epic branch and Jira. |
| Epic story work | Run `/plan-1` per story | Timing — human controls story sequencing within epic. |
| Epic close | Run `/epic-3 [id]` | Accountability — verify all stories done, PR to stage. |

---

## 9. Refactoring an Existing Codebase Into This System

If you are adopting this workflow on an existing project, the migration has a specific order. Do not try to do everything at once.

**Automated setup:** For a guided, interactive setup process, run **`/setup-dev-process-existing`**. It scans your codebase, proposes artifacts based on what it finds, and walks you through each phase with review and confirmation. The manual phases below describe the same process for reference.

### 9.1 Phase 1 — Write the Constitution First

Before touching rules, skills, or commands. Before writing any story. Spend time on `docs/design-principles/`. If the principles do not exist in written form yet, the Architect and PM skills have no north star to reference and will produce inconsistent output.

1. Write `vision.md` — the project north star. What problem. For whom. What success looks like.
2. Write `architecture.md` — current system patterns, what is approved, what is banned, ADR format.
3. Write `db.md`, `backend.md`, `frontend.md` — current philosophy and conventions, not aspirational ones.
4. Write `testing.md` — current quality expectations.
5. Write `security.md` — non-negotiables and the three risk tier definitions.

**Note:** Write these documents to describe the system as it IS, not as you wish it were. You can add aspirational sections, but label them clearly. Skills that reference these documents need to understand reality.

### 9.2 Phase 2 — Seed the Impl-Log

The index files start empty and that is fine for a new project. For an existing codebase, seed them with current state:

1. Write `db/index.md` — current schema inventory. Tables, key relationships, notable patterns (Neo4j graph model for this project).
2. Write `backend/index.md` — current service map. Key services, API surface, integration points.
3. Write `frontend/index.md` — component inventory, current state management approach, design system in use.
4. Write `architecture/index.md` — current system topology. The system brain. This is the most important one.
5. Write `security/index.md` — current threat surface assessment. Known risks.

Log files can start empty. The index files need to reflect reality from day one.

### 9.3 Phase 3 — Audit Existing Rules and Skills

If you have existing `.cursorrules`, `.cursor/rules/`, or other configuration:

1. **Archive everything first:** Move existing files to `.cursor/archive/` before changing anything.
2. **Categorize each item:** For each existing rule or instruction, ask: is this a universal invariant (Rule), domain expertise (Skill), workflow sequence (Command), or project context (design-principles)? Most existing rules are actually skills or design principles.
3. **Migrate to the right location:** Unconditional invariants → `.cursor/rules/` (this project uses a single file `multi-persona-workflow.mdc`). Domain expertise → relevant `.cursor/skills/[persona]/SKILL.md`. Workflow instructions → `.cursor/commands/`. Project context → design-principles.
4. **Discard what does not belong:** Many existing rules are workarounds for poor context management. With skills and proper context loading, many become unnecessary.
5. **Keep Rules concise:** This project uses one rule file with 7 bullets. If you cannot fit all true invariants there, you are putting the wrong things in Rules.

### 9.4 Phase 4 — Author the Skills

Write skills in this order — the dependencies flow downward:

1. Architect skill — depends on `architecture.md` and the index files
2. Product Manager skill — depends on `vision.md`
3. Dev Lead skill — depends on `architecture.md` and `backend.md`
4. DB Specialist skill — depends on `db.md` and `db/index.md`
5. App Builder Actions Developer skill — depends on its `references/` (platform, solution-patterns, application-points), `backend.md`, `backend/index.md`
6. App Builder Frontend Developer skill — depends on its `references/` (unified-shell, application-points), `frontend.md`, `frontend/index.md`
7. App Builder MCP Developer skill — depends on `mcp-setup.md`; archive MCP story when relevant
8. Test Engineer skill — depends on `testing.md`
9. Security Expert skill — depends on `security.md` and `risk-register.md`
10. Orchestrator skill — depends on all of the above

**Note:** Write each skill's description field last. Read the full skill body first, then write a routing description that uses the keywords someone would actually use when asking the agent to do that kind of work. See [persona-skill-authoring.md](./persona-skill-authoring.md).

### 9.5 Phase 5 — Define the Commands

Start with the story lifecycle commands before the impl commands. Validate that `/plan-1` through `/plan-3` works end-to-end on a low-stakes test story before building out the task-level commands.

### 9.6 What Goes to Archive

Move to `.cursor/archive/` rather than deleting:

- All previous `.cursorrules` files
- Previous rules that became skills or design principles
- Experimental commands that did not make the cut
- Previous persona attempts from earlier workflow iterations

The archive is not used by the system — it is a reference for understanding what was tried and why things changed. Label each archived file with a one-line note at the top explaining what it was and why it was replaced.

### 9.7 AGENTS.md

Keep `.cursor/AGENTS.md` as a lightweight map (~50 lines): pointers to constitution, impl-log, developer-setup, skills path, commands, rules. Do not dump process or learnings there. Patterns, decisions, and learnings belong in design-principles or impl-log.

---

## 10. Quick Reference

### 10.1 Decision Framework — Where Does This Belong?

| Question | Answer |
|----------|--------|
| Is it unconditional? Always applies regardless of context? | **Rule** (`.cursor/rules/`) |
| Is it domain expertise for a specific role? | **Skill** (`.cursor/skills/[persona]/SKILL.md`) |
| Is it workflow sequence or process logic? | **Command** (`.cursor/commands/`) |
| Is it project context or guiding philosophy? | **design-principles/** |
| Is it current system state for fast context loading? | **impl-log index file** |
| Is it historical narrative for deep research? | **impl-log log file** |

### 10.2 Context Loading by Persona

| Persona | Principle Files | Index Files | Task Files |
|---------|-----------------|-------------|------------|
| Product Manager | `vision.md` | — | story-plan.md (writing) |
| Architect | `architecture.md` + `vision.md` | All domain indexes | story-plan.md, impl/plan.md |
| Dev Lead | `architecture.md` + `backend.md` | architecture/index.md | story-plan.md, impl/plan.md |
| DB Specialist | `db.md` | db/index.md | task-plan.md (DB section) |
| App Builder Actions Developer | skill references (platform, solution-patterns, application-points), `backend.md` | backend/index.md | task-plan.md (reads DB first) |
| App Builder Frontend Developer | skill references (unified-shell, application-points), `frontend.md` | frontend/index.md | task-plan.md (reads backend first) |
| App Builder MCP Developer | `mcp-setup.md` | (as applicable) | MCP tool/resource specs, deployment notes |
| Test Engineer | `testing.md` | test/index.md | task-plan.md (reads all first) |
| Security Expert | `security.md` | security/index.md + risk-register | story-plan + task-plan |
| Orchestrator | — | All domain indexes | Full current artifact |

See [persona-and-command-reference.md](./persona-and-command-reference.md) for when-to-use and leverage patterns.

### 10.3 Status Line State Machine

| Status | Valid Next Command | Who Sets It |
|--------|-------------------|-------------|
| Ready for Architect Review | `/plan-2` (auto) | PM skill |
| Awaiting Risk Disposition | Human fills Disposition fields | Security Expert skill |
| BLOCKED - Tier 1 Security Finding | Resolve finding, then `/plan-2` | Security Expert skill |
| Needs PM Clarification | `/plan-debate` | Architect or Dev Lead skill |
| Deadlocked - Human Decision Required | Human decides, `/plan-debate` round-n | Debate command |
| Ready for Ratification | `/plan-3` | Review command (clean path) |
| Implementation Planning | `/dev-1` | Cursor User |
| Task Ready for Review | Human reviews, `/dev-3` | Task kickoff command |
| Task Complete | `/dev-3` or next task | Cursor User |
| Epic Ready for Review | `/epic-2` (auto) | PM + Architect |
| Epic Awaiting Risk Disposition | Human fills Disposition fields | Security Expert skill |
| Epic BLOCKED - Tier 1 Security Finding | Resolve finding | Security Expert skill |
| Epic Ready for Ratification | User ratifies in `/epic-2` | Review command (clean path) |
| Epic In Progress | `/plan-1` for child stories | `/epic-2` |
| Epic Stories Complete | `/epic-3` | When all stories done |

Exact transitions are enforced by the command files and workflow rule. See individual command definitions in `.cursor/commands/`.

---

## 11. In-Repo References & Confluence Sync

### In-Repo Docs

| Doc | Purpose |
|-----|---------|
| [docs/README.md](../README.md) | Docs structure. |
| [developer-setup/README.md](./README.md) | Developer-setup index. |
| [persona-and-command-reference.md](./persona-and-command-reference.md) | When to use which persona/command; leverage patterns. |
| [persona-skill-authoring.md](./persona-skill-authoring.md) | How to create/update SKILL.md and commands. |
| [mcp-setup.md](./mcp-setup.md) | MCP catalog and troubleshooting. |
| [.cursor/skills/app-builder-actions-developer/references/](../../.cursor/skills/app-builder-actions-developer/references/), [app-builder-frontend-developer/references/](../../.cursor/skills/app-builder-frontend-developer/references/) | App Builder reference: platform, solution-patterns, application-points, unified-shell. Canonical content under skills; optional stubs in developer-setup. |
| [odp-quick-reference.md](./odp-quick-reference.md) | ODP types, commands, relationships. |
| [RELATIONSHIP_VOCABULARY.md](./RELATIONSHIP_VOCABULARY.md) | Relationship vocabulary for ODP/graph. |
| [HTML_GENERATION_COMPLETE.md](./HTML_GENERATION_COMPLETE.md) | HTML generation completion notes. |
| [README-frameio-upload.md](./README-frameio-upload.md) | Frame.io upload script reference. |
| [.cursor/AGENTS.md](../../.cursor/AGENTS.md) | Lightweight context map. |
| [.cursor/rules/multi-persona-workflow.mdc](../../.cursor/rules/multi-persona-workflow.mdc) | Workflow constraints. |
| [.cursor/rules/typescript-standards.mdc](../../.cursor/rules/typescript-standards.mdc) | TypeScript standards. |
| [.cursor/rules/react-spectrum-ui.mdc](../../.cursor/rules/react-spectrum-ui.mdc) | React Spectrum UI rules. |
| [docs/design-principles/](../design-principles/) | Constitution. |
| [docs/impl-log/](../impl-log/) | System memory. |

### Confluence Update Checklist

When syncing this document to Confluence:

- [ ] Replace or merge Confluence "Developer Process" body with sections 1–11 (adjust tables/formatting for Confluence).
- [ ] Keep Confluence link to this file: `docs/developer-setup/developer-process.md`.
- [ ] Space key **dssea**, title **Developer Process**.
- [ ] Add "Last updated" from this file's git history or changelog if the space uses it.

**Confluence sync notes:**

- §2.1, §3.2, §4.1, §6.2, §9.4, §10.2 have been synced to the wiki to use **skill reference paths** (`.cursor/skills/app-builder-*-developer/references/` with platform.md, solution-patterns.md, application-points.md for actions; unified-shell.md, application-points.md for frontend). Design-principles no longer lists `app-builder-actions-patterns.md`; App Builder details live under skill references (see §2.1 note in this file). When syncing in future, keep those sections aligned with this file.

**Fix wiki links to GitHub** (many links on the Confluence page point to `http://...` or `https://wiki.corp.adobe.com/...` and are broken; they should point to this repo on GitHub):

- **Base URL:** `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/` (use `tree/main` for directories). Replace `main` with the repo default branch if different.
- **Broken patterns:** Links like `http://vision.md`, `http://SKILL.md`, `http://index.md`, etc. resolve nowhere. Links like `https://wiki.corp.adobe.com/persona-skill-authoring.md` or `https://wiki.corp.adobe.com/README.md` point at the wiki host, not the repo.
- **Correct targets:**

| Wiki currently shows / link text | Correct GitHub URL |
|----------------------------------|---------------------|
| vision.md, architecture.md, db.md, frontend.md, backend.md, react-spectrum-ui-patterns.md, testing.md, security.md | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/docs/design-principles/<filename>` |
| SKILL.md (in .cursor/skills/... ) | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/.cursor/skills/<persona>/SKILL.md` |
| index.md, log.md, plan.md, story-plan.md, task-plan.md, risk-register.md | In body text these are file names; if they are links, point to the relevant path under `docs/impl-log/`, `docs/stories/`, or `.cursor/` as appropriate. |
| AGENTS.md | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/.cursor/AGENTS.md` |
| multi-persona-workflow.mdc | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/.cursor/rules/multi-persona-workflow.mdc` |
| developer-process.md | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/docs/developer-setup/developer-process.md` |
| docs/README.md | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/docs/README.md` |
| persona-and-command-reference.md, persona-skill-authoring.md, mcp-setup.md, odp-quick-reference.md, app-builder-*.md, RELATIONSHIP_VOCABULARY.md, HTML_GENERATION_COMPLETE.md, README-frameio-upload.md, README.md | `https://github.com/OneAdobe/pattern_atlas_publishing/blob/main/docs/developer-setup/<filename>` |
| design-principles/ | `https://github.com/OneAdobe/pattern_atlas_publishing/tree/main/docs/design-principles` |
| impl-log/ | `https://github.com/OneAdobe/pattern_atlas_publishing/tree/main/docs/impl-log` |

- **Links that are already correct (do not change):** Confluence page link (wiki space dssea), GitHub repo links (OneAdobe/pattern_atlas_publishing, pattern_atlas_diagramming, pattern-atlas-ecosystem), Jira links, and in-page anchors (#1-philosophy..., etc.).

---

*Developer Process — {{PROJECT_NAME}} Publishing — multi-persona Cursor workflow. Source of truth: this file.*
