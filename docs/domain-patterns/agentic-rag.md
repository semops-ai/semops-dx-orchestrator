# Agentic RAG

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** `raptor`

Retrieval-Augmented Generation enhanced with autonomous agent orchestration for multi-step reasoning, tool use, and dynamic retrieval strategies. Adopted in SemOps for the query API and corpus retrieval system.

---

## Source

- **Standard/Origin:** Industry emerging pattern
- **URL:** N/A

## Adoption Context

- **Original Capability:** `internal-knowledge-access` — agentic retrieval over the knowledge corpus
- **Problem Statement:** Standard RAG retrieves once and generates. Agents need to iterate — refine queries based on results, choose retrieval strategies dynamically, and reason over partial results before deciding the next query.
- **Selection Criteria:** Agentic loop over retrieval: the agent decides the next query based on current results, rather than a single retrieve-and-generate pass. Adopted from emerging community practice (IBM, NVIDIA implementations).
- **ADR:** —

## Related Patterns

- `raptor`, `semantic-ingestion`, `research-synthesis`

