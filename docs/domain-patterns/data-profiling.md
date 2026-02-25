# Data Profiling

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** —

Statistical analysis of data quality, completeness, and distribution. Adopted in SemOps for assessing corpus quality and identifying data gaps.

---

## Source

- **Standard/Origin:** Industry standard
- **URL:** N/A

## Adoption Context

- **Original Capability:** `synthesis-simulation` — statistical validation of schema assumptions
- **Problem Statement:** Data quality is unknown until profiled — distributions, missing values, outliers, cardinality. Quality gates need statistical evidence, not assumptions.
- **Selection Criteria:** Standard data profiling techniques (descriptive statistics, histogram analysis, cardinality checks). Foundation for quality gates in the ingestion pipeline and data complexity assessment in scale-projection.
- **ADR:** —

## Related Patterns

- `data-lineage`, `scale-projection`

