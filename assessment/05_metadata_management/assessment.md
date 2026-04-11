# Domain 5: Metadata Management – Capability Assessment

> **Assessor(s):**  
> **Date:**  
> **Version:**  

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 5.1 Data Catalog

A data catalog is a centralized inventory of data assets with technical metadata, business context, ownership, and usage information.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data catalog tool in place (e.g., Collibra, Alation, Atlan, Azure Purview, DataHub, Apache Atlas)? | |
| 2 | How is metadata harvested – manually, via automated crawlers, or pushed from pipelines? | |
| 3 | What percentage of critical data assets are registered in the catalog? | |
| 4 | Does the catalog include business metadata (descriptions, owners, business terms, classifications) in addition to technical metadata? | |
| 5 | Is the catalog integrated with source systems, data warehouses, data lakes, and BI tools for automated metadata sync? | |
| 6 | How is catalog content kept current? Is there a process to detect and remove stale entries? | |
| 7 | Are data assets linked to business glossary terms, policies, and sensitive data classifications in the catalog? | |
| 8 | Who is responsible for maintaining catalog content – a central team, domain stewards, or automated tooling? | |
| 9 | Is the catalog accessible across the organization, and is it promoted as the single source of metadata truth? | |
| 10 | Are catalog usage metrics tracked (searches, views, most accessed assets, unanswered queries)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Number of Cataloged Assets | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 5.2 Data Discovery

Data discovery covers the ability for users to find the data they need quickly and with confidence.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Can users search for data assets using business terms, keywords, tags, or domain classifications? | |
| 2 | Are search results ranked by relevance, trust score, or usage frequency to help users find the best dataset? | |
| 3 | Is natural language search or AI-assisted discovery supported? | |
| 4 | Are data asset descriptions, owners, and quality indicators surfaced in search results to aid decision making? | |
| 5 | Can users discover data products, reports, and ML features as well as raw datasets? | |
| 6 | Is there a recommendation engine suggesting related or complementary datasets? | |
| 7 | How are sensitive or restricted assets surfaced in search (visible but access-restricted, or hidden)? | |
| 8 | How long does it typically take a new analyst to find a dataset they need? Is this measured? | |
| 9 | Is the discovery experience integrated into the tools analysts use (e.g., embedded within BI tools or notebooks)? | |
| 10 | Is user search behavior analyzed to improve discoverability and identify gaps in metadata quality? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 5.3 Data Lineage & Impact Analysis

Data lineage covers the tracking of data origin, transformations, and flow across systems. Impact analysis uses lineage to understand the downstream effect of proposed changes.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is end-to-end data lineage captured automatically across pipelines, transformations, and systems? | |
| 2 | What granularity of lineage is available – table-level, column-level, or row-level? | |
| 3 | Which tools produce and consume lineage information (e.g., dbt, Spark, ADF, Airflow, OpenLineage)? | |
| 4 | Is lineage available in the data catalog for all critical data assets? | |
| 5 | Can impact analysis be performed – i.e., when a source dataset changes, what downstream assets are affected? | |
| 6 | Is lineage used during incident investigation to trace data quality issues back to their source? | |
| 7 | How is lineage kept current as pipelines and systems evolve? | |
| 8 | Is cross-system lineage available (e.g., from a source database through Kafka, through a transformation, to a BI report)? | |
| 9 | Can business users view a simplified business lineage (source system → business domain → report), not just technical lineage? | |
| 10 | Is lineage used as input to regulatory reporting (e.g., BCBS 239, GDPR data mapping)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Lineage Standard Used (e.g., OpenLineage / proprietary) | |
| Lineage Granularity (table / column) | |
| Coverage (% of critical pipelines with lineage) | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
