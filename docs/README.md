# {{PROJECT_NAME}} — Documentation

## Structure

| Directory | Purpose |
|-----------|---------|
| [design-principles/](./design-principles/) | Constitution — invariants, conventions, architecture principles for your project |
| [impl-log/](./impl-log/) | System memory — current state (index) + history (log) per domain (created during development) |
| [developer-setup/](./developer-setup/) | Workflow setup, MCP config, developer process, persona reference |

## Docs at Root

- `CHANGELOG.md` — Version history (create when your project matures)
- `CONTRIBUTING.md` — Contribution guidelines

## Ephemeral

`docs/stories/` — In-flight story-plan and task-plan files. Not committed. Deleted on completion.

## Note

The `impl-log/` directory and its per-domain subdirectories (`architecture/`, `backend/`, `db/`, `frontend/`, `security/`, `test/`) are created automatically as you work through the `/dev-*` and `/plan-*` command pipeline. You do not need to create them manually.
