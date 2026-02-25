# Data Lineage

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** `prov-o`

Tracking data origin, transformations, and downstream consumers through the data lifecycle. Adopted in SemOps for provenance tracking across the ingestion and transformation pipeline.

---

## Source

- **Standard/Origin:** Industry standard
- **URL:** N/A

## Adoption Context

- **Original Capability:** `synthesis-simulation` — traceability for data transformations
- **Problem Statement:** Data transformations need traceability — which source fed which transformation, what quality checks were applied, where data quality degraded. Without lineage, debugging is guesswork.
- **Selection Criteria:** Industry-standard data lineage tracking, built on PROV-O foundation. Extended for ETL context where transformations are the primary unit of tracking.
- **ADR:** —

## Related Patterns

- `open-lineage`, `prov-o`, `agentic-lineage`

