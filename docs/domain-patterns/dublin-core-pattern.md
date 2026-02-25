# Dublin Core Pattern

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-01-10
> **Provenance:** 3P
> **Derives From:** Dublin Core Metadata Initiative

Dublin Core metadata elements for content attribution, discovery, and interoperability.

---

## Overview

**[Dublin Core](https://www.dublincore.org/)** is an ISO standard (ISO 15836) for cross-domain resource description. SemOps adopts Dublin Core for basic metadata that enables content discovery, attribution, and interoperability with external systems.

Dublin Core provides:
- **Creator attribution** - Who created the content
- **Date tracking** - When content was created/modified
- **Rights management** - Licensing and usage rights
- **Format identification** - Content types and formats
- **Identifier management** - Persistent identifiers (DOIs, URIs)


## Adoption Context

- **Original Capability:** `publishing-pipeline` — standardized content metadata
- **Problem Statement:** Content metadata needs standardization across publishing surfaces — title, author, date, subject, format. Inconsistent metadata prevents search, discovery, and cross-referencing.
- **Selection Criteria:** Dublin Core Metadata Initiative (DCMI) — 15 core metadata fields applicable to any resource type. Adopted for the metadata schema on published content entities.
- **ADR:** —

## Dublin Core Elements in SemOps

### Core Element Mapping

| DC Element | SemOps Field | Purpose |
|------------|--------------|---------|
| `dc:title` | `entity.title` | Content title |
| `dc:creator` | `entity.author` / `metadata.creator` | Attribution |
| `dc:date` | `entity.created_at`, `entity.updated_at` | Temporal tracking |
| `dc:description` | `entity.description` / `metadata.summary` | Content summary |
| `dc:type` | `entity.content_type` | Content classification |
| `dc:format` | `entity.filespec.mime_type` | File format |
| `dc:identifier` | `entity.id`, `entity.slug` | Unique identifiers |
| `dc:source` | Edge: `derived_from` | Source attribution |
| `dc:relation` | Edge predicates | Related resources |
| `dc:rights` | `metadata.license` | Usage rights |
| `dc:language` | `metadata.language` | Content language |

### Extended Elements (dcterms)

| DCTerms Element | SemOps Field | Purpose |
|-----------------|--------------|---------|
| `dcterms:created` | `entity.created_at` | Creation timestamp |
| `dcterms:modified` | `entity.updated_at` | Last modification |
| `dcterms:isPartOf` | Edge: `part_of` | Compositional hierarchy |
| `dcterms:references` | Edge: `cites` | Citations and references |
| `dcterms:isVersionOf` | Edge: `version_of` | Version lineage |

---

## Example Usage

### Entity Metadata

```yaml
entity:
  id: blog-post-semantic-coherence
  title: "Understanding Semantic Coherence"        # dc:title
  content_type: blog_post                          # dc:type
  created_at: 2026-01-10T10:00:00Z                 # dcterms:created
  updated_at: 2026-01-10T14:30:00Z                 # dcterms:modified

metadata:
  creator: "Tim Mitchell"                          # dc:creator
  summary: "A deep dive into measuring..."         # dc:description
  license: "CC-BY-4.0"                             # dc:rights
  language: "en"                                   # dc:language
```

### Frontmatter Format

```yaml
---
title: "Understanding Semantic Coherence"
author: "Tim Mitchell"
date: 2026-01-10
description: "A deep dive into measuring shared meaning across systems"
license: CC-BY-4.0
---
```

---

## Attribution and Provenance

Dublin Core's source and relation elements integrate with SemOps's edge predicates for rich provenance tracking:

### Source Attribution (dc:source → derived_from)

When content is derived from external sources:

```yaml
# Your transcript of an external video
entity:
  id: transcript-karpathy-llms
  content_type: transcript
  provenance: 1p                    # You created the transcript

relationships:
  - predicate: derived_from         # dc:source equivalent
    target_id: video-karpathy-youtube
    strength: 1.0
```

### Citation References (dcterms:references → cites)

When content references other works:

```yaml
relationships:
  - predicate: cites                # dcterms:references
    target_id: paper-attention
    strength: 0.9
```

---

## Rights and Licensing

Dublin Core's rights element maps to license metadata:

| License | Code | Use Case |
|---------|------|----------|
| Creative Commons Attribution | `CC-BY-4.0` | Open sharing with attribution |
| All Rights Reserved | `ARR` | Full copyright protection |
| Public Domain | `CC0` | No restrictions |
| MIT License | `MIT` | Code/documentation |

```yaml
metadata:
  license: "CC-BY-4.0"
  rights_holder: "Tim Mitchell"
  rights_note: "Attribution required for derivative works"
```

---

## Interoperability

Dublin Core enables interoperability with external systems:

### Schema.org Mapping

```json
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "Understanding Semantic Coherence",
  "author": {
    "@type": "Person",
    "name": "Tim Mitchell"
  },
  "datePublished": "2026-01-10",
  "license": "https://creativecommons.org/licenses/by/4.0/"
}
```

### OAI-PMH Export

For academic/research content, Dublin Core enables OAI-PMH harvesting:

```xml
<oai_dc:dc>
  <dc:title>Understanding Semantic Coherence</dc:title>
  <dc:creator>Tim Mitchell</dc:creator>
  <dc:date>2026-01-10</dc:date>
  <dc:type>Text</dc:type>
  <dc:format>text/html</dc:format>
</oai_dc:dc>
```

---

## Use Cases

### 1. Content Attribution
Every piece of content has clear creator, date, and rights information.

### 2. External Integration
Dublin Core metadata enables content syndication to platforms expecting DC-compliant metadata.

### 3. Discovery
Standard metadata fields enable consistent search and filtering across content types.

### 4. Citation Generation
DC elements provide the data needed for proper academic citations.

---

## Related Documentation

- [edge-predicates.md](edge-predicates.md) - Relationship predicates (dcterms mapping)
- [provenance-and-lineage.md](provenance-and-lineage.md) - Source tracking
- [dam-foundations.md](dam-foundations.md) - Domain context

### Standards
- [Dublin Core Metadata Initiative](https://www.dublincore.org/)
- [DCMI Metadata Terms](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/)
- [ISO 15836](https://www.iso.org/standard/71339.html) - Dublin Core Standard

---

## 3P Foundation

| DC Element | SemOps Adoption | Our Extension |
|------------|-----------------|---------------|
| Core 15 elements | Direct mapping | None needed |
| dcterms relations | Edge predicates | Strength weighting |
| Rights | metadata.license | Rights holder tracking |
| Source | derived_from edge | 1P/2P/3P provenance context |

**Key Extension:** Dublin Core's flat relation model (`dc:source`, `dc:relation`) is enhanced with typed predicates (`derived_from`, `cites`, `version_of`) and provenance context (1P/2P/3P).

