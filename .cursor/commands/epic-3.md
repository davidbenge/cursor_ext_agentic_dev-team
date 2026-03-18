# /epic-3 — Epic Complete

**Invoked by**: Cursor User when all epic stories are completed  
**Chains to**: Jira update, archive, PR to stage

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Pre-check

- All child stories are complete (status check via Jira MCP or local tracking)
- No open Tier 1 findings at epic level
- All Tier 2 have human Disposition

## Execute

### 1. Impl-log and archive

- Write epic-level summary to impl-log/architecture/log.md
- Update architecture/index.md in-place
- Move docs/epics/$EPIC_ID/ to docs/impl-log/epics/$EPIC_ID/

### 2. Branch and PR

- Stage all epic-related changes on `epic/<EPIC_ID>` branch
- Commit: `git commit -m "feat(<EPIC_ID>): <epic summary>"`
- Push: `git push origin epic/<EPIC_ID>`
- Create PR: `gh pr create --base stage --title "feat(<EPIC_ID>): <epic summary>" --body "<PR body>"`. PR body: Jira epic link, summary of all stories completed, testing notes, links to impl plans.

### 3. Jira

- Update Jira epic status via MCP
- Report clean state to user
