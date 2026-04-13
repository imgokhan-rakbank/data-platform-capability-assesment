# Domain 4: Gold Layer – Model Review Assessment

> **Assessor(s):**
> **Date:**
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

The Gold layer is the **consumption-ready, business-facing** zone of the Medallion Architecture. It contains:
- **Aggregated, denormalised, or pre-joined** models optimised for analytical query patterns.
- **Business metric definitions** and calculated KPIs that have a single, governed definition.
- **Domain-specific data marts** or reporting datasets aligned to business units (Risk, Finance, Retail, Compliance).
- **Risk reporting models** that consolidate Silver risk datasets into regulatory and management reporting structures.

Assessment questions cover all four modelling perspectives and have a particular focus on risk reporting readiness, which is a primary consumer of the Gold layer in a banking context.

---

## 4.1 Conceptual Design – Reporting Subjects

Conceptual design at Gold defines the reporting domains, key metrics, and aggregation subjects that the layer must support.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a conceptual catalogue of Gold-layer reporting subjects — i.e., a list of all data marts, reporting datasets, or presentation tables that exist or are planned? | Not communicated |
| 2 | Are the following **risk and regulatory reporting subjects** defined in the Gold conceptual model? For each, state whether it exists, is planned, or is absent: | |
|   | – Credit Risk Dashboard (portfolio quality, NPL ratio, coverage ratio) | |
|   | – IFRS 9 / ECL Reporting (ECL by stage, provision movement, sensitivities) | |
|   | – Large Exposure Report (top obligors, connected party groups vs. regulatory limit) | |
|   | – Liquidity Coverage Ratio (LCR) | |
|   | – Net Stable Funding Ratio (NSFR) | |
|   | – Interest Rate Risk in the Banking Book (IRRBB) | |
|   | – Market Risk (VaR, stress tests, mark-to-market P&L) | |
|   | – Operational Risk Loss Reporting (Basel operational risk categories) | |
|   | – AML / Suspicious Activity Reporting summary | |
|   | – Regulatory Capital Adequacy (CAR, Tier 1, RWA by asset class) | |
|   | – CBUAE Regulatory Returns (e.g., BR forms, credit register) | |
|   | – Management P&L / NIM / Fee Income | |
|   | – Customer / Portfolio Analytics (product penetration, attrition, lifetime value) | |
|   | – Channel & Transaction Analytics (volume, value, cost per channel) | |
| 3 | Is there a defined subject-area owner (business unit) for each Gold reporting domain, who approves metric definitions and is accountable for data quality in that domain? | |
| 4 | Are business reports and dashboards explicitly mapped back to Gold models — so that every number in a regulatory or management report has a single traceable Gold source? | |
| 5 | Is there a documented process for onboarding a new Gold reporting subject — from requirements gathering through to production deployment? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Gold Model Inventory / Catalogue Location | |
| Risk Reporting Gold Models Currently Implemented | |
| Known Missing Gold Models | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Modelling | | |
| Reporting Subject Coverage | | |
| Risk Reporting Coverage | | |
| **Sub-Area Overall** | | |

---

## 4.2 Logical Design – Aggregation Models

Logical design at Gold covers the structure of aggregation models, metric definitions, dimensional models, and how they relate to the Silver layer.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a documented logical model for each major Gold reporting area — e.g., a dimensional model (fact + dimensions) or a wide aggregation table, with grain defined? | |
| 2 | Are business metric definitions (e.g., "NPL Ratio", "Net Interest Margin", "LCR") formalised in a metrics catalogue with formula, numerator/denominator Silver sources, and any regulatory reference? | |
| 3 | Are dimension tables (e.g., `dim_date`, `dim_party`, `dim_product`, `dim_account`) defined at the Gold layer and shared across multiple fact tables — or does each Gold model embed its own denormalised copies? | |
| 4 | Is there a consistent grain definition for every Gold fact table (e.g., `fact_daily_account_balance` — one row per account per calendar day)? | |
| 5 | Are additive, semi-additive, and non-additive facts correctly classified — e.g., balance is semi-additive (can sum across accounts but not across time); rate is non-additive? | |
| 6 | Is there a bridge or factless fact table pattern used where many-to-many relationships exist (e.g., party-to-account, account-to-facility, collateral-to-contract)? | |
| 7 | Are all metric calculations traceable back to Silver source attributes — i.e., no opaque calculations without documented logic? | |
| 8 | Are risk metrics (PD, LGD, ECL, RWA) derived in Gold using documented formulas applied to Silver inputs, or are they simply passed through from a risk source system? Is the boundary clear? | |
| 9 | Are regulatory reporting models designed to produce outputs that directly match the format and logic of regulatory returns (e.g., CBUAE templates, Basel schedules)? | |
| 10 | Is there a semantic consistency check ensuring the same metric (e.g., "Total Outstanding Loans") returns the same value regardless of which Gold model or report it is sourced from? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Logical Model Location | |
| Metrics Catalogue Location | |
| Dimensional Modelling Approach | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Logical Modelling | | |
| Metric Definition Quality | | |
| Dimensional Model Completeness | | |
| **Sub-Area Overall** | | |

---

## 4.3 Physical Design

Physical design at Gold covers performance optimisation, materialisation strategy, aggregation table structures, and how Gold tables are served to consumers.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What materialisation strategy is used for Gold models — fully materialised Delta tables, views over Silver, materialised views, or a mix? Is the choice documented per model? | |
| 2 | Are Gold tables partitioned and clustered based on the dominant query patterns of the consuming BI / reporting tools (e.g., partitioned by reporting date, clustered by portfolio / business unit)? | |
| 3 | Are aggregate / pre-computed tables used for frequently queried metrics to avoid repeated expensive Silver joins at query time? | |
| 4 | Is there a defined refresh cadence for each Gold model (real-time, hourly, daily, monthly) aligned with the reporting SLA of its consumers? | |
| 5 | Are standard Gold audit columns present: `gold_created_ts`, `gold_updated_ts`, `reporting_date`, `silver_source_version`, `pipeline_run_id`? | |
| 6 | Is there a naming convention for Gold objects (e.g., `gold.<domain>.<fact|dim|report>_<subject>`) that is consistently applied? | |
| 7 | Are Gold tables exposed to consumers through a governed access layer (e.g., Unity Catalog grants, SQL warehouse endpoints, published datasets in Power BI / Tableau) rather than direct table access? | |
| 8 | Are DDL and transformation code for Gold models version-controlled, reviewed, and promoted via a CI/CD pipeline? | |
| 9 | Are test assertions executed on Gold model outputs — e.g., validating that total ECL provision matches risk system control totals, LCR ratio is within expected bounds? | |
| 10 | Is there a documented rebuild / replay strategy for Gold models if a Silver source changes or if a historic restatement is required? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Materialisation Strategy | |
| Partitioning & Clustering Strategy | |
| Gold Serving Layer / Access Method | |
| CI/CD for Gold Models | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Physical Modelling | | |
| Performance Optimisation | | |
| Standards Conformance | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 4.4 Semantic / Metric Layer

The semantic layer at Gold is the primary business-facing layer — it defines what metrics mean, how they are calculated, and ensures consistent interpretation across all consumers.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a semantic / metric layer tool in use (e.g., dbt Semantic Layer, Looker LookML, AtScale, Cube.dev, Power BI dataset) that centralises metric definitions above the Gold physical tables? | |
| 2 | Are key banking metrics (NIM, NPL Ratio, LCR, CAR, RoE, Cost-to-Income) formally defined in the semantic layer with a single canonical formula and approved by the business? | |
| 3 | Are dimensions (time, product hierarchy, geography, business unit, customer segment) consistently defined in the semantic layer and reusable across all reports? | |
| 4 | Is the semantic layer version-controlled and do changes require an approval process to prevent unauthorised changes to metric definitions? | |
| 5 | Are metric definitions in the semantic layer linked back to the corresponding regulatory definition where applicable (e.g., LCR formula linked to Basel III / CBUAE liquidity regulation)? | |
| 6 | Can business users discover and query Gold metrics through a self-service interface without writing SQL — e.g., through a BI tool connected to the semantic layer? | |
| 7 | Are there documented SLAs for Gold model data freshness that are communicated to and agreed with consumers? | |
| 8 | Is there a process for certifying Gold models — i.e., a formal sign-off that a model is production-ready, meets quality standards, and has business approval before being published to consumers? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Semantic Layer Tool | |
| Metrics Catalogue / Certified Metrics List | |
| Self-Service BI Tool | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Semantic / Metric Layer | | |
| Metric Definition Quality | | |
| Self-Service Enablement | | |
| **Sub-Area Overall** | | |

---

## 4.5 Risk Reporting Coverage

This sub-area specifically evaluates the Gold layer's ability to support **risk management and regulatory reporting** — a primary use case for the banking data platform.

### Questions

| # | Risk Reporting Area | Is a Gold model in place? | Coverage (Full / Partial / Absent) | Gap Description |
|---|---------------------|--------------------------|-------------------------------------|-----------------|
| 1 | Credit Portfolio Quality (NPL, coverage ratio, provision) | | | |
| 2 | IFRS 9 ECL Reporting (stage distribution, provision movement) | | | |
| 3 | Large Exposure (top 20 obligors vs. regulatory limit) | | | |
| 4 | Liquidity Coverage Ratio (LCR) | | | |
| 5 | Net Stable Funding Ratio (NSFR) | | | |
| 6 | Capital Adequacy Ratio (CAR / CBUAE CAR) | | | |
| 7 | Risk-Weighted Assets (RWA) by asset class | | | |
| 8 | Interest Rate Risk in the Banking Book (IRRBB / EVE / NII) | | | |
| 9 | Market Risk – VaR and Stress Test Results | | | |
| 10 | Operational Risk Loss Summary | | | |
| 11 | AML / Suspicious Activity Summary | | | |
| 12 | CBUAE Credit Register Submission | | | |
| 13 | Management Credit Risk Dashboard | | | |
| 14 | Management Liquidity Dashboard | | | |

### Additional Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are regulatory reports generated directly from Gold models, or is there an additional manual step in Excel or another tool before submission to the regulator? | |
| 2 | Is there an automated reconciliation between Gold risk totals and the numbers submitted to the regulator / published in management reports? | |
| 3 | Are stress testing scenarios modelled in Gold (or can parameterised Gold views support scenario analysis), or is stress testing entirely handled outside the data platform? | |
| 4 | Is there a data lineage trail from each regulatory submission number back through Gold → Silver → Bronze, providing full auditability for regulatory review? | |
| 5 | Are historical risk snapshots preserved in Gold at the required regulatory granularity (e.g., monthly for IFRS 9, quarterly for Basel Pillar 3) to support period comparisons and audit? | |
| 6 | Is the Gold risk reporting model reviewed by the Risk and Finance teams prior to each reporting cycle to confirm data completeness and accuracy? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Risk Reporting Tool / Platform consuming Gold | |
| Regulatory Submission Process | |
| Reconciliation Process | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Credit Risk Reporting | | |
| Liquidity Risk Reporting | | |
| Market Risk Reporting | | |
| Regulatory / Capital Reporting | | |
| Auditability & Lineage | | |
| **Sub-Area Overall** | | |

---

## 4.6 Gold Best Practices

This sub-area checks adherence to widely accepted Gold-layer best practices for a banking analytical platform.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are Gold models built exclusively from Silver (not from Bronze), ensuring that all cleansing and integration logic is centralised in Silver? | |
| 2 | Is there a "single source of truth" principle enforced — i.e., each business metric has exactly one Gold model as its authoritative source, and reports are not free to compute their own versions? | |
| 3 | Are Gold models modular — i.e., individual Gold models can be updated independently without requiring a full rebuild of the entire Gold layer? | |
| 4 | Are Gold model refresh jobs monitored with automated alerts for failures or SLA breaches? | |
| 5 | Is there a process for deprecating old Gold models when they are replaced by new versions — preventing zombie tables that consumers might inadvertently rely on? | |
| 6 | Are sensitive Gold outputs (e.g., individual customer risk scores, internal capital allocation) subject to additional access controls beyond general Gold access? | |
| 7 | Are Gold models tested with representative data volumes before being promoted to production to ensure query performance meets the SLA? | |
| 8 | Is there documentation of the expected row counts, financial totals, and key ratios for each Gold model so that anomalies can be detected during each load? | |
| 9 | Are Gold models aligned with regulatory reporting calendars — ensuring that month-end, quarter-end, and year-end snapshots are produced reliably and on schedule? | |
| 10 | Is there a documented disaster recovery process for Gold models — specifying recovery time objective (RTO) and recovery point objective (RPO) in the event of data loss? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Single-Source-of-Truth Enforcement Mechanism | |
| Gold Monitoring & Alerting | |
| Model Deprecation Process | |
| DR / Recovery Process | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Best Practices Adherence | | |
| Reliability & Monitoring | | |
| Security | | |
| **Sub-Area Overall** | | |
