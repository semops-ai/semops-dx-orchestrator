# Domain Patterns

> Centralized documentation for domain patterns used across the SemOps ecosystem.

**Owner:** semops-dx-orchestrator (Platform/DX)
**Status:** ACTIVE
**Last Updated:** 2026-02-21

## What Are Domain Patterns?

Domain patterns describe **stable semantic concepts** — the WHY behind system design. They are distinct from capabilities (WHAT the system delivers) and repos (WHERE code lives). All patterns are registered in [pattern_v1.yaml](../../schemas/pattern_v1.yaml).

Each pattern doc includes:

- **Metadata header:** Version, status (5-state lifecycle), provenance (1P/3P), derives_from lineage
- **Adoption Context:** Why adopted, what capability drove it, selection criteria, linked ADR
- **Core Concepts:** Pattern-specific key ideas
- **References:** ADRs, external sources, related patterns

## Directory Structure

```text
domain-patterns/
├── *.md              # Active patterns (37 files)
├── _TEMPLATE.md      # Pattern doc template
├── README.md         # This file
└── retired/          # Retired patterns (21 files) — kept for lineage
```

## Active Patterns (37)

### Semantic Operations (1P)

| Pattern | Description |
| ------- | ----------- |
| [semantic-coherence.md](semantic-coherence.md) | Primary quality signal — measures shared meaning |
| [semantic-ingestion.md](semantic-ingestion.md) | Bronze→silver→gold corpus pipeline |
| [semantic-object-pattern.md](semantic-object-pattern.md) | Structured self-describing domain concepts |
| [agentic-lineage.md](agentic-lineage.md) | Agent interactions as provenance events |
| [agentic-rag.md](agentic-rag.md) | Agentic loop over retrieval |
| [scale-projection.md](scale-projection.md) | Designing for next order-of-magnitude scale |
| [explicit-enterprise.md](explicit-enterprise.md) | Human-AI collaboration patterns |
| [explicit-architecture.md](explicit-architecture.md) | Architecture as queryable data |
| [provenance-first-design.md](provenance-first-design.md) | Ownership as primary discriminator |
| [unified-catalog.md](unified-catalog.md) | Unified view across owned and referenced content |

### Foundation Patterns

| Pattern | Description |
| ------- | ----------- |
| [ddd.md](ddd.md) | Domain-Driven Design — primary architectural framework |
| [dam.md](dam.md) | Digital Asset Management |
| [dam-foundations.md](dam-foundations.md) | Core catalog and asset concepts (DAM/CMS/SKOS convergence) |
| [edge-predicates.md](edge-predicates.md) | Typed relationships (PROV-O predicates) |
| [edge-predicate-examples.md](edge-predicate-examples.md) | Worked examples for edge predicates |
| [shape.md](shape.md) | Formal entity constraints (SHACL/JSON Schema) |
| [content-lifecycle-states.md](content-lifecycle-states.md) | Content state machines and dual-status model |
| [provenance-and-lineage.md](provenance-and-lineage.md) | Attribution and derivation (1P/2P/3P) |

### Content Workflow Patterns

| Pattern | Description |
| ------- | ----------- |
| [content-input-pattern.md](content-input-pattern.md) | How content enters the system |
| [content-compose-pattern.md](content-compose-pattern.md) | Atom-Hub-Structure composition |
| [publication-patterns.md](publication-patterns.md) | Publishing to surfaces |
| [research-synthesis-pattern.md](research-synthesis-pattern.md) | RAPTOR-inspired research RAG |
| [experience-taxonomy-pattern.md](experience-taxonomy-pattern.md) | Resume/portfolio taxonomies |
| [business-domain-pattern.md](business-domain-pattern.md) | Business classification (NAICS/NIST/KIBO) |

### 3P Standard References

| Pattern | Description |
| ------- | ----------- |
| [skos-pattern.md](skos-pattern.md) | W3C SKOS — concept hierarchies and taxonomies |
| [dublin-core-pattern.md](dublin-core-pattern.md) | Dublin Core metadata elements |
| [pim-pattern.md](pim-pattern.md) | PIM multi-channel syndication |
| [backstage-software-catalog.md](backstage-software-catalog.md) | Backstage schema model for REPOS.yaml |
| [arc42.md](arc42.md) | Architecture documentation template |
| [etl.md](etl.md) | Extract Transform Load |
| [medallion-architecture.md](medallion-architecture.md) | Bronze/Silver/Gold data quality tiers |
| [open-lineage.md](open-lineage.md) | OpenLineage (LF AI & Data) |
| [episode-provenance.md](episode-provenance.md) | W3C PROV-O episode extension |
| [pattern-language.md](pattern-language.md) | Pattern Language (Alexander/GoF) |
| [data-lineage.md](data-lineage.md) | Data Lineage |
| [semops-dataofiling.md](semops-dataofiling.md) | Data Profiling |
| [data-modeling.md](data-modeling.md) | Data Modeling |
| [raptor.md](raptor.md) | Recursive Abstractive Processing (RAPTOR) |
| [rlhf.md](rlhf.md) | Reinforcement Learning from Human Feedback |
| [seci.md](seci.md) | SECI Model (Nonaka — knowledge creation) |
| [platform-engineering.md](platform-engineering.md) | Platform Engineering |

### Guides and References

| Document | Description |
| -------- | ----------- |
| [guide-doc-style.md](guide-doc-style.md) | Documentation style guide |
| [guide-interpretive-frameworks.md](guide-interpretive-frameworks.md) | Interpretive frameworks (DIKW/DDD/PROV-O) |
| [guide-zettelkasten-hubs.md](guide-zettelkasten-hubs.md) | Hub document authoring guide |
| [related-links-convention.md](related-links-convention.md) | Link formatting conventions |
| [reference-entity-constraints.md](reference-entity-constraints.md) | Entity validation rules |
| [reference-edge-relationships.md](reference-edge-relationships.md) | Edge workflow patterns |

## Retired Patterns (21)

Retired patterns are in [`retired/`](retired/) — kept for lineage and historical reference. These were removed from the active registry during the #146 audit (66→37 patterns).

| Pattern | Reason |
| ------- | ------ |
| [mirror-architecture.md](retired/mirror-architecture.md) | Absorbed into `scale-projection` |
| [classifier-infrastructure.md](retired/classifier-infrastructure.md) | Superseded by `semantic-coherence` |
| [repository-operational-model.md](retired/repository-operational-model.md) | Contradicts corrected DDD model |
| [shared-kernel.md](retired/shared-kernel.md) | DDD sub-entity, covered by `ddd` |
| [published-language.md](retired/published-language.md) | DDD sub-entity, covered by `ddd` |
| [customer-supplier.md](retired/customer-supplier.md) | DDD sub-entity, not standalone |
| [conformist.md](retired/conformist.md) | DDD sub-entity, not standalone |
| [anti-corruption-layer.md](retired/anti-corruption-layer.md) | DDD sub-entity, not standalone |
| [context-engineering.md](retired/context-engineering.md) | Capability, not pattern |
| [task-management.md](retired/task-management.md) | Capability, not pattern |
| [synthetic-data.md](retired/synthetic-data.md) | Capability, not pattern |
| [open-primitive-pattern.md](retired/open-primitive-pattern.md) | Not a pattern |
| [containerization.md](retired/containerization.md) | Commodity infrastructure |
| [ci-cd.md](retired/ci-cd.md) | Commodity process |
| [mlops.md](retired/mlops.md) | Implementation practice |
| [edge-first-web-stack.md](retired/edge-first-web-stack.md) | Infrastructure choice |
| [accounting-system.md](retired/accounting-system.md) | Research input |
| [knowledge-organization-systems.md](retired/knowledge-organization-systems.md) | Research input |
| [viable-systems-model.md](retired/viable-systems-model.md) | Research input for `explicit-architecture` |
| [semantic-optimization.md](retired/semantic-optimization.md) | Content layer concept |
| [content-classify-pattern.md](retired/content-classify-pattern.md) | Covered by `semantic-coherence` |

## Pattern Template

New patterns should follow [_TEMPLATE.md](_TEMPLATE.md).

## Related

- [pattern_v1.yaml](../../schemas/pattern_v1.yaml) — Pattern registry schema
- [STRATEGIC_DDD.md](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md) — Capability→pattern registry
- [PATTERN_AUDIT.md](../PATTERN_AUDIT.md) — Audit trail from #146
