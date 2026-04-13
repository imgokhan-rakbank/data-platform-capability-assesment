# RAKBANK Data Platform — Silver Layer & Medallion Architecture
## State Assessment: Summary Deck

**Prepared by:** Data Platform Assessment  
**Date:** April 2026  
**Scope:** Medallion Architecture conformance + Silver Layer data modelling maturity  
**Evidence base:** `architecture_patterns.txt`, `logical_silver.xlsx`, `pyhsical_silver.csv` (`silver_dev_v2`, 148 tables), `pyhsical_silver_2.csv` (`silver_dev`, 408 tables)

---

## Executive Summary

| Item | Finding |
|------|---------|
| Medallion Architecture Maturity | **1.0 / 5 — Initial** |
| Silver Layer Maturity | **1.4 / 5 — Initial/Developing** |
| Critical Finding | **Two parallel Silver catalogs** (`silver_dev` + `silver_dev_v2`) with no single source of truth |
| Most Severe Gap | No enterprise canonical model — GL, Trade, Channels, Collateral only in v1 (no SCD2); Customer/Account/Loan only properly historised in v2 (incomplete) |
| Regulatory Risk | Risk datasets critically incomplete; PII stored in clear text; no data classification |

**The Silver layer is not yet an enterprise canonical data model. It is a collection of use-case ingestions across two parallel, incompatible catalogs.**

---

## 1. Medallion Architecture State

### Two Silver Catalogs — Critical Finding

```
CURRENT STATE (Problem):

  Bronze ──┬──► silver_dev       (408 tables, 18 schemas, 0 SCD2) ──┐
            └──► silver_dev_v2   (120 tables, 16 schemas, 11 SCD2) ──┴──► Gold
                                                                          (reads from both?)
```

```
TARGET STATE (Architecture ADR):

  Bronze ──► silver_ods.*        (source-system replicas, SCD1/2 per entity)
           ──► silver_analytical.* (domain model, SCD2, canonical)
                                                     └──► Gold
```

### Catalog Comparison

| Attribute | `silver_dev` | `silver_dev_v2` |
|-----------|-------------|-----------------|
| Tables (non-system) | **408** | **120** |
| SCD2 tables | **0** | 11 |
| GL coverage | ✅ 7 tables | ❌ |
| Trade / Treasury | ✅ 26 tables | ❌ |
| Electronic Channels | ✅ 20 tables | ❌ |
| Collateral (detailed) | ✅ 21 tables | ❌ |
| Application / Origination | ✅ 44 tables | ❌ |
| Risk Scoring | ✅ 39 tables | ❌ |
| Investments | ✅ 27 tables | ❌ |
| Source-system ODS schemas | ❌ | ✅ (finacle, flexcube, fis, etc.) |
| Data type defects | **249** | ~35 |
| Temp/test/backup tables | ~26 polluting catalog | 0 |
| Architecture pattern | Domain-analytical (no ODS) | ODS sublayer + partial analytical |

### Medallion Architecture Scores

| Sub-Area | Score | Headline |
|----------|-------|---------|
| Architecture Conformance | 1 / 5 | ADR exists; two catalogs run in parallel; no data contracts; no enforcement |
| Layer Responsibility Definition | 1 / 5 | Two Silver environments; ODS/Analytical co-located; no single source of truth |
| Cross-Layer Design Principles | 1 / 5 | No surrogate key strategy; SCD2 on 3% overall; inconsistent audit columns |
| **Overall** | **1.0 / 5** | |

---

## 2. Business Coverage & Domain Completeness

### Domain Coverage Matrix

| Banking Domain | `silver_dev` | `silver_dev_v2` | Either Catalog | Assessment |
|----------------|-------------|-----------------|----------------|------------|
| Customer / Party (Retail/Corporate/SME) | ✅ 39 tables | ✅ 10 tables | ✅ Present | Fragmented — no unified party hierarchy |
| Accounts (CASA, Loans, Cards, TD) | ✅ Present | ✅ Present | ✅ Present | No unified Account parent model; discriminator pattern absent |
| **Transactions (financial)** | ✅ casa_banking, electronic_channel | ✅ finacle/flexcube txns | ✅ Present | No unified transaction model; 5+ system-specific tables |
| **Transactions (non-financial)** | ✅ non_electronic_channel | ❌ | ⚠️ v1 only | Not in v2; at risk on decommission |
| Finance & GL | ✅ gl schema (7 tables) | ❌ | ⚠️ v1 only | Critical gap in v2; GL entirely absent from newer catalog |
| Trade & Treasury | ✅ trade schema (26 tables) | ❌ | ⚠️ v1 only | Not in v2; FX, bonds, money market only in legacy |
| Risk — Credit | ✅ Partial (risk scoring) | ✅ Partial (NPL, LGD/LTV) | ⚠️ Partial | PD absent; IFRS 9 Stage 1/2/3 not modelled |
| Risk — Liquidity (LCR/NSFR) | ❌ | ❌ | ❌ **Absent** | Critical regulatory gap |
| Risk — Market | ❌ | ❌ | ❌ **Absent** | VaR, sensitivities not modelled |
| Risk — Operational | ❌ | ❌ | ❌ **Absent** | OpRisk events not modelled |
| Compliance (KYC/AML) | ❌ | ❌ | ❌ **Absent** | AML transaction monitoring absent |
| Channels | ✅ electronic + non-electronic | ❌ | ⚠️ v1 only | Not in v2 |
| Collateral | ✅ 21-table collateral schema | ❌ (LGD/LTV only) | ⚠️ v1 only | Detailed collateral only in v1 |
| Application / Origination | ✅ application schema (44 tables) | ❌ | ⚠️ v1 only | Not in v2 |
| GL ↔ Product mapping | ✅ gl.product_gl_mapping | ❌ | ⚠️ v1 only | Not migrated |
| Investments | ✅ investments schema (27 tables) | ❌ | ⚠️ v1 only | Not in v2 |
| WBG / BBG | ✅ wbg_bbg schema | ❌ | ⚠️ v1 only | Not in v2 |
| Reference Data | ⚠️ Spread across all schemas | ✅ ref_data schema | Inconsistent | No centralised reference data catalog |

**Key finding**: Neither catalog alone covers all banking domains. Critical domains (GL, Trade, Channels, Collateral) exist only in the older `silver_dev` which has zero SCD2 and 249 data type defects.

---

## 3. Customer, Party & Relationship Modelling (Golden Core)

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Single Customer / Party master (Finacle as golden source) | ⚠️ Partial | `customer.customer` exists in both catalogs; no explicit golden-source designation; `customer_master` table coexists in v1 |
| Individual vs Corporate structure | ✅ Fields present | `Customer_Type`, individual/corporate fields in `customer.customer` |
| Group / Beneficial ownership | ✅ Partial | `customer.customer_groupings` in v1; `customer_relationships` has related parties |
| Related parties (directors, guarantors, nominees) | ✅ Partial | `customer.customer_relationships` in v1 (36 cols); role taxonomy not explicit |
| Customer ↔ Account relationship | ⚠️ Partial | `account.account_agreement_master.account_customer_id` references customer; no explicit junction table with relationship type |
| Customer ↔ Product relationship | ❌ Absent | Product master entirely absent from both catalogs; no Customer-Product junction |
| Party ↔ Party hierarchy | ⚠️ Minimal | `customer.customer_groupings` only; no recursive hierarchy (parent_customer_id) |
| 360° Customer View support | ❌ Not achievable | Fields scattered across 39 tables in v1; no integration with CLM/CRM boundaries; duplicated tables (`customer_master`, `customer_demographics_loan_copy`, `_bkp`, `_test`) |
| CDH / CRM / CLM boundary alignment | ❌ Not documented | No published boundary definition |

---

## 4. Product & Transaction Granularity

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Product-level data | ❌ Critical gap | **Product model entirely absent** from both catalogs. Only reference lookup tables (`reference_product` in trade schema). No product hierarchy (Product → Account → Contract → Transaction). |
| Account-level data | ✅ Present | CASA, loan, credit card, deposit accounts exist in both catalogs (though fragmented) |
| Contract-level data | ✅ Partial | `loan.loan_account_master`, `account.account_agreement_master`, `deposits.deposit_account` |
| Transaction/event-level data | ⚠️ Partial | Multiple system-specific transaction tables; no unified transaction entity |
| No aggregation in Silver (Gold only) | ❌ Violated | `loan.loan_account_master` in v2 contains `MTD_disbursement_amount`, `YTD_disbursement_amount`, `LTD_disbursement_amount` — aggregates embedded in a Silver contract table |
| Full transaction lineage | ❌ Absent | No transaction lineage from source event → Silver → Gold documented or implemented |
| Atomic, consistent, reusable Silver | ❌ Not achieved | 408 + 120 tables across two catalogs, multiple tables per entity, no canonical atomic model |

---

## 5. Slowly Changing Dimensions (SCD) Strategy

| Requirement | Status | Evidence |
|-------------|--------|---------|
| SCD strategy defined per entity type | ❌ Absent | No formal SCD register. No documentation of which entities use Type 1 vs Type 2 |
| SCD Type 2 implemented on customer profile | ✅ `silver_dev_v2` only | `customer.customer` in v2 has `effective_from`, `effective_to`, `is_current` |
| SCD Type 2 on account status | ✅ `silver_dev_v2` only | `account.account_agreement_master` in v2 has SCD2 fields |
| SCD coverage overall | ❌ Critical gap | **0 of 408 tables** in `silver_dev` have SCD2. **11 of 120** in `silver_dev_v2` (~9%). **Combined: 11 of ~500 unique entities (~2%)** |
| Mandatory SCD fields: `effective_start_date` | ✅ On SCD2 tables | Named `effective_from` on 11 v2 tables |
| Mandatory SCD fields: `effective_end_date` | ✅ On SCD2 tables | Named `effective_to` on 11 v2 tables |
| Mandatory SCD fields: `active_flag` | ✅ On SCD2 tables | Named `is_current` on 11 v2 tables |
| Mandatory SCD fields: `version_number` | ❌ Absent | No `version_number` column on any table in either catalog |
| Reference data → Type 1 | ⚠️ Implicit | Reference tables use Type 1 by default (no SCD2) but not formally declared |
| Full historical traceability | ❌ Not achievable | 98% of tables are SCD Type 1 (overwrite). Past states are permanently lost for all entities in `silver_dev` |

---

## 6. Historical Data & Time-Variant Storage

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Month-end / Day-end snapshot capability | ⚠️ Partial | `account.account_eod_balance` (v2 only); no month-end snapshot tables for other entities |
| Event-based storage (transaction timeline) | ⚠️ Partial | `loan.loan_transaction` (v2, SCD2); `casa_banking.casa_transaction_statement_data` (v1, SCD1); no uniform event timestamping |
| Risk model support (PD/LGD trends) | ❌ Critical gap | `customer_riskrating_scoring` exists in v1 but SCD1 only — no time series. LGD history in v2 (`loan.loss_given_default_detail`) but limited to loan domain |
| Regulatory reporting (point-in-time reconstruction) | ❌ Not achievable | Only 11 SCD2 tables in v2; 0 in v1. Cannot reconstruct bank state at arbitrary past date for regulatory purposes |
| Backdated corrections | ❌ Not handled | No correction/restatement pattern documented or implemented in either catalog |

---

## 7. Logical vs Physical Model Separation

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Logical model exists (business-driven, system-independent) | ✅ Partial | `logical_silver.xlsx` — 14 subject areas, 400+ entities |
| Logical model is versioned and governed | ❌ Absent | Stored as an Excel file; no version control; no formal governance |
| Physical model maps 1:1 to logical | ❌ Critical gap | Multiple physical tables per logical entity (e.g., 5+ loan contract tables across both catalogs); Product subject area has 0 physical entities despite logical model coverage |
| Traceability: Logical → Physical → Pipelines | ❌ Absent | `metadata.table_lineage` in v1 suggests partial lineage tracking; no published logical-to-physical mapping |
| Physical model optimised for Delta Lake | ❌ Absent | No partitioning on any table; no Z-ORDER or Liquid Clustering documented |

---

## 8. Metadata, Cataloging & Version Control

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Data dictionary (business + technical definitions) | ❌ Absent | `comment` column exists in both physical schema exports but is null/empty for ~95% of columns |
| Data lineage (source → Bronze → Silver → Gold) | ⚠️ Partial | `metadata.table_lineage` in v1; no Gold-to-Silver or Silver-to-Bronze lineage documented |
| Schema versioning | ❌ Absent | No schema changelog; `silver_dev_v2` naming suggests manual versioning by catalog rename |
| Central catalog alignment (Unity Catalog / Collibra / iServer) | ⚠️ Unity Catalog only | Tables exist in Unity Catalog but business metadata (owners, definitions, sensitivity tags) not populated |
| No "implicit knowledge" principle | ❌ Violated | Extensive tribal knowledge required — which catalog is authoritative, which table to use for a given entity, what `_bkp` and `_test` tables are |

---

## 9. Data Quality & Validation Framework

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Validation at ingestion (Bronze → Silver) | ⚠️ Partial | `control.dq_logs` in v2; `audit.silver_table_ddls` in v1; coverage unknown |
| Mandatory field checks (not-null) | ❌ Not systematic | No evidence of systematic not-null assertion framework |
| Referential integrity checks | ❌ Absent | No FK constraints in Delta Lake; no RI validation in pipeline |
| Range checks | ❌ Absent | Not evidenced |
| Data type correctness | ❌ Critical defects | **249 type defects in `silver_dev`**: status fields typed as TIMESTAMP, amount fields as STRING/BIGINT. ~35 defects in `silver_dev_v2`. |
| DQ KPIs (completeness, accuracy, timeliness) | ❌ Not published | No DQ dashboard or published SLAs |

### Top Data Type Defects (sample from `silver_dev`)

| Table | Column | Current Type | Should Be |
|-------|--------|-------------|-----------|
| account.account_agreement_master | account_status | TIMESTAMP | STRING |
| account.account_agreement_master | kyc_status | TIMESTAMP | STRING |
| account.ccod_account_master_table | Account_Status_Date | TIMESTAMP | DATE |
| application.application | Application_status_date | TIMESTAMP | DATE |
| account.account_balance_summary_dd | Account_Currency_Balance_Summary_Amount | STRING | DECIMAL(28,8) |
| account.account_group_risk_hist | Account_Group_Exposure_at_Dflt_Amount | STRING | DECIMAL(28,8) |
| account.agreement | Balance_Sheet_Code | STRING | DECIMAL |

---

## 10. Security, Privacy & Compliance

| Requirement | Status | Evidence |
|-------------|--------|---------|
| PII classification (customer names, IDs, addresses) | ❌ Critical gap | Full names, Passport_Number, EID_Number, Date_Of_Birth, email, phone stored in clear text in both catalogs |
| Column masking / tokenisation | ❌ Absent | No masking rules applied in Unity Catalog |
| Row-level security | ❌ Absent | No RLS policies documented or implemented |
| Unity Catalog sensitivity tags | ❌ Absent | `comment` column unused; no sensitivity classification applied |
| Audit trails | ⚠️ Partial | `Create_Date/Update_Date` on ~24 tables in v2; no immutable audit log per row |

### PII Exposure Sample (both catalogs)

| Schema.Table | Exposed PII Columns |
|-------------|---------------------|
| customer.customer | Customer_Full_Name, First_Name, Last_Name, Short_Name |
| customer.customer_documents | Passport_Number, EID_Number |
| customer.customer_contactinformation | Mail_Address1-4, Registered_Address1-5, Email_Address, Phone |
| customer.customer_demographics | Place_of_birth, Country_of_Birth, Date_Of_Birth |
| customer.customer_relationships | Full_Name, Passport_No_Trade_License, Date_of_Birth |

---

## 11. Governance & Change Lifecycle

| Requirement | Status | Evidence |
|-------------|--------|---------|
| Change request process (new fields, schema changes) | ❌ Absent | No formal CR process documented; new tables appear in both catalogs without governance (`_dummy`, `_test`, `_bkp` tables evident) |
| Impact assessment before schema changes | ❌ Absent | No downstream impact analysis process |
| Approval structure (Data Platform + EA + Business + Risk) | ❌ Not enforced | Owners exist but governance process is informal |
| Deprecation / removal process | ❌ Absent | Legacy tables (`_bkp`, `_old`, `_dummy`) accumulate; never removed |

---

## 12. Recommended Priority Actions

### Immediate (0–3 months)

| Priority | Action | Owner |
|----------|--------|-------|
| 🔴 P1 | **Declare one authoritative Silver catalog** — publish ADR choosing `silver_dev_v2` as future state and `silver_dev` as deprecated | Data Platform Arch |
| 🔴 P1 | **Map all Gold model dependencies** — audit which Gold models read from `silver_dev` vs `silver_dev_v2` before any decommission | Data Platform + Analytics |
| 🔴 P1 | **Fix critical data type defects** — prioritise `account_status` as TIMESTAMP, `balance` as BIGINT, financial amounts as STRING | Data Engineering |
| 🔴 P1 | **Classify and mask PII columns** — apply Unity Catalog sensitivity tags to all customer PII columns in both catalogs | Data Governance |
| 🔴 P1 | **Stop adding new entities to `silver_dev`** — freeze development on v1; all new entities go to v2 only | Data Engineering |

### Short-term (3–6 months)

| Priority | Action | Owner |
|----------|--------|-------|
| 🟠 P2 | **Migrate GL, Trade, Channels, Collateral to `silver_dev_v2`** with SCD2 and correct data types | Data Engineering |
| 🟠 P2 | **Implement SCD2 register** — formally define which entities use Type 1 vs Type 2; add `version_number` column | Data Architecture |
| 🟠 P2 | **Remove aggregations from Silver** — move MTD/YTD/LTD fields from `loan.loan_account_master` to Gold | Data Engineering |
| 🟠 P2 | **Design unified Transaction model** — single parent `silver_analytical.transaction.financial_transaction` with source discriminator | Data Architecture |
| 🟠 P2 | **Design unified Account / Product model** — account type discriminator hierarchy; minimum product reference tables | Data Architecture |

### Medium-term (6–12 months)

| Priority | Action | Owner |
|----------|--------|-------|
| 🟡 P3 | **Implement risk datasets** — LCR, NSFR, IFRS 9 Stage classification, ECL inputs | Risk + Data Engineering |
| 🟡 P3 | **Enforce namespace architecture** — `silver_ods.*` and `silver_analytical.*` as separate catalogs per ADR | Data Platform Arch |
| 🟡 P3 | **Deploy dbt or equivalent** — replace PySpark notebooks with schema-as-code transformation framework | Data Engineering |
| 🟡 P3 | **Publish logical-to-physical traceability** — for every Silver entity, map logical model entity → physical table(s) | Data Architecture |
| 🟡 P3 | **Data quality SLAs** — publish completeness, accuracy, timeliness KPIs per domain; build DQ dashboard | Data Governance |

---

## 13. Overall Maturity Scorecard

| Domain | Score | RAG |
|--------|-------|-----|
| Medallion Architecture Governance | 1.0 / 5 | 🔴 |
| Business Coverage & Domain Completeness | 1.5 / 5 | 🔴 |
| Customer / Party / Relationship (Golden Core) | 2.0 / 5 | 🟠 |
| Product & Transaction Granularity | 1.0 / 5 | 🔴 |
| SCD Strategy | 1.0 / 5 | 🔴 |
| Historical Data & Time-Variant Storage | 1.5 / 5 | 🔴 |
| Logical vs Physical Model Separation | 1.5 / 5 | 🔴 |
| Metadata, Cataloging & Version Control | 1.0 / 5 | 🔴 |
| Data Quality & Validation | 1.5 / 5 | 🔴 |
| Security, Privacy & Compliance | 1.0 / 5 | 🔴 |
| Governance & Change Lifecycle | 1.0 / 5 | 🔴 |
| Performance & Scalability | 1.0 / 5 | 🔴 |
| **Overall Silver Layer** | **1.3 / 5** | 🔴 |

**Maturity Level: Initial (Level 1) — approaching Developing in Customer domain only**

> The Silver layer cannot yet function as an enterprise canonical data model. The platform is technically functional for specific use cases but structurally fragmented, historically incomplete, and non-compliant with governance, risk, and regulatory requirements.
