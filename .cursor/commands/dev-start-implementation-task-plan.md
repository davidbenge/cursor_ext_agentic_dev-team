# /dev-start-implementation-task-plan

**Invoked by**: Auto from dev-start-implementation-plan, or Cursor User  
**Chains to**: Pause for human approval

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Load

graph-db-specialist, app-builder-actions-developer, app-builder-frontend-developer, test-engineer, security-expert (in order; each reads previous sections)

## Phases

1. Create docs/stories/$STORY_ID/impl/$TASK_ID/task-plan.md
2. Include: **Persona(s) responsible** (so task can be spun up with the right skill); **Work items (todo list)** — checklist of concrete steps; **Sign-off** table (role/persona, checkbox, notes). See developer-process §6.3.
3. DB writes section; Backend reads DB, writes; Frontend reads Backend, writes; Test reads all, writes; Security reviews all
4. Evaluate security (Tier 1 stop, Tier 2 disposition)
5. Pause: user reviews task-plan
