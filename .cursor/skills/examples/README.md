# Example Domain-Specialist Skills

These skills are real, production-grade persona skills extracted from a live Adobe App Builder project (Pattern Atlas Publishing Platform). They demonstrate the **full anatomy of a domain specialist** — reference files, agent descriptors, and a comprehensive `SKILL.md` trigger guide.

## Why They're Here

The six core personas (Orchestrator, PM, Architect, Dev Lead, Security, Test Engineer) are generic and ready to use as-is. Domain specialists are inherently project-specific. These examples show you **how to build your own** for your stack, cloud provider, auth system, or data layer.

## Included Examples

| Skill | What It Demonstrates |
|---|---|
| `app-builder-actions-developer/` | OpenWhisk microservice design; how to embed platform docs + solution patterns in `references/` |
| `app-builder-frontend-developer/` | Jamstack + Unified Shell frontend; how to encode UI framework conventions in a skill |
| `app-builder-mcp-developer/` | MCP server design + deployment; minimal skill with OpenAI agent descriptor |
| `graph-db-specialist/` | RDF/OWL/Neo4j ontology design; how to embed deep ontology + retrieval references |
| `ims-login/` | Auth integration specialist; how to encode OAuth flows and token-handling edge cases |

## How to Adapt One for Your Project

1. Pick the example closest to your domain.
2. Copy it to `.cursor/skills/<your-skill-name>/`.
3. Rewrite `SKILL.md`:
   - Update the **trigger sentence** (first line — what makes Cursor invoke this skill).
   - Replace the **responsibilities** section with your domain's concerns.
   - Update the **references** links to point to your new reference files.
4. Replace or delete the files in `references/` with your own domain documentation.
5. Update `agents/openai.yaml` with your persona's description (optional but recommended).
6. Add your new skill to `.cursor/AGENTS.md` so the context map stays current.

See [`docs/developer-setup/persona-skill-authoring.md`](../../../docs/developer-setup/persona-skill-authoring.md) for the full authoring guide.
