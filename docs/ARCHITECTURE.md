# SemOps System Architecture

Detailed architecture reference for the SemOps multi-repo system. For orientation and key decisions, see the [README](../README.md). This document provides the deeper technical reference.

---

## Subdomain Classification

Subdomains are emergent groupings of capabilities, not repo boundaries. A single subdomain spans multiple repos, and a single repo participates in multiple subdomains. Subdomains are discovered by tracing capability-to-pattern relationships.

| Subdomain | Type | Repos | Scope |
| --------- | ---- | ----- | ----- |
| **Semantic Operations** | Core | semops-core, semops-data | Pattern model, knowledge graph, coherence scoring, corpus routing |
| **Knowledge Management** | Core | semops-core, semops-data, semops-publisher | Ingestion, classification, retrieval, episode provenance, Research RAG |
| **Content Publishing** | Supporting | semops-publisher, semops-sites, semops-docs | Multi-surface publishing, content pipeline, theory documentation |
| **Platform/Process** | Supporting | semops-dx-orchestrator | Architecture governance, sync workflows, coordination |

---

## Integration Patterns

Repos interact through standard DDD integration patterns. The shared knowledge base (relational database, vector store, knowledge graph) is infrastructure accessible to any repo. These patterns describe how repos relate to the **domain model**, not the database.

| Pattern | Repos | Relationship |
| ------- | ----- | ------------ |
| **Shared Kernel** | semops-core <> semops-publisher, semops-docs | Both depend on the same domain vocabulary and schema definitions |
| **Published Language** | semops-dx-orchestrator > all repos | Architecture documentation, process patterns, decision record templates |
| **Customer-Supplier** | semops-core > semops-data, semops-sites | Upstream provides database, vector search, and document processing services |
| **Conformist** | semops-publisher, semops-docs > semops-core | Downstream adopts upstream Pattern/Entity model as-is |
| **Customer-Supplier** | semops-publisher > semops-sites | Content, fonts, PDF templates flow downstream to frontend |

---

## Domain Model

The domain model uses **Pattern as aggregate root** -- an applied unit of meaning with a business purpose. The model is organized into three layers.

### Three-Layer Architecture

| Layer | Entities | Purpose |
| ----- | -------- | ------- |
| **Pattern** (Core Domain) | Pattern, Pattern Edge | SKOS-based concept taxonomy with adoption relationships. Stable semantic core that everything traces to. |
| **Architecture** (Strategic Design) | Entity (capability), Entity (repository), Ingestion Run/Episode | Capabilities implement patterns; repos deliver capabilities. Integration patterns between repos. |
| **Content** (Publishing) | Entity (content), Edge, Surface, Delivery, Brand/Product | Publishing artifacts with W3C PROV-O relationships. Per-surface governance. |

The `entity` table uses an **entity_type discriminator** (content, capability, repository) -- one table serving three purposes. All entity types share embeddings, pattern links, edges, and metadata.

### Key Invariants

- **Capability coherence:** Every capability must trace to at least one pattern. Capabilities with zero pattern links indicate missing patterns or unjustified capabilities -- a measurable coherence signal.
- **Stable Core vs. Flexible Edge:** Entities with an assigned `primary_pattern_id` are classified (linked to the Pattern layer). Entities without one are unclassified -- awaiting classification through the coherence workflow.

---

## Naming Conventions

### Entity IDs
Kebab-case descriptive: `blog-post-semantic-ops-2024`

### Edge Predicates
Snake-case verbs: `derived_from`, `depends_on`, `cites`

### Pattern IDs
Kebab-case: `semantic-coherence`, `aggregate-root`

### Branch Names
- `feature/*` -- New features
- `fix/*` -- Bug fixes
- `docs/*` -- Documentation
- `experiment/*` -- Experiments

---

## Related

- [README](../README.md) -- System overview, key decisions, ideas worth studying
- [REPOS.yaml](REPOS.yaml) -- Machine-readable repo registry
- [semops-docs](https://github.com/semops-ai/semops-docs) -- Framework theory and concepts
- [semops-core](https://github.com/semops-ai/semops-core) -- Schema definitions and domain vocabulary
