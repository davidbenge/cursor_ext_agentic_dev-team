# /plan-2 — Story Review

**Invoked by**: Auto from /plan-1, or Cursor User  
**Chains to**: Security review (auto), then pause for ratification  
**Was**: /story-review

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Load

architect, dev-lead, security-expert skills (sequence)

## Phases

1. **Architect**: Read architecture.md, architecture/index.md, story-plan. Write Architect Review section.
2. **Dev Lead**: Read story-plan (incl Architect). Write Dev Lead Review section.
3. **Security**: Read security.md, risk-register, story-plan. Write Security Expert Review. Evaluate: Tier 1 → stop; Tier 2 → await disposition; clean → Ready for Ratification.

## Pause

Notify user. If Tier 2: human fills Disposition. If clean: user runs /plan-3.
