# RAPTOR

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-13
> **Provenance:** 3P
> **Derives From:** —

## Overview

**[RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval)](https://arxiv.org/abs/2401.18059)** is an academic pattern for organizing document corpora into hierarchical summaries using recursive clustering and abstraction. Unlike flat RAG approaches, RAPTOR builds a tree of increasingly abstract summaries, enabling retrieval at multiple granularity levels.

SemOps adopts RAPTOR's core principle — **themes emerge from semantic proximity** — as the foundation for the Research Synthesis pattern, which generates meta-analysis reports from research corpora without requiring explicit questions.

## Adoption Context

- **Original Capability:** `research` — hierarchical summarization for large document corpora
- **Problem Statement:** Flat retrieval over large document corpora misses high-level concepts. Searching individual chunks loses the hierarchical structure of knowledge.
- **Selection Criteria:** RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval) — arXiv 2401.18059. Tree-structured document summarization enabling hierarchical retrieval at multiple abstraction levels.
- **ADR:** —

## Key Concepts

- **Recursive clustering** — embed documents, cluster by proximity, summarize clusters, repeat
- **Multi-level retrieval** — query the tree at leaf (specific) or root (thematic) level
- **Question-free synthesis** — discover what the corpus contains rather than answering predefined queries

## Related Documentation

- [research-synthesis-pattern.md](research-synthesis-pattern.md) — 1P implementation of RAPTOR principles
- [agentic-rag.md](agentic-rag.md) — Related RAG pattern

