# SKOS Pattern

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-01-10
> **Provenance:** 3P
> **Derives From:** W3C SKOS

Simple Knowledge Organization System (SKOS) for semantic content classification and concept hierarchies.

---

## Overview

**[W3C SKOS (Simple Knowledge Organization System)](https://www.w3.org/TR/skos-reference/)** is a W3C recommendation for representing knowledge organization systems such as thesauri, classification schemes, and taxonomies. SemOps adopts SKOS extensively for content metadata and concept organization.

SKOS provides a shared vocabulary for expressing:
- Concept hierarchies (broader/narrower relationships)
- Preferred and alternative labels
- Definitions and scope notes
- Related concepts


## Adoption Context

- **Original Capability:** `domain-data-model` — semantic relationships between domain concepts
- **Problem Statement:** Domain concepts need machine-readable semantic relationships — broader, narrower, related. Without structured taxonomy, pattern relationships are described in prose and can't be queried.
- **Selection Criteria:** W3C SKOS (Simple Knowledge Organization System) — linked data vocabulary for concept schemes. Applied as the backbone of the pattern taxonomy, enabling coherence measurement via graph connectivity.
- **ADR:** —

## SKOS Properties in SemOps

### Metadata Field Mapping

| SKOS Property | SemOps Field | Purpose |
|---------------|--------------|---------|
| `skos:prefLabel` | `metadata.preferred_label` | Canonical display name for content |
| `skos:altLabel` | `metadata.alt_labels` | Alternative names, synonyms, abbreviations |
| `skos:definition` | `metadata.definition` | Formal definition of the concept |
| `skos:scopeNote` | `metadata.scope_note` | Usage context and constraints |
| `skos:example` | `metadata.example` | Illustrative examples |
| `skos:broader` | `metadata.broader_concepts` | Parent concepts in hierarchy |
| `skos:narrower` | `metadata.narrower_concepts` | Child concepts in hierarchy |
| `skos:related` | `metadata.related_concepts` | Associated concepts (non-hierarchical) |

### Example Usage

```yaml
metadata:
  $schema: content_metadata_v1
  content_type: github_doc
  primary_concept: semantic-coherence       # What concept this establishes
  preferred_label: "Semantic Coherence"     # skos:prefLabel
  alt_labels:                               # skos:altLabel
    - "SC"
    - "coherence metric"
  definition: "The degree of shared meaning..." # skos:definition
  broader_concepts:                         # skos:broader
    - "semantic-operations"
  narrower_concepts:                        # skos:narrower
    - "semantic-drift"
    - "semantic-compression"
  related_concepts:                         # skos:related
    - "domain-driven-design"
    - "ubiquitous-language"
  scope_note: "Applies to content within a bounded context"
```

---

## Architecture Principle

**SKOS describes semantics WITHIN entities:**
- Concept hierarchies (broader/narrower)
- Semantic typing and labels
- Internal concept organization

**Edge predicates (PROV-O) describe relationships BETWEEN entities:**
- Provenance chains and derivation lineage
- Citations and attributions
- Version succession and revisions

This separation keeps provenance relationships (edges) distinct from semantic content (metadata).

---

## Concept Identity Pattern

**Key Design Decision:** Concepts in SemOps are established through usage, not as separate entity instances.

Multiple entities can reference the same concept via `primary_concept` field:

```yaml
# Entity 1: Blog post
metadata:
  primary_concept: semantic-coherence
  content_type: blog_post

# Entity 2: Documentation
metadata:
  primary_concept: semantic-coherence
  content_type: github_doc

# Entity 3: Video
metadata:
  primary_concept: semantic-coherence
  content_type: video
```

All three entities contribute to defining the "semantic-coherence" concept. The concept identity emerges from the collection of entities that reference it.

---

## SKOS Concept Scheme

For organizing concepts into formal schemes, use the hierarchy relationships:

```
semantic-operations (Concept Scheme)
├── semantic-coherence
│   ├── semantic-drift
│   └── semantic-compression
├── pattern-operations
│   ├── stable-core-flexible-edge
│   └── domain-pattern
└── governance-as-strategy
```

Each concept uses `broader_concepts` to link up and `narrower_concepts` to link down.

---

## Use Cases in SemOps

### 1. Content Classification
- Every entity has a `primary_concept` identifying what it's about
- Enables semantic search: "find all content about semantic-coherence"

### 2. Knowledge Graph Navigation
- `broader_concepts` enables drill-up navigation
- `narrower_concepts` enables drill-down navigation
- `related_concepts` enables lateral exploration

### 3. Synonym Resolution
- `alt_labels` allows search to match alternative terms
- "SC" finds content labeled "Semantic Coherence"

### 4. RAG Retrieval
- SKOS metadata enriches embeddings with concept context
- Enables concept-based filtering of retrieval results

---

## Related Documentation

- [edge-predicates.md](edge-predicates.md) - PROV-O relationships (complements SKOS)
- [dam-foundations.md](dam-foundations.md) - Domain context and theoretical foundations
- [content_metadata_v1.json](../../schemas/metadata/content_metadata_v1.json) - Metadata schema implementation

### W3C Standards
- [W3C SKOS Reference](https://www.w3.org/TR/skos-reference/) - Full specification
- [SKOS Primer](https://www.w3.org/TR/skos-primer/) - Introduction and examples

---

## 3P Foundation

| SKOS Feature | SemOps Adoption | Our Extension |
|--------------|-----------------|---------------|
| Labels (pref/alt) | Direct mapping | Multi-language support planned |
| Hierarchy (broader/narrower) | Direct mapping | Concept emergence through usage |
| Related concepts | Direct mapping | Strength weighting integration |
| Definitions | Direct mapping | None |

**Key Extension:** Concepts emerge through entity references rather than being pre-defined in a formal scheme. This supports the bottom-up knowledge discovery pattern central to SemOps.

