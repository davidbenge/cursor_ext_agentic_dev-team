> **FRAMEWORK EXAMPLE**: This file documents the ODP HTML generation pipeline for a knowledge graph project. It is project-specific and retained as a reference example. Delete or replace with equivalent documentation for your project.

# ODP HTML Page Generation

**Purpose**: This document describes how ODP HTML pages are produced. It is the canonical reference for "what an ODP page is" and "how we generate the ODP HTML pages."

---

## What is an ODP?

An **ODP** (Object Definition Page) is an **HTML page** published to the internet at a **canonical URL**. The page contains:

- **RDFa** structure for semantic markup (e.g. `typeof`, `property`, `about`, `prefix`)
- **Embedded JSON-LD** (`application/ld+json`) for machine and LLM consumption

**Technical location of ODP pages**: `src/{{APP_EXTENSION_POINT}}/public/odp/` — one directory per ODP (e.g. `odp/use-cases/campaign-activation/`), each containing `index.html` with:

- `link rel="canonical"` pointing to the published URL
- RDFa attributes in the body
- A `<script type="application/ld+json">` block with the entity data

**Source of truth for graph data**: `data/RDF/` (Turtle). The build pipeline uses only **data/RDF** and **dist/jsonld**. A single script reads **dist/jsonld** and generates all ODP HTML (net-new source only; no `data/odps` in the pipeline).

---

## How ODP HTML Pages Are Generated

### Scripts

A **single script** produces all ODP HTML. The pipeline is **data/RDF** (Turtle) → `rdf-to-jsonld` → **dist/jsonld** → **generate-html.js**.

| Script | Handles | Source | Output |
|--------|---------|--------|--------|
| **`scripts/generate/generate-html.js`** | Products, Connectors, Sales Plays, Use Cases, Personas, Persona Groups, KPIs, Glossary, Processes, Activities, category index pages | Reads **JSON-LD** from **dist/jsonld** (output of `rdf-to-jsonld`) | Writes HTML with RDFa and embedded JSON-LD to `src/{{APP_EXTENSION_POINT}}/public/odp/` |

**Source data**: **data/RDF** (Turtle) is the canonical source. Build runs `validate:rdf` → `build:rdf` (data/RDF → dist/jsonld) → generate-html (from dist/jsonld). No pipeline use of `data/odps`.

### Canonical URL

`generate-html.js` sets the canonical URL via `BASE_URL`. Default: `https://your-namespace.adobeio-static.net/odp`. Set the `BASE_URL` environment variable when running the script to match your deployed app URL.

---

## File Structure (output)

Generated ODP pages live under:

```
src/{{APP_EXTENSION_POINT}}/public/odp/
├── use-cases/           (e.g. campaign-activation/index.html)
├── use-cases-library/   (nested by product/theme, then entry/index.html)
├── products/            (e.g. aem-assets-cs/index.html)
├── connectors/
├── sales-plays/
├── personas/
├── persona-groups/
├── kpis/
├── glossary/
├── processes/
└── activities/
```

Each ODP is a directory with `index.html` inside.

---

## Commands

```bash
# Full build (validate RDF → build:rdf → generate-html from dist/jsonld → copy-assets → sitemap)
npm run build:odps

# Or stepwise: build JSON-LD from data/RDF, then generate HTML
npm run build:rdf
node scripts/generate/generate-html.js

# Optional: set canonical base URL before generating
BASE_URL=https://your-app.adobeio-static.net/odp node scripts/generate/generate-html.js

# Deploy to App Builder (publishes the pages)
aio app deploy

# Verify a generated page locally
open src/{{APP_EXTENSION_POINT}}/public/odp/use-cases/campaign-activation/index.html
```

The `build:odps` script in `package.json` runs the full pipeline including generate-html from dist/jsonld.

---

## Re-ingest (Knowledge Base / Neo4j)

After adding or changing graph data in **data/RDF/**, you may need to:

1. **Regenerate ODP HTML** (so the published pages reflect the data), then
2. **Re-ingest** so chat and Neo4j are updated.

Neo4j ingest reads from **data/RDF** (Turtle, default). Production ingest runs from **data/RDF** only. See [odp-quick-reference.md](odp-quick-reference.md) for ingest commands.

---

## Features of Generated Pages

- **JSON-LD** embedded for LLM and machine consumption
- **RDFa** in the HTML body (in `generate-html.js` product/use-case templates: `typeof`, `property`, `about`)
- **Canonical URL** via `<link rel="canonical" href="...">`
- **Relationship links** (clickable navigation between entities)
- Adobe-style CSS and semantic headings

---

## Maintained By

The **graph-db-specialist** skill maintains this document so it stays aligned with how ODP HTML pages are generated, script behavior, source data (data/RDF → dist/jsonld), and output layout.
