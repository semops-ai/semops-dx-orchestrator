# Experience Taxonomy Pattern

> Standard taxonomies for classifying professional experience across multiple dimensions: customer type, industry, business model, service model, and product domain.

**Version:** 1.1.0
**Status:** active
**Last Updated:** 2025-12-31
**Provenance:** 1P
**Derives From:** DAM, SKOS

## Overview

This pattern defines standardized vocabularies for classifying work experience in a dimensional model. These taxonomies enable:

- **Dimensional analysis**: Calculate duration by Customer Type, Industry, Role, etc.
- **Pattern matching**: Match experience against job requirements
- **Composition**: Select relevant experience atoms for tailored outputs
- **Interoperability**: Use industry-standard terms for ATS/LinkedIn compatibility

**Primary Use Case:** Resume/portfolio schema in `semops-sites` repo, with potential reuse in Brand relationship CRM.

---

## Taxonomy Dimensions

### 1. Customer Type (Who you sell to)

Standard business model classification for target customer.

| ID | Name | Description | Example |
|----|------|-------------|---------|
| `b2b` | Business-to-Business | Selling to other businesses | Azure, AWS, Salesforce |
| `b2c` | Business-to-Consumer | Direct to end consumers | Netflix, Fire TV, Kindle |
| `b2b2c` | Business-to-Business-to-Consumer | Platform enabling B2C | Roku (content partners → consumers) |
| `b2g` | Business-to-Government | Government/public sector | Enterprise contracts, GSA |
| `d2c` | Direct-to-Consumer | Brand selling directly | Kindle Direct Publishing |
| `c2c` | Consumer-to-Consumer | Platform for consumer transactions | eBay, Etsy, Craigslist |

**Primary set for resume:** `b2b`, `b2c`, `b2b2c`, `b2g`

**Sources:**
- [Wikipedia - Types of E-commerce](https://en.wikipedia.org/wiki/Types_of_e-commerce)
- [KIBO Commerce - Commerce Landscape](https://kibocommerce.com/blog/commerce-landscape-b2g-b2b-b2b2c-d2c-b2c/)

---

### 2. Service Model (How software is delivered)

NIST cloud computing service model taxonomy.

| ID | Name | Description | Customer Manages | Provider Manages |
|----|------|-------------|------------------|------------------|
| `saas` | Software as a Service | Ready-to-use applications | Data, config | Everything else |
| `paas` | Platform as a Service | Development platform | App, data | Runtime, middleware, OS, infra |
| `iaas` | Infrastructure as a Service | Virtualized infrastructure | App, data, runtime, OS | Servers, storage, network |
| `caas` | Containers as a Service | Container orchestration | App, containers | Orchestration, infra |
| `on_premise` | On-Premise | Traditional licensed software | Everything | Nothing |

**Note:** Service model applies to software/cloud products. Hardware products (Fire TV device) have no service model.

**Sources:**
- [IBM - IaaS, PaaS, SaaS](https://www.ibm.com/think/topics/iaas-paas-saas)
- [AWS - Types of Cloud Computing](https://aws.amazon.com/types-of-cloud-computing/)
- [NIST SP 800-145](https://csrc.nist.gov/publications/detail/sp/800-145/final)

---

### 3. Business Model (How you make money)

Revenue model taxonomy.

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

---

### 4. Industry (Where the company operates)

Three-level hierarchy combining NAICS precision with LinkedIn job-matching relevance.

#### Level 1: Sector (~10-15 broad categories)

| ID | Sector | Description |
|----|--------|-------------|
| `technology` | Technology | Software, platforms, dev tools |
| `media_entertainment` | Media & Entertainment | Content, streaming, publishing |
| `telecommunications` | Telecommunications | Networks, carriers, infrastructure |
| `consumer_electronics` | Consumer Electronics | Devices, hardware products |
| `ecommerce_retail` | E-commerce & Retail | Online/offline retail |
| `cloud_infrastructure` | Cloud & Infrastructure | IaaS, PaaS, data centers |
| `advertising_marketing` | Advertising & Marketing | AdTech, MarTech, agencies |
| `financial_services` | Financial Services | Banking, fintech, payments |
| `enterprise_software` | Enterprise Software | SaaS, business applications |
| `healthcare` | Healthcare | Health tech, medical devices |
| `education` | Education | EdTech, learning platforms |

#### Level 2: Industry (~50-100 specific categories)

| ID | Industry | Parent Sector | NAICS Ref |
|----|----------|---------------|-----------|
| `streaming_ott` | Streaming / OTT | media_entertainment | 516210 |
| `connected_tv` | Connected TV / Smart TV | consumer_electronics | 334310 |
| `digital_books` | Digital Books / Publishing | media_entertainment | 511130 |
| `cloud_platform` | Cloud Platform (PaaS) | cloud_infrastructure | 518210 |
| `communications_platform` | Communications Platform | telecommunications | 517311 |
| `digital_music` | Digital Music | media_entertainment | 512290 |
| `search_engines` | Search Engines | technology | 519290 |
| `adtech` | Advertising Technology | advertising_marketing | 541890 |
| `video_games` | Video Games | media_entertainment | 511210 |
| `social_media` | Social Media | technology | 519130 |

#### Level 3: Vertical/Niche (optional specificity)

| ID | Vertical | Parent Industry |
|----|----------|-----------------|
| `voice_assistant` | Voice / Alexa | connected_tv |
| `video_on_demand` | VOD / SVOD / AVOD | streaming_ott |
| `ebook_reader` | E-reader / Kindle | digital_books |
| `real_time_comms` | Real-time Communications (WebRTC) | communications_platform |

**Reference Systems:**
- **NAICS** (North American Industry Classification System) - 6-digit government standard
- **LinkedIn Industries V2** - 434 codes, most relevant for job matching
- **GICS** (Global Industry Classification Standard) - Finance/investing focus

**Sources:**
- [Census.gov NAICS](https://www.census.gov/naics/)
- [LinkedIn Industry Codes V2](https://learn.microsoft.com/en-us/linkedin/shared/references/reference-tables/industry-codes-v2)
- [Wikipedia - Industry Classification](https://en.wikipedia.org/wiki/Industry_classification)

---

### 5. Product Domain (What capability you built)

Cross-cutting problem spaces that span industries. Unlike Industry (where the company operates), Product Domain describes what you owned/built.

| ID | Domain | Description | Example Products |
|----|--------|-------------|------------------|
| `search` | Search | Query-based finding (keyword, semantic, NLP) | HotBot, Fire TV search, Elasticsearch |
| `discovery` | Discovery | Algorithmic content surfacing, recommendations | Fire TV home, Netflix recs, Spotify Discover |
| `personalization` | Personalization | User-tailored experiences, A/B testing | Kindle reading, Amazon homepage |
| `browse` | Browse & Navigation | Categorical exploration, taxonomy-driven UX | Fire TV browse, MP3.com genres |
| `ml_ai` | ML / AI | Machine learning systems, models, MLOps | Recommendation engines, fraud detection |
| `data_platform` | Data Platform | Pipelines, data lakes, ETL, infrastructure | Azure Data, Snowflake, Fire TV analytics |
| `analytics_bi` | Analytics / BI | Reporting, dashboards, product insights | IODA dashboard, Amplitude, Looker |
| `content_ingestion` | Content Ingestion | Metadata, cataloging, enrichment | Music/video catalog systems |
| `voice_conversational` | Voice / Conversational | NLU, voice UX, chatbots | Fire TV Alexa, Siri, Google Assistant |
| `advertising` | Advertising | Ad serving, targeting, yield optimization | HotBot ads, DFP, programmatic |
| `real_time_comms` | Real-time Comms | WebRTC, video/audio, messaging | Azure Comms, Twilio, Zoom |
| `payments_commerce` | Payments / Commerce | Checkout, transactions, billing | Stripe, Square, Amazon payments |
| `internationalization` | Internationalization | i18n, localization, multi-market launch | Fire TV 40+ countries |
| `identity_auth` | Identity & Auth | Login, SSO, permissions, user management | Auth0, Okta, Cognito |
| `developer_platform` | Developer Platform | APIs, SDKs, documentation, dev experience | Azure SDKs, Twilio APIs |

#### Key Distinction: ML/AI vs. ML-Powered Features

| Tag | Meaning | Example |
|-----|---------|---------|
| `ml_ai` | Worked *on* ML systems (models, training, MLOps) | Built recommendation model |
| `personalization` + `discovery` | Worked *with* ML (PM driving ML-powered features) | Owned personalization roadmap |

Most Product Manager roles are the latter - driving ML-powered product features, not building the models.

---

## Schema Implementation

### Dimension Tables

```sql
-- Customer Type dimension
CREATE TABLE resume_customer_type (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Service Model dimension
CREATE TABLE resume_service_model (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Business Model dimension
CREATE TABLE resume_business_model (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Industry dimension (hierarchical)
CREATE TABLE resume_industry (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  level INTEGER NOT NULL CHECK (level IN (1, 2, 3)),  -- 1=sector, 2=industry, 3=vertical
  parent_id TEXT REFERENCES resume_industry(id),
  naics_code TEXT,
  linkedin_code TEXT,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Product Domain dimension
CREATE TABLE resume_product_domain (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Bridge Tables (Job → Dimension)

```sql
-- Job → Customer Type (with percentage allocation)
CREATE TABLE resume_job_customer (
  job_id TEXT REFERENCES resume_job(id) ON DELETE CASCADE,
  customer_type_id TEXT REFERENCES resume_customer_type(id),
  percentage NUMERIC(3,2) CHECK (percentage > 0 AND percentage <= 1),
  PRIMARY KEY (job_id, customer_type_id)
);

-- Job → Industry
CREATE TABLE resume_job_industry (
  job_id TEXT REFERENCES resume_job(id) ON DELETE CASCADE,
  industry_id TEXT REFERENCES resume_industry(id),
  PRIMARY KEY (job_id, industry_id)
);

-- Job → Product Domain
CREATE TABLE resume_job_product_domain (
  job_id TEXT REFERENCES resume_job(id) ON DELETE CASCADE,
  product_domain_id TEXT REFERENCES resume_product_domain(id),
  PRIMARY KEY (job_id, product_domain_id)
);

-- Job → Business Model
CREATE TABLE resume_job_business_model (
  job_id TEXT REFERENCES resume_job(id) ON DELETE CASCADE,
  business_model_id TEXT REFERENCES resume_business_model(id),
  PRIMARY KEY (job_id, business_model_id)
);
```

---

## Dimension Summary

| Dimension | Type | Cardinality | % Allocation? | Purpose |
|-----------|------|-------------|---------------|---------|
| **Customer Type** | Enum | ~6 | Yes | Who you sell to (B2B/B2C) |
| **Service Model** | Enum | ~5 | No | How product is delivered (SaaS/PaaS) |
| **Business Model** | Enum | ~7 | No | How you make money |
| **Industry** | Hierarchy | ~50-100 | No | Market/sector where company operates |
| **Product Domain** | Flat | ~15-20 | No | Cross-cutting capability you owned |

---

## Example Classifications

| Job | Customer | Service | Business | Industry | Product Domains |
|-----|----------|---------|----------|----------|-----------------|
| Microsoft Azure Comms | b2b | paas | usage_based | communications_platform | real_time_comms, data_platform, analytics_bi, developer_platform |
| Amazon Books | b2c | saas | transactional | digital_books | discovery, personalization, browse, ml_ai |
| Amazon Fire TV | b2c | - | transactional, advertising | connected_tv | search, discovery, personalization, voice_conversational, data_platform, ml_ai, internationalization |
| Roku | b2c, b2b2c | - | marketplace, advertising | connected_tv | search, discovery, personalization |
| IODA | b2b | saas | subscription | digital_music | analytics_bi, data_platform, content_ingestion |
| Wired/HotBot | b2c, b2b | - | advertising | adtech | search, advertising |

---

## Pattern Integration

### With Brand Entity (CRM)

The `resume_customer_type` dimension can be reused for Brand classification:

```sql
-- Brand metadata can include customer_type for company classification
UPDATE brand SET metadata = metadata || '{
  "customer_type": "b2b",
  "industry": "cloud_infrastructure"
}'::jsonb
WHERE brand_type = 'organization';
```

### With Job Intelligence System

These taxonomies enable job-to-experience matching:

```sql
-- Find jobs matching experience in B2B PaaS data platforms
SELECT j.*
FROM resume_job j
JOIN resume_job_customer jc ON j.id = jc.job_id
JOIN resume_job_product_domain jpd ON j.id = jpd.job_id
WHERE jc.customer_type_id = 'b2b'
  AND jpd.product_domain_id = 'data_platform';
```

---

## Related Documentation

- [ADR-0001: Resume Schema Design](https://github.com/semops-ai/semops-sites/blob/main/docs/decisions/ADR-0001-resume-schema-design.md) - Full schema design
- [UBIQUITOUS_LANGUAGE.md](../../schemas/UBIQUITOUS_LANGUAGE.md) - Brand entity definition
- [content-compose-pattern.md](content-compose-pattern.md) - Atom-to-structure composition

---

**Document Status:** Active | **Next Review:** 2026-01-07
**Maintainer:** SemOps Schema Team
**Change Process:** Updates require schema governance review

