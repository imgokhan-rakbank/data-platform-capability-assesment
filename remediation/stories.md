# RAKBANK Data Platform Recovery — Jira Task Hierarchy (60 Days)

---

## Target Silver Layer — Subject Area Reference

### Core Banking Subject Areas

| Subject Area | Silver Entities |
|---|---|
| Party / Customer | `silver.customer.party`, `silver.customer.party_role`, `silver.customer.party_relationship` |
| Account | `silver.account.account` (typed hierarchy: CASA, Loan, Card, TD, TF) |
| Product | `silver.product.product`, `silver.product.product_category`, `silver.product.product_type` |
| Transaction / Event | `silver.transaction.financial_transaction` (parent supertype) |
| Contract / Agreement | `silver.contract.contract`, `silver.contract.contract_party_role` |
| General Ledger | `silver.gl.gl_account`, `silver.gl.gl_posting`, `silver.gl.subledger_posting` |
| Collateral | `silver.collateral.collateral`, `silver.collateral.collateral_valuation`, `silver.collateral.contract_collateral_link` |
| Reference Data | `silver.reference.currency`, `silver.reference.country`, `silver.reference.branch`, `silver.reference.calendar` |

### Risk & Regulatory Subject Areas (Mandatory for Compliance)

| Subject Area | Silver Entities | Regulatory Requirement |
|---|---|---|
| Credit Risk | `silver.risk_credit.credit_exposure`, `silver.risk_credit.credit_rating`, `silver.risk_credit.ecl_component` | IFRS 9, Basel III |
| Liquidity Risk | `silver.risk_liquidity.liquidity_buffer`, `silver.risk_liquidity.cash_flow_projection` | Basel III LCR / NSFR |
| Market Risk | `silver.risk_market.position`, `silver.risk_market.market_valuation` | Basel III |
| Operational Risk | `silver.risk_operational.loss_event`, `silver.risk_operational.risk_incident` | Basel III Op Risk |
| AML / Compliance | `silver.compliance.kyc_case`, `silver.compliance.screening_result`, `silver.compliance.sar_flag` | CBUAE, FATF |
| Regulatory Metrics | `silver.regulatory.regulatory_metric`, `silver.regulatory.regulatory_submission` | CBUAE SupTech |

### Additional Banking Subject Areas

| Subject Area | Silver Entities | Notes |
|---|---|---|
| Cards | `silver.card.card_account`, `silver.card.card_transaction`, `silver.card.card_limit`, `silver.card.card_authorization` | Credit & Debit card lifecycle |
| Trade Finance | `silver.trade.letter_of_credit`, `silver.trade.bank_guarantee`, `silver.trade.trade_transaction`, `silver.trade.bill_collection` | LC, BG, documentary collections |
| Treasury | `silver.treasury.deal`, `silver.treasury.fx_position`, `silver.treasury.money_market_placement`, `silver.treasury.securities_holding` | FX, MM, fixed income |
| Payments | `silver.payment.payment_instruction`, `silver.payment.payment_clearing`, `silver.payment.nostro_reconciliation` | SWIFT, IPI, UAEFTS, WPS |
| Channels / Digital | `silver.channel.digital_session`, `silver.channel.channel_event`, `silver.channel.authentication_event` | Mobile, Internet Banking, ATM |
| Limits & Facilities | `silver.limit.facility`, `silver.limit.facility_utilization`, `silver.limit.limit_breach` | Credit limits and utilization |
| Fees & Charges | `silver.fee.fee_schedule`, `silver.fee.fee_transaction`, `silver.fee.waiver` | Product-level fee structures |
| Insurance | `silver.insurance.bancassurance_policy`, `silver.insurance.claim` | Bancassurance products |

---

## EPIC 1: Silver Layer Discovery & Assessment

**Timeline:** Days 0–15  
**Objective:** Establish a factual baseline across logical, physical, and data quality layers  
**Output:** Documented gap assessment across Silver, Logical, Physical, and Data Quality; initial salvage vs. rebuild view

---

### Story 1.1 — Target Subject Area Scope Definition

**Description:** Define and agree the authoritative list of subject areas, Silver schemas, and entities that RAKBANK's data platform must cover. This becomes the benchmark against which all subsequent assessments and builds are measured.

**Owner:** Enterprise Data Architect / Data Domain Lead  
**Estimate:** 3 days

**Tasks:**
1. Review the target subject area reference tables (Core Banking, Risk & Regulatory, Additional Banking) in this document with domain SMEs
2. Conduct workshops with stakeholders from Risk, Finance, CRM, Cards, Trade Finance, Treasury, Payments, Compliance, and Digital to confirm scope
3. Agree on any additions or exclusions and document rationale
4. Produce the **Agreed Subject Area Scope Register** — the single source of truth for what must exist in the Silver layer
5. Assign a domain SME owner to each subject area
6. Confirm priority tier for each subject area:
   - **Tier 1 (Must have — Phase 1):** Party, Account, Transaction, Credit Risk, GL
   - **Tier 2 (Must have — Phase 2):** Cards, Trade Finance, Treasury, Payments, Collateral, AML/Compliance, Regulatory
   - **Tier 3 (Should have — Phase 2+):** Channels, Limits, Fees, Insurance, Market Risk, Liquidity Risk, Op Risk
7. Baseline the register against the current Silver catalog to produce an initial coverage gap count

**Deliverables:**
- Agreed Subject Area Scope Register (subject area × tier × SME owner × Silver schema)
- Coverage gap count (subject areas missing vs. partially present vs. present)

**Acceptance Criteria / KPI:**
- Scope register signed off by Architecture Review Board and business stakeholders
- Every subject area has a named SME owner
- Tier classification agreed

**Test / Validation:**
- Sign-off checklist completed by each domain SME

---

### Story 1.2 — Modeling Standards & Guidelines Definition

**Description:** Define the modeling standards, naming conventions, design patterns, and quality rules that all Silver layer entities must conform to. These standards become the evaluation framework for the assessment stories that follow.

**Owner:** Data Modeling Lead / Platform Standards Lead  
**Estimate:** 3 days

**Tasks:**
1. Define **Logical Modeling Standards:**
   - Entity and attribute naming conventions (`snake_case`, `_id` suffix for business keys, `_code` for reference codes)
   - Mandatory metadata attributes on every entity (`_source_system`, `_load_timestamp`, `_record_hash`)
   - Primary key and business key definitions
   - Relationship and foreign key standards
   - Supertype / subtype pattern guidance (e.g., Account hierarchy, Transaction supertype)
2. Define **Physical / Databricks Standards:**
   - Delta table DDL template (managed tables in Unity Catalog)
   - SCD Type 2 column set (`_valid_from`, `_valid_to`, `_is_current`, `_record_key`)
   - Partitioning rules: date-partitioned for transactional, none for small references
   - Z-ordering / liquid clustering guidance
   - OPTIMIZE / VACUUM / retention policy standards
   - Folder and catalog naming: `catalog=silver`, `schema=<subject_area>`, `table=<entity>`
3. Define **Data Quality Standards:**
   - Mandatory DQ checks per entity type (completeness, PK uniqueness, referential integrity, reconciliation)
   - DQ severity thresholds (Critical / Major / Minor) and escalation path
   - Accepted tolerance levels for reconciliation (e.g., ≤ 0.01 % variance)
4. Define **Ingestion Standards:**
   - CDC pattern requirements (schema registry, idempotency, dead-letter queue)
   - Batch pattern requirements (watermark, full-load vs. incremental)
   - Streaming pattern requirements (Confluent Kafka, exactly-once semantics)
5. Publish standards as the **Silver Layer Modeling & Platform Standards Guide**
6. Review and approve with Architecture Review Board

**Deliverables:**
- Silver Layer Modeling & Platform Standards Guide (logical, physical, DQ, ingestion)
- DDL templates and SCD Type 2 column reference
- DQ rule templates per entity category

**Dependencies:**
- Story 1.1 (Agreed Subject Area Scope) must be complete

**Acceptance Criteria / KPI:**
- Standards guide approved by Architecture Review Board
- DDL templates validated against one reference table
- Standards cover all subject areas in the scope register


**Test / Validation:**
- Apply standards to the Party reference entity and confirm compliance

---

### Story 1.3 — Bronze Ingestion & Production State Assessment

**Description:** Assess the current state of Bronze data in production and the ingestion pipelines that populate it. Identify gaps in ingestion (missing history, lack of CDC, high-latency feeds, schema drift handling), and determine whether Bronze contains the required history and metadata to support reliable Silver builds.

**Owner:** Ingestion Platform Lead / Bronze Data Engineer  
**Estimate:** 4 days

**Dependencies:**
- Story 1.1 (Agreed Subject Area Scope Register) — to know which sources feed which subject areas
- Story 1.2 (Modeling Standards & Guidelines) — to check required metadata and history handling
- Access to Bronze catalog, ingestion logs, and streaming topic metrics

**Tasks:**
1. Inventory Bronze datasets, their source systems, ingestion cadence (batch/stream), and retention
2. For each Bronze dataset, record:
   - Ingestion method (CDC, full-load, API, file)
   - Whether change history is captured (change data feed, incremental keys, event timestamps)
   - Watermark / latency behavior (time-to-arrival, late-arrival handling)
   - Schema evolution handling (schema registry usage, automatic schema inference)
   - Error handling (dead-letter queues, retries, backfill capabilities)
3. Compare Bronze schema fields to the Source-to-Logical Mapping Register (Story 2.2) where available and note missing fields required for logical model mapping
4. Identify ingestion gaps that would prevent correct Silver building:
   - Missing CDC / history for entities that require SCD Type 2
   - No reliable business keys or record-level hashes
   - Lack of schema registry integration causing incompatible schema changes
   - No backfill / replay support for historical rebuilds
5. Produce an ingestion gap register with remediation recommendations (enable CDC, add timestamps, introduce schema registry, add backfill capability)
6. Prioritize ingestion fixes by domain tier (Tier 1 first)

**Deliverables:**
- Bronze inventory (dataset, source, cadence, retention, ingestion method)
- Ingestion gap register mapped to subject areas and required remediation
- Recommended quick wins (enable CDC on X feeds, add `_event_time` and `_record_hash` to Y topics)

**Acceptance Criteria / KPI:**
- Bronze inventory and ingestion gap register completed and reviewed
- Critical ingestion gaps (Tier 1) documented with remediation plan

**Test / Validation:**
- For a sample of Bronze feeds, demonstrate a backfill and successful replay into a staging Silver table
- Confirm presence of `_event_time` and `_record_hash` in at least 90 % of Tier 1 Bronze datasets

---

### Story 1.4 — Bronze → Silver Mapping Catalog Assessment

**Description:** Catalog and assess the existing mappings and transformation artifacts that move data from Bronze to Silver. Determine completeness, correctness, and traceability of mappings, and identify mapping gaps or undocumented transformations that may cause data quality or lineage issues.

**Owner:** Data Engineering Lead / Domain Engineers  
**Estimate:** 3 days

**Dependencies:**
- Story 1.1 (Agreed Subject Area Scope Register) — to know expected target entities
- Story 1.2 (Modeling Standards & Guidelines) — to validate mapping conformity
- Access to transformation notebooks, DLT pipelines, ingestion jobs, and pipeline code repositories

**Tasks:**
1. Inventory all existing Bronze→Silver transformation artifacts (notebooks, Jobs, DLT pipelines, SQL scripts)
2. For each transformation record:
   - Source Bronze dataset(s)
   - Target Silver table and columns
   - Transformation logic (field mappings, lookups, derivations, UDFs)
   - Idempotency/ replay capability
   - Job scheduling and dependencies
   - Test coverage (unit/integration tests) and CI usage
   - Documentation and owner
3. Validate mappings against the Source-to-Logical Mapping Register (Story 2.2) and the Target Silver schema (Story 2.3)
4. Identify gaps:
   - Missing mappings for required canonical attributes
   - Transformations that use embedded business logic (should be refactored into documented functions)
   - No-lineage or insufficient lineage metadata
   - Lack of idempotency / failure-resume capability
5. Create a Mapping Catalog with status (Complete / Partial / Missing), owner, and remediation recommendation
6. Prioritize mapping fixes for Tier 1 subject areas

**Deliverables:**
- Bronze→Silver Mapping Catalog (spreadsheet or wiki) with per-mapping status and owners
- Mapping gaps register with remediation actions and owners
- Recommended quick fixes (add lineage metadata, refactor UDFs, add tests)

**Acceptance Criteria / KPI:**
- Mapping catalog completed for all Tier 1 subject areas
- Owners assigned for all incomplete or missing mappings

**Test / Validation:**
- Run a sample transformation end-to-end for one Tier 1 mapping and verify data lineage and correctness

---

### Story 1.5 — Silver Layer Logical Model Assessment

**Description:** Assess the current Silver layer logical model against the target subject areas and modeling standards defined in Stories 1.1 and 1.2.

**Owner:** Data Modeling Lead / Enterprise Architect  
**Estimate:** 5 days

**Dependencies:**
- Story 1.1 (Agreed Subject Area Scope Register) — defines what must exist
- Story 1.2 (Modeling Standards & Guidelines) — defines how it must be built
- Unity Catalog / Hive metastore access
- Input from business SMEs per domain

**Tasks:**
1. Inventory every existing Silver schema, table, and column across all Databricks catalogs
2. For each item in the Agreed Subject Area Scope Register (Story 1.1), check whether a corresponding Silver entity exists
3. Map existing tables to **Core Banking** subject areas (Party, Account, Product, Transaction, Contract, GL, Collateral, Reference Data)
4. Map existing tables to **Risk & Regulatory** subject areas (Credit Risk, Liquidity, Market, Op Risk, AML, Regulatory Metrics)
5. Map existing tables to **Additional Banking** subject areas (Cards, Trade Finance, Treasury, Payments, Channels, Limits, Fees, Insurance)
6. For each subject area assess against the **Modeling Standards** (Story 1.2):
   - Entity and attribute naming compliance (`snake_case`, `_id` suffix, `_code` for reference codes)
   - Mandatory metadata columns present (`_source_system`, `_load_timestamp`, `_record_hash`)
   - Primary key and business key correctly defined
   - Supertype / subtype pattern applied where required
   - Identify missing entities (e.g., no `silver.trade.*` schema exists)
   - Identify duplicate or overlapping entities
7. Classify every gap as **Salvage**, **Partial refactor**, or **Full rebuild**
8. Produce a standards-compliance score and business-impact ranking (High / Medium / Low) per subject area

**Deliverables:**
- Inventory spreadsheet (schema → table → mapped subject area → standards-compliance score → status)
- Logical gaps register with remediation classification

**Acceptance Criteria / KPI:**
- 100 % of Silver entities reviewed and mapped
- Gap register completed and approved by Data Architecture

**Test / Validation:**
- Cross-reference sample entities with downstream report owners

---

### Story 1.6 — Enterprise Logical Data Model Review

**Description:** Evaluate enterprise data model coverage vs. business needs for Risk, ERP, CRM, SupTech, Cards, Trade Finance, Treasury, and Payments. Coverage is assessed against the Agreed Subject Area Scope Register (Story 1.1).

**Owner:** Enterprise Data Architect  
**Estimate:** 4 days

**Tasks:**
1. Collect canonical data requirements from:
   - **Risk** — Credit exposure, ECL, liquidity buffers, market positions, op-risk events
   - **ERP / GL** — Chart of accounts, postings, sub-ledger reconciliation
   - **CRM / Party** — Party, relationships, KYC
   - **SupTech / Regulatory** — CBUAE metrics, submissions
   - **Cards** — Card lifecycle, authorizations, limits
   - **Trade Finance** — LCs, BGs, documentary collections
   - **Treasury** — FX, money-market, securities
   - **Payments** — SWIFT, UAEFTS, WPS, nostro recon
2. Compare current Silver schema vs. required canonical entities per domain
3. Validate critical business keys and cross-domain relationships (e.g., `party_id` across Customer ↔ Account ↔ Contract ↔ Risk)
4. Capture misalignments between business meaning and stored data

**Deliverables:**
- Domain-by-domain gap analysis matrix
- Prioritized remediation list with business-impact scores

**Acceptance Criteria / KPI:**
- Logical gaps documented per domain
- Business impact ranked (High / Medium / Low)

**Test / Validation:**
- Business domain sign-off on prioritized list

---

### Story 1.7 — Physical Data Model & Databricks Implementation Assessment

**Description:** Review how the Silver layer is physically implemented in Databricks across all subject areas. Each table is assessed against the physical standards defined in Story 1.2.

**Owner:** Data Platform Engineer  
**Estimate:** 4 days

**Tasks:**
1. Inventory all Silver Delta tables — path, format, size, row count, partitioning, clustering
2. Compare physical table structures against logical definitions for each subject area
3. Assess:
   - Partitioning strategy (over-partitioning, skew)
   - Small-file problem and compaction status
   - Delta Lake features in use (Z-ordering, liquid clustering, change data feed)
4. Review SCD handling per entity — Type 1 (overwrite) vs. Type 2 (history) vs. none
5. Identify tables violating platform naming, folder, or retention standards
6. Document anti-patterns (e.g., wide denormalized tables in Silver, JSON blobs, missing PKs)

**Deliverables:**
- Physical model inventory spreadsheet
- Anti-patterns & remediation playbook

**Dependencies:**
- Story 1.2 (Modeling Standards & Guidelines) — physical standards checklist
- Databricks workspace admin access
- Platform standards runbook

**Acceptance Criteria / KPI:**
- Physical assessment completed for all Silver tables
- Anti-patterns list with remediation priority

**Test / Validation:**
- Run `OPTIMIZE` / `VACUUM` on sample tables and measure read-performance improvement

---

### Story 1.8 — Data Quality Baseline Assessment

**Description:** Establish baseline data quality state for the Silver layer across priority subject areas using the DQ standards and severity thresholds defined in Story 1.2.

**Owner:** Data Quality Lead  
**Estimate:** 5 days

**Tasks:**
1. Identify Critical Data Elements (CDEs) per subject area — priority order:
   - Party (`party_id`, `party_name`, `kyc_status`)
   - Account (`account_id`, `account_type`, `balance`)
   - Transaction (`txn_id`, `amount`, `value_date`, `currency`)
   - Credit Risk (`exposure_id`, `rating`, `ecl_stage`)
   - GL (`gl_account_id`, `posting_amount`, `posting_date`)
2. Profile CDEs: nulls, duplicates, range violations, referential integrity
3. Perform source → Silver reconciliation (row counts, key aggregates) for priority domains
4. Capture known business data issues from stakeholders
5. Define DQ severity scale (Critical / Major / Minor) and initial thresholds

**Deliverables:**
- CDE catalog with profiling results
- Top 20 critical data issues with severity and owner
- Initial DQ severity classification

**Dependencies:**
- Story 1.2 (Modeling Standards & Guidelines) — DQ severity thresholds and rule templates
- Profiling tooling (Spark SQL, Great Expectations, or equivalent)
- Stakeholder input

**Acceptance Criteria / KPI:**
- DQ baseline metrics captured for all priority CDEs
- Top 20 critical data issues documented

**Test / Validation:**
- Re-run profiling after one corrective action to verify metric improvement

---

### Story 1.9 — Assessment Consolidation & Recommendations

**Description:** Consolidate logical, physical, and DQ findings into a single remediation direction measured against the scope and standards defined in Stories 1.1 and 1.2.

**Owner:** Assessment PM / Lead Architect  
**Estimate:** 3 days

**Dependencies:**
- Stories 1.1 – 1.7 complete

**Tasks:**
1. Merge findings from Stories 1.4–1.7: logical gap register, physical anti-patterns list, and DQ issues
2. Score each subject area against the Agreed Scope Register (Story 1.1) and Standards Guide (Story 1.2)
3. Classify every Silver subject area as:
   - ✅ **Salvage** — compliant with standards; minor fixes, naming, metadata only
   - 🔧 **Partial refactor** — schema changes, SCD fixes, key alignment to standards
   - 🔴 **Full rebuild** — non-existent or fundamentally non-compliant (e.g., Trade Finance, Treasury, Cards)
4. Produce a standards-compliance scorecard (per subject area × standards dimension)
5. Prioritize: Risk & Regulatory domains first, then Core Banking Tier 1, then Tier 2 and Tier 3
6. Produce remediation roadmap with owners and estimated effort
7. Deliver assessment walkthrough to stakeholders

**Deliverables:**
- Consolidated assessment pack (slides + appendices)
- Remediation roadmap (subject area × action × owner × timeline)
- Quick-wins list

**Acceptance Criteria / KPI:**
- Assessment pack signed off by Business and IT
- Clear remediation direction agreed

**Test / Validation:**
- Walkthrough with business and IT; capture approval or action items

---

## EPIC 2: Design & Sign‑off

**Timeline:** Days 16–30  
**Objective:** Lock down target state design before build  
**Output:** Signed-off Logical + Silver Data Model; Approved Physical Design Standards; Data Quality Framework baseline

---

### Story 2.1 — Enterprise Logical Data Model Finalization

**Description:** Define and sign off the canonical enterprise logical data model covering all subject areas.

**Owner:** Enterprise Data Architect  
**Estimate:** 7 days (including workshops)

**Tasks:**
1. Finalize canonical entities and relationships for:
   - **Core Banking:** Party, Account, Product, Transaction, Contract, GL, Collateral, Reference Data
   - **Risk & Regulatory:** Credit Risk, Liquidity, Market, Op Risk, AML/Compliance, Regulatory Metrics
   - **Additional:** Cards, Trade Finance, Treasury, Payments, Channels, Limits, Fees, Insurance
2. Define canonical business definitions (data glossary) per entity
3. Standardize entity and attribute naming conventions (e.g., `snake_case`, `_id` suffix for keys)
4. Validate cross-domain integration points:
   - `party_id` as the universal customer key
   - `account_id` linking Account ↔ Transaction ↔ GL ↔ Contract
   - `contract_id` linking Contract ↔ Collateral ↔ Risk
5. Conduct business and IT review workshops (2–3 sessions)
6. Incorporate feedback and publish final LDM

**Deliverables:**
- Canonical LDM diagrams per subject area
- Entity definitions glossary
- Decisions register and change log

**Acceptance Criteria / KPI:**
- Logical model signed off by Business and IT
- All critical domains approved

**Test / Validation:**
- Selected downstream teams validate mappings via sample reports

---

### Story 2.2 — Source-to-Logical Model Mapping

**Description:** For every subject area in the Agreed Scope Register, produce a documented mapping from each source system field to its canonical logical model entity and attribute. This mapping becomes the authoritative input for Silver layer transformations and pipeline design.

**Owner:** Data Modeling Lead + Domain Engineers (one per subject area)  
**Estimate:** 7 days (parallel per domain)

**Dependencies:**
- Story 2.1 (Canonical LDM signed off) — defines target entities and attributes
- Story 1.1 (Scope Register) — defines which source systems feed each subject area
- Story 1.3 / 1.4 (Assessment findings) — highlights misalignments to resolve
- Source system SME availability per domain

**Tasks:**
1. Identify all source systems feeding each subject area:

   | Subject Area | Primary Source Systems |
   |---|---|
   | Party / Customer | Core banking CIF (T24 / Flexcube), CRM, KYC system |
   | Account | Core banking, card management system |
   | Transaction | Core banking, card switch, payment hub, trade system |
   | Contract | Core banking, loan origination system |
   | General Ledger | ERP / GL system (Oracle, SAP) |
   | Collateral | Collateral management system, core banking |
   | Credit Risk | Risk engine (Moody's, internal), provisioning system |
   | AML / Compliance | Screening system (Actimize, NICE), KYC workflow |
   | Regulatory Metrics | Risk engine, CBUAE reporting layer |
   | Cards | Card management system (VisionPLUS, FIS), Visa/Mastercard |
   | Trade Finance | Trade finance system (Finastra, Surecomp) |
   | Treasury | Treasury system (Murex, Calypso, Bloomberg) |
   | Payments | Payment hub (Temenos, Finastra), SWIFT gateway, UAEFTS |
   | Reference Data | Core banking, regulatory codes, CBUAE reference lists |

2. For each source system and subject area, produce a **Source-to-Logical Mapping Specification**:
   - Source system name and table / API endpoint
   - Source field name and data type
   - Target canonical entity and attribute
   - Transformation rule (direct map, lookup, derivation, concatenation, constant)
   - Handling of nulls, defaults, and exceptions
   - Business key derivation (e.g., how `party_id` is formed from CIF number)
   - SCD Type applied (Type 1 overwrite / Type 2 history)

3. Identify and document **mapping gaps and conflicts:**
   - Source fields with no canonical target (candidates for extension or exclusion)
   - Canonical attributes with no source (data not collected — raise as a gap)
   - Conflicting values from multiple sources (define survivorship / master data rules)
   - Encoding or format differences (dates, currencies, codes)

4. Define **cross-domain key resolution rules:**
   - `party_id` resolution across CIF, CRM, KYC, card system
   - `account_id` resolution across core banking, card system, GL
   - `contract_id` resolution across loan origination and core banking

5. Validate mappings with source system SMEs and business data owners

6. Publish mappings in the **Source-to-Logical Mapping Register** (one tab per subject area)

7. Sign off mappings per subject area before build begins

**Deliverables:**
- Source-to-Logical Mapping Register (spreadsheet / wiki, one sheet per subject area)
- Mapping gaps and conflicts log with resolution decisions
- Cross-domain key resolution rules document
- Survivorship / master data rules for conflicting sources

**Acceptance Criteria / KPI:**
- 100 % of Tier 1 subject area mappings signed off before EPIC 3 build starts
- 100 % of Tier 2 subject area mappings signed off before EPIC 4 build starts
- All mapping gaps triaged (accepted, deferred, or escalated)
- No build story proceeds without an approved mapping specification

**Test / Validation:**
- Engineering team dry-runs one transformation against the mapping spec and confirms correctness
- Business data owner spot-checks 10 % of mapped attributes per domain

---

### Story 2.3 — Silver Layer Target Design

**Description:** Finalize the target Silver layer structure mapped to the canonical LDM. Table designs are driven directly by the Source-to-Logical Mapping Register from Story 2.2.

**Owner:** Silver Layer Design Lead  
**Estimate:** 5 days

**Dependencies:**
- Story 2.1 (Canonical LDM signed off)
- Story 2.2 (Source-to-Logical Mapping Register) — drives table columns, SCD type, and transformation logic

**Tasks:**
1. Define Silver layer design principles:
   - One schema per subject area (e.g., `silver.customer`, `silver.risk_credit`, `silver.card`)
   - Supertype/subtype patterns for Account and Transaction
   - SCD Type 2 for slowly changing dimensions; Type 1 for reference data
2. Map every canonical entity to a target Silver table — full list:
   - `silver.customer.party`, `silver.customer.party_role`, `silver.customer.party_relationship`
   - `silver.account.account` (CASA, Loan, Card, TD, TF subtypes)
   - `silver.product.product`, `silver.product.product_category`, `silver.product.product_type`
   - `silver.transaction.financial_transaction`
   - `silver.contract.contract`, `silver.contract.contract_party_role`
   - `silver.gl.gl_account`, `silver.gl.gl_posting`, `silver.gl.subledger_posting`
   - `silver.collateral.collateral`, `silver.collateral.collateral_valuation`, `silver.collateral.contract_collateral_link`
   - `silver.reference.currency`, `silver.reference.country`, `silver.reference.branch`, `silver.reference.calendar`
   - `silver.risk_credit.*`, `silver.risk_liquidity.*`, `silver.risk_market.*`, `silver.risk_operational.*`
   - `silver.compliance.*`, `silver.regulatory.*`
   - `silver.card.*`, `silver.trade.*`, `silver.treasury.*`, `silver.payment.*`
   - `silver.channel.*`, `silver.limit.*`, `silver.fee.*`, `silver.insurance.*`
3. Identify shared vs. domain-specific tables
4. Confirm history handling per table (SCD Type 1 / 2 / snapshot)
5. Build a reference implementation for one domain (e.g., Party / Customer)

**Deliverables:**
- Target Silver schema catalog (schema → table → columns → SCD type → source)
- Reusable design patterns and anti-patterns doc
- Reference implementation for Party / Customer

**Acceptance Criteria / KPI:**
- Target Silver design signed off
- Design is reusable across all subject areas

**Test / Validation:**
- Prototype deployment for Party / Customer with logical → physical verification

---

### Story 2.4 — Physical Model & Databricks Standards Definition

**Description:** Define how Silver is physically implemented in Databricks.

**Owner:** Data Platform Standards Lead  
**Estimate:** 4 days

**Tasks:**
1. Define standard table DDL templates (managed Delta tables in Unity Catalog)
2. Define partitioning guidelines:
   - Date-based partitioning for transactions and events
   - No partitioning for small reference tables
3. Define clustering / Z-ordering strategy for high-cardinality joins
4. Define SCD handling:
   - Type 2 via `_valid_from`, `_valid_to`, `_is_current` columns
   - Type 1 for reference data with `_updated_at`
5. Define naming and folder standards:
   - Catalog: `silver`
   - Schema: `<subject_area>` (e.g., `customer`, `risk_credit`, `card`)
   - Table: `<entity>` (e.g., `party`, `credit_exposure`)
6. Define pipeline orchestration standards (Databricks Jobs / Workflows)
7. Define operational lifecycle: `OPTIMIZE`, `VACUUM`, retention policies

**Deliverables:**
- Databricks Silver standards document
- DDL templates and CI linting rules
- Operational runbook (vacuum, optimize, retention)

**Acceptance Criteria / KPI:**
- Physical design standards approved
- Databricks implementation guide published

**Test / Validation:**
- Apply standards to the Party reference implementation and run automated checks

---

### Story 2.5 — Data Quality Framework Definition

**Description:** Define the data quality operating model for the Silver layer.

**Owner:** Data Governance Lead  
**Estimate:** 4 days

**Tasks:**
1. Define DQ rule types per subject area:
   - **Completeness** — mandatory CDEs not null
   - **Validity** — format, range, referential integrity
   - **Uniqueness** — PK uniqueness checks
   - **Reconciliation** — source → Silver row counts and aggregates
   - **Timeliness** — SLA for data freshness
2. Define ownership model: business data owner vs. IT data steward per subject area
3. Define DQ severity and escalation (Critical → auto-block pipeline; Major → alert; Minor → log)
4. Define monitoring and alerting approach (dashboards, Slack/Teams, PagerDuty)
5. Align with governance tooling strategy (Unity Catalog, Informatica, Great Expectations)

**Deliverables:**
- DQ framework document
- Monitoring and alerting playbook
- Ownership matrix (subject area × owner × steward)

**Acceptance Criteria / KPI:**
- DQ framework baseline approved
- Ownership model agreed

**Test / Validation:**
- Configure an initial DQ pipeline for `silver.customer.party` CDEs and validate alerts

---

## EPIC 3: Build & Remediation — Phase 1 (Priority Domains)

**Timeline:** Days 31–45  
**Objective:** Implement corrected Silver model for high-priority domains; standardize pipelines; start DQ  
**Output:** Partially remediated Silver layer (priority domains); standardized ingestion pipelines initiated

---

### Story 3.1 — Party / Customer Domain Build

**Description:** Implement the canonical Party / Customer Silver model.

**Owner:** Customer Domain Engineer  
**Estimate:** 5 days

**Tasks:**
1. Create schema `silver.customer` in Unity Catalog
2. Implement `silver.customer.party` (SCD Type 2) — source: core banking CIF
3. Implement `silver.customer.party_role` — source: CIF + CRM
4. Implement `silver.customer.party_relationship` — source: CIF relationships
5. Implement source → Silver mapping and transformation (Databricks notebook / DLT)
6. Backfill historical data with idempotent load
7. Validate: row counts, key CDEs (party_id, party_name, kyc_status), referential integrity

**Acceptance Criteria / KPI:**
- `silver.customer.*` tables live with data aligned to LDM
- Source → Silver reconciliation 0 % variance

**Test / Validation:**
- BAT sign-off from CRM / Retail Banking team

---

### Story 3.2 — Account Domain Build

**Description:** Implement the canonical Account Silver model.

**Owner:** Account Domain Engineer  
**Estimate:** 5 days

**Tasks:**
1. Create schema `silver.account`
2. Implement `silver.account.account` with typed hierarchy (CASA, Loan, Card, TD, TF) — SCD Type 2
3. Map source systems (T24 / Flexcube / card system) to canonical account entity
4. Implement transformations and backfill
5. Validate: account_id uniqueness, balance accuracy, account_type completeness

**Acceptance Criteria / KPI:**
- `silver.account.account` live covering all product types
- Reconciliation with source GL balances

**Test / Validation:**
- Finance / GL team verifies sample balances

---

### Story 3.3 — Transaction / Event Domain Build

**Description:** Implement the canonical Transaction Silver model.

**Owner:** Transaction Domain Engineer  
**Estimate:** 5 days

**Tasks:**
1. Create schema `silver.transaction`
2. Implement `silver.transaction.financial_transaction` as parent supertype (SCD Type 1, append-only partitioned by `value_date`)
3. Map source transaction feeds (core banking, cards, payments, trade) into canonical structure
4. Implement CDC / streaming ingestion via Confluent for real-time feeds
5. Validate: txn_id uniqueness, amount aggregates vs source, currency consistency

**Acceptance Criteria / KPI:**
- `silver.transaction.financial_transaction` live
- Daily reconciliation automated

**Test / Validation:**
- Reconciliation with GL day-end totals

---

### Story 3.4 — Credit Risk Domain Build

**Description:** Implement the canonical Credit Risk Silver model (regulatory priority).

**Owner:** Risk Domain Engineer  
**Estimate:** 5 days

**Tasks:**
1. Create schema `silver.risk_credit`
2. Implement `silver.risk_credit.credit_exposure` — IFRS 9 / Basel III exposure at default
3. Implement `silver.risk_credit.credit_rating` — internal and external ratings
4. Implement `silver.risk_credit.ecl_component` — PD, LGD, EAD, staging
5. Map source: risk engine, rating systems, provisioning
6. Validate: exposure totals, staging distribution, ECL aggregates

**Acceptance Criteria / KPI:**
- `silver.risk_credit.*` tables live and reconciled
- IFRS 9 staging distribution matches risk engine output

**Test / Validation:**
- Risk team validates ECL figures against current reporting

---

### Story 3.5 — General Ledger Domain Build

**Description:** Implement the canonical GL Silver model.

**Owner:** GL Domain Engineer  
**Estimate:** 4 days

**Tasks:**
1. Create schema `silver.gl`
2. Implement `silver.gl.gl_account` (chart of accounts — SCD Type 2)
3. Implement `silver.gl.gl_posting` (daily postings — append, partitioned by posting_date)
4. Implement `silver.gl.subledger_posting` (sub-ledger detail)
5. Validate: trial balance reconciliation, inter-entity elimination

**Acceptance Criteria / KPI:**
- `silver.gl.*` tables live
- Trial balance matches source ERP within tolerance

**Test / Validation:**
- Finance team sign-off on trial balance reconciliation

---

### Story 3.6 — Ingestion Pipeline Standardization

**Description:** Align ingestion to standardized patterns across priority domains.

**Owner:** Ingestion Platform Lead  
**Estimate:** 7 days

**Tasks:**
1. Define and implement standard CDC pattern (Debezium / Confluent Connect → Bronze → Silver)
2. Define and implement standard batch ingestion pattern (file / API → Bronze → Silver)
3. Implement streaming ingestion for real-time transaction feeds via Confluent
4. Standardize schema registry integration for all topics
5. Implement common error-handling: dead-letter queues, retry, alerting
6. Create ingestion template repo with CI checks and documentation
7. Apply standardized pipelines to Party, Account, Transaction, Risk, GL domains

**Deliverables:**
- Ingestion pattern library (CDC, batch, streaming)
- Template repo with CI
- Error-handling and retry strategy guide

**Acceptance Criteria / KPI:**
- Standard ingestion patterns applied to all Phase 1 domains
- Pipeline failure rate reduced by ≥ 50 %

**Test / Validation:**
- Simulate schema drift and confirm graceful handling
- Track failure metrics over two weeks

---

### Story 3.7 — Data Quality Implementation — Phase 1

**Description:** Implement DQ checks for Phase 1 domains.

**Owner:** Data Quality Engineers  
**Estimate:** 5 days

**Tasks:**
1. Implement completeness checks on CDEs for Party, Account, Transaction, Risk, GL
2. Implement PK uniqueness checks per table
3. Implement referential integrity checks (e.g., `party_id` in Account exists in Party)
4. Implement source → Silver reconciliation checks (row counts, aggregates)
5. Enable DQ logging
6. Configure alerting (Slack/Teams) for Critical and Major failures
7. Document known accepted exceptions

**Deliverables:**
- DQ rules running for all Phase 1 domains
- DQ dashboard (pass-rate by domain, trend)
- Exceptions registry

**Acceptance Criteria / KPI:**
- DQ checks running for all Phase 1 Silver tables
- DQ pass-rate visible on dashboard

**Test / Validation:**
- Inject known-bad data and confirm alerts fire correctly

---

## EPIC 4: Build & Stabilization — Phase 2

**Timeline:** Days 46–60  
**Objective:** Complete Silver remediation across remaining domains; operationalize governance  
**Output:** Stabilized and governed Silver layer; operational DQ framework; platform ready for use-case delivery

---

### Story 4.1 — Cards Domain Build

**Description:** Implement the Cards Silver model.

**Owner:** Cards Domain Engineer  
**Estimate:** 5 days

**Tasks:**
1. Create schema `silver.card`
2. Implement `silver.card.card_account`, `silver.card.card_transaction`, `silver.card.card_limit`, `silver.card.card_authorization`
3. Map source card system (Visa/Mastercard, card management) → canonical entities
4. Backfill and validate

**Acceptance Criteria / KPI:**
- `silver.card.*` tables live and reconciled with card system

---

### Story 4.2 — Trade Finance Domain Build

**Description:** Implement the Trade Finance Silver model.

**Owner:** Trade Domain Engineer  
**Estimate:** 5 days

**Tasks:**
1. Create schema `silver.trade`
2. Implement `silver.trade.letter_of_credit`, `silver.trade.bank_guarantee`, `silver.trade.trade_transaction`, `silver.trade.bill_collection`
3. Map source trade finance system → canonical entities
4. Backfill and validate

**Acceptance Criteria / KPI:**
- `silver.trade.*` tables live and reconciled

---

### Story 4.3 — Treasury Domain Build

**Description:** Implement the Treasury Silver model.

**Owner:** Treasury Domain Engineer  
**Estimate:** 4 days

**Tasks:**
1. Create schema `silver.treasury`
2. Implement `silver.treasury.deal`, `silver.treasury.fx_position`, `silver.treasury.money_market_placement`, `silver.treasury.securities_holding`
3. Map source treasury system (e.g., Murex, Calypso) → canonical entities
4. Backfill and validate

**Acceptance Criteria / KPI:**
- `silver.treasury.*` tables live and reconciled

---

### Story 4.4 — Payments Domain Build

**Description:** Implement the Payments Silver model.

**Owner:** Payments Domain Engineer  
**Estimate:** 4 days

**Tasks:**
1. Create schema `silver.payment`
2. Implement `silver.payment.payment_instruction`, `silver.payment.payment_clearing`, `silver.payment.nostro_reconciliation`
3. Map source payment rails (SWIFT, UAEFTS, WPS, IPI) → canonical entities
4. Backfill and validate

**Acceptance Criteria / KPI:**
- `silver.payment.*` tables live and reconciled with nostro statements

---

### Story 4.5 — Remaining Domains Build (Collateral, Compliance, Limits, Fees, Channels, Insurance, Regulatory)

**Description:** Implement Silver models for all remaining subject areas.

**Owner:** Domain Engineering Teams  
**Estimate:** 7 days (parallel streams)

**Tasks:**
1. Create schemas: `silver.collateral`, `silver.compliance`, `silver.limit`, `silver.fee`, `silver.channel`, `silver.insurance`, `silver.regulatory`, `silver.risk_liquidity`, `silver.risk_market`, `silver.risk_operational`
2. Implement all target entities per the Silver schema catalog from Story 2.2
3. Map source systems → canonical entities
4. Backfill and validate
5. Remove / deprecate redundant legacy Silver tables
6. Update Unity Catalog metadata and lineage

**Acceptance Criteria / KPI:**
- 100 % of target Silver entities implemented
- All deprecated tables flagged for decommission

**Test / Validation:**
- Full reconciliation per domain

---

### Story 4.6 — Data Quality & Governance Activation

**Description:** Operationalize DQ and governance capabilities across the full Silver layer.

**Owner:** Data Governance & Platform Teams  
**Estimate:** 7 days

**Tasks:**
1. Extend DQ checks to all Phase 2 Silver tables
2. Enable DQ dashboards with domain-level drill-down
3. Assign data owners and stewards per subject area (update RACI)
4. Register all Silver tables in Unity Catalog with:
   - Column-level descriptions and tags
   - Classification labels (PII, confidential, public)
   - Row-level and column-level security policies
5. Integrate with governance tooling (Informatica, Collibra, or equivalent)
6. Define and enforce access and entitlement models

**Deliverables:**
- DQ dashboards in production (all domains)
- Unity Catalog fully populated with metadata and policies
- Data owner / steward assignments and runbook

**Acceptance Criteria / KPI:**
- DQ dashboards live for all Silver domains
- Governance controls active and auditable

**Test / Validation:**
- Governance audit: verify access control, lineage, and classification for sample datasets

---

### Story 4.7 — End‑to‑End Validation & Readiness

**Description:** Validate data readiness for downstream consumers across all use cases.

**Owner:** QA Lead / Platform PM  
**Estimate:** 5 days

**Tasks:**
1. Perform source → Silver → Gold / consumption reconciliation for:
   - Risk reporting (IFRS 9, Basel III, CBUAE submissions)
   - Finance / GL reporting (trial balance, P&L)
   - CRM / Retail analytics
   - Cards portfolio reporting
   - Trade finance exposure
   - Treasury position reporting
2. Conduct performance testing (query latency, concurrent users, job SLAs)
3. Resolve critical defects
4. Obtain final platform readiness sign-off

**Deliverables:**
- E2E reconciliation report per use case
- Performance test results and tuning notes
- Final readiness sign-off checklist

**Acceptance Criteria / KPI:**
- Reconciliation variance < agreed threshold per domain
- Platform approved for downstream use

**Test / Validation:**
- Reconciliation metrics and performance tests meet SLA

---

## EPIC 5: Program Management & Reporting (Runs Throughout)

**Objective:** Ensure delivery transparency and stakeholder alignment

**Owner:** Program Manager / Delivery Lead  
**Estimate:** Ongoing (60 days)

**Stories / Tasks:**
- Fortnightly milestone reporting aligned to the 4 phases (Days 0–15, 16–30, 31–45, 46–60)
- Jira sprint planning, tracking, and burn-down
- Risk and dependency management (risk register with weekly review)
- Stakeholder communication: weekly status email, fortnightly demo
- Escalation management for blocked items

**Processes / Cadence:**
- Two-week sprints with demo and retrospective
- Weekly leadership status with RAG indicators

**Deliverables:**
- Program backlog and sprint plan
- Risk register with mitigation actions
- Stakeholder cadence and demo schedule

**KPIs to Track in Jira:**

| KPI | Target |
|---|---|
| % Silver subject areas assessed | 100 % by Day 15 |
| % Silver tables aligned to target model | 100 % by Day 60 |
| Critical DQ issues open vs. closed | ≤ 5 open at Day 60 |
| Pipeline failure rate | ≥ 50 % reduction by Day 45 |
| Source → Silver reconciliation accuracy | ≥ 99.9 % by Day 60 |
| Business sign-offs achieved on time | 100 % |

---

## End of 60 Days — Expected Outcome

- ✅ Enterprise-aligned Silver data foundation established across all subject areas
- ✅ Logical → Physical model consistency achieved
- ✅ Standardized ingestion and lifecycle in place (CDC, batch, streaming)
- ✅ Data quality and governance operationalized
- ✅ Platform ready to reliably support Risk, ERP, CRM, Cards, Trade Finance, Treasury, Payments, and future use cases
