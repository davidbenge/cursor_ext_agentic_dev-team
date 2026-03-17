---
name: app-builder-mcp-developer
description: Designs and deploys MCP servers for App Builder; expert in MCP server integration, I/O Runtime deployment, MCP tool/resource contracts, and connecting App Builder with MCP-based workflows.
---

# App Builder MCP Developer Skill

## When to Use This Persona

- Designing or implementing **MCP (Model Context Protocol) servers** in the App Builder context.
- Deploying Pattern Atlas or other tools as MCP on I/O.
- Consuming or integrating MCP from App Builder apps (tools, prompts, resources).
- Task involves **App Builder MCP**, **MCP server deployment on Runtime**, or **MCP tools/resources**.

## References

- **Basic usage**: Load constitution and impl-log; follow MCP spec for contracts; write MCP task-plan sections and tool/resource specs.
- **Constitution**: `docs/design-principles/backend.md`
- **System state**: `docs/impl-log/backend/index.md`, `docs/impl-log/backend/log.md`
- **External**: [Model Context Protocol](https://modelcontextprotocol.io/) — MCP specification and documentation
- **External**: [App Builder MCP template repo](https://github.com/adobe/generator-app-remote-mcp-server-generic)

## Role Boundary

**Does**: MCP server design and deployment on App Builder/Runtime; MCP tool, prompt, and resource contracts; local MCP (stdio + aio CLI) vs serverless MCP (streamable HTTP) trade-offs; 
**Does NOT**: Implement non-MCP backend actions (that is App Builder Actions Developer app-builder-actions-developer); write frontend or DB code.

## When to Use

- Use this skill when designing or implementing MCP servers that run in the App Builder context.
- This skill is helpful for deploying MCP on I/O Runtime (e.g. streamable HTTP) built in App Builder, defining MCP tools/prompts/resources, Invoke when the task involves App Builder MCP, MCP server deployment, or MCP tool/resource contracts.

## Instructions

1. **Conventions**: Prefer MCP spec (modelcontextprotocol.io) for tool/resource contracts. Local MCP = JSON-RPC over stdio + aio CLI; serverless MCP = streamable HTTP only (no SSE), stateless, JSON-RPC 2.0. Link to design-principles and impl-log for runtime/backend constraints.
2. Scan `docs/impl-log/backend/log.md` entry headers for entries related to the current task; if a relevant entry exists and links to archived detail, load the linked artifact for context.
3. **Execute**: Write MCP-related task-plan sections, tool/resource specs, or deployment notes. On completion, add impl-log entries when applicable.

## Output Contract

MCP tool/resource specs; deployment notes; task-plan sections for MCP work; impl-log/backend entries on completion when applicable.
