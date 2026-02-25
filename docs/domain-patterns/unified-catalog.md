# Unified Catalog

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-13
> **Provenance:** 1P
> **Derives From:** `dam`, `skos`

## Overview

**Unified Catalog** is a 1P pattern that treats owned content (1P) and referenced content (3P) as first-class entities in the same catalog, distinguished by provenance rather than stored in separate systems.

Traditional DAM systems manage only owned assets. Knowledge management systems often track only external references. The Unified Catalog merges both into a single entity registry where provenance metadata (1P/2P/3P) determines lifecycle rules while the catalog structure remains uniform.

## Adoption Context

- **Original Capability:** `domain-data-model` — unified view across owned and referenced content
- **Problem Statement:** Content comes from multiple sources — internal docs (1P), external research (3P), collaborative work (2P). A single catalog needs to track both owned and referenced content with distinct lifecycle rules.
- **Selection Criteria:** DAM asset management + SKOS linked data vocabulary. Unified view where 1P (owned) and 3P (referenced) are distinct node types connected by edges, with provenance as the discriminator.
- **ADR:** —

## Key Concepts

- **Single entity table** — no separate tables for owned vs referenced content
- **Provenance as discriminator** — 1P/2P/3P field determines behavior, not storage location
- **Shared edge graph** — 3P references can have the same relationship types as 1P content
- **Discovery parity** — external standards are as discoverable as internal concepts

## Related Documentation

- [dam-foundations.md](dam-foundations.md) — Multi-domain convergence context
- [dam.md](dam.md) — Base DAM pattern
- [provenance-first-design.md](provenance-first-design.md) — Provenance as primary discriminator
- [skos-pattern.md](skos-pattern.md) — Classification vocabulary shared across owned/referenced

