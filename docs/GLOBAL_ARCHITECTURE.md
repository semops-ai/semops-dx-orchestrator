# SemOps Global Architecture

This document defines the system architecture for SemOps - a framework for semantic operations enabling human+AI collaboration.

**GitHub:** [github.com/semops-ai](https://github.com/semops-ai)
**Website:** [semops.ai](https://semops.ai)
**Status:** ACTIVE
**Version:** 3.0.0

---

## System Overview

SemOps tests the thesis that **shared semantic context enables better human+AI collaboration**. It implements Domain-Driven Design with **Pattern as the aggregate root** - an applied unit of meaning with a business purpose, measured for semantic coherence and optimization.

```
┌─────────────────────────────────────────────────────────────────┐
│                    semops-dx-orchestrator                        │
│                      [PLATFORM / DX]                             │
│                                                                  │
│  Process docs, global architecture, cross-repo coordination      │
│  The entry point for humans and AI agents                        │
│  Owns how we work, not what we know                              │
└─────────────────────────────────────────────────────────────────┘
        │ coordinates process
        ├───────────────────────────────────────────┐
        ▼                                           ▼
┌────────────────────────────────────────┐  ┌───────────────┐
│             semops-core                │  │  voice-agent  │
│       [SCHEMA / INFRASTRUCTURE]        │  │               │
│                                        │  │ [LOCAL STT]   │
│  Global schema, knowledge bases,       │  │               │
│  infrastructure services               │  │ Speech-to-    │
│  The "semantic operations" hypothesis  │  │ text service  │
└────────────────────────────────────────┘  └───────────────┘
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼
┌───────────────┐ ┌───────────────┐ ┌───────────────┐
│semops-publisher│ │  semops-docs  │ │  semops-data  │
│               │ │               │ │               │
│ [PUBLISHING]  │ │ [DOCUMENTS]   │ │ [PRODUCT]     │
│               │ │               │ │               │
│ Content       │ │ Theory &      │ │ Data eng/DS   │
│ workflow,     │ │ implementation│ │ utilities,    │
│ blog agents   │ │ docs          │ │ first product │
└───────────────┘ └───────────────┘ └───────────────┘
        │
        ▼
┌───────────────────────────────────────────────────────────────┐
│                        semops-sites                            │
│                       [FRONTEND]                               │
│                                                                │
│  Vercel/Supabase, websites, deployed apps                      │
│  (datavis, chatbots, agentic endpoints)                        │
└───────────────────────────────────────────────────────────────┘
```

---

## Active Repositories

### semops-dx-orchestrator (Platform/DX)

**Role:** Process owner, global architecture, and cross-repo coordinator
**Domain:** Platform tooling, process documentation, global architecture

**Owns:**
- Process documentation (how we work)
- Global architecture documentation (this file)
- Cross-repo coordination patterns
- ADR templates and workflow guides
- Session notes process

**Key Files:**
- `docs/GLOBAL_ARCHITECTURE.md` - System landscape (this file)
- `docs/REPOS.yaml` - Registry of all repos (source of truth)
- `docs/PATTERN_IMPLEMENTATION_MATRIX.md` - Pattern ownership across system
- `docs/PROCESS.md` - Process reference
- `docs/ADR_INDEX.md` - Aggregated ADRs across all repos
- `scripts/aggregate_adrs.py` - Generates ADR index from all repos
- `scripts/generate_diagram.py` - Generates Mermaid diagram from REPOS.yaml

**Key Insight:** This repo owns *process* (how we work) and *coordination* (cross-repo patterns), while `semops-core` owns *model* (what we know) and *infrastructure* (services).

---

### semops-core (Schema/Infrastructure)

**Role:** Schema owner and infrastructure provider
**Domain:** Knowledge management, semantic operations, data infrastructure

**Owns:**
- Global DDD schema (Pattern as aggregate root)
- Database schemas (PostgreSQL)
- Graph database (Neo4j)
- Docker infrastructure (services consumed by other repos)
- Ingestion pipelines and workflows
- Vector storage (Qdrant, pgvector)
- Domain patterns documentation

**Key Files:**
- `schemas/UBIQUITOUS_LANGUAGE.md` - Domain term definitions
- `schemas/phase2-schema.sql` - Current schema
- `docs/SYSTEM_CONTEXT.md` - Design philosophy
- `docker-compose.yml` - Infrastructure stack

---

### semops-publisher (Publishing)

**Role:** Content creation and publishing workflow
**Domain:** Blog posts, content agents, asset management

**Owns:**
- Published content assets
- Research/Outline/Draft/Format agents
- WordPress and LinkedIn formatting
- Post directory structure and metadata

**Depends On:** semops-core (patterns, entities, knowledge base)

---

### semops-docs (Documents)

**Role:** Theory and implementation documentation
**Domain:** Theory documentation, implementation notes, pattern rendering

**Owns:**
- Theoretical documentation (bounded concepts)
- Implementation decision records
- Pattern rendering (patterns → human-readable docs)
- Framework documentation

**Depends On:** semops-core (schema definitions, patterns)

---

### semops-data (Product)

**Role:** Data engineering/science hub and first publishable product
**Domain:** Data utilities, notebooks, reusable tools, research RAG pipelines

**Owns:**
- Data engineering utilities
- Research RAG module (ephemeral corpus → meta-analysis)
- Jupyter notebooks
- Sample datasets
- Documentation for external users

**Stack:** Python, Docker, Jupyter, Crawl4AI (web), Docling (PDF), OpenAI (embeddings), Qdrant (vectors), Anthropic (synthesis)

**Depends On:** semops-core (Qdrant, Ollama, Docling services via docker-compose)

**Key Pattern:** Research Synthesis Pattern (RAPTOR-inspired)
- Cluster embeddings to discover themes
- Extract theme summaries via LLM
- Generate meta-analysis reports without explicit questions

---

### semops-sites (Frontend)

**Role:** Web frontend and deployed applications
**Domain:** User-facing websites, deployed endpoints

**Owns:**
- Public websites
- Deployed applications (datavis, chatbots)
- Agentic endpoints
- Static content and MDX pages

**Stack:** Next.js 16, React 19, Tailwind, Supabase, Vercel

**Depends On:** semops-core (Supabase data), semops-publisher (content)

---

### voice-agent (Voice Control)

**Role:** Voice control system for audio production
**Domain:** Voice control, audio production automation

**Owns:**
- Whisper STT service configuration and deployment
- Intent parsing and command routing logic
- MIDI controller mappings for audio hardware
- Voice command vocabulary and aliases
- Session templates for different audio workflows

**Stack:** Python, FastAPI, faster-whisper, CUDA, MIDI, AppleScript

**Depends On:** None (standalone)

---

## Infrastructure Stack

### semops-core Infrastructure

All core services run via Docker Compose in `semops-core`:

| Service | Port | Purpose |
|---------|------|---------|
| **Supabase Studio** | 8000 | Database UI, API management |
| **PostgreSQL** | 5432 | Primary database (via Supabase) |
| **PostgREST** | (internal) | REST API for PostgreSQL |
| **n8n** | 5678 | Workflow automation |
| **Qdrant** | 6333, 6334 | Vector database for RAG |
| **Ollama** | 11434 | Local LLM/embeddings (nomic-embed-text) |
| **Docling** | 5001 | Document processing (PDF, DOCX) |
| **Neo4j** | 7474 (HTTP), 7687 (Bolt) | Graph database for patterns |
| **pgvector** | (internal) | Vector similarity in PostgreSQL |

---

## Data Flows

```
External Content                    Internal Content
(web, docs, APIs)                   (notes, drafts)
       │                                   │
       ▼                                   ▼
┌─────────────────────────────────────────────────────────┐
│                      semops-core                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │  Docling    │  │   n8n       │  │  Ingestion      │  │
│  │  (docs)     │  │  (workflow) │  │  Scripts        │  │
│  └──────┬──────┘  └──────┬──────┘  └────────┬────────┘  │
│         └────────────────┼──────────────────┘           │
│                          ▼                               │
│  ┌──────────────────────────────────────────────────┐   │
│  │              PostgreSQL (Supabase)                │   │
│  │  pattern ──→ entity ──→ edge ──→ delivery        │   │
│  └──────────────────────────────────────────────────┘   │
│         │                    │                          │
│         ▼                    ▼                          │
│  ┌─────────────┐      ┌─────────────┐                   │
│  │   Neo4j     │      │   Qdrant    │                   │
│  │  (graph)    │      │  (vectors)  │                   │
│  └─────────────┘      └─────────────┘                   │
└─────────────────────────────────────────────────────────┘
       │                          │
       ▼                          ▼
┌─────────────┐            ┌─────────────┐
│ semops-     │            │ semops-     │
│ publisher   │            │ sites       │
│             │            │ (frontend)  │
│ Reads:      │            │ Reads:      │
│ - patterns  │            │ - entities  │
│ - entities  │            │ - patterns  │
│             │            │             │
│ Writes:     │            │ Displays:   │
│ - posts/*   │            │ - content   │
│ - assets    │            │ - datavis   │
└─────────────┘            └─────────────┘
```

---

## Naming Conventions

### Repository Names
- `semops-*` - Core SemOps repos
- Other names - Standalone products or tools (e.g., voice-agent)

### Entity IDs
- Kebab-case descriptive: `blog-post-semantic-ops-2024`

### Edge Predicates
- Snake_case verbs: `derived_from`, `depends_on`, `cites`

### Pattern IDs
- Kebab-case: `semantic-coherence`, `aggregate-root`

### Branch Names
- `feature/*` - New features
- `fix/*` - Bug fixes
- `docs/*` - Documentation
- `experiment/*` - Experiments

---

## Development Workflow

### Daily Flow
1. Check issues: `gh issue list`
2. Create feature branch from `main`
3. Make changes (respect domain boundaries)
4. Test locally
5. Push and create PR

### Cross-Repo Changes
When changes span repos:
1. Start with schema changes in `semops-core`
2. Update dependent repos in order
3. Coordinate via GitHub issues

### Schema Changes
Schema changes are high-impact:
1. Read `schemas/UBIQUITOUS_LANGUAGE.md`
2. Create GitHub issue for discussion
3. Update `SCHEMA_CHANGELOG.md`
4. Consider impact on all dependent repos

---

## Architecture Decision Records (ADRs)

Each repo maintains its own decision records in `docs/decisions/`.

### Structure
- **Location:** `docs/decisions/` in each repo
- **Template:** `docs/decisions/TEMPLATE.md`
- **Aggregated Index:** `docs/ADR_INDEX.md` (this repo)

### Naming Convention
| Pattern | Purpose |
|---------|---------|
| `ADR-NNNN-kebab-title.md` | Numbered architectural decisions |
| `ISSUE-NN-kebab-title.md` | Issue exploration/resolution docs |
| `PHASE-N-description.md` | Phase milestone documentation |

### ADR Statuses
- **Draft** - Initial proposal, not yet decided
- **In Progress** - Decision made, implementation underway
- **Complete** - Fully implemented
- **Superseded** - Replaced by another ADR (don't delete, link to replacement)

### Aggregation

Generate a cross-repo ADR index:

```bash
python3 scripts/aggregate_adrs.py --output docs/ADR_INDEX.md
```

---

## Related Documents

**In this repo (semops-dx-orchestrator):**
- [REPOS.yaml](REPOS.yaml) - Registry of all repos (source of truth)
- [ADR_INDEX.md](ADR_INDEX.md) - Aggregated ADRs across all repos
- [PROCESS.md](PROCESS.md) - Process reference and conventions
- [PATTERN_IMPLEMENTATION_MATRIX.md](PATTERN_IMPLEMENTATION_MATRIX.md) - Pattern ownership

**In semops-core:**
- `docs/SYSTEM_CONTEXT.md` - Design philosophy and thesis
- `schemas/UBIQUITOUS_LANGUAGE.md` - Domain term definitions
