# Database — [Your Database Technology]

<!-- Replace "[Your Database Technology]" with e.g. "PostgreSQL", "Neo4j", "MongoDB", "App Builder State + Database", etc. -->

## Philosophy

<!-- Describe the core data modeling philosophy for this project -->
[Describe your data model philosophy — e.g., "Relational with strict normalization", "Document-oriented with embedded relationships", "Graph-native; relationships are first-class citizens", "Key-value cache (State) + document store (Database) on App Builder"].

## Schema

<!-- Define the canonical schema elements and naming conventions -->
- **[Entity type 1]**: [key fields and primary key convention]
- **[Entity type 2]**: [key fields and primary key convention]
- **[Relationships/indexes]**: [how relationships or indexes are defined]

## Connection Handling

<!-- Define connection lifecycle rules -->
**[Context 1, e.g., "Serverless / short-lived"]**: [How to handle connections — e.g., "Create connection per request; close in same invocation. Pass credentials via environment params."]

**[Context 2, e.g., "Long-running scripts"]**: [How to handle connections — e.g., "Create once, reuse, close on exit."]

## Query Rules

<!-- Enforce safe, performant query patterns -->
- [Rule 1 — e.g., "Always use parameterized queries — no string interpolation"]
- [Rule 2 — e.g., "Always include a LIMIT clause on list queries"]
- [Rule 3 — e.g., "Use OPTIONAL joins/matches for optional relationships"]

## Migration Policy

Document migrations in `impl-log/db/log.md`. One entry per significant change. Index = current state only.

## Tech Stack

<!-- Define the exact data storage technologies this project uses.
     The db-specialist reads this to know which storage services and MCP domain skills apply. -->

- **Primary data store**: [e.g., App Builder Database (MongoDB-compatible) / PostgreSQL / Neo4j / DynamoDB]
- **Cache / ephemeral store**: [e.g., App Builder State (key-value, TTL) / Redis / none]
- **File / blob store**: [e.g., App Builder Files (pre-signed URLs, up to 200 GB) / S3 / none]
- **Provisioning**: [e.g., "App Builder Database provisioned via `aio app db provision`" / "Managed by Terraform"]
- **Region**: [e.g., `amer` / `emea` / `apac`]

<!-- Adobe App Builder storage options — select what applies and remove the rest.
     Reference: https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/storage/ -->

### Adobe App Builder Storage (if applicable)

| Service | Library | Best for | Max size |
|---|---|---|---|
| **State** | `@adobe/aio-lib-state` | Session data, caching, config, TTL-expiring data | 1 MB/value, 1 GB/workspace |
| **Files** | `@adobe/aio-lib-files` | Images, documents, large payloads, shareable URLs | 200 GB |
| **Database** | `@adobe/aio-lib-db` | Complex queries, relationships, aggregations | 16 MB/document, 40 GB/pack |

**Decision guide** (from [Adobe storage docs](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/storage/)):
- Data > 100 KB + needs shareable URL → **Files**
- Data > 100 KB + needs queries → **Database**
- Data < 100 KB + needs auto-expiry → **State**
- Complex relationships or analytics → **Database**

<!-- After filling in the stack above, update the MCP Domain Skills section below. -->

## MCP Domain Skills

The `db-specialist` persona **must** check the `adobe-extensibility-mcp` MCP server before starting any domain-specific task. Call `list_skills` to confirm available domains, then `load_skill` for each that applies to this project.

**Domains relevant to this project:**

> The Adobe Extensibility MCP covers storage patterns as part of App Builder actions. If this project uses App Builder State, Files, or Database, the `app-builder-actions` skill includes the relevant storage patterns and SDK usage. For non-Adobe databases (PostgreSQL, Neo4j, etc.) there is no dedicated MCP skill — follow the db.md conventions and impl-log directly.

> During project setup, the relevant skills can be selected interactively via the setup commands.
> - When running setup, you will be given the option to either:
>   - Select the domains from a checklist, **or**
>   - Describe your data needs and let the AI recommend the correct domain skills for you.

If you're unsure, just ask in natural language — AI will help you select the right MCP domain skills.



<!-- Check all that apply and remove the rest -->
- [ ] `app-builder-actions` — Covers App Builder State, Files, and Database storage SDK patterns on I/O Runtime

Source: [github.com/davidbenge/adobe_extensibility_mcp — skills](https://github.com/davidbenge/adobe_extensibility_mcp/tree/stage/actions/skills-mcp/skills)

## Ownership

The `db-specialist` persona maintains **docs/impl-log/db/index.md** (current schema state) and any schema definition files (e.g., migrations, SHACL shapes, Prisma schema, collection definitions). Keep index and schema files aligned.
