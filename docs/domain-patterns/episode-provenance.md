# Episode Provenance

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 3P
> **Derives From:** `prov-o`

W3C PROV-O extension for tracking discrete agent interaction episodes as first-class provenance records. Adopted in SemOps for agent session tracking and decision audit trails.

---

## Adoption Context

Why episode-level provenance was adopted for agent interactions.

- **Original Capability:** `agentic-lineage` — decision audit trails for agent interactions
- **Problem Statement:** Agent sessions generate decisions (ingestion choices, classifications, ADR resolutions) but lineage is implicit. When a capability traces to a pattern, you can't ask "which agent made that decision?" or trace the full decision chain.
- **Selection Criteria:** Adopt W3C PROV-O extension for episode-level tracking: each agent session is a discrete episode with inputs, decisions, outputs, and metadata. PROV-O is a standard (interoperable), episodes are first-class records (queryable), agent decisions become transparent and reproducible.
- **ADR:** [ADR-0005: Ingestion Strategy and Corpus Architecture](../decisions/ADR-0005-ingestion-strategy-corpus-architecture.md)

## Source

- **Standard/Origin:** W3C PROV-O extension
- **URL:** N/A

## Related Patterns

- `prov-o`, `open-lineage`, `agentic-lineage`

