# Explicit Enterprise

> **Version:** 1.1.0
> **Status:** active
> **Last Updated:** 2026-02-17
> **Provenance:** 1P
> **Derives From:** `platform-engineering`, `context-engineering`, `task-management`, `accounting-system`

Explicit Enterprise describes how human and AI agents collaborate through shared platforms, structured context, and explicit task decomposition. The enterprise is explicit: plain text, CLI, repos as bounded contexts, git as governance, local-first, open primitives over SaaS opacity. Everything is primitive, inspectable, and text-based.

---

## Overview

The organizational pattern behind SemOps. The system is designed for human-AI collaboration as the default operating mode:

- **Humans provide:** Judgment, creativity, domain expertise, approval decisions
- **AI agents provide:** Execution speed, consistency, breadth of search, pattern recognition
- **The collaboration is mediated through:** Platform engineering (shared tooling), context engineering (CLAUDE.md, structured prompts), task management (GitHub Issues, project specs), and accounting (value tracking for effort allocation)

This is not "AI replacing humans" or "humans directing AI." It's a genuine collaboration where each party contributes capabilities the other lacks — mediated by explicit, inspectable structures that both humans and agents can navigate.

## Adoption Context

- **Original Capability:** `orchestration` — explicit human+AI collaboration framework
- **Problem Statement:** Enterprise automation treats tools as black boxes (SaaS, APIs, UI wrappers). The collaboration between humans and AI agents is implicit and uninspectable. Need shared platforms, structured context, and explicit task decomposition.
- **Selection Criteria:** Invert SaaS opacity: everything text-based, CLI-first, repos as bounded contexts, git as governance layer. Platform engineering + context engineering mediate the collaboration between human and agent.
- **ADR:** [ADR-0001: SemOps Organization Migration](../decisions/ADR-0001-semops-organization-migration.md)

## Related Documentation

- [platform-engineering.md](platform-engineering.md) - Shared tooling layer
- [context-engineering.md](retired/context-engineering.md) - AI context design
- [scale-projection.md](scale-projection.md) - Scale progression framework

