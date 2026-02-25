# Provenance-First Design

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-13
> **Provenance:** 1P
> **Derives From:** `prov-o`

## Overview

**Provenance-First Design** is a 1P pattern that makes content ownership (1P/2P/3P) the primary discriminator for all downstream behavior. Every entity in SemOps carries provenance metadata that determines its lifecycle, editorial authority, and coherence measurement.

The core insight: **ownership changes everything.** A 1P concept (original intellectual property) has different lifecycle rules, editorial authority, and coherence requirements than a 3P concept (adopted standard). By making this distinction explicit and primary, the system can apply appropriate governance automatically.

## Adoption Context

- **Original Capability:** `pattern-management` — ownership as primary domain discriminator
- **Problem Statement:** Ownership (1P/2P/3P) is typically an afterthought in knowledge systems. In SemOps, provenance determines lifecycle, editorial authority, and coherence rules — it should be the primary discriminator, not metadata.
- **Selection Criteria:** W3C PROV-O foundation extended to make ownership the first field checked. Every entity carries provenance metadata that determines all downstream behavior: 1P concepts get human review gates, 3P concepts get auto-matching.
- **ADR:** —

## Key Concepts

- **1P (First Party)** — original intellectual property, living concepts that evolve
- **2P (Second Party)** — collaborative/commissioned work
- **3P (Third Party)** — adopted external standards, fixed references
- **Provenance determines:** lifecycle states, editorial authority, coherence scoring, drift detection

## Related Documentation

- [provenance-and-lineage.md](provenance-and-lineage.md) — How provenance emerged from practice
- [edge-predicates.md](edge-predicates.md) — Provenance in PROV-O edge relationships
- [content-classify-pattern.md](retired/content-classify-pattern.md) — Ownership detection during classification

