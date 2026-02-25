# Infrastructure

Operational context for the SemOps local development environment.

> **Dynamic checks:** Use `/infra` command for live status (when implemented).
> **Port reference:** See [PORTS.md](PORTS.md) for port assignments.

## Service Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Local Machine                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Docker Networks                        │   │
│  │                                                           │   │
│  │  ┌─────────────────────┐  ┌─────────────────────────┐    │   │
│  │  │ semops-hub_default  │  │   supabase_default      │    │   │
│  │  │                     │  │                         │    │   │
│  │  │  semops-hub-pg      │  │  supabase-pooler :5432  │    │   │
│  │  │    host→5434:5432   │  │  supabase-kong   :8000  │    │   │
│  │  │  ike-qdrant  :6333  │  │  supabase-studio       │    │   │
│  │  │  ike-neo4j   :7474  │  │  + 10 more services    │    │   │
│  │  │  ike-docling :5001  │  │                         │    │   │
│  │  │  ike-n8n     :5678  │  └─────────────────────────┘    │   │
│  │  └─────────────────────┘                                  │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Local Services                         │   │
│  │                                                           │   │
│  │  Ollama (native)    :11434   mistral, nomic-embed-text   │   │
│  │                                                           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Docker Containers

### Always-On (semops-core)

| Container | Port | Purpose |
|-----------|------|---------|
| `semops-hub-pg` | 5434 (host) → 5432 (container) | PostgreSQL for KB entities, embeddings |
| `ike-qdrant` | 6333, 6334 | Vector search (REST/gRPC) |
| `ike-neo4j` | 7474, 7687 | Graph database (HTTP/Bolt) |
| `ike-docling` | 5001 | PDF/document parsing |
| `ike-n8n` | 5678 | Workflow automation |

### Supabase Stack (semops-sites, optional)

| Container | Port | Purpose |
|-----------|------|---------|
| `supabase-pooler` | 5432, 6543 | Connection pooling |
| `supabase-kong` | 8000, 8443 | API gateway |
| `supabase-studio` | (via Kong) | Admin UI |
| + 10 more | various | Auth, storage, realtime, etc. |

### Stale/Exited

| Container | Status | Action |
|-----------|--------|--------|
| `ike-n8n-import` | Exited (0) 3 days ago | One-time import, can remove |
| `recursing_northcutt` | Exited (0) 7 weeks ago | Unknown, investigate or remove |

## Docker Networks

| Network | Purpose |
|---------|---------|
| `semops-hub_default` | Main KB infrastructure |
| `supabase_default` | Supabase local stack |
| `ike-semantic-ops_default` | Legacy (may be stale) |
| `ike_default` | Legacy (may be stale) |
| `comfyui_default` | ComfyUI for image generation |

## Docker Volumes

### Active

| Volume | Purpose |
|--------|---------|
| `ike-semantic-ops_qdrant_storage` | Qdrant vector data |
| `ike-semantic-ops_neo4j_data` | Neo4j graph data |
| `ike_n8n_storage` | n8n workflows and data |
| `ike-semantic-ops_docling_cache` | Docling PDF cache |
| `semops-core_*` | Newer volume naming |

### Potentially Stale

Volumes with `ike_` prefix may be from older compose files. Compare with `ike-semantic-ops_` volumes.

## Python Environments

| Repo | Environment | Package Manager | Notes |
|------|-------------|-----------------|-------|
| `semops-dx-orchestrator` | `.venv` | uv | pyproject.toml |
| `semops-core` | `.venv` | uv | pyproject.toml |
| `semops-publisher` | `.venv` | uv | pyproject.toml + requirements.txt |
| `semops-docs` | none | - | No Python code |
| `semops-data` | `venv` | uv | pyproject.toml |
| `semops-sites` | none | - | Node.js (pnpm) |

### Environment Strategy

- **All Python repos:** Use `uv` with `.venv` and `pyproject.toml`
- **Activation:** `source .venv/bin/activate` or use `uv run <script>`

## Credentials

### Location Map

| Credential | Location | Used By |
|------------|----------|---------|
| `GITHUB_TOKEN` | `~/.bashrc` | All repos (git, gh CLI) |
| `OPENAI_API_KEY` | `semops-core/.env` | Embeddings, LLM calls |
| `ANTHROPIC_API_KEY` | `semops-core/.env` | Claude API (if used) |
| `POSTGRES_PASSWORD` | `semops-core/.env` | Supabase container build |
| `SEMOPS_DB_*` | `semops-core/.env` | KB database connection (scripts/agents) |
| `FASTMAIL_*` | GitHub Secrets | Calendar sync, email |

### Per-Repo .env Status

| Repo | .env | .env.example |
|------|------|--------------|
| semops-dx-orchestrator | ✓ | ✓ |
| semops-core | ✓ | ✓ |
| semops-publisher | ✓ | ✓ |
| semops-docs | ✓ | - |
| semops-data | ✓ | ✓ |
| semops-sites | ✓ | ✓ |

## Ollama

**Status:** Running natively (not containerized)
**Endpoint:** `http://localhost:11434`

### Loaded Models

| Model | Purpose |
|-------|---------|
| `mistral:latest` | General LLM |
| `nomic-embed-text:latest` | Text embeddings |

### Adding Models

```bash
ollama pull <model-name>
ollama list  # Verify
```

## Known Workarounds

### ~~Postgres Container IP~~ (RESOLVED — semops-core#124)

~~The `semops-hub-pg` container gets a dynamic IP on the Docker bridge network. Currently hardcoded to `172.24.0.4` in various configs.~~

**Resolution:** PostgreSQL is now exposed on host port 5434 (`127.0.0.1:5434:5432`). All scripts use `SEMOPS_DB_*` env vars via shared `scripts/db_utils.py`. No more container IP references.

### ~~Port 5432 Conflict~~ (RESOLVED — semops-core#124)

~~Both `semops-hub-pg` and `supabase-pooler` want port 5432.~~

**Resolution:** Supavisor keeps `:5432` (internal Supabase use). PostgreSQL is directly accessible on `:5434` for scripts and agents. Use `SEMOPS_DB_PORT=5434`.

### Supabase vs Standalone Postgres

Two Postgres instances exist:
1. **semops-hub-pg** (`:5434`) - KB entities, embeddings, semantic search
2. **supabase-pooler** (`:5432`) - Connection pooler for Supabase internals

They serve different purposes. Don't confuse them.

## Startup Commands

### Start KB Infrastructure

```bash
cd 
docker compose up -d
```

### Start Supabase (if needed)

```bash
cd 
npx supabase start
```

### Start Ollama

```bash
ollama serve  # If not running as service
```

## Health Checks

### Quick Status

```bash
# Containers
docker ps --format "table {{.Names}}\t{{.Status}}"

# Postgres (direct on port 5434)
pg_isready -h localhost -p 5434

# Qdrant
curl -s http://localhost:6333/health | jq

# Neo4j
curl -s http://localhost:7474

# Ollama
curl -s http://localhost:11434/api/tags | jq '.models[].name'
```

## Related

- [PORTS.md](PORTS.md) - Port assignments
- [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) - System architecture
- [REPOS.yaml](REPOS.yaml) - Repository registry
