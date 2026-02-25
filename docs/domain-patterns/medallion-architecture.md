# Medallion Architecture (Bronze/Silver/Gold)

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** `etl`

Databricks data lakehouse pattern for progressive data quality tiers (bronze=raw, silver=cleaned, gold=curated). Adopted in SemOps for the three-tier corpus quality model.

---

## Source

- **Standard/Origin:** Databricks
- **URL:** https://www.databricks.com/glossary/medallion-architecture

## Adoption Context

- **Original Capability:** `ingestion-pipeline` — progressive data quality enrichment
- **Problem Statement:** Raw data quality is unpredictable. Mixing raw and enriched data in a single tier creates quality ambiguity — consumers can't trust the data without knowing its processing stage.
- **Selection Criteria:** Databricks medallion architecture (bronze/silver/gold) — three-tier quality model where each tier represents a defined quality level. Foundation for `semantic-ingestion` where tiers map to semantic enrichment stages.
- **ADR:** —

## Related Patterns

- `etl`, `semantic-ingestion`

