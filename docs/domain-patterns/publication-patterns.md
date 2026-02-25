# Publication Patterns

> Content publication workflows that combine provenance, visibility, status, and publication surfaces.

**Version:** 2.0.0
**Status:** active
**Last Updated:** 2025-11-23

---

## Overview

Publication patterns represent meaningful combinations of entity attributes that define specific business workflows in the SemOps publishing domain. These patterns combine `provenance`, `visibility`, `approval_status`, and publication surfaces to model how content moves through the system.

Each pattern includes:
- **Definition** - What this pattern represents
- **Attributes** - Required attribute combinations
- **Surface Types** - Where content appears
- **Use Cases** - Real-world scenarios
- **Examples** - Concrete JSON representations

---

## Pattern: Internal Knowledge Curation (3P + Private)

**Definition:** External content collected and made available for internal use in knowledge systems.

**Aggregate Root Attributes:**
- `provenance`: `3p` (created by external party)
- `visibility`: `private` (restricted to internal use)
- `approval_status`: `approved` (finalized and indexed)
- `asset_type`: Typically `link` (external reference) or `file` (downloaded copy)

**Surface Types:**
- Vector databases (RAG systems)
- Internal knowledge repositories
- Personal note systems
- Graph databases

**Use Cases:**
- Research paper added to vector database for RAG retrieval
- YouTube video transcript indexed for internal search
- Third-party blog post quote saved for future (public) citation
- Academic paper saved for future reference and possible citation

**Example Entity:**
```json
{
  "id": "research-attention-is-all-you-need",
  "asset_type": "link",
  "title": "Attention Is All You Need",
  "provenance": "3p",
  "approval_status": "approved",
  "visibility": "private",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "https://arxiv.org/abs/1706.03762",
    "platform": "arxiv"
  },
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "research_paper",
    "subject_area": ["AI/ML", "NLP"]
  }
}
```

**Related Edges:**
- May have `curates` edge from your collection entity
- May have `cites` edge from your own content that references it

---

## Pattern: Public Citation (Public 3P Reference)

**Definition:** External content you're referencing or attributing in your public-facing work.

**Attributes:**
- `provenance`: `3p` (created by external party)
- `visibility`: `public` (referenced in public context)
- `approval_status`: `approved` (already published by original author)
- `asset_type`: `link` (reference to external source)

**Surface Types:**
- Your blog posts (via `cites` edge)
- Public documentation
- Educational content
- Published articles

**Use Cases:**
- Research paper cited in blog post
- YouTube video embedded in educational content
- Open source project referenced in technical guide
- External article linked as supporting evidence

**Example with Edge:**
```json
// 3P Entity (the cited content)
{
  "id": "openai-gpt4-technical-report",
  "asset_type": "link",
  "provenance": "3p",
  "approval_status": "approved",
  "visibility": "public",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "https://arxiv.org/abs/2303.08774",
    "platform": "arxiv"
  }
}

// Edge from your blog post
{
  "source_id": "blog-understanding-gpt4",
  "destination_id": "openai-gpt4-technical-report",
  "predicate": "cites",
  "strength": 0.9
}
```

**Key Distinction from Internal Curation:**
- `visibility=public` signals this is part of public-facing attribution
- Often connected via `cites` edge rather than stored for internal retrieval
- May have different licensing/copyright implications

---

## Pattern: Original Publication (Public 1P)

**Definition:** Your original content published publicly for the first time.

**Attributes:**
- `provenance`: `1p` (you created it)
- `visibility`: `public` (intended for public access)
- `approval_status`: `approved` (finalized and released)
- `asset_type`: `file` (you possess the content)

**Delivery Attributes:**
- `role`: `original` (first publication)
- `approval_status`: `approved`
- One delivery per entity with `role=original`

**Surface Types:**
- Your WordPress blog
- Your YouTube channel
- Your GitHub repositories
- Your Medium publication

**Use Cases:**
- Blog post published on your site
- Video uploaded to your channel
- Code repository pushed to GitHub
- Research paper published on your platform

**Example with Delivery:**
```json
// Entity
{
  "id": "blog-post-semantic-operations-intro",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "public",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "file:///content/blog/semantic-ops-intro.md",
    "format": "markdown"
  }
}

// Delivery (original publication)
{
  "id": "delivery-blog-wordpress-001",
  "entity_id": "blog-post-semantic-operations-intro",
  "surface_id": "wordpress-my-blog",
  "role": "original",
  "approval_status": "approved",
  "url": "https://myblog.com/semantic-operations-intro",
  "published_at": "2024-11-15T09:00:00Z"
}
```

---

## Pattern: Content Syndication (Public 1P Republication)

**Definition:** Your content republished to additional platforms after original publication.

**Attributes:**
- `provenance`: `1p` (you created it)
- `visibility`: `public` (public on all surfaces)
- `approval_status`: `approved` (already finalized)

**Delivery Attributes:**
- `role`: `syndication` (cross-posted)
- Multiple deliveries with `role=syndication`
- At least one delivery with `role=original` should exist (or is implied)

**Surface Types:**
- Medium (cross-posting blog)
- LinkedIn (article sharing)
- Dev.to (developer content)
- Hashnode (blog syndication)

**Use Cases:**
- Blog post cross-posted to Medium
- YouTube video shared to LinkedIn
- GitHub project featured on multiple platforms
- Newsletter content republished as blog post

**Example Multi-Surface Delivery:**
```json
// Original delivery
{
  "entity_id": "blog-post-semantic-operations-intro",
  "surface_id": "wordpress-my-blog",
  "role": "original",
  "url": "https://myblog.com/semantic-operations-intro"
}

// Syndication delivery 1
{
  "entity_id": "blog-post-semantic-operations-intro",
  "surface_id": "medium-my-publication",
  "role": "syndication",
  "url": "https://medium.com/@me/semantic-operations-intro"
}

// Syndication delivery 2
{
  "entity_id": "blog-post-semantic-operations-intro",
  "surface_id": "linkedin-my-profile",
  "role": "syndication",
  "url": "https://linkedin.com/articles/semantic-ops"
}
```

**Business Rules:**
- Canonical URL typically points to `role=original` delivery
- Syndication may include attribution/backlink to original
- Timing: syndication usually happens after original publication

---

## Pattern: Private Draft (1P Work in Progress)

**Definition:** Content you're actively developing that isn't ready for publication.

**Attributes:**
- `provenance`: `1p` (you're creating it)
- `visibility`: `private` (internal only)
- `approval_status`: `pending` (not finalized)
- `asset_type`: `file` (you're working on it locally)

**Surface Implications:**
- NO delivery records should exist (drafts aren't delivered)
- May exist in local filesystem or private workspace

**Use Cases:**
- Unfinished blog post
- Script for future video
- Research notes in progress
- Code before first commit

**Example:**
```json
{
  "id": "draft-blog-advanced-rag-techniques",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "pending",
  "visibility": "private",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "file:///drafts/advanced-rag.md",
    "format": "markdown"
  },
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "blog_post",
    "word_count": 487
  }
}
```

**State Transitions:**
```
draft → published (when ready to publish)
draft → archived (if abandoned)
```

---

## Pattern: Derived Knowledge Asset (1P from 3P)

**Definition:** Transformations or extractions you created from external source material.

**Attributes:**
- `provenance`: `1p` (YOU created the derivative work)
- `visibility`: `private` OR `public` (depending on use)
- `approval_status`: `approved` (finalized extraction/transformation)
- `asset_type`: `file` (you possess the derivative)

**Required Edge:**
- Edge with `predicate=derived_from` pointing to the 3P source entity

**Use Cases:**
- Transcript you extracted from 3P YouTube video
- Summary you wrote of 3P research paper
- Quote block you extracted from 3P article
- Notes you took on 3P content
- Translation you created from 3P content

**Example - Private Transcript:**
```json
// Your 1P transcript (private)
{
  "id": "transcript-karpathy-intro-to-llms",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "private",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "file:///transcripts/karpathy-llms.txt",
    "format": "txt"
  },
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "transcript",
    "word_count": 12453
  }
}

// Edge to source
{
  "source_id": "transcript-karpathy-intro-to-llms",
  "destination_id": "video-karpathy-intro-to-llms-youtube",
  "predicate": "derived_from",
  "strength": 1.0
}

// Original 3P video
{
  "id": "video-karpathy-intro-to-llms-youtube",
  "asset_type": "link",
  "provenance": "3p",
  "approval_status": "approved",
  "visibility": "public",
  "filespec": {
    "$schema": "filespec_v1",
    "uri": "https://youtube.com/watch?v=...",
    "platform": "youtube"
  }
}
```

**Example - Public Quote:**
```json
// Your extracted quote (used publicly)
{
  "id": "quote-attention-mechanism-explanation",
  "asset_type": "file",
  "provenance": "1p",
  "approval_status": "approved",
  "visibility": "public",
  "metadata": {
    "$schema": "content_metadata_v1",
    "content_type": "quote"
  }
}

// Edge showing derivation
{
  "source_id": "quote-attention-mechanism-explanation",
  "destination_id": "research-attention-is-all-you-need",
  "predicate": "derived_from",
  "strength": 1.0
}

// May also have citation edge from your blog
{
  "source_id": "blog-understanding-transformers",
  "destination_id": "quote-attention-mechanism-explanation",
  "predicate": "uses",
  "strength": 0.8
}
```

**Key Insight:**
- Provenance indicates WHO created THIS artifact (you), NOT the ultimate source
- Edge `derived_from` captures the lineage and attribution
- Derivative can have different visibility than source (e.g., private notes on public content)

---

## Pattern: Collaborative Content (2P Partnership)

**Definition:** Content created through direct collaboration with partners or colleagues.

**Attributes:**
- `provenance`: `2p` (collaborative creation)
- `visibility`: `public` OR `private` (depends on agreement)
- `approval_status`: Can be `pending`, `approved`, or `rejected`
- `asset_type`: `file` (jointly possessed)

**Attribution Attributes:**
- `attribution.authors`: List of all collaborators
- `attribution.organization`: Shared organization if applicable

**Use Cases:**
- Co-authored research paper
- Joint blog post
- Collaborative GitHub repository
- Shared documentation project

**Example:**
```json
{
  "id": "paper-collaborative-ai-research",
  "asset_type": "file",
  "provenance": "2p",
  "approval_status": "approved",
  "visibility": "public",
  "attribution": {
    "$schema": "attribution_v2",
    "creator": ["You", "Partner A", "Partner B"],
    "organization": "Research Lab",
    "rights": "CC-BY-4.0"
  }
}
```

**Ownership Implications:**
- Shared or negotiated rights
- Assume permission for use given collaborative relationship
- May require coordination for updates or republication

---

## Pattern Evolution

As the domain evolves, new patterns may emerge. Document them here following this structure:

### Pattern Template

```markdown
### Pattern: [Pattern Name]

**Definition:** [What this pattern represents]

**Attributes:**
- `attribute_name`: value (description)
- ...

**Surface/Delivery/Edge Specifics:** (if applicable)

**Use Cases:**
- Use case 1
- Use case 2

**Example:**
[JSON example]

**Business Rules:** (if any special constraints)
```

---

## Related Documentation

- [dam-foundations.md](dam-foundations.md) - Domain context and industry alignment
- [reference-entity-constraints.md](reference-entity-constraints.md) - Validation rules for attribute combinations
- [content-lifecycle-states.md](content-lifecycle-states.md) - State transitions for these patterns
- [reference-edge-relationships.md](reference-edge-relationships.md) - Edge patterns connecting entities
- [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) - Core term definitions

---

**Document Status:** Active | **Next Review:** 2025-12-23
**Maintainer:** SemOps Schema Team
**Change Process:** All updates require schema governance review and should be reflected in validation rules
