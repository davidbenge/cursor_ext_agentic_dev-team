# Cursor Agentic Framework

A production-grade multi-persona AI development workflow for Cursor — extracted from a live enterprise project and ready to drop into any new or existing codebase.

## What This Is

A complete **agentic dev team** that lives inside your Cursor project. Rather than talking to a single AI assistant, you orchestrate a roster of named personas — each with a distinct role, skill set, and scope — through a structured command pipeline. The AI team plans, debates, implements, logs, and reviews work the same way a real team does.

**The core insight**: giving the AI a named role, a scoped skill file, and a structured workflow produces dramatically more consistent, reviewable, and correct output than an open-ended prompt.

## The Team

### Core Personas (ready to use as-is)

| Persona | Role |
|---|---|
| **Orchestrator** | Meta-coordinator — unsticks deadlocks, provides top-down workflow view |
| **Product Manager** | Story writing, acceptance criteria, stakeholder interviews, backlog management |
| **Architect** | ADRs, system boundary decisions, trade-off analysis, sequence diagram review |
| **Dev Lead** | Effort estimation, task ordering, technical risk identification |
| **Security Expert** | Threat surface review, auth concerns, data exposure risk assessment |
| **Test Engineer** | Test strategy, coverage plans, edge case definition |

### Domain Specialist Personas (examples)

Adobe platform domain skills are provided by the **[Adobe Extensibility MCP](https://github.com/davidbenge/adobe_extensibility_mcp)** server — already pre-wired in `.cursor/mcp.json`. When your agents need Adobe-specific patterns, the MCP delivers them on demand:

| MCP Skill | Coverage |
|---|---|
| `app-builder-actions` | App Builder backend actions on I/O Runtime |
| `app-builder-frontend` | React + Adobe React Spectrum UI for extension points |
| `workfront-extension` | Workfront product extension registration |
| `workfront-tasks-api` | Workfront Tasks REST API (CRUD, bulk, queries) |
| `workfront-issues-api` | Workfront Issues/Requests REST API |
| `workfront-forms-api` | Workfront custom forms and parameterValues |
| `workfront-projects-api` | Projects, Portfolios, Programs, Milestones |
| `workfront-events-api` | Workfront Event Subscriptions (webhooks) |
| `workfront-documents-api` | Document upload, versioning, organization |
| `workfront-approvals-api` | Approval Processes — decisions, routes, status |

Additional local example personas in `.cursor/skills/examples/` (graph-db-specialist, ims-login, app-builder-mcp-developer) serve as templates for building your own domain-specific skills for non-Adobe platforms. See `.cursor/skills/examples/README.md` for how to adapt one.

## The Workflow

Three numbered command pipelines cover the full SDLC:

```
/plan-1  →  /plan-2  →  /plan-3     (story: ideate → approve → complete)
/epic-1  →  /epic-2  →  /epic-3     (epic: decompose → review → close)
/dev-1   →  /dev-2   →  /dev-3      (impl: plan → execute → complete)
```

Debate commands (`/plan-debate`, `/dev-debate`, `/epic-debate`) handle cross-persona conflicts. Log commands (`/dev-write-impl-log-entry`, `/dev-update-impl-log-index`) automate the living implementation log.

Full command reference: [`docs/developer-setup/persona-and-command-reference.md`](docs/developer-setup/persona-and-command-reference.md)

## The Knowledge Base

```
docs/
├── design-principles/    ← The "constitution" — invariant conventions every persona follows
├── impl-log/             ← Living memory — per-domain index (current state) + log (history)
└── developer-setup/      ← Process docs, onboarding, MCP setup
```

The constitution files in `docs/design-principles/` are fill-in templates — see [`docs/design-principles/_INSTRUCTIONS.md`](docs/design-principles/_INSTRUCTIONS.md) for guidance.

## How to Adopt

Open your project in Cursor and run one command:

**New project:**
```
/setup-dev-process-new
```

**Existing project:**
```
/setup-dev-process-existing
```

The setup command handles everything interactively — project identity, constitution, personas, commands, and rules. No manual file editing required.

For manual copy instructions and full details: [`QUICK_START.md`](QUICK_START.md)

For the full process reference: [`docs/developer-setup/developer-process.md`](docs/developer-setup/developer-process.md)

## What's Included

| Directory | Contents |
|---|---|
| `.cursor/commands/` | 28 slash commands — the workflow engine |
| `.cursor/skills/` | 6 core personas, ready to use |
| `.cursor/skills/examples/` | 5 specialist personas from a live project |
| `.cursor/rules/` | 4 Cursor rules — workflow hygiene, TypeScript, UI, scripts |
| `.cursor/mcp.json` | MCP server config (Adobe Extensibility MCP pre-wired) |
| `docs/design-principles/` | Fill-in constitution templates |
| `docs/developer-setup/` | Process docs, skill authoring guide, developer process |

## Works Better Together

This framework pairs with the **[Adobe Extensibility MCP](https://github.com/davidbenge/adobe_extensibility_mcp)** server to form a complete AI dev team for Adobe platform development.

| This repo | Adobe Extensibility MCP |
|---|---|
| The **team** — personas, workflow, commands, and process | The **knowledge** — curated Adobe developer patterns served on demand |

When both are active, your AI agents follow structured workflow (this framework) while automatically pulling the right Adobe-specific guidance at the right moment (the MCP server). The MCP server is already pre-wired in `.cursor/mcp.json`.

## Requirements

- [Cursor](https://cursor.sh) (agent skills and slash commands are Cursor features)
- A Cursor project (new or existing)

## License

MIT
