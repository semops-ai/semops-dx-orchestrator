# User Guide

> **Repo:** `semops-dx-orchestrator`
> **Role:** Platform/DX Orchestrator
> **Last Updated:** 2025-12-30

## About This Repo

semops-dx-orchestrator is unique in the SemOps ecosystem - it's the **orchestrator** that defines process and tooling for all other repos. Its "product" is the development workflow itself.

Because of this, the detailed process reference serves as both:
- How to use semops-dx-orchestrator
- How we work across all repos

**Workflow contracts:** [.agent/commands.yml](../.agent/commands.yml)

## Quick Start

### 1. Prime Context

From any repo, load context:

```bash
# Local repo context
/prime

# Full cross-repo context (best from semops-dx-orchestrator)
/prime-global
```

### 2. Start Work on an Issue

```bash
# Assign issue and create session notes
/issue 42

# Or work autonomously (if criteria met)
/issue 42 --auto
```

### 3. Complete Work

Follow the Completion Protocol (automatically triggered):
1. Review changes
2. Update session notes
3. Check if docs need updates
4. Close issue

## Key Commands

| Command | Purpose |
|---------|---------|
| `/prime` | Load local repo context |
| `/prime-global` | Load full SemOps context |
| `/issue <n>` | Assign issue, create session notes |
| `/plan` | Plan implementation |
| `/adr` | Load ADR context |
| `/register-repo` | Add new repo to ecosystem |
| `/arch-sync` | Per-repo template compliance and drift audit |
| `/global-arch-sync` | Cross-repo audit and global doc sync |

Full command reference: [COMMANDS.md](COMMANDS.md)

## Key Files in This Repo

| File | Purpose |
|------|---------|
| [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) | System landscape for all repos |
| [commands.yml](../.agent/commands.yml) | Workflow contracts and process conventions |
| [COMMANDS.md](COMMANDS.md) | Slash command reference |
| [ADR_INDEX.md](ADR_INDEX.md) | Cross-repo ADR aggregation |
| [REPOS.yaml](REPOS.yaml) | Registry of all repos |
| [templates/](templates/) | Documentation templates |

## Troubleshooting

### GitHub Authentication

```bash
# Check status
gh auth status

# If issues, run:
/fixauth
```

### Stale YOLO Sessions

```bash
# View active sessions
/yolo-status

# Cleanup orphaned sessions
git worktree list | grep yolo
git worktree remove <path> --force
```

### Context Not Loading

Ensure you're in a registered repo:

```bash
grep "$(basename $(pwd))" 
```

If not found, run `/register-repo`.

## Publishing to Public Repos

We use a three-tier workflow to separate private development from public releases.

### The Three Tiers

```
Tier 1: Working         Tier 2: Staging              Tier 3: Public
(timjmitchell/*-pr)  →  (timjmitchell/semops-*)  →   (semops-ai/semops-*)

Private, messy          Private, curated             Public, released
```

| Working | Staging | Public |
|---------|---------|--------|
| `semops-dx-orchestrator` | `semops-dx-orchestrator` | `semops-ai/semops-dx-orchestrator` |
| `semops-core` | `semops-core` | `semops-ai/semops-core` |
| `semops-publisher` | `semops-publisher` | `semops-ai/semops-publisher` |
| `semops-docs` | `semops-docs` | `semops-ai/semops-docs` |
| `semops-data` | `semops-data` | `semops-ai/semops-data` |
| `semops-sites` | `semops-sites` | `semops-ai/semops-sites` |

### Staging Content (Tier 1 → Tier 2)

When content is ready for public release, curate and push to staging:

```bash
# Work in your private repo
cd 

# Copy public-ready content to staging
# (Manual curation - remove internal refs, add public context)
cp README.md 

# Commit to staging
cd 
git add README.md
git commit -m "docs: update README for public release"
git push
```

### Releasing to Public (Tier 2 → Tier 3)

Create a PR from staging to public:

```bash
cd 

# Add public remote (first time only)
git remote add public https://github.com/semops-ai/semops-core.git

# Push branch for PR
git push public main:staging-update

# Create PR
gh pr create --repo semops-ai/semops-core \
  --head staging-update \
  --base main \
  --title "docs: update from staging" \
  --body "Curated updates from development"
```

**Note:** The initial release was pushed directly to main (force push) since the repos had no common history. Future updates use the PR workflow above.

### What Lives Where

| Content | Working | Staging | Public |
|---------|---------|---------|--------|
| Source code | ✓ | ✓ (curated) | ✓ |
| Public README/CLAUDE.md | ✓ | ✓ | ✓ |
| Session notes | ✓ | ✗ | ✗ |
| Internal ADRs | ✓ | ✗ | ✗ |
| .env files | ✓ | ✗ | ✗ |

**Full details:** [ADR-0003: Three-Tier Repo Workflow](decisions/ADR-0003-three-tier-repo-workflow.md)

## Related Documentation

- [commands.yml](../.agent/commands.yml) - Workflow contracts and process conventions
- [GLOBAL_ARCHITECTURE.md](GLOBAL_ARCHITECTURE.md) - System overview
- [~/.claude/CLAUDE.md](~/.claude/CLAUDE.md) - Global user instructions
