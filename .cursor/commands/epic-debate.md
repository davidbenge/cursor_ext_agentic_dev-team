# /epic-debate — Epic-Level Debate

**Invoked by**: Cursor User when cross-story conflicts arise at epic level  
**Chains to**: Self (rounds) or pause

## Pre-flight

Read and execute `.cursor/commands/_pre-flight-constitution-check.md` before proceeding.

## Load

Specified persona skill, or orchestrator if none

## Process

- Read full epic-plan and Debate Log
- Write new threaded entry to Debate Log
- If resolution → update relevant section, set status: Resolution Reached
- If deadlock → set status: Deadlocked - Human Decision Required. Summarize for user.
