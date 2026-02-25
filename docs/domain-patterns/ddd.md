# Domain-Driven Design

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-13
> **Provenance:** 3P
> **Derives From:** —

## Overview

**[Domain-Driven Design](https://domainlanguage.com/ddd/)** (Eric Evans, 2003) is an approach to software development that centers the design on the domain model and its ubiquitous language. SemOps adopts DDD as the primary architectural framework across all repos.

Key DDD concepts applied in SemOps:

- **Ubiquitous Language** — shared vocabulary across code, docs, and conversation
- **Bounded Contexts** — SemOps operates as a single bounded context with one ubiquitous language
- **Strategic Design** — repos are agent role boundaries, not separate bounded contexts
- **Context Mapping** — shared-kernel, published-language, customer-supplier, conformist, anti-corruption-layer patterns govern inter-repo relationships

## Adoption Context

Why DDD was selected as the primary architectural framework for SemOps.

- **Original Capability:** Platform/DX governance — establishing shared language across all repos
- **Problem Statement:** SemOps operates across 7 repos with diverse domains (content, schema, data, voice). Without a shared conceptual framework, each repo drifts into its own language and model, creating semantic fragmentation.
- **Selection Criteria:** DDD provides bounded contexts, ubiquitous language, and strategic design patterns. SemOps adopts DDD because: (1) it solves bounded-context at scale, (2) repos operate as agent role boundaries within a single shared language, (3) integration patterns (shared-kernel, published-language) formalize cross-repo collaboration.
- **ADR:** [ADR-0002: System Landscape](../decisions/ADR-0002-system-landscape.md), [ADR-0008: Bounded Context Extraction](../decisions/ADR-0008-bounded-context-extraction.md)

## Key Concepts

SemOps's DDD model has a specific interpretation (decided semops#122):

- **One bounded context** — all repos share the same ubiquitous language
- **Repos as agent roles** — each repo is a deployment/responsibility boundary, not a semantic boundary
- **Three-layer domain model** — Pattern (WHY), Architecture (WHAT/WHERE/HOW), Content (publishing artifacts)
- **Capabilities trace to patterns** — every capability must link to at least one pattern (coherence signal)

## Related Documentation

- [shared-kernel.md](retired/shared-kernel.md) — DDD integration pattern
- [published-language.md](retired/published-language.md) — DDD integration pattern
- [customer-supplier.md](retired/customer-supplier.md) — DDD integration pattern
- [conformist.md](retired/conformist.md) — DDD integration pattern
- [anti-corruption-layer.md](retired/anti-corruption-layer.md) — DDD integration pattern

