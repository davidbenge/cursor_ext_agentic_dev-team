# Design Principles — Instructions

This directory is the **constitution** of your project. Every persona reads from here to understand what is invariant. Keep entries short, authoritative, and non-negotiable.

## How to Fill In Each File

### `vision.md`
The "why" of the project. Defines the problem, success metrics, non-goals, and target users. Personas use this to:
- Calibrate the scope of any feature request
- Identify when a proposed solution is out of scope
- Understand who is affected by their decisions

**Fill in**: Problem statement, success metrics, non-goals, target users.

### `architecture.md`
The approved technology stack and banned patterns. Personas use this to:
- Know which libraries and platforms they are allowed to use
- Know what is explicitly forbidden (and why)
- Understand branching and release conventions
- Follow script organization conventions

**Fill in**: Approved tech stack, Jira project key, banned patterns, branching strategy.

### `backend.md`
Backend service conventions. Personas use this to:
- Structure actions/handlers/services consistently
- Handle errors and timeouts correctly
- Know who owns what

**Fill in**: Runtime platform, handler structure, timeout tiers, error handling rules.

### `frontend.md`
Frontend conventions. Personas use this to:
- Choose components correctly
- Manage state appropriately
- Avoid mixing browser/server APIs

**Fill in**: UI framework, component rules, state management approach.

### `db.md`
Database conventions. Personas use this to:
- Write correct, safe queries
- Handle connections properly
- Know the schema ownership model

**Fill in**: Database technology, schema conventions, connection handling, migration policy.

### `security.md`
Security non-negotiables. Personas use this to:
- Classify risk on every story (Tier 1/2/3)
- Know the auth model
- Enforce secure coding invariants

**Fill in**: Risk tier definitions (keep as-is), non-negotiables, auth model details.

### `testing.md`
Testing standards. Personas use this to:
- Know what test coverage is required for any task to be marked complete
- Know which tools to use
- Know the release gate criteria

**Fill in**: Test stack, coverage expectations, release gate criteria.

## Tips

- **Short beats long**: Each file should be scannable in 30 seconds. Deep implementation details belong in skill `references/` files, not here.
- **Commands, not suggestions**: Use imperative language ("Use parameterized queries", not "You should consider using...").
- **Keep `_INSTRUCTIONS.md` around or delete it**: This file is for the framework author — delete it before publishing your project's design principles to production teams, or keep it as a living authoring guide.
