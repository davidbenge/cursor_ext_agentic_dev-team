# /epic-2 — Epic Review and Kickoff

**Invoked by**: Auto from /epic-1, or Cursor User  
**Chains to**: Pause for user to begin story work

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Load

architect, dev-lead, security-expert skills (sequence)

## Phases

### 1. Architect Review

Review epic-plan for system-level impact of the full epic scope, cross-story coherence, and architectural risk of the combined change set. Write Architect Review section.

### 2. Dev Lead Review

Review for effort, coordination risks, story ordering feasibility. Write Dev Lead Review section.

### 3. Security Review

Review full epic surface area with risk tier table. Evaluate:

- Tier 1 → set status: BLOCKED; stop
- Tier 2 → set status: Awaiting Risk Disposition; stop for human
- Clean → set status: Ready for Ratification

### 4. Imported Epic Findings (Mode B only)

For imported epics, persona reviews explicitly call out any concerns with the pre-existing Jira story definitions:

- Unclear scope in specific stories
- Missing security considerations
- Architectural gaps or unstated assumptions
- Testing blind spots

These are written as review findings, not silent edits to the Jira content.

### 5. Ratification and Kickoff

On ratification by user:

- Create epic branch from `stage`: `git checkout stage && git pull && git checkout -b epic/<EPIC_ID>`
- Push epic branch: `git push -u origin epic/<EPIC_ID>`
- **Mode A**: Push new stories to Jira as children of epic via MCP
- **Mode B**: Stories already exist in Jira; update with any new findings or labels
- Set status: Epic In Progress

## Pause

Notify user. User begins story work with `/plan-1` for individual stories. All story and task work commits directly to the `epic/<EPIC_ID>` branch.
