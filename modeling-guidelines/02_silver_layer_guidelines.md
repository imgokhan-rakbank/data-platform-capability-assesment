# Silver Layer – Logical Data Modelling Guidelines

> **Version:** 1.0  
> **Owner:** Data Architecture & Modelling  
> **Applies to:** All Silver layer models  
> **Industry references:** DAMA-DMBOK2, ISO/IEC 11179, Inmon CDW, Kimball Dimensional Modelling, FS-LDM (BIAN), IFW (IBM), IFRS 9, Basel III

---

## 1. Purpose & Scope

The Silver layer is the **conformed, cleansed, and integrated** zone of the Medallion Architecture. It is the primary semantic modelling layer of the platform and the single authoritative source for all business entities. Every Gold model, report, and analytics product must trace its data to Silver.

Silver uses a single unified namespace under the `silver` catalog:

| Layer | Namespace | Purpose | SCD Strategy |
|---|---|---|---|
| **Silver** | `silver.<domain>.<entity>` | Domain-aligned enterprise data model; cleansed, conformed across sources; full history for core entities | SCD Type 2 for core entities; SCD Type 1 for reference/operational tables (see Section 5) |

All Silver entities reside under a single `silver` catalog per environment tier (dev, test, prod).

---

## 2. Core Principles

| # | Principle | Rationale |
|---|-----------|-----------|
| S-P1 | **Single authoritative Silver catalog** – One `silver` catalog per environment tier (dev, test, prod) | Eliminates parallel-catalog ambiguity that creates consumer confusion and reconciliation failures |
| S-P2 | **Domain-driven, not source-driven** – Silver is organised by business domain, not by source system | Decouples consumer models from source system changes; supports multi-source entity resolution |
| S-P3 | **No duplication of business logic** – Each business rule is applied once in Silver; not re-applied in Gold | Single point of change reduces drift between reports |
| S-P4 | **Full historisation for core entities** – All entities listed in the SCD mandate (Section 5) must carry SCD Type 2 | Supports point-in-time regulatory reporting and audit |
| S-P5 | **Cross-source entity resolution at Silver** – Silver is the point at which multiple source feeds for the same entity are reconciled | Prevents Gold from needing to handle source system divergence |
| S-P6 | **Surrogate key ownership** – Silver generates and owns all surrogate keys; Gold only inherits them | Decouples logical identity from source natural keys |

---

## 3. Data Modelling Standards

### 3.1 Normalisation Target – Third Normal Form (3NF) with Pragmatic Exceptions

Silver entities **must target Third Normal Form (3NF)** as the default modelling approach. 3NF is the right level of normalisation for an enterprise integration layer because it:
- Eliminates update anomalies that arise when the same fact is stored in multiple places
- Maximises reusability — a single entity can serve multiple Gold models without carrying consumer-specific denormalisation
- Aligns with the Inmon CDW / FS-LDM / BIAN canonical model approach used as a reference for this platform

| Normal Form | Requirement | Notes |
|---|---|---|
| **1NF** | All columns are atomic; no repeating groups or arrays as entity attributes | Arrays / nested structures are permitted only as serialised audit/metadata columns (e.g., `_before_image`); never as queryable entity attributes |
| **2NF** | Every non-key attribute is fully dependent on the whole primary key | Particularly important for composite-key entities; partial dependencies must be extracted into their own entity |
| **3NF** | No transitive dependencies between non-key attributes | E.g., `branch_region` must not sit on `account`; it belongs on `branch` |
| **Pragmatic exception** | Controlled, documented denormalisation is permitted only where a 3NF join would create a measurable and unacceptable query performance problem **and** where the data architecture team has approved the exception in an ADR | Denormalised attributes must still carry lineage back to the normalised source column |

> **What Silver is not:** Silver is **not** a dimensional model (no star/snowflake schema). It is **not** a wide denormalised mart. Dimensional modelling belongs in Gold.

### 3.2 Proximity to Source – Domain-Aligned, Not Source-System-Aligned

Silver models must be **domain-aligned**, not a thin rename of the source system table structure.

| Dimension | Guideline |
|---|---|
| **Entity abstraction level** | Model the business concept, not the source table. A single Silver `party` entity may integrate records from Finacle, Flexcube, and a CRM — it must not have one entity per source system |
| **Source system column names** | Do not carry source-system-specific column names into Silver (e.g., `FINACLE_ACCT_NO`). Use platform-canonical names per Section 4.2 |
| **Source system internal codes** | Decode source-specific status codes, type codes, and flags to platform-canonical reference values using `silver.reference.*` lookup tables before storing on the entity |
| **Source table structure** | Do not replicate source system table structure. If a source has a single wide `CUSTOMER_MASTER` table with 200 columns, decompose it into `party`, `party_address`, `party_contact`, `party_identification` in Silver |
| **Multi-source integration** | When the same concept comes from multiple source systems, resolve to a single entity per the entity resolution rules (Section 7.1). Do not create `customer_finacle` and `customer_flexcube` as separate Silver entities |
| **Source system coupling** | Silver pipelines may reference Bronze source system code (`source_reference_id`) for audit purposes only; no Silver business logic may branch on source system identity |

### 3.3 Bronze Detail Preservation – What to Carry Forward and What to Leave Behind

Silver must be complete enough to serve any downstream Gold consumer without requiring Bronze to be re-read. At the same time, Silver must not carry unnecessary noise that degrades query performance and model clarity.

| Category | Rule |
|---|---|
| **Business attributes** | Carry all business-meaningful attributes from Bronze, even if no current Gold consumer uses them. Omitting attributes is a point-of-no-return decision that forces Bronze re-reads later |
| **Technical ingestion columns** | Do **not** carry Bronze pipeline-internal columns (`_source_file_name`, `_batch_id`, `_op_type`, `_before_image`) into Silver entity columns. Summarise lineage via Silver audit columns (`run_id`, `source_ingestion_date`) instead |
| **Duplicate / redundant columns** | If Bronze preserves both a code and its already-decoded description from the source, carry the code plus the platform-decoded description; drop the source-system description column unless it contains independent information |
| **Before-image data** | Do not surface Bronze `_before_image` JSON as Silver entity columns. SCD Type 2 versions capture the change history; before-images exist in Bronze for replay only |
| **Derived Bronze columns** | Bronze may contain columns that are simple arithmetic of other Bronze columns (e.g., `total_amount = principal + interest` already present in source). Carry these only if they are defined in the source system data dictionary; do not re-derive them independently in Silver |
| **NULL / empty columns** | If a Bronze column is NULL for 100% of rows across all loads (verified over at least one full business cycle), it may be excluded with Data Architecture approval and a documented ADR. Do not routinely trim columns based on current population rates |
| **High-cardinality event data** | Bronze event or transaction rows (e.g., every individual GL posting) must all be preserved in Silver at atomic grain — do not pre-aggregate. Aggregation is Gold's responsibility |
| **Sensitive / restricted attributes** | PII and PCI columns must be carried into Silver (required for entity resolution and downstream risk models) but must be tagged with Unity Catalog classification labels and access-controlled at the column level |

### 3.4 Entity Granularity

| Entity Class | Required Grain | Notes |
|---|---|---|
| Core master entities (Party, Account, Product, Collateral) | One row per entity per SCD Type 2 version | Current version: `is_current = true`; history: `is_current = false` |
| Agreement / Contract | One row per contract per SCD Type 2 version | Loan drawdown events are separate child entities, not versions of the contract |
| Transaction / Event | One row per source event (immutable) | No aggregation; no deduplication beyond exact structural duplicates from Bronze mechanics |
| GL Posting | One row per posting line (immutable) | Subledger and general ledger postings are separate entities |
| Reference / Code | One row per code value per effective period | SCD Type 1 for most; SCD Type 2 if regulatory history of code changes is required |
| Snapshot / Measurement | One row per entity per measurement date | Daily balance, daily exposure, collateral valuation — separate snapshot tables, not SCD2 on the parent entity |

---

## 4. Naming Conventions

### 4.1 Silver Namespace

```
silver.<domain>.<entity_name>
```

| Domain | Entity Example |
|---|---|
| `customer` | `silver.customer.party` |
| `account` | `silver.account.account` |
| `loan` | `silver.loan.loan_agreement` |
| `credit_card` | `silver.credit_card.card_account` |
| `risk_credit` | `silver.risk_credit.credit_exposure` |
| `gl` | `silver.gl.gl_posting` |
| `reference` | `silver.reference.currency` |

### 4.2 Column Naming Rules

| Rule | Standard |
|---|---|
| Case style | `snake_case` throughout; no camelCase or PascalCase |
| Primary key | `<entity_name>_key` (surrogate) + `<entity_name>_id` or `source_<entity_name>_id` (business/natural key) |
| Foreign key | `<referenced_entity>_key` referencing Silver surrogate |
| Boolean | Prefix `is_` or `has_` (e.g., `is_active`, `has_collateral`) |
| Date columns | Suffix `_date` (e.g., `effective_date`, `maturity_date`) |
| Timestamp columns | Suffix `_ts` (e.g., `created_ts`, `updated_ts`) |
| Amount columns | Suffix `_amount` with currency code companion column |
| Rate / percentage | Suffix `_rate` or `_pct` (e.g., `interest_rate`, `pd_pct`) |
| Indicator/flag | Suffix `_flag` or `_ind` for non-boolean integer flags |
| SCD columns | Standardised names: `effective_from`, `effective_to`, `is_current` (see Section 5) |
| Audit columns | Standardised names defined in Section 6 |
| Abbreviations | Use the platform abbreviation dictionary; do not invent new abbreviations |

---

## 5. Surrogate Key Strategy

### 5.1 Key Design

| Layer | Key Type | Generation Method |
|---|---|---|
| Silver | `SHA2-256` hash surrogate key | Hash of the entity's defined business key set (see entity-level key manifest) |

### 5.2 Surrogate Key Generation Standard

```sql
-- Standard surrogate key pattern (Silver)
SHA2(
    CONCAT_WS('|',
        UPPER(TRIM(COALESCE(source_system_code, 'UNKNOWN'))),
        UPPER(TRIM(COALESCE(source_entity_id,   'UNKNOWN')))
    ),
    256
) AS party_key
```

Rules:
- Hash input columns must be documented in the entity's key manifest file (stored in the `modeling-guidelines/key-manifests/` folder)
- `COALESCE` sentinel value for null components must be `'UNKNOWN'` (not empty string, not `NULL`)
- `UPPER(TRIM(...))` applied to all string components to prevent case/whitespace-induced duplicates
- The hash algorithm is `SHA2-256` platform-wide; no other algorithm is permitted
- Source natural keys are **always retained** as a companion column (`source_<entity>_id`) to enable traceability back to Bronze

### 5.3 Cross-Layer Key Traceability

Every Silver entity must support the following traceability chain:

```
Gold Record → silver.<domain>.<entity>_key
           → silver.<domain>.<entity>.source_<entity>_id
           → bronze.<source_system>.<table>.<natural_key_column>
```

---

## 6. SCD (Slowly Changing Dimension) Strategy

### 6.1 SCD Type 2 Column Standard

Every SCD Type 2 entity **must** include exactly these three columns with these exact names and types:

| Column | Data Type | Constraints | Semantics |
|---|---|---|---|
| `effective_from` | `DATE` | NOT NULL | Business effective start date of this version (not the system load date) |
| `effective_to` | `DATE` | NOT NULL; `9999-12-31` for the current version | Business effective end date of this version (inclusive); `9999-12-31` sentinel for open-ended current version |
| `is_current` | `BOOLEAN` | NOT NULL | `true` if this is the current (latest) version; `false` for historical versions |

> **Fail condition:** Use of `NULL` for `effective_to` of the current record is prohibited. The `9999-12-31` sentinel must be used to ensure that range-scan queries (`WHERE effective_to >= reporting_date`) work without null-handling.

### 6.2 System Load Time vs. Business Effective Time

| Column | Semantics | Required |
|---|---|---|
| `effective_from` | **Business** effective date — when the change was effective in the real world | Mandatory for Type 2 |
| `record_created_ts` | **System** timestamp — when this row was inserted into Silver | Mandatory for all entities |
| `record_updated_ts` | **System** timestamp — when this row was last updated in Silver (Type 1 only) | Mandatory for Type 1 |

Never conflate business effective time with system load time. The two are distinct columns with distinct semantics.

### 6.3 High-Velocity Attribute Policy

Attributes that change frequently (e.g., daily balance, intraday position) must not be modelled as SCD2 attributes of a parent entity. They must be modelled as separate fact/snapshot tables to avoid row explosion. Examples:

| Volatile Concept | Correct Modelling Pattern |
|---|---|
| Daily account balance | Separate `account_daily_balance` fact table (one row per account per day) |
| Intraday FX rates | `reference.fx_rate` table partitioned by date |
| Collateral market value | `collateral_valuation_history` fact table |

---

## 7. Mandatory Audit Columns

Every Silver table **must** include the following columns, populated by the pipeline:

| Column | Data Type | Nullable | Description |
|---|---|---|---|
| `source_reference_id` | `STRING` | NOT NULL | Short code of the source system (e.g., `FINACLE`, `FLEXCUBE`, `FIS`) |
| `run_id` | `STRING` | NOT NULL | Batch/pipeline run ID from which this record was loaded |
| `source_ingestion_date` | `DATE` | NOT NULL | Business date of the source extract that produced this row |
| `record_created_ts` | `TIMESTAMP` (UTC) | NOT NULL | System timestamp when this row was first inserted into Silver |
| `record_updated_ts` | `TIMESTAMP` (UTC) | Nullable | System timestamp of last update (only for Type 1 tables; NULL for Type 2 historical rows) |
| `is_deleted` | `BOOLEAN` | NOT NULL | `true` if this record has been soft-deleted (CDC Delete event processed); `false` otherwise |
| `dq_score` | `DECIMAL(5,2)` | Nullable | Optional data quality score (0–100) assigned by the DQ pipeline; NULL if DQ checks not yet run |

---

## 8. Transformation Rules – Permitted and Prohibited

Silver is the **one and only** place where raw Bronze data is cleansed, conformed, and integrated into business entities. The scope of transformations is deliberately bounded: Silver must produce trusted, reusable, source-agnostic entities — not analytical or business-metric outputs. All transformations applied must be reproducible, documented, and deterministic.

### 8.1 Permitted Transformations

The following transformations are **explicitly allowed** at Silver:

| Category | Permitted Transformation | Example |
|---|---|---|
| **Data type casting** | Cast source-preserved Bronze types to canonical Silver types (e.g., string date → `DATE`, string amount → `DECIMAL(18,4)`) | `CAST(acct_open_dt AS DATE)` |
| **Null handling / defaulting** | Replace nulls with a documented, domain-approved sentinel or default where the business rule is clear and agreed | `COALESCE(middle_name, '')` — only if business rules mandate it; must be documented |
| **String normalisation** | Trim whitespace, standardise case (e.g., `UPPER` for country codes, `INITCAP` for person names) | `UPPER(TRIM(country_code))` |
| **Code / lookup decoding** | Translate source system codes to platform-canonical codes using a managed reference table registered in Silver | `LEFT JOIN silver.reference.account_status_map` |
| **Unit standardisation** | Standardise currencies, units of measure to a platform canonical (e.g., minor currency units → major) | Amount in fils → AED: `amount_fils / 100` |
| **Date / timestamp normalisation** | Normalise source timezone-unaware timestamps to UTC; standardise date formats | `CONVERT_TIMEZONE('UTC', raw_ts)` |
| **Deduplication** | Remove exact structural duplicates created by Bronze ingestion mechanics (e.g., duplicate CDC events) using a documented deduplication key and rule | `ROW_NUMBER() OVER (PARTITION BY source_id ORDER BY _ingest_timestamp DESC) = 1` |
| **Entity resolution / golden record** | Reconcile multiple source feeds for the same real-world entity into a single canonical record using a defined match-merge rule | Merge Finacle and Flexcube customer records on national ID |
| **SCD Type 2 version management** | Apply SCD Type 2 change detection (hash compare), close prior version (`effective_to`, `is_current = false`), insert new version | Standard SCD2 MERGE pattern |
| **Surrogate key generation** | Generate SHA2-256 surrogate keys per the standard in Section 4 | `SHA2(CONCAT_WS('\|', ...), 256)` |
| **Audit column population** | Populate mandatory audit columns (`source_reference_id`, `run_id`, `record_created_ts`, etc.) | Pipeline framework auto-populates |
| **Referential integrity enforcement** | Validate FK relationships; route violations to DQ quarantine | Pipeline DQ check before Silver write |
| **Soft-delete flagging** | Set `is_deleted = true` when a CDC Delete event is received; do not physically delete rows | `is_deleted = true` on `_op_type = 'D'` |
| **Domain attribute derivation (structural only)** | Derive attributes that are **direct structural derivations** of source attributes with no ambiguity and no business policy decision involved | Full name from `first_name + last_name`; age band from `date_of_birth` |

### 8.2 Prohibited Transformations

The following are **explicitly prohibited** at Silver. Violations are a Critical severity breach:

| Category | Prohibited Transformation | Why Prohibited | Where It Belongs |
|---|---|---|---|
| **Business metric calculation** | Calculating KPIs, ratios, or aggregates that require a business policy decision | Silver must not pre-bake metric definitions that belong to the semantic/metric layer | Gold layer or semantic layer |
| **Aggregation** | Summing, averaging, counting, or grouping across rows (except as part of deduplication logic) | Silver rows must represent individual business entities, not summary views | Gold `agg_` or `rpt_` models |
| **Risk / financial model application** | Applying IFRS 9 staging rules, ECL calculation, Basel RWA weightings, or any regulatory formula | These are business models that must be version-controlled and approved separately | Gold regulatory models |
| **Filtering / subsetting for a consumer** | Removing rows to create a subset for a specific downstream consumer or business unit | Silver is universal; consumers must filter at the Gold or semantic layer | Gold `mart_` models |
| **Joining to Gold** | Reading Gold tables to enrich Silver entities | Creates circular dependency; Silver must only read Bronze (and other Silver entities for FK resolution) | Not permitted at any layer |
| **Direct Bronze reads bypassed** | Transforming data from a source not yet in Bronze (e.g., reading directly from source APIs or flat files in a staging area) | All data must transit Bronze for auditability and replay capability | Bronze ingestion must be created first |
| **Hardcoded business constants** | Embedding business thresholds, rates, or classification values directly in pipeline code rather than a managed reference table | Breaks single point of change; untraceable in lineage | `silver.reference.*` managed tables |
| **Consumer-specific column aliasing** | Renaming columns to match a specific BI tool, report, or team preference | Naming must follow the platform standard (Section 3); consumer mapping belongs in the semantic layer | Semantic/metric layer or Gold |
| **Currency conversion at entity level** | Converting amounts to a reporting currency within a Silver entity row | FX rates change daily; point-in-time conversion belongs in Gold snapshot models with the correct rate date | Gold periodic snapshot |
| **Lossy transformation** | Any transformation that reduces information content without a documented, reversible rule | Violates Silver's role as the auditable source of truth | Not permitted at any layer |
| **Removing audit / lineage columns** | Dropping or overwriting `source_reference_id`, `run_id`, `record_created_ts`, or any Bronze technical column | Breaks the full traceability chain required for regulatory audit | Not permitted at any layer |

### 8.3 Decision Guide — "Does This Belong in Silver?"

Use this decision tree before implementing any transformation:

```
Is it cleaning, conforming, or structurally integrating raw data?
  ├── Yes → Does it require a business policy decision or introduce
  │         analytical interpretation?
  │           ├── No  → ✅ Permitted in Silver
  │           └── Yes → ❌ Move to Gold or semantic layer
  └── No  → Is it aggregation, metric calculation, or consumer filtering?
              ├── Yes → ❌ Move to Gold
              └── No  → Consult Data Architecture before implementing
```

### 8.4 Transformation Documentation Requirement

Every non-trivial transformation applied in a Silver pipeline **must** be documented:

| Requirement | Standard |
|---|---|
| Description | Plain-English description of what the transformation does and why |
| Business rule reference | Link to the business rule document or data dictionary entry that governs the transformation |
| Source column(s) | Bronze column(s) from which the output is derived |
| Target column | Silver column name |
| Null / exception handling | How nulls, unexpected values, or out-of-range inputs are handled |
| Test / DQ check | The DQ check that validates the transformation output (must exist before the model goes to production) |
| Change control | Any change to an existing Silver transformation must go through the data change request process and be reviewed by Data Architecture |

---

## 9. Data Quality at Silver

| Requirement | Standard |
|---|---|
| Null validation | All NOT NULL columns must be validated by the pipeline; records failing null validation are written to a DQ quarantine table, not to the main Silver table |
| Type conformance | All columns must match the declared Delta schema; schema evolution must follow the Bronze schema evolution rules carried forward |
| Referential integrity | All FK relationships documented in Section 7.1 must have a corresponding DQ check executed at each load; violations logged to the DQ metrics table |
| Uniqueness | Primary key (surrogate key + SCD version) uniqueness validated at every load; duplicates block the load and alert the pipeline operations team |
| Reconciliation | Silver row counts reconciled against Bronze source counts per batch; tolerance threshold: 0 discrepancy |
| Data quality score | `dq_score` column populated for core entities; minimum acceptable score threshold defined per entity in the DQ policy document |

---

## 10. Silver Best Practices Summary

| # | Best Practice | Severity if Violated |
|---|---|---|
| S-BP1 | Single authoritative `silver` catalog per environment tier | Critical |
| S-BP2 | All Silver entities organised by business domain, not source system | Critical |
| S-BP3 | All core entities have SCD Type 2 applied per the mandate in Section 5 | Critical |
| S-BP4 | `effective_to = '9999-12-31'` (not NULL) for current versions | Critical |
| S-BP5 | SHA2-256 surrogate key generated for all Silver entities | High |
| S-BP6 | Source natural key retained alongside surrogate key | High |
| S-BP7 | All seven mandatory audit columns present and populated | High |
| S-BP8 | No business logic duplicated between Silver and Gold | High |
| S-BP9 | All FK relationships have DQ checks running at each load | High |
| S-BP10 | All Silver entities linked to Business Glossary | Medium |
| S-BP11 | Volatile attributes (daily balance, FX rates) in separate snapshot tables | Medium |
| S-BP12 | Party model uses Party Role pattern (no hard-coded role flags) | Medium |
| S-BP13 | No aggregation, KPI calculation, consumer filtering, or currency conversion applied in Silver pipelines | Critical |
| S-BP14 | Every non-trivial transformation documented with business rule reference, source columns, null-handling, and DQ check | High |
| S-BP15 | Hardcoded business constants replaced with managed reference tables in `silver.reference.*` | High |
| S-BP16 | Silver entities modelled to 3NF; any denormalisation deviation approved via ADR | High |
| S-BP17 | No source-system table structure replicated into Silver; multi-source entities resolved into a single domain entity | Critical |
| S-BP18 | All business-meaningful Bronze attributes carried forward; Bronze pipeline-internal columns (`_batch_id`, `_op_type`, `_before_image`) excluded from Silver entity columns | High |
| S-BP19 | Grain declared in table comment for every Silver entity | High |

---

## 11. Silver Modelling Checklist Reference

See `04_ldm_compliance_checklist.md` → **Section S: Silver Layer Controls** for the full gated compliance check that must be completed before a Silver model change is approved for production.
