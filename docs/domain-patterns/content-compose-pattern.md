# Content Compose Pattern

> A Zettelkasten-inspired pattern for composing atomic content entities into structured documents through explicit edge relationships.

**Version:** 1.1.0
**Status:** active
**Last Updated:** 2025-12-31
**Provenance:** 1P
**Derives From:** Zettelkasten

## Overview

The Content Compose Pattern defines how atomic content entities ("zettels") are assembled into larger composed structures ("structure notes") for document generation. It extends the [Content Input Pattern](content-input-pattern.md) from ideation into production.

### Core Principle

**Composition is explicit, not implicit.** Rather than embedding content inline, composed documents reference atomic entities via edges. The final document is *generated* from these relationships, preserving:

- Semantic traceability (every claim links to its source)
- Reusability (atoms can appear in multiple structures)
- Maintainability (update the atom, all structures reflect the change)

---

## The Three-Layer Model

Adapted from Zettelkasten methodology, SemOps uses a three-layer composition hierarchy:

```
┌─────────────────────────────────────────────────────────────────────┐
│  L1: STRUCTURE LAYER                                                │
│  content_type: article, paper, guide, presentation                  │
│  Purpose: Final composed documents for delivery                     │
│                                                                     │
│    Uses edges: documents, cites, derived_from                       │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│  L2: HUB LAYER                                                      │
│  content_type: methodology, framework, pattern                      │
│  Purpose: Organizing structures that group related concepts         │
│                                                                     │
│    Uses edges: part_of (atoms → hub), related_to, depends_on        │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│  L3: ATOMIC LAYER                                                   │
│  content_type: definition, principle, quote, example                │
│  Purpose: Self-contained, single-idea entities                      │
│                                                                     │
│    Uses edges: cites (to 3P sources), derived_from                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Layer Characteristics

| Layer | Note Type | Entity Pattern | Provenance | Typical Visibility |
|-------|-----------|----------------|------------|-------------------|
| L1 | Structure Note | `article`, `paper`, `guide` | 1P | public |
| L2 | Hub Note | `methodology`, `framework` | 1P | public or private |
| L3 | Atomic Note | `definition`, `principle`, `quote` | 1P or 3P | private (internal use) |

---

## Composition via Edges

### Edge Predicates for Composition

| Predicate | Composition Role | Direction | Example |
|-----------|------------------|-----------|---------|
| `part_of` | Atom belongs to hub | Atom → Hub | `semantic-drift` part_of `semantic-operations` |
| `documents` | Structure explains concepts | Structure → Hub/Atom | Article documents `dikw` framework |
| `cites` | Any layer references source | Citer → Cited | Definition cites Ackoff 1989 |
| `depends_on` | Prerequisite relationship | Advanced → Prereq | `semantic-coherence` depends_on `bounded-context` |
| `derived_from` | Synthesis from source | Derivative → Source | Summary derived_from paper |

### Composition Graph Example

```
                    ┌──────────────────────────┐
                    │  L1: STRUCTURE           │
                    │  blog-semantic-drift     │
                    │  content_type: article   │
                    └───────────┬──────────────┘
                                │
              ┌─────────────────┼─────────────────┐
              │ documents       │ documents       │ cites
              ▼                 ▼                 ▼
    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
    │  L2: HUB        │ │  L2: HUB        │ │  L3: ATOM (3P)  │
    │  semantic-ops   │ │  dikw-framework │ │  paper-ackoff   │
    │  methodology    │ │  framework      │ │  paper          │
    └────────┬────────┘ └────────┬────────┘ └─────────────────┘
             │                   │
    ┌────────┴────────┐    ┌─────┴─────┐
    │ part_of         │    │ part_of   │
    ▼                 ▼    ▼           ▼
┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐
│ L3: ATOM  │  │ L3: ATOM  │  │ L3: ATOM  │  │ L3: ATOM  │
│ semantic- │  │ semantic- │  │ data      │  │ wisdom    │
│ drift     │  │ coherence │  │ definition│  │ definition│
│ definition│  │ definition│  │           │  │           │
└───────────┘  └───────────┘  └───────────┘  └───────────┘
```

---

## Entity Patterns by Layer

### L3: Atomic Entities

Atomic entities are self-contained, single-concept notes. They answer ONE question or define ONE thing.

**Characteristics:**
- `content_type`: `definition`, `principle`, `quote`, `example`, `clip`
- `primary_concept`: The concept this entity establishes
- `metadata.quality_score`: Relevance/quality for RAG retrieval
- Typically `visibility: private` (internal knowledge base)

**Example - Definition Entity:**
```json
{
  "id": "def-semantic-drift",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "private",
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "definition",
    "primary_concept": "semantic-drift",
    "preferred_label": "Semantic Drift",
    "definition": "The gradual loss of meaning as data moves between systems without active preservation of semantic context.",
    "broader_concepts": ["semantic-operations"],
    "media_type": "text",
    "quality_score": 0.9
  }
}
```

**Example - Quote Entity (3P):**
```json
{
  "id": "quote-ackoff-dikw-1989",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "private",
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "quote",
    "primary_concept": "dikw",
    "preferred_label": "Ackoff on Wisdom",
    "media_type": "text",
    "quality_score": 1.0
  },
  "attribution": {
    "$schema": "attribution_v2",
    "creator": ["Russell Ackoff"],
    "source_reference": "Ackoff (1989)",
    "concept_ownership": "3p"
  }
}
```

With edge:
```json
{
  "source_id": "quote-ackoff-dikw-1989",
  "destination_id": "paper-ackoff-data-to-wisdom",
  "predicate": "derived_from",
  "strength": 1.0,
  "metadata": {"extraction_type": "quote"}
}
```

### L2: Hub Entities

Hub entities organize related atoms into coherent conceptual groupings. They serve as **finding aids** and **context aggregators**.

**Characteristics:**
- `content_type`: `methodology`, `framework`, `pattern`, `glossary`
- Aggregate multiple `part_of` incoming edges from atoms
- May have `depends_on` edges to other hubs
- Can be public (published framework) or private (internal organization)

**Example - Methodology Hub:**
```json
{
  "id": "hub-semantic-operations",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "public",
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "methodology",
    "primary_concept": "semantic-operations",
    "preferred_label": "Semantic Operations",
    "definition": "A methodology synthesizing DIKW, DDD, and W3C standards for meaning-preserving data operations.",
    "narrower_concepts": ["semantic-drift", "semantic-coherence", "knowledge-promotion"],
    "media_type": "text"
  }
}
```

Atoms connect via `part_of`:
```json
{
  "source_id": "def-semantic-drift",
  "destination_id": "hub-semantic-operations",
  "predicate": "part_of",
  "strength": 1.0
}
```

### L1: Structure Entities

Structure entities are composed documents ready for delivery. They **assemble** atoms and hubs into coherent arguments.

**Characteristics:**
- `content_type`: `article`, `paper`, `guide`, `presentation`, `tutorial`
- `documents` edges to hubs/atoms they explain
- `cites` edges to sources they reference
- Typically `visibility: public` (for delivery)
- Have associated `delivery` records when published

**Example - Article Structure:**
```json
{
  "id": "article-why-data-quality-misses-point",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "public",
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "article",
    "primary_concept": "semantic-drift",
    "preferred_label": "Why Data Quality Metrics Miss the Point",
    "summary": "Enterprise data problems are fundamentally about semantic drift, not data quality.",
    "media_type": "text",
    "word_count": 2500,
    "reading_time_minutes": 10
  }
}
```

With composition edges:
```json
[
  {
    "source_id": "article-why-data-quality-misses-point",
    "destination_id": "hub-semantic-operations",
    "predicate": "documents",
    "strength": 1.0
  },
  {
    "source_id": "article-why-data-quality-misses-point",
    "destination_id": "hub-dikw-framework",
    "predicate": "documents",
    "strength": 0.8
  },
  {
    "source_id": "article-why-data-quality-misses-point",
    "destination_id": "paper-ackoff-data-to-wisdom",
    "predicate": "cites",
    "strength": 0.9
  }
]
```

---

## Composition Workflow

### Phase 1: Ideation (Content Input Pattern)

Use [Content Input Pattern](content-input-pattern.md) to capture the initial idea:

```markdown
## Core Narrative

Enterprise organizations struggle with `semantic-drift` - the gradual
loss of meaning as data moves between systems...

## Connections to Explore

- How does `semantic-drift` relate to `data-entropy`?
- Is `semantic-coherence` the inverse of `semantic-drift`?
```

### Phase 2: Concept Resolution

Process the input to:

1. **Lookup existing atoms** - Find `semantic-drift`, `semantic-coherence` in knowledge base
2. **Flag new concepts** - Mark undefined concepts for potential creation
3. **Identify hubs** - Determine which methodologies/frameworks are relevant
4. **Build context** - Aggregate definitions and relationships

### Phase 3: Structure Assembly

Create the L1 structure entity with edges:

```yaml
# Structure entity
id: article-why-data-quality-misses-point
content_type: article

# Composition edges
edges:
  - predicate: documents
    destination: hub-semantic-operations
    strength: 1.0

  - predicate: documents
    destination: def-semantic-drift
    strength: 0.9

  - predicate: cites
    destination: paper-ackoff-data-to-wisdom
    strength: 0.9

  - predicate: depends_on
    destination: hub-dikw-framework
    strength: 0.8
```

### Phase 4: Content Generation

Generate the final document by:

1. **Traverse edges** - Follow `documents` and `cites` edges to gather context
2. **Inject definitions** - Pull in atom content where referenced
3. **Build citations** - Generate reference list from `cites` edges
4. **Validate coherence** - Ensure all concept references resolve

### Phase 5: Delivery

Create delivery records for target surfaces:

```json
{
  "entity_id": "article-why-data-quality-misses-point",
  "surface_id": "wordpress-main-blog",
  "role": "original",
  "status": "published"
}
```

---

## Querying the Composition Graph

### Find All Atoms in a Structure

```sql
-- Get all atomic entities that a structure documents
WITH RECURSIVE composition AS (
  -- Direct edges from structure
  SELECT destination_id, predicate, strength, 1 as depth
  FROM edge
  WHERE source_id = 'article-why-data-quality-misses-point'
    AND predicate IN ('documents', 'cites')

  UNION

  -- Atoms that are part_of hubs
  SELECT e.source_id, e.predicate, e.strength, c.depth + 1
  FROM edge e
  JOIN composition c ON e.destination_id = c.destination_id
  WHERE e.predicate = 'part_of'
    AND c.depth < 3
)
SELECT DISTINCT destination_id, predicate, strength, depth
FROM composition
ORDER BY depth, strength DESC;
```

### Build Context for Generation

```sql
-- Get all context needed to generate a structure
SELECT
  e.id,
  e.metadata->>'content_type' as content_type,
  e.metadata->>'preferred_label' as label,
  e.metadata->>'definition' as definition,
  edge.predicate,
  edge.strength
FROM entity e
JOIN edge ON e.id = edge.destination_id
WHERE edge.source_id = 'article-why-data-quality-misses-point'
ORDER BY edge.strength DESC;
```

### Detect Missing Atoms

```sql
-- Find concepts referenced but not defined
SELECT DISTINCT jsonb_array_elements_text(
  e.metadata->'narrower_concepts' ||
  e.metadata->'related_concepts'
) as concept_slug
FROM entity e
WHERE e.id = 'hub-semantic-operations'
  AND NOT EXISTS (
    SELECT 1 FROM entity
    WHERE metadata->>'primary_concept' = concept_slug
  );
```

---

## Composition Constraints

| Constraint | Rationale |
|------------|-----------|
| Atoms should have `primary_concept` | Required for semantic linking |
| Hubs should have `narrower_concepts` populated | Documents what atoms belong |
| Structures must have at least one `documents` edge | Cannot compose without content |
| `part_of` edges should form a DAG | No circular containment |
| Atoms should be `visibility: private` until promoted | Internal knowledge base first |

---

## Anti-Patterns

### Inline Embedding

**Problem:** Copying atom content directly into structure
```markdown
# Bad - content duplicated
Semantic drift is the gradual loss of meaning...
```

**Solution:** Reference via edge, generate at render time
```yaml
edges:
  - predicate: documents
    destination: def-semantic-drift
```

### Flat Structure

**Problem:** Structure documents only atoms, no hubs
```
article → documents → def-1
article → documents → def-2
article → documents → def-3
# No organizing principle
```

**Solution:** Use hubs to group related atoms
```
article → documents → hub-semantic-ops
                          ↑
              def-1, def-2, def-3 (part_of)
```

### Orphan Atoms

**Problem:** Atoms with no `part_of` edges
```json
{
  "id": "def-semantic-drift",
  "edges": []  // No connections
}
```

**Solution:** Every atom should belong to at least one hub

### Missing Provenance Chain

**Problem:** Quote without `derived_from` edge to source
```json
{
  "id": "quote-ackoff",
  "content_type": "quote",
  "edges": []  // Where did this come from?
}
```

**Solution:** Always link quotes/extracts to their 3P source

---

## Relationship to Zettelkasten

| Zettelkasten Concept | SemOps Implementation |
|----------------------|---------------------------|
| Atomic Notes | Entities with `content_type: definition, principle, quote` |
| Unique Identifiers | `id` field (kebab-case slugs) |
| Hub Notes | Entities with `content_type: methodology, framework` |
| Structure Notes | Entities with `content_type: article, paper, guide` |
| Explicit Links | Edge relationships with predicates |
| Link Context | `predicate` + `strength` on edges |
| Emergence | Composition graph enables discovery |
| One Zettelkasten for Life | Single `entity` table as aggregate root |

---

## Integration Points

### RAG/Vector Search

Atoms (L3) are indexed for retrieval:
- `quality_score` influences ranking
- `primary_concept` enables concept-based search
- Edge graph enables context expansion

### Content Input Pattern

[content-input-pattern.md](content-input-pattern.md) feeds this pattern:
- Backtick notation identifies concepts to resolve
- "Connections to Explore" maps to edge discovery
- "Known References" maps to `cites` edges

### Publication Patterns

[publication-patterns.md](publication-patterns.md) governs delivery:
- Structures (L1) with `visibility: public` can be delivered
- Atoms (L3) typically remain `visibility: private`
- Hubs (L2) may be either depending on use case

---

## Pattern Evolution

This pattern emerged from:

1. **Zettelkasten methodology** - Three-layer note hierarchy
2. **DDD Aggregates** - Entity as single aggregate root
3. **PROV-O derivation chains** - Edge predicates for composition
4. **Content Input Pattern** - Ideation workflow integration

Future extensions may include:

- Automated hub detection from atom clustering
- Strength inference from co-occurrence patterns
- Structure template generation from hub structures
- Multi-structure reuse tracking for atoms

---

## Related Documentation

- **[content-input-pattern.md](content-input-pattern.md)** - Ideation workflow (Phase 1)
- **[edge-predicates.md](edge-predicates.md)** - Predicate semantics
- **[reference-edge-relationships.md](reference-edge-relationships.md)** - High-level relationship patterns
- **[publication-patterns.md](publication-patterns.md)** - Delivery workflows
- **[reference-entity-constraints.md](reference-entity-constraints.md)** - Validation rules

---

**Document Status:** Active | **Next Review:** 2026-01-04
**Maintainer:** SemOps Schema Team

