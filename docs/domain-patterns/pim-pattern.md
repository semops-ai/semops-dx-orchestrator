# PIM Pattern

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-01-10
> **Provenance:** 3P
> **Derives From:** Product Information Management

Product Information Management (PIM) patterns for branded content syndication and multi-channel publishing.

---

## Overview

**Product Information Management (PIM)** is an industry pattern for maintaining a single source of truth for product data that gets distributed across multiple channels. SemOps adopts PIM for managing branded content (blog posts, articles, videos) across publication surfaces like semops.ai, LinkedIn, and Medium.

PIM provides:
- **Centralized source of truth** - One canonical record per content item
- **Channel syndication** - Distribute to multiple platforms with transformations
- **Platform-specific field mapping** - Adapt content structure per channel
- **Version consistency** - Track which version is live where


## Adoption Context

- **Original Capability:** `publishing-pipeline` — product information management for multi-channel publishing
- **Problem Statement:** Product information (specs, attributes, relationships) needs management across sales and publishing channels. Each channel has different format requirements.
- **Selection Criteria:** Industry-standard Product Information Management — canonical pattern for managing product metadata across channels. Supports the DAM publishing workflow in semops-publisher.
- **ADR:** —

## PIM Concepts in SemOps

### Core Mapping

| PIM Concept | SemOps Implementation | Purpose |
|-------------|----------------------|---------|
| Product | Entity (content item) | The canonical content record |
| Channel | Surface | Publication platform (WordPress, Medium, LinkedIn) |
| Listing | Delivery | Platform-specific publication record |
| Catalog | Entity table (unified) | All content (1P + 3P) in one place |

### Key Entities

**Surface** - A publication platform or channel:
```yaml
surface:
  id: semops-ai-blog
  platform: wordpress
  base_url: https://semops.ai/blog/
  field_mapping:
    title: post_title
    body: post_content
    summary: post_excerpt
```

**Delivery** - A specific publication instance:
```yaml
delivery:
  entity_id: blog-post-semantic-coherence
  surface_id: semops-ai-blog
  role: original                    # or "syndication"
  status: published
  published_at: 2026-01-10T10:00:00Z
  platform_url: https://semops.ai/blog/semantic-coherence
```

---

## Multi-Channel Syndication Flow

```
                    Entity (Canonical)
                           │
                           ▼
           ┌───────────────┼───────────────┐
           │               │               │
           ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ Surface  │    │ Surface  │    │ Surface  │
    │ semops.ai│    │  Medium  │    │ LinkedIn │
    └────┬─────┘    └────┬─────┘    └────┬─────┘
         │               │               │
         ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ Delivery │    │ Delivery │    │ Delivery │
    │ original │    │syndication│   │syndication│
    └──────────┘    └──────────┘    └──────────┘
```

### Publication Roles

| Role | Meaning | Use Case |
|------|---------|----------|
| `original` | First/primary publication | Your blog, main site |
| `syndication` | Cross-posted to additional channel | Medium, LinkedIn, Dev.to |

---

## Platform-Specific Field Mapping

Different platforms have different content requirements. PIM's field mapping pattern enables one canonical entity to publish appropriately everywhere:

### Example: Blog Post Entity

```yaml
# Canonical Entity Fields
entity:
  title: "Understanding Semantic Coherence"
  summary: "A deep dive into measuring shared meaning..."
  body: "[Full markdown content]"
  author: "Tim Mitchell"
  tags: ["semantic-operations", "data-architecture"]
  canonical_url: "https://semops.ai/blog/semantic-coherence"
```

### WordPress Mapping (semops.ai)
```yaml
field_mapping:
  title → post_title
  summary → post_excerpt
  body → post_content
  tags → post_tags
  canonical_url → canonical_link
```

### Medium Mapping
```yaml
field_mapping:
  title → title
  body → content (markdown)
  tags → tags (max 5)
  canonical_url → canonicalUrl
# Note: Medium doesn't support excerpts
```

### LinkedIn Mapping
```yaml
field_mapping:
  title → article_title
  summary → share_text (for link posts)
  body → article_content (for articles)
# Note: LinkedIn has character limits
```

---

## Branded Content Integration

PIM is foundational for integrating "branded" components (content published under semops.ai or timjmitchell.com brands) into the SemOps architecture:

### 1. Catalog as Source of Truth
All branded content exists as 1P entities in the unified catalog before publication.

### 2. Publication Workflow
```
Draft (pending) → Approved → Delivered (original) → Syndicated
```

### 3. Lineage Tracking
Syndicated versions link back to original via `source_hash`:
```yaml
delivery:
  entity_id: blog-post-001
  surface_id: medium
  role: syndication
  source_hash: "abc123"  # Links to original version
```

### 4. Update Propagation
When canonical entity updates:
1. Original delivery updated first
2. Syndication deliveries queued for update
3. Each platform receives appropriate field mapping

---

## Dual-Status Model

PIM's separation of "product readiness" from "channel listing status" maps directly to SemOps's dual-status model:

| PIM Concept | SemOps Field | Values |
|-------------|--------------|--------|
| Product Status | `entity.approval_status` | pending, approved, rejected |
| Listing Status | `delivery.status` | planned, queued, published, failed, removed |

**Key Insight:** An entity can be "approved" (ready to publish) but not yet "delivered" to any surface. And delivery failures on one surface don't affect the entity's approval status.

---

## Use Cases

### 1. Blog Post Launch
- Create entity with all content
- Approve when finalized
- Deliver to semops.ai (original)
- Queue syndication to Medium, LinkedIn

### 2. Content Update
- Update canonical entity
- All deliveries marked for re-sync
- Platform-specific transformations applied

### 3. Platform Expansion
- Add new Surface definition
- Define field mapping
- Queue existing approved content for new channel

### 4. Analytics Aggregation
- Each delivery tracks platform-specific analytics
- Roll up to entity level for cross-platform metrics

---

## Related Documentation

- [content-lifecycle-states.md](content-lifecycle-states.md) - Content state machines (uses PIM dual-status)
- [publication-patterns.md](publication-patterns.md) - Publishing workflow patterns
- [dam-foundations.md](dam-foundations.md) - Domain context and convergence model

---

## 3P Foundation

| PIM Concept | SemOps Adoption | Our Extension |
|-------------|-----------------|---------------|
| Central catalog | Entity table | Unified 1P + 3P catalog |
| Channel syndication | Surface/Delivery | Role (original/syndication) tracking |
| Field mapping | Surface config | Platform-specific transformations |
| Version tracking | source_hash | Cryptographic content hashing |

**Key Extension:** Unlike traditional PIM (which only manages owned products), SemOps's unified catalog includes 3P references. This enables citing external sources in the same system that manages your branded content.

