# Domain 4: Analytics – Capability Assessment

> **Assessor(s):**  
> **Date:**  
> **Version:**  

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 4.1 Data Marketplace

A data marketplace is a curated, searchable catalog where data consumers can discover, request, and access datasets and data products.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a data marketplace or data catalog that surfaces available datasets to business consumers? | |
| 2 | What tool powers the marketplace (e.g., Collibra Marketplace, Alation, Atlan, Azure Purview, custom portal)? | |
| 3 | How are datasets published to the marketplace – manually by engineers, or through automated pipeline integration? | |
| 4 | Can consumers request access to datasets directly through the marketplace? Is the approval workflow automated? | |
| 5 | How are datasets rated, reviewed, or certified within the marketplace? | |
| 6 | Is data usage analytics available (most accessed datasets, popular queries, consumer counts)? | |
| 7 | How are sensitive or restricted datasets managed in the marketplace (visibility, access tiers)? | |
| 8 | Is the marketplace kept up to date? What is the process for removing stale or deprecated datasets? | |
| 9 | Can external or third-party data also be listed in the marketplace? | |
| 10 | What is the adoption rate among business consumers? Are there feedback loops to improve the marketplace? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Number of Published Datasets | |
| Number of Active Consumers | |
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

## 4.2 Data Visualization

Data visualization covers the creation, management, and governance of dashboards, reports, and visual analytics delivered to business users.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What BI / visualization tools are in use (e.g., Power BI, Tableau, Looker, Qlik, Grafana, Superset)? Is there a standardized tool? | |
| 2 | Is there a defined process for publishing, certifying, and governing dashboards (e.g., certified vs. exploratory)? | |
| 3 | Are there semantic layer or metric definitions that ensure consistency across reports (e.g., single definition of "revenue")? | |
| 4 | How is report performance managed (caching, dataset optimization, incremental refresh)? | |
| 5 | Is row-level security or column-level masking applied in visualization tools to enforce data access policies? | |
| 6 | Is there a process for managing the report lifecycle (review, archival, decommissioning of unused reports)? | |
| 7 | Are dashboard usage metrics tracked (views, users, refresh failures)? | |
| 8 | How are dashboard development standards maintained (naming conventions, color palettes, UX guidelines)? | |
| 9 | Are mobile or embedded analytics supported? | |
| 10 | What is the level of self-service – can business users create their own reports without IT involvement? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Number of Published Dashboards / Reports | |
| Number of Active Users | |
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

## 4.3 Exploratory Data Analysis (EDA)

EDA covers ad-hoc investigation of data by analysts and data scientists to discover patterns, test hypotheses, and generate insights.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What tools are used for EDA (e.g., Jupyter notebooks, Databricks notebooks, SageMaker Studio, RStudio, Hex)? | |
| 2 | Is there a managed notebook environment that controls access, compute, and data connectivity? | |
| 3 | How do analysts access data for exploration – direct database access, sandboxed datasets, or curated samples? | |
| 4 | Are notebooks version-controlled and shared within teams? Is there a collaboration platform? | |
| 5 | Is there guidance or guardrails to prevent analysts from accessing sensitive production data during exploration? | |
| 6 | How are exploratory findings promoted into production dashboards, data products, or ML models? | |
| 7 | Are compute resources for EDA workloads managed (auto-shutdown of idle environments, quota enforcement)? | |
| 8 | Is there a library/package management process for analyst environments to ensure reproducibility? | |
| 9 | Are there sandboxed data environments that mirror production data without exposing PII? | |
| 10 | How is knowledge sharing around EDA findings encouraged across teams (notebooks published, wikis, demos)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Number of Active Analysts / Data Scientists | |
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

## 4.4 Self-Service Analytics

Self-service analytics covers the ability of business users to independently answer data questions without relying on the data engineering or BI team.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are business users able to create their own reports and dashboards without IT assistance? | |
| 2 | Is there a semantic or business layer (e.g., Looker Explores, Power BI datasets, Tableau data sources) enabling non-technical users to query data safely? | |
| 3 | What training and enablement programmes are in place to build self-service analytics skills? | |
| 4 | How is data quality and accuracy assured for self-service content (certified datasets, metadata, owner labels)? | |
| 5 | Are natural language query (NLQ) or AI-assisted analytics capabilities available? | |
| 6 | How is sprawl of self-service content managed (content governance, archival policies)? | |
| 7 | What percentage of business reporting is created self-service vs. delivered by the BI team? | |
| 8 | Are there communities of practice or data champions supporting self-service adoption? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Self-Service Adoption Rate | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
