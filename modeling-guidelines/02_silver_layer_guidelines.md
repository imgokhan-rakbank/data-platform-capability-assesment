# Silver Layer – Logical Data Modelling Guidelines

> **Version:** 1.0  
> **Owner:** Data Architecture & Modelling  
> **Applies to:** All Silver ODS and Silver Analytical models  
> **Industry references:** DAMA-DMBOK2, ISO/IEC 11179, Inmon CDW, Kimball Dimensional Modelling, FS-LDM (BIAN), IFW (IBM), IFRS 9, Basel III

---

## 1. Purpose & Scope

The Silver layer is the **conformed, cleansed, and integrated** zone of the Medallion Architecture. It is the primary semantic modelling layer of the platform and the single authoritative source for all business entities. Every Gold model, risk report, and analytics product must trace its data to Silver.

Silver is logically divided into two distinct sub-layers with separate physical namespaces:

| Sub-Layer | Namespace | Purpose | SCD Strategy |
|---|---|---|---|
| **Silver ODS** | `silver_ods.<source_system>.<entity>` | Current-state operational data; source-system aligned; low latency | SCD Type 1 (overwrite) |
| **Silver Analytical** | `silver_analytical.<domain>.<entity>` | Domain-aligned enterprise data model; full history; conformed across sources | SCD Type 2 (append new version) |

These two namespaces **must** be physically separate catalogs or schemas. Co-location in a single catalog is not permitted.

---

## 2. Core Principles

| # | Principle | Rationale |
|---|-----------|-----------|
| S-P1 | **Single authoritative Silver** – One catalog/environment per sub-layer per environment tier (dev, test, prod) | Eliminates the parallel-catalog ambiguity that creates consumer confusion and reconciliation failures |
| S-P2 | **Domain-driven, not source-driven** – Analytical Silver is organised by business domain, not by source system | Decouples consumer models from source system changes; supports multi-source entity resolution |
| S-P3 | **No duplication of business logic** – Each business rule is applied once in Silver; not re-applied in Gold | Single point of change reduces drift between reports |
| S-P4 | **Full historisation for core entities** – All entities listed in the SCD mandate (Section 5) must carry SCD Type 2 | Supports point-in-time regulatory reporting and audit |
| S-P5 | **Cross-source entity resolution at Silver** – Silver is the point at which multiple source feeds for the same entity are reconciled | Prevents Gold from needing to handle source system divergence |
| S-P6 | **Surrogate key ownership** – Silver generates and owns all surrogate keys; Gold only inherits them | Decouples logical identity from source natural keys |

---

## 3. Naming Conventions

### 3.1 Silver ODS Namespace

```
silver_ods.<source_system>.<source_schema>_<entity_name>
```

**Examples:**

| Source | Object |
|---|---|
| Finacle GL | `silver_ods.finacle.fin_gl_accounts` |
| Flexcube Loans | `silver_ods.flexcube.lms_loan_master` |
| FIS Cards | `silver_ods.fis.cards_card_account` |

### 3.2 Silver Analytical Namespace

```
silver_analytical.<domain>.<entity_name>
```

| Domain | Entity Example |
|---|---|
| `customer` | `silver_analytical.customer.party` |
| `account` | `silver_analytical.account.account` |
| `loan` | `silver_analytical.loan.loan_agreement` |
| `credit_card` | `silver_analytical.credit_card.card_account` |
| `risk_credit` | `silver_analytical.risk_credit.credit_exposure` |
| `gl` | `silver_analytical.gl.gl_posting` |
| `reference` | `silver_analytical.reference.currency` |

### 3.3 Column Naming Rules

| Rule | Standard |
|---|---|
| Case style | `snake_case` throughout; no camelCase or PascalCase |
| Primary key | `<entity_name>_key` (surrogate) + `<entity_name>_id` or `source_<entity_name>_id` (business/natural key) |
| Foreign key | `<referenced_entity>_key` referencing Silver Analytical surrogate |
| Boolean | Prefix `is_` or `has_` (e.g., `is_active`, `has_collateral`) |
| Date columns | Suffix `_date` (e.g., `effective_date`, `maturity_date`) |
| Timestamp columns | Suffix `_ts` (e.g., `created_ts`, `updated_ts`) |
| Amount columns | Suffix `_amount` with currency code companion column |
| Rate / percentage | Suffix `_rate` or `_pct` (e.g., `interest_rate`, `pd_pct`) |
| Indicator/flag | Suffix `_flag` or `_ind` for non-boolean integer flags |
| SCD columns | Standardised names: `effective_from_date`, `effective_to_date`, `is_current` (see Section 5) |
| Audit columns | Standardised names defined in Section 6 |
| Abbreviations | Use the platform abbreviation dictionary; do not invent new abbreviations |

---

## 4. Surrogate Key Strategy

### 4.1 Key Design

| Layer | Key Type | Generation Method |
|---|---|---|
| Silver ODS | Natural/source key preserved; no surrogate generated | — |
| Silver Analytical | `SHA2-256` hash surrogate key | Hash of the entity's defined business key set (see entity-level key manifest) |

### 4.2 Surrogate Key Generation Standard

```sql
-- Standard surrogate key pattern (Silver Analytical)
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

### 4.3 Cross-Layer Key Traceability

Every Silver Analytical entity must support the following traceability chain:

```
Gold Record → silver_analytical.<domain>.<entity>_key
           → silver_analytical.<domain>.<entity>.source_<entity>_id
           → bronze.<source_system>.<table>.<natural_key_column>
```

---

## 5. SCD (Slowly Changing Dimension) Strategy

### 5.1 SCD Type Mandate per Entity Class

| Entity Class | Required SCD Type | Rationale |
|---|---|---|
| Party / Customer | Type 2 | Regulatory: KYC status history, address history, risk rating history |
| Account | Type 2 | Regulatory: Account status changes, limit changes, terms modifications |
| Loan / Financing Agreement | Type 2 | IFRS 9: Stage transitions (Stage 1 → 2 → 3), ECL history |
| Credit Card Account | Type 2 | Regulatory: credit limit changes, status changes |
| Credit Exposure / Facility | Type 2 | Basel III: Exposure history, collateral re-valuation |
| Collateral | Type 2 | Valuation history for risk and regulatory reporting |
| GL Posting | Type 0 (insert-only fact) | GL postings are immutable events |
| Transaction | Type 0 (insert-only fact) | Financial transactions are immutable events |
| Reference / Code Tables | Type 1 (ODS) or Type 2 (if history needed) | Determined per reference entity based on regulatory need |
| ODS tables | Type 1 | Current-state only by design |

### 5.2 SCD Type 2 Column Standard

Every SCD Type 2 entity **must** include exactly these three columns with these exact names and types:

| Column | Data Type | Constraints | Semantics |
|---|---|---|---|
| `effective_from_date` | `DATE` | NOT NULL | Business effective start date of this version (not the system load date) |
| `effective_to_date` | `DATE` | NOT NULL; `9999-12-31` for the current version | Business effective end date of this version (inclusive); `9999-12-31` sentinel for open-ended current version |
| `is_current` | `BOOLEAN` | NOT NULL | `true` if this is the current (latest) version; `false` for historical versions |

> **Fail condition:** Use of `NULL` for `effective_to_date` of the current record is prohibited. The `9999-12-31` sentinel must be used to ensure that range-scan queries (`WHERE effective_to_date >= reporting_date`) work without null-handling.

### 5.3 System Load Time vs. Business Effective Time

| Column | Semantics | Required |
|---|---|---|
| `effective_from_date` | **Business** effective date — when the change was effective in the real world | Mandatory for Type 2 |
| `record_created_ts` | **System** timestamp — when this row was inserted into Silver | Mandatory for all entities |
| `record_updated_ts` | **System** timestamp — when this row was last updated in Silver (Type 1 only) | Mandatory for Type 1 |

Never conflate business effective time with system load time. The two are distinct columns with distinct semantics.

### 5.4 High-Velocity Attribute Policy

Attributes that change frequently (e.g., daily balance, intraday position) must not be modelled as SCD2 attributes of a parent entity. They must be modelled as separate fact/snapshot tables to avoid row explosion. Examples:

| Volatile Concept | Correct Modelling Pattern |
|---|---|
| Daily account balance | Separate `account_daily_balance_snapshot` fact table (one row per account per day) |
| Intraday FX rates | `reference.fx_rate_snapshot` table partitioned by date |
| Collateral market value | `collateral_valuation_history` fact table |

---

## 6. Mandatory Audit Columns

Every Silver table (ODS and Analytical) **must** include the following columns, populated by the pipeline:

| Column | Data Type | Nullable | Description |
|---|---|---|---|
| `source_system_code` | `STRING` | NOT NULL | Short code of the source system (e.g., `FINACLE`, `FLEXCUBE`, `FIS`) |
| `source_batch_id` | `STRING` | NOT NULL | Batch/pipeline run ID from which this record was loaded |
| `source_ingestion_date` | `DATE` | NOT NULL | Business date of the source extract that produced this row |
| `record_created_ts` | `TIMESTAMP` (UTC) | NOT NULL | System timestamp when this row was first inserted into Silver |
| `record_updated_ts` | `TIMESTAMP` (UTC) | Nullable | System timestamp of last update (only for Type 1 tables; NULL for Type 2 historical rows) |
| `is_deleted` | `BOOLEAN` | NOT NULL | `true` if this record has been soft-deleted (CDC Delete event processed); `false` otherwise |
| `dq_score` | `DECIMAL(5,2)` | Nullable | Optional data quality score (0–100) assigned by the DQ pipeline; NULL if DQ checks not yet run |

---

## 7. Cross-Domain Integrity Standards

### 7.1 Canonical Entity Linkage Pattern

The following cross-domain relationships are **mandatory** and must be navigable from any Silver entity:

```
Party ──── 1:N ──── Account
Account ── 1:N ──── Transaction
Account ── 1:N ──── Contract / Agreement
Contract ─ N:M ──── Collateral  (via bridge: contract_collateral_link)
Party ───── 1:N ──── Exposure / Facility
Exposure ── N:1 ──── GL Account (subledger)
```

Each relationship must be implemented as:
- A documented foreign key constraint (logical, not enforced at engine level in Delta Lake)
- A referential integrity data quality check executed on each Silver refresh
- A lineage edge registered in Unity Catalog

### 7.2 Single Concept Definition Rule

| Principle | Standard |
|---|---|
| One definition per concept | A concept such as "Customer", "Account", "Outstanding Balance" must have exactly one Silver Analytical entity as its authoritative definition |
| No domain-level re-definition | Risk domain, Finance domain, and Retail domain must all reference the same `silver_analytical.customer.party` entity — not create their own customer entity |
| Glossary linkage | Every Silver Analytical entity must be linked to its corresponding entry in the Business Glossary in Unity Catalog / data catalogue |

---

## 8. Banking Domain Coverage Requirements

### 8.1 Foundational Subject Areas (Mandatory – All Must Exist)

| Subject Area | Analytical Silver Entity | ODS Counterpart |
|---|---|---|
| Party / Customer | `customer.party`, `customer.party_role`, `customer.party_relationship` | `silver_ods.<source>.customer_*` |
| Account | `account.account` (typed hierarchy: CASA, Loan, Card, TD, TF) | `silver_ods.<source>.account_*` |
| Product | `product.product`, `product.product_category`, `product.product_type` | `silver_ods.<source>.product_*` |
| Transaction / Event | `transaction.financial_transaction` (parent supertype) | `silver_ods.<source>.transaction_*` |
| Contract / Agreement | `contract.contract`, `contract.contract_party_role` | `silver_ods.<source>.contract_*` |
| General Ledger | `gl.gl_account`, `gl.gl_posting`, `gl.subledger_posting` | `silver_ods.<source>.gl_*` |
| Collateral | `collateral.collateral`, `collateral.collateral_valuation`, `collateral.contract_collateral_link` | `silver_ods.<source>.collateral_*` |
| Reference Data | `reference.currency`, `reference.country`, `reference.branch`, `reference.calendar` | Shared; no ODS counterpart |

### 8.2 Risk & Regulatory Subject Areas (Mandatory for Compliance)

| Subject Area | Analytical Silver Entity | Regulatory Requirement |
|---|---|---|
| Credit Risk | `risk_credit.credit_exposure`, `risk_credit.credit_rating`, `risk_credit.ecl_component` | IFRS 9, Basel III |
| Liquidity Risk | `risk_liquidity.liquidity_buffer`, `risk_liquidity.cash_flow_projection` | Basel III LCR/NSFR |
| Market Risk | `risk_market.position`, `risk_market.market_valuation` | Basel III |
| Operational Risk | `risk_operational.loss_event`, `risk_operational.risk_incident` | Basel III Op Risk |
| AML / Compliance | `compliance.kyc_case`, `compliance.screening_result`, `compliance.sar_flag` | CBUAE, FATF |
| Regulatory Metrics | `regulatory.regulatory_metric`, `regulatory.regulatory_submission` | CBUAE SupTech |

### 8.3 Party Model Design Standard

The Party model must follow the **Party Role** pattern (industry-standard FS-LDM / BIAN approach):

```
PARTY (supertype)
├── PERSON (subtype)
└── ORGANISATION (subtype)

PARTY_ROLE (role assignment entity)
├── role_type: CUSTOMER / GUARANTOR / BENEFICIAL_OWNER / DIRECTOR / SIGNATORY / RM
├── party_key (FK → PARTY)
└── linked_entity_key (FK → ACCOUNT or CONTRACT)
```

- Roles must not be hard-coded as columns in the Party entity (e.g., no `is_guarantor` flag on the party table)
- Relationship types (household grouping, corporate hierarchy) must be modelled in a `PARTY_RELATIONSHIP` entity, not embedded columns

---

## 9. Data Quality at Silver

| Requirement | Standard |
|---|---|
| Null validation | All NOT NULL columns must be validated by the pipeline; records failing null validation are written to a DQ quarantine table, not to the main Silver table |
| Type conformance | All columns must match the declared Delta schema; schema evolution must follow the Bronze schema evolution rules carried forward |
| Referential integrity | All FK relationships documented in Section 7.1 must have a corresponding DQ check executed at each load; violations logged to the DQ metrics table |
| Uniqueness | Primary key (surrogate key + SCD version) uniqueness validated at every load; duplicates block the load and alert the pipeline operations team |
| Reconciliation | Silver row counts reconciled against Bronze source counts per batch; tolerance threshold: 0 discrepancy for CDC; ≤0.001% for batch full-loads |
| Data quality score | `dq_score` column populated for core entities; minimum acceptable score threshold defined per entity in the DQ policy document |

---

## 10. Silver Best Practices Summary

| # | Best Practice | Severity if Violated |
|---|---|---|
| S-BP1 | Silver ODS and Silver Analytical are in physically separate namespaces | Critical |
| S-BP2 | Single authoritative Silver catalog per environment tier | Critical |
| S-BP3 | All core entities have SCD Type 2 applied per the mandate in Section 5 | Critical |
| S-BP4 | `effective_to_date = '9999-12-31'` (not NULL) for current versions | Critical |
| S-BP5 | SHA2-256 surrogate key generated for all Silver Analytical entities | High |
| S-BP6 | Source natural key retained alongside surrogate key | High |
| S-BP7 | All seven mandatory audit columns present and populated | High |
| S-BP8 | No business logic duplicated between Silver and Gold | High |
| S-BP9 | All FK relationships have DQ checks running at each load | High |
| S-BP10 | All Silver Analytical entities linked to Business Glossary | Medium |
| S-BP11 | Volatile attributes (daily balance, FX rates) in separate snapshot tables | Medium |
| S-BP12 | Party model uses Party Role pattern (no hard-coded role flags) | Medium |

---

## 11. Silver Modelling Checklist Reference

See `04_ldm_compliance_checklist.md` → **Section S: Silver Layer Controls** for the full gated compliance check that must be completed before a Silver model change is approved for production.
