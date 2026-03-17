# /dev-2 — Task Kickoff

**Invoked by**: Auto from /dev-1, or Cursor User  
**Chains to**: Pause for human approval  
**Was**: /task-kickoff

## Invocation Modes

### Single task
```
/dev-2 $STORY_ID $TASK_ID
```
Plan and implement one task. Pause for review before moving to the next.

### All tasks (run everything)
```
/dev-2 $STORY_ID --all
```
Execute every task from `impl/plan.md` in dependency order without stopping between tasks. Still pause at the end for final review before `/dev-3`.

### Range
```
/dev-2 $STORY_ID $TASK_ID --continue
```
Start at `$TASK_ID` and run all remaining tasks in order without stopping between them.

## Load

graph-db-specialist, app-builder-actions-developer, app-builder-frontend-developer, test-engineer, security-expert (in order; each reads previous sections)

## Phases (per task)

1. Create docs/stories/$STORY_ID/impl/$TASK_ID/task-plan.md
2. DB writes section; Backend reads DB, writes; Frontend reads Backend, writes; Test reads all, writes; Security reviews all
3. Evaluate security (Tier 1 stop, Tier 2 disposition) — **stop and surface any Tier 1 risk immediately, even in --all mode**
4. Implement the task
5. In `--all` / `--continue` mode: auto-advance to next task; in single-task mode: pause for review

## Optional: Jira Transition

If the story has Jira subtasks mapped to this task, ask: "Transition $TASK_ID to In Progress in Jira? (y/n)" — use `transition_jira_status_by_name`.  
In `--all` mode, skip this prompt and proceed without transitioning subtasks.

## Pause Prompt

After completing a task (or all tasks), always end with a clear next-steps block:

---
**Next steps:**
- If the work looks good: run `/dev-3 $STORY_ID $TASK_ID` to archive this task and log it to the impl-log
- To implement the next task: run `/dev-2 $STORY_ID $NEXT_TASK_ID`
- To run all remaining tasks at once: run `/dev-2 $STORY_ID $NEXT_TASK_ID --continue`
- If all tasks are complete: run `/dev-3 $STORY_ID` to close out the story
---
