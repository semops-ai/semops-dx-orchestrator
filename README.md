# semops-dx-orchestrator

[![GitHub](https://img.shields.io/badge/org-semops--ai-blue)](https://github.com/semops-ai)
[![Website](https://img.shields.io/badge/web-semops.ai-green)](https://semops.ai)

**The orchestration layer for SemOps** - a multi-repo system implementing Semantic Operations for human+AI collaboration.

## What is SemOps?

**Semantic Operations (SemOps)** is a framework for enabling both human and machine systems to collaborate effectively in any business or organizational domain.

The core thesis: **Shared semantic context enables better human+AI collaboration.**

SemOps implements:
- **Strategic Data** - Data as strategic asset and AI accelerator
- **Explicit Architecture** - Applied DDD at organizational scale
- **Semantic Optimization** - Measuring and maintaining meaning as operational infrastructure

## This Repository

`semops-dx-orchestrator` is the **entry point** for humans and AI agents. It provides:

- **Process documentation** - How we work across repositories
- **Global architecture** - System landscape and coordination patterns
- **Slash commands** - Shared tooling for AI agents (Claude Code)
- **ADR aggregation** - Cross-repo architecture decision tracking
- **Session notes process** - Agent memory across sessions

### Role in the Architecture

```
semops-dx-orchestrator [PLATFORM/DX]
        │
        │  Owns: Process (how we work)
        │  Coordinates: Cross-repo workflows
        │
        ├────────────────────────────────────────┐
        ▼                                        ▼
semops-core [SCHEMA]                    voice-agent [STANDALONE]
        │
        │  Owns: Model (what we know)
        │  Schema, knowledge graph, services
        │
        ├─────────────┬─────────────┐
        ▼             ▼             ▼
semops-publisher  semops-docs   semops-data
[PUBLISHING]      [DOCUMENTS]   [PRODUCT]
        │
        ▼
semops-sites [FRONTEND]
```

**Key insight:** This repo owns *process* (how we work), while `semops-core` owns *model* (what we know).

## Key Concepts

### Pattern as Aggregate Root

SemOps uses **Pattern** as the fundamental unit of meaning - a bounded context with clear semantic boundaries, measurable for coherence.

Patterns are:
- **Concrete** - Standard, well-understood structures (SKOS, PROV-O, DDD)
- **Coherent** - Maintain semantic invariants
- **Composable** - New patterns don't destabilize existing ones
- **AI-friendly** - LLMs excel at working with standard patterns

### Multi-Repo as Departments

Each repository acts like a department in a company:
- Clear bounded context (responsibility)
- Local autonomy within global constraints
- Cross-repo coordination via explicit processes
- This repo provides the "company-wide policies"

### Semantic Coherence

Coherence is measured across three dimensions:
- **Availability** - Can agents find the meaning they need?
- **Consistency** - Do different systems interpret concepts the same way?
- **Stability** - Does meaning stay constant over time?

## Getting Started

### For Humans

1. Read [GLOBAL_ARCHITECTURE.md](docs/GLOBAL_ARCHITECTURE.md) for the system overview
2. Check [SESSION_NOTES.md](docs/SESSION_NOTES.md) for ongoing work
3. Review [ADR_INDEX.md](docs/ADR_INDEX.md) for architectural decisions

### For AI Agents (Claude Code)

1. Install global slash commands from `~/.claude/commands/`
2. Use `/prime` to load context for the current repo
3. Use `/prime-global` to load cross-repo context
4. Follow session notes process for multi-session work

## Documentation

- [docs/GLOBAL_ARCHITECTURE.md](docs/GLOBAL_ARCHITECTURE.md) - System landscape
- [docs/REPOS.yaml](docs/REPOS.yaml) - Repository registry
- [docs/methodology/](docs/methodology/) - Development methodology exports (ADRs, project specs)
- [docs/repository-operational-model.md](docs/repository-operational-model.md) - Repository operational model

## Related Repositories

| Repository | Role | Description |
|------------|------|-------------|
| [semops-core](https://github.com/semops-ai/semops-core) | Schema/Infrastructure | Schema, knowledge graph, services |
| [semops-publisher](https://github.com/semops-ai/semops-publisher) | Publishing | Content workflow, blog agents |
| [semops-docs](https://github.com/semops-ai/semops-docs) | Documents | Theory, implementation docs |
| [semops-data](https://github.com/semops-ai/semops-data) | Product | Data eng/DS utilities |
| [semops-sites](https://github.com/semops-ai/semops-sites) | Frontend | Websites, deployed apps |

## Contributing

This is currently a personal project by Tim Mitchell. Contributions are welcome once the public release is complete.

## License

[TBD - License to be determined for public release]
