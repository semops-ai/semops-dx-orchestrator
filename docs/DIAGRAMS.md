# SemOps System Diagrams

Visual representations of the SemOps architecture and data flows.

**Version:** 1.5.0
**Last Updated:** 2026-02-19

> **See Also:** [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) (ownership/relationships) | [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) (services/ports)

> **Style Guide:** [DIAGRAM_STYLE_GUIDE.md](DIAGRAM_STYLE_GUIDE.md) — palette, theme block, labeling rules. Derived from semops.ai site tokens.

---

## System Context

High-level view of repos and their relationships.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'edgeLabelBackground': '#EDF3F0', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph semopscore["SemOps Core"]
 dx["semops-dx-orchestrator<br/><i>Platform/DX</i><br/><small>Process docs, global architecture<br/>Cross-repo coordination</small>"]
 hub["semops-core<br/><i>Schema/Infrastructure</i><br/><small>MCP Server, Query API<br/>PostgreSQL, Qdrant, Neo4j</small>"]
 data["semops-data<br/><i>Analytics/MLOps</i><br/><small>Research RAG<br/>Coherence scoring</small>"]
 end

 subgraph content["Content"]
 pub["semops-publisher<br/><i>Content</i><br/><small>Content authoring<br/>MDX publishing</small>"]
 docs["semops-docs<br/><i>Documents</i><br/><small>Theory docs<br/>ADRs</small>"]
 sites["semops-sites<br/><i>Frontend</i><br/><small>timjmitchell.com :3100<br/>semops.ai :3101</small>"]
 end

 subgraph operations["Operations"]
 back["semops-backoffice<br/><i>Operations</i><br/><small>Attention Stream<br/>Financial Pipeline</small>"]
 end

 dx -->|coordinates| hub
 dx -->|coordinates| back
 hub -->|"provides<br/>services"| pub
 hub -->|"provides<br/>services"| docs
 hub -->|"provides<br/>services"| data
 hub -->|"provides<br/>data"| sites
 hub -->|"provides<br/>PostgreSQL"| back
 pub -->|"publishes<br/>MDX"| sites
 data -->|"uses<br/>Qdrant/Docling"| hub
```

---

## Data Flows

How data moves through the system.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'edgeLabelBackground': '#EDF3F0', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph external["External Sources"]
 web["Web Content<br/>URLs, APIs"]
 email["Fastmail<br/>IMAP, CalDAV, SMTP"]
 gh["GitHub<br/>Projects, Issues"]
 end

 subgraph semops["semops-core"]
 docling["Docling<br/>:5001"]
 n8n["n8n<br/>:5678"]
 ingest["Ingestion<br/>Scripts"]
 pg["PostgreSQL<br/>:5434"]
 neo["Neo4j<br/>:7474/:7687"]
 qdrant["Qdrant<br/>:6333/:6334"]
 mcp["MCP Server"]
 query["Query API<br/>:8101"]
 end

 subgraph operations["semops-backoffice"]
 attention["Attention<br/>Stream"]
 finance["Financial<br/>Pipeline"]
 status["Status<br/>Reports"]
 end

 subgraph content["Content"]
 pub["semops-publisher<br/>Patterns → posts"]
 docs["semops-docs<br/>Patterns → theory docs"]
 sites["semops-sites<br/>:3100/:3101"]
 end

 data["semops-data<br/>Research RAG<br/>Coherence scoring"]
 agents["AI Agents<br/>Claude Code"]

 %% External to semops-hub
 web --> docling
 web --> ingest

 %% semops-hub internal
 docling --> pg
 n8n --> pg
 ingest --> pg
 pg --> neo
 pg --> qdrant
 neo --> query
 pg --> query
 pg --> mcp

 %% Operations flows
 email --> attention
 email --> finance
 gh --> attention
 gh --> status
 finance --> pg
 attention --> pg
 status --> email

 %% Content access
 query --> pub
 query --> sites
 mcp --> docs
 pub --> sites

 %% SemOps Core access
 qdrant --> data
 docling --> data
 mcp --> agents
```

---

## Infrastructure Services

Service dependencies and ports.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart LR
 subgraph hub["semops-core (Docker Compose)"]
 direction TB
 supabase["Supabase Studio<br/>:8000"]
 postgres["PostgreSQL<br/>:5434"]
 postgrest["PostgREST<br/>(internal)"]
 n8n2["n8n<br/>:5678"]
 qdrant2["Qdrant<br/>:6333 REST<br/>:6334 gRPC"]
 ollama["Ollama<br/>:11434"]
 docling2["Docling<br/>:5001"]
 neo2["Neo4j<br/>:7474 HTTP<br/>:7687 Bolt"]
 queryapi["Query API<br/>:8101"]
 end

 subgraph sites["semops-sites (Vercel)"]
 direction TB
 tim["timjmitchell.com<br/>:3100 (dev)"]
 semops["semops.ai<br/>:3101 (dev)"]
 supa["Supabase Cloud"]
 end

 subgraph back["semops-backoffice"]
 direction TB
 imap["Fastmail IMAP"]
 caldav2["Fastmail CalDAV"]
 ghapi["GitHub API"]
 end

 subgraph datarepo["semops-data (DevContainer)"]
 direction TB
 jupyter["Jupyter Lab<br/>:8888"]
 mlflow["MLflow<br/>(local)"]
 end

 subgraph pubrepo["semops-publisher"]
 direction TB
 comfy["ComfyUI<br/>:8188"]
 end

 %% Dependencies
 sites --> postgres
 back --> postgres
 datarepo --> postgres
 datarepo --> qdrant2
 datarepo --> docling2
 datarepo --> ollama
 pubrepo --> postgres
 pubrepo --> qdrant2
```

---

## Attention Stream Flow

Backoffice-pr unified attention view.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph sources["Sources"]
 email2["Email<br/>task@semops.ai"]
 ghproj["GitHub Projects<br/>target_date items"]
 manual["Manual CLI<br/>add command"]
 end

 subgraph attention2["Attention Service"]
 poll["poll-email"]
 query2["GitHub query"]
 tracker["tracker-pr<br/>Issues"]
 unified["Unified View"]
 end

 subgraph outputs["Outputs"]
 calendar["Fastmail Calendar<br/>CalDAV"]
 dashboard["tracker-pr<br/>README.md"]
 cli["CLI list"]
 end

 email2 --> poll
 poll --> tracker
 ghproj --> query2
 manual --> tracker
 tracker --> unified
 query2 --> unified
 unified --> calendar
 unified --> dashboard
 unified --> cli
```

---

## Financial Pipeline Flow

Backoffice-pr email-to-beancount automation.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph fastmail["Fastmail (IMAP)"]
 bofa["finances/bofa/<br/>Transaction alerts"]
 invoices["finances/invoices/<br/>Vendor invoices"]
 end

 subgraph pipeline["Financial Pipeline"]
 classifier["Classifier<br/>bofa_alert | invoice_pdf | invoice_html"]
 bofaparser["BofA Parser"]
 invoiceproc["Invoice Processor<br/>Docling PDF"]
 end

 subgraph storage["PostgreSQL"]
 raw["raw_emails<br/>Source of truth"]
 trans["transactions<br/>Parsed alerts"]
 inv["invoices<br/>Parsed invoices"]
 patterns["vendor_patterns<br/>Auto-categorization"]
 end

 subgraph output["Outputs"]
 agent["Agent Queries<br/>SQL"]
 beancount["Beancount Export<br/>Monthly"]
 recon["CSV Reconciliation"]
 end

 bofa --> classifier
 invoices --> classifier
 classifier --> bofaparser
 classifier --> invoiceproc
 bofaparser --> raw
 invoiceproc --> raw
 raw --> trans
 raw --> inv
 patterns --> trans
 patterns --> inv
 trans --> agent
 trans --> beancount
 inv --> beancount
 beancount --> recon
```

---

## DDD Context Map

Integration patterns between repos. Note: repos are agent role boundaries, not bounded contexts (see [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md#repos-as-agent-role-boundaries)). This diagram shows how repos access the shared domain model.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'edgeLabelBackground': '#EDF3F0', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph coordination["Coordination Context"]
 dx["semops-dx-orchestrator<br/><i>Published Language</i><br/>Architecture governance,<br/>process docs, ADR templates"]
 end

 subgraph core["Core Domain Context"]
 hub["semops-core<br/><i>Shared Kernel</i><br/>Pattern aggregate root<br/>UBIQUITOUS_LANGUAGE.md<br/>Schema, MCP, Query API"]
 data["semops-data<br/><i>Shared Kernel + Customer-Supplier</i><br/>Coherence scoring, lineage<br/>Shares domain model, consumes services"]
 end

 subgraph supporting["Supporting Contexts"]
 pub["semops-publisher<br/><i>Conformist</i><br/>Follows semops schema<br/>for patterns/entities"]
 docsr["semops-docs<br/><i>Conformist</i><br/>References patterns<br/>Theory documentation"]
 end

 subgraph generic["Generic Contexts"]
 sites["semops-sites<br/><i>Customer-Supplier</i><br/>Consumes Supabase data<br/>Own frontend domain"]
 back["semops-backoffice<br/><i>Anti-Corruption Layer</i><br/>Translates shared PostgreSQL<br/>to own financial domain"]
 end

 hub -.->|"Shared Kernel<br/>(schema, UL) +<br/>Customer-Supplier<br/>(Qdrant, Docling)"| data
 hub -.->|"Shared Kernel<br/>(schema, UL)"| pub
 hub -.->|"Shared Kernel<br/>(schema, UL)"| docsr
 hub -->|"U/D Supplier<br/>(site data)"| sites
 hub -->|"ACL<br/>(financial domain)"| back
 dx -.->|coordinates| hub
 dx -.->|coordinates| pub
 dx -.->|coordinates| data
 pub -->|"Customer-Supplier<br/>(MDX, fonts, PDF)"| sites
```

**Integration Pattern Key:**

- **Published Language** - Context defines a well-documented shared model for others to consume (e.g., global architecture, process docs, ADR templates)
- **Shared Kernel** - Both sides depend on same schema (UBIQUITOUS_LANGUAGE.md)
- **Customer-Supplier (U/D)** - Upstream provides services, downstream consumes
- **Conformist** - Downstream adopts upstream model as-is
- **ACL (Anti-Corruption Layer)** - Downstream translates upstream model to protect own domain (e.g., data governance)

---

## DDD Domain Model

Two co-equal core aggregates (Pattern + Coherence Assessment) forming the Semantic Optimization Loop (ADR-0012), with three architectural layers. Source: `semops-core/schemas/UBIQUITOUS_LANGUAGE.md` (v8.0.0) + `phase2-schema.sql` + `semops-core/docs/STRATEGIC_DDD.md`. All non-pattern entities share a single `entity` table with `entity_type` discriminator (`content`, `capability`, `repository`).

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'edgeLabelBackground': '#EDF3F0', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph coreAggregates["Core Aggregates (Semantic Optimization Loop)"]
 subgraph aggregate["Pattern Aggregate (Prescriptive)"]
 pattern["Pattern<br/><i>Aggregate Root</i><br/><small>Concept taxonomy (SKOS)<br/>Provenance (1P/3P)</small>"]
 pedge["Pattern Edge<br/><i>SKOS + Adoption</i><br/><small>broader, narrower, related<br/>adopts, extends, modifies</small>"]
 end

 subgraph coherence["Coherence Assessment Aggregate (Evaluative)"]
 assessment["Coherence Assessment<br/><i>Aggregate Root</i><br/><small>Measures gap between<br/>intent and reality</small>"]
 end
 end

 subgraph architecture["Architecture Layer (Strategic Design)"]
 capability["Capability<br/><i>entity_type: capability</i><br/><small>What the system can do<br/>Links to implementing patterns</small>"]
 repository["Repository<br/><i>entity_type: repository</i><br/><small>Where capabilities live<br/>Agent role boundaries</small>"]
 end

 subgraph content["Content Layer (DAM + PIM)"]
 entity["Entity<br/><i>entity_type: content</i><br/><small>Publishing artifacts<br/>Classified or unclassified</small>"]
 edge["Edge<br/><i>W3C PROV-O</i><br/><small>derived_from, cites, version_of</small>"]
 surface["Surface<br/><small>Publishing destination<br/>semops.ai, LinkedIn, etc.</small>"]
 delivery["Delivery<br/><small>Surface + status<br/>Approval workflow</small>"]
 brand["Brand / Product<br/><i>PIM bolt-on</i><br/><small>Property hierarchy<br/>domains, products</small>"]
 end

 pattern --> pedge
 pattern -->|"classifies"| entity
 pattern -->|"implemented by"| capability
 assessment -->|"audits"| capability
 assessment -.->|"informs"| pattern
 capability -->|"delivered by"| repository
 entity --> edge
 entity -->|"published via"| delivery
 delivery --> surface
 brand -->|"owns"| surface
```

**Key model changes (v8.0.0 + ADR-0012):**

- **Two co-equal core aggregates** — Pattern (prescriptive) + Coherence Assessment (evaluative/directive), forming the Semantic Optimization Loop: Pattern pushes, Coherence aligns
- **Single entity table** with `entity_type` discriminator — not separate tables per layer
- **Architecture layer is new** — Capabilities (WHAT), Repositories (WHERE). Previously these were implicit.
- **Pattern types reduced** to `concept` and `domain` only — removed `architecture`, `topology`, `bounded-context`, `data-flow`, `system-landscape`
- **Subdomains are emergent** from capability→pattern tracing, not stored
- **Data flows are emergent** from shared capability participation, not explicitly modeled

**Bolt-on patterns (adopted 3P standards):**

- **DAM** (Digital Asset Management) — Content artifacts, surfaces, delivery workflow
- **PIM** (Product Information Management) — Brand/product hierarchy, property domains. Thin now but architecturally present for packaging and product relationships.
- **SKOS** — Pattern taxonomy (broader/narrower/related)
- **PROV-O** — Content provenance edges (derived_from, cites, version_of)

**Stable Core vs Flexible Edge:**

- **Stable Core** — Entities classified by a Pattern (the "classifies" edge exists)
- **Flexible Edge** — Unclassified entities awaiting pattern assignment
- **Promotion** — Assigning a pattern; **Rejection** — Deletion/archival

---

## DDD Subdomain Classification

Subdomains are **emergent from capability→pattern tracing**, not explicitly modeled or stored. They are cross-cutting capabilities that span multiple repos. Repos are **agent role boundaries** that participate in multiple subdomains. See [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md#repos-as-agent-role-boundaries) for the full rationale.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'edgeLabelBackground': '#EDF3F0', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
flowchart TB
 subgraph shared["Three-Layer Domain Model"]
 direction LR
 patternlayer["Pattern Layer<br/><small>Stable semantic core</small>"]
 archlayer["Architecture Layer<br/><small>Capabilities, repos, integration</small>"]
 contentlayer["Content Layer<br/><small>Publishing artifacts, delivery</small>"]
 end

 subgraph subdomains["Subdomains (emergent from capability→pattern tracing)"]
 direction TB
 semops["Semantic Operations · CORE<br/><i>Pattern model, knowledge graph,<br/>corpus routing, coherence measurement</i><br/><small>Layers: Pattern, Architecture</small>"]
 knowledge["Knowledge Management · CORE<br/><i>Ingestion, classification,<br/>retrieval, provenance</i><br/><small>Layers: All three</small>"]
 publishing["Content Publishing · SUPPORTING<br/><i>Multi-surface creation,<br/>MDX pipeline, style guides</i><br/><small>Layers: Content</small>"]
 platform["Platform/Process · SUPPORTING<br/><i>Coordination, architecture<br/>governance, sync workflows</i><br/><small>Layers: Architecture</small>"]
 ops["Agentic Operations · GENERIC<br/><i>Attention stream, financial pipeline,<br/>status reports, voice control</i><br/><small>Layers: Content</small>"]
 end

 subgraph repos["Repos (agent role boundaries)"]
 direction TB
 hub3["semops-core"]
 data3["semops-data"]
 pub3["semops-publisher"]
 sites3["semops-sites"]
 docs3["semops-docs"]
 dx3["semops-dx-orchestrator"]
 back3["semops-backoffice"]
 end

 shared --- subdomains
 subdomains --- repos

 semops -.- hub3
 semops -.- data3
 knowledge -.- hub3
 knowledge -.- data3
 knowledge -.- pub3
 publishing -.- pub3
 publishing -.- sites3
 publishing -.- docs3
 platform -.- dx3
 ops -.- back3
```

**Key insight:** Subdomains are not modeled entities — they are emergent from capability→pattern tracing (query, don't store). Repos are agent-scoped workspaces that participate in one or more subdomains.

**Subdomain → Layer → Repo Participation:**

| Subdomain | Type | Primary Layer(s) | Repos | What It Covers |
|-----------|------|------------------|-------|--------------- |
| **Semantic Operations** | Core | Pattern, Architecture | semops-core, semops-data | Pattern model, knowledge graph, coherence scoring, corpus routing |
| **Knowledge Management** | Core | All three | semops-core, semops-data, semops-publisher | Ingestion, classification, retrieval, episode provenance, Research RAG |
| **Content Publishing** | Supporting | Content | semops-publisher, semops-sites, semops-docs | Multi-surface publishing, MDX pipeline, resume composition, theory docs |
| **Platform/Process** | Supporting | Architecture | semops-dx-orchestrator | Architecture governance, sync workflows, coordination |
| **Agentic Operations** | Generic | Content | semops-backoffice | Attention stream, financial pipeline, status reports, voice control |

**Why repos ≠ subdomains:**

- semops-data's coherence scoring is part of the **Semantic Operations** core domain, not a separate subdomain
- semops-publisher's pattern-based content creation participates in both **Knowledge Management** (reads patterns) and **Content Publishing** (creates deliveries)
- The shared knowledge base (PostgreSQL, Qdrant, Neo4j) is queryable by any repo or any future service on the Docker network

**Scale Projection:** The shared data layer means any new container/service can join the system by connecting to the same infrastructure. Repos are deployment-flexible — the architecture doesn't change when the deployment topology changes.

**Corpus Routing** (within Core Domain):

| Corpus | Schema Integration | Retention | Purpose |
|--------|-------------------|-----------|---------|
| `core_kb` | Full (Pattern + Entity + Edge) | Permanent | Curated knowledge |
| `deployment` | Entity + Edge | Permanent | Operational artifacts |
| `published` | Full + Delivery | Permanent | Published content |
| `research_*` | Entity only | Project-scoped | External research |
| `ephemeral_*` | Vectors only | Temporary | Experiments |

---

## Related Documentation

- [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) - System landscape, ownership
- [GLOBAL_INFRASTRUCTURE.md](GLOBAL_INFRASTRUCTURE.md) - Services, ports, environment
- [PORTS.md](PORTS.md) - Detailed port registry
- `semops-core/schemas/UBIQUITOUS_LANGUAGE.md` - Domain term definitions (v8.0.0)
- `semops-core/schemas/phase2-schema.sql` - PostgreSQL schema
