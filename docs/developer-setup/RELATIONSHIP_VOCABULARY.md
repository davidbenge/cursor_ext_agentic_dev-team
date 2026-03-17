# Relationship Vocabulary — {{PROJECT_NAME}} RDF

**Status**: Active  
**Version**: 4.0.0  
**Purpose**: Human-readable reference for relationship vocabulary used in RDF/Turtle ODP data. Derived from and consistent with the {{PROJECT_NAME}} ontology.

---

## Source of Truth

The canonical relationship vocabulary is defined in the **OWL ontology**:

- **File**: [data/RDF/ontology/pattern-atlas.ttl](../../data/RDF/ontology/pattern-atlas.ttl)
- **Namespace**: `adobe:` — `https://architecture.adobe.com/vocab#`

This document is a derived summary. When in doubt, or when adding new relationship types, update the ontology first; then update this doc and the SHACL shapes as needed.

---

## Model: Direct RDF Triples

Relationships in {{PROJECT_NAME}} are **direct RDF triples**: subject –*predicate*→ object. There is no reified "Relationship" object or `relationshipType` verb wrapper. The **predicate** (object property) is the relationship.

Example:

```turtle
<https://architecture.adobe.com/personas/account-manager/v1.0.0>
  adobe:memberOf <https://architecture.adobe.com/persona-groups/agencies> ;
  adobe:performsActivity <https://architecture.adobe.com/activities/manage-client-relationships/v1.0.0> .
```

Instance data uses **versioned IRIs** (e.g. `.../v1.0.0`) and standard versioning metadata (PAV, dcterms, schema:validFrom). See [data/RDF/personas/account-manager.ttl](../../data/RDF/personas/account-manager.ttl) and other files under `data/RDF/`.

---

## Object Properties (from Ontology)

### Product / System

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:hasCapability` | System | Capability | System/product provides this capability. |
| `adobe:plannedCapability` | System | Capability | Capability planned for a future release. |
| `adobe:integratesWith` | System | System | Product/system integrates with another. |
| `adobe:willBeReplacedBy` | System | — | Points to the entity (version IRI) that will replace this one. |
| `adobe:targetPersona` | System | Persona | Product targets this persona. |

### Persona

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:performsActivity` | Persona | Activity | Persona performs this activity. |
| `adobe:measuresSuccess` | Persona | KPI | Persona uses this KPI to measure success. |
| `adobe:memberOf` | Persona | PersonaGroup | Persona belongs to this group. |
| `adobe:interactsWith` | Persona | Persona | Persona interacts with this persona. |

### Connector

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:connectsFrom` | Connector | — | Source system of the connector. |
| `adobe:connectsTo` | Connector | — | Target system of the connector. |

### Generic / Cross-entity

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:requires` | — | — | Dependency: this entity requires another (versioned IRI). |
| `adobe:extends` | — | — | This entity extends another. |
| `adobe:implements` | — | — | This entity implements another (e.g. feature implements capability). |

### ProductFeature

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:featureOf` | ProductFeature | Product | Feature belongs to this product. |
| `adobe:implements` | ProductFeature | Capability | Feature implements this capability. |

### UseCase

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:cscPhase` | UseCase | — | Content Supply Chain phase this use case belongs to. |
| `adobe:primaryActor` | UseCase | Persona | Primary persona for this use case. |
| `adobe:actorGroup` | UseCase | — | Persona group or group IRI for actors. |
| `adobe:implementedByFeature` | UseCase | ProductFeature | Product features that implement this use case. |
| `adobe:involvesActivity` | UseCase | Activity | Activities this use case involves. |
| `adobe:valueProposition` | UseCase | — | Structured value proposition (blank node). |
| `adobe:kpiMeasurement` | UseCase | — | Structured KPI link (blank node). |
| `adobe:kpi` | — | KPI | References a KPI in value proposition or measurement. |

### FutureVersion

| Property | Domain | Range | Description |
|----------|--------|-------|-------------|
| `adobe:migrationGuide` | FutureVersion | — | IRI to migration guide. |

### In Use in Instance Data (to be formalized in ontology if needed)

The following predicates appear in `data/RDF/` (e.g. in `data/RDF/csc-detailed-phases/`) and are used for CSC phase structure. They may be added to the ontology for full formalization.

| Property | Used in | Description |
|----------|---------|-------------|
| `adobe:partOf` | CscDetailedPhase → CscPhase | This detailed phase is part of this CSC phase. |
| `adobe:includesActivity` | CscDetailedPhase → Activity | This phase includes this activity. |

---

## Turtle Examples

### Persona (from data/RDF/personas/account-manager.ttl)

```turtle
@prefix adobe: <https://architecture.adobe.com/vocab#> .
@prefix schema: <https://schema.org/> .

<https://architecture.adobe.com/personas/account-manager/v1.0.0>
  a adobe:Persona ;
  schema:name "Account Manager" ;
  schema:description "Manages client relationships and project requirements..." ;
  adobe:performsActivity <https://architecture.adobe.com/activities/manage-client-relationships/v1.0.0> ;
  adobe:performsActivity <https://architecture.adobe.com/activities/coordinate-project-requirements/v1.0.0> ;
  adobe:measuresSuccess <https://architecture.adobe.com/kpis/client-satisfaction-score/v1.0.0> ;
  adobe:memberOf <https://architecture.adobe.com/persona-groups/agencies> ;
  adobe:interactsWith <https://architecture.adobe.com/personas/creative-agency-partner/v1.0.0> ;
  adobe:interactsWith <https://architecture.adobe.com/personas/program-manager/v1.0.0> .
```

### CSC detailed phase (from data/RDF/csc-detailed-phases/activation-across-channels.ttl)

```turtle
<https://architecture.adobe.com/csc-detailed-phases/activation-across-channels/v1.0.0>
  a adobe:CscDetailedPhase ;
  schema:name "Activation Across Channels" ;
  adobe:partOf <https://architecture.adobe.com/csc-phase/delivery-activation/v1.0.0> ;
  adobe:includesActivity <https://architecture.adobe.com/activities/distribute-content-via-channels/v1.0.0> ;
  adobe:includesActivity <https://architecture.adobe.com/activities/publish-content/v1.0.0> ;
  adobe:includesActivity <https://architecture.adobe.com/activities/coordinate-campaign-execution/v1.0.0> .
```

---

## Relationship Metadata (Temporal, etc.)

For relationships that carry their own properties (e.g. temporal bounds, confidence), the project uses or may use:

- **RDF-star** (if tooling supports): e.g. `<<:aem adobe:integratesWith :assets>> schema:validFrom "2023-01"^^xsd:date .`
- **Named graphs** for provenance or temporal context.
- **Reification** only where necessary.

See the graph-db-specialist skill and ontology for the chosen pattern. There is no JSON-LD-style `relationshipMetadata` wrapper in the RDF model.

---

## Neo4j Mapping (Reference)

When ingesting from RDF into Neo4j, object properties typically map to relationship types. Examples:

| adobe: predicate | Neo4j relationship type (example) |
|------------------|------------------------------------|
| `adobe:performsActivity` | `PERFORMS_ACTIVITY` |
| `adobe:memberOf` | `MEMBER_OF` |
| `adobe:interactsWith` | `INTERACTS_WITH` |
| `adobe:measuresSuccess` | `MEASURES_SUCCESS` |
| `adobe:integratesWith` | `INTEGRATES_WITH` |
| `adobe:partOf` | `PART_OF` |
| `adobe:includesActivity` | `INCLUDES_ACTIVITY` |

Exact naming depends on the ingest script ([scripts/ingest/ingest-odps-neo4j.ts](../../scripts/ingest/ingest-odps-neo4j.ts)).

---

## Guidelines for Adding New Relationship Types

1. **Add the object property to the ontology** ([data/RDF/ontology/pattern-atlas.ttl](../../data/RDF/ontology/pattern-atlas.ttl)) with `rdfs:label`, `rdfs:comment`, and domain/range where appropriate.
2. **Update SHACL shapes** ([data/RDF/shapes/odp-shapes.ttl](../../data/RDF/shapes/odp-shapes.ttl)) if the new property should be validated.
3. **Update this document** with the new property in the appropriate table and an example if helpful.
4. **Version** this document when making substantive changes.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 4.0.0 | 2026-02 | Aligned with RDF/OWL: adobe: namespace, direct triples from pattern-atlas.ttl. Removed ex:Relationship reification and ex:relationshipType. Added Turtle examples, partOf/includesActivity (in use in data). |
| 3.x | 2025-12 | Previous JSON-LD / ex: relationship object vocabulary (superseded). |

---

**Maintained by**: Graph DB Specialist (see [.cursor/skills/graph-db-specialist/SKILL.md](../../.cursor/skills/graph-db-specialist/SKILL.md)). Keep aligned with [data/RDF/ontology/pattern-atlas.ttl](../../data/RDF/ontology/pattern-atlas.ttl) and usage in `data/RDF/`.
