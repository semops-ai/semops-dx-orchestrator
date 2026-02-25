# Foundation

> Domain context and theoretical foundations for SemOps semantic operations framework.

**Version:** 2.2.0
**Status:** active
**Last Updated:** 2026-01-10
**Provenance:** 1P
**Derives From:** DAM, CMS, SKOS, Dublin Core, PIM

## Domain Context

SemOps operates in the **digital publishing** domain with extensions into:
- **Content Management Systems (CMS)** - Content lifecycle and versioning
- **Digital Asset Management (DAM)** - Asset storage and retrieval
- **Knowledge Management** - Semantic relationships and graph-based knowledge
- **AI/LLM Operations** - RAG systems, content generation, and agent workflows

See [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) for foundational term definitions.

---

## Multi-Domain Convergence

SemOps's schema represents a **convergence of established patterns** from multiple specialized domains, creating a unified system for knowledge-first digital publishing.

### Primary Domain: Digital Publishing

The overarching domain is **digital publishing** - the entire lifecycle of creating, managing, and distributing content across multiple channels.

### Adopted Industry Patterns

Each pattern is documented in detail in its own file. See linked documentation for implementation specifics.

| Pattern | Documentation | Purpose |
|---------|---------------|---------|
| **DAM** (Digital Asset Management) | [content-lifecycle-states.md](content-lifecycle-states.md) | Approval workflows, asset organization |
| **CMS** (Content Management System) | [content-lifecycle-states.md](content-lifecycle-states.md) | Content lifecycle, visibility, publishing |
| **SKOS** (Knowledge Organization) | [skos-pattern.md](skos-pattern.md) | Concept hierarchies, content metadata |
| **PIM** (Product Information Management) | [pim-pattern.md](pim-pattern.md) | Channel syndication, field mapping |
| **Dublin Core** (Metadata) | [dublin-core-pattern.md](dublin-core-pattern.md) | Attribution, rights, interoperability |
| **PROV-O** (Provenance Ontology) | [edge-predicates.md](edge-predicates.md) | Derivation, lineage, attribution |

```
SemOps Schema (Convergence Model)
┌──────────────────────────────────────────────────────┐
│ Digital Publishing (Umbrella Domain)                 │
├──────────────────────────────────────────────────────┤
│                                                      │
│ ✅ DAM (Digital Asset Management)                   │
│    • Approval workflows (pending/approved/rejected) │
│    • Asset storage and organization                 │
│    • Multi-channel distribution tracking            │
│    • Version control and metadata                   │
│                                                      │
│ ✅ CMS (Content Management System)                  │
│    • Content lifecycle (draft → published)          │
│    • Visibility controls (public/private)           │
│    • Multi-surface publishing                       │
│    • Syndication support                            │
│                                                      │
│ ✅ Knowledge Management / Graph Database            │
│    • Semantic relationships (Edge entity)           │
│    • Typed predicates (cites, derived_from, etc.)   │
│    • Relationship strength weighting                │
│    • Graph-based lineage tracking                   │
│                                                      │
│ ✅ PIM (Product Information Management)             │
│    • Channel syndication patterns                   │
│    • Platform-specific field mapping                │
│    • Centralized source of truth                    │
│                                                      │
│ ✅ AI/LLM Operations (Emerging)                     │
│    • RAG-aware architecture                         │
│    • Agent attribution tracking                     │
│    • Quality scoring for retrieval                  │
│    • Internal surfaces for vector DBs               │
│                                                      │
└──────────────────────────────────────────────────────┘
```

### Unique Innovations

Beyond adopting industry standards, SemOps introduces novel patterns:

**1. Provenance-First Design (1P/2P/3P)**
- Differentiates owned content from referenced content
- Tracks collaborative vs external sources
- Enables derivative work attribution
- **No equivalent in traditional DAM/CMS**

**2. Unified Catalog (Owned + Referenced)**
- Single system for both your content AND external references
- External links cataloged alongside owned assets
- Citations and sources as first-class entities
- **Traditional DAM only manages owned assets**

**3. Dual-Status Model (Enabled by Unified Catalog)**
- **Approval Status** (Entity) - Is it ready?
- **Delivery Status** (Delivery) - Where is it live?
- **Why needed:** The Unified Catalog includes 3P content that's "approved for internal use" but never "delivered" (you don't publish other people's content), and 1P content that can be "approved" but not yet "published"
- Separation enables "approved but not yet delivered" state
- **Matches DAM best practices but rare in CMS**

**4. Derivative Work Lineage**
- Explicit `derived_from` edges for transformations
- Transcripts, quotes, summaries tracked as 1P derivatives from 3P sources
- Multi-hop derivation chains
- **Traditional CMS doesn't track content lineage**

**5. Graph-Native Relationships**
- Rich semantic predicates beyond simple parent/child
- Relationship strength for ranking and filtering
- Bidirectional querying (what cites this? what does this cite?)
- **Traditional DAM/CMS use hierarchical folders, not graphs**

### What This Is NOT

To clarify boundaries:

**Not Pure DAM:**
- DAM lacks knowledge graph relationships
- DAM doesn't track content provenance (1P/2P/3P)
- DAM doesn't model derivative work lineage
- DAM doesn't integrate RAG/AI operations

**Not Pure CMS:**
- CMS doesn't have approval workflows like DAM
- CMS doesn't track external references as entities
- CMS uses hierarchical taxonomies, not semantic graphs
- CMS doesn't distinguish file vs link at schema level

**Not Pure Knowledge Graph:**
- Knowledge graphs don't have publishing workflows
- Knowledge graphs don't track approval states
- Knowledge graphs don't integrate with distribution channels
- Knowledge graphs don't have DAM-style versioning

### The Result: Semantic Content Operations

**What to call this?**

> **"Semantic Content Operations"** or **"Knowledge-First Digital Publishing"**

A system that combines:
- The rigor of DAM (approval workflows, asset management)
- The flexibility of CMS (multi-channel publishing)
- The intelligence of knowledge graphs (semantic relationships)
- The future-readiness of AI operations (RAG, agent integration)

All unified under a provenance-first, graph-native architecture.

---

## W3C Standards Foundation

SemOps is built on W3C semantic web standards to ensure interoperability and long-term viability:

### SKOS (Simple Knowledge Organization System) → `content_metadata`

**Purpose:** Semantic content organization WITHIN entities

[W3C SKOS Reference](https://www.w3.org/TR/skos-reference/)

**SKOS Properties Mapped to content_metadata:**
- `skos:prefLabel` → `metadata.preferred_label`
- `skos:altLabel` → `metadata.alt_labels`
- `skos:definition` → `metadata.definition`
- `skos:broader` → `metadata.broader_concepts`
- `skos:narrower` → `metadata.narrower_concepts`
- `skos:related` → `metadata.related_concepts`
- `skos:scopeNote` → `metadata.scope_note`
- `skos:example` → `metadata.example`

**Note:** Concepts are established through `metadata.primary_concept` field on entities, not as separate entity instances. Multiple entities can reference the same concept via `primary_concept`, creating a concept identity established through usage.

**Example:**
```yaml
metadata:
  $schema: content_metadata_v1
  content_type: github_doc
  primary_concept: semantic-coherence       # What concept this establishes/documents
  preferred_label: "Semantic Coherence"     # skos:prefLabel
  broader_concepts: ["semantic-operations"] # skos:broader
  narrower_concepts: ["semantic-drift"]     # skos:narrower
  definition: "The degree of shared..."     # skos:definition
```

### PROV-O (Provenance Ontology) → Edge Predicates

**Purpose:** Provenance relationships BETWEEN entities

[W3C PROV-O Specification](https://www.w3.org/TR/prov-o/)

**PROV-O Properties Mapped to Edge Predicates:**
- `prov:wasDerivedFrom` → `derived_from`
- `prov:wasQuotedFrom` → `cites`
- `prov:wasRevisionOf` → `version_of`

**Example:**
```yaml
relationships:
  - predicate: derived_from    # prov:wasDerivedFrom
    target_id: source-video
    strength: 1.0
```

See [SKOS_PROVO_MAPPING.md](../SKOS_PROVO_MAPPING.md) for visual architecture diagram.

---

## Industry Pattern Alignment

Beyond W3C standards, SemOps adopts established patterns from Digital Asset Management (DAM) and Content Management System (CMS) industries to ensure compatibility with standard workflows and terminology.

### DAM Pattern: Approval Workflow

**Industry Standard:** Asset approval workflows with states like pending, approved, and rejected are core to DAM systems.

**Sources:**
- [Bynder Asset Workflow](https://support.bynder.com/hc/en-us/articles/360013932879-Asset-Workflow-Assets) - Uses "In Review", "Approved", "Rejected" statuses
- [Adobe Experience Manager](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/dynamicmedia/dynamic-media-open-apis/approve-assets) - Uses "Expired", "Published", "Approved", "Rejected" statuses
- [Acquia DAM (Webdam)](https://www.damsuccess.com/hc/en-us/articles/360000037590-Webdam-Reports) - Tracks "Published", "Scheduled to Publish", "Failed", "Canceled" statuses
- [Brandfolder](https://help.brandfolder.com/hc/en-us/articles/360002467213-Asset-Availability-) - Uses "Pending Admin Review" with availability controls

**Our Implementation:**
- **Entity Attribute:** `approval_status` with values `pending | approved | rejected`
- **Semantic Meaning:** Represents asset readiness and approval state in the catalog
- **Applies To:** All content (1P, 2P, 3P) - differentiates from "published to external surfaces"

**Key Insight:** Approval status (is it ready?) is independent of delivery status (where is it live?). This separation matches DAM best practices.

### DAM Pattern: Multi-Channel Distribution

**Industry Standard:** DAM systems enable content distribution across multiple channels with tracking and analytics.

**Sources:**
- [Pimcore DAM](https://pimcore.com/en/products/digital-asset-management) - "Smart multi-channel publishing across digital, social, mobile, print"
- [Hivo DAM - Content Syndication](https://hivo.co/blog/leveraging-dam-for-content-syndication) - "Strategic sharing of digital assets to various platforms"
- [Hivo DAM - Web Syndication](https://hivo.co/blog/how-dam-facilitates-web-content-syndication) - Version tracking and analytics across channels

**Our Implementation:**
- **Surface Entity:** Platform/channel configuration (YouTube, WordPress, Medium, etc.)
- **Delivery Entity:** Distribution records tracking where and when content was published
- **Role Attribute:** `original | syndication` differentiates primary vs cross-posted content
- **Status Tracking:** Each delivery has independent `planned | queued | published | failed | removed` status

**Key Features:**
- ✅ One asset, multiple deliveries (syndication support)
- ✅ Platform-specific field mapping
- ✅ Version tracking via `source_hash`
- ✅ Analytics metadata per delivery

### DAM Pattern: Asset Lifecycle Management

**Industry Standard:** DAM workflows track assets through creation, approval, distribution, and archival stages.

**Sources:**
- [Frontify DAM Workflows](https://www.frontify.com/en/guide/dam-workflows) - Lifecycle: Creation → Ingestion → Approval → Active → Archived
- [CommPort - What is DAM](https://www.commport.com/what-is-digital-asset-management/) - "Track all versions ensuring only approved content is used"

**Our Implementation:**

```
1P/2P Content Lifecycle:
pending (draft/development) → approved (ready for use) → rejected (not approved)

3P Content Lifecycle:
pending (staged in inbox, needs cataloging) → approved (fully cataloged) → rejected (not relevant)
```

**Delivery Lifecycle (Independent):**
```
planned → queued → published (or failed/removed)
```

**Note:** The dual-status model (approval + delivery) allows content to be "approved and ready" without being "delivered to surfaces yet."

---

## Related Documentation

### 3P Pattern Documentation
- [content-lifecycle-states.md](content-lifecycle-states.md) - DAM/CMS lifecycle patterns
- [skos-pattern.md](skos-pattern.md) - SKOS knowledge organization
- [pim-pattern.md](pim-pattern.md) - PIM channel syndication
- [dublin-core-pattern.md](dublin-core-pattern.md) - Dublin Core metadata
- [edge-predicates.md](edge-predicates.md) - PROV-O provenance relationships

### Schema and Reference
- [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) - Core term definitions
- [SKOS_PROVO_MAPPING.md](../SKOS_PROVO_MAPPING.md) - W3C standards visual mapping
- [publication-patterns.md](publication-patterns.md) - Content workflow patterns
- [README.md](README.md) - Domain patterns overview

---

**Document Status:** Active | **Next Review:** 2026-03-01
**Maintainer:** SemOps Schema Team
**Change Process:** All updates require schema governance review

