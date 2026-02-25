# Backstage Software Catalog

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-10
> **Provenance:** 3P
> **Derives From:** Spotify (2020)

Structured component metadata schema for describing software systems, their dependencies, and the APIs they expose and consume.

---

## Overview

**[Backstage Software Catalog](https://backstage.io/docs/features/software-catalog/)** (Spotify) provides a standardized schema (`catalog-info.yaml`) for describing software components in a multi-service architecture. Key concepts: components belong to systems, systems depend on other systems, and components expose and consume APIs.

SemOps adopts the **schema model** (not the Backstage portal itself) for `REPOS.yaml` — making the repo registry machine-queryable with dependency graphs, API contracts, and system grouping.


## Adoption Context

Why Backstage's schema model was adopted for the repository registry.

- **Original Capability:** Repository registry and dependency graph (`REPOS.yaml` as machine-queryable catalog)
- **Problem Statement:** `REPOS.yaml` started as unstructured documentation. Repository relationships, dependencies, and API contracts were implicit in prose. No validation: circular dependencies undetected, API contracts unstated, system grouping ambiguous.
- **Selection Criteria:** Adopt Backstage's **schema model** (not the portal): `spec.type`, `spec.dependsOn`, `spec.providesApis` map to `REPOS.yaml` fields. Backstage is proven for dependency graphs; schema-only adoption keeps data in git with no portal overhead; integrates with `/sync-architecture` validation.
- **ADR:** [ADR-0002: System Landscape](../decisions/ADR-0002-system-landscape.md)

## Schema Mapping

How Backstage catalog-info.yaml concepts map to REPOS.yaml:

| Backstage Concept | REPOS.yaml Field | Purpose |
| ----------------- | ----------------- | ------- |
| `spec.type` | `layer` | semops-core / domain-application / external |
| `spec.system` | `layer` + `context_type` | System grouping + DDD classification |
| `spec.dependsOn` | `depends_on` | Repo-level dependencies |
| `spec.providesApis` | `provides` | APIs and services exposed |
| `spec.consumesApis` | `consumes` | APIs and services consumed |
| `metadata.tags` | `subdomains` | Subdomain participation |
| `spec.lifecycle` | `lifecycle_stage` | planned / draft / in_progress / active / retired |

**Not adopted** (different context):
- `metadata.annotations` — Backstage plugin configuration, not relevant
- `spec.owner` — single operator system, ownership is implicit
- `apiVersion` / `kind` — Backstage-specific Kubernetes-style metadata

---

## Additional Fields

Fields in REPOS.yaml that extend beyond Backstage:

| Field | Origin | Purpose |
| ----- | ------ | ------- |
| `context_type` | DDD | core / supporting / generic subdomain classification |
| `integration_pattern` | DDD Context Maps | shared-kernel, conformist, customer-supplier, separate-ways, published-language |
| `capabilities` | Strategic DDD | Named DDD modules within each repo |

---

## Design Decisions

**Schema only, not the portal.** Backstage is a developer portal with a React frontend, plugin system, and PostgreSQL backend. We adopt only the data model for describing component relationships — consistent with the Explicit Enterprise principle of open primitives over platform abstraction.

**YAML over database.** The catalog data lives in version-controlled YAML rather than a database. This enables git-based governance (every change is a commit with author and diff) and keeps the data accessible to AI agents without API calls.

**Validation via `/sync-architecture`.** Instead of Backstage's catalog processor, the `/sync-architecture` command validates REPOS.yaml fields for consistency: no circular dependencies, consumed services must be provided somewhere, subdomains must match SYSTEM_LANDSCAPE.md.

---

## Integration Points

- **SYSTEM_LANDSCAPE.md** — Building Block View tables derived from REPOS.yaml
- **GLOBAL_ARCHITECTURE.md** — repo descriptions should match REPOS.yaml fields
- **DIAGRAMS.md** — System Context diagram should reflect REPOS.yaml dependencies
- `/sync-architecture` — validates REPOS.yaml structured fields for drift

---

## References

- [Backstage Software Catalog docs](https://backstage.io/docs/features/software-catalog/)
- [catalog-info.yaml format](https://backstage.io/docs/features/software-catalog/descriptor-format/)
- [REPOS.yaml](../REPOS.yaml) — our implementation
- [semops-dx-orchestrator#118](https://github.com/semops-ai/semops-dx-orchestrator/issues/118) — implementation issue

