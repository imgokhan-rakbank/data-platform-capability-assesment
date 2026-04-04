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
| 1 | Is a data profiling tool in use (e.g., Informatica DQ, Talend DQ, Great Expectations, dbt tests, Atlan, Monte Carlo)? | |
| 2 | What percentage of critical data assets have been profiled? Is profiling done on a scheduled basis? | |
| 3 | What profiling metrics are captured (completeness, uniqueness, nulls, min/max, patterns, distributions, referential integrity)? | |
| 4 | Are profiling results stored historically to detect trends or regressions over time? | |
| 5 | Are profiling results accessible to data stewards, analysts, and consumers through a portal or catalog? | |
| 6 | Is profiling automated as part of data ingestion pipelines (profile on arrival)? | |
| 7 | How are profiling findings acted upon? Is there a workflow to report and remediate issues discovered? | |
| 8 | Is statistical profiling (outlier detection, value distribution analysis) performed in addition to structural profiling? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Number of Datasets Profiled | |
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

## 3.2 DQ Rules Management

DQ rules management covers the definition, cataloging, versioning, and lifecycle management of data quality rules applied to datasets.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a central repository or catalog where all DQ rules are defined, documented, and managed? | |
| 2 | Who is responsible for defining DQ rules – data stewards, engineers, or business stakeholders? Is there a formal process? | |
| 3 | Are DQ rules mapped to business terms, critical data elements (CDEs), or data products? | |
| 4 | How are DQ rules versioned and promoted through environments (dev/test/prod)? | |
| 5 | Are there standardized rule categories (completeness, accuracy, consistency, timeliness, uniqueness, validity)? | |
| 6 | How are rule changes reviewed and approved (peer review, business sign-off)? | |
| 7 | Is there a mechanism to reuse rules across multiple datasets (parameterized rules, rule templates)? | |
| 8 | How are rules retired or updated when source systems or business definitions change? | |
| 9 | Is there a priority or severity classification for rules (critical / high / medium / low)? | |
| 10 | How many DQ rules are currently in production, and what is the coverage across critical data domains? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Number of Active DQ Rules | |
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

## 3.3 DQ Monitoring

DQ monitoring covers the ongoing, automated execution of DQ rules and the tracking of data quality scores over time.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are DQ checks executed automatically on a scheduled basis or as part of data pipeline execution? | |
| 2 | Is there a DQ dashboard or scorecard showing quality scores per dataset, domain, or data product? | |
| 3 | How are DQ failures alerted (email, Slack, PagerDuty, ticketing system)? Is there an on-call rotation? | |
| 4 | Are quality trends tracked over time (e.g., week-over-week quality score change)? | |
| 5 | Is data quarantined, flagged, or blocked from downstream consumption when quality thresholds are breached? | |
| 6 | Are DQ SLAs defined (e.g., "completeness of customer table must be ≥ 98%") and tracked against thresholds? | |
| 7 | How is root cause analysis performed when a DQ issue is detected? Is there tooling support? | |
| 8 | Are DQ results published to consumers as part of data product metadata? | |
| 9 | Is DQ monitoring integrated into data pipelines to prevent bad data from propagating downstream? | |
| 10 | What is the current average quality score for critical data domains, and how was it calculated? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Alerting / Notification Channel | |
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

## 3.4 Data Observability

Data observability covers the end-to-end visibility into the health, freshness, volume, schema stability, and distribution of data across the platform.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data observability platform in use (e.g., Monte Carlo, Bigeye, Acceldata, dbt expectations, custom solutions)? | |
| 2 | Are the five pillars of data observability monitored: freshness, volume, schema, distribution, and lineage? | |
| 3 | Is anomaly detection used to identify unexpected changes in data without manually defining thresholds? | |
| 4 | How quickly is a data incident detected after it occurs (MTTD – mean time to detect)? | |
| 5 | Are end-to-end pipeline health signals (SLA breaches, upstream delays) visible in a single pane of glass? | |
| 6 | Is data lineage used as part of incident investigation to identify blast radius and impacted downstream assets? | |
| 7 | Are data consumers notified automatically when data they depend on has a quality or freshness issue? | |
| 8 | How are data incidents tracked, managed, and resolved (ticketing, SLAs, post-mortems)? | |
| 9 | Is observability coverage measured and tracked? What percentage of critical assets are covered? | |
| 10 | Are observability insights used proactively to improve pipeline reliability and quality? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Coverage (% of critical assets monitored) | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
