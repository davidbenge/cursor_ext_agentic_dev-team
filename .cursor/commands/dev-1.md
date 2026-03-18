# /dev-1 — Implementation Plan Kickoff

**Invoked by**: Cursor User  
**Chains to**: /dev-2 for first task (auto)  
**Was**: /plan-kickoff

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Pre-flight Check

Before doing anything else, check whether `docs/stories/$STORY_ID/story-plan.md` exists.

**If it does NOT exist**, stop immediately and respond with something like:

> Oh wow, bold move. You skipped `/plan-1` and came straight here — very "measure twice, cut once" energy, except you skipped the measuring part entirely.
>
> You need to run `/plan-1` first so we actually have a story plan to implement. Wild concept, I know.
>
> Need me to spell it out? `/plan-1 $STORY_ID`. Or just say the word and I'll run it for you. Your call, speed racer.

Do not proceed past this check if the story-plan is missing.

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
