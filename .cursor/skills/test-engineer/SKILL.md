---
name: test-engineer
description: Use when defining test strategy, coverage plans, or edge cases for stories.
---

# Test Engineer Skill

## References

- **Basic usage**: Instructions in this file (read testing docs and impl-log, read specialist sections, write Test Engineer section). For UI changes, include Frame.io artifact upload per Artifact upload section below.
- **Constitution**: `docs/design-principles/testing.md`
- **System state**: `docs/impl-log/test/index.md`, `docs/impl-log/test/log.md`
- **Domain skills MCP**: When defining test strategies for domain-specific features (App Builder, Workfront, AEM, etc.), call `list_skills` on the `adobe-extensibility-mcp` MCP server and `load_skill` for the relevant domain to understand platform-specific edge cases and test patterns. See [adobe_extensibility_mcp](https://github.com/davidbenge/adobe_extensibility_mcp).

## Role Boundary

**Does**: Write test section of task-plans. Edge cases, integration tests, coverage.  
**Does NOT**: Write implementation code.

## Instructions

- Read testing.md and test/index.md
- Scan `docs/impl-log/test/log.md` entry headers for entries related to the current task; if a relevant entry exists and links to archived detail, load the linked artifact for context.
- Read all specialist sections (DB, Backend, Frontend) before writing
- Write Test Engineer section: coverage plan, edge cases, verification steps

## Artifact upload (UI changes)

When a story includes **UI changes**, the test-engineer (or the process they define) includes:

- Running the Frame.io upload script for screenshots and/or videos captured during front-end testing (e.g. from Playwright or manual runs), passing **story id and name** (e.g. `--story-id=AARCH-123 --story-name="Feature complete"`) so uploaded file names start with the story for traceability.
- Ensuring artifacts are uploaded to the configured Frame.io folder (e.g. sprint folder) and referenced where needed (e.g. in impl-log/test or task completion notes).

Script: `scripts/dev/upload-frameio-artifacts.js`. Env vars and usage: see `docs/developer-setup/README-frameio-upload.md`.

## Output Contract

Test section in task-plan; impl-log/test entries on completion.
