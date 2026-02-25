# Related Links Convention

> **Version:** 1.0.0
> **Status:** draft
> **Last Updated:** 2026-01-10
> **Provenance:** 1P
> **Derives From:** edge-predicates

Standard format for documenting relationships at the end of content documents, enabling automated edge extraction by ingestion agents.

---

## Overview

Every content document should include a "Related Links" section at the end that explicitly declares relationships to other entities. This convention enables:

1. **Automated edge extraction** - Ingestion agents can parse and create edges
2. **Self-documenting provenance** - Relationships are visible in the source content
3. **3P entity discovery** - External links become candidates for catalog inclusion
4. **Consistent relationship typing** - Section headings map to edge predicates

## Section Format

```markdown
---

## Related Links

### Citations
- [Title](URL) - Optional description

### See Also
- [Title](URL)

### Builds On
- [Title](URL)
```

## Predicate Mapping

| Section Heading | Edge Predicate | Direction | Typical Use |
|-----------------|----------------|-----------|-------------|
| **Citations** | `cites` | source → destination | Academic references, authoritative sources |
| **See Also** | `related_to` | source ↔ destination | Conceptually related content |
| **Builds On** | `depends_on` | source → destination | Foundational concepts this content requires |
| **Derived From** | `derived_from` | source → destination | Source material transformed into this content |
| **Implements** | `implements` | source → destination | Abstract concept made concrete |
| **Part Of** | `part_of` | source → destination | Component of larger work |
| **Further Reading** | `related_to` | source → destination | Supplementary material (lower strength) |

## URL Patterns for Provenance Detection

Ingestion agents should use URL patterns to determine provenance:

| Pattern | Provenance | Example |
|---------|------------|---------|
| Relative path (`../`, `./`) | 1P | `../SEMANTIC_OPERATIONS/semantic-coherence.md` |
| Same GitHub org | 1P | `https://github.com/semops-ai/semops-docs/...` |
| External URL | 3P | `https://en.wikipedia.org/wiki/DIKW_pyramid` |
| Partner org URL | 2P | (define partner orgs in config) |

## Strength Guidelines

| Context | Suggested Strength |
|---------|-------------------|
| Primary citation (core dependency) | 1.0 |
| Important reference | 0.8-0.9 |
| Supplementary/related | 0.5-0.7 |
| Tangential mention | 0.3-0.5 |

Agents may infer strength from:
- Section type (Citations > See Also > Further Reading)
- Position in list (first items often more important)
- Explicit annotations (if provided)

## Complete Example

```markdown
---

## Related Links

### Citations
- [DIKW Pyramid - Wikipedia](https://en.wikipedia.org/wiki/DIKW_pyramid) - Foundational framework
- [Ackoff, R. L. (1989). From Data to Wisdom](https://doi.org/10.1016/0378-7206(89)90062-1) - Original academic source
- [Domain-Driven Design Reference](https://www.domainlanguage.com/ddd/reference/) - Eric Evans' DDD summary

### Builds On
- [DIKW Hierarchy](../FIRST_PRINCIPLES/dikw.md) - Data-to-wisdom transformation model
- [Domain-Driven Design](../GLOBAL_ARCHITECTURE/domain-driven-design.md) - Ubiquitous language patterns

### See Also
- [Semantic Coherence](../SEMANTIC_OPERATIONS/semantic-coherence.md) - Related concept
- [The Hard Problem](../AI_TRANSFORMATION/the-hard-problem.md) - Application context

### Further Reading
- [Knowledge Management on Wikipedia](https://en.wikipedia.org/wiki/Knowledge_management)
```

## Agent Processing Rules

When an ingestion agent encounters a Related Links section:

1. **Parse each section** to determine predicate type
2. **For each link:**
   - Extract title and URL
   - Determine provenance from URL pattern
   - Check if destination entity exists in catalog
   - If 3P and not in catalog, flag for potential addition
3. **Create edges:**
   - `source_id`: Current document's entity_id
   - `destination_id`: Target entity_id (or URL for unresolved 3P)
   - `predicate`: From section heading mapping
   - `strength`: From guidelines or explicit annotation
4. **Log unresolved references** for manual review

## Edge Cases

### Unresolved 3P Links
When a 3P link doesn't have a corresponding entity:
- Create edge with `destination_id` as URL (temporary)
- Flag for entity creation review
- Or auto-create 3P entity with `approval_status: pending`

### Bidirectional Relationships
For `related_to` edges, consider creating inverse edge on destination if both are 1P content under your control.

### Multiple Links to Same Entity
If a document links to the same entity in multiple sections:
- Create single edge with highest-priority predicate
- Or create multiple edges if semantically distinct

## Validation

Documents should be validated for:
- [ ] Related Links section exists
- [ ] At least one link in Citations or Builds On (for non-foundational docs)
- [ ] All relative links resolve to valid files
- [ ] No orphan documents (nothing links to them)

## Migration Path

For existing documents without Related Links:
1. Agent scans inline links in document body
2. Suggests Related Links section based on link analysis
3. Human reviews and approves/modifies
4. Section added to document

---

## Related Links

### Builds On
- [Edge Predicates](./edge-predicates.md) - Complete predicate semantics
- [Provenance and Lineage](./provenance-and-lineage.md) - 1P/2P/3P patterns

### See Also
- [Doc Style Guide](./guide-doc-style.md) - General documentation conventions
- [Foundation Entity Catalog](../foundation/foundation-entity-catalog.yaml) - Golden sample using this convention

