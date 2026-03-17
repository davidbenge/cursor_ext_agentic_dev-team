# /dev-3 — Task Complete

**Invoked by**: Cursor User when task work is reviewed and passing  
**Chains to**: /log-entry, /index-update (auto) then archive task folder  
**Was**: /task-complete

## Pre-check

User confirms work reviewed and passing

## Execute

- Chain to /log-entry for each domain (db, backend, frontend, test, security)
- Chain to /index-update for each domain
- Move docs/stories/$STORY_ID/impl/$TASK_ID/ to docs/impl-log/stories/$STORY_ID/$TASK_ID/
- If all tasks done: prompt user to run /plan-3 at impl level

## Jira — Always Execute (no prompt required)

When the story has a Jira ID (identifiable from the story plan header or impl/plan.md):

**Story-level close (all tasks done):**
1. Transition story to **Done** — `transition_jira_status_by_name` with `statusName: "Done"`
2. Post implementation comment — `add_jira_comment` with a structured body covering:
   - ✅ Status + date + branch + commit ref
   - What was delivered (ACs checked off)
   - Key decisions (non-obvious trade-offs)
   - Security contracts propagated (if any)
   - Files changed summary
   - Link to impl plan: `docs/impl-log/stories/$STORY_ID/impl/plan.md`

**Task-level close (subtask):**
1. Transition Jira subtask to Done if mapped — `transition_jira_status_by_name`
2. Post brief task comment noting what was done and any contracts established

Do not ask the user — execute automatically. If the transition fails (e.g. no valid transition to Done from current state), note the error in your response and continue.
