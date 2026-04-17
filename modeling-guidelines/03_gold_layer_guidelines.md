# Gold Layer – Logical Data Modelling Guidelines

> **Version:** 1.0  
> **Owner:** Data Architecture & Modelling  
> **Applies to:** All Gold data mart, reporting, and metric models  
> **Industry references:** Kimball Dimensional Modelling Toolkit, DAMA-DMBOK2, IFRS 9, Basel III, CBUAE SupTech reporting, dbt Metrics Layer

---

## 1. Purpose & Scope

The Gold layer is the **consumption-ready, business-facing** zone of the Medallion Architecture. It contains aggregated, pre-joined, or pre-computed models optimised for the query patterns of specific consumer groups: business analysts, risk teams, finance, regulators, and BI tools.

Gold models must:
- Read exclusively from Silver (never from Bronze directly)
- Contain no business rules not already expressed in Silver (Gold can aggregate; it must not redefine what a "customer" is)
- Have a **single authoritative definition** for every metric they expose
- Be fully traceable back to Silver and Bronze via lineage

Gold is organised into distinct sub-namespaces by consumption domain using the `gold.<category>.<subcategory>_<type>_<subject>` pattern:

| Sub-Namespace Pattern | Purpose | Examples |
|---|---|---|
| `gold.<category>.<subcategory>_<type>_<subject>` | Regulatory submission datasets | `gold.regulatory.basel3_rpt_rwa_summary` |
| `gold.<category>.<subcategory>_<type>_<subject>` | Risk management KPIs and dashboards | `gold.risk.credit_daily_ecl_provision` |
| `gold.<category>.<subcategory>_<type>_<subject>` | Management reporting and MIS | `gold.management.retail_daily_customer_metrics` |
| `gold.<category>.<subcategory>_<type>_<subject>` | Self-service analytics dimensions and facts | `gold.analytics.customer_dim_customer` |
| `gold.<category>.<subcategory>_<type>_<subject>` | CBUAE SupTech submission models | `gold.suptech.cbuae_rpt_credit_register` |

---

## 2. Core Principles

| # | Principle | Rationale |
|---|-----------|-----------|
| G-P1 | **Gold reads Silver only** – No direct Bronze reads permitted in Gold pipelines | All cleansing and integration is owned by Silver; Gold consumers must not inherit raw data concerns |
| G-P2 | **Single source of truth per metric** – Each business metric (NIM, NPL, LCR, etc.) has exactly one Gold model as its authoritative source | Eliminates reconciliation conflicts between dashboards, prevents metric proliferation |
| G-P3 | **Explicit grain** – Every Gold fact table declares its grain in documentation and in table comments | Prevents misinterpretation of aggregation levels; critical for semi-additive measures |
| G-P4 | **Certified before publishing** – Gold models must pass quality certification and business approval before being accessible to consumers | Prevents ungoverned data from flowing into regulatory submissions or management reports |
| G-P5 | **Immutable snapshots for regulatory** – Point-in-time regulatory snapshots are never overwritten once produced and signed off | Provides an unalterable audit trail for CBUAE, Basel, and IFRS 9 submissions |
|  | **Note:** While regulatory snapshots must be immutable after period close, other Gold modelling patterns (periodic immutable snapshots, accumulating snapshots, iterative/append models) depend on the use case (query patterns, performance and lineage needs) and may be applied as required. |
| G-P6 | **Semantic layer as access boundary** – Business metrics are served through the semantic/metric layer, not through direct Gold table access by BI tools | Prevents BI tools from defining their own metric logic, causing divergence |

---

## 3. Naming Conventions

### 3.1 Object Naming Pattern

```
gold.<category>.<subcategory>_<type>_<subject>
```

| Type Prefix | Use |
|---|---|
| `fact_` | Fact tables (transactional, periodic snapshot, or accumulating snapshot) |
| `dim_` | Dimension tables (conformed, shared across facts) |
| `agg_` | Pre-aggregated summary tables (not a full dimensional model) |
| `rpt_` | Regulatory or management report output tables |
| `bridge_` | Bridge/association tables for many-to-many relationships |
| `mart_` | Denormalised wide tables for a specific business unit's reporting |

**Examples:**

| Pattern | Example |
|---|---|
| `gold.analytics.customer_dim_customer` | Conformed customer dimension |
| `gold.analytics.account_fact_daily_account_balance` | Daily balance snapshot fact |
| `gold.risk.credit_agg_ecl_provision_monthly` | Monthly ECL aggregation |
| `gold.regulatory.basel3_rpt_rwa_summary` | RWA regulatory report |
| `gold.suptech.cbuae_rpt_credit_register` | CBUAE Credit Register submission table |

### 3.2 Column Naming Rules

Gold column naming follows the same `snake_case` rules as Silver with these additions:

| Rule | Standard |
|---|---|
| Reporting date | `reporting_date DATE` — the business date this row represents |
| Surrogate FK | `<dim_entity>_key STRING` — inherit Silver surrogate key; do not re-generate |
| Measure naming | `<measure_name>_amount / _count / _rate / _pct / _ratio` depending on measure type |
| Currency companion | Any amount column must have a companion `<measure>_currency_code CHAR(3)` column |
| Audit columns | See Section 6 for mandatory Gold audit columns |

---

## 4. Dimensional Modelling Standards (Kimball)

### 4.1 Fact Table Design

| Requirement | Standard |
|---|---|
| Grain declaration | Grain must be declared in the table comment and the model documentation: e.g., "One row per account per calendar day" |
| Grain enforcement | The primary key of the fact table must enforce the declared grain (no duplicate rows per grain combination allowed) |
| Measures | Classify every measure: **Additive** (can sum across all dimensions), **Semi-additive** (can sum across some dimensions, e.g., balance across accounts but not across time), **Non-additive** (e.g., ratio, percentage) |
| Degenerate dimensions | Degenerate dimensions (transaction reference numbers, document IDs) stored directly on the fact; no dimension table required |
| Factless facts | Use factless fact tables for many-to-many relationships with no natural measure (e.g., `party_account_bridge`) |
| No raw IDs | Fact tables reference dimension surrogate keys; raw source system IDs are for traceability only, not for joining |

### 4.2 Conformed Dimension Standards

The following dimensions are **conformed** — shared across all Gold fact tables. No Gold model may define its own version of these:

| Dimension | Conformed Name | Grain |
|---|---|---|
| Date / Calendar | `gold.analytics.shared_dim_date` | One row per calendar day; includes business calendar, fiscal period, bank holiday flag |
| Customer / Party | `gold.analytics.customer_dim_customer` | One row per party per SCD2 version (current + history) |
| Account | `gold.analytics.account_dim_account` | One row per account per SCD2 version |
| Product | `gold.analytics.product_dim_product` | One row per product type in the product hierarchy |
| Branch / Channel | `gold.analytics.channel_dim_channel` | One row per channel/branch |
| Currency | `gold.analytics.reference_dim_currency` | One row per ISO 4217 currency code |
| Business Unit | `gold.analytics.reference_dim_business_unit` | One row per business unit / cost centre |

### 4.3 Snapshot Fact Table Patterns

| Pattern | Use Case | Example |
|---|---|---|
| **Daily periodic snapshot** | Balances, positions, exposures — one row per entity per day | `fact_daily_account_balance` (one row per account per calendar day) |
| **Transaction fact** | Immutable financial events | `fact_financial_transaction` (one row per posted transaction) |
| **Monthly aggregate** | Regulatory reporting, provisioning | `agg_ecl_provision_monthly` (one row per account per month-end) |
| **Accumulating snapshot** | Multi-step business processes | `fact_loan_lifecycle` (one row per loan, columns for each lifecycle milestone) |

---

Model selection and the snapshot pattern should be driven by the use case: regulatory workloads typically require immutable point-in-time snapshots, whereas management or analytical workloads may use accumulating snapshots, periodic snapshots, or iterative/append models based on performance, storage, and lineage requirements. Apply the pattern that best meets the business, technical and compliance needs.

## 5. Metric & KPI Standards

### 5.1 Metric Catalogue Requirement

Every metric exposed by a Gold model must be registered in the platform Metrics Catalogue with the following attributes before it is consumed in any report or dashboard:

| Attribute | Required |
|---|---|
| Metric name | Human-readable canonical name (e.g., "Non-Performing Loan Ratio") |
| Metric code | Machine-readable code (e.g., `NPL_RATIO`) |
| Formula | Complete formula in SQL or pseudocode |
| Numerator Silver source | Table and column(s) |
| Denominator Silver source | Table and column(s) |
| Additivity | Additive / Semi-additive / Non-additive |
| Grain | Smallest grain at which this metric is valid |
| Regulatory reference | Applicable regulation or CBUAE circular (if applicable) |
| Business owner | Named approver from the consuming business unit |
| Certification status | Draft / Approved / Certified / Deprecated |

### 5.2 Banking KPI Standards

The following key banking metrics must be defined and certified before their respective Gold models go to production:

| Metric | Formula Reference | Regulatory Link |
|---|---|---|
| NPL Ratio | Non-Performing Loans / Total Gross Loans | CBUAE Circular |
| Coverage Ratio | Total Provisions / NPL Balance | IFRS 9 |
| LCR | HQLA / Net Cash Outflows over 30 days | Basel III |
| NSFR | Available Stable Funding / Required Stable Funding | Basel III |
| CAR / CBUAE CAR | Eligible Capital / Total RWA | Basel III Pillar 1 |
| RWA by Asset Class | Per Basel III standardised or IRB approach | Basel III Pillar 1 |
| ECL by Stage | Stage 1 / 2 / 3 ECL amounts | IFRS 9 |
| NIM | Net Interest Income / Average Earning Assets | Management |
| Cost-to-Income | Operating Costs / Operating Income | Management |
| RoE | Net Profit / Average Equity | Management |

### 5.3 Semantic Layer Integration

- All certified metrics must be registered in the platform semantic layer tool 
- BI tools must connect to Gold through the semantic layer; direct Gold table SQL queries by BI tools are not permitted for certified metrics
- The semantic layer definition must include: dimension list, time granularity, aggregation function, and filter restrictions

---

## 6. Mandatory Audit Columns

Every Gold table **must** include the following:

| Column | Data Type | Nullable | Description |
|---|---|---|---|
| `reporting_date` | `DATE` | NOT NULL | Business/reporting date this row represents |
| `gold_created_ts` | `TIMESTAMP` (UTC) | NOT NULL | System timestamp when this row was inserted into Gold |
| `gold_updated_ts` | `TIMESTAMP` (UTC) | Nullable | System timestamp of last update (for Type 1 / overwrite Gold tables) |
| `silver_source_version` | `STRING` | NOT NULL | Silver pipeline run ID or version identifier used to build this row |
| `pipeline_run_id` | `STRING` | NOT NULL | Gold pipeline run ID |
| `is_restated` | `BOOLEAN` | NOT NULL | `true` if this row was produced by a restatement/backdated run; `false` for regular production |
| `restatement_reason` | `STRING` | Nullable | Free-text reason for restatement; NULL for non-restated rows |

---

## 7. Risk & Regulatory Reporting Standards

### 7.1 Regulatory Snapshot Immutability

| Requirement | Standard |
|---|---|
| Month-end snapshots | Gold regulatory tables must produce a month-end snapshot on the last business day of each month; this snapshot must not be overwritten once the reporting cycle closes |
| IFRS 9 quarterly | ECL provision snapshots at quarter-end must be retained indefinitely |
| Restatement handling | Restatements of closed periods must produce new rows with `is_restated = true` and `restatement_reason` populated; original rows must not be deleted |
| Lineage to submission | Every number in a CBUAE / Basel submission must be traceable to a specific Gold row via `reporting_date` and `pipeline_run_id` |

### 7.2 Required Regulatory Gold Models

| Model | Namespace | Source Silver Domain | Regulatory Framework |
|---|---|---|---|
| RWA Summary | `gold.regulatory.basel3_rpt_rwa_summary` | `risk_credit`, `risk_market` | Basel III Pillar 1 |
| Capital Adequacy | `gold.regulatory.basel3_rpt_capital_adequacy` | `risk_credit`, `gl` | Basel III Pillar 1 |
| LCR / NSFR | `gold.regulatory.basel3_rpt_liquidity_ratios` | `risk_liquidity`, `reference` | Basel III Pillar 3 |
| IFRS 9 ECL | `gold.regulatory.ifrs9_rpt_ecl_provision` | `risk_credit`, `loan`, `account` | IFRS 9 |
| Large Exposures | `gold.regulatory.cbuae_rpt_large_exposures` | `risk_credit`, `account`, `customer` | CBUAE |
| Credit Register | `gold.suptech.cbuae_rpt_credit_register` | `loan`, `account`, `customer` | CBUAE SupTech |
| AML Summary | `gold.regulatory.cbuae_rpt_aml_summary` | `compliance` | CBUAE |

### 7.3 Reconciliation Controls for Regulatory Models

Before a Gold regulatory model is submitted:
1. Row count must be compared against a control total from the source risk/finance system
2. Key financial totals (e.g., total ECL provision, total RWA, total eligible capital) must be reconciled to within ±0.01% of the control total
3. Reconciliation results must be stored in a `gold.control.reconciliation_log` table and signed off by the responsible data steward

---

## 8. Physical Design Standards

| Requirement | Standard |
|---|---|
| Materialisation | Fully materialised Delta tables; views over Silver are permitted only for thin pass-through layers where no aggregation occurs |
| Partitioning | Partition by `reporting_date` for all periodic snapshot and regulatory tables; use Liquid Clustering for frequently-filtered dimensions |
| Refresh cadence | Defined per model; default: daily at 06:00 UTC; regulatory month-end snapshots by 08:00 UTC on the first business day |
| OPTIMIZE | Scheduled OPTIMIZE + ZORDER on `reporting_date` for large fact tables; at minimum weekly |
| Delta properties | Same `logRetentionDuration` and `deletedFileRetentionDuration` settings as Silver apply to Gold |
| Naming | Object names follow the patterns in Section 3 |
| Access | Row-level security (RLS) applied via Unity Catalog row filters for sensitive Gold models (individual credit scores, internal ratings, HR data) |

---

## 9. Gold Model Certification Process

Before a Gold model is published to consumers, it must pass:

| Gate | Requirement |
|---|---|
| Data quality gate | All dbt or Great Expectations tests pass (uniqueness, not-null, referential integrity, accepted values, range checks) |
| Reconciliation gate | Financial totals reconcile to Silver control totals (tolerance ≤ 0.01%) |
| Business approval | Named business owner signs off metric definitions and formula |
| Security review | Column-level sensitivity labels reviewed; RLS configured for sensitive models |
| Documentation gate | Table comment, column comments, grain statement, metric catalogue entries all populated |
| Lineage gate | End-to-end lineage from Gold to Bronze registered in Unity Catalog |

Certification status tracked in `gold.control.model_certification_register` with: model name, version, certification date, approver, and next review date.

---

## 10. Gold Best Practices Summary

| # | Best Practice | Severity if Violated |
|---|---|---|
| G-BP1 | Gold reads Silver only; no Bronze reads | Critical |
| G-BP2 | Single authoritative Gold model per metric; no parallel competing definitions | Critical |
| G-BP3 | Grain declared and enforced (PK = grain) for all fact tables | Critical |
| G-BP4 | Regulatory snapshots immutable after period close | Critical |
| G-BP5 | Conformed dimensions shared across all facts; no duplicated dimension definitions | High |
| G-BP6 | All certified metrics registered in Metrics Catalogue with formula and business approver | High |
| G-BP7 | All seven mandatory audit columns present | High |
| G-BP8 | Reconciliation to Silver control totals before each regulatory submission | High |
| G-BP9 | Gold accessed through semantic layer for certified metrics; direct BI SQL joins prohibited | High |
| G-BP10 | Gold models certified (DQ + business approval) before production access granted | High |
| G-BP11 | Additive / semi-additive / non-additive declared for every measure | Medium |
| G-BP12 | Restatements tracked with `is_restated` flag and restatement reason | Medium |

---

## 11. Gold Modelling Checklist Reference

See `04_ldm_compliance_checklist.md` → **Section G: Gold Layer Controls** for the full gated compliance check required before a Gold model is approved for production deployment.
