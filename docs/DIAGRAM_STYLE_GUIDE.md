# Diagram Style Guide

Style conventions for Mermaid diagrams across the SemOps system.

**Source:** Design tokens derived from `semops-sites/apps/semops/` (globals.css, tailwind config, mermaid-diagram.tsx).

> **Applies to:** [DIAGRAMS.md](DIAGRAMS.md) and any Mermaid diagrams in project docs.

---

## Diagram Framework

Diagrams follow the **C4 model** (Simon Brown) for structural views, with DDD-specific diagrams for domain modeling.

### C4 Levels

| Level | Name | Shows | Label vocabulary |
| ----- | ---- | ----- | --------------- |
| **1** | System Context | System as black box + external actors/systems | Actor names, system names, high-level verbs ("uses", "manages") |
| **2** | Container | Applications, services, data stores inside the system | Deployment units, technology choices, protocols |
| **3** | Component | Components inside a container | Module names, responsibilities |
| **4** | Code | Classes, modules | Implementation detail |

### Diagram Classification

| Diagram | C4 Level | Label source |
| ------- | -------- | ----------- |
| System Context | **L2 Container** | GLOBAL_ARCHITECTURE.md — repo roles, layer names |
| Data Flows | **L2 Container** | GLOBAL_ARCHITECTURE.md + GLOBAL_INFRASTRUCTURE.md — services, ports |
| Infrastructure Services | **L2 Container** | GLOBAL_INFRASTRUCTURE.md — services, ports, deployment |
| Attention Stream Flow | **L3 Component** | semops-backoffice domain |
| Financial Pipeline Flow | **L3 Component** | semops-backoffice domain |
| DDD Context Map | **DDD** | UBIQUITOUS_LANGUAGE.md — integration patterns |
| DDD Domain Model | **DDD** | UBIQUITOUS_LANGUAGE.md — entities, edges, aggregates |
| DDD Subdomain Classification | **DDD** | UBIQUITOUS_LANGUAGE.md + GLOBAL_ARCHITECTURE.md — capabilities, subdomains |

### Label Source Rule

- **C4 diagrams** use terms from GLOBAL_ARCHITECTURE.md and GLOBAL_INFRASTRUCTURE.md
- **DDD diagrams** use terms from UBIQUITOUS_LANGUAGE.md exclusively
- When the same entity appears in both, use the vocabulary appropriate to the diagram type

---

## Color Palette

| Name | Hex | HSL | Role |
|------|-----|-----|------|
| **forest** | `#315E49` | `hsl(152 32% 28%)` | Primary — borders, strokes |
| **pine** | `#3D765B` | `hsl(152 32% 35%)` | Lines, edges |
| **mist** | `#EDF3F0` | `hsl(152 20% 94%)` | Light green fill — secondary/accent |
| **slate** | `#2E3138` | `hsl(220 10% 12%)` | Text foreground |
| **cloud** | `#F3F4F6` | `hsl(220 14% 96%)` | Node fill — default background |
| **ember** | `#813131` | `hsl(0 45% 35%)` | Dark red accent — emphasis |

---

## Theme Block

Copy this `init` block verbatim into every Mermaid diagram:

```
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#F3F4F6', 'primaryBorderColor': '#315E49', 'primaryTextColor': '#2E3138', 'lineColor': '#3D765B', 'secondaryColor': '#EDF3F0', 'tertiaryColor': '#F3F4F6', 'fontFamily': 'JetBrains Mono, monospace', 'fontSize': '14px'}, 'flowchart': {'curve': 'basis'}}}%%
```

**Theme variable → palette mapping:**

| Variable | Palette name | Hex |
|----------|-------------|-----|
| `primaryColor` | cloud | `#F3F4F6` |
| `primaryBorderColor` | forest | `#315E49` |
| `primaryTextColor` | slate | `#2E3138` |
| `lineColor` | pine | `#3D765B` |
| `secondaryColor` | mist | `#EDF3F0` |
| `tertiaryColor` | cloud | `#F3F4F6` |

---

## Accent Classes

Define these after the `init` block when needed:

```
classDef emphasis stroke:#813131,color:#813131
classDef accent fill:#EDF3F0,stroke:#315E49
```

Apply with `:::emphasis` or `:::accent` on nodes.

---

## Labeling Rules

### Edge Labels

- **DDD diagrams:** Use domain-level terms, never infrastructure names. Say "corpus data" not "Qdrant", "operational data" not "PostgreSQL".
- **C4 diagrams:** May reference services and protocols (ports, service names) since those are the vocabulary at that level.
- **Multi-word labels:** Use quoted syntax with `<br/>` line breaks: `|"word<br/>word"|`
- **Integration patterns on edges:** Spell out pattern names (e.g., "Anti-Corruption Layer" not "ACL")

### Node Labels

- **Name** (plain text) — repo or service name
- **Role** (`<i>`) — DDD pattern or architectural role
- **Details** (`<small>`) — 1-2 lines of context (C4 diagrams only)
- **`<b>` rejected** — JetBrains Mono weight difference too subtle to register

### Port Numbers

- **Private/internal services** should show ports (e.g., `:5434`, `:6333`)
- **Public/external services** should not (ports are deployment detail)

### Subgraphs

- Labels are quoted strings: `subgraph name["Display Label"]`
- IDs are lowercase, no spaces: `semopscore`, `coordination`

### Node IDs

- Short, lowercase, no hyphens: `dx`, `hub`, `pub`, `data`, `back`
- Consistent across diagrams — same repo gets same ID everywhere

---

## DDD Domain Model Conventions

Rules specific to the tactical DDD Domain Model diagram.

### Domain Expert Test

Only include concepts a domain expert would naturally use. If you need a developer to explain what it is, it's implementation detail and doesn't belong. Example: "Semantic Optimization" passes; "Ingestion Episode" fails.

### Node Label Density

- **Name + role only** — no field-level details (SQL columns, types, enums)
- Value objects documented in prose below the diagram, not in node labels

### Subgraph Labels — 3P Pattern References

Reference the adopted third-party pattern in subgraph labels:

| Subgraph | 3P Pattern |
| -------- | ---------- |
| Pattern Aggregate (SKOS) | Simple Knowledge Organization System |
| Content & Publishing (DAM) | Digital Asset Management |
| Product (PIM) | Product Information Management |

### Edge Conventions

- **Within-aggregate edges** — unlabeled (composition is implied)
- **Cross-aggregate edges** — labeled with domain verbs (e.g., `"classifies"`, `"tracks"`, `"published to"`)
- **Bidirectional edges** — use `<-->` for mutual/virtuous-loop relationships

---

## Related

- [DIAGRAMS.md](DIAGRAMS.md) — diagram content
- `semops-sites/apps/semops/src/app/globals.css` — site CSS variables
- `semops-sites/apps/semops/src/components/mermaid-diagram.tsx` — Mermaid renderer
