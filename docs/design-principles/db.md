# Database — [Your Database Technology]

<!-- Replace "[Your Database Technology]" with e.g. "PostgreSQL", "Neo4j", "MongoDB", "DynamoDB", etc. -->

## Philosophy

<!-- Describe the core data modeling philosophy for this project -->
[Describe your data model philosophy — e.g., "Relational with strict normalization", "Document-oriented with embedded relationships", "Graph-native; relationships are first-class citizens"].

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

## Ownership

The [DB specialist persona name] maintains **docs/impl-log/db/index.md** (current schema state) and any schema definition files (e.g., migrations, SHACL shapes, Prisma schema). Keep index and schema files aligned.
