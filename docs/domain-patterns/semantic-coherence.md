# Semantic Coherence

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 1P
> **Derives From:** `semantic-optimization`, `ddd`, `skos`

Semantic Coherence measures how well the system's domain model, capabilities, and content share consistent meaning. It is the primary quality signal in SemOps.

---

## Overview

The central innovation of SemOps. Coherence is measurable via embedding similarity (Qdrant), graph connectivity (Neo4j), and pattern coverage (capability→pattern tracing). Unlike traditional metrics that measure operational health, semantic coherence measures whether the system still means what you think it means.

Every capability must trace to at least one pattern (coherence signal). When patterns drift — new capabilities appear without pattern coverage, or patterns exist without capability consumers — the system flags semantic drift. This is the measurable form of "does the architecture match the intent?"

Previously documented in `classifier-infrastructure.md` (now deprecated). The tiered classifier pipeline described there is the implementation mechanism for coherence measurement.

## Adoption Context

Why semantic coherence was created as the primary quality signal for SemOps.

- **Original Capability:** `coherence-measurement` — quality measurement for ingested content
- **Problem Statement:** Traditional observability (error rates, latency) doesn't measure semantic health. Capabilities may lack patterns, patterns may lack implementations, theory-implementation may misalign. Need a measurable signal for "does the architecture still mean what we think it means?"
- **Selection Criteria:** Coherence uses embedding similarity (Qdrant), graph connectivity (Neo4j), and pattern coverage (capability→pattern tracing). This is the 1P innovation: drift between theory and implementation is measurable, not hidden.
- **ADR:** [ADR-0005: Ingestion Strategy and Corpus Architecture](../decisions/ADR-0005-ingestion-strategy-corpus-architecture.md)

## Core Concepts

- **Embedding coherence:** Average cosine similarity between a concept and its SKOS-related concepts
- **Graph coherence:** Connectivity, centrality, and community structure in the knowledge graph
- **Pattern coverage:** Every capability in STRATEGIC_DDD.md traces to ≥1 registered pattern
- **Drift detection:** Changes that reduce coverage or similarity trigger alerts

## Related Documentation

- [semantic-ingestion.md](semantic-ingestion.md) - How content enters the coherence measurement system
- [agentic-lineage.md](agentic-lineage.md) - Provenance for coherence decisions
- [semantic-object-pattern.md](semantic-object-pattern.md) - Structure of coherent objects
- [classifier-infrastructure.md](retired/classifier-infrastructure.md) - (Deprecated) Original pipeline docs
- [ADR-0005](../decisions/ADR-0005-ingestion-strategy-corpus-architecture.md) - Ingestion strategy

