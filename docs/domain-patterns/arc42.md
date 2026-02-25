# Arc42

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-10
> **Provenance:** 3P
> **Derives From:** Starke & Hruschka (2005)

Structured architecture documentation framework with 12 standard sections from introduction through glossary.

---

## Overview

**[Arc42](https://arc42.org/)** (Starke & Hruschka) is a template for architecture documentation that provides a standardized structure: Introduction & Goals, Constraints, Context & Scope, Solution Strategy, Building Block View, Runtime View, Deployment View, Crosscutting Concepts, Architecture Decisions, Quality Requirements, Risks & Technical Debt, and Glossary.

SemOps adopts Arc42 as the framework for the top-level system landscape document, adapted with an **anti-staleness principle**: each section provides connective tissue and context, then references the source of truth rather than duplicating details.


## Adoption Context

- **Original Capability:** `pattern-management` — structured architecture documentation template
- **Problem Statement:** Software architecture documentation varies widely across teams and repos. No standard template for communicating architecture decisions, constraints, and quality requirements.
- **Selection Criteria:** Arc42 template (Starke & Hruschka) — standard 12-section architecture documentation. Adopted for SYSTEM_LANDSCAPE.md and as a reference for per-repo ARCHITECTURE.md structure.
- **ADR:** —

## Adaptation

The standard 12-section Arc42 template is adapted to 9 sections for SemOps:

| Arc42 Section | SemOps Adaptation |
| ------------- | ----------------- |
| 1. Introduction & Goals | Section 1 — merged with Constraints (Arc42 section 2) |
| 2. Architecture Constraints | Merged into Section 1 as subsection |
| 3. Context & Scope | Section 2 — business context + three-ring technical boundary |
| 4. Solution Strategy | Section 3 — 8 decisions linked to ADRs |
| 5. Building Block View | Section 4 — Layer 1/2 repos + subdomain participation |
| 6. Runtime View | Section 5 — agent workflow, publishing pipeline, knowledge ingestion |
| 7. Deployment View | Section 6 — infrastructure topology with references |
| 8. Crosscutting Concepts | Section 7 — domain model, 1P/3P, semantic coherence |
| 9. Architecture Decisions | Omitted — covered by ADR_INDEX.md (linked from sections 3 and 9) |
| 10. Quality Requirements | Omitted — covered in Section 1 Quality Goals |
| 11. Risks & Technical Debt | Section 8 — 6 risks with severity and mitigations |
| 12. Glossary | Omitted — covered by `semops-core/schemas/UBIQUITOUS_LANGUAGE.md` |

**Custom addition:** Section 9 (Document Index) — routing guide to all architecture docs.

---

## Anti-Staleness Principle

The key adaptation of Arc42 for SemOps: sections should **not** contain detailed content that exists elsewhere. Instead:

- Provide context for **why** the section matters
- State the **key facts** concisely
- Reference the **source of truth** for details

This prevents the common failure mode of architecture docs: they start comprehensive, then drift from reality because nobody updates them. By keeping SYSTEM_LANDSCAPE.md as connective tissue, staleness is limited to broken references (detectable) rather than stale prose (undetectable).

---

## Integration Points

- **GLOBAL_ARCHITECTURE.md** — detailed repo descriptions referenced from Building Block View
- **GLOBAL_INFRASTRUCTURE.md** — service details referenced from Deployment View
- **DIAGRAMS.md** — visual representations referenced from multiple sections
- **REPOS.yaml** — structured data backing the Building Block View tables
- **ADR_INDEX.md** — decision log referenced from Solution Strategy
- **UBIQUITOUS_LANGUAGE.md** — domain terms replacing Arc42 Glossary section

---

## References

- [Arc42 official site](https://arc42.org/)
- [Arc42 template (English)](https://arc42.org/download)
- [SYSTEM_LANDSCAPE.md](../SYSTEM_LANDSCAPE.md) — our implementation
- [semops-dx-orchestrator#118](https://github.com/semops-ai/semops-dx-orchestrator/issues/118) — implementation issue

