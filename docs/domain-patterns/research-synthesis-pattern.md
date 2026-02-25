# Research Synthesis Pattern

> RAPTOR-inspired automated meta-analysis for generating thematic reports from ephemeral research corpora without explicit questions.

**Version:** 1.1.0
**Status:** active
**Last Updated:** 2025-12-31
**Provenance:** 1P
**Derives From:** RAPTOR

## Overview

The Research Synthesis Pattern defines how to automatically generate meta-analysis reports from a corpus of research documents by:

1. **Clustering embeddings** to discover emergent themes
2. **Summarizing clusters** to extract key findings per theme
3. **Synthesizing across themes** to produce structured reports

This pattern is the **ephemeral research** counterpart to the Content Classify Pattern. While Content Classify operates on the permanent 1P knowledge base with human review, Research Synthesis operates on temporary 3P research corpora with full automation.

### Core Principle

**Themes emerge from semantic proximity.** Rather than imposing a predefined taxonomy, let the embedding space reveal what the corpus is actually about. Then use LLMs to articulate those emergent structures.

---

## The Problem

### Question-Driven RAG Limitations

```
┌─────────────────────────────────────────────────────────────────┐
│  STANDARD RAG (Question-Driven)                                 │
│                                                                 │
│  User asks: "What causes AI transformation to fail?"            │
│                                                                 │
│  - Retrieves chunks relevant to the question                    │
│  - Synthesizes answer from those chunks                         │
│  - MISSES: themes the user didn't think to ask about            │
│  - MISSES: cross-cutting patterns across sources                │
│  - MISSES: contradictions between sources                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│  RESEARCH SYNTHESIS (Theme-Driven)                              │
│                                                                 │
│  No question required - corpus speaks for itself                │
│                                                                 │
│  - Discovers what themes ARE in the corpus                      │
│  - Surfaces unexpected patterns                                 │
│  - Identifies agreements and contradictions                     │
│  - Produces structured meta-analysis                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Why Clustering Works

Research documents on a topic share semantic structure:
- **Problem statements** cluster together
- **Cause analyses** cluster together
- **Solution proposals** cluster together
- **Evidence/data** clusters together

By clustering embeddings, we discover this latent structure without needing to predefine categories.

---

## Relationship to Atoms → Hubs → Structures

This pattern is RAPTOR applied to ephemeral research:

| SemOps Model | RAPTOR Equivalent | Research Synthesis |
|-------------------|-------------------|-------------------|
| **Atoms** | Leaf chunks | Raw text chunks from sources |
| **Hubs** | Cluster summaries | Theme summaries (auto-generated) |
| **Structures** | Tree root | Meta-analysis report |

The key difference: Research Synthesis is **fully automated** for 3P content, while Content Classify requires **human review** for 1P content.

---

## Synthesis Pipeline

### Three-Phase Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 1: CLUSTERING                                            │
│                                                                 │
│  Input: All chunk embeddings from Qdrant collection             │
│                                                                 │
│  1. Export embeddings + metadata                                │
│  2. Apply clustering algorithm (HDBSCAN or k-means)             │
│  3. Identify cluster centroids                                  │
│  4. Select representative chunks per cluster                    │
│                                                                 │
│  Output: Clusters with representative chunks                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 2: THEME EXTRACTION                                      │
│                                                                 │
│  For each cluster:                                              │
│                                                                 │
│  1. Retrieve N chunks nearest to centroid                       │
│  2. LLM prompt: "Identify the theme and key findings"           │
│  3. Extract: theme_name, summary, key_points, sources           │
│                                                                 │
│  Output: Theme summaries (one per cluster)                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 3: META-SYNTHESIS                                        │
│                                                                 │
│  Input: All theme summaries                                     │
│                                                                 │
│  LLM prompt: "Synthesize a meta-analysis covering:              │
│    - Problem definition                                         │
│    - Root causes                                                │
│    - Proposed solutions                                         │
│    - Evidence quality                                           │
│    - Gaps and contradictions"                                   │
│                                                                 │
│  Output: Structured meta-analysis report                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Clustering

### Clustering Algorithm Selection

| Algorithm | When to Use | Pros | Cons |
|-----------|-------------|------|------|
| **HDBSCAN** | Unknown number of themes | Discovers natural clusters, handles noise | Requires tuning min_cluster_size |
| **K-Means** | Known approximate theme count | Fast, predictable | Must specify k upfront |
| **Agglomerative** | Hierarchical analysis | Produces dendrogram | Computationally expensive |

### Implementation Approach

```python
# Using scikit-learn for clustering
from sklearn.cluster import KMeans
import numpy as np

def cluster_embeddings(embeddings: list[list[float]], n_clusters: int = 8):
    """Cluster embeddings and return cluster assignments."""
    X = np.array(embeddings)

    # K-means clustering
    kmeans = KMeans(n_clusters=n_clusters, random_state=42)
    labels = kmeans.fit_predict(X)

    # Get centroids for representative selection
    centroids = kmeans.cluster_centers_

    return labels, centroids
```

### Representative Selection

For each cluster, select chunks that best represent the theme:

1. **Centroid-nearest**: Chunks closest to cluster centroid (most typical)
2. **Diverse sampling**: Mix of centroid-near and boundary chunks
3. **Source-balanced**: Ensure representation from multiple sources

```python
def get_representative_chunks(
    cluster_id: int,
    embeddings: np.ndarray,
    labels: np.ndarray,
    centroid: np.ndarray,
    n_representatives: int = 5
) -> list[int]:
    """Get indices of representative chunks for a cluster."""
    # Get indices of chunks in this cluster
    cluster_indices = np.where(labels == cluster_id)[0]
    cluster_embeddings = embeddings[cluster_indices]

    # Calculate distances to centroid
    distances = np.linalg.norm(cluster_embeddings - centroid, axis=1)

    # Return indices of n nearest to centroid
    nearest_indices = np.argsort(distances)[:n_representatives]
    return cluster_indices[nearest_indices].tolist()
```

---

## Phase 2: Theme Extraction

### Theme Extraction Prompt

```markdown
## Task: Extract Theme from Research Cluster

Analyze these related excerpts from research documents and identify the unifying theme.

### Instructions

1. Read all excerpts carefully
2. Identify the common thread or topic
3. Extract key findings and claims
4. Note any contradictions or nuances
5. Cite specific sources

### Output Format

```json
{
  "theme_name": "Short descriptive name (3-5 words)",
  "theme_type": "problem | cause | solution | evidence | methodology",
  "summary": "2-3 sentence summary of this theme",
  "key_points": [
    {
      "point": "Specific finding or claim",
      "source": "Source title or author",
      "confidence": "high | medium | low"
    }
  ],
  "contradictions": [
    {
      "claim_a": "One source says...",
      "claim_b": "Another source says...",
      "sources": ["Source A", "Source B"]
    }
  ],
  "evidence_quality": "strong | moderate | weak | anecdotal"
}
```

### Excerpts to Analyze

{cluster_excerpts}

### Source Metadata

{source_metadata}
```

### Theme Type Classification

Automatically classify themes by their role in the analysis:

| Theme Type | Indicators | Example |
|------------|------------|---------|
| **problem** | "challenge", "issue", "barrier", "gap" | "Measurement Challenges in AI ROI" |
| **cause** | "because", "due to", "results from" | "Data Silos as Integration Barrier" |
| **solution** | "approach", "strategy", "framework" | "Collaborative Intelligence Model" |
| **evidence** | statistics, case studies, surveys | "2025 Enterprise AI Adoption Data" |
| **methodology** | "how to", "process", "steps" | "AI Transformation Assessment Framework" |

---

## Phase 3: Meta-Synthesis

### Meta-Analysis Prompt

```markdown
## Task: Generate Meta-Analysis Report

Synthesize these theme summaries into a comprehensive meta-analysis report.

### Report Structure

Generate a structured report covering:

1. **Executive Summary** (2-3 paragraphs)
   - Core problem definition
   - Key findings
   - Main recommendations

2. **Problem Space**
   - What problems does the literature identify?
   - How are problems framed differently across sources?
   - What's the scope and scale?

3. **Cause Analysis**
   - What root causes are identified?
   - Which causes have strongest evidence?
   - Are there competing causal models?

4. **Solution Landscape**
   - What solutions are proposed?
   - What evidence supports each?
   - What gaps exist in proposed solutions?

5. **Cross-Cutting Themes**
   - Patterns that span multiple themes
   - Contradictions between sources
   - Emerging consensus vs. open debates

6. **Evidence Assessment**
   - Strength of evidence across themes
   - Methodological approaches used
   - Gaps in research

7. **Synthesis & Implications**
   - Integrated model of the problem space
   - Actionable recommendations
   - Areas for further research

### Theme Summaries

{theme_summaries_json}

### Source Context

Project: {project_name}
Sources: {source_count} documents
Chunks analyzed: {chunk_count}
Themes identified: {theme_count}
```

### Output Format Options

| Format | Use Case | Structure |
|--------|----------|-----------|
| **markdown** | Human reading, publishing | Full prose with headers |
| **json** | Programmatic use, further processing | Structured data |
| **outline** | Quick review, editing | Bullet points only |

---

## Configuration Parameters

### Clustering Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `n_clusters` | 8 | Number of themes to discover (k-means) |
| `min_cluster_size` | 5 | Minimum chunks per cluster (HDBSCAN) |
| `representatives_per_cluster` | 5 | Chunks to use for theme extraction |

### Synthesis Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `model` | `claude-sonnet-4-20250514` | LLM for synthesis |
| `max_tokens` | 4096 | Maximum output length |
| `output_format` | `markdown` | Report format |
| `include_sources` | true | Cite sources in report |

---

## CLI Interface

### Synthesize Command

```bash
# Basic synthesis (auto-detect clusters)
python -m data_systems_toolkit.research.cli synthesize

# Specify number of themes
python -m data_systems_toolkit.research.cli synthesize --n-clusters 10

# Output to file
python -m data_systems_toolkit.research.cli synthesize --output report.md

# JSON output for programmatic use
python -m data_systems_toolkit.research.cli synthesize --format json

# Verbose mode (show theme extraction)
python -m data_systems_toolkit.research.cli synthesize --verbose
```

### Expected Output

```
Research Synthesis
==================
Collection: research-ai-transformation
Chunks: 1,128
Sources: 5

Phase 1: Clustering
-------------------
Clustering 1,128 chunks into 8 themes...
  Theme 1: 156 chunks (13.8%)
  Theme 2: 203 chunks (18.0%)
  ...

Phase 2: Theme Extraction
-------------------------
Extracting themes from clusters...
  [1/8] "Measurement Challenges" (problem)
  [2/8] "Leadership Vacuum" (cause)
  [3/8] "Data Silo Barriers" (cause)
  ...

Phase 3: Meta-Synthesis
-----------------------
Generating meta-analysis report...

================================================================================

# Meta-Analysis: AI Transformation Research

## Executive Summary

[Generated report content...]
```

---

## Quality Considerations

### Cluster Quality Signals

| Signal | Good | Warning |
|--------|------|---------|
| **Cluster size variance** | Similar sizes | One huge cluster + many tiny |
| **Theme coherence** | Clear, distinct themes | Overlapping or vague themes |
| **Source diversity** | Multiple sources per theme | Single-source themes |
| **Coverage** | All chunks assigned | Many outliers/noise |

### When to Adjust

- **Too few clusters**: Themes too broad, miss nuance
- **Too many clusters**: Themes too narrow, lose coherence
- **Uneven clusters**: Consider hierarchical clustering
- **Poor themes**: Increase representatives_per_cluster

---

## Relationship to Other Patterns

### Content Classify Pattern
- **Scope**: Permanent 1P knowledge base
- **Automation**: Human-in-loop for 1P concepts
- **Output**: Atoms, Hubs, Structures with edges

### Research Synthesis Pattern
- **Scope**: Ephemeral 3P research corpora
- **Automation**: Fully automated
- **Output**: Meta-analysis report

### When to Use Which

| Scenario | Pattern |
|----------|---------|
| Refactoring messy internal docs | Content Classify |
| Analyzing external research | Research Synthesis |
| Building permanent knowledge base | Content Classify |
| Generating temporary research report | Research Synthesis |
| 1P concept development | Content Classify |
| 3P literature review | Research Synthesis |

---

## Implementation Notes

### Dependencies

```toml
# pyproject.toml [research] extras
scikit-learn>=1.3.0  # For clustering
numpy>=1.24.0        # For array operations
```

### Qdrant Integration

Export embeddings from existing collection:

```python
from qdrant_client import QdrantClient

def export_embeddings(collection_name: str):
    """Export all embeddings and metadata from Qdrant."""
    client = QdrantClient(url="http://localhost:6333")

    # Scroll through all points
    points = []
    offset = None
    while True:
        result = client.scroll(
            collection_name=collection_name,
            limit=100,
            offset=offset,
            with_payload=True,
            with_vectors=True,
        )
        points.extend(result[0])
        offset = result[1]
        if offset is None:
            break

    return points
```

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Theme coherence | Human judges themes as distinct and meaningful |
| Coverage | >90% of chunks assigned to themes |
| Source balance | No theme dominated by single source |
| Report usefulness | Actionable insights without manual queries |
| Processing time | <5 minutes for 1000 chunks |

---

## References

- **RAPTOR**: Recursive Abstractive Processing for Tree-Organized Retrieval
- **BERTopic**: Topic modeling with BERT embeddings
- **GATOS**: LLM-supported thematic analysis workflow
- **SemOps Content Classify Pattern**: [content-classify-pattern.md](retired/content-classify-pattern.md)

---

**Document Status:** Draft | **Next Review:** 2025-12-19
**Maintainer:** SemOps Research Team

