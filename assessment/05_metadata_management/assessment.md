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
| 1 | Is a data catalog tool in place (e.g., Collibra, Alation, Atlan, Azure Purview, DataHub, Apache Atlas)? | Unity Catalog (internal) and Informatica Data Governance & Catalog (enterprise) are in place. |
| 2 | How is metadata harvested – manually, via automated crawlers, or pushed from pipelines? | Unity Catalog is populated internally; Informatica uses automated and AI-assisted discovery for source systems only. No integration or harvesting for data platform. |
| 3 | What percentage of critical data assets are registered in the catalog? | Only some source systems are covered in Informatica; data platform assets are not cataloged. |
| 4 | Does the catalog include business metadata (descriptions, owners, business terms, classifications) in addition to technical metadata? | Informatica includes business metadata for covered source systems; not for data platform. |
| 5 | Is the catalog integrated with source systems, data warehouses, data lakes, and BI tools for automated metadata sync? | No integration between Unity Catalog and Informatica; Informatica covers only some source systems. |
| 6 | How is catalog content kept current? Is there a process to detect and remove stale entries? | No formal process for the data platform; Informatica updates source system metadata. |
| 7 | Are data assets linked to business glossary terms, policies, and sensitive data classifications in the catalog? | Only for source systems in Informatica; not for data platform. |
| 8 | Who is responsible for maintaining catalog content – a central team, domain stewards, or automated tooling? | Data modelers are responsible for models; governance team owns cataloging and quality. |
| 9 | Is the catalog accessible across the organization, and is it promoted as the single source of metadata truth? | Informatica is enterprise-wide but not covering the data platform; Unity Catalog is internal only. |
| 10 | Are catalog usage metrics tracked (searches, views, most accessed assets, unanswered queries)? | Not tracked for the data platform. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Unity Catalog (internal), Informatica DG & Catalog (enterprise, source systems only) |
| Vendor / Version | Databricks Unity Catalog, Informatica |
| Deployment Model | Hybrid (Unity Catalog internal, Informatica enterprise) |
| Number of Cataloged Assets | Only some source systems; data platform not cataloged. |
| Known Gaps / Limitations | No integration, no cataloging for data platform, limited coverage, no usage metrics. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 2 | Cataloging is formalized for source systems but not for the data platform. |
| Tooling Maturity | 2 | Two tools exist but are not integrated; limited coverage. |
| People / Skills | 2 | Governance team and modelers have defined roles, but skills are limited to current scope. |
| Automation Level | 2 | AI-assisted discovery in Informatica; no automation for data platform. |
| **Sub-Area Overall** | 2 | Cataloging is partial, not integrated, and does not cover the data platform. |

---

## 5.2 Data Discovery

Data discovery covers the ability for users to find the data they need quickly and with confidence.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Can users search for data assets using business terms, keywords, tags, or domain classifications? | Only for source systems in Informatica; not for data platform. |
| 2 | Are search results ranked by relevance, trust score, or usage frequency to help users find the best dataset? | Not implemented. |
| 3 | Is natural language search or AI-assisted discovery supported? | AI-assisted discovery is used in Informatica for classification. |
| 4 | Are data asset descriptions, owners, and quality indicators surfaced in search results to aid decision making? | Only for source systems in Informatica. |
| 5 | Can users discover data products, reports, and ML features as well as raw datasets? | Not for data platform; only source systems. |
| 6 | Is there a recommendation engine suggesting related or complementary datasets? | Not available. |
| 7 | How are sensitive or restricted assets surfaced in search (visible but access-restricted, or hidden)? | Not managed for data platform. |
| 8 | How long does it typically take a new analyst to find a dataset they need? Is this measured? | Not measured. |
| 9 | Is the discovery experience integrated into the tools analysts use (e.g., embedded within BI tools or notebooks)? | Not integrated. |
| 10 | Is user search behavior analyzed to improve discoverability and identify gaps in metadata quality? | Not analyzed. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Informatica (source systems only) |
| Vendor / Version | Informatica |
| Known Gaps / Limitations | No discovery for data platform, not integrated, limited to source systems. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 2 | Discovery is formalized for source systems, not for data platform. |
| Tooling Maturity | 2 | Informatica supports AI-assisted discovery, but not for data platform. |
| People / Skills | 2 | Governance team has skills for Informatica, not for platform-wide discovery. |
| Automation Level | 2 | AI-assisted discovery in Informatica; no automation for data platform. |
| **Sub-Area Overall** | 2 | Discovery is limited to source systems, not integrated or platform-wide. |

---

## 5.3 Data Lineage & Impact Analysis

Data lineage covers the tracking of data origin, transformations, and flow across systems. Impact analysis uses lineage to understand the downstream effect of proposed changes.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is end-to-end data lineage captured automatically across pipelines, transformations, and systems? | No end-to-end lineage; only subset of pipelines in Databricks Unity Catalog. |
| 2 | What granularity of lineage is available – table-level, column-level, or row-level? | Table-level and some column-level in Unity Catalog; not comprehensive. |
| 3 | Which tools produce and consume lineage information (e.g., dbt, Spark, ADF, Airflow, OpenLineage)? | Unity Catalog for Databricks pipelines only. |
| 4 | Is lineage available in the data catalog for all critical data assets? | No; only for subset in Unity Catalog. |
| 5 | Can impact analysis be performed – i.e., when a source dataset changes, what downstream assets are affected? | Not available. |
| 6 | Is lineage used during incident investigation to trace data quality issues back to their source? | Not available. |
| 7 | How is lineage kept current as pipelines and systems evolve? | Not managed end-to-end. |
| 8 | Is cross-system lineage available (e.g., from a source database through Kafka, through a transformation, to a BI report)? | Not available. |
| 9 | Can business users view a simplified business lineage (source system → business domain → report), not just technical lineage? | Not available. |
| 10 | Is lineage used as input to regulatory reporting (e.g., BCBS 239, GDPR data mapping)? | Not available. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Unity Catalog (Databricks, subset of pipelines) |
| Lineage Standard Used (e.g., OpenLineage / proprietary) | Proprietary (Unity Catalog) |
| Lineage Granularity (table / column) | Table-level, some column-level |
| Coverage (% of critical pipelines with lineage) | Low; only subset in Databricks |
| Known Gaps / Limitations | No end-to-end lineage, no cross-system or business lineage, not integrated with Informatica. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process for end-to-end or cross-system lineage. |
| Tooling Maturity | 2 | Unity Catalog provides partial lineage; not integrated or comprehensive. |
| People / Skills | 2 | Some skills for Unity Catalog; not for end-to-end lineage. |
| Automation Level | 2 | Partial automation in Unity Catalog; not end-to-end. |
| **Sub-Area Overall** | 1 | Lineage is limited, not integrated, and not end-to-end. |
