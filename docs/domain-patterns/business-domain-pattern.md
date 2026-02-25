# Business Domain Pattern

> **Version:** 1.0.0
> **Status:** active
> **Last Updated:** 2026-01-18
> **Provenance:** 3P (aggregation)
> **Derives From:** NIST (Service Model), NAICS/LinkedIn (Industry), KIBO (Customer Type)

## Overview

The Business Domain Pattern provides a reusable framework for classifying the **business context** of any domain using standard dimensions. It enables consistent vocabulary for understanding businesses, products, and markets across consulting, research, product development, and personal career data.

This pattern is one component of a broader reference architecture capability:

```text
Reference Architecture (future capability)
    │
    ├── Business Domain Pattern ← THIS PATTERN
    │       └── Market, Revenue, Capability dimensions
    │
    ├── Technical Architecture Pattern (future)
    │       └── Analytics maturity, data patterns
    │
    └── [Other patterns...]
```

## Adoption Context

- **Original Capability:** `domain-reference-architecture` — reference architectures for domain validation
- **Problem Statement:** Domain model examples are incomplete without reference architectures from existing companies and industries. Business domain classification enables comparative analysis and validates SemOps modeling choices.
- **Selection Criteria:** NIST/NAICS/KIBO industry taxonomies provide proven classification. Applied to architecture modeling so domain patterns can be compared against real-world implementations.
- **ADR:** —

## Business Classification

### Market Dimensions (Who/Where)

#### Customer Type

Standard business model classification for target customer.

| ID | Name | Description | Example |
|----|------|-------------|---------|
| `b2b` | Business-to-Business | Selling to other businesses | Azure, AWS, Salesforce |
| `b2c` | Business-to-Consumer | Direct to end consumers | Netflix, Fire TV, Kindle |
| `b2b2c` | Business-to-Business-to-Consumer | Platform enabling B2C | Roku (content partners → consumers) |
| `b2g` | Business-to-Government | Government/public sector | Enterprise contracts, GSA |
| `d2c` | Direct-to-Consumer | Brand selling directly | Kindle Direct Publishing |
| `c2c` | Consumer-to-Consumer | Platform for consumer transactions | eBay, Etsy, Craigslist |

**Sources:**
- [Wikipedia - Types of E-commerce](https://en.wikipedia.org/wiki/Types_of_e-commerce)
- [KIBO Commerce - Commerce Landscape](https://kibocommerce.com/blog/commerce-landscape-b2g-b2b-b2b2c-d2c-b2c/)

#### Industry

Three-level hierarchy combining NAICS precision with LinkedIn job-matching relevance.

| Level | Cardinality | Description | Example |
|-------|-------------|-------------|---------|
| **Sector** | ~10-15 | Broad categories | `technology`, `media_entertainment`, `financial_services` |
| **Industry** | ~50-100 | Specific categories | `streaming_ott`, `cloud_platform`, `digital_books` |
| **Vertical** | Optional | Niche specificity | `voice_assistant`, `ebook_reader` |

**Reference Systems:**
- **NAICS** (North American Industry Classification System) - 6-digit government standard
- **LinkedIn Industries V2** - 434 codes, most relevant for job matching
- **GICS** (Global Industry Classification Standard) - Finance/investing focus

#### Geography

| Scope | Description | Example |
|-------|-------------|---------|
| `global` | Worldwide operations | Netflix, AWS |
| `regional` | Multi-country (continent/trade bloc) | EU-focused SaaS |
| `local` | Single country/market | Regional media outlet |

---

### Revenue Dimensions (How money flows)

#### Business Model

| ID | Name | Description | Example |
|----|------|-------------|---------|
| `subscription` | Subscription | Recurring fee (monthly/annual) | Netflix, Spotify, SaaS |
| `usage_based` | Usage-Based | Pay per use / API call | Azure, AWS, Twilio |
| `transactional` | Transactional | One-time purchase per item | Fire TV device, Kindle books |
| `freemium` | Freemium | Free tier + paid upgrades | Spotify, Dropbox |
| `advertising` | Advertising | Ad-supported revenue | HotBot, YouTube, Fire TV AVOD |
| `marketplace` | Marketplace | Transaction fees / rev share | Roku channel store, App Store |
| `licensing` | Licensing | Per-seat or enterprise license | Microsoft Office, Oracle |

**Note:** Many products have multiple business models (e.g., Amazon = transactional + subscription + advertising).

#### Service Model

NIST cloud computing service model taxonomy.

| ID | Name | Description | Customer Manages | Provider Manages |
|----|------|-------------|------------------|------------------|
| `saas` | Software as a Service | Ready-to-use applications | Data, config | Everything else |
| `paas` | Platform as a Service | Development platform | App, data | Runtime, middleware, OS, infra |
| `iaas` | Infrastructure as a Service | Virtualized infrastructure | App, data, runtime, OS | Servers, storage, network |
| `caas` | Containers as a Service | Container orchestration | App, containers | Orchestration, infra |
| `on_premise` | On-Premise | Traditional licensed software | Everything | Nothing |

**Note:** Service model applies to software/cloud products. Hardware products have no service model.

**Sources:**
- [IBM - IaaS, PaaS, SaaS](https://www.ibm.com/think/topics/iaas-paas-saas)
- [NIST SP 800-145](https://csrc.nist.gov/publications/detail/sp/800-145/final)

---

### Capability Dimensions (What they do)

#### Product Domain

Cross-cutting problem spaces that span industries. Unlike Industry (where the company operates), Product Domain describes what capability exists.

| ID | Domain | Description | Example Products |
|----|--------|-------------|------------------|
| `search` | Search | Query-based finding (keyword, semantic, NLP) | HotBot, Fire TV search |
| `discovery` | Discovery | Algorithmic content surfacing, recommendations | Fire TV home, Netflix recs |
| `personalization` | Personalization | User-tailored experiences, A/B testing | Kindle reading, Amazon homepage |
| `browse` | Browse & Navigation | Categorical exploration, taxonomy-driven UX | Fire TV browse, genre pages |
| `ml_ai` | ML / AI | Machine learning systems, models, MLOps | Recommendation engines |
| `data_platform` | Data Platform | Pipelines, data lakes, ETL, infrastructure | Azure Data, Snowflake |
| `analytics_bi` | Analytics / BI | Reporting, dashboards, product insights | Amplitude, Looker |
| `content_ingestion` | Content Ingestion | Metadata, cataloging, enrichment | CMS, catalog systems |
| `voice_conversational` | Voice / Conversational | NLU, voice UX, chatbots | Alexa, Siri |
| `advertising` | Advertising | Ad serving, targeting, yield optimization | DFP, programmatic |
| `real_time_comms` | Real-time Comms | WebRTC, video/audio, messaging | Twilio, Zoom |
| `payments_commerce` | Payments / Commerce | Checkout, transactions, billing | Stripe, Square |
| `internationalization` | Internationalization | i18n, localization, multi-market launch | Multi-country launches |
| `identity_auth` | Identity & Auth | Login, SSO, permissions | Auth0, Okta |
| `developer_platform` | Developer Platform | APIs, SDKs, documentation | Azure SDKs, Twilio APIs |
| `publishing` | Publishing | CMS, editorial workflow, content distribution | WordPress, custom CMS |
| `video_streaming` | Video Streaming | OTT, VOD, live streaming | Roku, Fire TV |

---

## Instantiation Template

To apply this pattern to a specific domain, create a classification using this structure:

```yaml
# Domain Classification
subject: "Company/Product/Project Name"

market:
  customer_type: [b2b, b2c, b2b2c, b2g, d2c, c2c]
  industry:
    sector: "technology | media_entertainment | ..."
    industry: "streaming_ott | cloud_platform | ..."
    vertical: "optional niche"  # optional
  geography: "global | regional | local"

revenue:
  business_model: [subscription, usage_based, transactional, freemium, advertising, marketplace, licensing]
  service_model: "saas | paas | iaas | caas | on_premise | null"  # null for hardware

capabilities:
  product_domains: [search, discovery, personalization, ...]
```

---

## Example Instantiations

### Resume/Career (semops-sites)

```yaml
subject: "Professional Experience"

market:
  customer_type: null  # personal data, not a business
  industry: null
  geography: null

# Resume uses these dimensions differently - classifying past jobs
dimensions_used:
  - customer_type  # of companies worked for
  - industry       # of companies worked for
  - product_domain # capabilities built/owned
  - role          # resume-specific
  - skill         # resume-specific
```

See: [Experience Taxonomy Pattern](experience-taxonomy-pattern.md) for the resume-specific instantiation.

### Media Publishing Company (Motorsport Network)

```yaml
subject: "Motorsport Network"

market:
  customer_type: [b2c, b2b2c]  # consumers + content partners
  industry:
    sector: "media_entertainment"
    industry: "digital_publishing"
    vertical: "motorsport"
  geography: "global"

revenue:
  business_model: [advertising, subscription, transactional]
  service_model: null  # media company, not software

capabilities:
  product_domains:
    - publishing          # CMS, editorial
    - video_streaming     # Motorsport.tv
    - discovery           # content recommendations
    - personalization     # user experience
    - advertising         # ad operations
```

### SaaS Company (Generic)

```yaml
subject: "Generic B2B SaaS"

market:
  customer_type: [b2b]
  industry:
    sector: "enterprise_software"
    industry: "vertical_saas"
    vertical: "depends on vertical"
  geography: "regional"  # often starts regional

revenue:
  business_model: [subscription, usage_based]
  service_model: "saas"

capabilities:
  product_domains:
    - core_product       # whatever the SaaS does
    - data_platform      # customer data
    - analytics_bi       # product analytics
    - identity_auth      # user management
```

---

## Integration Points

### Related Patterns

- **Experience Taxonomy Pattern** - Resume/career instantiation of Business Domain
- **Technical Architecture Pattern** (future) - Analytics maturity, data architecture patterns
- **business-analytics-patterns.md** (semops-docs) - Detailed breakdown of BI, Product, Marketing, Customer, Web/Digital analytics

### Usage in Consulting

When analyzing a company or domain:
1. Apply Business Domain Pattern to classify market, revenue, capabilities
2. Use classification to identify applicable Technical Architecture patterns
3. Cross-reference with business-analytics-patterns.md for analytics maturity assessment

---

## Knowledge Base Integration

When ingested to semops-core knowledge base:

- **Entity Type:** `domain-pattern`
- **Visibility:** public
- **Related Patterns:** experience-taxonomy-pattern, dam-pattern, skos-pattern
- **Tags:** business-classification, market-analysis, consulting, reference-architecture

---

## References

- [Experience Taxonomy Pattern](experience-taxonomy-pattern.md) - Resume instantiation
- [ADR-0001: Resume Schema Design](https://github.com/semops-ai/semops-sites/blob/main/docs/decisions/ADR-0001-resume-schema-design.md) - Original research
- [business-analytics-patterns.md](https://github.com/semops-ai/semops-docs/blob/main/docs/Publicv1_Candidates/SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/business-analytics-patterns.md) - Analytics classification (semops-docs)
- [NAICS](https://www.census.gov/naics/) - Industry classification standard
- [NIST SP 800-145](https://csrc.nist.gov/publications/detail/sp/800-145/final) - Cloud service models

