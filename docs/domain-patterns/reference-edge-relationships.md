# Relationships

> Edge predicates and graph patterns connecting entities in SemOps.

**Version:** 2.0.0
**Status:** active
**Last Updated:** 2025-11-23

---

## Overview

This document describes common **workflow patterns** using Edge predicates to connect entities in the SemOps knowledge graph. Edges represent semantic relationships between entities, enabling graph-based queries, lineage tracking, and knowledge discovery.

> **For comprehensive predicate documentation** including origins, semantics, cardinality patterns, and strength guidelines, see **[edge-predicates.md](edge-predicates.md)**.

**Key Concepts:**
- **Edge** - A relationship between two entities
- **Predicate** - The type of relationship (e.g., `cites`, `derived_from`, `documents`)
- **Strength** - Relationship weight (0.0 to 1.0) for ranking and filtering
- **Pattern** - Common edge configurations for specific workflows

---

## Pattern: Citation Chain

**Definition:** Series of references showing how ideas flow through content.

**Edge Type:** `cites`

**Structure:**
```
Your Blog Post → cites → Research Paper
Research Paper → cites → Foundational Paper
Your Blog Post → cites → Foundational Paper
```

**Use Cases:**
- Academic reference chains
- Building on prior work
- Supporting evidence
- Attributing ideas to sources

**Example:**
```json
// Your blog post entity
{
  "id": "blog-understanding-transformers",
  "provenance": "1p",
  "title": "Understanding Transformers"
}

// Research paper entity (3P)
{
  "id": "attention-is-all-you-need",
  "provenance": "3p",
  "title": "Attention Is All You Need"
}

// Citation edge from your blog to research paper
{
  "source_id": "blog-understanding-transformers",
  "destination_id": "attention-is-all-you-need",
  "predicate": "cites",
  "strength": 1.0,
  "metadata": {
    "context": "Primary reference for transformer architecture"
  }
}
```

**Graph Queries:**

Get all papers cited by your blog post:
```cypher
MATCH (blog:Entity {id: 'blog-understanding-transformers'})-[r:CITES]->(paper:Entity)
RETURN paper
```

Get all content that cites a specific paper:
```cypher
MATCH (content:Entity)-[r:CITES]->(paper:Entity {id: 'attention-is-all-you-need'})
RETURN content
```

---

## Pattern: Content Derivation

**Definition:** Transformations and extractions creating new artifacts from source material.

**Edge Type:** `derived_from`

**Structure:**
```
Transcript (1P) → derived_from → YouTube Video (3P)
Summary (1P) → derived_from → Research Paper (3P)
Quote (1P) → derived_from → Article (3P)
Translation (1P) → derived_from → Original Text (3P)
```

**Use Cases:**
- Content transformation (video → transcript)
- Extraction workflows (paper → summary)
- Format conversion (audio → text)
- Translation (source language → target language)

**Example - Transcript from Video:**
```json
// Your transcript (1P derivative)
{
  "id": "transcript-karpathy-llm-intro",
  "provenance": "1p",
  "asset_type": "file",
  "metadata": {
    "content_type": "transcript"
  }
}

// Source video (3P)
{
  "id": "video-karpathy-llm-intro",
  "provenance": "3p",
  "asset_type": "link",
  "filespec": {
    "uri": "https://youtube.com/watch?v=xyz"
  }
}

// Derivation edge
{
  "source_id": "transcript-karpathy-llm-intro",
  "destination_id": "video-karpathy-llm-intro",
  "predicate": "derived_from",
  "strength": 1.0,
  "metadata": {
    "transformation_type": "transcription",
    "tool": "whisper-api"
  }
}
```

**Example - Multi-Hop Derivation:**
```json
// Video (3P) → Transcript (1P) → Summary (1P)

// Summary
{
  "id": "summary-karpathy-llm",
  "provenance": "1p"
}

// Summary derived from transcript
{
  "source_id": "summary-karpathy-llm",
  "destination_id": "transcript-karpathy-llm-intro",
  "predicate": "derived_from",
  "strength": 1.0
}

// Transcript derived from video (from previous example)
{
  "source_id": "transcript-karpathy-llm-intro",
  "destination_id": "video-karpathy-llm-intro",
  "predicate": "derived_from",
  "strength": 1.0
}
```

**Graph Queries:**

Get derivation chain (multi-hop):
```cypher
MATCH path = (derived:Entity)-[:DERIVED_FROM*]->(source:Entity)
WHERE derived.id = 'summary-karpathy-llm'
RETURN path
```

Find all derivatives of a source:
```cypher
MATCH (derivative:Entity)-[r:DERIVED_FROM]->(source:Entity {id: 'video-karpathy-llm-intro'})
RETURN derivative
```

---

## Pattern: Documentation Graph

**Definition:** Explanatory content covering concepts or tools.

**Edge Type:** `documents`

**Structure:**
```
Blog Post (1P) → documents → Concept Definition (1P)
Tutorial (1P) → documents → Tool (3P)
Guide (1P) → documents → Framework (3P)
```

**Use Cases:**
- Educational content
- Technical documentation
- Concept explanations
- Tool guides

**Example:**
```json
// Blog post establishing and documenting the concept
{
  "id": "blog-semantic-coherence-explained",
  "provenance": "1p",
  "title": "Semantic Coherence Explained",
  "metadata": {
    "content_type": "blog_post",
    "primary_concept": "semantic-coherence",
    "preferred_label": "Semantic Coherence",
    "broader_concepts": ["semantic-operations"]
  }
}

// Another document referencing the same concept
{
  "id": "github-semantic-coherence-guide",
  "provenance": "1p",
  "title": "Implementation Guide for Semantic Coherence",
  "metadata": {
    "content_type": "github_doc",
    "primary_concept": "semantic-coherence"
  }
}

// Edge showing relationship between documents
{
  "source_id": "github-semantic-coherence-guide",
  "destination_id": "blog-semantic-coherence-explained",
  "predicate": "related_to",
  "strength": 0.8,
  "metadata": {
    "relationship_type": "complementary"
  }
}
```

**Graph Queries:**

Find all documentation for a concept:
```cypher
MATCH (doc:Entity)-[r:DOCUMENTS]->(concept:Entity {id: 'concept-semantic-coherence'})
RETURN doc
```

---

## Pattern: Dependency Graph

**Definition:** Required relationships between entities.

**Edge Type:** `depends_on`

**Structure:**
```
RAG Implementation → depends_on → Vector Database Concept
Advanced Tutorial → depends_on → Basic Tutorial
API Client → depends_on → API Specification
```

**Use Cases:**
- Learning paths (prerequisite tracking)
- Technical dependencies
- Build order requirements
- Conceptual foundations

**Example - Learning Path:**
```json
// Advanced tutorial
{
  "id": "tutorial-advanced-rag",
  "provenance": "1p",
  "title": "Advanced RAG Techniques"
}

// Basic tutorial (prerequisite)
{
  "id": "tutorial-basic-rag",
  "provenance": "1p",
  "title": "RAG Fundamentals"
}

// Dependency edge
{
  "source_id": "tutorial-advanced-rag",
  "destination_id": "tutorial-basic-rag",
  "predicate": "depends_on",
  "strength": 1.0,
  "metadata": {
    "dependency_type": "prerequisite"
  }
}
```

**Graph Queries:**

Get learning path (ordered by dependencies):
```cypher
MATCH path = (advanced:Entity)-[:DEPENDS_ON*]->(basic:Entity)
WHERE advanced.id = 'tutorial-advanced-rag'
RETURN path
ORDER BY length(path) DESC
```

Find all content that depends on a foundation:
```cypher
MATCH (content:Entity)-[r:DEPENDS_ON]->(foundation:Entity {id: 'tutorial-basic-rag'})
RETURN content
```

---

## Pattern: Implementation Map

**Definition:** Concrete realizations of abstract concepts.

**Edge Type:** `implements`

**Structure:**
```
Pinecone Tool → implements → Vector Database Concept
LangChain Library → implements → RAG Framework
GPT-4 → implements → Large Language Model Concept
```

**Use Cases:**
- Tool selection (finding implementations of concepts)
- Technology mapping
- Architectural decisions
- Vendor/option comparison

**Example:**
```json
// Document establishing the concept
{
  "id": "github-vector-database-concept",
  "provenance": "1p",
  "title": "Vector Database Concept",
  "metadata": {
    "content_type": "github_doc",
    "primary_concept": "vector-database",
    "preferred_label": "Vector Database"
  }
}

// Implementation 1
{
  "id": "tool-pinecone",
  "provenance": "3p",
  "title": "Pinecone"
}

// Implementation 2
{
  "id": "tool-weaviate",
  "provenance": "3p",
  "title": "Weaviate"
}

// Implementation edges
{
  "source_id": "tool-pinecone",
  "destination_id": "concept-vector-database",
  "predicate": "implements",
  "strength": 1.0,
  "metadata": {
    "maturity": "production"
  }
}

{
  "source_id": "tool-weaviate",
  "destination_id": "concept-vector-database",
  "predicate": "implements",
  "strength": 1.0,
  "metadata": {
    "maturity": "production"
  }
}
```

**Graph Queries:**

Find all implementations of a concept:
```cypher
MATCH (tool:Entity)-[r:IMPLEMENTS]->(concept:Entity {id: 'concept-vector-database'})
RETURN tool
```

Find what concepts a tool implements:
```cypher
MATCH (tool:Entity {id: 'tool-pinecone'})-[r:IMPLEMENTS]->(concept:Entity)
RETURN concept
```

---

## Pattern: Related Concepts

**Definition:** Conceptual relationships without hierarchy.

**Edge Type:** `related_to`

**Structure:**
```
Semantic Coherence → related_to → Semantic Drift
RAG → related_to → Vector Search
Knowledge Graph → related_to → Ontology
```

**Use Cases:**
- Concept navigation
- Discovery of related topics
- Associative knowledge graphs
- Non-hierarchical relationships

**Example:**
```json
// Concept 1
{
  "id": "concept-semantic-coherence",
  "provenance": "1p"
}

// Concept 2
{
  "id": "concept-semantic-drift",
  "provenance": "1p"
}

// Bidirectional relationship (create both edges)
{
  "source_id": "concept-semantic-coherence",
  "destination_id": "concept-semantic-drift",
  "predicate": "related_to",
  "strength": 0.8,
  "metadata": {
    "relationship_nature": "inverse"
  }
}

{
  "source_id": "concept-semantic-drift",
  "destination_id": "concept-semantic-coherence",
  "predicate": "related_to",
  "strength": 0.8,
  "metadata": {
    "relationship_nature": "inverse"
  }
}
```

---

## Composite Patterns

Multiple edge types working together.

### Pattern: Complete Content Lineage

**Definition:** Full provenance chain from source through derivatives to citations.

**Structure:**
```
Your Blog Post → cites → Your Quote
Your Quote → derived_from → Research Paper (3P)
Research Paper → cites → Foundational Paper (3P)
```

**Example:**
```json
// Your blog post
{
  "id": "blog-transformers-explained"
}

// Your extracted quote
{
  "id": "quote-attention-mechanism",
  "provenance": "1p"
}

// Research paper (source)
{
  "id": "paper-attention-is-all-you-need",
  "provenance": "3p"
}

// Edge 1: Blog cites your quote
{
  "source_id": "blog-transformers-explained",
  "destination_id": "quote-attention-mechanism",
  "predicate": "cites",
  "strength": 0.9
}

// Edge 2: Quote derived from paper
{
  "source_id": "quote-attention-mechanism",
  "destination_id": "paper-attention-is-all-you-need",
  "predicate": "derived_from",
  "strength": 1.0
}
```

**Query - Full Attribution Chain:**
```cypher
MATCH path = (blog:Entity {id: 'blog-transformers-explained'})
  -[:CITES|DERIVED_FROM*]->(source:Entity)
RETURN path
```

---

## Pattern Evolution

As the domain evolves, new relationship patterns may emerge. Document them using this structure:

### Pattern Template

```markdown
### Pattern: [Pattern Name]

**Definition:** [What this pattern represents]

**Edge Type:** `predicate_name`

**Structure:**
[ASCII diagram or description]

**Use Cases:**
- Use case 1
- Use case 2

**Example:**
[JSON example with entities and edges]

**Graph Queries:**
[Cypher or similar queries]
```

---

## Edge Metadata Best Practices

Edges can carry additional context via metadata:

```json
{
  "source_id": "entity-a",
  "destination_id": "entity-b",
  "predicate": "cites",
  "strength": 0.9,
  "metadata": {
    "context": "Primary reference for algorithm",
    "page_number": "42",
    "quote": "excerpt text",
    "added_at": "2024-11-15T10:00:00Z",
    "added_by": "agent-id-123"
  }
}
```

**Common Metadata Fields:**
- `context` - Why this relationship exists
- `confidence` - How certain we are (0.0-1.0)
- `tool` - Tool that created the edge (for `derived_from`)
- `added_at` - Timestamp
- `added_by` - User or agent ID
- `page_number` / `timestamp` - Location in source

---

## Related Documentation

- [edge-predicates.md](edge-predicates.md) - Comprehensive predicate reference
- [edge-predicate-examples.md](edge-predicate-examples.md) - Detailed use case scenarios
- [publication-patterns.md](publication-patterns.md) - How edges support publication workflows
- [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) - Edge entity definition

---

**Document Status:** Active | **Next Review:** 2025-12-23
**Maintainer:** SemOps Schema Team
**Change Process:** New relationship patterns should align with W3C PROV-O standards where applicable
