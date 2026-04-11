# Domain 3: Data Quality Management – Capability Assessment

> **Assessor(s):**  
> **Date:**  
> **Version:**  

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 3.1 Data Profiling

Data profiling covers the automated examination of data to understand its structure, content, relationships, and quality characteristics.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data profiling tool in use (e.g., Informatica DQ, Talend DQ, Great Expectations, dbt tests, Atlan, Monte Carlo)? | No framework in use for the data platform. Informatica Data Governance and Catalog exist but only cover some source systems. |
| 2 | What percentage of critical data assets have been profiled? Is profiling done on a scheduled basis? | Very limited profiling; only basic checks on bronze tables. Not scheduled. |
| 3 | What profiling metrics are captured (completeness, uniqueness, nulls, min/max, patterns, distributions, referential integrity)? | Only basic metrics (e.g., null checks) on bronze tables. |
| 4 | Are profiling results stored historically to detect trends or regressions over time? | No historical storage of profiling results. |
| 5 | Are profiling results accessible to data stewards, analysts, and consumers through a portal or catalog? | Not accessible for data platform; Informatica covers only some source systems. |
| 6 | Is profiling automated as part of data ingestion pipelines (profile on arrival)? | No automation. |
| 7 | How are profiling findings acted upon? Is there a workflow to report and remediate issues discovered? | No workflow or remediation process. |
| 8 | Is statistical profiling (outlier detection, value distribution analysis) performed in addition to structural profiling? | Not performed. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | None for data platform; Informatica for some source systems. |
| Vendor / Version | Informatica (limited scope) |
| Deployment Model | On-premises/Hybrid (where Informatica is used) |
| Number of Datasets Profiled | Very few; only some source systems, not data platform. |
| Known Gaps / Limitations | No DQ framework for data platform, only basic bronze checks, Informatica not covering platform, no automation or workflow. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No formal process or framework for data profiling on the data platform. |
| Tooling Maturity | 1 | No platform tooling; Informatica covers only some source systems. |
| People / Skills | 2 | Some skills exist for Informatica, but not for the data platform. |
| Automation Level | 1 | No automation for profiling or DQ checks. |
| **Sub-Area Overall** | 1 | Data profiling is ad hoc, limited, and not automated for the data platform. |

---

## 3.2 DQ Rules Management

DQ rules management covers the definition, cataloging, versioning, and lifecycle management of data quality rules applied to datasets.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a central repository or catalog where all DQ rules are defined, documented, and managed? | No central repository or catalog for DQ rules. |
| 2 | Who is responsible for defining DQ rules – data stewards, engineers, or business stakeholders? Is there a formal process? | No formal process or clear responsibility. |
| 3 | Are DQ rules mapped to business terms, critical data elements (CDEs), or data products? | No mapping exists. |
| 4 | How are DQ rules versioned and promoted through environments (dev/test/prod)? | No versioning or promotion process. |
| 5 | Are there standardized rule categories (completeness, accuracy, consistency, timeliness, uniqueness, validity)? | No standardization of rule categories. |
| 6 | How are rule changes reviewed and approved (peer review, business sign-off)? | No review or approval process. |
| 7 | Is there a mechanism to reuse rules across multiple datasets (parameterized rules, rule templates)? | No reuse mechanism. |
| 8 | How are rules retired or updated when source systems or business definitions change? | No process for retiring or updating rules. |
| 9 | Is there a priority or severity classification for rules (critical / high / medium / low)? | No classification. |
| 10 | How many DQ rules are currently in production, and what is the coverage across critical data domains? | Very few; only basic bronze checks, no coverage for critical domains. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | None for data platform; Informatica for some source systems. |
| Vendor / Version | Informatica (limited scope) |
| Number of Active DQ Rules | Very few; only basic bronze checks. |
| Known Gaps / Limitations | No DQ framework, no central rule management, no coverage for data platform. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or central management for DQ rules. |
| Tooling Maturity | 1 | No tooling for DQ rules on the data platform. |
| People / Skills | 2 | Some skills for Informatica, but not for the platform. |
| Automation Level | 1 | No automation for DQ rule management. |
| **Sub-Area Overall** | 1 | DQ rules management is ad hoc and not formalized for the data platform. |

---

## 3.3 DQ Monitoring

DQ monitoring covers the ongoing, automated execution of DQ rules and the tracking of data quality scores over time.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are DQ checks executed automatically on a scheduled basis or as part of data pipeline execution? | No automation; only basic checks on bronze tables, not scheduled. |
| 2 | Is there a DQ dashboard or scorecard showing quality scores per dataset, domain, or data product? | No dashboard or scorecard exists. |
| 3 | How are DQ failures alerted (email, Slack, PagerDuty, ticketing system)? Is there an on-call rotation? | No alerting or on-call process. |
| 4 | Are quality trends tracked over time (e.g., week-over-week quality score change)? | Not tracked. |
| 5 | Is data quarantined, flagged, or blocked from downstream consumption when quality thresholds are breached? | No quarantine or blocking process. |
| 6 | Are DQ SLAs defined (e.g., "completeness of customer table must be ≥ 98%") and tracked against thresholds? | No SLAs defined or tracked. |
| 7 | How is root cause analysis performed when a DQ issue is detected? Is there tooling support? | No root cause analysis process or tooling. |
| 8 | Are DQ results published to consumers as part of data product metadata? | Not published. |
| 9 | Is DQ monitoring integrated into data pipelines to prevent bad data from propagating downstream? | Not integrated. |
| 10 | What is the current average quality score for critical data domains, and how was it calculated? | Not calculated. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | None for data platform; Informatica for some source systems. |
| Vendor / Version | Informatica (limited scope) |
| Alerting / Notification Channel | None |
| Known Gaps / Limitations | No DQ monitoring, alerting, or SLAs for the data platform. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or automation for DQ monitoring. |
| Tooling Maturity | 1 | No tooling for DQ monitoring on the data platform. |
| People / Skills | 2 | Some skills for Informatica, but not for the platform. |
| Automation Level | 1 | No automation for DQ monitoring. |
| **Sub-Area Overall** | 1 | DQ monitoring is not implemented for the data platform. |

---

## 3.4 Data Observability

Data observability covers the end-to-end visibility into the health, freshness, volume, schema stability, and distribution of data across the platform.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data observability platform in use (e.g., Monte Carlo, Bigeye, Acceldata, dbt expectations, custom solutions)? | No data observability platform in use. |
| 2 | Are the five pillars of data observability monitored: freshness, volume, schema, distribution, and lineage? | Not monitored. |
| 3 | Is anomaly detection used to identify unexpected changes in data without manually defining thresholds? | Not used. |
| 4 | How quickly is a data incident detected after it occurs (MTTD – mean time to detect)? | Not measured. |
| 5 | Are end-to-end pipeline health signals (SLA breaches, upstream delays) visible in a single pane of glass? | Not visible. |
| 6 | Is data lineage used as part of incident investigation to identify blast radius and impacted downstream assets? | Not used. |
| 7 | Are data consumers notified automatically when data they depend on has a quality or freshness issue? | Not notified. |
| 8 | How are data incidents tracked, managed, and resolved (ticketing, SLAs, post-mortems)? | Not tracked or managed. |
| 9 | Is observability coverage measured and tracked? What percentage of critical assets are covered? | Not measured or tracked. |
| 10 | Are observability insights used proactively to improve pipeline reliability and quality? | Not used. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | None |
| Vendor / Version | N/A |
| Coverage (% of critical assets monitored) | 0% |
| Known Gaps / Limitations | No observability, monitoring, or incident management for the data platform. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process or platform for data observability. |
| Tooling Maturity | 1 | No tooling for data observability. |
| People / Skills | 1 | No skills or experience with data observability. |
| Automation Level | 1 | No automation for observability. |
| **Sub-Area Overall** | 1 | Data observability is not implemented. |
