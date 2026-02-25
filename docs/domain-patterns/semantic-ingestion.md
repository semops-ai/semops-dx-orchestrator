# Semantic Ingestion

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 1P
> **Derives From:** `etl`, `medallion-architecture`, `mlops`

Semantic Ingestion converts raw documents and data into structured, semantically-enriched corpus entries using a three-tier quality model.

---

## Overview

The ingestion pipeline applies the medallion architecture (bronze→silver→gold) with semantic quality gates at each tier:

- **Bronze (raw):** Document ingestion via Docling. Minimal processing — extract text, preserve structure, assign provenance.
- **Silver (enriched):** Entity extraction, deduplication, SKOS classification. Automated classifiers score concepts for coherence and quality.
- **Gold (curated):** Human-reviewed, promoted to stable corpus. Full lineage, SKOS relationships, and implementation tracing.

Each tier has automated classifiers (rule-based, embedding, graph, LLM) and human-in-the-loop approval gates for promotion decisions.

## Adoption Context

Why semantic ingestion was designed as a tiered pipeline rather than simple document vectorization.

- **Original Capability:** `ingestion-pipeline` — the mechanism that powers coherence measurement
- **Problem Statement:** Raw document ingestion (Docling + pgvector) loses semantic structure. Promoted concepts lack lineage, deduplication is manual, classification is inconsistent. Content enters the corpus as vectors but not as structured knowledge.
- **Selection Criteria:** Adopt medallion architecture (bronze→silver→gold) with semantic classifiers per tier. Medallion is proven in data engineering; applying it to semantic/conceptual content with quality gates makes promotion decisions transparent and traceable.
- **ADR:** [ADR-0005: Ingestion Strategy and Corpus Architecture](../decisions/ADR-0005-ingestion-strategy-corpus-architecture.md)

## Core Concepts

- **Phase A:** Document ingestion — Docling extracts text, Qdrant stores embeddings
- **Phase B:** Domain pattern ingestion — structured pattern docs enter the corpus
- **Phase C:** Query API — retrieval over the enriched corpus
- **Promotion:** Concepts move bronze→silver→gold via classifier scores + HITL

## Related Documentation

- [semantic-coherence.md](semantic-coherence.md) - Quality measurement for ingested content
- [medallion-architecture.md](medallion-architecture.md) - The three-tier quality model
- [ADR-0005](../decisions/ADR-0005-ingestion-strategy-corpus-architecture.md) - Ingestion strategy and corpus architecture

