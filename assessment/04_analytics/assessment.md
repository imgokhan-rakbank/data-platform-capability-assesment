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
| 1 | Is there a data marketplace or data catalog that surfaces available datasets to business consumers? | No marketplace exists. Informatica Data Governance and Catalog tool is planned but not integrated. |
| 2 | What tool powers the marketplace (e.g., Collibra Marketplace, Alation, Atlan, Azure Purview, custom portal)? | None in use. Informatica planned. |
| 3 | How are datasets published to the marketplace – manually by engineers, or through automated pipeline integration? | Not applicable; no marketplace. |
| 4 | Can consumers request access to datasets directly through the marketplace? Is the approval workflow automated? | Not applicable. |
| 5 | How are datasets rated, reviewed, or certified within the marketplace? | Not applicable. |
| 6 | Is data usage analytics available (most accessed datasets, popular queries, consumer counts)? | Not available. |
| 7 | How are sensitive or restricted datasets managed in the marketplace (visibility, access tiers)? | Not applicable. |
| 8 | Is the marketplace kept up to date? What is the process for removing stale or deprecated datasets? | Not applicable. |
| 9 | Can external or third-party data also be listed in the marketplace? | Not applicable. |
| 10 | What is the adoption rate among business consumers? Are there feedback loops to improve the marketplace? | Not applicable. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | None for marketplace; Power BI for reporting. |
| Vendor / Version | Microsoft Power BI; Informatica (planned, not integrated) |
| Number of Published Datasets | None in marketplace. |
| Number of Active Consumers | None in marketplace. |
| Known Gaps / Limitations | No marketplace, no integration, no process or standards. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or standards for marketplace or analytics. |
| Tooling Maturity | 2 | Power BI is used for reporting; Informatica planned but not integrated. |
| People / Skills | 2 | Some Power BI skills; no marketplace or catalog skills. |
| Automation Level | 1 | No automation for analytics or marketplace. |
| **Sub-Area Overall** | 1 | Analytics is ad hoc, no marketplace, no standards, no automation. |

---

## 4.2 Data Visualization

Data visualization covers the creation, management, and governance of dashboards, reports, and visual analytics delivered to business users.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What BI / visualization tools are in use (e.g., Power BI, Tableau, Looker, Qlik, Grafana, Superset)? Is there a standardized tool? | Power BI is used for reporting. |
| 2 | Is there a defined process for publishing, certifying, and governing dashboards (e.g., certified vs. exploratory)? | No process exists. |
| 3 | Are there semantic layer or metric definitions that ensure consistency across reports (e.g., single definition of "revenue")? | No standards or definitions. |
| 4 | How is report performance managed (caching, dataset optimization, incremental refresh)? | No formal management. |
| 5 | Is row-level security or column-level masking applied in visualization tools to enforce data access policies? | Not consistently applied. |
| 6 | Is there a process for managing the report lifecycle (review, archival, decommissioning of unused reports)? | No process exists. |
| 7 | Are dashboard usage metrics tracked (views, users, refresh failures)? | Not tracked. |
| 8 | How are dashboard development standards maintained (naming conventions, color palettes, UX guidelines)? | No standards exist. |
| 9 | Are mobile or embedded analytics supported? | Not formally supported. |
| 10 | What is the level of self-service – can business users create their own reports without IT involvement? | IT assistance is required for report generation. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Power BI |
| Vendor / Version | Microsoft Power BI |
| Number of Published Dashboards / Reports | Not tracked. |
| Number of Active Users | Not tracked. |
| Known Gaps / Limitations | No process, standards, or tracking; IT required for reporting. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or standards for visualization/reporting. |
| Tooling Maturity | 2 | Power BI is available but not fully leveraged. |
| People / Skills | 2 | Some Power BI skills; no standards or enablement. |
| Automation Level | 1 | No automation for reporting or dashboard management. |
| **Sub-Area Overall** | 1 | Visualization is ad hoc, no standards, no automation, IT-dependent. |

---

## 4.3 Exploratory Data Analysis (EDA)

EDA covers ad-hoc investigation of data by analysts and data scientists to discover patterns, test hypotheses, and generate insights.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What tools are used for EDA (e.g., Jupyter notebooks, Databricks notebooks, SageMaker Studio, RStudio, Hex)? | Sandbox environment is ready for EDA, but no users or use cases are onboarded. |
| 2 | Is there a managed notebook environment that controls access, compute, and data connectivity? | Sandbox exists but not used. |
| 3 | How do analysts access data for exploration – direct database access, sandboxed datasets, or curated samples? | Not in use; no users onboarded. |
| 4 | Are notebooks version-controlled and shared within teams? Is there a collaboration platform? | Not applicable. |
| 5 | Is there guidance or guardrails to prevent analysts from accessing sensitive production data during exploration? | Not applicable. |
| 6 | How are exploratory findings promoted into production dashboards, data products, or ML models? | Not applicable. |
| 7 | Are compute resources for EDA workloads managed (auto-shutdown of idle environments, quota enforcement)? | Not applicable. |
| 8 | Is there a library/package management process for analyst environments to ensure reproducibility? | Not applicable. |
| 9 | Are there sandboxed data environments that mirror production data without exposing PII? | Not applicable. |
| 10 | How is knowledge sharing around EDA findings encouraged across teams (notebooks published, wikis, demos)? | Not applicable. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Sandbox (not used) |
| Vendor / Version | N/A |
| Number of Active Analysts / Data Scientists | 0 |
| Known Gaps / Limitations | Sandbox exists but not used; no EDA activity. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or usage for EDA. |
| Tooling Maturity | 2 | Sandbox is available but not adopted. |
| People / Skills | 1 | No active users or enablement for EDA. |
| Automation Level | 1 | No automation for EDA. |
| **Sub-Area Overall** | 1 | EDA is not practiced; sandbox is unused. |

---

## 4.4 Self-Service Analytics

Self-service analytics covers the ability of business users to independently answer data questions without relying on the data engineering or BI team.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are business users able to create their own reports and dashboards without IT assistance? | No; IT assistance is required. |
| 2 | Is there a semantic or business layer (e.g., Looker Explores, Power BI datasets, Tableau data sources) enabling non-technical users to query data safely? | No semantic/business layer for self-service. |
| 3 | What training and enablement programmes are in place to build self-service analytics skills? | None. |
| 4 | How is data quality and accuracy assured for self-service content (certified datasets, metadata, owner labels)? | Not assured; no process. |
| 5 | Are natural language query (NLQ) or AI-assisted analytics capabilities available? | Not available. |
| 6 | How is sprawl of self-service content managed (content governance, archival policies)? | Not managed. |
| 7 | What percentage of business reporting is created self-service vs. delivered by the BI team? | 0% self-service; all delivered by IT/BI team. |
| 8 | Are there communities of practice or data champions supporting self-service adoption? | None. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Power BI |
| Vendor / Version | Microsoft Power BI |
| Self-Service Adoption Rate | 0% |
| Known Gaps / Limitations | No self-service, no enablement, no governance. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or enablement for self-service analytics. |
| Tooling Maturity | 2 | Power BI is available but not used for self-service. |
| People / Skills | 1 | No enablement or skills for self-service analytics. |
| Automation Level | 1 | No automation for self-service analytics. |
| **Sub-Area Overall** | 1 | No self-service analytics; all reporting is IT-driven. |
