# SemOps Global Architecture

This document defines the system architecture for SemOps - a personal knowledge and publishing system built on semantic operations principles.

**Owner:** Tim Mitchell
**Status:** ACTIVE
**Version:** 3.2.0
**Last Updated:** 2026-02-16

> **See Also:** [DIAGRAMS.md](DIAGRAMS.md) (visual diagrams) | [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) (services/ports) | [GLOBAL_ARCHITECTURE.proposed.md](GLOBAL_ARCHITECTURE.proposed.md) (pending changes)

---

## System Overview

SemOps is testing the thesis that **shared semantic context enables better human+AI collaboration**. It implements Domain-Driven Design with two co-equal core aggregates — **Pattern** (prescriptive intent) and **Coherence Assessment** (evaluative alignment) — forming the Semantic Optimization Loop, measured for semantic coherence and optimization.

> **Visual diagrams:** See [DIAGRAMS.md](DIAGRAMS.md) for system context, data flows, and infrastructure diagrams.

**Repo Layers:**

| Layer | Repos | Purpose |
|-------|-------|---------|
| **SemOps Core** | semops-dx-orchestrator, semops-core, semops-data | Reusable semantic operations engine — process coordination, schema/services, coherence scoring. Domain-agnostic. |
| **Content** | semops-publisher, semops-docs, semops-sites | Current domain application — publishing, theory docs, frontends. Replaceable with a different domain. |
| **Operations** | semops-backoffice | Agentic enterprise — attention management, financial automation. |

**Key insight:** SemOps Core is the product — a reusable engine for semantic operations. Content/Publishing is the first domain application built on it. The publishing layer could be replaced entirely with a different domain while the core remains unchanged.

### Repos as Agent Role Boundaries

Repos are **not** DDD subdomain boundaries. They are **agent role boundaries** that simulate organizational role boundaries in a company. - organizational modules that scope what an AI agent (or human) needs in context to do useful work.

**What repos provide:**

- **Agent focus** - Each repo limits the context window to a relevant slice of the system. An agent working in semops-publisher has content creation context; in semops-data, research/analysis context.
- **Human navigation** - "Where does this file go?" is answered by repo role, not domain membership.
- **Infrastructure proximity** - Code lives near the services it primarily interacts with.
- **Single ownership (DRY)** - Each capability has exactly one owning repo. Other repos consume, never copy. Following the DRY principle at the repo level eliminates duplication drift:
  - `semops-sites` owns display: fonts, Mermaid styling, UI components
  - `semops-publisher` owns content rules: style guides, templates, export formats
  - `semops-core` owns the model: schema, UBIQUITOUS_LANGUAGE.md, ingestion
  - When a deployment needs resources from multiple repos, each resource has lineage back to its owning repo - there is no second copy that can fall out of sync.

**What repos are NOT:**

- **Domain boundaries** - Subdomains (Semantic Operations, Knowledge Management, Content Publishing) cut *across* repo lines. Coherence scoring in semops-data is part of the same core domain as pattern declaration in semops-core.
- **Access boundaries** - The shared knowledge base (PostgreSQL, Qdrant, Neo4j) is accessible to any repo. Any agent can query it. Schema is *stored* in semops-core but *used* everywhere.
- **Fixed boundaries** - Functionality can be moved between repos without domain impact. A chunk of semops-publisher could split to a new repo or merge into semops-core with minimal friction.

**Scripts as Capability Implementations:**

Capabilities decompose into small, focused scripts — each one a bounded piece of executable functionality. This is an anti-monolith principle: features live in identifiable, replaceable files rather than buried in large applications.

- Each script in `scripts/` should trace to a capability (and transitively to a pattern)
- Scripts are self-describing via comments and docstrings — git provides the lineage
- The `/arch-sync` workflow inventories scripts and flags any without capability traceability
- Cross-repo script awareness lives in each repo's ARCHITECTURE.md "Key Components" section

**Shared Domain Model:**

All repos operate within a single ubiquitous language defined in `semops-core/schemas/UBIQUITOUS_LANGUAGE.md`. The domain model (Pattern → Entity → Edge → Delivery) is shared infrastructure, not owned by one bounded context.

**Scale Projection:**

The shared knowledge base is infrastructure that any service can query:

- Today: repos access PostgreSQL/Qdrant/Neo4j via localhost
- Tomorrow: a new container on the Docker network queries the same services
- At scale: services behind an API gateway, same shared data layer

This means repos are **deployment-flexible**. The architecture doesn't change when the deployment topology changes.

> See [DDD Alignment](#ddd-alignment) below for the formal subdomain classification, integration patterns, and domain model.

---

## DDD Alignment

SemOps applies Domain-Driven Design with adaptations for a single-owner, multi-repo, AI-agent-assisted system. The key structural decisions are documented here; see [DIAGRAMS.md](DIAGRAMS.md) for visual representations.

### Subdomains

Subdomains are **emergent groupings** of capabilities, not repo boundaries or stored concepts. They are discovered by tracing capability→pattern relationships (query, don't store). A single subdomain may span multiple repos, and a single repo may participate in multiple subdomains. Subdomains cut across all three architectural layers (Pattern / Architecture / Content).

| Subdomain | Type | Repos | Scope |
|-----------|------|-------|-------|
| **Semantic Operations** | Core | semops-core, semops-data | Pattern model, knowledge graph, coherence scoring, corpus routing |
| **Knowledge Management** | Core | semops-core, semops-data, semops-publisher | Ingestion, classification, retrieval, episode provenance, Research RAG |
| **Content Publishing** | Supporting | semops-publisher, semops-sites, semops-docs | Multi-surface publishing, MDX pipeline, resume composition, theory docs |
| **Platform/Process** | Supporting | semops-dx-orchestrator | Architecture governance, sync workflows, coordination |
| **Agentic Operations** | Generic | semops-backoffice | Financial pipeline, attention stream |

> **Visual:** [DIAGRAMS.md — DDD Subdomain Classification](DIAGRAMS.md#ddd-subdomain-classification)

### Integration Patterns

Repos interact through standard DDD integration patterns. The shared knowledge base (PostgreSQL, Qdrant, Neo4j) is infrastructure accessible to any repo; these patterns describe how repos relate to the **domain model**, not the database.

| Pattern | Repos | Relationship |
|---------|-------|--------------|
| **Shared Kernel** | semops-core ↔ semops-publisher, semops-docs | Both depend on UBIQUITOUS_LANGUAGE.md schema |
| **Published Language** | semops-dx-orchestrator → all repos | Global architecture, process docs, ADR templates |
| **Shared Kernel** | semops-core ↔ semops-data | Both operate within the same core domain model (Pattern/Entity/Episode) — Architecture + Data Systems |
| **Customer-Supplier** | semops-core → semops-data | Upstream provides Qdrant, Docling, Supabase services |
| **Customer-Supplier** | semops-core → semops-sites | Upstream provides Supabase services |
| **Conformist** | semops-publisher, semops-docs → semops-core | Downstream adopts upstream Pattern/Entity model as-is |
| **Anti-Corruption Layer** | semops-backoffice → semops-core | Translates shared PostgreSQL to own financial domain |
| **Customer-Supplier** | semops-publisher → semops-sites | MDX content, resume seed.sql, fonts/templates |

> **Full integration map with metadata:** [semops-core/docs/STRATEGIC_DDD.md](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md#integration-patterns)
> **Visual:** [DIAGRAMS.md — DDD Context Map](DIAGRAMS.md#ddd-context-map)

### Domain Model

The domain model has two **co-equal core aggregates** that form the Semantic Optimization Loop ([semops ADR-0012](https://github.com/semops-ai/semops-core/blob/main/docs/decisions/ADR-0012-pattern-coherence-co-equal-aggregates.md)):

- **Pattern** (prescriptive) — what we should look like. An applied unit of meaning with a business purpose, organized via SKOS taxonomy with provenance tiers (1P/2P/3P) and adoption lineage.
- **Coherence Assessment** (evaluative/directive) — how well reality matches intent, and what to do about it. Audits the Pattern → Capability → Script chain and drives pattern evolution, reversal, or realignment.

Pattern without Coherence is documentation. Coherence without Pattern is measurement without a reference. Together: **Pattern pushes, Coherence aligns.**

The model is defined in `semops-core/schemas/UBIQUITOUS_LANGUAGE.md` (v8.0.0) and organized into three layers per [semops ADR-0009](https://github.com/semops-ai/semops-core/blob/main/docs/decisions/ADR-0009-strategic-tactical-ddd-refactor.md).

**Three-Layer Architecture:**

| Layer | Entities | Purpose |
|-------|----------|---------|
| **Pattern** (Core Domain — the WHY) | Pattern, Pattern Edge, Coherence Assessment | Two co-equal aggregates (Semantic Optimization Loop). Pattern: SKOS taxonomy with adoption relationships. Coherence Assessment: audits Pattern → Capability chain, drives evolution. |
| **Architecture** (Strategic Design — WHAT/WHERE/HOW) | Entity (`capability`), Entity (`repository`), Ingestion Run/Episode | Capabilities implement patterns; repos deliver capabilities. Integration patterns between repos. |
| **Content** (DAM Publishing — artifacts) | Entity (`content`), Edge, Surface, Delivery, Brand/Product | Publishing artifacts with W3C PROV-O relationships. Per-surface governance. |

The `entity` table uses an **`entity_type` discriminator** (`content`, `capability`, `repository`) — one table serving three purposes. All entity types share embeddings, pattern links, edges, and JSONB metadata.

**Stable Core vs Flexible Edge** (Content layer): Content entities with `primary_pattern_id` set are classified (linked to the Pattern layer). Content entities with `primary_pattern_id = NULL` are unclassified (awaiting classification). Promotion assigns a pattern; rejection archives.

**Capability→Pattern coherence:** Every capability must trace to ≥1 pattern. Capabilities with zero pattern links indicate missing patterns or unjustified capabilities — a measurable coherence signal.

> **Full capability registry and integration map:** [semops-core/docs/STRATEGIC_DDD.md](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md)
> **Visual:** [DIAGRAMS.md — DDD Domain Model](DIAGRAMS.md#ddd-domain-model)

---

## Active Repositories

> **Architecture vs Infrastructure:** Each repo section below describes **architecture** — capabilities, domain role, ownership boundaries, and key relationships. For **infrastructure** — services, ports, stacks, environment variables — see [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md).

### semops-dx-orchestrator (Platform/DX)

**Role:** Process owner, global architecture, and cross-repo coordinator
**Domain:** Platform tooling, process documentation, global architecture
**Repository:** [semops-ai/semops-dx-orchestrator](https://github.com/semops-ai/semops-dx-orchestrator)
**Version:** 1.3.0

**Architecture (capabilities):**

- **Orchestration** - Process documentation, global architecture (this file), cross-repo coordination patterns, project specs
- **Context Engineering** - Agent context management: CLAUDE.md per repo, global slash commands, MCP configuration, domain pattern docs
- **Autonomous Execution** - YOLO Mode (worktree isolation), Experiment Sandbox (Docker-based isolation)
- **Pattern Management** - Pattern registry (`schemas/pattern_v1.yaml`), domain pattern documentation (`docs/domain-patterns/`), architecture sync (`/arch-sync`, `/global-arch-sync`)

**Key Files:**

- `docs/GLOBAL_ARCHITECTURE.md` - System landscape (this file)
- `docs/domain-patterns/` - Centralized domain pattern documentation
- `docs/REPOS.yaml` - Registry of all repos (source of truth)
- `schemas/pattern_v1.yaml` - Pattern registry (manifest)
- `~/.claude/commands/` - Global slash commands (shared across all repos)

> **Note:** Status report scripts (generate, deliver, sync) migrated to `semops-backoffice/scripts/` (2026-02-10).

**Key Insight:** This repo owns *process* (how we work) and *coordination* (cross-repo patterns), while `semops-core` owns *model* (what we know) and *infrastructure* (services).

---

### semops-core (Schema/Infrastructure)

**Role:** Schema owner and infrastructure provider
**Domain:** Knowledge management, semantic operations, data infrastructure
**Repository:** [semops-ai/semops-core](https://github.com/semops-ai/semops-core)
**Version:** 3.2.0

**Architecture (capabilities):**

- **Domain Data Model** - Pattern + Coherence Assessment (co-equal aggregates), UBIQUITOUS_LANGUAGE.md, SKOS + PROV-O
- **Internal Knowledge Access** - MCP Server (10 tools) + Query API for cross-repo agent access. Two query surfaces: semantic search (content discovery via embeddings) and ACL queries (deterministic architectural lookups against DDD schema — patterns, capabilities, integrations, fitness checks)
- **Ingestion Pipeline** - Provenance-tracked ingestion with corpus routing and episode lineage
- **Agentic Lineage** - Episode-centric provenance tracking for DDD operations
- **Pattern Management** - Pattern declaration, SKOS taxonomy, adoption relationships
- **Coherence Scoring** - Semantic coherence measurement against pattern taxonomy

**Infrastructure (services):** See [GLOBAL_INFRASTRUCTURE.md — Core Services](GLOBAL_INFRASTRUCTURE.md#core-services-semops-core)

- PostgreSQL, Qdrant, Neo4j, Docling, n8n, Ollama (all Docker Compose)

**Key Files:**
- `schemas/UBIQUITOUS_LANGUAGE.md` - Domain term definitions (v8.0.0)
- `api/mcp_server.py` - MCP server for agent KB access (semantic search + ACL queries)
- `api/query.py` - Query API (REST endpoints)
- `scripts/search.py` - Shared semantic search module
- `scripts/schema_queries.py` - Shared ACL query module (patterns, capabilities, integrations, fitness)
- `scripts/lineage/` - Episode-centric provenance system
- `docker-compose.yml` - Infrastructure stack

---

### semops-publisher (Content)

**Role:** Content creation and AI-assisted writing for all publishing surfaces
**Domain:** Multi-format content creation, style guides, AI writing assistants
**Repository:** [semops-ai/semops-publisher](https://github.com/semops-ai/semops-publisher)
**Version:** 1.2.0

**Architecture (capabilities):**

- **Style guides** - Blog, technical docs, whitepapers, social
- **Content creation agents** - Research/Outline/Draft/Format pipeline
- **AI writing assistant** - Edit capture, style learning workflows
- **Resume composition** - Dimensional schema for structured career data
- **MDX publishing** - Transform markdown to MDX for Next.js sites
- **Multi-surface publishing** - Blogs, social, resumes, proposals, whitepapers
- **PDF export** - Custom templates via semops-sites
- 23 slash commands for content workflows

**Key Files:**

- `docs/resumes/RESUME_SCHEMA.md` - Resume dimensional model
- `scripts/generate_mdx.py` - MDX publishing
- `scripts/corpus_to_sql.py` - Resume seed generation
- `.claude/commands/` - 23 slash commands for workflow
- `scripts/export_pdf.py` - PDF generation

**Depends On:** semops-core (patterns, entities, MCP server), semops-sites (fonts, PDF templates)

---

### semops-docs (Documents)

**Role:** Theory and implementation documentation
**Domain:** Theory documentation, implementation notes, pattern rendering
**Repository:** [semops-ai/semops-docs](https://github.com/semops-ai/semops-docs)

**Architecture (capabilities):**

- **Theory documentation** - 1P bounded concepts
- **Implementation decision records**
- **Pattern rendering** - Patterns → human-readable docs
- Private notes and drafts

**Depends On:** semops-core (schema definitions, patterns)

---

### semops-data (Analytics/MLOps)

**Role:** Analytics, coherence scoring, and research pipelines
**Domain:** MLOps, coherence measurement, research RAG, data utilities
**Repository:** [semops-ai/semops-data](https://github.com/semops-ai/semops-data)
**Version:** 1.2.0

**Architecture (capabilities):**

- **Research RAG** - Ephemeral corpus → meta-analysis (RAPTOR-inspired synthesis)
- **Coherence scoring** - MLflow-tracked experiments measuring semantic drift
- **Data lineage tracking** - OpenLineage, Marquez
- **Synthetic data generation** - SDV, Faker
- Data engineering utilities, sample datasets, documentation for external users

**Infrastructure (services):** See [GLOBAL_INFRASTRUCTURE.md — Repo-Specific Services](GLOBAL_INFRASTRUCTURE.md#repo-specific-services)

- GPU-enabled DevContainer, Jupyter Lab, MLflow

**In Development:**
- Reference Architecture Toolkit
- Agentic Lineage concepts

**Key Files:**
- `src/data_systems_toolkit/research/` - Research RAG pipeline module
- `docs/ARCHITECTURE.md` - System architecture
- `docs/INFRASTRUCTURE.md` - Service dependencies

**Depends On:** semops-core (Qdrant, Docling, Ollama, MCP server)

**Key Pattern:** Research Synthesis Pattern (RAPTOR-inspired)
- Cluster embeddings to discover themes
- Extract theme summaries via LLM
- Generate meta-analysis reports without explicit questions

---

### semops-sites (Frontend)

**Role:** Web frontend and deployed applications
**Domain:** User-facing websites, deployed endpoints
**Repository:** [semops-ai/semops-sites](https://github.com/semops-ai/semops-sites)
**Version:** 1.4.0

**Architecture (capabilities):**

- **Web Publishing** - semops-ai.com, semops.ai (Next.js + Vercel)
- **Font Infrastructure** - Centralized font management, conversion, cross-repo distribution
- **PDF Templates** - LaTeX templates for branded PDF export via Pandoc

**Infrastructure (services):** See [GLOBAL_INFRASTRUCTURE.md — Repo-Specific Services](GLOBAL_INFRASTRUCTURE.md#repo-specific-services)

- Next.js dev servers (:3100, :3101), Vercel (prod), Supabase Cloud

**Monorepo Structure:**

- `apps/semops-ai/` - semops-ai.com
- `apps/semops/` - semops.ai
- `packages/fonts/` - Centralized font infrastructure
- `packages/pdf-templates/` - LaTeX templates for PDF export
- `packages/shared/` - Shared components

**Depends On:** semops-core (Supabase data), semops-publisher (MDX content, resume seed.sql)

---

### semops-backoffice (Operations)

**Role:** Agentic enterprise — replaces SaaS tools with agent-driven automation
**Domain:** Attention management, financial automation, voice control, email ingestion
**Repository:** semops-backoffice *(private)*
**Version:** 1.1.0

**Architecture (capabilities):**

- **Attention Stream** - Unified view of actionable items from multiple sources (email, GitHub Projects, manual CLI, calendar)
- **Financial Pipeline** - Email-based financial automation (transaction parsing, invoice extraction, vendor pattern matching, Beancount export)
- **Voice Control** - Voice commands for audio production (consolidated from voice-agent). Whisper STT Phase 1 complete; intent parsing Phases 2-3 pending.
- **Status Reports** - Daily project status generation and delivery

**Infrastructure (services):** See [GLOBAL_INFRASTRUCTURE.md — External Services](GLOBAL_INFRASTRUCTURE.md#external-services)

- Fastmail (IMAP, CalDAV, SMTP), GitHub API, PostgreSQL (via semops-core)

**Key Files:**

- `attention.py` - Attention CLI (list, add, done, snooze, poll-email, sync-calendar)
- `main.py` - Financial pipeline CLI (process, daemon, export)
- `src/attention_service.py` - Unified attention view
- `src/jmap_client.py` - Fastmail IMAP client (despite filename)
- `schema.sql` - PostgreSQL schema for financial data

**Depends On:** semops-core (PostgreSQL for financial data), tracker-pr (GitHub repo used as issue tracker for attention items — not a SemOps application repo)

**Security:** Secure tier - private financial data, never published

---

## Future Repositories (Placeholders)

### semops-crm (Planned)

**Role:** Customer relationship and marketing
**Domain:** Outreach, marketing, pipeline, networking

**Will Own:**
- Contact management
- Marketing campaigns
- Sales pipeline
- Networking tracking

---

## Deprecated Repositories

These repos are archived or deprecated. Do not use for new work.

| Repo | Reason | Disposition |
|------|--------|-------------|
| `project-ike` | Replaced by private repo | Archive |
| `ike-knowledge-ops` | Merged into semops-core | Archive |
| `ike-meta-repo` | No longer needed | Delete |
| `blog-generator` | Replaced by semops-publisher | Archive |
| `tac-1` | Experiment complete | Archive |
| `colemedin_context-engineering` | External fork, not used | Delete |
| `contextualizer` | Merging into semops-core | Merge |
| `voice-agent` | Consolidated into semops-backoffice (Productivity) | Merge |

---

## Infrastructure

> **Full details:** [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) — services, ports, environment variables, Docker networks.
> **Visual diagram:** [DIAGRAMS.md — Infrastructure Services](DIAGRAMS.md#infrastructure-services)

---

## Naming Conventions

### Repository Names
- `semops-*` - Core SemOps repos (formerly `ike-*`)
- `*-pr` - Active project repos
- Other names - Standalone products or tools

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

## Repository Visibility

| Visibility | Repos |
|------------|-------|
| **Public** | semops-data, semops-sites |
| **Private** | semops-docs, semops-publisher, semops-core (sanitized versions published) |

---

## Governance Model

Artifact governance follows the agent governance model defined in [semops ADR-0011](https://github.com/semops-ai/semops-core/blob/main/docs/decisions/ADR-0011-agent-governance-model.md). Full spec: [schemas/GOVERNANCE_MODEL.md](https://github.com/semops-ai/semops-core/blob/main/schemas/GOVERNANCE_MODEL.md).

### Universal Lifecycle

All entity types share a 5-stage lifecycle stored in `entity.metadata.lifecycle_stage`:

```
DRAFT → ACTIVE → STABLE → DEPRECATED → ARCHIVED
```

The lifecycle evidence differs by entity type:

| Entity Type | Draft → Active Evidence | Governance Mechanism |
|-------------|------------------------|---------------------|
| **Capability** | `pattern_count > 0` AND `repo_count > 0` (has both WHY and WHERE) | Edge coverage via `capability_coverage` view |
| **Repository** | Delivers at least one capability | Edge coverage via `repo_capabilities` view |
| **Content** | Ingested, classified, embedded | Source config path routing (`corpus_routing.rules`) |
| **Pattern** | Stateless — activation is per-context via `implements` edge on capabilities | `pattern_coverage` view (emergent from capability lifecycle) |

### Governance Modes

| Mode | Scope | Enforcement |
|------|-------|-------------|
| **Internal (andon cord)** | Architecture updates, ingestion, edge proposals, pattern management | Self-correcting: act → record → auto-recover. Human inspects on demand. |
| **Public boundary (hard gate)** | Publishing to surfaces (WordPress, LinkedIn, semops.ai) | Gated: act → record → gate → approve → publish. |

Mode is determined by **delivery target**, not entity type.

### Operational Layers

- **Layer 1 (state inspection):** `/arch-sync`, `/project-review`, coverage views — "what's the state of things?"
- **Layer 2 (content verification):** `ingest_domain_patterns.py --audit`, crosswalk audits — "does declared match actual?"

### Scale Projection

| Level | Model | Current |
|-------|-------|---------|
| L1 | Human drives | Most operations |
| L3 | Agent acts, human reviews | Ingestion classification |
| L4 | Agent autonomous, coherence validates | Ingestion episodes |
| L5 | Full agentic, anomalies trigger HITL | Target state |

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
cd semops-dx-orchestrator
python3 scripts/aggregate_adrs.py --output docs/ADR_INDEX.md
```

This produces [ADR_INDEX.md](ADR_INDEX.md) with:
- Status summary across all repos
- Per-repo tables with links
- Timeline view sorted by date

### When to Create an ADR
- Significant architectural decisions
- Technology choices with long-term implications
- Design patterns affecting multiple components
- Decisions that future-you will wonder "why did I do this?"

### Issues + ADRs Workflow

GitHub Issues and ADRs serve complementary purposes:

| GitHub Issue | ADR |
|--------------|-----|
| "What needs to be done" | "Why and how we decided" |
| Trackable, assignable | Documented reasoning |
| Cross-repo linkable | Implementation details |
| Status in `gh issue list` | Session logs for multi-session work |

**The workflow:**

1. **Issue created** → defines the work
2. **ADR created** (if significant) → documents the decision, links to issue
3. **Work happens** → branch, commits, session logs in ADR
4. **PR merges** → implementation complete
5. **Issue closed** → work done
6. **ADR status → Complete** → decision documented

**Cross-repo dependencies:**

Use issue references to link work across repos:
```markdown
## Blocked By
- semops-ai/semops-core#NN - Concept table readiness

## Blocks
- semops-ai/semops-publisher#NN - Phase 1.5 implementation
```

GitHub renders these as clickable links with status indicators.

**When to use PRs vs direct commits:**

| Scenario | PR | Direct to Main |
|----------|:--:|:--------------:|
| Code changes (features, fixes) | ✓ | |
| Schema changes | ✓ | |
| ADR creation (Draft status) | | ✓ |
| ADR updates (status, session logs) | | ✓ |
| Documentation updates | | ✓ |
| Config changes affecting runtime | ✓ | |

---

## Related Documents

**Companion documents:**
- [DIAGRAMS.md](DIAGRAMS.md) - Visual diagrams (system context, data flows, infrastructure)
- [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) - Services, ports, environment variables
- [PORTS.md](PORTS.md) - Detailed port registry

**In this repo (semops-dx-orchestrator):**
- [REPOS.yaml](REPOS.yaml) - Registry of all repos (source of truth)
- [ADR_INDEX.md](ADR_INDEX.md) - Aggregated ADRs across all repos
- [commands.yml](../.agent/commands.yml) - Workflow contracts and process conventions
- [pattern_v1.yaml](../schemas/pattern_v1.yaml) - Pattern registry (manifest)
- [decisions/ADR-0007](decisions/ADR-0007-global-documentation-restructure.md) - This restructure decision

**In semops-core:**

- `schemas/UBIQUITOUS_LANGUAGE.md` - Domain term definitions
- `schemas/GOVERNANCE_MODEL.md` - Agent governance model (lifecycle, modes, per-entity specs)
- `docs/decisions/ADR-0011` - Agent governance model ADR

**Global:**
- `~/.claude/CLAUDE.md` - Global Claude Code instructions
- `~/.claude/commands/` - Shared slash commands
