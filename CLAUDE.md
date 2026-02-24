# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Role in Global Architecture

**Role:** Platform/DX (Developer Experience)

```
semops-dx-orchestrator [PLATFORM/DX]
 │
 ├── Owns: Process (how we work)
 │ - Global architecture docs
 │ - Cross-repo synchronization rules
 │ - ADR templates, workflow guides
 │ - Session notes process
 │
 └── Coordinates with: semops-core [SCHEMA]
 - Owns: Model (what we know)
 - Schema, knowledge graph, semantic operations
```

**Key Ownership Boundary:**

- This repo owns **process** - workflow documentation, global architecture docs, ADR templates
- `semops-core` owns **model** - schema, UBIQUITOUS_LANGUAGE.md, knowledge graph, RAG pipeline

**Global Docs Location:** `docs/GLOBAL_ARCHITECTURE.md` - the system landscape for all repos

## Session Notes

Document work sessions tied to GitHub Issues in `docs/session-notes/`:

- **Format:** `ISSUE-NN-description.md` (one file per issue, append-forever)
- **Structure:** Date sections within file for chronological tracking
- **Index:** Update `docs/SESSION_NOTES.md` with new entries
- **When:** Working on any GitHub Issue, or ad-hoc exploratory sessions

## Project Specs

Every GitHub Project gets a scope definition in `docs/project-specs/`:

- **Format:** `PROJECT-NN-description.md` (NN = GitHub Project number)
- **Template:** `docs/project-specs/TEMPLATE.md`
- **Rule:** semops-dx-orchestrator holds coordination issues and specs only — implementation issues belong in the repo where code changes happen

## Key Files

- [docs/GLOBAL_ARCHITECTURE.md](docs/GLOBAL_ARCHITECTURE.md) - System landscape for all repos
- [docs/project-specs/](docs/project-specs/) - Project scope definitions
- [docs/decisions/](docs/decisions/) - Architecture Decision Records
- [docs/session-notes/](docs/session-notes/) - Session logs by issue
- [~/.claude/CLAUDE.md](~/.claude/CLAUDE.md) - Global instructions (user-level)
