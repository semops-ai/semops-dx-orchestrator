# Shape

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-13
> **Provenance:** 3P
> **Derives From:** —

## Overview

**Shape** (from W3C [SHACL](https://www.w3.org/TR/shacl/) and JSON Schema) is the pattern of defining concrete data structures that validate and constrain entity metadata. In SemOps, shapes define what fields an entity can have and what values are valid.

SemOps applies shapes via JSONB metadata columns with JSON Schema validation, following SHACL's Closed World Assumption: validate the fields that are defined, allow extension through metadata.

## Adoption Context

- **Original Capability:** `domain-data-model` — formal constraints for entity structures
- **Problem Statement:** Data structures need explicit definition rather than inference. Schema assumptions are unreliable across repos, and entities lack formal shape validation.
- **Selection Criteria:** W3C SHACL + JSON Schema standards applied to domain model entities. Every entity type has explicit shape constraints that validate structure at ingestion and audit time.
- **ADR:** —

## Key Concepts

- **Closed World Assumption** — validate defined fields, reject unknown ones in controlled schemas
- **JSONB metadata** — flexible extension point for entity-specific data
- **Schema evolution** — shapes version independently of the core relational schema
- **Meta-circularity** — the pattern registry itself has a shape (`pattern_v1.yaml`)

## Related Documentation

- [reference-entity-constraints.md](reference-entity-constraints.md) — Validation rules and attribute constraints

