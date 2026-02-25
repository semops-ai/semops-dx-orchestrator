# Agentic Lineage

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-02-08
> **Provenance:** 1P
> **Derives From:** `open-lineage`, `episode-provenance`

Agentic Lineage extends data lineage to track AI agent interactions as first-class provenance events in the knowledge graph.

---

## Overview

Traditional data lineage tracks ETL transformations — which dataset was read, what transformation was applied, what was written. Agentic lineage extends this to track the full context of AI-assisted decisions: which agent, what context was provided (CLAUDE.md, tool access, conversation history), what tools were used, what was decided, and what changed.

Every agent session becomes an immutable episode in the lineage graph. This enables auditing of AI-generated content, understanding why a decision was made, and detecting when agent context drift leads to inconsistent outcomes.

## Adoption Context

- **Original Capability:** `agentic-lineage` — decision audit trails for AI agent interactions
- **Problem Statement:** Traditional data lineage (ETL tracking) misses AI agent interactions — context windows, tool decisions, reasoning chains. Need to track the full agent session as an immutable episode with provenance.
- **Selection Criteria:** Extend `open-lineage` + `episode-provenance` (3P standards) with agent decision context. Episode-centric provenance enables auditing AI-generated content and detecting context drift across sessions.
- **ADR:** [semops-core ADR-0012: Co-Equal Aggregates](https://github.com/semops-ai/semops-core/blob/main/docs/decisions/ADR-0012-co-equal-aggregates.md)

## Core Concepts

- **Episode:** A discrete agent interaction session with defined inputs, actions, and outputs
- **Context snapshot:** The agent's context at decision time (tools available, files read, instructions)
- **Decision trail:** The sequence of tool calls and reasoning that led to an outcome
- **Lineage edge:** Connects an episode to the artifacts it created or modified

## Related Documentation

- [provenance-and-lineage.md](provenance-and-lineage.md) - Foundation provenance patterns
- [open-lineage.md](open-lineage.md) - Open standard for lineage events
- [episode-provenance.md](episode-provenance.md) - W3C PROV-O episode extension

