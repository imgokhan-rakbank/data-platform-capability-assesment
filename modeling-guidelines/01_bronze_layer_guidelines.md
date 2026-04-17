# Bronze Layer – Logical Data Modelling Guidelines

> **Version:** 1.0  
> **Owner:** Data Architecture & Modelling  
> **Applies to:** All source onboardings that land data in the Bronze Medallion layer  
> **Industry references:** Databricks Medallion Architecture, DAMA-DMBOK2, TOGAF, Delta Lake Best Practices

---

## 1. Purpose & Scope

The Bronze layer is the **immutable raw landing zone** of the Medallion Architecture. Its sole responsibility is to preserve a faithful, unaltered copy of source data exactly as received. No business transformation, filtering, aggregation, or enrichment is permitted. Every source system onboarding must produce a Bronze model that complies with the standards in this document before any Silver modelling begins.

---

## 2. Core Principles

| # | Principle | Rationale |
|---|-----------|-----------|
| B-P1 | **Immutability** – Raw records are never deleted or updated once written | Provides an audit-proof replay capability and regulatory evidence trail (CBUAE, Basel III) |
| B-P2 | **Fidelity** – Source data types, nulls, and encoding are preserved exactly | Prevents silent data loss |
| B-P3 | **Append-only by default** – Changes are captured as insert-new-row, not overwrite | Enables point-in-time reconstruction and Silver reprocessing |
| B-P4 | **Single responsibility** – Ingestion pipelines write Bronze; transformation pipelines read it | Decouples ingestion failures from transformation failures |
| B-P5 | **Schema-first** – Every Bronze table has a declared schema (schema-on-write via Delta) | Prevents corrupt records from silently propagating downstream |

---

## 3. Naming Conventions

### 3.1 Namespace Pattern

```
bronze.<source_system>.<source_schema>_<source_table>
```

**Examples:**

| Source System | Source Schema | Source Table | Bronze Object Name |
|---|---|---|---|
| finacle | crmuser | accounts_dlt | `bronze.finacle.crmuser_accounts_dlt` |
| flexcube | fcubsprod | cltb_amount_paid | `bronze.flexcube.fcubsprod_cltb_amount_paid` |

### 3.2 Naming Rules

| Rule | Requirement |
|---|---|
| Character set | Lowercase letters, digits, and underscores only (`snake_case`) |
| Source system | Must exactly match the authoritative source system code in the platform's source register |
| Source schema/table | Must reflect the actual source object name (do not abbreviate unless the source name itself is abbreviated) |
| Temporary/staging | Prefix with `_tmp_` and suffix with `_YYYYMMDD`; must reside outside the `bronze.*` namespace |
| Real Time Integration | Tables with a `_dlt` suffix represent streaming / real-time (CDC or near‑real‑time) feeds. Tables without the `_dlt` suffix represent batch end-of-day (EOD) state loads. |
| No business naming | Table and column names must not introduce business terminology absent in the source; e.g., do not rename `acct_no` to `account_number` in Bronze |

---

## 4. Physical Schema Standards

### 4.1 Mandatory Technical Columns

Every Bronze table **must** include the following system-level columns. These are appended by the ingestion pipeline; they must not exist in source systems.

| Column Name | Data Type | Nullable | Description |
|---|---|---|---|
| `brz_insert_time` | `TIMESTAMP` (UTC) | NOT NULL | System timestamp at which the record was written to Bronze (pipeline clock, UTC) |
| `_source_file_name` | `STRING` | NOT NULL for file-based; NULL for CDC | Full path of the source file or Kafka topic offset reference |
| `_batch_id` | `STRING` | NOT NULL | Unique pipeline run identifier (correlated with the orchestration job) |
| `_op_type` | `STRING` | NOT NULL for CDC; `I` for full-load | CDC operation: `I` (insert), `U` (update), `D` (delete); full-load rows use `I` |
| `_before_image` | `STRING` (JSON) | Nullable | Serialised before-image for UPDATE events (CDC only); NULL for INSERT/DELETE |
| `_source_load_date` | `DATE` | NOT NULL | Business date of the source extract or CDC batch window (not the pipeline runtime) |

> **Rationale for `_before_image`:** Preserving before-images allows Silver to derive what changed, supporting Type 4 / mini-dimension patterns and change-data audit logs without reading Bronze twice.

### 4.2 Source Column Preservation Rules

| Rule | Requirement |
|---|---|
| Data types | Preserve source types exactly; do not widen or narrow. If source is `VARCHAR(50)`, use `STRING` in Delta (which is unbounded); document source length in column comment. |
| Nulls | Preserve source nulls as Delta `NULL`; do not substitute sentinel values in Bronze |
| Encoding | Resolve encoding (UTF-8 normalisation) at ingestion time only if the source sends non-UTF-8; record this transformation in the ingestion pipeline's metadata log |
| Numeric precision | Map source `DECIMAL(p,s)` to `DECIMAL(p,s)` exactly; do not default to `DOUBLE` |
| Timestamps | Store source timestamps without timezone conversion; capture source timezone if known in column comment |
| Boolean | Preserve source boolean representation (e.g., `Y/N`, `1/0`) as `STRING` or `INTEGER` unless the source is a native boolean type |

### 4.3 Delta Lake Table Properties

| Property | Required Setting | Rationale |
|---|---|---|
| `delta.logRetentionDuration` | `interval 90 days` | Enables 90-day time-travel for Silver reprocessing |
| `delta.deletedFileRetentionDuration` | `interval 30 days` | Aligns with VACUUM policy; prevents premature file deletion |
| `delta.autoOptimize.optimizeWrite` | `true` | Reduces small-file problem for streaming ingestions |
| `delta.autoOptimize.autoCompact` | `true` | Maintains read performance without manual OPTIMIZE jobs |
| `delta.enableChangeDataFeed` | `true` for CDC sources; `false` for full-load | Enables downstream Change Data Feed reads from Silver pipelines |

### 4.4 Partitioning Strategy

| Ingestion Pattern | Recommended Partition Key | Notes |
|---|---|---|
| Daily batch full-load | `_source_load_date DATE` | Enables date-range pruning for Silver incremental processing |
| CDC / streaming | `CAST(brz_insert_time AS DATE)` | Prevents unbounded partition growth; Silver reads last N days |

> **Anti-pattern:** Do not partition by high-cardinality columns (e.g., `account_number`, `customer_id`). Partition keys must have bounded, predictable cardinality.

---

## 5. Storage & Retention Standards

| Standard | Requirement |
|---|---|
| Storage format | Delta Lake only (no raw Parquet, CSV, or JSON files in the `bronze.*` namespace) |
| Retention | 7 years for all Bronze data (regulatory mandate — CBUAE) |
| VACUUM policy | Run VACUUM with `RETAIN 30 DAYS` minimum; never run VACUUM with retention < 7 days |
| Deletion | Hard deletes are prohibited in Bronze. Logical deletes are represented by a CDC `D` row with `_op_type = 'D'` |
| Access | Write access: ingestion pipelines only. Read access: Silver pipelines and authorised audit/data-engineering roles. End-users and analysts must not have direct Bronze access. |

---

## 6. Schema Governance

### 6.1 Schema Evolution Rules

| Change Type | Permitted in Bronze? | Handling |
|---|---|---|
| New column added in source | Yes – additive change | Pipeline detects new column; appends to Delta schema via `mergeSchema = true`; column comment populated automatically; downstream consumers notified via data catalogue alert |
| Column renamed in source | Special handling | Old column retained; new column added; a mapping record is created in the source schema change log; Silver pipeline updated before next load |
| Column removed in source | Retained in Bronze | Column set to `NULL` from the date of removal; a deprecation comment is added in the catalogue with the removal date |
| Data type widened (e.g., `INT` → `BIGINT`) | Yes | Handled by Delta schema evolution |
| Data type narrowed (e.g., `DECIMAL(28,8)` → `DECIMAL(10,2)`) | Blocked | Requires an ADR and a new versioned table; old table continues until Silver migration is complete |
| Table dropped at source | Retained in Bronze | Bronze table is marked `DEPRECATED` in catalogue; no new data lands; retained for 7 years |

### 6.2 Schema Registration

All Bronze tables **must** be registered in Unity Catalog with the following metadata populated before the first data load reaches production:

| Metadata Field | Required |
|---|---|
| Table comment | Source system name, ingestion type (CDC/batch), refresh cadence |
| Column comments | Source column name, source data type, business meaning (if known) |
| Owner tag | `ingestion-pipelines` team |
| Data classification tags | PII / Confidential / Internal / Public (applied per column) |
| Source system tag | Source system code from the platform source register |
| Ingestion SLA | Expected freshness (e.g., `CDC lag < 5 min`, `batch by 04:00 UTC`) |

---

## 7. CDC Handling Standards

| Requirement | Standard |
|---|---|
| Before-image preservation | `_before_image` column captures the prior state as a JSON string for all UPDATE operations |
| Operation code | `_op_type` column always present: `I`, `U`, `D` |
| Delete representation | Delete events are stored as a row with `_op_type = 'D'` and all key columns populated; not a physical deletion of prior rows |
| Out-of-order events | Bronze ingestion appends in arrival order; Silver is responsible for sequencing via `brz_insert_time` and source-provided sequence columns |
| Initial load vs. incremental | Initial full-load rows carry `_op_type = 'I'`; a `_is_initial_load` flag (BOOLEAN) may be added to distinguish initial load rows from steady-state inserts |

---

## 8. Security & Compliance at Ingestion

| Requirement | Standard |
|---|---|
| PII at rest | PII data lands in Bronze unmasked (necessary for Silver deduplication); access controlled at the catalog/schema level to ingestion pipelines and authorised data engineering roles only |
| PCI DSS | Card data (PAN, CVV) must be tokenised or masked **before** writing to Bronze; clear-text card data is prohibited in Bronze |
| Audit logging | All read and write access to Bronze tables must be auditable via Unity Catalog access logs |

---

## 9. Bronze Best Practices Summary

| # | Best Practice | Severity if Violated |
|---|---|---|
| B-BP1 | Never apply business transformation logic in Bronze (type casting for technical reasons only is acceptable) | Critical |
| B-BP2 | Always preserve CDC before-image; never silently drop | Critical |
| B-BP3 | Bronze tables are append-only; UPDATE/DELETE DML is prohibited | Critical |
| B-BP4 | All five mandatory technical columns present and populated | High |
| B-BP5 | Idempotent ingestion: re-running a pipeline does not create duplicate records | High |
| B-BP6 | Schema registered in Unity Catalog before first production load | High |
| B-BP7 | Delta CDF enabled for all CDC source tables | Medium |
| B-BP8 | Partition key defined per table based on ingestion pattern | Medium |
| B-BP9 | VACUUM retention never set below 30 days | Medium |
| B-BP10 | PII column-level classification tags applied before production | High |

---

## 10. Bronze Modelling Checklist Reference

See `04_ldm_compliance_checklist.md` → **Section B: Bronze Layer Controls** for the full gated compliance check that must be completed before a new Bronze source is approved for production.
