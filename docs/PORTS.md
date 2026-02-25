# Service Registry

Centralized service allocation for all SemOps repos. Owned by semops-dx-orchestrator (orchestrator).

Each repo owns its services. A central registry prevents conflicts and documents the topology.

## Service Inventory

### semops-core (Schema/Infrastructure)

The heaviest service footprint — hosts all shared infrastructure.

#### Core Stack (`docker-compose.yml`)

| Service | Role | Stack |
|---------|------|-------|
| n8n | Workflow automation | Workflow |
| Qdrant | Vector database (REST + gRPC) | Core |
| Docling | Document processing | Core |
| Neo4j | Graph database (HTTP + Bolt) | Core |

#### Supabase Stack

| Service | Role |
|---------|------|
| Supavisor | Connection pooler |
| PostgreSQL | Primary database (host-mapped for scripts/agents) |
| Kong | API gateway (HTTP + HTTPS) |
| PostgREST | REST API for PostgreSQL |
| Realtime | WebSocket subscriptions |
| Imgproxy | Image transformation |

#### Supabase Dev Tools

| Service | Role |
|---------|------|
| Supabase Studio | Database admin UI |
| Inbucket | Mail testing (SMTP + Web + POP3) |
| Postgres Meta | Database metadata admin |

#### Local AI Stack

| Service | Role |
|---------|------|
| Ollama | LLM server |
| Open WebUI | Chat interface |
| Flowise | LLM orchestration |
| Langfuse | LLM observability (Web + Worker) |
| ClickHouse | Analytics (Langfuse backend) |
| SearXNG | Search engine |
| Whisper | Speech-to-text |
| Redis | Cache/broker |
| PostgreSQL | Langfuse database (separate from Supabase) |
| MinIO | Object storage (API + Console) |

#### Caddy Reverse Proxy

Routes external traffic to internal services via path-based routing. Each service gets a stable `/service-name` route.

### semops-publisher (Publishing/Content)

| Service | Role |
|---------|------|
| ComfyUI | Image generation |

### semops-data (Analytics/MLOps)

| Service | Role |
|---------|------|
| MLflow | Experiment tracking (DevContainer) |
| Marquez | Data lineage (API + Admin + Web) |
| Jupyter Lab | Notebooks |

### semops-sites (Frontend/Deployment)

| Service | Role |
|---------|------|
| timjmitchell.com | Next.js application |
| semops.ai | Next.js application |
| Supabase local | Local Supabase instance |

### semops-dx-orchestrator (Platform/DX)

| Service | Role |
|---------|------|
| MkDocs | Documentation server |
| Gitea | Git server (HTTP + SSH) |

## Conflict Resolution

Multiple services sometimes need the same default port. Resolution strategies:

1. **Remap** the less-critical service to a different port (e.g., MinIO, Marquez Web)
2. **Profile isolation** — services behind Docker Compose profiles don't run simultaneously
3. **Document and avoid** — some conflicts are acceptable if services are never co-active

## Convention for New Services

1. Check this registry before allocating a port
2. Prefer ports in the repo's suggested range for repo-specific services
3. Update this document when adding services
4. Use `.env` / `.env.example` to make ports configurable

### Suggested Port Ranges

| Range | Reserved For |
|-------|-------------|
| 3000–3099 | Web application frontends |
| 5000–5099 | Application APIs |
| 5432–5499 | PostgreSQL instances |
| 6300–6399 | Vector databases |
| 7400–7699 | Graph databases |
| 8000–8099 | API gateways, reverse proxies |
| 8100–8199 | ML/AI services |
| 8800–8899 | Notebooks, dev tools |
| 11434 | Ollama (standard) |
| 54321–54399 | Supabase local instances |
