# Architecture

> **Repo:** `semops-dx-orchestrator`
> **Role:** Platform/DX - Orchestrator for SemOps
> **Status:** ACTIVE
> **Version:** 1.3.0
> **Last Updated:** 2026-02-06
> **Infrastructure:** [INFRASTRUCTURE.md](INFRASTRUCTURE.md)

## Role

semops-dx-orchestrator is the **orchestration layer** for SemOps. It owns process documentation, global architecture, and cross-repo coordination. This is the entry point for humans and AI agents working across the ecosystem.

**Key distinction:** This repo owns *process* (how we work) and *coordination* (cross-repo patterns). `semops-core` owns *model* (what we know) and *infrastructure* (shared services).

## Ownership

**What this repo owns (source of truth for):**

- Process documentation (how we work)
- Global architecture documentation ([GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md))
- **Domain patterns documentation** ([domain-patterns/](domain-patterns/))
- Cross-repo coordination patterns
- ADR templates and workflow guides
- Session notes process and cross-repo index
- Pattern registry ([schemas/pattern_v1.yaml](../schemas/pattern_v1.yaml)) — manifest of all patterns with lineage, implementations, documentation paths
- Repository registry ([REPOS.yaml](REPOS.yaml))
- Global slash commands (`~/.claude/commands/`)

**What this repo does NOT own (consumed from elsewhere):**

- Schema/domain definitions → `semops-core/schemas/UBIQUITOUS_LANGUAGE.md`
- Infrastructure services → `semops-core` (Docker, databases)
- Content workflow → `semops-publisher`
- Theory documentation → `semops-docs`

## Key Components

| Component | Purpose |
|-----------|---------|
| `docs/GLOBAL_ARCHITECTURE.md` | System landscape for all repos |
| `docs/domain-patterns/` | Centralized domain pattern documentation |
| `docs/REPOS.yaml` | Registry of all repos (source of truth) |
| `docs/ADR_INDEX.md` | Aggregated ADRs across all repos |
| `schemas/pattern_v1.yaml` | Pattern registry (manifest) — patterns, lineage, implementations |
| `.agent/commands.yml` | Workflow contracts and process conventions |
| `docs/templates/` | ARCHITECTURE, INFRASTRUCTURE templates |
| `scripts/gh_project_writer.py` | Sync spec data to GitHub Projects (sequence, status, labels) |
| `scripts/aggregate_adrs.py` | Cross-repo ADR index generation |
| `scripts/aggregate_session_notes.py` | Cross-repo session notes index |
| `scripts/generate_diagram.py` | Architecture diagram generation |
| `~/.claude/commands/` | Global slash commands (shared across repos) |

> **Note:** Status report pipeline (collect, generate, deliver, analyze) lives in `semops-backoffice/scripts/`. Aggregation scripts and `gh_project_writer.py` remain here — they are cross-repo coordination tools owned by semops-dx-orchestrator. `semops-backoffice` has its own copy of `gh_project_writer.py` as `sync_project_fields.py` for its status report field sync.

## Dependencies

| Repo | What We Consume |
|------|-----------------|
| semops-core | Schema definitions (UBIQUITOUS_LANGUAGE.md) |
| All repos | ADRs, session notes (for aggregation) |

## Data Flows

```text
All Repos (ADRs, session notes)
       │
       ▼
┌─────────────────────────────────────┐
│           semops-dx-orchestrator                 │
│                                     │
│  Aggregates:                        │
│  - ADR_INDEX.md (from all repos)    │
│  - SESSION_NOTES.md (index)         │
│  - REPOS.yaml (registry)            │
│  - Status reports (Projects, Issues)│
│                                     │
│  Distributes:                       │
│  - Templates (ARCHITECTURE, etc.)   │
│  - Global commands (~/.claude/)     │
│  - Process documentation            │
│  - Email reports, Calendar events   │
└─────────────────────────────────────┘
       │
       ▼
All Repos (consume templates, commands)
```

## Autonomous Execution

semops-dx-orchestrator includes capabilities for autonomous agent execution:

### YOLO Mode

Safe autonomous execution with git worktree isolation:
- `/yolo-status` - Auto-approve safe commands during status generation
- `/issue --auto` - Autonomous issue processing with worktree isolation

### Experiment Sandbox

Docker-based isolation for risky experiments:
- `/prep-experiment` - Set up Docker container environment
- `/run-experiment` - Execute experiment with full isolation

See [ADR: Plan Autonomous Agent Capabilities](decisions/) for design details.

## Architecture Sync

Cross-repo documentation sync workflow (ADR-0006):

| Command | Purpose |
|---------|---------|
| `/arch-sync` | Per-repo template compliance, REPOS.yaml consistency, stack and infra drift |
| `/global-arch-sync` | Cross-repo audit, bidirectional propagation to global docs |
| `/generate-proposed` | Generate `*.proposed.md` from GitHub issues |
| `/promote-proposed` | Merge proposed → current |

## Related Documentation

- [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) - System landscape (all repos)
- [INFRASTRUCTURE.md](INFRASTRUCTURE.md) - Operational context (containers, envs)
- [commands.yml](../.agent/commands.yml) - Workflow contracts and process conventions
- [ADR_INDEX.md](ADR_INDEX.md) - Cross-repo ADR aggregation
- [pattern_v1.yaml](../schemas/pattern_v1.yaml) - Pattern registry (manifest)
- [decisions/ADR-0006-architecture-infrastructure-sync-workflow.md](decisions/ADR-0006-architecture-infrastructure-sync-workflow.md) - Sync workflow
- [~/.claude/CLAUDE.md](~/.claude/CLAUDE.md) - Global Claude Code instructions
