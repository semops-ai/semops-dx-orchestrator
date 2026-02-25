# Extract Transform Load (ETL)

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** —

Industry-standard data pipeline pattern for extracting data from sources, transforming it into a target format, and loading it into a destination system. Adopted in SemOps for the ingestion pipeline that processes documents into the semantic corpus.

---

## Source

- **Standard/Origin:** Industry standard
- **URL:** N/A

## Adoption Context

- **Original Capability:** `ingestion-pipeline` — foundational data movement abstraction
- **Problem Statement:** Data must move between systems — sources to transformations to destinations. Without a standard abstraction, each integration is bespoke.
- **Selection Criteria:** Industry-standard Extract-Transform-Load pattern. Foundation for `semantic-ingestion` and `medallion-architecture` — the base pattern that SemOps extends with semantic quality gates.
- **ADR:** —

## Related Patterns

- `medallion-architecture`, `semantic-ingestion`

