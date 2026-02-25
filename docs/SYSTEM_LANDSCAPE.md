# System Landscape

> **Framework:** Arc42 (adapted) — structured architecture documentation
> **Replaces:** PROJECT_CONTEXT.md
> **Issue:** [#118](https://github.com/semops-ai/semops-dx-orchestrator/issues/118)

---

## 1. Introduction and Goals

### Quality Goals

| Priority | Goal | Implication |
| -------- | ---- | ----------- |
| 1 | **Force multiplier** — one person, agentic-friendly | Architecture must be navigable by AI agents; structured data over prose; explicit conventions |
| 2 | **Best practices under constraints** — DDD, PIM, DAM, SKOS | Adopt established patterns (3P) rather than inventing; every capability traces to a pattern |
| 3 | **Fully transparent and explicit** — expert-auditable, leader-readable | Code speaks for itself to practitioners; docs speak for themselves to non-technical leaders; claims are verifiable |

### Requirements

1. **Domain-agnostic core** — Semantic Operations should work for any domain. The architecture has a "core" (two co-equal aggregates: Pattern + Coherence Assessment) that encapsulates Semantic Operations, and any domain can be bolted on. Our first iteration is digital publishing (Content layer), but it could be replaced entirely.

2. **Innovation over completion** — The architecture exists to discover new and better ways of working with AI and explaining those discoveries. We prioritize innovation and expansion of ideas over "just get it to work" — "Overkill is Underrated."

3. **Self-validating architecture** — Per Semantic Operations principles, we use semantic governance to enable agentic management of not just code, but higher-level structures (architecture, infrastructure, an entire organization). Architecture and infrastructure must be maintained in a self-validating way.

4. **Pattern-aligned** — Aligns with Semantic Operations principles via adopted patterns: primarily Domain-Driven Design + AI/Agentic-first (Explicit Enterprise) capabilities.

### Stakeholders

| Stakeholder | Expectations |
| ----------- | ----------- |
| **Tim Mitchell** (creator) | Career growth in AI/agentic space; framework may have standalone value; "calling card" for next career move; potential basis for consulting or company |
| **Potential employers** | Demonstrates deep AI integration expertise with real architecture, not just skills |
| **Potential collaborators** | Ex-colleagues and friends who share interest — architecture must be legible enough for onboarding |
| **Potential consulting clients** | Believe Semantic Operations can help them — framework must be extractable and applicable |
| **Potential investors** | If this becomes a company, this is the technical foundation |
| **Public / practitioners** | Content shared via LinkedIn, blogs, semops.ai — ideas should be adoptable by others |

### Constraints

1. **Extractable bounded contexts** — Any bounded context should be extractable as a distinct project/product that still follows SemOps. Examples: "resume builder" from semops-publisher, a CPG domain modeled in Semantic Operations.

2. **Infrastructure-agnostic scaling** — Architect so infrastructure "doesn't really matter" (Scale Projection). VS Code + Claude Code + multi-repo today, but massive scale-up should be obvious and predictable. This may lead to decisions unusual for the current problem size — "Overkill is Underrated" for architecture and data.

3. **Adaptive, but explicit** — We adapt to discoveries. If strict DDD doesn't fit, we change direction, but the change is explicit (ADRs, session notes, UL updates).

---

## 2. Context and Scope

### Business Context

| Actor / System | Role | Interaction |
| -------------- | ---- | ----------- |
| **Tim Mitchell** | Human operator | Drives all repos via AI agents; makes architectural decisions; creates content |
| **AI Agents** | Autonomous workers | Execute work across repos; consume architecture docs, MCP, knowledge base for context. Currently Claude Code; will expand to other agent types. |
| **Public audience** | Consumers | Reads content via timjmitchell.com, semops.ai, LinkedIn, GitHub public repos |
| **GitHub** | Platform | Issue tracking, project management, CI/CD, code hosting, collaboration |
| **Fastmail** | Communications | Email/calendar ingestion for Inbox and Financial Pipeline |
| **Vercel** | Hosting | Production deployment for public sites |
| **Supabase Cloud** | Hosted DB | Production database for public sites |

### Technical Context — System Boundary

The system has three concentric rings:

**SemOps Core** (the product) — always present regardless of domain:

- semops-dx-orchestrator — strategic planner, architecture governance
- semops-core — schema, knowledge base, infrastructure services
- semops-data — coherence scoring, analytics, research

**Domain Applications** (pluggable, replaceable) — bounded context repos that consume SemOps Core:

- **Content** (1st party): semops-publisher, semops-docs, semops-sites — digital publishing, our own domain application
- **Operations** (1st party): semops-backoffice — agentic enterprise, replaces SaaS tools
- **External domains** (independent): e.g., music-sites — use the same architecture/infrastructure but are separate bounded contexts with their own domain logic

**Outside** — external services and audiences:

- GitHub API, Fastmail, Vercel, Supabase Cloud
- Public websites (deployed output)
- Public repos (semops-ai GitHub org)
- AI agent runtimes

**Key boundary rule:** SemOps Core never depends on a domain application. Domain applications depend on Core. This is what makes them extractable (Constraint #1) — you can remove semops-publisher and add a CPG domain without changing the core.

**Domain application repos** follow the same architectural patterns (DDD, UL, pattern traceability) but own their domain logic independently. They connect to the shared knowledge base and infrastructure but don't need to coordinate with other domain applications.

---

## 3. Solution Strategy

Key architectural decisions that shape the system. Each links to a full ADR for rationale and consequences.

| Decision | Approach | ADR |
| -------- | -------- | --- |
| **Domain model** | Two co-equal aggregates (Pattern + Coherence Assessment); three-layer model: Pattern → Architecture → Content | semops-core ADR-0004, ADR-0009, ADR-0012 |
| **Repo structure** | Multi-repo by bounded context; each repo = agent role boundary, not a microservice | [ADR-0002](decisions/ADR-0002-system-landscape.md) (superseded — now this doc) |
| **Publishing workflow** | Three-tier: working (`*-pr`) → staging (`semops-*`) → public (`semops-ai/*`) | [ADR-0003](decisions/ADR-0003-three-tier-repo-workflow.md) |
| **Knowledge management** | Five corpus types (core_kb, deployment, published, research_ai, research_general); architecture-first ingestion sequence | [ADR-0005](decisions/ADR-0005-ingestion-strategy-corpus-architecture.md) |
| **Documentation structure** | Separate concerns: architecture (ownership), infrastructure (services), diagrams (visuals) | [ADR-0007](decisions/ADR-0007-global-documentation-restructure.md) |
| **Architecture sync** | Bidirectional: repo-local ↔ global via `.proposed.md` + promotion workflow | [ADR-0006](decisions/ADR-0006-architecture-infrastructure-sync-workflow.md) |
| **Scale projection** | Project architecture to scaled deployment, categorize gaps as Clean/Concerning/Blocking to validate domain coherence before scaling | [scale-projection](domain-patterns/scale-projection.md) |
| **Explicit enterprise** | Architecture → data → infrastructure (not the reverse). Open primitives (git, markdown, SQL, CLI) over vendor platforms; avoid enterprise abstraction layers; every tool choice serves agent-readiness and portability | `semops-docs` [explicit-enterprise.md](https://github.com/semops-ai/semops-docs/blob/main/docs/SEMOPS_DOCS/SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/explicit-enterprise.md) |

**Guiding principle:** Adopt established 3P patterns (DDD, SKOS, DAM, PIM, PROV-O) rather than inventing. 1P innovation happens at the integration level — how these patterns compose under Semantic Operations.

> **Full ADR index:** [ADR_INDEX.md](ADR_INDEX.md)

---

## 4. Building Block View

The system is organized into three layers. Each repo owns capabilities and participates in cross-cutting subdomains.

**Layer 1 — SemOps Core** (domain-agnostic, reusable):

| Repo | Role | Key Capabilities |
| ---- | ---- | ---------------- |
| semops-dx-orchestrator | Platform/DX | Architecture governance, project specs, cross-repo coordination |
| semops-core | Schema/Infrastructure | Pattern schema, knowledge base, MCP Server, Query API, infrastructure services |
| semops-data | Analytics/MLOps | Research RAG, coherence scoring, data lineage |

**Layer 2 — Domain Applications** (pluggable, replaceable):

| Repo | Role | Key Capabilities |
| ---- | ---- | ---------------- |
| semops-publisher | Content | Content authoring, MDX publishing, media generation |
| semops-docs | Documents | Theory documentation, pattern rendering |
| semops-sites | Frontend | Public websites, design system assets |
| semops-backoffice | Operations | Inbox, Financial Pipeline, Voice Control |

**Subdomain participation** — repos are agent role boundaries, not bounded contexts. A single repo may participate in multiple subdomains:

| Subdomain | Type | Repos |
| --------- | ---- | ----- |
| Semantic Operations | Core | semops-core, semops-data |
| Knowledge Management | Core | semops-core, semops-data, semops-publisher |
| Content Publishing | Supporting | semops-publisher, semops-sites, semops-docs |
| Platform/Process | Supporting | semops-dx-orchestrator |
| Agentic Operations | Generic | semops-backoffice |

> **Detailed repo descriptions:** [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md)
> **Structured registry:** [REPOS.yaml](REPOS.yaml)
> **Visual:** [DIAGRAMS.md — System Context](DIAGRAMS.md#system-context), [DDD Subdomain Classification](DIAGRAMS.md#ddd-subdomain-classification)

---

## 5. Runtime View

Key interaction flows that span multiple repos and services.

### Agent Workflow

AI agents are the primary execution mechanism. Each repo is an agent role boundary — an agent working in semops-publisher has that repo's CLAUDE.md context and operates within Content domain rules.

```text
Tim (human operator)
  → Claude Code (in any repo)
    → reads CLAUDE.md, architecture docs, MCP knowledge base
    → executes within repo's bounded context
    → creates issues/PRs, updates session notes
    → may trigger cross-repo coordination via semops-dx-orchestrator issues
```

Cross-repo work uses **issues as contracts**: a parent issue in semops-dx-orchestrator coordinates child issues in target repos. Each repo's agent works independently on its child issue.

### Publishing Pipeline

Content flows through three tiers ([ADR-0003](decisions/ADR-0003-three-tier-repo-workflow.md)):

```text
Working (*-pr repos)     → private, active development
Staging (semops-* repos) → review-ready, public GitHub org
Public (semops-ai/*)     → deployed to Vercel, visible to audience
```

semops-publisher authors content → semops-sites renders and deploys → Vercel serves publicly.

### Knowledge Ingestion

Architecture-first ingestion sequence ([ADR-0005](decisions/ADR-0005-ingestion-strategy-corpus-architecture.md)):

```text
External sources (docs, research, APIs)
  → semops-data (collection, manifests)
    → semops-core (semantic processing, classification, RAG indexing)
      → Qdrant (vector search) + PostgreSQL (relational) + Neo4j (graph)
        → MCP Server + Query API (agent-accessible knowledge base)
```

Five corpus types: core_kb (architecture), deployment (infra/config), published (our content), research_ai (AI/ML research), research_general (non-AI research).

> **Visual:** [DIAGRAMS.md — Data Flows](DIAGRAMS.md#data-flows)

---

## 6. Deployment View

Infrastructure is centralized in semops-core with repo-specific services run on demand.

### Infrastructure Topology

| Environment | Host | Services |
| ----------- | ---- | -------- |
| **Core services** | semops-core Docker Compose | PostgreSQL :5434, Qdrant :6333, Neo4j :7474, Ollama :11434, n8n :5678, Docling :5001 |
| **Knowledge APIs** | semops-core (Python) | MCP Server (stdio), Query API :8101 |
| **Public sites** | Vercel (production), semops-sites :3100/:3101 (local) | timjmitchell.com, semops.ai |
| **Production DB** | Supabase Cloud | PostgreSQL for public site data |
| **Data science** | semops-data DevContainer | Jupyter :8888, GPU support |
| **Media generation** | semops-publisher | ComfyUI :8188 |

### Key Infrastructure Decisions

- **All services Docker-based** — portable, reproducible, infrastructure-agnostic (Constraint #2)
- **Single `start_services.py` entry point** — one command spins up all core services
- **Port registry** — centralized in [PORTS.md](PORTS.md) to prevent conflicts
- **No cloud lock-in for core** — Supabase Cloud is for public sites only; core services run locally

> **Full details:** [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md)
> **Port registry:** [PORTS.md](PORTS.md)
> **Visual:** [DIAGRAMS.md — Infrastructure Services](DIAGRAMS.md#infrastructure-services)

---

## 7. Crosscutting Concepts

### Domain Model

The domain model has two **co-equal core aggregates** that form the Semantic Optimization Loop ([semops ADR-0012](https://github.com/semops-ai/semops-core/blob/main/docs/decisions/ADR-0012-pattern-coherence-co-equal-aggregates.md)):

- **Pattern** (prescriptive) — what we should look like. SKOS taxonomy with provenance tiers (1P/2P/3P) and adoption lineage.
- **Coherence Assessment** (evaluative/directive) — how well reality matches intent. Audits Pattern → Capability → Script chain. **Pattern pushes, Coherence aligns.**

The model has three layers:

- **Pattern** (Core Domain, SKOS) — stable semantic concepts: 3P standards, 1P concepts, integration patterns
- **Architecture** (Strategic Design) — capabilities (WHAT), repositories (WHERE), integration (HOW)
- **Content** (DAM Publishing) — publishing artifacts: blog posts, articles, media

> **Schema:** `semops-core/schemas/UBIQUITOUS_LANGUAGE.md`
> **Aggregates & building blocks:** `semops-core/docs/STRATEGIC_DDD.md`
> **Visual:** [DIAGRAMS.md — DDD Domain Model](DIAGRAMS.md#ddd-domain-model)

### Innovation Index (1P / 3P)

The project distinguishes between first-party innovations and third-party foundations:

- **1P (first-party)** — novel concepts coined by this project (e.g., semantic coherence, explicit architecture, pattern operations)
- **3P (third-party)** — established patterns we adopt (e.g., DDD, SKOS, DAM, PIM, PROV-O, Arc42)

Every capability must trace to at least one pattern. 1P innovation happens at the composition level — how 3P patterns integrate under Semantic Operations.

> **1P concepts:** `semops-docs/docs/Publicv1_Candidates/SEMOPS_FRAMEWORK/`
> **Pattern registry:** [pattern_v1.yaml](../schemas/pattern_v1.yaml)

### Capability Traceability

The system enforces a full traceability chain from stable meaning to executable code:

```text
Pattern → Capability → Script
(why)      (what)       (where it runs)
```

- **Pattern → Capability** — every capability must trace to at least one pattern. Gaps indicate missing patterns or unjustified capabilities. Tracked in `semops-core/docs/STRATEGIC_DDD.md`.
- **Capability → Script** — capabilities decompose into small, focused scripts. Each script is a bounded piece of executable functionality that can be identified, replaced, and audited independently (anti-monolith). Tracked in per-repo `ARCHITECTURE.md` "Key Components > Scripts" tables.
- **Lineage** — git provides the change history for scripts. Comments and docstrings provide intent. No separate registry is needed at current scale.

This chain is a measurable coherence signal: a script without capability traceability is unjustified code; a capability without a script is unimplemented intent.

> **Capability registry:** `semops-core/docs/STRATEGIC_DDD.md`
> **Per-repo script inventory:** Each repo's `ARCHITECTURE.md` § Key Components > Scripts
> **Audit:** `/arch-sync` Step 5 (per-repo), Step 6b governance check, `/global-arch-sync` Step 4e (cross-repo)

### Agent Governance Model

Artifact lifecycle governance follows a 5-stage universal model (`draft → active → stable → deprecated → archived`) with two governance modes: self-correcting andon cord (internal operations) and hard gates (public boundary). Lifecycle evidence is entity-type-specific — capabilities activate via edge coverage (pattern + repo links), content activates via source config routing.

The governance model is operationalized through two audit layers: Layer 1 (state inspection via coverage views and `/arch-sync`) and Layer 2 (content verification via `ingest_domain_patterns.py --audit` and crosswalk audits). The path from manual (L1) to autonomous (L5) governance is defined by Scale Projection levels.

> **Spec:** `semops-core/schemas/GOVERNANCE_MODEL.md`
> **ADR:** [semops ADR-0011](https://github.com/semops-ai/semops-core/blob/main/docs/decisions/ADR-0011-agent-governance-model.md)

### Agent Permissions Model

Claude Code permissions follow a three-tier hierarchy — global settings apply to all repos, repo-committed settings define project conventions, repo-local settings capture per-user state:

| Tier | File | Scope | In Git? |
| ---- | ---- | ----- | ------- |
| **Global** | `~/.claude/settings.json` | All repos, all sessions | No (user home) |
| **Repo committed** | `<repo>/.claude/settings.json` | Anyone working in this repo | Yes |
| **Repo local** | `<repo>/.claude/settings.local.json` | One user, one repo | No (gitignored) |

**Design decisions:**

- **Global allows all tools** — `"Bash"`, `"Read"`, `"Write"`, `"Edit"`, `"Glob"`, `"Grep"`, `"WebFetch"`, `"WebSearch"`, `"Task"` are all auto-approved globally. This means repo-level settings don't need to enumerate bash commands.
- **Deny list for secrets** — Global deny blocks `curl`/`wget` with `*secret*`, `*password*`, `*token*` patterns to prevent accidental exfiltration.
- **Repo-local files stay empty** — When Claude Code prompts for individual command approval, those approvals accumulate in `settings.local.json`. These should be periodically cleared to prevent bloat and avoid storing sensitive values (tokens, passwords) as permission strings.

**Key insight:** Authentication (GITHUB_TOKEN) is managed via `~/.bashrc`, which the shell environment inherits automatically. Agents never need explicit token export commands.

> **File locations and current config:** [GLOBAL_INFRASTRUCTURE.md — Claude Code Configuration](GLOBAL_INFRASTRUCTURE.md#claude-code-configuration)

### Semantic Coherence Measurement

The system's core KPI — **SC = (Availability × Consistency × Stability)^(1/3)** — measures how well implementation aligns with domain intent:

- **Availability** — are patterns implemented where expected?
- **Consistency** — do implementations match schema definitions?
- **Stability** — how often do patterns change unexpectedly?

Currently aspirational; Project 18 (@Semantic_Coherence_Measurement) will operationalize this with MLflow-tracked scoring and PR-level coherence checks.

> **Theory:** `semops-docs/docs/Publicv1_Candidates/SEMOPS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence-measurement.md`
> **Project spec:** [PROJECT-18-semantic-coherence-measurement.md](project-specs/PROJECT-18-semantic-coherence-measurement.md)

---

## 8. Risks and Technical Debt

| Risk / Debt | Severity | Mitigation |
| ----------- | -------- | ---------- |
| **Single-operator dependency** — all knowledge is in one person's head | High | Architecture docs, CLAUDE.md per repo, UL, session notes — make the system legible to agents and future collaborators |
| **Documentation staleness** — docs drift from implementation | High | Arc42 anti-staleness (this doc); structured data (REPOS.yaml, UBIQUITOUS_LANGUAGE.md); audit scripts; semantic coherence measurement (Project 18) |
| **Schema still in progress** — semops-core schema not finalized | Medium | Three-layer model decided (ADR-0009); implementation phased via Projects 13/18; capabilities work without schema completion |
| **Infrastructure complexity** — many services for a one-person project | Medium | Docker Compose single entry point; "Overkill is Underrated" — invest now for scale readiness (Constraint #2) |
| **Cross-repo coordination overhead** — 8 repos with interdependencies | Medium | semops-dx-orchestrator as coordinator; project specs define scope; issues as contracts pattern for cross-repo work |
| **No CI/CD yet** — validation is manual | Low | Planned via semantic coherence PR checks (Project 18); current scale makes manual validation acceptable |

---

## 9. Document Index

<!-- Where to find what — the routing guide -->

| For Questions About | Look In |
| ------------------- | ------- |
| **Domain terms and schema** | `semops-core/schemas/UBIQUITOUS_LANGUAGE.md` |
| **System architecture (repos, capabilities)** | [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) |
| **Infrastructure (services, ports)** | [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) |
| **Visual diagrams** | [DIAGRAMS.md](DIAGRAMS.md) |
| **Diagram styling conventions** | [DIAGRAM_STYLE_GUIDE.md](DIAGRAM_STYLE_GUIDE.md) |
| **Strategic DDD capabilities** | `semops-core/docs/STRATEGIC_DDD.md` |
| **Theory / 1P concepts** | `semops-docs/docs/Publicv1_Candidates/SEMOPS_FRAMEWORK/` |
| **Domain patterns (process/implementation)** | [domain-patterns/](domain-patterns/) + [pattern_v1.yaml](../schemas/pattern_v1.yaml) |
| **Architecture decisions** | [ADR_INDEX.md](ADR_INDEX.md) |
| **Project scope definitions** | [project-specs/](project-specs/) |
| **Port registry** | [PORTS.md](PORTS.md) |
| **Repo registry (structured)** | [REPOS.yaml](REPOS.yaml) |

---

## Related

- [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) — detailed repo descriptions and ownership
- [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) — services, ports, Docker, environment
- [DIAGRAMS.md](DIAGRAMS.md) — 8 Mermaid diagrams (System Context through DDD)
- `semops-core/schemas/UBIQUITOUS_LANGUAGE.md` — domain term definitions
