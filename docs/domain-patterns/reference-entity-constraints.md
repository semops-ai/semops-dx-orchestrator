# Constraints

> Validation rules and attribute combination constraints for SemOps entities.

**Version:** 2.0.0
**Status:** active
**Last Updated:** 2025-11-23

---

## Overview

This document defines the validation rules and constraints that govern valid combinations of entity attributes in the SemOps schema. These constraints ensure data integrity and prevent contradictory states.

**Types of Constraints:**
- **Provenance Constraints** - Valid status values per provenance type
- **Visibility Constraints** - Surface direction restrictions
- **Approval Constraints** - Delivery record requirements
- **Asset Type Constraints** - Filespec format requirements
- **Delivery Role Constraints** - Cardinality rules

---

## Provenance + Approval Status Constraints

| Provenance | Valid Approval Status Values | Notes |
|------------|------------------------------|-------|
| `3p` | `pending`, `approved`, `rejected` | External content can be pending catalog approval |
| `1p` | `pending`, `approved`, `rejected` | Full lifecycle control |
| `2p` | `pending`, `approved`, `rejected` | Collaborative lifecycle |

**No Special Constraints:** All provenance types support the full approval lifecycle.

**Examples:**

**3P Content Workflow:**
- 3P content starts as `pending` when dropped in staging inbox
- Agent or user reviews and approves → `approval_status=approved`
- Low-quality or irrelevant 3P content → `approval_status=rejected`

**1P Content Workflow:**
- 1P content starts as `pending` during drafting
- Creator finalizes → `approval_status=approved`
- Content deemed unsuitable → `approval_status=rejected`

**2P Content Workflow:**
- 2P content starts as `pending` during collaboration
- All parties approve → `approval_status=approved`
- Collaboration discontinued → `approval_status=rejected`

---

## Visibility + Surface Direction Constraints

| Visibility | Surface Direction | Constraint |
|-----------|------------------|------------|
| `private` | `publish` | ONLY to private/internal surfaces |
| `public` | `publish` | Can publish to any public surface |
| ANY | `ingest` | No constraint (can ingest both public and private) |

**Validation Rules:**
```
IF visibility = "private" AND surface.direction = "publish"
  THEN surface MUST be internal/private (knowledge base, RAG, etc.)

IF visibility = "public" AND surface.direction = "publish"
  THEN surface CAN be public platform (blog, YouTube, etc.)
```

**Examples:**

**Valid - Private to Internal Surface:**
```json
// Entity
{
  "id": "private-research-notes",
  "visibility": "private"
}

// Surface (internal)
{
  "id": "internal-knowledge-base",
  "direction": "publish",
  "platform": "knowledge_base",
  "visibility": "private"
}

// Delivery
{
  "entity_id": "private-research-notes",
  "surface_id": "internal-knowledge-base"
}
```

**Invalid - Private to Public Surface:**
```json
// Entity
{
  "id": "private-notes",
  "visibility": "private"
}

// Surface (public)
{
  "id": "youtube-public-channel",
  "direction": "publish",
  "platform": "youtube",
  "visibility": "public"
}

// Delivery - INVALID
{
  "entity_id": "private-notes",
  "surface_id": "youtube-public-channel"  // ❌ CONSTRAINT VIOLATION
}
```

**Valid - Public to Public Surface:**
```json
// Entity
{
  "id": "blog-post-123",
  "visibility": "public"
}

// Surface (public)
{
  "id": "wordpress-blog",
  "direction": "publish",
  "platform": "wordpress",
  "visibility": "public"
}

// Delivery
{
  "entity_id": "blog-post-123",
  "surface_id": "wordpress-blog"
}
```

---

## Approval Status + Delivery Constraints

| Approval Status | Delivery Records | Notes |
|-----------------|------------------|-------|
| `pending` | SHOULD NOT exist | Pending content not yet approved for delivery |
| `approved` | MAY exist | Approved content can be delivered |
| `rejected` | SHOULD NOT exist | Rejected content not approved for use |

**Validation Rules:**
```
IF approval_status = "pending" THEN delivery_count SHOULD = 0
IF approval_status = "approved" THEN delivery_count MAY > 0
IF approval_status = "rejected" THEN delivery_count SHOULD = 0
```

**Examples:**

**Valid - Pending with No Deliveries:**
```json
// Entity
{
  "id": "draft-article",
  "approval_status": "pending"
}

// No delivery records exist ✅
```

**Valid - Approved with Deliveries:**
```json
// Entity
{
  "id": "approved-article",
  "approval_status": "approved"
}

// Delivery
{
  "entity_id": "approved-article",
  "surface_id": "blog",
  "approval_status": "approved"
}
```

**Invalid - Pending with Delivery:**
```json
// Entity
{
  "id": "unfinished-article",
  "approval_status": "pending"
}

// Delivery exists - INVALID
{
  "entity_id": "unfinished-article",
  "surface_id": "blog",
  "approval_status": "approved"  // ❌ CONSTRAINT VIOLATION
}
```

**Valid - Rejected with No Deliveries:**
```json
// Entity
{
  "id": "rejected-content",
  "approval_status": "rejected"
}

// No delivery records exist ✅
```

---

## Asset Type + Filespec Constraints

| Asset Type | Filespec URI Pattern | Required Fields |
|-----------|---------------------|----------------|
| `file` | `file://`, `s3://`, `gs://` | `uri`, `format`, `hash` |
| `link` | `https://` | `uri`, `platform` (optional) |

**Validation Rules:**
```
IF asset_type = "file"
  THEN filespec.uri MUST match (file|s3|gs|gcs)://
  AND filespec.hash SHOULD be present
  AND filespec.format MUST be present

IF asset_type = "link"
  THEN filespec.uri MUST match https?://
  AND filespec.platform MAY be present
```

**Examples:**

**Valid - File Asset:**
```json
{
  "id": "blog-post-markdown",
  "asset_type": "file",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "file:///content/blog/post.md",
    "format": "markdown",
    "hash": "sha256:abc123..."
  }
}
```

**Valid - Link Asset:**
```json
{
  "id": "youtube-video-reference",
  "asset_type": "link",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "https://youtube.com/watch?v=xyz",
    "platform": "youtube"
  }
}
```

**Invalid - File with HTTP URI:**
```json
{
  "id": "invalid-file",
  "asset_type": "file",
  "filespec": {
    "uri": "https://example.com/file.pdf"  // ❌ CONSTRAINT VIOLATION
  }
}
```

**Invalid - Link with file:// URI:**
```json
{
  "id": "invalid-link",
  "asset_type": "link",
  "filespec": {
    "uri": "file:///local/path.txt"  // ❌ CONSTRAINT VIOLATION
  }
}
```

---

## Delivery Role Constraints

| Role | Constraint | Notes |
|------|-----------|-------|
| `original` | AT MOST 1 per entity | The first/primary publication |
| `syndication` | 0 to N per entity | Republications to other surfaces |

**Validation Rules:**
```
FOR EACH entity:
  COUNT(deliveries WHERE role = "original") <= 1

IF delivery.role = "syndication"
  THEN SHOULD exist delivery WHERE role = "original"
    OR original publication is external/implied
```

**Examples:**

**Valid - One Original:**
```json
// Entity
{
  "id": "blog-post-123"
}

// Original delivery
{
  "entity_id": "blog-post-123",
  "surface_id": "wordpress-blog",
  "role": "original"
}
```

**Valid - Original + Syndications:**
```json
// Entity
{
  "id": "blog-post-123"
}

// Original delivery
{
  "entity_id": "blog-post-123",
  "surface_id": "wordpress-blog",
  "role": "original"
}

// Syndication 1
{
  "entity_id": "blog-post-123",
  "surface_id": "medium",
  "role": "syndication"
}

// Syndication 2
{
  "entity_id": "blog-post-123",
  "surface_id": "linkedin",
  "role": "syndication"
}
```

**Invalid - Multiple Originals:**
```json
// Entity
{
  "id": "blog-post-123"
}

// Original delivery 1
{
  "entity_id": "blog-post-123",
  "surface_id": "wordpress-blog",
  "role": "original"
}

// Original delivery 2 - INVALID
{
  "entity_id": "blog-post-123",
  "surface_id": "medium",
  "role": "original"  // ❌ CONSTRAINT VIOLATION - second original
}
```

**Valid - Syndication Only (External Original):**
```json
// Entity (original was published externally)
{
  "id": "guest-post-republished"
}

// Syndication to your platform (original elsewhere)
{
  "entity_id": "guest-post-republished",
  "surface_id": "your-blog",
  "role": "syndication"
}
```

---

## Composite Constraints

Some constraints involve multiple attributes working together.

### Draft Constraint (Composite)

**Rule:** Content with `approval_status=pending` should also have `visibility=private`

**Rationale:** Drafts are by definition not ready for public consumption.

**Validation Rule:**
```
IF approval_status = "pending"
  THEN visibility SHOULD = "private"
```

**Valid:**
```json
{
  "id": "draft-content",
  "approval_status": "pending",
  "visibility": "private"  // ✅ Correct
}
```

**Invalid:**
```json
{
  "id": "public-draft",
  "approval_status": "pending",
  "visibility": "public"  // ❌ CONSTRAINT VIOLATION
}
```

### Private Content Publishing Constraint

**Rule:** Private content can only be delivered to private/internal surfaces

**Validation Rule:**
```
IF entity.visibility = "private" AND delivery exists
  THEN delivery.surface.visibility MUST = "private"
```

See [Visibility + Surface Direction Constraints](#visibility--surface-direction-constraints) above for full details.

---

## Enforcement Strategies

### Database-Level Enforcement

Constraints that can be enforced at the database level:

```sql
-- Example: Prevent multiple original deliveries
CREATE UNIQUE INDEX idx_one_original_per_entity
ON deliveries (entity_id)
WHERE role = 'original';

-- Example: Check valid approval_status values
ALTER TABLE entities
ADD CONSTRAINT chk_valid_approval_status
CHECK (approval_status IN ('pending', 'approved', 'rejected'));

-- Example: Check asset_type values
ALTER TABLE entities
ADD CONSTRAINT chk_valid_asset_type
CHECK (asset_type IN ('file', 'link'));
```

### Application-Level Validation

Constraints that require application logic:

```python
def validate_visibility_surface_constraint(entity, delivery, surface):
    """Validate private content only goes to private surfaces."""
    if entity.visibility == "private" and surface.direction == "publish":
        if surface.visibility != "private":
            raise ValidationError(
                f"Private entity {entity.id} cannot be published to "
                f"public surface {surface.id}"
            )

def validate_pending_no_deliveries(entity, deliveries):
    """Validate pending content has no delivery records."""
    if entity.approval_status == "pending" and len(deliveries) > 0:
        raise ValidationError(
            f"Pending entity {entity.id} should not have delivery records"
        )

def validate_filespec_uri_pattern(entity):
    """Validate filespec URI matches asset_type."""
    if entity.asset_type == "file":
        if not re.match(r"^(file|s3|gs|gcs)://", entity.filespec.uri):
            raise ValidationError(
                f"File asset {entity.id} must have file:// or cloud storage URI"
            )
    elif entity.asset_type == "link":
        if not re.match(r"^https?://", entity.filespec.uri):
            raise ValidationError(
                f"Link asset {entity.id} must have HTTP(S) URI"
            )
```

---

## Related Documentation

- [publication-patterns.md](publication-patterns.md) - Workflow patterns using these constraints
- [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) - Attribute definitions
- [schema_v2.sql](../../schemas/schema_v2.sql) - Database implementation

---

**Document Status:** Active | **Next Review:** 2025-12-23
**Maintainer:** SemOps Schema Team
**Change Process:** All constraint updates require schema governance review and database migration planning
