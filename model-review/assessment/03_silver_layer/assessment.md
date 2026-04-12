# Domain 3: Silver Layer – Model Review Assessment

> **Assessor(s):** Gokhan Imral / Copilot (from supporting artifacts)
> **Date:** 12.04.2026
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
| 1 | Is there a documented conceptual data model (entity-relationship diagram or subject area map) for the Silver layer covering the core banking domain? | Exists for some domains, but not comprehensive or up-to-date for all areas. |
| 2 | Are the following **foundational banking subject areas** explicitly represented in the Silver conceptual model? For each, state whether it exists, is planned, or is absent: | Partial. Some subject areas exist, but Product, Event/Transaction, and Party are not unified; sub-types do not exist. |
|   | – **Party / Customer** (individual and corporate counterparties, identity, KYC attributes) | Exists, but not unified; no sub-type structure. |
|   | – **Product** (product catalogue, product hierarchy, product parameters) | Does not really exist in a unified manner. |
|   | – **Account** (all account types: current, savings, loan, card, investment, trade finance) | Exists. |
|   | – **Transaction** (financial transactions, movements, postings) | Exists, but system-specific; no unified parent. |
|   | – **Contract / Agreement** (loan agreements, credit facilities, term deposits, trade finance contracts) | Exists. |
|   | – **Channel** (branch, digital, ATM, call centre — interaction and event data) | Exists. |
|   | – **Reference Data** (currencies, countries, industry codes, rate types, GL codes, regulatory codes) | Exists. |
|   | – **General Ledger / Finance** (GL entries, cost centres, P&L lines) | Exists. |
|   | – **Collateral** (collateral types, valuations, collateral-to-facility linkages) | Exists. |
|   | – **Risk – Credit** (credit ratings, probability of default, exposure at default, loss given default, expected credit loss) | Exists. |
|   | – **Risk – Market** (positions, valuations, VaR inputs, interest rate sensitivity) | Planned. |
|   | – **Risk – Liquidity** (liquidity ratios, LCR/NSFR components, cash flow projections) | Planned. |
|   | – **Risk – Operational** (incident register, loss events, controls register) | Absent. |
|   | – **Regulatory / Compliance** (CBUAE / Basel III reporting entities, regulatory classifications, AML flags) | Exists. |
|   | – **Employee / HR** (supporting internal fraud and access risk) | Absent. |
| 3 | Are subject area boundaries clearly defined to prevent overlap — e.g., is it clear whether a "card" is an Account or a Product, and how a card Transaction relates to both? | Partially defined; some overlaps and ambiguities remain. |
| 4 | Is there a defined integration model showing how subject areas relate to each other (e.g., Party linked to Account, Account linked to Transaction, Collateral linked to Contract)? | Exists for core entities, but not for all subject areas. |
| 5 | Are subject areas versioned and is there a process for adding new subject areas as business needs evolve? | Versioning is done manually in SharePoint; no easy collaboration or formal process. |
| 6 | Is there a documented maturity level per subject area — distinguishing between foundational coverage (core attributes) and extended coverage (advanced analytics / risk attributes)? | No. |
| 7 | Are banking-specific modelling patterns (e.g., party role model, product hierarchy, financial instrument classification per IFRS 9) applied in the conceptual design? | Partially applied for core domains. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Conceptual Model / Diagram Location | SharePoint (manual versioning, not collaborative) |
| Subject Areas Currently Implemented | Party, Account, Transaction (system-specific), Contract, Channel, Reference Data, GL/Finance, Collateral, Risk – Credit, Regulatory/Compliance |
| Subject Areas Planned / In Progress | Risk – Market, Risk – Liquidity |
| Subject Areas Missing / Gaps | Product (unified), Event/Transaction (unified), Risk – Operational, Employee/HR |
| Known Gaps / Limitations | No unified models for Product/Event/Party; no sub-type structure; manual versioning; not all boundaries are clear. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Modelling | 2 | Some models exist, but not comprehensive, unified, or consistently maintained. |
| Banking Domain Coverage (Foundational) | 2 | Most foundational subject areas exist, but Product/Event/Party are not unified; sub-types do not exist. |
| Banking Domain Coverage (Risk & Extended) | 2 | Risk and extended domains are planned or absent; not fully covered. |
| **Sub-Area Overall** | 2 | Coverage is partial; foundational areas are better represented than extended, but lack of unification and formal process is a major gap. |

---

## 3.2 Logical Design – Core Banking Entities

Logical design at Silver examines the attribute-level detail of the most important banking entities, normalisation choices, key design, and relationship cardinality.

### Questions

**Party / Customer**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a unified Party model that handles both **individual** and **corporate / legal entity** counterparties under a single structure with type-specific attributes? | Partially. The `customer.customer` table holds both individual and corporate counterparties using a `Customer_Type` discriminator column and a mix of individual fields (First_Name, Birth_date, ID_number) and corporate fields (Organization_type_code, Name_of_Company_Firm, Trade_License_Issue_Authority). There is no formal typed sub-type hierarchy — all attributes coexist in one flat table, and type-specific attributes are sparsely populated. The logical model defines separate entities (`Customer_Individual`, `Customer_Business`) but these are not physically implemented as distinct tables. |
| 2 | Does the Party model support **party roles** (e.g., borrower, guarantor, beneficial owner, authorised signatory) linked to contracts/accounts, rather than hard-coding roles into the party entity itself? | Partially. A `customer.customer_relationships` table exists, which can represent relationship types between parties. However, the account model uses denormalised role columns (`primary_account_holder`, `1st_joint_account_holder`, `2nd_joint_account_holder` in `account_agreement_master`) rather than a normalised party-role-to-account linkage. Role assignment to contracts is not explicitly modelled as a separate relationship entity. |
| 3 | Are KYC / AML attributes (risk rating, due diligence status, PEP flag, sanctions screening status, next review date) part of the Party model? | Partially. The `customer.customer` table contains `Blacklisted`, `Negated`, `Risk_Profile`, `Risk_Profile_Expiry_Date`, `Credit_Grading_Code`, `Counterparty_Rating`, and `Overall_Blacklisted_Status`. Explicit PEP flag and sanctions screening status columns are not present. KYC_status exists in `account_agreement_master` but is incorrectly typed as TIMESTAMP (data quality issue). |
| 4 | Is there a mechanism to consolidate duplicate party records from multiple source systems into a single golden record (Master Data Management or survivorship rules)? | No. Customer data arrives from multiple source systems (Finacle, Prime, etc.) each with their own `source_system_code`. There is no MDM deduplication or survivorship rule applied at Silver. The `customer.customer` table is effectively a source-system-specific extract from Finacle, not a cross-system golden record. |
| 5 | Is party relationship management modelled — e.g., corporate hierarchies, household groupings, relationship manager assignment? | Partially. `customer.customer_relationships` table exists. Relationship manager assignment is embedded in the customer table via `Primary_RM_Code`, `Secondary_RM_Code`, `Tertiary_RM_Code` (with name and email). Corporate group linkages use `Group_Code`, `Group_Name`, `Corporate_ID`, `Primary_Parent_Company`. No formal household or entity hierarchy model is implemented. |

**Account**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 6 | Does the Account model support all account types relevant to the bank (current, savings, fixed deposit, loan/financing, credit card, investment, trade finance facility) through a typed hierarchy or discriminator pattern? | No unified model. Account types are implemented as separate, unrelated tables across different schemas: `account.account_agreement_master` (CASA – Finacle), `credit_card.credit_card_account` (cards – FIS), `loan.loan_account_master` (loans – Finacle/Flexcube), `deposits.deposit_term_account` / `flexcube.flexcube_td_contract` / `prime4.prime_td_contract` (term deposits), `eximbills.*` (trade finance). No parent Account table or discriminator pattern exists that unifies these. |
| 7 | Are account lifecycle states (open, dormant, restricted, closed, written-off) modelled and historised? | Partially. `account_agreement_master` has an `account_status` column but it is incorrectly typed as TIMESTAMP (data quality defect). A `dormant_account` table exists for dormant accounts. Loan status lifecycle is present in `loan_account_master` via `loan_status_code` and `car_status_code`. SCD Type 2 (`effective_from`, `effective_to`, `is_current`) is applied to `account_agreement_master` and `loan_account_master`, enabling historisation of status changes. |
| 8 | Are account balances captured as a separate fact (balance history / daily balance snapshot) rather than denormalised into the account entity? | Partially. `account.account_eod_balance` and `account.account_balance_summary_dd` exist as dedicated balance tables. However, the `account_agreement_master` also contains `balance` (typed BIGINT — a data quality concern) and `account_clear_balance_amount`, creating denormalisation. No single standardised balance snapshot pattern is consistently applied across all account types. |
| 9 | Is there a clear model for multi-currency accounts and the representation of book currency vs. reporting currency balances? | Partially. `currency_code` and `account_currency` fields exist in the account table. The `account.currency_units_conversion_rate` table exists. However, there is no systematic pattern of dual-currency columns (original currency amount + AED/USD equivalent) applied uniformly across all amount columns. Some tables carry `_AED` suffixed columns (e.g., `non_funded_and_acceptances_amount_AED`) while others do not. |

**Product**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 10 | Is there a product catalogue / product hierarchy model that classifies products by type, sub-type, and regulatory classification? | No. `reference_product` tables exist within individual schemas (account, loan, credit_card) but there is no unified Product catalogue. `scheme_code` is used as a proxy for product code in the account table. The logical model defines a Product subject area but it has zero physical entities implemented at this stage. Product classification by type, sub-type, and regulatory classification is absent. |
| 11 | Are product parameters (interest rate basis, fee structure, tenure, amortisation type) captured in a way that supports historical rate/parameter lookups? | No. Product parameters are embedded within contract/account tables (e.g., `interest_rate`, `effective_rate`, `spread_rate` in `loan_account_master`) as current-state values. There is no dedicated product parameter history table. Rate lookups for historical periods are not supported through a structured product parameter model. |

**Transaction**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 12 | Is there a unified transaction model that integrates transactions from multiple source systems (core banking, card, payments) into a consistent structure with a standard transaction type classification? | No. Transactions are stored in system-specific tables: `finacle.finacle_casa_transaction`, `finacle.finacle_loan_transaction`, `finacle.finacle_td_transaction`, `flexcube.flexcube_loan_transaction`, `flexcube.flexcube_td_transaction`, `fis.fis_transaction`, `prime4.prime_td_transaction`. No unified transaction entity exists that consolidates these across systems with a common transaction type taxonomy. The logical model defines a unified Electronic Channel Transaction subject area but this is not physically implemented. |
| 13 | Are debit/credit indicators, transaction direction, and double-entry accounting principles respected in the transaction model? | Partially. `amount_tag` and `amount_tag_desc` columns exist in Finacle transaction tables (stored as STRING — a data type issue; should be DECIMAL for amounts). No explicit debit/credit indicator or transaction direction column is consistently applied across all transaction tables. Double-entry accounting linkage is not modelled at Silver. |
| 14 | Is transaction status lifecycle (pending, cleared, reversed, failed) captured and historised? | Not evidenced. No transaction status column or transaction state history table is visible in the physical model. Finacle transaction tables are snapshots of posted transactions without lifecycle tracking. |
| 15 | Are large-value or suspicious transactions flagged or enriched with AML / fraud attributes in Silver, or is that deferred to Gold? Is the boundary documented? | The boundary is not documented. The `customer.customer` table carries `Blacklisted` and `Overall_Blacklisted_Status` flags at the party level, but no transaction-level AML or fraud enrichment columns are present in transaction tables. Enrichment appears to be absent or deferred, without a formal documented boundary. |

**Contract / Facility**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 16 | Is there a credit facility / contract model that captures limit, utilisation, tenor, collateral linkage, and pricing for loans, overdrafts, and trade finance instruments? | Partially. `loan.loan_account_master` is the most complete contract table, containing `account_currency_commitment_amount` (limit), `account_currency_available_limit` (availability), `effective_rate`, `spread_rate`, `Tenure`, `loan_term`, `Maturity_Date`, `cash_collateral_value`, and `blended_ltv_amount`. Trade finance contracts exist separately in `eximbills.*` tables (LC, guarantees, etc.). No unified facility model across all credit product types. Overdraft contracts are in separate ODA-specific tables. Collateral linkage is only indirectly via LTV in the loan table. |
| 17 | Does the contract model support the IFRS 9 staging attributes (Stage 1 / 2 / 3, ECL provision, significant increase in credit risk indicators)? | Partially. `loan.loan_account_master` contains `NPA_Stage_Identifier` (NPL stages), `Provision_Amount`, `effective_provision_amount`, `interest_suspense_amount`, and `Bucket` (DPD bucket). However, explicit IFRS 9 Stage 1 / 2 / 3 classification columns are not present as named fields. The model captures provision outcomes but not the IFRS 9 stage decision logic or Significant Increase in Credit Risk (SICR) triggers. |

**Risk Datasets**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 18 | Are credit risk attributes (PD, LGD, EAD, ECL, collateral coverage ratio) modelled in Silver at the obligor and facility level, or are they only available in source system extracts? | Partially. `loan.loss_given_default` captures `collateral_property_amount_aed` and `collateral_shares_amount_aed` at obligor level. `loan.account_loss_given_default_detail` provides `loan_to_value_percentage` and `loan_amount` at facility level. `loan.loan_account_master` contains `Provision_Amount` and `effective_provision_amount`. PD (Probability of Default) is absent — no PD table or column is present. EAD (Exposure at Default) is implicitly represented by outstanding balances but no dedicated EAD column exists. ECL as a regulatory IFRS 9 concept (expected credit loss from PD × LGD × EAD) is not formally modelled. |
| 19 | Is there a liquidity risk dataset in Silver that captures contracted cash flows, behavioural cash flows, and liquidity buffer assets sufficient to support LCR / NSFR reporting in Gold? | Absent in the physical model. `UAECB_LIQUIDITY` appears in the logical model (Bank Asset subject area) but has no physical implementation visible in the current Silver schemas. No contractual maturity bucketing, cash flow projection, or HQLA buffer table is present. |
| 20 | Is there a market risk positions dataset in Silver capturing open positions, mark-to-market valuations, and sensitivity measures (DV01, CS01)? | Absent. No market risk or treasury position tables are visible in the current physical Silver model. The logical model defines an Investment subject area with trade and position entities, but these are not physically implemented. |
| 21 | Is operational risk loss event data modelled in Silver with event category, business line, loss amount, recovery amount, and regulatory classification (Basel operational risk categories)? | Absent in the physical model. The logical model defines an extensive ORM subject area (39 entities including loss data collection, KRI, risk incidents) but none of these are physically implemented in Silver currently. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Logical Model Location (ER diagrams, dbt docs, etc.) | SharePoint (Erwin logical model) + `logical_silver.xlsx` (14 subject areas, 400+ entities defined at logical level) |
| Party Model Implementation Status | Partial — `customer.customer` (single flat table, individual+corporate combined), `customer.customer_relationships`, `customer.customer_contactinformation`, `customer.customer_demographics`, `customer.customer_documents`, `customer.customer_preferences`. No sub-type hierarchy; no golden record/MDM. |
| Account Model Implementation Status | Partial — Implemented as separate system-specific tables by account type. No unified parent account entity. SCD2 applied to `account_agreement_master`. Separate balance snapshot tables exist. |
| Transaction Model Implementation Status | Partial — System-specific tables only (Finacle, Flexcube, FIS, Prime4). No unified transaction model. |
| Risk Dataset Implementation Status | Partial (Credit Risk only) — LGD and LTV at facility level; NPL flag and provision amounts in loan tables. PD, formal ECL, liquidity, market risk, and operational risk datasets are absent. |
| Known Gaps / Limitations | No unified Product model; no unified Transaction model; no PD/EAD/formal ECL model; no liquidity or market risk datasets; no MDM for party; party role-to-contract linkage not normalised; account_status and KYC_status incorrectly typed as TIMESTAMP; amounts stored as STRING in some transaction tables. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Logical Modelling – Party / Customer | 2 | Customer entity exists with reasonable attributes but no typed sub-type hierarchy, no MDM, and roles are denormalised. |
| Logical Modelling – Account | 2 | Multiple account tables exist but no unified model; balance snapshots partially in place; status column has data type defect. |
| Logical Modelling – Transaction | 1 | Purely system-specific transaction tables; no unified transaction model or standard taxonomy. |
| Logical Modelling – Product & Contract | 2 | Contract/facility is reasonably detailed for loans; Product model is entirely absent. IFRS 9 staging attributes only partially present. |
| Logical Modelling – Risk Datasets | 1 | Only partial LGD/LTV present; PD, formal ECL, liquidity, market risk, and OpRisk datasets absent. |
| **Sub-Area Overall** | 2 | Core banking entities are foundationally covered but fragmented and inconsistent. Risk datasets are critically incomplete for a banking platform. |

---

## 3.3 Physical Design

Physical design at Silver covers the concrete implementation of the logical model: table structures, naming conventions, storage optimisation, historisation implementation, and query performance.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined and consistently applied naming convention for Silver databases, schemas, and tables (e.g., `silver.<domain>.<entity>_<grain>`)? | Defined in architecture documents (`architecture_patterns.txt`) with two sub-layers: Silver ODS (`silver_ods.{source_system}.{schema}_{table}`) and Silver Analytical (`silver_analytical.{domain}.{entity}`). However, the physical catalog (`silver_dev_v2`) shows mixed adherence: analytical schemas (customer, account, loan, credit_card) follow domain naming, but source-specific schemas (finacle, flexcube, erp, fis, prime4, eximbills) replicate ODS naming. Column naming is inconsistent: 72 of 148 tables have PascalCase column names (e.g., the entire `customer.customer` table), while others use snake_case — not enforced. |
| 2 | Are surrogate keys generated consistently (e.g., hash of natural key, UUID) to decouple Silver from source system identifiers? | No. Natural / source-system keys are used throughout (e.g., `account_id`, `account_number`, `CIF_NO`, `cif_no`). No platform-wide surrogate key generation strategy (hash or UUID) is implemented. Different tables use different names for what appears to be the same concept (`account_id` vs. `account_number` vs. `CIF_NO`). |
| 3 | Is the historisation / SCD pattern (Type 1 overwrite, Type 2 versioning, Type 4 history table) defined per entity type and consistently implemented? | Partially. SCD Type 2 (`effective_from`, `effective_to`, `is_current`) is implemented on 11 of 148 Silver tables — primarily the key analytical entities: `customer.customer`, `account.account_agreement_master`, `loan.loan_account_master`, `loan.loan_transaction`, `loan.loss_given_default`, `loan.account_loss_given_default_detail`, `customer.customer_demographics`, `customer.customer_preferences`, `account.currency_units_conversion_rate`. The remaining ~137 tables (source-specific ODS schemas: finacle, flexcube, erp, fis, prime4) use SCD Type 1 (overwrite) with no history. |
| 4 | Are standard Silver audit / lineage columns present on every table: `silver_created_ts`, `silver_updated_ts`, `source_system_code`, `bronze_batch_id`, `is_current_flag`, `valid_from_ts`, `valid_to_ts`? | Partially. Evidence from the physical model: `source_system_code` is present on ~35 tables, `Create_Date` / `Update_Date` / `Delete_Date` on ~24 tables, `run_id` on ~20 tables, `source_ingestion_date` on ~20 tables. These are not uniformly present across all 148 tables. Column names are inconsistent (`created_date` vs. `Create_Date`). The standard `bronze_batch_id` and `valid_from_ts` / `valid_to_ts` naming is not used — instead `effective_from` / `effective_to` is used for SCD2 tables. |
| 5 | Is data partitioning strategy defined for Silver tables based on typical access patterns (e.g., partition by `account_open_date` for account tables, by `transaction_date` for fact tables)? | No. The physical schema metadata shows no partitioning applied to Silver tables (`partition_index` is null for all rows). No documented partitioning strategy for Silver exists. |
| 6 | Are Delta Lake optimisations applied — Liquid Clustering or Z-ORDER on high-cardinality join keys (e.g., `account_id`, `party_id`) to support efficient Silver-to-Gold queries? | No evidence of Z-ORDER or Liquid Clustering applied to Silver tables in the physical metadata. Not documented or enforced. |
| 7 | Are Silver tables created via automated DDL generation or transformation frameworks (e.g., dbt models) rather than hand-crafted SQL scripts? | No. Silver tables are built via PySpark notebooks. No dbt or equivalent transformation framework is used. DDL is hand-crafted or schema-inferred by Databricks autoloader / Delta Lake. No automated DDL generation from a logical model. |
| 8 | Are test assertions (not-null, unique, referential integrity, accepted values) defined and executed on Silver table outputs as part of the pipeline? | Partially. A `control.dq_logs` table exists, suggesting some data quality logging is in place. A `control.silver_job` and `control.silver_task` framework exists. However, systematic test assertions (not-null, uniqueness, RI checks) are not evidenced as a standard part of every Silver pipeline. The extent and coverage of DQ rules is unknown. |
| 9 | Is there a clear definition of the Silver table grain for each entity (e.g., one row per account per day for account snapshots, one row per transaction for transaction facts)? | Not documented. The `account.account_eod_balance` table implies a daily balance snapshot grain, and `loan.loan_transaction` implies one row per transaction. However, grain is not formally documented per table. The `loan.loan_account_master` contains aggregated fields (MTD/YTD/LTD disbursement amounts) mixed into what should be a contract-level table, violating grain discipline. |
| 10 | Are data type standards enforced — e.g., monetary amounts always as `DECIMAL(18,4)` or `DECIMAL(28,8)`, dates as `DATE`, timestamps as `TIMESTAMP`? | No. Significant inconsistency observed in the physical model: monetary amounts use at least 10 different data types including DECIMAL(28,8), DECIMAL(38,18), DOUBLE, BIGINT, STRING, and INT. The `account_agreement_master.balance` is typed BIGINT (should be DECIMAL). `account_status` and `KYC_status` are typed TIMESTAMP (should be STRING or BOOLEAN). Multiple `amount_tag` columns in Finacle transaction tables are STRING (should be DECIMAL). No type standard is enforced. |
| 11 | Are foreign key relationships between Silver tables documented (even if not enforced by the engine) and validated through data quality checks? | No. Foreign key relationships between Silver tables are not documented in the physical model (Unity Catalog does not enforce FK constraints in Delta Lake). No RI validation checks are evidenced in the pipeline. For example, `account_agreement_master.account_customer_id` → `customer.customer` linkage is implicit but not declared or validated. |
| 12 | Is there a physical model review checklist that is completed before a new Silver table is deployed to production? | No. There is no evidence of a physical model review checklist or approval gate before Silver table deployment. New tables are added through the engineering process without a formal modelling review step. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Naming Convention Documentation | Architecture patterns document (ADO / SharePoint). Schema-level naming follows the defined pattern; column naming is inconsistent (PascalCase in customer schema, snake_case elsewhere). |
| Surrogate Key Strategy | None. Natural source keys used throughout. |
| SCD / Historisation Pattern | SCD Type 2 (`effective_from`, `effective_to`, `is_current`) on 11 analytical tables. SCD Type 1 on all remaining ~137 tables. No formal definition of which entity types use which SCD pattern. |
| Standard Audit Columns List | `source_system_code`, `Source_System_Id`, `Create_Date`, `Update_Date`, `Delete_Date`, `IsActiveFlag`, `run_id`, `source_ingestion_date` — partially applied, inconsistently named. |
| Transformation Framework (e.g., dbt, PySpark notebooks) | PySpark notebooks. No dbt or schema-as-code framework. DDL is hand-crafted or schema-inferred. |
| Known Gaps / Limitations | No surrogate keys; no partitioning; SCD2 on only 7% of tables; multiple data type inconsistencies including status columns stored as TIMESTAMP and amounts stored as STRING/BIGINT; no physical review checklist; no automated DDL generation; grain not documented per table. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Physical Modelling | 2 | Schema structure follows the architecture pattern for high-level naming; table-level design has significant defects (wrong types, mixed naming, no grain documentation). |
| Storage Optimisation | 1 | No partitioning; no clustering; no Delta table property standards applied. |
| Standards Conformance | 1 | Multiple naming styles, inconsistent data types, no enforcement mechanism. Physical model deviates significantly from best practice. |
| Automation Level | 1 | All DDL is hand-crafted or schema-inferred. No dbt, no automated DDL generation, no CI test assertions as standard. |
| **Sub-Area Overall** | 1 | Physical implementation is ad hoc and inconsistent. While some structure exists, the number and severity of physical defects (type errors, missing partitioning, no surrogate keys, SCD2 on 7% of tables) brings this to Initial/Developing. |

---

## 3.4 Semantic / Business Layer

The semantic perspective at Silver covers how business meaning is applied to the integrated data — business-friendly naming, definitions, data dictionaries, and the exposure of cleansed data to self-service consumers.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are all Silver tables and columns documented with business-friendly descriptions in a data catalogue (e.g., Unity Catalog, Collibra, Atlan)? | Partially. Unity Catalog is in use (`silver_dev_v2` catalog). Column-level comments exist for some columns (e.g., `account_agreement_master` has descriptions for ~15 of 67 columns; `loan.loan_account_master` has descriptions on SCD2 columns), but the majority of columns across all 148 Silver tables have null comments. No systematic cataloguing effort has been applied. |
| 2 | Is there a business glossary linked to Silver table attributes — for example, is "Effective Interest Rate" defined in the glossary and linked to the corresponding column(s)? | No. There is no evidence of a business glossary linked to Unity Catalog column attributes. Informatica Cloud is available as a data governance tool but is not integrated with the Silver data platform model. |
| 3 | Are calculated / derived attributes in Silver (e.g., days past due, outstanding balance after partial repayment) documented with their calculation logic? | No. Derived attributes such as `days_past_due_count`, `Bucket` (DPD bucket), `outstanding_principal_aed_amount`, `average_balance`, and `effective_provision_amount` exist in Silver tables but their calculation logic is not documented in the catalogue or in table/column comments. |
| 4 | Are Silver tables classified by sensitivity (PII, confidential, restricted, public) and are access policies enforced based on classification? | No. Data sensitivity classification (PII, Confidential, etc.) is not applied to Silver tables or columns in Unity Catalog. The Silver layer contains highly sensitive PII data (Customer_Full_Name, First_Name, Last_Name, Birth_date, ID_number, Mobile_Number, Address fields) stored in clear text without documented classification or column-level access masking policies. |
| 5 | Is there a domain-based ownership model — i.e., does a business domain team (e.g., Risk, Finance, Retail Banking) own and steward the Silver tables relevant to their domain? | No formal stewardship model. The Data Platform team owns all Silver tables. Business domain teams (Risk, Finance, BBG, WBG) are consumers but do not act as data stewards for specific Silver tables. No steward assignments are visible in Unity Catalog metadata. |
| 6 | Are Silver tables published to a data marketplace or self-service catalogue so that Gold layer developers and analysts can discover and understand available datasets? | No data marketplace exists. Unity Catalog provides technical discoverability (table browsing), but without business descriptions and steward information, it does not function as a self-service catalogue. Consumers rely on tribal knowledge to discover the right Silver tables. |
| 7 | Are data quality scores / completeness metrics published at the Silver table level and visible to consumers? | No. The `control.dq_logs` table suggests DQ results are logged internally, but these are not surfaced as consumer-visible quality scores or data product health indicators. |
| 8 | Are Silver tables versioned with a documented schema change history so that consumers understand what changed between versions? | No. No schema change log or migration history is maintained. Schema changes are applied directly without versioning or change communication to downstream consumers. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Catalogue Tool | Unity Catalog (`silver_dev_v2` catalog) — used for technical metadata; business metadata sparse |
| Business Glossary Tool / Location | Informatica Cloud (available but not integrated with data platform Silver layer) |
| Data Classification Status | Not applied. PII data present in Silver without classification or column masking. |
| Domain Ownership Model | Absent. Data Platform team owns all Silver assets. No business domain stewards assigned. |
| Known Gaps / Limitations | Column-level descriptions sparse; no business glossary linkage; no data classification; no self-service catalogue; no stewardship; DQ scores not visible to consumers; no schema change history. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Semantic / Business Layer | 1 | Unity Catalog exists but column comments are sparse and no business glossary is linked. Effectively absent from a business-consumer perspective. |
| Discoverability | 1 | No data marketplace or self-service catalogue. Consumer access relies on tribal knowledge and direct Databricks access. |
| Stewardship | 1 | No domain stewards assigned; no data classification; PII unmasked in Silver. |
| **Sub-Area Overall** | 1 | Semantic layer is at the Initial level. The tooling (Unity Catalog, Informatica) exists but is not operationalised for business-facing metadata. |

---

## 3.5 Banking Domain Completeness – Foundational Subject Areas

This sub-area directly assesses whether the Silver layer provides the foundational banking datasets required to support core reporting and operations.

### Questions

| # | Foundational Subject Area | Is it modelled in Silver? | Coverage Level (Full / Partial / Absent) | Gap Description |
|---|---------------------------|--------------------------|------------------------------------------|-----------------|
| 1 | Party / Customer (individual & corporate) | Yes | Partial | Single flat table combining individual and corporate; no sub-type hierarchy; no MDM/golden record; KYC status incorrectly typed. Logical model defines 31 entities, only ~6 physically implemented. |
| 2 | Account (all account types) | Yes | Partial | Account types implemented in separate unrelated tables (CASA in `account_agreement_master`, cards in `credit_card_account`, loans in `loan_account_master`, term deposits in multiple system-specific tables). No unified Account entity or typed hierarchy. Trade finance accounts in eximbills schema. |
| 3 | Product Catalogue | No | Absent | Only `reference_product` lookup tables in individual schemas. No unified product catalogue, product hierarchy, or product parameter history. The logical model defines a Product subject area with 0 physical entities implemented. |
| 4 | Financial Transaction | Yes | Partial | Implemented as system-specific tables (Finacle CASA, Loan, TD; Flexcube Loan, TD; FIS; Prime4). No unified transaction model or common transaction type taxonomy. Amount columns stored as STRING in some Finacle tables. |
| 5 | Loan / Credit Contract & Facility | Yes | Partial | `loan.loan_account_master` covers retail/SME loans well. Overdraft (`finacle_oda_contract`), CASA (`finacle_casa_contract`), and trade finance (`eximbills.*`) are in source-specific schemas without unification. No cross-product facility view. |
| 6 | Reference Data (currency, country, GL codes) | Yes | Partial | Multiple reference tables per schema (reference_currency_code, reference_country, reference_product, reference_branch_code). Not centralised — each domain schema maintains its own copies, creating potential inconsistency. `ref_data` schema has only 2 tables. |
| 7 | Channel / Interaction Data | Yes | Partial | `channel_id` in account table; Electronic Channel Transaction subject area defined in logical model with 47 entities. Physical implementation limited — no unified channel event or interaction history table evident in the physical model. |
| 8 | General Ledger / Finance Posting | Yes | Partial | `finacle.finacle_trial_balance` exists. GL subject area in logical model (7 entities: GL_MASTER, GL_TRANSACTIONS, GL_ADJUSTMENTS, etc.). The `erp.finacle_trial_balance` schema suggests GL data is present but not modelled in the analytical Silver layer. |
| 9 | Collateral | Yes | Partial | `loan.loss_given_default` and `loan.account_loss_given_default_detail` provide LGD/LTV at facility level. Logical model defines a Customer Collateral Asset subject area (12 entities for different collateral types). No full collateral registry or valuation history physically implemented. |
| 10 | Employee (for internal fraud / access risk) | No | Absent | No employee or HR data tables in the Silver physical model. No internal fraud or access risk dataset present. |

### Additional Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are relationships between all foundational subject areas defined, documented, and tested (e.g., Account → Party → Product → Transaction)? | Not formally defined or tested. Key linkages exist as columns (e.g., `account_customer_id` in `account_agreement_master`, `CIF_NO` in `loan_account_master`) but these are natural keys, not surrogate keys, and are not validated through RI checks. The relationship between Account and Product is implicit via `scheme_code` / `product_id` only. No RI test suite across subject area boundaries. |
| 2 | Is the Silver layer capable of supporting regulatory reporting that requires combining multiple subject areas (e.g., a CBUAE credit register report combining Party, Account, Contract, Collateral, and Risk)? | Partially. A CBUAE credit register requiring Party + Account + Contract + Collateral can be assembled from existing Silver tables, but it would require significant manual join logic against multiple disparate schemas and denormalised structures. The absence of a unified Account model, unified Product model, and formal collateral registry makes this complex and brittle. Risk attributes (PD, formal ECL) needed for full CBUAE credit register are absent. |
| 3 | Is there a maturity roadmap for extending foundational subject areas with additional attributes (e.g., adding behavioural scoring to Party, adding IFRS 9 staging to Contract)? | No formal roadmap published. Risk – Market and Risk – Liquidity are noted as planned. The logical model (`logical_silver.xlsx`) defines a comprehensive target state (400+ entities across 14 subject areas) but there is no prioritised phased delivery plan for implementing the logical model physically. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Foundational Subject Area Coverage | 2 | Most subject areas have some physical tables, but coverage is partial and fragmented. Product and Employee/HR are absent. No unified models for Party, Account, or Transaction. |
| Relationship Completeness | 1 | Inter-entity relationships are implicit natural-key references; no formal declaration, documentation, or RI validation. |
| Regulatory Readiness | 2 | Data for a CBUAE credit register can be approximately assembled but requires complex joins across fragmented schemas; critical risk attributes (PD, formal IFRS9 staging) are missing. |
| **Sub-Area Overall** | 2 | Foundational coverage exists at a surface level but the fragmentation, lack of unification, and missing Product/HR/collateral models represent significant gaps for a banking data platform. |

---

## 3.6 Banking Domain Completeness – Risk & Regulatory Datasets

This sub-area assesses whether Silver contains the risk-relevant datasets needed to support Gold-layer risk reporting (credit, market, liquidity, operational, regulatory).

### Questions

| # | Risk / Regulatory Subject Area | Is it modelled in Silver? | Coverage Level | Gap Description |
|---|-------------------------------|--------------------------|----------------|-----------------|
| 1 | Credit Risk – Obligor risk rating (PD, internal grade) | Partial | Partial | `customer.customer` has `Credit_Grading_Code`, `Counterparty_Rating`, `Risk_Profile`. No Probability of Default (PD) column or PD history table. No internal rating grade per asset class. `customer.customer_demographics.STATUS_CHANGE_DATE` typed incorrectly as TIMESTAMP. |
| 2 | Credit Risk – Facility exposure (EAD, outstanding, limit) | Yes | Partial | `loan.loan_account_master` has `account_currency_commitment_amount` (limit), `account_currency_available_limit`, `outstanding_principal_aed_amount`, `outstanding_principal_amount`. No formal EAD column using regulatory definition. Off-balance sheet / contingent exposure (e.g., guarantees in eximbills) not consolidated into a unified exposure view. |
| 3 | Credit Risk – ECL / Impairment (IFRS 9 Stage, PD, LGD, ECL provision) | Partial | Partial | `loan.loan_account_master` has `Provision_Amount`, `effective_provision_amount`, `interest_suspense_amount`, `NPA_Stage_Identifier`. LGD proxied via `blended_ltv_amount` and `cash_collateral_value`. No explicit IFRS 9 Stage 1/2/3 flag. No PD input for ECL calculation. ECL = PD × LGD × EAD calculation is not modelled. Significant Increase in Credit Risk (SICR) triggers absent. |
| 4 | Credit Risk – Non-performing loan (NPL) classification | Yes | Partial | `loan.loan_account_master` has `npl_flag`, `NPA_Stage_Identifier`, `Bucket` (DPD bucket), `days_past_due_count`, `Collection_status_description`, `account_collection_status_code`. Bucket history (times at 10-29, 30-59, 60-89, 90+ days) is present. However, NPL classification by CBUAE regulatory definition is not documented; `NPA_Stage_Identifier` semantics are not clarified in the catalogue. |
| 5 | Collateral – Valuation and linkage to facility | Yes | Partial | `loan.loss_given_default` has `collateral_property_amount_aed` and `collateral_shares_amount_aed` at obligor (CIF) level. `loan.account_loss_given_default_detail` has `loan_to_value_percentage` at facility level. `loan.loan_account_master` has `cash_collateral_value`. No full collateral registry with valuation history, collateral type classification, or many-to-many collateral-to-facility linkage table. |
| 6 | Liquidity Risk – Contractual cash flows by maturity bucket | No | Absent | No liquidity risk tables in the physical Silver model. No contractual maturity cash flow data or maturity gap analysis. `UAECB_LIQUIDITY` appears in logical model only (Bank Asset subject area) — not physically implemented. |
| 7 | Liquidity Risk – HQLA buffer assets | No | Absent | No High-Quality Liquid Assets (HQLA) table in the physical Silver model. Cannot support LCR or NSFR reporting from Silver. |
| 8 | Market Risk – Trading and banking book positions | No | Absent | No market risk or treasury position tables visible in the Silver physical model. Investment and trading activity appears in the logical model (Investment subject area, 31 entities) but has no physical implementation. |
| 9 | Market Risk – MTM valuations and sensitivity | No | Absent | No mark-to-market valuation, sensitivity (DV01, CS01), or VaR input tables in the physical Silver model. |
| 10 | Operational Risk – Loss events and near-misses | No | Absent | No operational risk dataset in the physical Silver model. The logical model defines an extensive ORM subject area (39 entities including Basel II risk event classification, KRI, loss data collection) but none are physically implemented. |
| 11 | AML / Fraud – Transaction monitoring flags and case data | No | Absent | Transaction-level AML/fraud flagging is absent. Party-level flags exist (`Blacklisted`, `Overall_Blacklisted_Status`, `Negated` in `customer.customer`) but no transaction monitoring output table, suspicious activity case data, or AML alert dataset is present in Silver. |
| 12 | Regulatory – CBUAE reporting entities and classifications | Yes | Partial | `account_agreement_master` has `regulatory_body`. `loan.loan_account_master` has `regulators_class_code`. `UAECB_ASSET_CODES` in logical model. `reference_country` and other reference tables support regulatory lookups. No unified CBUAE regulatory classification view combining all product types. |
| 13 | Regulatory – Large exposure data (connected counterparty groups) | No | Absent | No counterparty group or connected-party hierarchy table in the Silver physical model. `corporate_ID`, `Group_Code`, `Group_Name`, `Primary_Parent_Company` fields exist in `customer.customer` but are not structured for large exposure aggregation. No connected-party data model for CBUAE large exposure reporting. |

### Additional Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are risk datasets in Silver sourced from authoritative risk systems (e.g., a dedicated credit risk engine, a treasury system) and properly integrated with the core Party/Account/Contract model? | Partially. Credit risk data (`loss_given_default`, `loan_account_master` provisions) appears sourced from Finacle (core banking) rather than a dedicated risk engine. The `model_id`, `model_run_id`, `model_run_date_time` columns in `loan.loan_account_master` suggest some risk model outputs are loaded, but the source risk system and model governance are not documented. Linkage to the Party model uses `CIF_NO` (natural key) which is consistent. |
| 2 | Are IFRS 9 staging attributes (Stage 1, 2, 3 classification triggers) modelled in Silver at the instrument level and historised to support point-in-time regulatory reporting? | No. There is no IFRS 9 Stage 1/2/3 explicit classification column in any Silver table. `NPA_Stage_Identifier` exists but its mapping to IFRS 9 stages is unclear and not documented. SICR triggers (e.g., 30 DPD threshold, credit grade migration) are not captured as structured attributes. While SCD2 is applied to `loan.loan_account_master`, enabling point-in-time queries, the IFRS 9 stage logic itself is not present. |
| 3 | Is there a single definition of "exposure" in Silver that is consistently used across credit risk, large exposure, and regulatory reporting (avoiding different exposure calculations in different Gold models)? | No. Multiple exposure proxies exist across tables: `account_currency_commitment_amount`, `outstanding_principal_aed_amount`, `account_currency_available_limit` in loan tables; off-balance sheet items in eximbills. No canonical EAD definition or exposure measure that unifies these for regulatory purposes. |
| 4 | Are counterparty linkages (parent-subsidiary, connected parties) modelled to support large exposure aggregation required by CBUAE regulations? | No. While `Group_Code`, `Corporate_ID`, and `Primary_Parent_Company` fields exist in the customer table, there is no formal connected-party group hierarchy model (e.g., a parent-child table or graph model) that enables CBUAE large exposure aggregation across a counterparty group. |
| 5 | Is the Silver risk model granular enough to support Basel III / CBUAE Pillar 3 disclosures — can exposure be sliced by asset class, geography, industry, and product? | Partially. The `loan.loan_account_master` has `segment_code`, `Industry_Description`, `Nationality`, `Residential_NonResidential_Flag`, `Constitution` which support some dimensional slicing. However, formal Basel asset class classification (Sovereigns, Banks, Corporates, Retail, etc.) is not standardised as a column. Geography slicing is limited to branch/nationality fields, not a formal country-of-risk model. |
| 6 | Is there a liquidity gap analysis dataset in Silver (contractual maturities bucketed per the standard time bands) that can directly feed an LCR or NSFR calculation in Gold? | No. This capability is entirely absent from the current Silver physical model. |
| 7 | Is there a written data lineage or mapping document tracing each risk metric in Gold back to its Silver source attributes? | No. No such lineage document exists. Databricks Unity Catalog lineage tracks table-level lineage from pipelines, but column-level lineage for risk metrics is not documented. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Credit Risk Source System(s) | Finacle (core banking — loans, provisions, DPD), KRM (possible risk system — `krm.dbo_port_rak`, `krm.dbo_tp_report_txn` visible), RLS schema present |
| Liquidity Risk Source System(s) | Not currently ingested into Silver |
| Market Risk Source System(s) | Not currently ingested into Silver |
| Operational Risk Source System(s) | Not currently ingested into Silver |
| Regulatory Reporting Source System(s) | Finacle (core banking), UAECB codes in logical model |
| Known Gaps / Limitations | PD model absent; formal IFRS 9 stage attributes absent; no ECL = PD × LGD × EAD model; liquidity, market risk, and OpRisk datasets entirely absent; no connected-party model for large exposure; AML transaction monitoring absent; CBUAE regulatory classification incomplete. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Credit Risk Dataset Completeness | 2 | NPL flag, provision amounts, LTV/LGD present but PD, formal ECL, and IFRS 9 stage classification absent. |
| Liquidity Risk Dataset Completeness | 1 | Entirely absent from physical Silver. |
| Market Risk Dataset Completeness | 1 | Entirely absent from physical Silver. |
| Operational Risk Dataset Completeness | 1 | Entirely absent from physical Silver. |
| Regulatory Dataset Completeness | 2 | Some regulatory codes and classifications present but no unified CBUAE view, no large exposure model, no capital adequacy inputs. |
| **Sub-Area Overall** | 1 | Only credit risk has any meaningful presence, and even that is incomplete. Liquidity, market, and operational risk datasets are entirely absent — making the Silver layer insufficient to support the risk reporting goals from Gold without significant additional development. |

---

## 3.7 Silver Best Practices

This sub-area checks adherence to widely accepted Silver-layer best practices in a banking lakehouse context.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the Silver layer free from business metric calculations and aggregations — i.e., are those deliberately deferred to Gold? | No. `loan.loan_account_master` contains pre-aggregated fields (`MTD_disbursement`, `YTD_disbursement`, `LTD_disbursement`, `interest_paid_itd`, `principal_paid_itd`, `average_balance`) that represent period aggregations. These aggregated values embedded in a contract-level Silver table blur the Silver/Gold boundary and make point-in-time restatement more complex. |
| 2 | Is there exactly one authoritative Silver table per business entity (e.g., one `silver.customer.party` table), or do multiple tables exist for the same entity leading to inconsistency? | No. Multiple overlapping tables exist for the same entities: Loan contracts are in `finacle.finacle_loan_contract`, `flexcube.flexcube_loan_contract`, `erp.finacle_loan_contract` and `loan.loan_account_master`. Term deposit contracts are in `finacle.finacle_td_contract`, `flexcube.flexcube_td_contract`, `prime4.prime_td_contract`, `deposits.deposit_term_account`. CASA contracts are in `finacle.finacle_casa_contract`, `erp.finacle_casa_contract`, `casa.finacle_contract`. Consumers cannot easily identify the authoritative table for a given entity. |
| 3 | Are data quality rules (not-null, uniqueness, referential integrity, range checks, domain value checks) enforced and logged at the Silver loading step? | Partially. The `control.dq_logs` table exists, suggesting some DQ rule results are logged. A `control.silver_job` and `control.silver_task` orchestration framework is in place. However, the coverage, rule definitions, and failure-handling process are not documented. Critical data type issues (`account_status` as TIMESTAMP, `balance` as BIGINT, amount columns as STRING) suggest that type-level validation is not enforced. |
| 4 | Is there a reconciliation process validating that record counts and key financial totals (e.g., sum of outstanding balances) in Silver match the source system or Bronze? | No formal reconciliation process exists. There is no documented process or automated check comparing Silver record counts or financial totals against Bronze or source system control totals. |
| 5 | Is PII masked, tokenised, or encrypted in Silver tables for columns that do not need to be human-readable for analytical purposes (e.g., account number hashing, name masking in non-production environments)? | No. The `customer.customer` table stores `Customer_Full_Name`, `First_Name`, `Last_Name`, `Birth_date`, `ID_number`, `Mobile_Number`, `Phone_Number`, `Address_Line1/2/3` in clear text. No evidence of PII masking or tokenisation in Silver. The `delete_flag` and `Delete_Date` columns suggest logical deletion is handled, but PII protection is not. |
| 6 | Is the Silver layer designed to be replayable — i.e., can it be fully reconstructed from Bronze without any data loss if a Silver table is corrupted or needs to be rebuilt? | Partially. Tables with SCD Type 2 (`effective_from`/`effective_to`/`is_current`) could theoretically be rebuilt from Bronze if Bronze is append-only and retains history. However, SCD Type 1 tables (the majority) overwrite data, meaning that if the Bronze source also uses overwrite patterns, historical Silver state cannot be fully reconstructed. No documented replay/rebuild procedure exists. |
| 7 | Is there a clear policy on whether Silver tables can be read directly by analysts / BI tools, or whether all consumer access must go via Gold? Is this enforced? | No defined policy. The architecture document describes Silver as serving both ODS (operational) and analytical consumers, implying direct Silver access is intended. However, no access control policy distinguishes Silver consumer types, and no enforcement mechanism (e.g., Unity Catalog GRANT policies, Row-Level Security) is documented or applied. |
| 8 | Are late-arriving records (e.g., backdated transactions, retroactive corrections) handled explicitly in Silver with a defined policy for how history is corrected? | No. No late-arriving data policy is documented. SCD Type 2 tables could theoretically handle corrections by updating the `effective_to` date of historical records and inserting new versions, but no process document exists for this. |
| 9 | Is there an established change-data-management process so that when a Silver table schema changes, downstream Gold models are notified and updated in a controlled manner? | No. Schema changes are made directly without a documented change management process or downstream notification mechanism. No data contract or schema registry enforces compatibility between Silver and Gold consumers. |
| 10 | Is the Silver model aligned with any industry standard data models (e.g., BIAN — Banking Industry Architecture Network, IFW — IBM Financial Data Model, FIBO — Financial Industry Business Ontology)? If not, is alignment planned? | No documented alignment. The logical model (`logical_silver.xlsx`) appears to be a custom model developed for RAKBANK. References to CBUAE, Basel III, and IFRS 9 regulatory requirements are embedded, but no alignment to BIAN, IFW, or FIBO is stated. No alignment plan is documented. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Data Quality Rule Engine | `control.dq_logs` table (custom logging framework); coverage and rule definitions not documented |
| Reconciliation Process | Absent. No automated Silver-to-source reconciliation. |
| PII Masking / Tokenisation Tool | Absent. No PII masking applied in Silver. Raw PII data stored in clear text. |
| Industry Model Alignment Status | No alignment to BIAN, IFW, or FIBO. Custom model. |
| Known Gaps / Limitations | Aggregated metrics in Silver blur the Silver/Gold boundary; multiple tables for the same entity create ambiguity; no PII masking; no reconciliation; no replay procedure; no late-data policy; no schema change management process; no industry model alignment. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Best Practices Adherence | 1 | Multiple Silver best practice violations: aggregations in Silver, multiple tables for same entity, no authoritative-source designation, no late-data policy. |
| Data Quality Enforcement | 2 | DQ logging framework exists but coverage is unknown and critical type errors exist in production tables. |
| Security & Privacy | 1 | PII stored in clear text with no masking, tokenisation, or column-level access policies. Data classification absent. |
| Replayability | 2 | SCD2 tables (11 of 148) could be replayed from Bronze; SCD1 tables (majority) may not be. No documented procedure. |
| **Sub-Area Overall** | 1 | Silver best practices are at the Initial level. The most critical gaps are the absence of PII protection, multiple competing tables for the same entity, and embedded aggregations that blur the Silver/Gold boundary. |
