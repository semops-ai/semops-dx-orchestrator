# OpenLineage

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** `prov-o`

Open standard for data lineage collection and interoperability, providing a common event model for tracking dataset lifecycle across tools. Adopted in SemOps for standardized lineage tracking across the ingestion pipeline.

---

## Source

- **Standard/Origin:** OpenLineage Project (LF AI & Data)
- **URL:** https://openlineage.io/

## Adoption Context

- **Original Capability:** `agentic-lineage` — standardized lineage event protocol
- **Problem Statement:** Data lineage tracking across heterogeneous tools needs a common event format. Proprietary lineage implementations create silos.
- **Selection Criteria:** OpenLineage standard (Linux Foundation AI & Data) — interoperable lineage events across tools and platforms. Extended with agent context for `agentic-lineage` to track AI decision provenance.
- **ADR:** —

## Related Patterns

- `data-lineage`, `episode-provenance`, `agentic-lineage`

