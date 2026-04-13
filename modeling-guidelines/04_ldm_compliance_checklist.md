# LDM Compliance Checklist – Refined

> **Version:** 2.0 (Refined)  
> **Owner:** Data Architecture & Modelling  
> **Replaces:** LDM Compliance Checklist v1 (`supporting-documents/LDM Compliance checklist.docx`)  
> **Industry references:** DAMA-DMBOK2, ISO/IEC 11179, Databricks Medallion Best Practices, IFRS 9, Basel III, CBUAE SupTech, TOGAF ADM  
> **Last updated:** 2026-04-13

---

## How to Use This Checklist

This checklist must be completed for every data model change that is promoted to production. It is divided into four sections:

| Section | When Required |
|---|---|
| **Section 0 – Common Governance** | All changes, all layers |
| **Section B – Bronze Layer** | New Bronze source onboarding or schema changes |
| **Section S – Silver Layer** | New Silver entities or attribute-level changes |
| **Section G – Gold Layer** | New Gold models, metric additions, or report changes |

### Severity Levels

| Severity | Meaning | Handling |
|---|---|---|
| 🔴 **CRITICAL** | Blocks production deployment; no exception | Must be resolved before merge |
| 🟠 **HIGH** | Blocks production deployment; exception requires ARB written waiver | Must be resolved or waived with ARB sign-off |
| 🟡 **MEDIUM** | Does not block deployment but must be remediated within 30 days | Logged as a tracked remediation item |
| 🟢 **LOW** | Advisory; best practice guidance | Noted in review; no action gate |

### Pass / Fail / Waived / N/A

| Status | Meaning |
|---|---|
| ✅ Pass | Control fully satisfied with evidence |
| ❌ Fail | Control not satisfied; see remediation action |
| ⚠️ Waived | Exception granted; waiver reference and expiry date recorded |
| ➖ N/A | Control does not apply to this change with documented justification |

---

## Section 0 – Common Governance (All Layers)

### 0.1 Applicability & Context

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| 0.1.1 | The change request clearly states which Medallion layer is affected (Bronze / Silver ODS / Silver Analytical / Gold) | 🟠 HIGH | | |
| 0.1.2 | The LDM under review explicitly declares its scope: enterprise-wide, domain-specific, or single subject area | 🟠 HIGH | | |
| 0.1.3 | The model is versioned (semantic version: MAJOR.MINOR.PATCH) and version history is maintained in Git | 🟠 HIGH | | |
| 0.1.4 | A named Data Architecture owner is recorded for this model | 🟠 HIGH | | |
| 0.1.5 | A named Business Data Steward or Domain Owner is recorded | 🟠 HIGH | | |
| 0.1.6 | Formal sign-off from the Data Architecture & Modelling team has been obtained | 🔴 CRITICAL | | |

### 0.2 Change Governance

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| 0.2.1 | The change is tracked in the ADO / Jira work item linked to this pull request | 🟡 MEDIUM | | |
| 0.2.2 | Breaking changes (column rename, type change, column removal) are flagged as breaking in the PR title and description | 🔴 CRITICAL | | |
| 0.2.3 | If breaking: a minimum 14-day consumer notification period has been observed, or all consumers have confirmed they are updated | 🔴 CRITICAL | | |
| 0.2.4 | If breaking: an ARB review has been completed and an ADR has been recorded | 🟠 HIGH | | |
| 0.2.5 | Downstream consumer impact has been assessed using Unity Catalog lineage; all impacted Gold models and reports are listed | 🟠 HIGH | | |
| 0.2.6 | A rollback plan is documented: how to revert this change if it causes a production incident | 🟡 MEDIUM | | |

### 0.3 Documentation Standards

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| 0.3.1 | Unity Catalog table comment is populated (minimum: layer, domain, purpose, grain) | 🟠 HIGH | | |
| 0.3.2 | Unity Catalog column-level comments are populated for 100% of columns | 🟠 HIGH | | |
| 0.3.3 | Table is linked to its corresponding entry in the Business Glossary | 🟡 MEDIUM | | |
| 0.3.4 | An entity-relationship diagram reflecting this change is updated and committed to the model repository | 🟡 MEDIUM | | |
| 0.3.5 | A schema change log entry is created (date, changed by, change description, version before/after) | 🟡 MEDIUM | | |

### 0.4 Data Classification & Security

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| 0.4.1 | Every column with PII, Confidential, or Sensitive data is tagged with the correct data classification in Unity Catalog | 🔴 CRITICAL | | |
| 0.4.2 | Access permissions are reviewed: only the correct roles/service principals have read/write access | 🟠 HIGH | | |
| 0.4.3 | PCI DSS cardholder data: no clear-text PAN, CVV, or full magnetic stripe data is stored in any Silver or Gold table | 🔴 CRITICAL | | |
| 0.4.4 | Data residency: storage is confirmed in UAE North region; no cross-region replication of sensitive data | 🔴 CRITICAL | | |

---

## Section B – Bronze Layer Controls

*Complete this section for every new Bronze source onboarding or Bronze schema change.*

### B.1 Raw Ingestion Compliance

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| B.1.1 | No business transformation logic is applied in the Bronze pipeline (type normalisation for technical reasons only is permitted) | 🔴 CRITICAL | | |
| B.1.2 | All source data types are preserved exactly as they arrive from the source system | 🔴 CRITICAL | | |
| B.1.3 | Bronze table is append-only; UPDATE and DELETE DML statements are not present in the ingestion pipeline | 🔴 CRITICAL | | |
| B.1.4 | CDC before-image is preserved in the `_before_image` column for all UPDATE operations | 🔴 CRITICAL | | |
| B.1.5 | CDC operation type (`_op_type`) column is present and populated with `I`, `U`, or `D` | 🟠 HIGH | | |
| B.1.6 | Ingestion is idempotent: re-running the pipeline with the same source batch does not produce duplicate rows | 🟠 HIGH | | |

### B.2 Mandatory Technical Columns

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| B.2.1 | `_ingest_timestamp TIMESTAMP` (UTC) — present and populated | 🟠 HIGH | | |
| B.2.2 | `_source_file_name STRING` — present and populated (NULL permitted for CDC sources without file reference) | 🟡 MEDIUM | | |
| B.2.3 | `_batch_id STRING` — present and populated with the pipeline run ID | 🟠 HIGH | | |
| B.2.4 | `_op_type STRING` — present and populated | 🟠 HIGH | | |
| B.2.5 | `_source_load_date DATE` — present and populated with the business date of the source extract | 🟠 HIGH | | |

### B.3 Naming & Physical Standards

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| B.3.1 | Table name follows the pattern `bronze.<source_system>.<source_schema>_<source_table>` (all lowercase snake_case) | 🟠 HIGH | | |
| B.3.2 | Source system code in the table name matches the authoritative source register | 🟠 HIGH | | |
| B.3.3 | Partition key is defined based on ingestion pattern (see Bronze Layer Guidelines Section 4.4) | 🟡 MEDIUM | | |
| B.3.4 | `delta.logRetentionDuration = 'interval 90 days'` is set | 🟡 MEDIUM | | |
| B.3.5 | `delta.deletedFileRetentionDuration = 'interval 30 days'` is set | 🟡 MEDIUM | | |
| B.3.6 | `delta.autoOptimize.optimizeWrite = true` is set | 🟢 LOW | | |
| B.3.7 | `delta.enableChangeDataFeed = true` is set for CDC source tables | 🟡 MEDIUM | | |

### B.4 Catalogue Registration

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| B.4.1 | Table is registered in Unity Catalog before first production data load | 🟠 HIGH | | |
| B.4.2 | Table comment includes: source system name, ingestion type (CDC/batch), refresh cadence | 🟠 HIGH | | |
| B.4.3 | Column comments populated for all columns (at minimum: source column name, source data type) | 🟡 MEDIUM | | |
| B.4.4 | Data classification tags applied to PII and sensitive columns | 🔴 CRITICAL | | |
| B.4.5 | Ingestion SLA documented (e.g., CDC lag < 5 min, batch by 04:00 UTC) | 🟡 MEDIUM | | |

### B.5 Schema Governance

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| B.5.1 | Source schema change detection is in place (e.g., Autoloader `cloudFiles.inferColumnTypes` with schema evolution alerting) | 🟠 HIGH | | |
| B.5.2 | Schema change log entry exists for this onboarding, capturing columns, types, and nullable flags | 🟡 MEDIUM | | |
| B.5.3 | Foreign key relationships from source are documented in Unity Catalog or the data catalogue (even if not enforced physically) | 🟢 LOW | | |

---

## Section S – Silver Layer Controls

*Complete this section for new Silver entities or attribute-level changes to existing Silver entities.*

### S.1 Architecture Conformance

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.1.1 | The Silver model is in the correct sub-namespace: `silver_ods.*` for ODS, `silver_analytical.*` for domain analytical | 🔴 CRITICAL | | |
| S.1.2 | The change does not land in any non-standard catalog (no `silver_dev_v2`, `silver_new`, or ad-hoc schema names) | 🔴 CRITICAL | | |
| S.1.3 | The entity is domain-aligned (Silver Analytical) or source-system-aligned (Silver ODS) as per the architecture | 🔴 CRITICAL | | |
| S.1.4 | Business logic is applied at Silver; the Silver pipeline does not delegate transformation to Gold or Bronze | 🟠 HIGH | | |

### S.2 Entity Design

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.2.1 | Entity name follows `silver_analytical.<domain>.<entity_name>` pattern (snake_case, domain matches approved domain list) | 🟠 HIGH | | |
| S.2.2 | Entity has a declared primary key: `<entity>_key` (surrogate, SHA2-256) + `source_<entity>_id` (natural key retained) | 🔴 CRITICAL | | |
| S.2.3 | SHA2-256 surrogate key generation uses the documented key manifest for this entity (stored in `modeling-guidelines/key-manifests/`) | 🟠 HIGH | | |
| S.2.4 | Natural / source business key is preserved as a companion column alongside the surrogate key | 🟠 HIGH | | |
| S.2.5 | All foreign keys reference Silver Analytical surrogate keys of the referenced entity | 🟠 HIGH | | |

### S.3 SCD Compliance

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.3.1 | The SCD type for this entity is explicitly declared (Type 0 / 1 / 2) in the table comment and the model documentation | 🔴 CRITICAL | | |
| S.3.2 | If this is a core entity (Party, Account, Contract, Loan, Exposure, GL Posting), SCD Type 2 is applied unless a waiver is recorded | 🔴 CRITICAL | | |
| S.3.3 | SCD Type 2 columns are present with the exact standard names: `effective_from_date DATE`, `effective_to_date DATE`, `is_current BOOLEAN` | 🔴 CRITICAL | | |
| S.3.4 | Current records have `effective_to_date = '9999-12-31'` (not NULL) | 🔴 CRITICAL | | |
| S.3.5 | `effective_from_date` represents business effective time, not system load timestamp | 🔴 CRITICAL | | |
| S.3.6 | High-velocity attributes (daily balances, intraday rates) are NOT modelled as SCD2 columns; they are in separate snapshot tables | 🟠 HIGH | | |
| S.3.7 | Rationale for chosen SCD type is documented in table comment or ADR | 🟡 MEDIUM | | |

### S.4 Mandatory Audit Columns

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.4.1 | `source_system_code STRING NOT NULL` — present and populated | 🟠 HIGH | | |
| S.4.2 | `source_batch_id STRING NOT NULL` — present and populated | 🟠 HIGH | | |
| S.4.3 | `source_ingestion_date DATE NOT NULL` — present and populated | 🟠 HIGH | | |
| S.4.4 | `record_created_ts TIMESTAMP NOT NULL` (UTC) — present and populated | 🟠 HIGH | | |
| S.4.5 | `record_updated_ts TIMESTAMP` (UTC) — present; populated for Type 1 tables, NULL for historical Type 2 rows | 🟡 MEDIUM | | |
| S.4.6 | `is_deleted BOOLEAN NOT NULL` — present and populated | 🟠 HIGH | | |

### S.5 Column Standards

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.5.1 | All column names are `snake_case` (no camelCase or PascalCase) | 🟠 HIGH | | |
| S.5.2 | Boolean columns are prefixed `is_` or `has_` | 🟡 MEDIUM | | |
| S.5.3 | Date columns are suffixed `_date`; timestamp columns are suffixed `_ts` | 🟡 MEDIUM | | |
| S.5.4 | Amount columns are suffixed `_amount`; each has a companion `_currency_code CHAR(3)` column | 🟠 HIGH | | |
| S.5.5 | Rate / percentage columns are suffixed `_rate` or `_pct` | 🟡 MEDIUM | | |
| S.5.6 | Abbreviations used are from the approved platform abbreviation dictionary | 🟡 MEDIUM | | |
| S.5.7 | Monetary amounts use `DECIMAL(28,8)` or `DECIMAL(18,4)` per the data type standard | 🟠 HIGH | | |
| S.5.8 | All timestamps stored in UTC; source timezone documented in column comment if different | 🟠 HIGH | | |
| S.5.9 | NULL handling: mandatory columns validated as NOT NULL in the pipeline; optional columns NULL-permitted with documented semantics | 🟠 HIGH | | |
| S.5.10 | Sentinel values for open-ended dates use `'9999-12-31'` (not NULL) | 🔴 CRITICAL | | |

### S.6 Data Quality

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.6.1 | DQ checks are defined and passing: uniqueness of primary key | 🔴 CRITICAL | | |
| S.6.2 | DQ checks are defined and passing: not-null on all NOT NULL columns | 🟠 HIGH | | |
| S.6.3 | DQ checks are defined and passing: referential integrity for all FK columns | 🟠 HIGH | | |
| S.6.4 | DQ checks are defined and passing: accepted values for all code / status / enum columns | 🟡 MEDIUM | | |
| S.6.5 | Silver row count reconciled against Bronze control totals; result logged and within tolerance | 🟠 HIGH | | |
| S.6.6 | Records failing DQ checks are quarantined (written to DQ quarantine table) rather than silently dropped | 🟠 HIGH | | |

### S.7 Cross-Domain Integrity

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.7.1 | The entity does not redefine a concept already defined in another Silver Analytical domain (single concept = single entity) | 🔴 CRITICAL | | |
| S.7.2 | Cross-domain FK relationships (e.g., `party_key`, `account_key`) reference the authoritative Silver Analytical entity | 🟠 HIGH | | |
| S.7.3 | Lineage edge from this entity to its Bronze source(s) is registered in Unity Catalog | 🟡 MEDIUM | | |
| S.7.4 | Entity is linked to its Business Glossary entry | 🟡 MEDIUM | | |

### S.8 Banking Domain Model Completeness

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| S.8.1 | If this entity is "Party", it uses the Party Role pattern (no hard-coded role flags on the party entity) | 🔴 CRITICAL | | |
| S.8.2 | If this entity is "Account", it is part of the unified Account hierarchy (not a standalone silo per product) | 🔴 CRITICAL | | |
| S.8.3 | If this is a financial transaction entity, grain is unambiguously declared (one row per what?) | 🔴 CRITICAL | | |
| S.8.4 | If this entity contains "status" information, legal state and operational state are separate (not conflated in one field) | 🟠 HIGH | | |
| S.8.5 | KYC/AML attributes (PEP flag, sanctions status, KYC risk rating) are present on the Party entity if customer data is in scope | 🟠 HIGH | | |

---

## Section G – Gold Layer Controls

*Complete this section for new Gold models, metric additions, or structural changes to Gold tables.*

### G.1 Architecture Conformance

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.1.1 | All Gold pipelines read from Silver only (no Bronze reads) | 🔴 CRITICAL | | |
| G.1.2 | Table name follows the pattern `gold_<category>.<subcategory>.<type>_<subject>` (type prefix: `fact_`, `dim_`, `agg_`, `rpt_`, `bridge_`, `mart_`) | 🟠 HIGH | | |
| G.1.3 | The Gold model does not redefine business logic already defined in Silver | 🔴 CRITICAL | | |
| G.1.4 | This Gold model is the single authoritative source for its metrics (no duplicate definition in another Gold model or BI tool) | 🔴 CRITICAL | | |

### G.2 Dimensional Modelling (where applicable)

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.2.1 | Grain is declared explicitly in the table comment: "One row per [X] per [Y]" | 🔴 CRITICAL | | |
| G.2.2 | Primary key enforces the declared grain (no duplicate rows per grain combination) | 🔴 CRITICAL | | |
| G.2.3 | Every measure is classified: Additive / Semi-additive / Non-additive — documented in table comment | 🟠 HIGH | | |
| G.2.4 | Fact table references conformed dimension surrogate keys (not raw source IDs for joins) | 🟠 HIGH | | |
| G.2.5 | Conformed dimensions (`dim_date`, `dim_customer`, `dim_account`, `dim_product`, `dim_channel`, `dim_currency`, `dim_business_unit`) are referenced, not redefined | 🟠 HIGH | | |
| G.2.6 | Many-to-many relationships use bridge/factless fact tables (not denormalised lists in a single column) | 🟡 MEDIUM | | |

### G.3 Metric & KPI Standards

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.3.1 | All new metrics introduced in this model are registered in the Metrics Catalogue with formula, numerator/denominator Silver sources, grain, and business owner | 🔴 CRITICAL | | |
| G.3.2 | Metric definitions have been approved by the named business owner | 🟠 HIGH | | |
| G.3.3 | If this is a regulatory metric (LCR, CAR, ECL, RWA): the formula references the applicable regulation or CBUAE circular | 🔴 CRITICAL | | |
| G.3.4 | Metrics are registered in the semantic layer tool; BI tools must not define their own metric logic for any certified metric | 🟠 HIGH | | |
| G.3.5 | Metric parity check between the semantic layer definition and the Gold physical table has been validated | 🟡 MEDIUM | | |

### G.4 Mandatory Audit Columns

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.4.1 | `reporting_date DATE NOT NULL` — present and populated | 🔴 CRITICAL | | |
| G.4.2 | `gold_created_ts TIMESTAMP NOT NULL` (UTC) — present and populated | 🟠 HIGH | | |
| G.4.3 | `silver_source_version STRING NOT NULL` — present and populated with Silver pipeline run ID | 🟠 HIGH | | |
| G.4.4 | `pipeline_run_id STRING NOT NULL` — present and populated | 🟠 HIGH | | |
| G.4.5 | `is_restated BOOLEAN NOT NULL` — present and populated | 🟠 HIGH | | |
| G.4.6 | `restatement_reason STRING` — present; NULL for non-restated rows; populated for all restated rows | 🟡 MEDIUM | | |

### G.5 Data Quality & Certification

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.5.1 | All dbt/GX tests pass: uniqueness (grain PK), not-null, referential integrity, accepted values | 🔴 CRITICAL | | |
| G.5.2 | Financial totals reconcile to Silver control totals within ±0.01% | 🔴 CRITICAL | | |
| G.5.3 | Business owner has signed off on the model output for the first production load | 🔴 CRITICAL | | |
| G.5.4 | Model is registered in `gold_control.model_certification_register` with certification date and approver | 🟠 HIGH | | |
| G.5.5 | End-to-end lineage from Gold to Bronze registered in Unity Catalog | 🟠 HIGH | | |
| G.5.6 | Query performance validated on representative data volumes; SLA met (query < defined threshold) | 🟡 MEDIUM | | |

### G.6 Regulatory Model Controls (where applicable)

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.6.1 | Regulatory snapshots are not overwritten once the reporting period closes (immutable after close) | 🔴 CRITICAL | | |
| G.6.2 | Restatements of closed periods use new rows with `is_restated = true`; original rows not deleted | 🔴 CRITICAL | | |
| G.6.3 | Reconciliation results logged in `gold_control.reconciliation_log` and signed off by the data steward | 🟠 HIGH | | |
| G.6.4 | Full lineage trail from every regulatory submission number to Bronze is available and documented | 🔴 CRITICAL | | |
| G.6.5 | Month-end and quarter-end snapshots are produced by the required deadline (08:00 UTC on the first business day after period close) | 🟠 HIGH | | |

### G.7 Physical Design

| # | Control | Severity | Status | Evidence / Notes |
|---|---------|----------|--------|-----------------|
| G.7.1 | Gold model is fully materialised as a Delta table (not a view over Silver, unless it is a documented thin passthrough) | 🟡 MEDIUM | | |
| G.7.2 | Partition key is `reporting_date` for all periodic snapshot and regulatory tables | 🟡 MEDIUM | | |
| G.7.3 | Row-level security configured for sensitive Gold models (credit scores, internal ratings, individual risk data) | 🟠 HIGH | | |
| G.7.4 | DDL and transformation code version-controlled in Git and promoted via CI/CD pipeline | 🟠 HIGH | | |

---

## Compliance Outcome

| Outcome | Definition | Action |
|---|---|---|
| ✅ **Compliant** | All CRITICAL and HIGH controls pass (or have a recorded waiver) | Approved for production deployment |
| ⚠️ **Conditionally Compliant** | No CRITICAL failures; some HIGH or MEDIUM gaps with documented remediation plan and owner | Approved for production with remediation tracked in ADO/Jira; must reach Compliant within 30 days |
| ❌ **Non-Compliant** | One or more CRITICAL controls fail | Blocked from production deployment; must resolve all CRITICAL failures and re-submit |

### Waiver Process

Any HIGH control may be waived with:
1. Written justification documenting why the control cannot be met and the risk accepted
2. ARB written approval (email or ADO sign-off)
3. Waiver expiry date (maximum 90 days; must be re-evaluated before expiry)

CRITICAL controls cannot be waived except in the case of documented regulatory emergency with CISO and Head of Data Architecture sign-off.

---

## Sign-Off

| Role | Name | Date | Signature |
|---|---|---|---|
| Data Architect (reviewer) | | | |
| Data Domain Steward | | | |
| ARB Representative (if breaking change or waiver) | | | |
| Business Owner (Gold models only) | | | |
