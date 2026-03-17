# {{PROJECT_NAME}} — AI Agent Context

> **Purpose**: Lightweight pointer to project knowledge. Cursor loads this automatically.

**Context principle**: Do not load the destinations below by default. Personas load only what their role requires. Use this map when orienting or when a specific need arises—not as a trigger to load everything.

## Where to Look

- **Constitution** (invariants, conventions): [docs/design-principles/](../docs/design-principles/) — domain-specific implementation details live under `.cursor/skills/[persona]/references/`.
- **Current system state**: [docs/impl-log/](../docs/impl-log/)
- **Developer setup**: [docs/developer-setup/](../docs/developer-setup/)
- **Persona skills**: `.cursor/skills/[persona]/SKILL.md` (product-manager, architect, dev-lead, test-engineer, security-expert, orchestrator — plus any domain specialists you add) — first-class Cursor skills, auto-discovered
- **Commands**: `.cursor/commands/` — plan-1/2/3, dev-1/2/3, epic-1/2/3, debates, log-entry, index-update, skill-review, setup-dev-process-new, setup-dev-process-existing
- **Rules**: `.cursor/rules/multi-persona-workflow.mdc`

## Workflow

Multi-persona AI development. Numbered command groups: plan (story), dev (implementation), epic (epic). Use `/plan-1` for new story ideas. Use `/epic-1` for epics. Branches target `stage`.

**Keep this file under ~50 lines.** Patterns, decisions, and learnings belong in design-principles or impl-log—not here.
