> **FRAMEWORK EXAMPLE**: This file documents Ontology Design Patterns (ODPs) for a knowledge graph project. It is specific to the graph-db-specialist example skill. Replace with domain-equivalent reference material or delete if not applicable.

# Quick Reference: {{PROJECT_NAME}} ODP System

**Last Updated**: 2026-02

---

## What is an ODP?

An **ODP** (Object Definition Page) is an **HTML page** published to the internet at a **canonical URL**. The page contains:

- **RDFa** structure for semantic markup (e.g. `typeof`, `property`, `about`, `prefix`)
- **Embedded JSON-LD** (`application/ld+json`) for machine and LLM consumption

**Technical location of ODP pages**: `src/{{APP_EXTENSION_POINT}}/public/odp/` — by type: `use-cases/`, `use-cases-library/`, `products/`, `personas/`, `persona-groups/`, `kpis/`, `glossary/`, `processes/`, `activities/`, `connectors/`, `sales-plays/`. Each ODP is a directory with `index.html`.

**Source of truth for graph data**: `data/RDF/` (Turtle). The build pipeline uses only **data/RDF** and **dist/jsonld** (output of `rdf-to-jsonld`). HTML generation reads from **dist/jsonld**; there is no pipeline use of `data/odps`.

---

## Quick Commands

### Generate ODP HTML Pages

Build from **data/RDF** to HTML: `npm run build:rdf` (data/RDF → dist/jsonld) then a single script reads **dist/jsonld** and writes HTML to `src/{{APP_EXTENSION_POINT}}/public/odp/`.

```bash
npm run build:odps   # validate:rdf → build:rdf → generate-html (from dist/jsonld) → copy-assets → sitemap
# Or stepwise:
npm run build:rdf
node scripts/generate/generate-html.js   # Single script: all entity types from dist/jsonld
```

Optional: set `BASE_URL` for canonical URLs (e.g. `BASE_URL=https://your-app.adobeio-static.net/odp node scripts/generate/generate-html.js`).

### Deploy to App Builder

```bash
aio app deploy
```

### Neo4j Ingest

Ingest script reads **data/RDF** (Turtle, default). Production ingest runs from **data/RDF** only (net-new model and data).

```bash
npm run ingest:neo4j
# Or: npx tsx scripts/ingest/ingest-odps-neo4j.ts --source data/RDF --format turtle
```

### Re-ingest Knowledge Base (Chat)

From admin panel or:

```bash
curl -X POST https://your-app.adobeioruntime.net/api/admin/kb/ingest
```

---

## Relationships

Relationship semantics are defined in the **ontology** and in the **relationship vocabulary**. Use those as the source of truth:

- **Ontology**: [data/RDF/ontology/pattern-atlas.ttl](../../data/RDF/ontology/pattern-atlas.ttl) — `adobe:` object properties (e.g. `adobe:performsActivity`, `adobe:memberOf`, `adobe:integratesWith`).
- **Vocabulary doc**: [RELATIONSHIP_VOCABULARY.md](RELATIONSHIP_VOCABULARY.md) — Human-readable list of predicates, domains, ranges, and Turtle examples.

---

## File Structure

```
pattern_atlas_publishing/
├── src/{{APP_EXTENSION_POINT}}/public/odp/     ← ODP pages (HTML): one dir per ODP, each with index.html
│   ├── use-cases/
│   ├── use-cases-library/
│   ├── products/
│   ├── connectors/
│   ├── sales-plays/
│   ├── personas/
│   ├── persona-groups/
│   ├── kpis/
│   ├── glossary/
│   ├── processes/
│   └── activities/
│
├── data/RDF/                         ← Canonical source (Turtle); pipeline uses only this and dist/jsonld
│   ├── ontology/                     (pattern-atlas.ttl)
│   ├── shapes/                       (odp-shapes.ttl)
│   ├── personas/, products/, activities/, use-cases/, ...
│
├── dist/jsonld/                      ← Output of rdf-to-jsonld; input to generate-html.js
│
├── scripts/
│   ├── generate-html.js              (reads dist/jsonld, writes HTML to public/odp)
│   ├── ingest-odps-neo4j.ts           (reads data/RDF, default)
│   ├── rdf-to-jsonld.ts              (data/RDF → dist/jsonld)
│   └── validate-rdf.ts               (validates Turtle under data/RDF)
│
└── docs/
    ├── design-principles/
    ├── impl-log/
    └── developer-setup/               ← This file, RELATIONSHIP_VOCABULARY, HTML_GENERATION_COMPLETE
```

---

## Maintenance

### Adding or Updating an ODP

1. **Add or edit the underlying data** in **data/RDF/** (Turtle). Keep ontology and SHACL shapes aligned.
2. **Run the build**: `npm run build:odps` (validate:rdf → build:rdf → generate-html from dist/jsonld → copy-assets → sitemap) so ODP page(s) appear under `src/{{APP_EXTENSION_POINT}}/public/odp/`.
3. **Re-ingest** Neo4j / Knowledge Base if needed so chat and graph reflect the changes.

### Updating Relationships

Edit the RDF in **data/RDF/**. Relationship vocabulary is defined in the ontology and documented in [RELATIONSHIP_VOCABULARY.md](RELATIONSHIP_VOCABULARY.md). After changes, run `npm run build:odps` and re-ingest as needed.

---

## Documentation Links

- [HTML_GENERATION_COMPLETE.md](HTML_GENERATION_COMPLETE.md) — What an ODP page is and how we generate the HTML.
- [RELATIONSHIP_VOCABULARY.md](RELATIONSHIP_VOCABULARY.md) — Relationship predicates (adobe:) and Turtle examples.
- **ODP schema** (embedded JSON-LD in ODP pages): `src/schemas/odp-schema.json`
- **Ontology**: `data/RDF/ontology/pattern-atlas.ttl`
- **SHACL shapes**: `data/RDF/shapes/odp-shapes.ttl`

---

## Release / Deploy Checklist

Use when releasing or deploying:

- [ ] All desired graph data in `data/RDF/` is current
- [ ] Build run (`npm run build:odps` — includes generate-html from dist/jsonld)
- [ ] Deploy to App Builder (`aio app deploy`)
- [ ] Re-ingest Knowledge Base / Neo4j if needed for chat and graph
