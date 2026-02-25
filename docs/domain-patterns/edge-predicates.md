# Edge Predicates Reference

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-01-10
> **Provenance:** 3P (extends W3C PROV-O)
> **Derives From:** prov-o

Semantic relationship types for the SemOps knowledge graph.

## Overview

Edge predicates define the **type and meaning** of relationships between entities in the knowledge graph. Each predicate has specific semantics, directionality, and expected cardinality patterns.

## Adoption Context

- **Original Capability:** `domain-data-model` — typed relationships between domain entities
- **Problem Statement:** DDD entities need typed relationships — pattern inheritance, entity derivation, capability-to-pattern tracing. Ad-hoc edge naming creates semantic ambiguity.
- **Selection Criteria:** Adopt W3C PROV-O standard predicates and apply to domain model edges. Every edge is a PROV-O predicate, ensuring interoperability and semantic precision.
- **ADR:** —

## Standards Foundation: W3C PROV-O

**SemOps edge predicates are based on [W3C PROV-O (Provenance Ontology)](https://www.w3.org/TR/prov-o/)**, the standard for expressing provenance information on the web. PROV-O provides a formal model for tracking how entities are derived, transformed, and attributed.

### Architecture Principle

**Edge predicates (PROV-O) describe relationships BETWEEN entities**
- Provenance chains and derivation lineage
- Citations and attributions
- Version succession and revisions
- Cross-entity dependencies

**Content metadata (SKOS) describes semantics WITHIN entities**
- Concept hierarchies (broader/narrower)
- Semantic typing and labels
- Internal concept organization
- See [content_metadata_v1.json](../schemas/metadata/content_metadata_v1.json)

This separation keeps provenance relationships (edges) distinct from semantic content (metadata).

---

## W3C PROV-O Alignment

### Core PROV-O Predicates (Direct Mapping)

| Our Predicate | PROV-O Property | W3C Specification |
|---------------|-----------------|-------------------|
| `derived_from` | `prov:wasDerivedFrom` | [wasDerivedFrom](https://www.w3.org/TR/prov-o/#wasDerivedFrom) |
| `cites` | `prov:wasQuotedFrom` | [wasQuotedFrom](https://www.w3.org/TR/prov-o/#wasQuotedFrom) |
| `version_of` | `prov:wasRevisionOf` | [wasRevisionOf](https://www.w3.org/TR/prov-o/#wasRevisionOf) |

### Schema.org Extensions

| Our Predicate | Schema.org Property | Specification |
|---------------|---------------------|---------------|
| `part_of` | `schema:isPartOf` | [isPartOf](https://schema.org/isPartOf) |
| `documents` | `schema:about` (inverted) | [about](https://schema.org/about) |

### SemOps Domain Extensions

| Our Predicate | Purpose | Inspired By |
|---------------|---------|-------------|
| `depends_on` | Prerequisite knowledge relationships | Software dependency graphs (npm, Maven) |
| `related_to` | General conceptual associations | SKOS `skos:related` |

### Novel Contributions

**Unique to SemOps:**
- **Provenance-aware lineage** - Combining PROV-O `wasDerivedFrom` with 1P/2P/3P provenance tracking
- **Strength weighting** - Quantifying relationship importance (0.0-1.0) for all edge types
- **Unified catalog** - Same predicates for both owned content (1P) and external references (3P)
- **Graph-native architecture** - Full bidirectional querying of provenance chains

---

## Core Predicates

### 1. `cites` - Attribution/Reference

**Meaning:** Source entity references or attributes the destination entity as a source of information.

**Origin:** Dublin Core `dcterms:references`, BibTeX citation patterns

**Direction:** Citing document → Cited source

**Cardinality:** Many-to-Many (N:N)
- One document can cite many sources
- One source can be cited by many documents

**Examples:**
```yaml
# Your blog post cites a research paper
source: blog-post-semantic-operations
predicate: cites
destination: paper-attention-is-all-you-need
strength: 0.9  # Primary citation

# Multiple blog posts cite the same paper
blog-post-1 → cites → paper-attention
blog-post-2 → cites → paper-attention
```

**Use Cases:**
- Research papers you reference
- Books/articles that influenced your thinking
- External sources you're attributing
- Academic citations
- Source material for derivative works

**Strength Guidelines:**
- 0.9-1.0: Primary source, essential citation
- 0.7-0.8: Important supporting source
- 0.5-0.6: Secondary reference
- 0.3-0.4: Tangential or passing reference

---

### 2. `derived_from` - Transformation/Extraction

**Meaning:** Source entity was created by transforming, extracting, or processing the destination entity.

**Origin:** W3C PROV-O `prov:wasDerivedFrom`, data lineage patterns

**Direction:** Derivative work → Original source

**Cardinality:** Many-to-One (typical, but N:N allowed)
- One derivative typically from one primary source
- Can have multiple sources for synthesis/merge operations

**Examples:**
```yaml
# Transcript you created from YouTube video
source: transcript-karpathy-llms
predicate: derived_from
destination: video-karpathy-youtube
strength: 1.0  # Direct derivation

# Summary you wrote of research paper
source: summary-dikw-framework
predicate: derived_from
destination: paper-ackoff-data-to-wisdom
strength: 1.0

# Multiple derivatives from same source (all valid)
transcript-karpathy → derived_from → video-karpathy
summary-karpathy → derived_from → video-karpathy
quotes-karpathy → derived_from → video-karpathy
```

**Key Insight:** The derivative is `provenance=1p` (you made it), but it has `derived_from` edge to the `provenance=3p` source.

**Use Cases:**
- Transcripts from videos/podcasts
- Summaries of papers/articles
- Quotes extracted from sources
- Translations
- Code examples from documentation
- Screenshots from applications
- Data extracts from datasets

**Strength Guidelines:**
- 1.0: Direct, mechanical transformation (transcript, screenshot)
- 0.8-0.9: Interpretive transformation (summary, analysis)
- 0.6-0.7: Synthesis with significant new content
- 0.4-0.5: Inspired by, but substantially different

---

### 3. `documents` - Explanation/Documentation

**Meaning:** Source entity provides explanation, documentation, or detailed information about the destination entity.

**Origin:** CMS documentation patterns, technical writing conventions

**Direction:** Documentation → Thing being documented

**Cardinality:** Many-to-Many (N:N)
- One guide can document multiple concepts
- One concept can have multiple guides

**Examples:**
```yaml
# Your guide documents the DIKW concept
source: guide-applying-dikw
predicate: documents
destination: dikw-framework-canonical
strength: 1.0

# Architecture doc documents the system
source: global-architecture-overview
predicate: documents
destination: ike-knowledge-ops-system
strength: 1.0

# One guide documents multiple concepts
guide-semops → documents → concept-dikw
guide-semops → documents → concept-knowledge-promotion
```

**Use Cases:**
- Guides explaining frameworks
- Documentation for code/systems
- Tutorials for concepts
- Reference materials
- User manuals
- API documentation
- Explainer articles

**Strength Guidelines:**
- 1.0: Comprehensive, authoritative documentation
- 0.8-0.9: Detailed coverage, primary reference
- 0.6-0.7: Partial coverage, supplementary docs
- 0.4-0.5: Brief mention, overview only

---

### 4. `related_to` - Semantic Association

**Meaning:** Source and destination are conceptually related but without strict hierarchy or dependency.

**Origin:** SKOS `skos:related`, general semantic web patterns

**Direction:** Less important (could be bidirectional)
- Pick the more natural direction
- Or create bidirectional edges

**Cardinality:** Many-to-Many (N:N)

**Examples:**
```yaml
# DDD relates to DIKW framework
source: domain-driven-design
predicate: related_to
destination: dikw-framework
strength: 0.7  # Strong conceptual overlap

# Semantic operations relates to knowledge ops
source: semantic-operations
predicate: related_to
destination: knowledge-operations
strength: 0.9  # Very closely related
```

**Use Cases:**
- Conceptually overlapping topics
- Cross-references between frameworks
- "See also" relationships
- Complementary concepts
- Parallel approaches to same problem

**Strength Guidelines:**
- 0.9-1.0: Deeply intertwined concepts
- 0.7-0.8: Strong thematic overlap
- 0.5-0.6: Moderate connection
- 0.3-0.4: Loose association

---

### 5. `depends_on` - Logical Dependency

**Meaning:** Understanding source requires understanding destination first. Represents prerequisite knowledge or logical dependencies.

**Origin:** Software dependency graphs (npm, Maven), build systems (Make), learning pathways

**Direction:** Advanced concept → Prerequisite concept

**Cardinality:** Many-to-Many (N:N)
- Advanced topics can depend on multiple prerequisites
- One foundational concept can be depended on by many advanced topics

**Examples:**
```yaml
# Advanced guide depends on basic tutorial
source: advanced-semantic-coherence
predicate: depends_on
destination: semantic-operations-intro
strength: 0.9

# Application depends on first principles
source: knowledge-promotion-practice
predicate: depends_on
destination: dikw-framework
strength: 0.8

# Multiple prerequisites
advanced-rag → depends_on → semantic-operations
advanced-rag → depends_on → vector-databases
advanced-rag → depends_on → llm-basics
```

**Use Cases:**
- Prerequisite relationships
- Learning paths
- Conceptual dependencies
- Build order for understanding
- Curriculum sequencing

**Strength Guidelines:**
- 0.9-1.0: Essential prerequisite, can't understand without it
- 0.7-0.8: Important foundation, strongly recommended
- 0.5-0.6: Helpful background, recommended
- 0.3-0.4: Optional context, nice to have

**Important:** Watch for circular dependencies! This predicate should form a DAG (Directed Acyclic Graph).

---

### 6. `part_of` - Compositional Hierarchy

**Meaning:** Source is a component, section, or part of the destination whole.

**Origin:** Schema.org `isPartOf`, DublinCore `dcterms:isPartOf`

**Direction:** Part → Whole

**Cardinality:** Many-to-One (recommended, but not enforced)
- A component is typically part of **one** whole
- The whole has many parts

**Examples:**
```yaml
# Specific chapter is part of broader framework
source: semantic-coherence-audit
predicate: part_of
destination: semantic-operations-framework
strength: 1.0

# Sub-concept is part of larger concept
source: progressive-semantics
predicate: part_of
destination: knowledge-promotion
strength: 1.0

# Multiple chapters in one book
chapter-1 → part_of → book-semops
chapter-2 → part_of → book-semops
chapter-3 → part_of → book-semops
```

**Use Cases:**
- Chapters in a book
- Sections in a framework
- Components in a system
- Sub-concepts in a larger concept
- Modules in a course
- Files in a collection

**Strength Guidelines:**
- 1.0: Essential component, defines the whole
- 0.8-0.9: Major component
- 0.6-0.7: Supporting component
- 0.4-0.5: Minor or optional component

**Recommendation:** Soft constraint - warn if one entity has `part_of` pointing to multiple parents (unusual but not invalid for cross-cutting structures).

---

### 7. `version_of` - Version Tracking

**Meaning:** Source is a different version of the same content as destination. Forms version chains.

**Origin:** Software version control patterns, CMS versioning systems

**Direction:** Newer version → Older version (convention)
- Or use explicit version numbers in entity metadata

**Cardinality:** One-to-One (ideally, forms a chain)
- Each version points to its immediate predecessor

**Examples:**
```yaml
# Version chain
source: dikw-framework-v3
predicate: version_of
destination: dikw-framework-v2
strength: 1.0

dikw-v3 → version_of → dikw-v2 → version_of → dikw-v1
```

**Use Cases:**
- Updated versions of docs
- Revised frameworks
- Iteration tracking
- Content evolution history
- Document revisions

**Strength Guidelines:**
- 1.0: Direct version succession (always)

**Recommendation:** Soft constraint - each entity should have at most one `version_of` edge (unless branching versions exist).

---

## Predicate Comparison Table

| Predicate      | Origin Domain         | Direction Important? | Typical Cardinality | Cyclical? |
|----------------|-----------------------|----------------------|---------------------|-----------|
| `cites`        | Bibliography/Citation | Yes (citer → cited)  | N:N                 | No        |
| `derived_from` | Data Lineage/PROV     | Yes (derivative → source) | N:1 (usually) | No        |
| `documents`    | CMS/Tech Docs         | Yes (docs → concept) | N:N                 | No        |
| `related_to`   | Semantic Web          | Less important       | N:N                 | Yes (symmetric) |
| `depends_on`   | Software Dependencies | Yes (advanced → prereq) | N:N              | **No (DAG)** |
| `part_of`      | Schema.org            | Yes (part → whole)   | N:1 (recommended)   | No        |
| `version_of`   | Version Control       | Yes (new → old)      | 1:1 (chain)         | No        |

---

## Cardinality Patterns

### No Hard Database Constraints

The schema **intentionally has no hard cardinality limits** at the database level. This provides maximum flexibility.

**What You Have:**
- ✅ One entity can have **many outgoing edges** (1:N from source)
- ✅ One entity can have **many incoming edges** (N:1 to destination)
- ✅ Same source + destination can have **multiple predicates** (different relationship types)

### Recommended: Prevent Duplicate Edges

You should prevent exact duplicates: same (source, destination, predicate).

**SQL Constraint:**
```sql
ALTER TABLE edge
ADD CONSTRAINT unique_edge_triple
UNIQUE (source_id, destination_id, predicate);
```

**Why:**
- `blog-post → cites → paper-1` should only exist once
- If you need to change strength, UPDATE the existing edge
- Prevents accidental duplicates during ingestion

---

## Application-Level Validation

### Optional Warnings

For predicates with expected patterns, consider application-level warnings:

```python
def validate_part_of_edges(entity_id):
    """Warn if entity is part_of multiple parents (unusual)."""
    cursor.execute(
        "SELECT COUNT(*) FROM edge WHERE source_id = %s AND predicate = 'part_of'",
        (entity_id,)
    )
    count = cursor.fetchone()[0]
    if count > 1:
        logger.warning(f"Entity {entity_id} has {count} part_of relationships (expected 1)")

def detect_circular_dependencies(predicate='depends_on'):
    """Detect cycles in dependency graph."""
    # Graph traversal to find cycles
    # Return list of entity_ids involved in cycles
```

---

## Relationship Strength Guidelines

| Strength Range | Meaning | Use Cases |
|----------------|---------|-----------|
| **1.0** | Essential, direct | Direct derivation, primary source, comprehensive documentation |
| **0.8-0.9** | Core relationship | Important citation, strong dependency, major component |
| **0.5-0.7** | Moderate | Related concepts, supporting citations, helpful background |
| **0.3-0.4** | Weak | Tangential reference, loose connection, optional context |

---

## Practical Example: Pillar Docs

Example frontmatter for your canonical framework docs:

```yaml
# DIKW.md (foundational framework)
---
entity_id: dikw-framework-canonical
content_type: framework
relationships:
  - predicate: cites
    target_file: semantic_first_principles.md
    strength: 0.9
---

# SEMANTIC_OPERATIONS.md
---
entity_id: semantic-operations-framework
content_type: framework
relationships:
  - predicate: depends_on
    target_file: DIKW.md
    strength: 0.9
  - predicate: related_to
    target_file: Domain_Driven_Design.md
    strength: 0.8
---

# Knowledge_Promotion.md
---
entity_id: knowledge-promotion-process
content_type: framework
relationships:
  - predicate: part_of
    target_file: SEMANTIC_OPERATIONS.md
    strength: 1.0
  - predicate: depends_on
    target_file: DIKW.md
    strength: 0.7
---

# Domain_Driven_Design.md
---
entity_id: ddd-framework-overview
content_type: framework
relationships:
  - predicate: documents
    target_id: ike-knowledge-ops-system
    strength: 1.0
  - predicate: related_to
    target_file: SEMANTIC_OPERATIONS.md
    strength: 0.8
---
```

---

## Questions to Ask When Choosing Predicates

1. **What's the nature of the connection?**
   - Citation/Reference? → `cites`
   - Transformation/Derivation? → `derived_from`
   - Explanation/Documentation? → `documents`
   - Conceptual overlap? → `related_to`
   - Logical prerequisite? → `depends_on`
   - Compositional hierarchy? → `part_of`
   - Version succession? → `version_of`

2. **Which direction makes sense?**
   - Who references whom?
   - What derives from what?
   - What explains what?
   - What depends on what?

3. **How strong is this relationship?**
   - Essential to understanding? → 0.9-1.0
   - Important but not critical? → 0.6-0.8
   - Nice to know? → 0.3-0.5

---

## Future Predicates

As the domain evolves, you may add predicates for:

- `supersedes` - New version replaces old (stronger than `version_of`)
- `implements` - Code implements spec/design
- `contradicts` - Conflicting viewpoints (useful for tracking debates)
- `inspired_by` - Weaker than `derived_from`, shows influence
- `exemplifies` - Example demonstrates concept
- `critiques` - Analysis/criticism relationship

**Guideline:** Only add new predicates when:
1. Existing predicates don't capture the semantic meaning
2. The relationship pattern appears frequently
3. You need to query specifically for this relationship type

---

## See Also

### Project Documentation
- [SKOS_PROVO_MAPPING.md](../SKOS_PROVO_MAPPING.md) - Visual diagram of SKOS + PROV-O architecture
- [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) - Edge entity definition
- [reference-edge-relationships.md](reference-edge-relationships.md) - High-level relationship patterns
- [edge-predicate-examples.md](edge-predicate-examples.md) - Real-world use case examples
- [frontmatter_template.md](../frontmatter_template.md) - How to use relationships in markdown docs
- [content_metadata_v1.json](../../schemas/metadata/content_metadata_v1.json) - SKOS-based metadata schema

### W3C Standards
- [W3C PROV-O](https://www.w3.org/TR/prov-o/) - Provenance Ontology (foundation for edge predicates)
- [W3C SKOS](https://www.w3.org/TR/skos-reference/) - Simple Knowledge Organization System (foundation for content_metadata)
- [Schema.org](https://schema.org/) - Structured data vocabulary
- [Dublin Core](https://www.dublincore.org/specifications/dublin-core/) - Metadata terms for citations

