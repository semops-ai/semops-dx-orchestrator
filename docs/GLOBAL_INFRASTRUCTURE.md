# SemOps Global Infrastructure

How infrastructure connects across the SemOps ecosystem. For port assignments see [PORTS.md](PORTS.md). For operational details (start/stop, health checks, troubleshooting) see each repo's `docs/INFRASTRUCTURE.md`.

**Version:** 2.1.0
**Last Updated:** 2026-02-22

> **See Also:** [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) (ownership/relationships) | [DIAGRAMS.md](DIAGRAMS.md) (visual diagrams) | [PORTS.md](PORTS.md) (port registry)

---

## Infrastructure Ownership

Each repo owns specific infrastructure. Core services run always-on via Docker Compose in semops-core; everything else runs on demand.

| Repo | Owns | Type |
|------|------|------|
| **semops-core** | PostgreSQL, Qdrant, Neo4j, Docling, n8n, Ollama, Supabase, Query API, MCP Server | Always-on (Docker Compose) + on-demand (Python) |
| **semops-data** | Jupyter Lab, MLflow | On-demand (DevContainer) |
| **semops-publisher** | ComfyUI | On-demand |
| **semops-sites** | Next.js dev servers, Supabase local | On-demand |
| **semops-research** | *(no local services — consumes Ollama, Qdrant, Docling from semops-core)* | Consumer only |
| **semops-backoffice** | *(no local services — connects to external APIs)* | External only |

> **Port assignments:** [PORTS.md](PORTS.md) — the single source of truth for all port allocations and conflict resolution.

---

## How Repos Connect

All repos connect to semops-core's core services via `localhost` ports. There is no service mesh or API gateway — Docker exposes ports to the host, and scripts/agents connect directly.

```text
                    semops-core (Docker Compose)
                    ┌─────────────────────────────────┐
                    │  PostgreSQL :5434                │
                    │  Qdrant     :6333                │
                    │  Neo4j      :7474/:7687          │
                    │  Docling    :5001                │
                    │  Query API  :8101                │
                    │  MCP Server (stdio)              │
                    └──────────┬──────────────────────┘
                               │ localhost
            ┌──────────┬───────┼───────┬──────────┬──────────┐
            │          │       │       │          │          │
        semops-data   publisher  docs   sites    backoffice  research
        (Qdrant,  (MCP,     (MCP,  (Supa-   (PostgreSQL (Ollama,
         PG,      Query)    schema) base)    via :5434)  Qdrant,
         Docling)                                        Docling)
```

**Key connection conventions (established in [semops-core#124](https://github.com/semops-ai/semops-core/issues/124)):**

- **PostgreSQL:** Port 5434 (direct access). Port 5432 is Supavisor (Supabase internal — do not use for scripts/agents).
- **Env var prefix:** `SEMOPS_DB_*` for application config, `POSTGRES_*` for Supabase container config. These are different — see semops-core `.env.example`.
- **Connection module:** `semops-core/scripts/db_utils.py` — shared `get_db_connection()` with resolution order: `SEMOPS_DB_*` → `POSTGRES_*` → defaults.
- **MCP Server:** stdio subprocess managed by Claude Code. Registered in `~/.claude.json` (global) and `.mcp.json` (project-level).
- **Query API:** REST on :8101. Start manually: `uvicorn api.query:app --port 8101`.

---

## External Services

Services hosted externally, accessed via API:

| Service | Protocol | Used By | Purpose |
|---------|----------|---------|---------|
| **GitHub API** | REST/GraphQL | all repos (via `gh` CLI) | Issues, Projects, CI |
| **OpenAI** | REST | semops-core, semops-data, semops-research | Embeddings (semops/data), LLM fallback (research) |
| **Anthropic** | REST | semops-publisher, semops-data, semops-research | LLM synthesis |
| **Fastmail** | IMAP/CalDAV/SMTP | semops-backoffice | Email, calendar, status delivery |
| **Vercel** | HTTPS | semops-sites | Production hosting |
| **Supabase Cloud** | HTTPS | semops-sites | Production database |
| **Cloudflare** | HTTPS | semops-sites | CDN, DNS |

---

## Environment Variable Conventions

### Global (set in `~/.bashrc`)

| Variable | Used By | Purpose |
|----------|---------|---------|
| `GITHUB_TOKEN` | All repos | GitHub CLI auth (HTTPS, never SSH) |
| `ANTHROPIC_API_KEY` | semops-publisher, semops-core, semops-data, semops-research | Claude API |
| `OPENAI_API_KEY` | semops-data, semops-core, semops-research | Embeddings (semops/data), LLM fallback (research) |

### Per-Repo (set in `.env`, loaded from `.env.example`)

Each repo's `.env.example` documents required variables. Key conventions:

| Convention | Example | Purpose |
|------------|---------|---------|
| `SEMOPS_DB_*` | `SEMOPS_DB_HOST`, `SEMOPS_DB_PORT` | Application database config (scripts/agents) |
| `POSTGRES_*` | `POSTGRES_PASSWORD` | Supabase Docker container config |
| `SEMOPS_QDRANT_*` | `QDRANT_HOST`, `QDRANT_PORT` | Vector database config |

> **Full per-repo env var lists:** See each repo's `docs/INFRASTRUCTURE.md` or `.env.example`.

---

## Docker Networks

| Network | Services | Purpose |
|---------|----------|---------|
| `semops-network` | All semops-core Docker services | Internal container communication |

External repos connect via `localhost` ports, not the Docker network. Cross-compose networking (external `semops-infra` network) is deferred — localhost port mapping is sufficient at current scale.

---

## Claude Code Configuration

> **Architecture:** See [SYSTEM_LANDSCAPE.md — Agent Permissions Model](SYSTEM_LANDSCAPE.md#agent-permissions-model) for the permission hierarchy design and rationale.

### Configuration Files

| File | Purpose | In Git? |
| ---- | ------- | ------- |
| `~/.claude/CLAUDE.md` | Global instructions (auth, conventions, port registry) | No (user home) |
| `~/.claude/settings.json` | Global permissions — allow/deny lists | No (user home) |
| `~/.claude/settings.local.json` | Global local state (MCP servers, accumulated approvals) | No (user home) |
| `~/.claude/commands/` | Shared slash commands (available in all repos) | No (user home) |
| `~/.claude.json` | MCP server registrations (semops-kb) | No (user home) |
| `<repo>/.claude/settings.json` | Repo-level permissions (team conventions) | Yes |
| `<repo>/.claude/settings.local.json` | Repo-level local state (per-user) | No (gitignored) |
| `<repo>/CLAUDE.md` | Repo-specific agent instructions | Yes |

### Current Global Permissions (`~/.claude/settings.json`)

**Allow:** `Bash`, `Read`, `Write`, `Edit`, `Glob`, `Grep`, `WebFetch`, `WebSearch`, `Task`

**Deny:**

- `Bash(curl *secret*)`, `Bash(curl *password*)`, `Bash(curl *token*)`
- `Bash(wget *secret*)`, `Bash(wget *password*)`, `Bash(wget *token*)`

### Hygiene

`settings.local.json` files (both global and per-repo) accumulate one-off command approvals over time. These can grow to hundreds of entries and may capture sensitive values (tokens, passwords) as verbatim permission strings.

**Maintenance:** Periodically clear the `allow` array in all `settings.local.json` files. The global `settings.json` already allows all tools, so local entries are redundant.

### MCP Servers

| Server | Registration | Transport | Purpose |
| ------ | ------------ | --------- | ------- |
| `semops-kb` | `~/.claude.json` | stdio | Knowledge base access (10 tools): semantic search (entities, chunks, corpora) + ACL queries (patterns, capabilities, integrations, fitness checks) |
| `postgres` | `~/.claude/settings.local.json` | stdio | Direct PostgreSQL access via `@modelcontextprotocol/server-postgres` |

---

## Stack Ecosystem

**Python:** Most Python repos use `uv` with `pyproject.toml`, `.venv/`, `ruff`, and `pytest`. Exception: `semops-research` and `semops-data` use `pip` (DevContainer/standard venv). Per-repo details in each repo's `docs/INFRASTRUCTURE.md` § Python Stack.

**TypeScript/Node.js:** semops-sites uses `pnpm` with Turborepo monorepo, Next.js, React. Per-app details in each app's `package.json`.

### Cross-Repo Library Overlap

Libraries appearing in 2+ repos signal shared infrastructure candidates and patterns stable enough for deterministic scripts. Source: each repo's `pyproject.toml`.

| Library | semops | publisher | data | research | backoffice | dx-hub | Strategic Note |
|---------|--------|-----------|------|----------|------------|--------|----------------|
| `pyyaml` | x | x | x | x | x | x | Universal — standardize YAML handling |
| `python-dotenv` | x | x | x | — | x | — | Candidate for `pydantic-settings` convergence |
| `pydantic` | x | x | x | — | — | — | Shared base models / settings class candidate |
| `click` | x | x | x | — | — | — | Shared CLI scaffolding candidate |
| `sqlalchemy` | x | — | x | — | — | — | Shared query layer (extends `db_utils.py`) |
| `anthropic` | x | x | — | x | — | — | Shared LLM client wrapper candidate |
| `requests` / `httpx` | x | x | — | x | — | — | Converge on `httpx` (async-ready) |
| `beautifulsoup4` | — | x | — | x | x | — | HTML parsing — different domains |
| `qdrant-client` | — | — | — | x | — | — | Vector database — shared infra |
| `pandas` | — | — | x | — | x | — | Data manipulation — different domains |
| `caldav` | — | — | — | — | x | x | Calendar sync — shared module candidate |
| `rich` | x | x | — | — | — | — | CLI output formatting |
| `tqdm` | x | — | x | — | — | — | Progress bars for long-running scripts |

**How to use this table:**

- **3+ repos** = strong candidate for a shared module or base class in `semops-core`
- **2 repos, same domain** = converge when one repo's pattern stabilizes
- **2 repos, different domains** = independent — no action needed
- When an agentic workflow stabilizes (consistent business logic across runs), the "Shared With" signal helps identify which libraries to build deterministic scripts around

### Library → Capability Crosswalk

Where infrastructure meets architecture: shared libraries mapped to the scripts and capabilities they power. Only actively imported libraries are listed — phantom dependencies (declared but unused) are flagged separately below.

| Library | Repo | Script(s) | Capability |
| ------- | ---- | --------- | ---------- |
| **pydantic** | semops | `source_config.py`, `api/query.py` | Source Configuration, Query API |
| | publisher | `config.py` | Publishing Configuration |
| | backoffice | `voice/shared/schemas.py` | Voice Control Models |
| **click** | publisher | `publish.py`, `export_pdf.py` | Publishing CLI, PDF Export |
| | data | `cli.py` | Data Toolkit CLI |
| **anthropic** | semops | `classifiers/llm.py` | LLM Classification |
| | publisher | `agents/*.py` | Content Agents (research, draft, outline, format) |
| **pyyaml** | semops | `source_config.py`, `entity_builder.py` | Source Config, Entity Construction |
| | publisher | `log_edit.py`, `capture_edits.py` | Edit Capture |
| | backoffice | `collect_project_status.py` | Status Collection |
| | dx-hub | `aggregate_adrs.py`, `aggregate_session_notes.py` | ADR/Session Aggregation |
| **rich** | semops | `ingest_from_source.py`, `semantic_search.py` | Ingestion, Search |
| | publisher | `publish.py` | Publishing CLI |
| | backoffice | `voice/client/main.py` | Voice Client |
| **beautifulsoup4** | backoffice | `bofa_parser.py`, `invoice_processor.py` | Financial Parsing |
| **caldav** | backoffice | `attention_service.py` | Calendar Sync |
| **pandas** | data | `synthetic/*.py`, `profiling/profiler.py` | Synthetic Data, Profiling |
| **httpx** | semops | `docling_ingest.py` | Document Processing |
| | backoffice | `voice/client/main.py` | Voice Client |
| **pyyaml** | research | `diligence/project.py`, `diligence/lookups.py` | Data Due Diligence, Reference Generation |
| **requests** | research | `rag/embed.py`, `rag/ingest.py` | Corpus Meta-Analysis |
| **qdrant-client** | research | `rag/embed.py`, `rag/query.py` | Corpus Meta-Analysis |
| **anthropic** | research | `rag/query.py` | Corpus Meta-Analysis |
| **beautifulsoup4** | research | `rag/ingest.py` | Corpus Meta-Analysis |

**Phantom dependencies** (declared in `pyproject.toml` but no active imports — candidates for removal):

| Library | Repo | Notes |
| ------- | ---- | ----- |
| `sqlalchemy` | semops, data | psycopg used directly instead |
| `caldav` | dx-hub | Vestigial — migrated to semops-backoffice |
| `requests` | publisher | No active imports in non-archived code |
| `beautifulsoup4` | publisher | No active imports in non-archived code |
| `pandas` | backoffice | Zero imports |
| `tqdm` | semops, data | `rich.progress` used instead in semops |

---

## Purposeful Duplication Registry

Code intentionally duplicated across repos when shared dependency cost exceeds duplication cost. Tracked here so copies don't silently diverge.

| Module | Canonical Owner | Duplicate | Reason |
|--------|----------------|-----------|--------|
| `spec_parser.py` | `semops-backoffice/scripts/analyzers/spec_analyzer.py` | `semops-dx-orchestrator/scripts/spec_parser.py` | `gh_project_writer.py` needs standalone mode. Analysis functions remain only in semops-backoffice. |

**Rules:**
- When updating the canonical version, check the duplicate for needed sync
- The duplicate should note its origin in a docstring/comment
- Converge to a shared package when repo responsibilities stabilize

---

## Related Documentation

- [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) - System landscape, ownership, DDD alignment
- [DIAGRAMS.md](DIAGRAMS.md) - Visual representations (includes infrastructure diagram)
- [PORTS.md](PORTS.md) - Port registry (single source of truth for all port assignments)
- Per-repo: `docs/INFRASTRUCTURE.md` — operational details, health checks, troubleshooting
