# Edge Predicate Examples

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-01-10
> **Provenance:** 1P
> **Derives From:** prov-o, edge-predicates

Practical examples showing how to choose the right semantic relationships.

## Overview

This document provides real-world examples of choosing Edge predicates for knowledge graph relationships. For comprehensive predicate documentation, see [edge-predicates.md](edge-predicates.md).

**Key Principle:** Edge predicates capture **semantic meaning**, not just structural connections. The predicate you choose reveals **how concepts relate**, not just that they're connected.

### Architecture Foundation

SemOps follows W3C standards for semantic web architecture:

**Edge Predicates (PROV-O)** - Relationships BETWEEN entities
- Based on [W3C PROV-O (Provenance Ontology)](https://www.w3.org/TR/prov-o/)
- Express provenance, derivation, citations, dependencies
- Enable graph traversal and relationship queries

**Content Metadata (SKOS)** - Semantics WITHIN entities
- Based on [W3C SKOS (Simple Knowledge Organization System)](https://www.w3.org/TR/skos-reference/)
- Express concept hierarchies, labels, definitions
- Stored in `content_metadata` value object

See [SKOS_PROVO_MAPPING.md](../SKOS_PROVO_MAPPING.md) for visual architecture diagram.


## Adoption Context

- **Original Capability:** `domain-data-model` — reference documentation for edge predicates
- **Problem Statement:** PROV-O edge types need concrete examples showing how to use `:wasDerivedFrom`, `:wasInformedBy`, and other predicates in SemOps context. Abstract definitions are insufficient for implementation.
- **Selection Criteria:** Reference companion to `edge-predicates` — concrete usage patterns showing predicate selection in practice. Extends PROV-O with SemOps-specific worked examples.
- **ADR:** —

## Use Case 1: Framework with Derivative Explanations

### Scenario: The Hard Problem of AI Transformation

**Context:** You have a parent framework document that takes a 3P concept (Hard Problem of Consciousness) and creates a 1P analogy to analyze the AI Transformation Problem. You then spin off three detailed explanation documents.

**File Structure:**
```
/docs/AI_TRANSFORMATION/
├── THE_HARD_PROBLEM.md          # Parent framework (1P)
├── RUNTIME_EMERGENCE.md          # Explanation 1 (1P)
├── THE_REGRESSION_PARADOX.md    # Explanation 2 (1P)
└── SEMANTIC_DECOHERENCE.md      # Explanation 3 (1P)
```

### Relationship Analysis

#### 1. Parent Framework → 3P Philosophical Concept

**THE_HARD_PROBLEM.md → Hard Problem of Consciousness (David Chalmers)**

```yaml
# In THE_HARD_PROBLEM.md frontmatter
relationships:
  - predicate: related_to
    target_id: hard-problem-consciousness-chalmers-3p
    strength: 0.9
```

**Predicate Choice: `related_to`**

**Why NOT `derived_from`?**
- You're not transforming Chalmers' work directly (not a transcript, summary, or extraction)
- You're drawing a **conceptual analogy** between consciousness studies and AI transformation
- The relationship is thematic/conceptual, not transformational

**Why NOT `cites`?**
- While you do reference Chalmers, `cites` typically means direct attribution/sourcing
- `related_to` better captures "I'm using this as an analogy framework"
- You could add `cites` edges to specific papers if you quote them directly

**Why `related_to`?**
- ✅ Captures strong thematic overlap between domains
- ✅ Shows conceptual inspiration without claiming derivation
- ✅ Symmetric relationship (consciousness problem ↔ transformation problem)

**Strength: 0.9** (very strong conceptual connection, central to your framework)

---

#### 2. Parent Framework → 3P Industry Problem

**THE_HARD_PROBLEM.md → AI Transformation Problem (Industry Concept)**

```yaml
relationships:
  - predicate: documents
    target_id: ai-transformation-problem-industry-3p
    strength: 1.0
```

**Predicate Choice: `documents`**

**Why `documents`?**
- ✅ The AI Transformation Problem already exists in industry discourse (3P)
- ✅ Your framework provides **comprehensive explanation and analysis** of it
- ✅ Direction: Your documentation → The concept being documented
- ✅ Classic "docs → thing documented" pattern

**Why NOT `cites`?**
- `cites` would be for referencing specific sources (research papers, blog posts)
- This is broader: you're documenting/explaining the entire problem space

**Why NOT `related_to`?**
- Too weak - you're doing more than just relating to it
- `documents` captures that you're providing authoritative analysis

**Strength: 1.0** (comprehensive, authoritative documentation)

---

#### 3. Explanation Documents → Parent Framework

**RUNTIME_EMERGENCE.md / THE_REGRESSION_PARADOX.md / SEMANTIC_DECOHERENCE.md → THE_HARD_PROBLEM.md**

```yaml
# In each of the three explanation docs
relationships:
  - predicate: part_of
    target_file: THE_HARD_PROBLEM.md
    strength: 1.0
```

**Predicate Choice: `part_of`**

**Why `part_of`?**
- ✅ Each document is a **component** of the larger framework
- ✅ THE_HARD_PROBLEM sets up three problems; these docs **are** those three problems
- ✅ Direction: Part → Whole (explanation → framework)
- ✅ Compositional hierarchy (like chapters in a book)

**Why NOT `depends_on`?**
- `depends_on` would mean "you must read X before understanding Y"
- THE_HARD_PROBLEM can be understood **without** the detailed explanations (it has summaries)
- The three docs are **expansions**, not **prerequisites**

**Why NOT `derived_from`?**
- These aren't transformations of the parent doc
- They're elaborations/expansions of concepts introduced in the parent

**Strength: 1.0** (essential components that define the framework)

---

#### 4. Cross-References Between Explanation Documents

**RUNTIME_EMERGENCE.md ↔ THE_REGRESSION_PARADOX.md ↔ SEMANTIC_DECOHERENCE.md**

```yaml
# In RUNTIME_EMERGENCE.md
relationships:
  - predicate: part_of
    target_file: THE_HARD_PROBLEM.md
    strength: 1.0

  - predicate: related_to
    target_file: THE_REGRESSION_PARADOX.md
    strength: 0.7

  - predicate: related_to
    target_file: SEMANTIC_DECOHERENCE.md
    strength: 0.7
```

**Predicate Choice: `related_to`**

**Why `related_to`?**
- ✅ They're **peer concepts** within the same framework
- ✅ Not hierarchical (not `part_of` each other)
- ✅ Not prerequisites (not `depends_on` - can read in any order)
- ✅ Strong thematic overlap (all explain aspects of the same problem)

**Why NOT `part_of`?**
- They're siblings, not parent/child
- None is a component of the others

**Why NOT `depends_on`?**
- You can understand each independently
- No required reading order

**Strength: 0.7** (strong connection but not essential for understanding)

---

#### 5. Explanation Documents → Foundation Concepts

**Each explanation references SEMANTIC_OPERATIONS concepts**

```yaml
# In RUNTIME_EMERGENCE.md
relationships:
  - predicate: depends_on
    target_file: ../SEMANTIC_OPERATIONS/SEMANTIC_COHERENCE.md
    strength: 0.8

  - predicate: depends_on
    target_file: ../SEMANTIC_OPERATIONS/SEMANTIC_OPTIMIZATION.md
    strength: 0.7
```

**Predicate Choice: `depends_on`**

**Why `depends_on`?**
- ✅ Understanding Runtime Emergence **requires** understanding Semantic Coherence
- ✅ Direction: Advanced concept → Prerequisite concept
- ✅ These are foundational concepts needed for full comprehension

**Why NOT `related_to`?**
- Too weak - there's a clear dependency relationship
- You **must** understand semantic coherence to fully grasp runtime emergence

**Why NOT `cites`?**
- `cites` is for attribution/references
- `depends_on` captures the logical dependency

**Strength: 0.8-0.9** (important foundation, strongly recommended prerequisite)

---

### Complete Frontmatter Examples

#### THE_HARD_PROBLEM.md

```yaml
---
entity_id: hard-problem-ai-transformation
title: The Hard Problem of AI Transformation
description: >
  Why good AI implementation doesn't create transformation,
  using the Hard Problem of Consciousness as analogy
category: ai-transformation
content_type: framework
provenance: 1p
visibility: public
approval_status: approved
tags:
  - ai-transformation
  - consciousness
  - dikw
  - semantic-operations

relationships:
  # Conceptual analogy to philosophy
  - predicate: related_to
    target_id: hard-problem-consciousness-chalmers-3p
    strength: 0.9

  # Documents the industry problem
  - predicate: documents
    target_id: ai-transformation-problem-industry-3p
    strength: 1.0

  # Optional: Direct citations to specific sources
  - predicate: cites
    target_id: chalmers-1995-facing-up-problem-3p
    strength: 0.8

  - predicate: cites
    target_id: nagel-1974-what-is-it-like-to-be-a-bat-3p
    strength: 0.7
---
```

---

#### RUNTIME_EMERGENCE.md

```yaml
---
entity_id: runtime-emergence-transformation
title: Runtime Emergence - The Transformation Paradox
description: >
  Why organizational transformation only exists during active operation
  because meaning is negotiated dynamically, not stored statically
category: ai-transformation
content_type: article
provenance: 1p
visibility: public
approval_status: approved
tags:
  - ai-transformation
  - runtime
  - emergence
  - semantic-coherence

relationships:
  # Part of parent framework
  - predicate: part_of
    target_file: THE_HARD_PROBLEM.md
    strength: 1.0

  # Related to peer concepts
  - predicate: related_to
    target_file: THE_REGRESSION_PARADOX.md
    strength: 0.7

  - predicate: related_to
    target_file: SEMANTIC_DECOHERENCE.md
    strength: 0.7

  # Depends on foundational concepts
  - predicate: depends_on
    target_file: ../SEMANTIC_OPERATIONS/SEMANTIC_COHERENCE.md
    strength: 0.8

  - predicate: depends_on
    target_file: ../SEMANTIC_OPERATIONS/SEMANTIC_OPTIMIZATION.md
    strength: 0.7
---
```

---

#### THE_REGRESSION_PARADOX.md

```yaml
---
entity_id: regression-paradox-ai
title: The Regression Paradox - Why Better AI Means More Average
description: >
  Why the LLM tendency to regress to the mean prevents
  transformation at scale
category: ai-transformation
content_type: article
provenance: 1p
visibility: public
approval_status: approved
tags:
  - ai-transformation
  - llm-limitations
  - regression-to-mean
  - wisdomator

relationships:
  # Part of parent framework
  - predicate: part_of
    target_file: THE_HARD_PROBLEM.md
    strength: 1.0

  # Related to peer concepts
  - predicate: related_to
    target_file: RUNTIME_EMERGENCE.md
    strength: 0.7

  - predicate: related_to
    target_file: SEMANTIC_DECOHERENCE.md
    strength: 0.7

  # Documents the Wisdomator example
  - predicate: documents
    target_file: ../examples-analogies/wisdomator-example-regress-paradoxs.md
    strength: 0.9

  # Depends on DIKW framework
  - predicate: depends_on
    target_file: ../FIRST_PRINCIPLES/DIK(U)W.md
    strength: 0.7
---
```

---

#### SEMANTIC_DECOHERENCE.md

```yaml
---
entity_id: semantic-decoherence-measurement
title: Semantic Decoherence - Why Transformation Can't Be Measured in Advance
description: >
  The natural decay of shared meaning and the measurement problem
  in organizational transformation
category: ai-transformation
content_type: article
provenance: 1p
visibility: public
approval_status: approved
tags:
  - ai-transformation
  - semantic-coherence
  - entropy
  - measurement-problem

relationships:
  # Part of parent framework
  - predicate: part_of
    target_file: THE_HARD_PROBLEM.md
    strength: 1.0

  # Related to peer concepts
  - predicate: related_to
    target_file: RUNTIME_EMERGENCE.md
    strength: 0.7

  - predicate: related_to
    target_file: THE_REGRESSION_PARADOX.md
    strength: 0.7

  # Depends on foundational concepts (critical prerequisites)
  - predicate: depends_on
    target_file: ../SEMANTIC_OPERATIONS/SEMANTIC_COHERENCE.md
    strength: 0.9

  - predicate: depends_on
    target_file: ../SEMANTIC_OPERATIONS/SEMANTIC_OPTIMIZATION.md
    strength: 0.8

  - predicate: depends_on
    target_file: ../FIRST_PRINCIPLES/DIK(U)W.md
    strength: 0.7
---
```

---

## Decision Tree: Choosing the Right Predicate

### Question 1: Is one entity a transformation of the other?

**YES** → Use `derived_from`
- Transcript from video
- Summary of paper
- Translation of article
- Quote extracted from book
- Code example from documentation

**NO** → Continue to Question 2

---

### Question 2: Does one entity explain/document the other?

**YES** → Use `documents`
- Guide explains framework
- Documentation describes system
- Tutorial teaches concept
- Reference manual covers API
- Analysis examines problem

**NO** → Continue to Question 3

---

### Question 3: Is one entity a component of the other?

**YES** → Use `part_of`
- Chapter in book
- Section in framework
- Module in course
- File in collection
- Component in system

**NO** → Continue to Question 4

---

### Question 4: Must you understand one before the other?

**YES** → Use `depends_on`
- Advanced guide needs basic tutorial first
- Application requires understanding principles
- Synthesis depends on foundation
- Complex topic needs prerequisite knowledge

**NO** → Continue to Question 5

---

### Question 5: Is one a direct reference/attribution of the other?

**YES** → Use `cites`
- Research paper you reference
- Book that influenced your thinking
- External source you're attributing
- Academic citation
- Source material for claims

**NO** → Continue to Question 6

---

### Question 6: Are they conceptually related?

**YES** → Use `related_to`
- Overlapping topics
- Cross-references between frameworks
- "See also" relationships
- Complementary concepts
- Parallel approaches to same problem

**NO** → Consider if relationship exists or if you need a new predicate

---

## Common Patterns Summary

### Pattern: Framework with Sub-Concepts

```
Framework (1P)
├── part_of ← Sub-Concept 1 (1P)
├── part_of ← Sub-Concept 2 (1P)
└── part_of ← Sub-Concept 3 (1P)

Sub-Concept 1 ↔ related_to ↔ Sub-Concept 2
Sub-Concept 2 ↔ related_to ↔ Sub-Concept 3
```

**Example:** THE_HARD_PROBLEM.md with three explanation documents

---

### Pattern: 1P Analysis of 3P Concept

```
Your Framework (1P)
├── documents → Industry Problem (3P)
├── related_to → Academic Concept (3P)
└── cites → Specific Paper (3P)
```

**Example:** Your framework documenting AI Transformation, relating to Hard Problem of Consciousness

---

### Pattern: Derivative Works

```
3P Video (3P)
└── derived_from ← Your Transcript (1P)
    └── derived_from ← Your Summary (1P)
        └── derived_from ← Your Analysis (1P)
```

**Example:** Multi-hop derivation chain from external source

---

### Pattern: Learning Path

```
Foundation (1P)
└── depends_on ← Intermediate (1P)
    └── depends_on ← Advanced (1P)
```

**Example:** DIKW basics → Semantic Operations → Advanced Applications

---

### Pattern: Citation Network

```
Your Blog Post (1P)
├── cites → Research Paper A (3P)
├── cites → Research Paper B (3P)
└── cites → Industry Report (3P)
```

**Example:** Blog post with multiple academic citations

---

## Strength Guidelines Cheatsheet

| Strength    | Meaning           | Use Cases                                                                     |
| ----------- | ----------------- | ----------------------------------------------------------------------------- |
| **1.0**     | Essential, direct | Direct derivation, primary source, comprehensive docs, essential component    |
| **0.8-0.9** | Core relationship | Important citation, strong dependency, major component, critical prerequisite |
| **0.5-0.7** | Moderate          | Related concepts, supporting citations, helpful background, peer concepts     |
| **0.3-0.4** | Weak              | Tangential reference, loose connection, optional context, minor mention       |

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Using `cites` for conceptual relationships

```yaml
# WRONG
- predicate: cites
  target_id: hard-problem-consciousness
  strength: 0.9
```

**Problem:** You're not citing a specific paper, you're using it as an analogy

**Fix:** Use `related_to` for conceptual overlap

---

### ❌ Mistake 2: Using `derived_from` for expansions

```yaml
# WRONG
- predicate: derived_from
  target_file: THE_HARD_PROBLEM.md
  strength: 1.0
```

**Problem:** RUNTIME_EMERGENCE isn't a transformation of THE_HARD_PROBLEM, it's a component

**Fix:** Use `part_of` for compositional hierarchy

---

### ❌ Mistake 3: Using `part_of` for prerequisites

```yaml
# WRONG
- predicate: part_of
  target_file: SEMANTIC_COHERENCE.md
  strength: 0.8
```

**Problem:** SEMANTIC_COHERENCE isn't a component of RUNTIME_EMERGENCE, it's a prerequisite

**Fix:** Use `depends_on` for logical dependencies

---

### ❌ Mistake 4: Confusing direction

```yaml
# WRONG (backwards)
- predicate: part_of
  target_file: RUNTIME_EMERGENCE.md  # Child pointing to child
  strength: 1.0
```

**Problem:** Direction matters! Part points to Whole, not sibling

**Fix:** Part → Whole (explanation → framework)

---

## Quick Reference: Predicate Selection

| Relationship Type | Predicate      | Direction             | Example                   |
| ----------------- | -------------- | --------------------- | ------------------------- |
| Transformation    | `derived_from` | Derivative → Original | Transcript → Video        |
| Explanation       | `documents`    | Docs → Concept        | Guide → Framework         |
| Composition       | `part_of`      | Part → Whole          | Chapter → Book            |
| Prerequisite      | `depends_on`   | Advanced → Basic      | Advanced → Tutorial       |
| Attribution       | `cites`        | Citing → Cited        | Blog → Paper              |
| Conceptual        | `related_to`   | A ↔ B                 | Framework A ↔ Framework B |
| Versioning        | `version_of`   | New → Old             | v2 → v1                   |

---

## Additional Resources

- [edge-predicates.md](edge-predicates.md) - Comprehensive predicate documentation
- [reference-edge-relationships.md](reference-edge-relationships.md) - High-level relationship patterns
- [publication-patterns.md](publication-patterns.md) - Content workflow patterns
- [frontmatter_template.md](../frontmatter_template.md) - Frontmatter syntax guide
- `semops-docs/docs/Publicv1_Candidates/SEMOPS_FRAMEWORK/` - Architectural rationale

---

*This document demonstrates practical Edge predicate selection through real examples from the SemOps knowledge base.*

