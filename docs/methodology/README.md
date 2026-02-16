# Methodology Exports

Machine-readable exports of SemOps development methodology. These YAML files describe **how we work** — the processes, templates, and patterns used across the SemOps ecosystem.

## Files

| File | What it describes |
|------|-------------------|
| [decisions.yaml](decisions.yaml) | Architecture Decision Records — process, template structure, and cross-repo index |
| [project-methodology.yaml](project-methodology.yaml) | Project Spec Pattern — scope definitions, acceptance criteria, execution sequences |

## Purpose

These exports serve two goals:

1. **Transparency** — Show the methodology behind the architecture, not just the architecture itself
2. **Reusability** — Structured YAML that agents and tools can consume directly

## What's included vs. excluded

**Included:** Process descriptions, template structures, workflow definitions, and sanitized indexes (titles, dates, status).

**Excluded:** Individual ADR content, internal project specs, issue references, and coordination details. These are internal work artifacts.

## Related

- [GLOBAL_ARCHITECTURE.md](../GLOBAL_ARCHITECTURE.md) — System landscape
- [REPOS.yaml](../REPOS.yaml) — Repository registry
- [semops-core schemas](https://github.com/semops-ai/semops-core/tree/main/schemas) — Domain schema and ubiquitous language
