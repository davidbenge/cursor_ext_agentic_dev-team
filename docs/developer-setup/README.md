# Developer Setup — {{PROJECT_NAME}} Publishing

## Quick Start

1. Copy `.env.example` to `.env` at repo root and set values
2. Start Cursor from a shell with env vars set (`source .env` or `export` vars)
3. Run `npm install`

## Required Config (`.env`)

- **Neo4j**: NEO4J_URI, NEO4J_USERNAME, NEO4J_PASSWORD
- **Azure OpenAI**: AZURE_OPENAI_ENDPOINT, AZURE_OPENAI_KEY, AZURE_OPENAI_EMBEDDING_DEPLOYMENT, AZURE_OPENAI_CHAT_DEPLOYMENT
- **MCP** (optional): GITHUB_TOKEN, JIRA_API_TOKEN (or equivalent) for GitHub/Jira MCP servers
- **IMS (standalone)** (optional): See [IMS standalone setup](ims-standalone-setup.md) for `IMS_PATTERN_ATLAS_*` vars when running outside Unified Shell

## CI/CD

- [CI/CD setup](cicd-setup.md) — CircleCI workflows, and base64-encoded `.aio` / `.env` for build and deploy

## Key Commands

- `npm run type-check` — TypeScript (actions + web)
- `npm test` — Unit tests
- `npm run e2e` — E2E tests
- `npm run ingest:neo4j` — Ingest ODPs to Neo4j
- `npm run build:odps` — Process ODPs, generate sitemap

## MCP Servers

Config: `.cursor/mcp.json`. See [mcp-setup.md](./mcp-setup.md) for catalog and troubleshooting.

Servers: Context7 (library docs), GitHub (PRs, issues), Atlassian (Jira), Neo4j (Cypher queries, read-only).

## Developer Process (End-to-End)

- [Developer Process](developer-process.md) — Full developer strategy: setup, multi-persona workflow, roles, commands, constitution, impl-log. Source for Confluence [Developer Process](https://wiki.corp.adobe.com/pages/viewpage.action?spaceKey=dssea&title=Developer+Process) page.

## Persona and Command Docs

- [Persona and command reference](persona-and-command-reference.md) — Where personas/commands live, when to use which, leverage patterns
- [Persona skill authoring](persona-skill-authoring.md) — How to create or update persona SKILL.md and commands

## Workflow Quick Reference

**Story workflow:** `/plan-1` (plan) → `/plan-2` (review) → `/plan-3` (complete) → `/dev-1` (impl) → `/dev-2` (tasks) → `/dev-3` (done)

**Epic workflow:** `/epic-1` (plan or import) → `/epic-2` (review + kickoff) → stories via plan/dev commands → `/epic-3` (complete)

**Project setup:** `/setup-dev-process-new` (new project) | `/setup-dev-process-existing` (retrofit)

## ODP Quick Reference

- [ODP quick reference](odp-quick-reference.md) — ODP types, commands, relationships, maintenance
