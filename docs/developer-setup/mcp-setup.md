# MCP Setup — {{PROJECT_NAME}}

## Config

- **File**: `.cursor/mcp.json` (references env vars)
- **Developers**: Copy `.env.example` to `.env`, set tokens. Start Cursor from shell with env set. 
Use EasyMCP to access the internal dev assets [EasyMCP](https://docs.easymcp.pages.adobeitc.com/#/)

## Servers

| Server | Purpose |
|-------|---------|
| Context7 | Up-to-date library docs and examples |
| GitHub | Create issues, PRs; read specs |
| Neo4j | Run Cypher, query ODP graph (read-only) |

## Troubleshooting

- Env vars not loaded: Start Cursor from terminal where you've `source .env` or exported vars
- MCP connection fails: Check token validity, server command in mcp.json
