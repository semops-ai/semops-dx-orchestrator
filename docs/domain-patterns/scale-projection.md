# Scale Projection

> **Version:** 2.0.0
> **Status:** active
> **Last Updated:** 2026-02-20
> **Provenance:** 1P
> **Derives From:** `ddd`, `rlhf`, `seci`, `semops-dataofiling`, `synthetic-data`

Scale Projection is a validation technique: project your current architecture to a scaled deployment, and measure the gap. If the gap is purely infrastructure, your domain model is coherent. If it requires logic changes, your abstractions are wrong.

---

## Overview

> Production-readiness is a property of your abstractions, not your infrastructure.

Scale Projection inverts the conventional framing of scaling. Instead of asking "what infrastructure do we need?", it asks "do we understand the domain well enough that infrastructure choices are constrained — possibly obvious?" The technique: sketch what your system would look like at the next order of magnitude, then categorize every gap as Clean (infrastructure only), Concerning (logic changes needed), or Blocking (fundamental rethink). If you can't sketch it cleanly, you're not missing infrastructure knowledge — you're missing domain knowledge.

Scale Projection evolved from "Progressive Productization" ([#65](https://github.com/semops-ai/semops-dx-orchestrator/issues/65)). The early iteration used the term "Mirror Architecture" for the structural scaffolding (directory layout, parallel representations), but the value was never the structure — it was what the gap between local and projected reveals about domain coherence. The structural conventions (formerly "Mirror Architecture") are now part of this pattern; see [ADR-0004](../decisions/ADR-0004-mirror-architecture.md) for the original framing.

## Adoption Context

- **Original Capability:** `scale-projection` — validating domain coherence by projecting architecture to scaled deployment
- **Problem Statement:** Need a diagnostic for whether architecture is coherent at scale, without actually deploying at scale. The conventional approach ("scale it and see") is expensive and reveals problems too late. Infrastructure-first thinking lets you *feel* like you're making progress without doing the hard work of understanding the domain.
- **Selection Criteria:** Domain-first validation — the gap between local and scaled should be infrastructure only. If logic changes are required, the domain model needs work, not the infrastructure.
- **ADR:** [ADR-0004: Mirror Architecture](../decisions/ADR-0004-mirror-architecture.md) (original framing)

## Core Concepts

### The Continuum

Eight levels of formalization representing **domain legibility**, not operational maturity. Unlike CMMI's 5 linear maturity levels, Continuum levels are not sequential — a feature may jump from Level 0 to Level 4 when the domain is well-understood, or stay at Level 1 indefinitely when it is not.

| Level | What It Represents | Key Characteristic |
| ----- | ------------------ | ------------------ |
| 0. Chat | "I'm exploring what this even is" | Ephemeral, manual |
| 1. Slash commands | "I can name the operations" | Structured invocation |
| 2. Subagents / CLAUDE.md | "I can externalize the rules" | Repo-persisted context |
| 3. External trigger | "I know the boundaries and entry points" | Event-driven |
| 4. Local container | "I can specify inputs and outputs precisely" | Isolated execution |
| 5. Cloud sandbox | "The domain model is stable enough that infra is just plumbing" | Managed infrastructure |
| 6. Deployed service | Production | Full state, auth, orchestration |
| 7. Scaled infra | Enterprise scale | Distributed, multi-tenant |

The discipline is staying in the lab (levels 0-3) until the business logic is *earned*, not assumed. Then productionization is straightforward — you're just wrapping known-good logic in infrastructure. Treating levels 0-3 as "quick MVP" and rushing to 4+ before the manifest is solid means scaling confusion.

### The Axes

Six independent dimensions of formalization. You don't move all axes together — strong isolation with weak observability is a valid state. The axes are diagnostic, not prescriptive.

| Axis | Informal → Formal |
| ---- | ------------------ |
| **Directed** | Chat → Slash → Event → API → Scheduled |
| **Context** | Conversation → Hooks → Manifest → DB-backed |
| **Isolation** | Shared env → Worktree → Container → VM → Cluster |
| **Access** | Just you → Team → Authenticated users → Public |
| **State** | Stateless → Issue-as-queue → Managed queue → DB |
| **Observability** | Print → Logs → Metrics → Tracing |

### GAPS.md

The coherence test artifact. Maintained per-repo, categorizing every gap between local and projected as Clean (infra only), Concerning (logic changes), or Blocking (rethink). If "Blocking" has items, stop and fix abstractions. If "Concerning" grows faster than "Clean," you're accruing domain debt. See the [GAPS.md template](../templates/GAPS.md).

### The Manifest

Business logic as the portable artifact across all levels. The manifest (context, tools, constraints, prompts) is identical whether running as a CLI, container, or distributed service. Infrastructure is just the wrapper.

The manifest you end up with isn't documentation of features — it's **crystallized understanding** of the problem. The slash commands aren't shortcuts, they're hypotheses you're testing.

### DDD Lens

The levels aren't about operational maturity — they're about domain understanding:

| Symptom | Conventional Diagnosis | Domain Diagnosis |
| ------- | ---------------------- | ---------------- |
| Scaling is complicated | Need better infra | Boundaries are wrong |
| Adding features is hard | Tech debt | Manifest structure doesn't match real task taxonomy |
| Agents fail unpredictably | Need better prompts | Constraints aren't explicit because they aren't understood |
| Integration is messy | Need middleware | Contracts don't reflect actual data flows |

**The reframe:** "We need to understand the domain. When we do, infrastructure choices will be constrained — possibly obvious."

### 3P Lineage

| Source | Contribution | Extension |
|--------|-------------|----------|
| Domain-Driven Design (Evans) | Bounded contexts, ubiquitous language, domain model primacy | "If scaling requires logic changes, your boundaries are wrong" — gap analysis as DDD diagnostic |
| Hexagonal Architecture (Cockburn) | Domain doesn't know infrastructure | Domain logic is *identical* across scale levels, not just isolated |
| Maturity Models (CMMI) | Progressive capability levels | Levels represent *domain understanding*, not process maturity; non-linear |
| Fitness Functions (Ford/Parsons/Kua) | Automated architecture validation | *Sketch* the scaled version as a diagnostic, don't deploy it |
| Capacity Planning (SRE) | Forecast resource needs | Use infrastructure delta to reveal *architecture* problems |
| Architecture Runway (SAFe) | Forward-looking investment in architectural capability | Scale Projection is the *diagnostic* that identifies what the Runway should invest in. Runway answers "build what first?"; Scale Projection answers "is what we built coherent?" |

### Novelty Claim

> If the infrastructure delta between local and scaled is anything other than "wrapper and plumbing," your architecture (not your infrastructure) needs work.

## Scale Vectors

Scale Vectors are the dimensions you project along when performing a Scale Projection. Where the Axes (Directed, Context, Isolation, Access, State, Observability) describe the *formalization state* of your infrastructure, Scale Vectors describe the *growth dimensions* that stress your architecture. You pick the relevant vectors, project your system along each one, and categorize the resulting gaps in GAPS.md.

Not every vector applies to every projection. Part of the methodology is selecting which vectors are relevant — a solo data pipeline doesn't need the Team Size vector, and an internal tool doesn't need Geographic Distribution.

### Vector Reference

| Vector | Guiding Question | Example Projection | Typical Clean Gap | Typical Concerning Gap |
| ------ | ---------------- | ------------------ | ----------------- | ---------------------- |
| **Data Volume** | What happens at 10x / 100x current data? | File-based → managed DB; batch → streaming | Add object storage, partition tables | Business logic assumes everything fits in memory |
| **Concurrent Users** | What happens with N simultaneous users? | Single-tenant → multi-tenant; add auth, rate limiting | Add auth proxy, connection pooling | Domain model has implicit single-user assumptions |
| **Geographic Distribution** | What if users or data are global? | Single region → CDN, edge compute, data residency | Add CDN, regional replicas | Data model assumes co-located storage; no concept of locality |
| **Throughput / Latency** | What if response time matters at volume? | Sync → async; add queues, caching layers | Swap in-process queue → managed queue | Business logic is synchronous by design; can't decouple without rewrite |
| **Data Complexity** | What if schema or relationships grow? | Flat files → relational; add search index, graph | Add indexes, migrate storage engine | Entity relationships are implicit in code, not in schema |
| **Regulatory / Compliance** | What if audit, privacy, or access control requirements apply? | Implicit → explicit logging, RBAC, encryption at rest | Add audit logging, encryption layer | No concept of data ownership or access boundaries in domain model |
| **Team Size** | What if multiple contributors work on this? | Solo → CI/CD, code review, access control, documentation | Add CI pipeline, branch protection | Knowledge is tribal; manifest and contracts aren't explicit enough to onboard |

### Selecting Vectors

When performing a Scale Projection:

1. **Start with the forcing function** — What's the most likely growth dimension? (Often Data Volume or Concurrent Users, but not always.)
2. **Add regulatory if external-facing** — Any system that handles user data or integrates with external services should project along Regulatory/Compliance.
3. **Skip what doesn't apply** — A batch data pipeline may only need Data Volume and Data Complexity. A public API may need Concurrent Users, Throughput, and Geographic Distribution.
4. **Project one vector at a time** — Don't combine vectors in a single projection pass. Each vector stress-tests different parts of the architecture. Combining them obscures which vector reveals which gap.
5. **Record which vectors you projected along** — GAPS.md should note the vectors that informed each gap item, so future reviewers understand the projection context.

### Relationship to Axes

The Axes describe *where you are* on the formalization spectrum. The Scale Vectors describe *what happens when you project forward*. They interact:

| Scale Vector | Most Affected Axes |
| ------------ | ------------------ |
| Data Volume | State, Isolation |
| Concurrent Users | Access, State, Isolation |
| Geographic Distribution | Context, Isolation |
| Throughput / Latency | Directed, State |
| Data Complexity | Context, State |
| Regulatory / Compliance | Access, Observability |
| Team Size | Context, Observability, Directed |

A gap that requires moving along an Axis (e.g., from "Stateless" to "DB-backed" on the State axis) is likely Clean. A gap that requires the domain logic to change regardless of axis position is Concerning or Blocking.

## Resourcing Methodology

Once GAPS.md identifies gaps, you need a systematic way to evaluate options for resolving them. The resourcing methodology answers: "I know what's missing — now what do I do about it?"

### Triage by Gap Category

The gap category determines the resolution approach:

| Category | Resolution Approach | Urgency |
| -------- | ------------------- | ------- |
| **Clean** | Evaluate infrastructure options; resolve when needed | Low — these are known and bounded |
| **Concerning** | Investigate domain model; determine if abstraction change is needed before choosing infrastructure | Medium — accruing debt if ignored |
| **Blocking** | Stop. Fix the domain model. Re-project after fix. | High — no infrastructure investment until resolved |

**Key principle:** Never resource a Concerning or Blocking gap with infrastructure. If the domain model is wrong, better infrastructure just scales the wrong thing faster.

### Resolution Process

For each gap in GAPS.md:

1. **Classify the gap type** — Is it an infrastructure gap (domain model correct, need different wrapper), an abstraction gap (implicit assumptions break at scale), or a boundary gap (responsibilities in the wrong place)? Infrastructure gaps are typical for Clean items, abstraction gaps for Concerning, boundary gaps for Blocking.

2. **Map the design space** — For infrastructure gaps, identify the categories of solutions:

   | Gap Example | Design Space |
   | ----------- | ------------ |
   | File queue → managed queue | Managed cloud (SQS, Pub/Sub), self-hosted (Redis, RabbitMQ), in-process (Celery) |
   | No auth → auth proxy | Platform-native (Supabase Auth, Cognito), reverse proxy (Kong, Caddy), application-level (Passport, NextAuth) |
   | No monitoring → observability | Managed (Datadog, New Relic), self-hosted (Grafana + Loki), lightweight (structured logging) |

   For abstraction gaps, identify what the domain model needs to express that it currently doesn't (e.g., tenant boundaries, data locality, access ownership).

3. **Evaluate against constraints:**

   | Criterion | Question |
   | --------- | -------- |
   | **Cost** | What's the cost at projected scale, not just current? |
   | **Operational complexity** | Who maintains this? What's the failure mode? |
   | **Team familiarity** | Can the current team operate this, or does it require new expertise? |
   | **Vendor lock-in** | How portable is this choice? What's the exit cost? |
   | **Open-source availability** | Is there a viable OSS alternative? |
   | **Coherence** | Does this choice align with existing patterns, or introduce a new paradigm? |

4. **Validate against scale vectors** — Check that the chosen solution handles the projected scale, not just the current one. A solution that resolves today's gap but creates a new gap at the next projection is not a resolution — it's a deferral.

5. **Document the decision** — For significant choices, create an ADR. For routine infrastructure choices, annotate GAPS.md directly.

### Decision Criteria Template

Use this template for each Clean gap that requires evaluation:

```markdown
### Gap: [description]

- **Scale vector:** [which vector revealed this gap]
- **Current state:** [what exists now]
- **Projected need:** [what the projection requires]
- **Options considered:**
  1. [Option A] — [pros / cons]
  2. [Option B] — [pros / cons]
  3. [Option C] — [pros / cons]
- **Selection criteria:** [which constraints matter most for this gap]
- **Decision:** [chosen approach]
- **Rationale:** [why this option over the others]
- **ADR:** [link if created, or "inline — routine infrastructure choice"]
```

### 3P Lineage for Resourcing

The resourcing methodology draws from established frameworks rather than inventing evaluation criteria from scratch:

| Source Framework | What It Contributes | How Scale Projection Uses It |
| ---------------- | ------------------- | ---------------------------- |
| AWS Well-Architected (6 pillars) | Structured evaluation: cost, operational excellence, reliability, performance, security, sustainability | The constraint evaluation criteria map to these pillars |
| ThoughtWorks Technology Radar | Adopt / Trial / Assess / Hold categorization for technology options | Options in the design space can be categorized by maturity and confidence |
| AWS 6 Rs (Migration) | Rehost, Replatform, Refactor, Repurchase, Retire, Retain | Clean gaps map to Rehost/Replatform; Concerning gaps map to Refactor; Blocking gaps map to Rearchitect |
| SRE Capacity Planning | Forecasting resource needs from observed growth | Scale vectors provide the growth dimensions; capacity planning provides the forecasting methodology |
| DORA / Accelerate | Deployment frequency, lead time, MTTR, change failure rate | Team Size vector evaluation should consider DORA metrics as operational readiness indicators |

## Examples

### GAPS.md for a Content Publishing Service

```markdown
## Clean (infrastructure only)
- [ ] Swap file-based queue → SQS/Redis
- [ ] Add container orchestration (Docker Compose → ECS)
- [ ] Add auth proxy (none → Kong/Supabase Auth)
- [ ] Add monitoring (print statements → structured logging + Grafana)

## Concerning (logic changes needed)
- [ ] Multi-tenancy: manifest assumes single user, need tenant isolation in content model
- [ ] Rate limiting: no concept of quotas in current domain model

## Blocking (fundamental rethink)
- (none)
```

**Interpretation:** Two "Concerning" items reveal the domain model has implicit single-tenant assumptions baked into the content model. These are not infrastructure problems — they are abstraction problems. Fix the domain model before investing in infrastructure scaling.

### Coherence Test Applied

| Question | This Service | Signal |
| -------- | ------------ | ------ |
| Is the manifest the same locally and at scale? | Yes — same commands.yml | Healthy |
| Is the input/output shape the same? | Yes — same JSON schemas | Healthy |
| What's the diff between local and scaled runner? | Container wrapper + queue polling | Clean (infra only) |
| Can you explain every GAPS.md item? | Yes, but two are domain concerns | Concerning — fix first |

### Healthy Progression

```text
Level 0: Chat with Claude about content publishing
Level 1: Create /publish slash command
Level 2: Document in CLAUDE.md, add schemas
Level 3: Trigger via GitHub Issues
Level 4: Docker container with same manifest
Level 5: Deploy to Modal/Fly with minimal changes
```

At each level, the business logic (what gets published, how it's formatted, validation rules) stays in the manifest. Only the infrastructure wrapper changes.

### Unhealthy Signs

- Scaled API shape requires completely different endpoints than local
- Can't explain what GAPS.md "Concerning" items actually require
- "We'll figure out auth/multi-tenancy later" appears in GAPS.md
- Manifest has `# TODO: works locally, needs rewrite for production`

## References

- [ADR-0004: Mirror Architecture](../decisions/ADR-0004-mirror-architecture.md) — original framing and decision record
- [rlhf.md](rlhf.md) — human feedback for progressive quality improvement
- [seci.md](seci.md) — knowledge creation spiral (tacit → explicit)
- [synthetic-data.md](retired/synthetic-data.md) — test data generation for scale testing
- [explicit-enterprise.md](explicit-enterprise.md) — organizational scaling context
- Origin: [semops-dx-orchestrator#65](https://github.com/semops-ai/semops-dx-orchestrator/issues/65) (closed) — Progressive Productization

