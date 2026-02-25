# Explicit Architecture

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-15
> **Provenance:** 1P
> **Derives From:** `viable-systems-model`, `ddd`

Architecture is not real until it is inspectable by both humans and machines. When the architecture itself is modeled as queryable data (entities + typed edges), governance becomes a set of projections over that model rather than a separate enforcement layer. The system validates its own structural completeness passively.

---

## Adoption Context

- **Original Capability:** `domain-data-model` — architecture as queryable data, not documentation
- **Problem Statement:** Governance traditionally lives outside the model — gates, checklists, manual spreadsheets. Architecture is described in prose but not queryable. Coverage gaps are invisible until someone manually audits.
- **Selection Criteria:** DDD + Viable Systems Model insight: encode architecture as an entity/edge graph. Coverage views (`pattern_coverage`, `capability_coverage`) derive lifecycle and governance from structural completeness — governance is just SQL.
- **ADR:** —

## Core Thesis

Traditional governance adds enforcement on top of architecture: gates, approvals, compliance checks. Explicit Architecture inverts this. If your architecture is a queryable data model, then governance questions are just queries:

- "Which capabilities lack pattern justification?" = `SELECT * FROM capability_coverage WHERE pattern_count = 0`
- "Which patterns have nothing deployed against them?" = `SELECT * FROM pattern_coverage WHERE capability_count = 0`
- "Is this capability active?" = computed from edge completeness, not manually assigned

The governance mechanism and the operational data model are the same artifact.

## 3P Foundations

| 3P Pattern | Contribution |
|-----------|-------------|
| **Viable Systems Model** (Beer) | Self-regulating systems through feedback loops; the feedback mechanism must be part of the system |
| **DDD** (Evans) | Bounded contexts, aggregate roots — the structural vocabulary that makes architecture modelable |

## 1P Innovation

The specific innovation is: **coverage views as homeostatic sensors over an entity/edge graph**.

- `pattern_coverage` view = passive fitness function (always up to date, no one has to "run" it)
- `capability_coverage` view = bidirectional completeness check (patterns above, repos below)
- `derive_lifecycle_stages()` = lifecycle computed from structural completeness, not assigned by humans
- Andon cord = `SELECT` query — the answer was always sitting in the data

## Key Principles

1. **Architecture as data model.** Entities, edges, and typed predicates — not wiki pages or slide decks.
2. **Governance as projection.** Coverage views are not separate tools — they're SQL views over the same tables the system uses to operate.
3. **Lifecycle as emergent property.** A capability's lifecycle stage is derived from its edge coverage (has patterns? has repos?), not assigned by a human.
4. **Passive self-validation.** The views don't enforce anything. They make the structural truth queryable. The andon cord is a query, not an alarm.

## Related Patterns

- `viable-systems-model` — 3P foundation: homeostatic self-regulation
- `ddd` — 3P foundation: bounded contexts as structural vocabulary
- `semantic-coherence` — Measures content alignment; Explicit Architecture measures structural completeness
- `backstage-software-catalog` — 3P software catalog with lifecycle; Explicit Architecture goes deeper (edge-derived lifecycle)
- `content-lifecycle-states` — Lifecycle stages that Explicit Architecture derives automatically

## References

- [GOVERNANCE_MODEL.md](semops-core/schemas/GOVERNANCE_MODEL.md) — Full governance model spec
- [ADR-0011](semops-core/docs/decisions/ADR-0011-agent-governance-model.md) — Design decision
- [Explicit Architecture (theory)](semops-docs/docs/SEMOPS_DOCS/SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/explicit-architecture.md) — Original theory doc
- [Discovery Through Data](semops-docs/docs/SEMOPS_DOCS/SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/discovery-through-data.md) — Related concept

