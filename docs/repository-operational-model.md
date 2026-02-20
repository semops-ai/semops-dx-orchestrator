# Repository Operational Model

> **Version:** 1.0.0
> **Status:** Active
> **Last Updated:** 2026-01-10

> Git repositories as the fundamental unit of operational organization, replacing traditional enterprise tools.

---

## Overview

NOTE: This document references the private repo implementation of SemOps, and therefore the repo names aren't accessible; however, the document is included to provide an explanation of how the model works.

The Repository Operational Model treats git repositories as:

- **Bounded contexts** with single responsibilities
- **Semantic functions** that compress context for AI agents
- **Lineage databases** where every commit is an immutable provenance event
- **Decision audit trails** where PRs document business decisions

This pattern derives from version control (git), Domain-Driven Design (bounded contexts), and the broader shift toward "everything is semantic object"

---

## Where Implemented

| Repo | Feature/Component | Status | Notes |
|------|-------------------|--------|-------|
| All repos | Repository structure | Active | Each repo = bounded context |
| `semops-dx-orchestrator` | CLAUDE.md, GLOBAL_ARCHITECTURE.md | Active | Agent context files |
| `semops-core` | UBIQUITOUS_LANGUAGE.md | Active | Canonical definitions |
| `semops-docs` | Frontmatter schema | Active | Structured metadata |

---

## Core Principles

### 1. Repos as Bounded Contexts

Each repository owns a single domain responsibility:

| Repo | Responsibility | What It "Knows" |
|------|----------------|-----------------|
| `semops-dx-orchestrator` | Platform/DX | Process, tooling, global architecture |
| `semops-core` | Schema/Infrastructure | Ground truth definitions, data flows |
| `semops-docs` | Theory/Documentation | 1P concepts, decisions, IP |
| `semops-publisher` | Publishing | Content workflow, blog agents |
| `semops-sites` | Frontend | User-facing apps, deployment |

### 2. Git as Lineage Database

Git already provides what data governance demands:

| Git Feature | Governance Equivalent |
|-------------|----------------------|
| Commit | Immutable provenance event |
| Diff history | Semantic change log |
| Author + timestamp | Attribution signals |
| Tags/releases | Milestones |
| PRs | Decision audit trail |
| Branches | Safe experimentation |

### 3. Repos as Semantic Functions

Repositories compress context for AI agents:

```
Full system context (~100K tokens)
    → Repo context (~5K tokens) + relationship awareness (~500 tokens)
    = Effective context with much less noise
```

When an agent operates in a repo:
1. Reads `CLAUDE.md` → knows the repo's purpose
2. Sees folder structure → knows where things live
3. Understands bounded context → knows what NOT to modify
4. Knows relationships → can reference other repos appropriately

---

## Key Structures

### CLAUDE.md (Agent Context)

Every repo has a `CLAUDE.md` that provides agent instructions:

```markdown
# CLAUDE.md

## Role in Global Architecture
- What this repo owns
- What it depends on
- What depends on it

## Key Files
- Important files for context

## Conventions
- Patterns used in this repo
```

### UBIQUITOUS_LANGUAGE.md (Canonical Definitions)

The schema owner repo maintains canonical term definitions:

```markdown
# Entity
An entity represents...

# Edge
An edge represents a relationship...
```

### Frontmatter Schema (Structured Metadata)

Documents use YAML frontmatter for machine-readable semantics:

```yaml
---
concept_id: semantic-coherence
definition: "The degree of shared meaning..."
owner: semops-core
version: 2.1
---
```

---

## Repos Replace Enterprise Tools

| Traditional Tool | Repository Equivalent |
|-----------------|----------------------|
| **Confluence/SharePoint** | Markdown in repos |
| **Jira** | GitHub Issues + Projects |
| **Slack (alignment)** | GitHub Discussions + PRs |
| **CRM** | Structured YAML/JSON in repos |
| **Workflow tools** | GitHub Actions |

### Why Repos Win

| Feature | Wiki | Repository |
|---------|------|------------|
| **Metadata** | Tags (maybe) | YAML frontmatter, typed folders |
| **Versioning** | Edit history | Full commit graph with diffs |
| **Lineage** | None | Every commit = immutable event |
| **Machine-readable** | No | Yes - frontmatter, JSON |
| **Automation** | None | Actions, hooks, CI/CD |
| **Branching** | None | Parallel experiments |
| **PRs** | None | Decision audit trail |

---

## Multi-Repo Coordination

### Context Map

Repos relate to each other like DDD bounded contexts:

```
semops-dx-orchestrator [PLATFORM]
    │
    └── semops-core [SCHEMA]
            │
            ├── semops-publisher [PUBLISHING]
            ├── semops-docs [DOCUMENTS]
            └── semops-sites [FRONTEND]
```

### Dependency Direction

- **Upstream repos** define contracts (schema, types)
- **Downstream repos** consume and extend
- **Changes flow in one direction** (schema → content → frontend)

### Cross-Repo References

Use GitHub issue references to coordinate:

```
semops-dx-orchestrator#NN (parent issue)
├── semops-publisher#NN (related work)
├── semops-core#NN (schema changes)
└── semops-docs#NN (documentation)
```

---

## Agent Implications

The Repository Operational Model makes AI agents more effective:

### Scope Limitation
Agent knows what it can modify (this repo) vs. read-only reference (other repos).

### Dependency Awareness
Agent understands upstream/downstream relationships.

### Convention Inheritance
Each repo has its own patterns documented in CLAUDE.md.

### Conflict Prevention
Can't accidentally break schema from content repo.

---

## Tradeoffs

### Advantages

| Advantage | Detail |
|-----------|--------|
| **Full lineage** | Every change tracked with attribution |
| **Agent-ready** | CLAUDE.md provides instant context |
| **Portable** | Git repos can move between hosts |
| **Composable** | Repos reference each other cleanly |

### Disadvantages

| Disadvantage | Mitigation |
|--------------|------------|
| **Learning curve** | Git is widely known |
| **Multiple repos** | Clear ownership boundaries help |
| **Not real-time** | Use Issues/Discussions for async |

---

## Related Documentation

- [open-primitive-pattern.md](open-primitive-pattern.md) - GitHub as Open Primitive tool
- [ADR-0002-system-landscape.md](../decisions/ADR-0002-system-landscape.md) - Bounded contexts
- [GLOBAL_ARCHITECTURE.md](../GLOBAL_ARCHITECTURE.md) - System landscape
- [everything-is-github.md](https://github.com/semops-ai/semops-docs/blob/main/docs/Publicv1_Supplemental/narrative/everything-is-github.md) - Extended narrative

---

## 3P Foundation

| Source | What We Adopt |
|--------|---------------|
| **Git** | Distributed version control, commits as events |
| **DDD** | Bounded contexts, ubiquitous language |
| **Infrastructure as Code** | Everything versioned, everything reviewable |
| **12-Factor App** | Codebase in version control, explicit dependencies |

**Our Application:** Repositories as the operational unit for AI-native organizations, replacing traditional enterprise software with structured, versioned, agent-addressable knowledge.
