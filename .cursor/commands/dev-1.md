# /dev-1 — Implementation Plan Kickoff

**Invoked by**: Cursor User  
**Chains to**: /dev-2 for first task (auto)  
**Was**: /plan-kickoff

## Load

architect, dev-lead skills

## Epic Awareness

When the story is part of an epic, also load `docs/epics/$EPIC_ID/epic-plan.md` for cross-story contracts so task planning references cross-story interfaces.

## Phases

1. Read Jira story (MCP), design-principles/architecture.md, all impl-log index files
2. Create docs/stories/$STORY_ID/impl/plan.md: tasks, dependency order, interface contracts
3. Chain to /dev-2 for each task in order

## Optional: Jira Transition

If the story has a Jira issue (identified in step 1), transition it to **In Progress** using `transition_jira_status_by_name`. Ask the user first — "Transition $STORY_ID to In Progress in Jira? (y/n)".

## Pause

When all task-plans written and security reviewed. User reviews and approves.
