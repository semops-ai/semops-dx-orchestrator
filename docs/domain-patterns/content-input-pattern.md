# Content Input Pattern

> A Zettelkasten-inspired pattern for structured content ideation that treats concepts as first-class semantic entities.

**Version:** 1.0.0
**Status:** active
**Last Updated:** 2025-12-03

---

## Overview

The Content Input Pattern defines a structured format for capturing content ideas before research and drafting. It draws from **Zettelkasten methodology** and aligns with **Domain-Driven Design** principles by treating concepts as semantic entities with explicit relationships.

### Core Principle

**Concepts are declared, not just referenced.** Using backtick notation (`` `concept-slug` ``) marks a term as a concept regardless of whether it exists in the knowledge base. This enables:

- Semantic linking to existing concepts
- Discovery of new concepts to potentially add
- Explicit relationship mapping between ideas

---

## Pattern Structure

### Input Document Sections

| Section | Purpose | DDD Alignment |
|---------|---------|---------------|
| **Core Narrative** | The central argument/POV with embedded concept links | Ubiquitous Language in prose |
| **Key Points** | Main arguments, each linked to supporting concepts | Entity relationships |
| **Connections to Explore** | Explicit questions about concept relationships | Context mapping |
| **Known References** | Existing docs/concepts to include as context | Bounded context imports |
| **Search Hints** | External queries (for future implementation) | External context discovery |

### Concept Declaration

Use backticks to declare concepts inline:

```markdown
## Core Narrative

The challenge with enterprise data systems is maintaining `semantic-coherence`
as data flows through multiple `bounded-contexts`. Traditional approaches
treat this as a `data-quality` problem, but it's fundamentally about
`meaning-preservation` across transformations.
```

**Behavior:**
- `` `semantic-coherence` `` - Looked up in knowledge base; if found, context included
- `` `meaning-preservation` `` - If not found, flagged as potential new concept

### Relationship Questions

The "Connections to Explore" section makes relationship discovery explicit:

```markdown
## Connections to Explore

- How does `semantic-operations` extend `dikw`?
- What's the boundary between `data-quality` and `semantic-coherence`?
- Is `meaning-preservation` a narrower concept of `semantic-operations`?
```

This mirrors **Context Mapping** in DDD - explicitly defining how concepts from different domains relate.

---

## Alignment with DDD

| DDD Concept | Content Input Pattern | Implementation |
|-------------|----------------------|----------------|
| **Ubiquitous Language** | Consistent concept slugs across all content | Backtick notation |
| **Bounded Context** | Each concept has clear boundaries | Concept definitions in knowledge base |
| **Entity Identity** | Concepts have stable slugs | `kebab-case` identifiers |
| **Aggregate Roots** | Core narrative concept anchors related concepts | `primary_concept` in output |
| **Context Mapping** | Explicit relationship questions | "Connections to Explore" section |
| **Domain Events** | Content creation triggers concept discovery | New concepts flagged for review |

---

## Alignment with Zettelkasten

| Zettelkasten Principle | Content Input Pattern | Implementation |
|------------------------|----------------------|----------------|
| **Atomic Notes** | Each concept is self-contained | Concept definitions in knowledge base |
| **Unique Identifiers** | Stable concept slugs | `kebab-case` IDs |
| **Explicit Links** | Inline concept references | Backtick notation |
| **Link Context** | Relationships have meaning | Predicate types (depends_on, extends, etc.) |
| **Emergence** | Meaning emerges from connections | Outline generation from linked context |

---

## Example Input

```markdown
# Experiment Input

## Core Narrative

Enterprise organizations struggle with `semantic-drift` - the gradual
loss of meaning as data moves between systems. While `data-governance`
frameworks focus on policy compliance, they miss the deeper issue:
`meaning` is contextual and must be actively preserved.

`Semantic-operations` offers a different approach: treating meaning
preservation as an operational concern, not just a governance one.
This connects `dikw` (where meaning transforms data into information)
with `domain-driven-design` (where meaning lives in bounded contexts).

## Key Points

- `Semantic-drift` is inevitable without active intervention
- `Data-quality` metrics miss semantic degradation
- `Bounded-contexts` from DDD provide the model for meaning boundaries
- `Semantic-operations` operationalizes meaning preservation

## Connections to Explore

- How does `semantic-drift` relate to `data-entropy`?
- Is `semantic-coherence` the inverse of `semantic-drift`?
- What predicates connect `semantic-operations` to `dikw`? (depends_on? extends?)

## Known References

- `dikw` (foundational framework)
- `domain-driven-design` (bounded context model)
- `semantic-operations` (our methodology)
- ./docs/SEMANTIC_OPERATIONS/SEMANTIC_OPERATIONS.md (primary doc)

## Search Hints

- "semantic interoperability enterprise"
- "meaning loss data integration"
```

---

## Output: Outline with Concept Links

The generated outline preserves concept links:

```markdown
# Outline: Why Data Quality Metrics Miss the Point

## Thesis

Enterprise data problems are fundamentally about `semantic-drift`,
not `data-quality` - and solving them requires `semantic-operations`.

## Sections

1. **The Hidden Problem** - `Semantic-drift` accumulates silently
   (evidence: `data-entropy` patterns)

2. **Why Governance Falls Short** - `Data-governance` focuses on
   policy, not `meaning-preservation` (evidence: `bounded-context` gaps)

3. **An Operational Approach** - `Semantic-operations` treats meaning
   as runtime concern (evidence: `dikw` transformation model)

## Flags

- [ ] Define `semantic-drift` if not in knowledge base
- [ ] Clarify relationship: `semantic-operations` → `dikw` (depends_on or extends?)
- [ ] Need external citation for data governance limitations
```

---

## Processing Behavior

### Concept Resolution

When processing input:

1. **Extract concepts** - Find all `` `backtick` `` terms
2. **Lookup existing** - Query knowledge base by slug
3. **Flag new concepts** - Mark unknown slugs for potential addition
4. **Build context** - Aggregate definitions and relationships
5. **Generate outline** - Use context to inform structure

### Relationship Discovery

The "Connections to Explore" section triggers:

1. **Edge queries** - Look for existing edges between mentioned concepts
2. **Gap identification** - Flag relationships that should exist but don't
3. **Predicate suggestions** - Recommend edge types based on question phrasing

---

## Constraints

| Constraint | Rationale |
|------------|-----------|
| Concepts use `kebab-case` slugs | Consistent with entity ID conventions |
| Core Narrative required | Cannot generate outline without central argument |
| At least one concept must be declared | Pattern requires semantic linking |
| Backticks reserved for concepts | Other inline code uses different notation |

---

## Anti-Patterns

### Concept Soup

**Problem:** Overloading narrative with concept links
```markdown
# Bad
The `challenge` with `enterprise` `data` `systems` is `maintaining` `quality`.
```

**Solution:** Only mark domain-specific concepts, not common words

### Orphan Concepts

**Problem:** Declaring concepts with no relationship context
```markdown
# Bad
## Key Points
- `concept-a`
- `concept-b`
- `concept-c`
```

**Solution:** Explain how concepts relate to the argument

### Missing Connections Section

**Problem:** Declaring concepts without exploring relationships
```markdown
# Bad - no Connections to Explore section
```

**Solution:** Always include relationship questions, even if uncertain

---

## Related Documentation

- **[reference-edge-relationships.md](reference-edge-relationships.md)** - Edge predicate vocabulary
- **[edge-predicates.md](edge-predicates.md)** - Predicate semantics
- **[dam-foundations.md](dam-foundations.md)** - SKOS and W3C alignment
- **[guide-doc-style.md](guide-doc-style.md)** - Schema terminology conventions

---

## Pattern Evolution

This pattern emerged from:

1. **Zettelkasten methodology** - Atomic notes with explicit links
2. **DDD Ubiquitous Language** - Consistent terminology across contexts
3. **SKOS concept schemes** - Hierarchical and associative relationships
4. **Content workflow needs** - Structured ideation before drafting

Future extensions may include:

- Predicate suggestion based on relationship question phrasing
- Automatic concept extraction from narrative (NER)
- Relationship strength inference from context

---

**Document Status:** Active | **Next Review:** 2026-01-03
**Maintainer:** SemOps Schema Team
