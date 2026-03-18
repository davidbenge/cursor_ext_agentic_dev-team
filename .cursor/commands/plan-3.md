# /plan-3 — Story Complete

**Invoked by**: Cursor User  
**Chains to**: Jira push, archive story artifacts, branch/commit/PR (standalone) or archive + Jira only (epic)  
**Was**: /story-complete

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Pre-check

- No open Tier 1 findings
- All Tier 2 have human Disposition
- Status: Ready for Ratification

## Execute

### 1. Jira and archive

**Jira — always execute (no prompt required):**
- Transition story to **Done** — `transition_jira_status_by_name` with `statusName: "Done"`
- Post implementation comment — `add_jira_comment` with:
  - ✅ Status + date + branch + commit ref
  - Summary of what was delivered (ACs resolved)
  - Key decisions
  - Security contracts propagated (if any)
  - Link to `docs/impl-log/stories/$STORY_ID/impl/plan.md`
- If transition fails (already Done, or invalid transition), note it and continue

**Archive:**
- Append summary to impl-log/architecture/log.md
- Update architecture/index.md in-place
- Move docs/stories/$STORY_ID/story-plan.md to docs/impl-log/stories/$STORY_ID/story-plan.md (or move docs/stories/$STORY_ID/impl/ to docs/impl-log/stories/$STORY_ID/ for impl-level complete)
- Delete docs/stories/$STORY_ID/ (now empty)

### 2. Branch, commit, and PR

**Epic-aware behavior:** When this story is part of an epic, skip all branching and PR steps below. Git operations for epic stories are handled at the epic level by `/epic-3`. All task work is committed directly to the `epic/<EPIC_ID>` branch with commit messages: `feat(<STORY_ID>): <short summary>`.

**Standalone story behavior** (not part of an epic):

- Create branch from stage: `git stash -u` (if uncommitted changes), `git checkout stage`, `git pull`, `git checkout -b <story-id-lowercase>` (e.g. `aarch-75`), `git stash pop`
- Resolve any merge conflicts from stash pop
- Stage all story-related changes (exclude .kombai, IDE artifacts): `git add` for modified and new files
- Commit: `git commit -m "feat(<STORY_ID>): <short summary>"`
- Push: `git push -u origin <branch>`
- Create PR: `gh pr create --base stage --title "feat(<STORY_ID>): <short summary>" --body "<PR body>"` (or open the URL from `git push` output). PR body: Jira link, summary of changes, testing notes, link to impl plan.
