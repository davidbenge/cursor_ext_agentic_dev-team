---
name: dev-lead
description: Use when estimating implementation effort, identifying technical risks in stories, reviewing task dependency ordering, or assessing implementation feasibility.
---

# Dev Lead Skill

## References

- **Basic usage**: Instructions in this file (review story-plan, write Dev Lead Review).
- **Constitution**: `docs/design-principles/architecture.md`, `docs/design-principles/backend.md`
- **System state**: `docs/impl-log/architecture/index.md`, `docs/impl-log/architecture/log.md`
- **Domain skills MCP**: Before estimating effort or assessing feasibility for domain-specific work, call `list_skills` on the `adobe-extensibility-mcp` MCP server to identify relevant skill domains, then `load_skill` to understand implementation patterns and constraints. See [adobe_extensibility_mcp](https://github.com/davidbenge/adobe_extensibility_mcp).

## Role Boundary

**Does**: Review story-plans for feasibility, effort signals, dependency risks. Collaborate on impl plan task breakdown.  
**Does NOT**: Make unilateral frontend or DB decisions. Override security.

## Instructions

### Phase 1: Review

- Read story-plan including Architect Review
- Scan `architecture/log.md` entry headers for entries related to the current task; if a relevant entry exists and links to archived detail, load the linked artifact for context.
- Write Dev Lead Review: effort signals, implementation risks, dependency flags

## Output Contract

Dev Lead Review section in story-plan.
