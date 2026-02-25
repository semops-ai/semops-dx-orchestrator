# Digital Asset Management

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-13
> **Provenance:** 3P
> **Derives From:** —

## Overview

**Digital Asset Management (DAM)** is an industry pattern for organizing, storing, retrieving, and distributing digital assets (images, video, documents, data files) with rich metadata. SemOps adopts DAM as the foundational pattern for its content layer — every piece of content is a managed asset with lifecycle state, ownership, and metadata.

DAM provides:

- **Asset catalog** — centralized registry of all content items
- **Metadata-driven discovery** — find assets by attributes, not just filenames
- **Lifecycle management** — creation, approval, publication, archival
- **Multi-format support** — one logical asset, multiple renditions/formats

## Adoption Context

- **Original Capability:** `publishing-pipeline` — digital asset management for content production
- **Problem Statement:** Digital assets (images, fonts, templates, media) need systematic management — versioning, permissions, metadata, delivery to multiple surfaces (web, social, print).
- **Selection Criteria:** Industry-standard Digital Asset Management pattern. Canonical approach to managing content production lifecycle, adopted for the semops-publisher content workflow.
- **ADR:** —

## Key Concepts

SemOps extends standard DAM with:

- **Provenance tracking** (1P/2P/3P ownership) — not typical in commercial DAM
- **Semantic edges** — assets connected via knowledge graph, not just folders
- **Dual-status model** — separates approval state from delivery state

## Related Documentation

- [dam-foundations.md](dam-foundations.md) — Multi-domain convergence context
- [dublin-core-pattern.md](dublin-core-pattern.md) — Metadata standard adopted for assets
- [pim-pattern.md](pim-pattern.md) — Multi-channel distribution
- [content-lifecycle-states.md](content-lifecycle-states.md) — Lifecycle state machine

