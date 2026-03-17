# Persona Skill and Command Authoring

> **Purpose**: Process and templates for creating or updating persona SKILL.md files and commands so they are consistent, discoverable, and effective in the multi-persona workflow.

---

## Overview

**Persona skills** are role-based instructions in `.cursor/skills/[persona]/SKILL.md`. **Commands** are user-invoked workflows in `.cursor/commands/*.md`. Personas live under `skills/` for first-class Cursor discovery (auto-loaded when relevant). A consistent authoring process and clear structure ensure they are discoverable, maintainable, and effective.

- **Personas**: Role-bound instructions loaded when that persona is relevant or invoked. See [persona-and-command-reference](persona-and-command-reference.md) for discovery and when-to-use.
- **Commands**: Step-by-step workflows (e.g. `/plan-new-backlog-story`, `/dev-completed-implementation`). The user explicitly invokes them.
- **Templates**: Ready-to-use structures below for new personas and commands.

---

## Authoring Workflow

Follow four phases: **Discovery** → **Design** → **Implementation** → **Verification**.

### Phase 1: Discovery

**Goal**: Clarify purpose, scope, and when the persona/command should be used.

- [ ] **Purpose**: What specific task or workflow does this support?
- [ ] **Scope**: Persona (role-based) or command (explicit workflow)?
- [ ] **Triggers**: When should the agent apply this? (e.g. “when planning features”, “when user runs /plan-new-backlog-story”)
- [ ] **Key domain knowledge**: What must the agent know that it wouldn’t already infer?
- [ ] **Output/format**: Any required templates, formats, or styles?
- [ ] **Existing patterns**: Reference [design-principles](../design-principles/) and [persona-and-command-reference](persona-and-command-reference.md).

### Phase 2: Design

**Goal**: Define name, description, and structure before writing.

- [ ] **Name**: Persona: lowercase, single word or hyphenated (e.g. `product-manager`, `graph-db-specialist`, `app-builder-actions-developer` for platform-specific roles). Command: lowercase, hyphenated; Story Planner commands use `plan-*`, Developer commands use `dev-*` or `review-*` (e.g. `plan-new-backlog-story`, `dev-completed-implementation`, `review-persona-skills`).
- [ ] **Description**: One line, third person, WHAT + WHEN; include trigger terms (see [Description best practices](#description-best-practices)).
- [ ] **Sections**: Persona: Principles, Role Boundary, **Instructions** (phases as subsections if needed). **When to Use** (optional; discovery can rely on frontmatter `description`). Command: Invoked by, Chains to; then either Pre-check + Execute, or Load + Phases (see [Command structure](#command-structure)).
- [ ] **Supporting files**: Personas may reference design-principles, impl-log, or other docs. Keep SKILL.md under ~500 lines.

### Phase 3: Implementation

**Goal**: Create or update the file(s).

- [ ] **Persona**: Create `.cursor/skills/[persona]/SKILL.md` with required frontmatter and body sections.
- [ ] **Command**: Create `.cursor/commands/[name].md` with Invoked by, Chains to; then Pre-check + Execute, or Load + Phases (see [Command structure](#command-structure)).
- [ ] **AGENTS.md**: Update `.cursor/AGENTS.md` if adding a new persona or command (keep the list current).
- [ ] Keep content concise; link to design-principles or impl-log for detail.

### Phase 4: Verification

**Goal**: Ensure the artifact matches spec and is ready for use.

- [ ] **Persona**: Valid YAML frontmatter with `name` and `description`. Third-person description with WHAT + WHEN (drives discovery). Sections include **Instructions**; **When to Use** optional. Under ~500 lines.
- [ ] **Command**: Clear sections (Invoked by, Chains to; then Pre-check + Execute or Load + Phases). No ambiguity in steps.
- [ ] No anti-patterns (see [Anti-patterns](#anti-patterns)).
- [ ] Terminology consistent with design-principles and other personas/commands.
- [ ] AGENTS.md references the new persona/command if applicable.

---

## Best practice alignment (Cursor + SKILL.md spec)

External guidance (Cursor docs, [agentskills.io](https://agentskills.io/), create-skill) recommends:

- **When to Use** in-body (optional): A short section can reinforce trigger scenarios when the full skill is loaded; discovery can rely on frontmatter `description` (WHAT + WHEN + trigger terms).
- **Instructions**: Step-by-step directions; domain-specific conventions and best practices in one place.
- **Progressive disclosure**: Discovery (name/description) → Activation (full SKILL.md) → Execution (follow instructions, load referenced docs). Keep SKILL.md under ~500 lines; link to design-principles or skill `references/` for detail. App Builder–specific reference lives under `.cursor/skills/app-builder-*/references/`; design-principles holds only cross-cutting invariants.
- **Concise**: Assume the agent is capable; only add context it wouldn’t already have.

**Project strategy (unchanged):** We keep **Principles** (what to load first), **Role Boundary** (does / does not), and **Output Contract**. We use **Instructions** (and optionally **When to Use**) so skills match best practice without dropping our values: constitution-first loading, specialist boundaries, procedural (not encyclopedic) body, and single source of truth in design-principles/impl-log.

---

## Persona SKILL.md Structure

### Required frontmatter

Every persona SKILL.md must have YAML frontmatter:

| Field | Requirements | Purpose |
|-------|--------------|---------|
| `name` | Lowercase, matches directory name (e.g. `product-manager` in `skills/product-manager/`) | Unique identifier |
| `description` | Non-empty, max 1024 chars | Used for discovery; WHAT + WHEN |

Example:

```yaml
---
name: product-manager
description: Use when planning new features, writing user stories, defining acceptance criteria, assessing backlog overlap, or interviewing stakeholders about product needs.
---
```

### Body sections

- **Principles**: What to load (docs, impl-log), key constraints. *(Project: constitution-first.)*
- **Role Boundary**: What the persona **does** and **does NOT** do.
- **Instructions**: Step-by-step guidance for the agent; domain-specific conventions and best practices. Use phases as subsections (e.g. "Phase 1: Discovery") where the persona has multiple phases.
- **When to Use** (optional): Short bullets reinforcing trigger scenarios; aligns with description. Discovery can rely on frontmatter `description` alone, so this section is optional. If present, keep to 3–5 bullets; detail stays in [persona-and-command-reference](persona-and-command-reference.md). *Existing variants in codebase: "When to Use", "When to Use This Skill", "When to Use This Persona".*
- **Anti-Patterns** (optional): What to avoid.
- **Output Contract** (optional): Expected artifact format.

---

## Persona Template

Use for new personas in `.cursor/skills/[persona]/SKILL.md`:

```markdown
---
name: persona-name
description: One-line third-person: WHAT the persona does. Use when [trigger 1], [trigger 2], or when the user mentions [terms].
---

# Persona Name Skill

## Principles

- Load: [relevant docs or impl-log indexes]
- [Key constraints]

## Role Boundary

**Does**: [Scope of responsibility].  
**Does NOT**: [Out of scope].

## When to Use (optional)

- Use this skill when [trigger 1].
- This skill is helpful for [trigger 2], [trigger 3], or when the user mentions [terms].

*(Omit this section if frontmatter `description` already gives WHAT + WHEN; discovery relies on it.)*

## Instructions

1. First step or principle.
2. Second step or principle.
3. [Add steps; include domain conventions and best practices as needed.]

[Optional: Phase 2 as subsection with its own steps.]

## Anti-Patterns

- [What to avoid]

## Output Contract

[Expected artifact or outcome]
```

**Frontmatter guidance**:
- **name**: Lowercase, hyphenated, matches directory (e.g. `graph-db-specialist`).
- **description**: Third person, WHAT + WHEN, trigger terms; under 1024 chars.

---

## Command Structure

Commands are markdown files in `.cursor/commands/[name].md`. Every command has **Invoked by** and **Chains to**; the body may follow either of two patterns.

**Common header** (all commands):

| Section | Purpose |
|---------|---------|
| **Invoked by** | Who/what triggers this (user, another command) |
| **Chains to** | Commands or actions that run after |

**Pattern A — Pre-check + Execute** (e.g. `/dev-completed-implementation`, `/dev-completed-implementation-task`, `/dev-start-implementation-task-plan`): Use when the command has clear preconditions and a single execution sequence.

| Section | Purpose |
|---------|---------|
| **Pre-check** | Conditions that must be true before executing |
| **Execute** | Step-by-step actions |

**Pattern B — Load + Phases** (e.g. `/plan-new-backlog-story`): Use when the command loads a persona or context first, then runs named phases (Discovery, Research, Write, etc.). May include **Auto-chain** for the next command.

| Section | Purpose |
|---------|---------|
| **Load** | Persona skill or context to load (e.g. `product-manager skill`) |
| **Phases** | Numbered phases with steps |
| **Auto-chain** | (Optional) Next command to run automatically (e.g. `/plan-story-review-with-tech-team $STORY_ID`) |

---

## Command Template

Use for new commands in `.cursor/commands/[command-name].md`:

```markdown
# /[command-name]

**Invoked by**: Cursor User when [trigger scenario]  
**Chains to**: [next command or action, if any]

## Pre-check

[Conditions that must be true]

## Execute

1. First step.
2. Second step.
3. [Add steps; reference other commands if chained]
```

**Naming**: Use lowercase with hyphens. Story Planner commands: `plan-*` (e.g. `plan-new-backlog-story`, `plan-story-complete`). Developer commands: `dev-*` or `review-*` (e.g. `dev-completed-implementation`, `dev-completed-implementation-task`, `review-persona-skills`). The user invokes as `/plan-new-backlog-story`, `/dev-completed-implementation-task`, etc.

---

## Description Best Practices

The description drives **discovery** for personas.

1. **Third person**  
   The description is used in context.  
   - ✅ “Use when planning features, writing user stories, defining acceptance criteria.”  
   - ❌ “I plan features.” / “You can use me to plan.”

2. **WHAT and WHEN**  
   - **WHAT**: What the persona does.  
   - **WHEN**: When to use it (trigger scenarios).  
   Example: “Use when writing Cypher queries, debugging Neo4j, or implementing graph traversals.”

3. **Trigger terms**  
   Include words/phrases that should trigger the persona (e.g. “Neo4j”, “Cypher”, “OpenWhisk”, “plan-new-backlog-story”).

4. **Size**  
   One concise line; stay within 1024 characters.

---

## Anti-Patterns

Avoid these to keep personas and commands consistent:

1. **Vague names**  
   - ✅ `graph-db-specialist`, `dev-completed-implementation-task`  
   - ❌ `helper`, `utils`, `do-thing`

2. **Description in first or second person**  
   Keep in third person for system/context use.

3. **Overloading a persona**  
   One role, one scope. Split responsibilities into separate personas if needed.

4. **Deep nesting in SKILL.md**  
   Link to design-principles or impl-log; keep SKILL.md under ~500 lines.

5. **Inconsistent terminology**  
   Align with design-principles and other personas (e.g. “impl-log” not “implementation log” in docs).

6. **Pattern A commands without Pre-check**  
   If a command has preconditions (e.g. “work reviewed”), use Pattern A and state them in Pre-check.

---

## References

- [Persona and command reference](persona-and-command-reference.md) — Discovery, when to use, leverage patterns
- [Design principles](../design-principles/) — Constitution and invariants
- [AGENTS.md](../../.cursor/AGENTS.md) — Context map (update when adding personas/commands)
- Cursor documentation: [Skills](https://cursor.com/docs/skills) for official SKILL.md conventions
