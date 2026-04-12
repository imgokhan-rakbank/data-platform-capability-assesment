# Domain 3: Silver Layer – Model Review Assessment

> **Assessor(s):**
> **Date:**
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

The Silver layer is the **conformed, cleansed, and integrated** zone of the Medallion Architecture. It is the central modelling layer for a banking data platform. Data here is:
- **Cleansed** – business rules applied, nulls handled, type casting done.
- **Integrated** – multiple source feeds for the same entity resolved into a single authoritative representation.
- **Historised** – slowly changing dimensions or temporal tables maintained.
- **Domain-aligned** – structured around banking subject areas, not source system artefacts.

The Silver layer must support both analytical consumption (via Gold) and operational/risk reporting (directly or via thin Gold views). Assessment questions are structured across all four modelling perspectives and evaluate banking domain completeness, including the foundational subject areas and risk-critical datasets.

---

## 3.1 Conceptual Design & Subject Area Coverage

Conceptual design at Silver defines the subject areas, their boundaries, and their relationships at a business level — independent of any technical implementation.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a documented conceptual data model (entity-relationship diagram or subject area map) for the Silver layer covering the core banking domain? | |
| 2 | Are the following **foundational banking subject areas** explicitly represented in the Silver conceptual model? For each, state whether it exists, is planned, or is absent: | |
|   | – **Party / Customer** (individual and corporate counterparties, identity, KYC attributes) | |
|   | – **Product** (product catalogue, product hierarchy, product parameters) | |
|   | – **Account** (all account types: current, savings, loan, card, investment, trade finance) | |
|   | – **Transaction** (financial transactions, movements, postings) | |
|   | – **Contract / Agreement** (loan agreements, credit facilities, term deposits, trade finance contracts) | |
|   | – **Channel** (branch, digital, ATM, call centre — interaction and event data) | |
|   | – **Reference Data** (currencies, countries, industry codes, rate types, GL codes, regulatory codes) | |
|   | – **General Ledger / Finance** (GL entries, cost centres, P&L lines) | |
|   | – **Collateral** (collateral types, valuations, collateral-to-facility linkages) | |
|   | – **Risk – Credit** (credit ratings, probability of default, exposure at default, loss given default, expected credit loss) | |
|   | – **Risk – Market** (positions, valuations, VaR inputs, interest rate sensitivity) | |
|   | – **Risk – Liquidity** (liquidity ratios, LCR/NSFR components, cash flow projections) | |
|   | – **Risk – Operational** (incident register, loss events, controls register) | |
|   | – **Regulatory / Compliance** (CBUAE / Basel III reporting entities, regulatory classifications, AML flags) | |
|   | – **Employee / HR** (supporting internal fraud and access risk) | |
| 3 | Are subject area boundaries clearly defined to prevent overlap — e.g., is it clear whether a "card" is an Account or a Product, and how a card Transaction relates to both? | |
| 4 | Is there a defined integration model showing how subject areas relate to each other (e.g., Party linked to Account, Account linked to Transaction, Collateral linked to Contract)? | |
| 5 | Are subject areas versioned and is there a process for adding new subject areas as business needs evolve? | |
| 6 | Is there a documented maturity level per subject area — distinguishing between foundational coverage (core attributes) and extended coverage (advanced analytics / risk attributes)? | |
| 7 | Are banking-specific modelling patterns (e.g., party role model, product hierarchy, financial instrument classification per IFRS 9) applied in the conceptual design? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Conceptual Model / Diagram Location | |
| Subject Areas Currently Implemented | |
| Subject Areas Planned / In Progress | |
| Subject Areas Missing / Gaps | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Modelling | | |
| Banking Domain Coverage (Foundational) | | |
| Banking Domain Coverage (Risk & Extended) | | |
| **Sub-Area Overall** | | |

---

## 3.2 Logical Design – Core Banking Entities

Logical design at Silver examines the attribute-level detail of the most important banking entities, normalisation choices, key design, and relationship cardinality.

### Questions

**Party / Customer**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a unified Party model that handles both **individual** and **corporate / legal entity** counterparties under a single structure with type-specific attributes? | |
| 2 | Does the Party model support **party roles** (e.g., borrower, guarantor, beneficial owner, authorised signatory) linked to contracts/accounts, rather than hard-coding roles into the party entity itself? | |
| 3 | Are KYC / AML attributes (risk rating, due diligence status, PEP flag, sanctions screening status, next review date) part of the Party model? | |
| 4 | Is there a mechanism to consolidate duplicate party records from multiple source systems into a single golden record (Master Data Management or survivorship rules)? | |
| 5 | Is party relationship management modelled — e.g., corporate hierarchies, household groupings, relationship manager assignment? | |

**Account**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 6 | Does the Account model support all account types relevant to the bank (current, savings, fixed deposit, loan/financing, credit card, investment, trade finance facility) through a typed hierarchy or discriminator pattern? | |
| 7 | Are account lifecycle states (open, dormant, restricted, closed, written-off) modelled and historised? | |
| 8 | Are account balances captured as a separate fact (balance history / daily balance snapshot) rather than denormalised into the account entity? | |
| 9 | Is there a clear model for multi-currency accounts and the representation of book currency vs. reporting currency balances? | |

**Product**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 10 | Is there a product catalogue / product hierarchy model that classifies products by type, sub-type, and regulatory classification? | |
| 11 | Are product parameters (interest rate basis, fee structure, tenure, amortisation type) captured in a way that supports historical rate/parameter lookups? | |

**Transaction**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 12 | Is there a unified transaction model that integrates transactions from multiple source systems (core banking, card, payments) into a consistent structure with a standard transaction type classification? | |
| 13 | Are debit/credit indicators, transaction direction, and double-entry accounting principles respected in the transaction model? | |
| 14 | Is transaction status lifecycle (pending, cleared, reversed, failed) captured and historised? | |
| 15 | Are large-value or suspicious transactions flagged or enriched with AML / fraud attributes in Silver, or is that deferred to Gold? Is the boundary documented? | |

**Contract / Facility**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 16 | Is there a credit facility / contract model that captures limit, utilisation, tenor, collateral linkage, and pricing for loans, overdrafts, and trade finance instruments? | |
| 17 | Does the contract model support the IFRS 9 staging attributes (Stage 1 / 2 / 3, ECL provision, significant increase in credit risk indicators)? | |

**Risk Datasets**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 18 | Are credit risk attributes (PD, LGD, EAD, ECL, collateral coverage ratio) modelled in Silver at the obligor and facility level, or are they only available in source system extracts? | |
| 19 | Is there a liquidity risk dataset in Silver that captures contracted cash flows, behavioural cash flows, and liquidity buffer assets sufficient to support LCR / NSFR reporting in Gold? | |
| 20 | Is there a market risk positions dataset in Silver capturing open positions, mark-to-market valuations, and sensitivity measures (DV01, CS01)? | |
| 21 | Is operational risk loss event data modelled in Silver with event category, business line, loss amount, recovery amount, and regulatory classification (Basel operational risk categories)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Logical Model Location (ER diagrams, dbt docs, etc.) | |
| Party Model Implementation Status | |
| Account Model Implementation Status | |
| Transaction Model Implementation Status | |
| Risk Dataset Implementation Status | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Logical Modelling – Party / Customer | | |
| Logical Modelling – Account | | |
| Logical Modelling – Transaction | | |
| Logical Modelling – Product & Contract | | |
| Logical Modelling – Risk Datasets | | |
| **Sub-Area Overall** | | |

---

## 3.3 Physical Design

Physical design at Silver covers the concrete implementation of the logical model: table structures, naming conventions, storage optimisation, historisation implementation, and query performance.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined and consistently applied naming convention for Silver databases, schemas, and tables (e.g., `silver.<domain>.<entity>_<grain>`)? | |
| 2 | Are surrogate keys generated consistently (e.g., hash of natural key, UUID) to decouple Silver from source system identifiers? | |
| 3 | Is the historisation / SCD pattern (Type 1 overwrite, Type 2 versioning, Type 4 history table) defined per entity type and consistently implemented? | |
| 4 | Are standard Silver audit / lineage columns present on every table: `silver_created_ts`, `silver_updated_ts`, `source_system_code`, `bronze_batch_id`, `is_current_flag`, `valid_from_ts`, `valid_to_ts`? | |
| 5 | Is data partitioning strategy defined for Silver tables based on typical access patterns (e.g., partition by `account_open_date` for account tables, by `transaction_date` for fact tables)? | |
| 6 | Are Delta Lake optimisations applied — Liquid Clustering or Z-ORDER on high-cardinality join keys (e.g., `account_id`, `party_id`) to support efficient Silver-to-Gold queries? | |
| 7 | Are Silver tables created via automated DDL generation or transformation frameworks (e.g., dbt models) rather than hand-crafted SQL scripts? | |
| 8 | Are test assertions (not-null, unique, referential integrity, accepted values) defined and executed on Silver table outputs as part of the pipeline? | |
| 9 | Is there a clear definition of the Silver table grain for each entity (e.g., one row per account per day for account snapshots, one row per transaction for transaction facts)? | |
| 10 | Are data type standards enforced — e.g., monetary amounts always as `DECIMAL(18,4)` or `DECIMAL(28,8)`, dates as `DATE`, timestamps as `TIMESTAMP`? | |
| 11 | Are foreign key relationships between Silver tables documented (even if not enforced by the engine) and validated through data quality checks? | |
| 12 | Is there a physical model review checklist that is completed before a new Silver table is deployed to production? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Naming Convention Documentation | |
| Surrogate Key Strategy | |
| SCD / Historisation Pattern | |
| Standard Audit Columns List | |
| Transformation Framework (e.g., dbt, PySpark notebooks) | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Physical Modelling | | |
| Storage Optimisation | | |
| Standards Conformance | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 3.4 Semantic / Business Layer

The semantic perspective at Silver covers how business meaning is applied to the integrated data — business-friendly naming, definitions, data dictionaries, and the exposure of cleansed data to self-service consumers.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are all Silver tables and columns documented with business-friendly descriptions in a data catalogue (e.g., Unity Catalog, Collibra, Atlan)? | |
| 2 | Is there a business glossary linked to Silver table attributes — for example, is "Effective Interest Rate" defined in the glossary and linked to the corresponding column(s)? | |
| 3 | Are calculated / derived attributes in Silver (e.g., days past due, outstanding balance after partial repayment) documented with their calculation logic? | |
| 4 | Are Silver tables classified by sensitivity (PII, confidential, restricted, public) and are access policies enforced based on classification? | |
| 5 | Is there a domain-based ownership model — i.e., does a business domain team (e.g., Risk, Finance, Retail Banking) own and steward the Silver tables relevant to their domain? | |
| 6 | Are Silver tables published to a data marketplace or self-service catalogue so that Gold layer developers and analysts can discover and understand available datasets? | |
| 7 | Are data quality scores / completeness metrics published at the Silver table level and visible to consumers? | |
| 8 | Are Silver tables versioned with a documented schema change history so that consumers understand what changed between versions? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Catalogue Tool | |
| Business Glossary Tool / Location | |
| Data Classification Status | |
| Domain Ownership Model | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Semantic / Business Layer | | |
| Discoverability | | |
| Stewardship | | |
| **Sub-Area Overall** | | |

---

## 3.5 Banking Domain Completeness – Foundational Subject Areas

This sub-area directly assesses whether the Silver layer provides the foundational banking datasets required to support core reporting and operations.

### Questions

| # | Foundational Subject Area | Is it modelled in Silver? | Coverage Level (Full / Partial / Absent) | Gap Description |
|---|---------------------------|--------------------------|------------------------------------------|-----------------|
| 1 | Party / Customer (individual & corporate) | | | |
| 2 | Account (all account types) | | | |
| 3 | Product Catalogue | | | |
| 4 | Financial Transaction | | | |
| 5 | Loan / Credit Contract & Facility | | | |
| 6 | Reference Data (currency, country, GL codes) | | | |
| 7 | Channel / Interaction Data | | | |
| 8 | General Ledger / Finance Posting | | | |
| 9 | Collateral | | | |
| 10 | Employee (for internal fraud / access risk) | | | |

### Additional Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are relationships between all foundational subject areas defined, documented, and tested (e.g., Account → Party → Product → Transaction)? | |
| 2 | Is the Silver layer capable of supporting regulatory reporting that requires combining multiple subject areas (e.g., a CBUAE credit register report combining Party, Account, Contract, Collateral, and Risk)? | |
| 3 | Is there a maturity roadmap for extending foundational subject areas with additional attributes (e.g., adding behavioural scoring to Party, adding IFRS 9 staging to Contract)? | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Foundational Subject Area Coverage | | |
| Relationship Completeness | | |
| Regulatory Readiness | | |
| **Sub-Area Overall** | | |

---

## 3.6 Banking Domain Completeness – Risk & Regulatory Datasets

This sub-area assesses whether Silver contains the risk-relevant datasets needed to support Gold-layer risk reporting (credit, market, liquidity, operational, regulatory).

### Questions

| # | Risk / Regulatory Subject Area | Is it modelled in Silver? | Coverage Level | Gap Description |
|---|-------------------------------|--------------------------|----------------|-----------------|
| 1 | Credit Risk – Obligor risk rating (PD, internal grade) | | | |
| 2 | Credit Risk – Facility exposure (EAD, outstanding, limit) | | | |
| 3 | Credit Risk – ECL / Impairment (IFRS 9 Stage, PD, LGD, ECL provision) | | | |
| 4 | Credit Risk – Non-performing loan (NPL) classification | | | |
| 5 | Collateral – Valuation and linkage to facility | | | |
| 6 | Liquidity Risk – Contractual cash flows by maturity bucket | | | |
| 7 | Liquidity Risk – HQLA buffer assets | | | |
| 8 | Market Risk – Trading and banking book positions | | | |
| 9 | Market Risk – MTM valuations and sensitivity | | | |
| 10 | Operational Risk – Loss events and near-misses | | | |
| 11 | AML / Fraud – Transaction monitoring flags and case data | | | |
| 12 | Regulatory – CBUAE reporting entities and classifications | | | |
| 13 | Regulatory – Large exposure data (connected counterparty groups) | | | |

### Additional Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are risk datasets in Silver sourced from authoritative risk systems (e.g., a dedicated credit risk engine, a treasury system) and properly integrated with the core Party/Account/Contract model? | |
| 2 | Are IFRS 9 staging attributes (Stage 1, 2, 3 classification triggers) modelled in Silver at the instrument level and historised to support point-in-time regulatory reporting? | |
| 3 | Is there a single definition of "exposure" in Silver that is consistently used across credit risk, large exposure, and regulatory reporting (avoiding different exposure calculations in different Gold models)? | |
| 4 | Are counterparty linkages (parent-subsidiary, connected parties) modelled to support large exposure aggregation required by CBUAE regulations? | |
| 5 | Is the Silver risk model granular enough to support Basel III / CBUAE Pillar 3 disclosures — can exposure be sliced by asset class, geography, industry, and product? | |
| 6 | Is there a liquidity gap analysis dataset in Silver (contractual maturities bucketed per the standard time bands) that can directly feed an LCR or NSFR calculation in Gold? | |
| 7 | Is there a written data lineage or mapping document tracing each risk metric in Gold back to its Silver source attributes? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Credit Risk Source System(s) | |
| Liquidity Risk Source System(s) | |
| Market Risk Source System(s) | |
| Operational Risk Source System(s) | |
| Regulatory Reporting Source System(s) | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Credit Risk Dataset Completeness | | |
| Liquidity Risk Dataset Completeness | | |
| Market Risk Dataset Completeness | | |
| Operational Risk Dataset Completeness | | |
| Regulatory Dataset Completeness | | |
| **Sub-Area Overall** | | |

---

## 3.7 Silver Best Practices

This sub-area checks adherence to widely accepted Silver-layer best practices in a banking lakehouse context.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the Silver layer free from business metric calculations and aggregations — i.e., are those deliberately deferred to Gold? | |
| 2 | Is there exactly one authoritative Silver table per business entity (e.g., one `silver.customer.party` table), or do multiple tables exist for the same entity leading to inconsistency? | |
| 3 | Are data quality rules (not-null, uniqueness, referential integrity, range checks, domain value checks) enforced and logged at the Silver loading step? | |
| 4 | Is there a reconciliation process validating that record counts and key financial totals (e.g., sum of outstanding balances) in Silver match the source system or Bronze? | |
| 5 | Is PII masked, tokenised, or encrypted in Silver tables for columns that do not need to be human-readable for analytical purposes (e.g., account number hashing, name masking in non-production environments)? | |
| 6 | Is the Silver layer designed to be replayable — i.e., can it be fully reconstructed from Bronze without any data loss if a Silver table is corrupted or needs to be rebuilt? | |
| 7 | Is there a clear policy on whether Silver tables can be read directly by analysts / BI tools, or whether all consumer access must go via Gold? Is this enforced? | |
| 8 | Are late-arriving records (e.g., backdated transactions, retroactive corrections) handled explicitly in Silver with a defined policy for how history is corrected? | |
| 9 | Is there an established change-data-management process so that when a Silver table schema changes, downstream Gold models are notified and updated in a controlled manner? | |
| 10 | Is the Silver model aligned with any industry standard data models (e.g., BIAN — Banking Industry Architecture Network, IFW — IBM Financial Data Model, FIBO — Financial Industry Business Ontology)? If not, is alignment planned? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Data Quality Rule Engine | |
| Reconciliation Process | |
| PII Masking / Tokenisation Tool | |
| Industry Model Alignment Status | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Best Practices Adherence | | |
| Data Quality Enforcement | | |
| Security & Privacy | | |
| Replayability | | |
| **Sub-Area Overall** | | |
