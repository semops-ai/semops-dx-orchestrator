# Semantic Object Pattern

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 1P
> **Derives From:** `knowledge-organization-systems`, `pattern-language`

The Semantic Object Pattern defines how domain concepts are represented as structured, self-describing objects with SKOS relationships, provenance metadata, and implementation tracing.

---

## Overview

Every concept in SemOps follows a standardized structure that enables automated coherence checking and knowledge organization:

- **Identity:** Kebab-case ID, preferred label, definition
- **Classification:** SKOS relationships (broader/narrower/related), provenance (1P/2P/3P)
- **Implementation:** Which systems implement this concept, in what role
- **Documentation:** Primary doc path, related docs

This pattern is the schema behind `pattern_v1.yaml` and the `concept` table in semops-core. By standardizing how we represent domain knowledge, we can measure coherence, detect drift, and automate knowledge curation.

## Adoption Context

- **Original Capability:** `pattern-management` — standardized representation for domain concepts
- **Problem Statement:** Domain concepts need a canonical structure — SKOS relationships, provenance metadata, implementation tracing. Without standardization, each concept is described differently and coherence measurement is impossible.
- **Selection Criteria:** Pattern as aggregate root with standardized fields: (id, label, definition, SKOS edges, implementations). The `pattern_v1.yaml` schema is the operationalization of this pattern.
- **ADR:** —

## Core Concepts

- **Provenance classification:** Every concept is 1P (our innovation), 2P (our adaptation), or 3P (adopted standard)
- **derives_from:** SKOS-style lineage array showing which patterns a concept builds on
- **implementations:** Which systems implement, own, or consume the concept
- **Coherence signal:** Concepts with SKOS relationships enable automated similarity checking

## Related Documentation

- [knowledge-organization-systems.md](retired/knowledge-organization-systems.md) - KOS foundations
- [pattern-language.md](pattern-language.md) - Pattern documentation method
- [skos-pattern.md](skos-pattern.md) - SKOS vocabulary usage

