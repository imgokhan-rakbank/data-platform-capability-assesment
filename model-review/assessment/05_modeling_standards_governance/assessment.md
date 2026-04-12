# Domain 5: Modelling Standards & Governance – Model Review Assessment

> **Assessor(s):**
> **Date:**
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

Modelling standards and governance are the backbone of a scalable, maintainable data model. Without enforced standards, the Medallion model will accumulate technical debt, inconsistencies, and shadow models. This domain assesses whether naming conventions, type standards, key design rules, documentation requirements, and change governance are defined, communicated, and enforced across all three Medallion layers.

---

## 5.1 Naming Conventions

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a written naming convention standard that covers: database/schema names, table names, column names, view names, and temporary/staging objects — for all three layers (Bronze, Silver, Gold)? | |
| 2 | Does the naming convention prescribe the layer prefix or namespace (e.g., `bronze.`, `silver.`, `gold.`) — and is this consistently applied so that the layer of any object is immediately apparent from its name alone? | |
| 3 | Are there rules for abbreviations — i.e., a standard abbreviation dictionary that prevents different engineers from abbreviating the same word differently (e.g., `amt` vs. `amount` vs. `amnt`)? | |
| 4 | Is snake_case enforced as the column naming style across all layers? Are mixed cases (camelCase, PascalCase) present in the current model? | |
| 5 | Are boolean column names prefixed with `is_` or `has_` (e.g., `is_active`, `has_collateral`) consistently? | |
| 6 | Are date columns suffixed with `_date`, timestamp columns with `_ts` or `_timestamp`, and amount columns with `_amount` or `_amt` — and is this applied uniformly? | |
| 7 | Are primary key columns named consistently across entities (e.g., `<entity>_id` or `<entity>_key` throughout)? | |
| 8 | Are foreign key columns named after the referenced entity and key (e.g., `party_id` in an account table referencing the party table)? | |
| 9 | Is there an automated linter or CI check that validates naming conventions before code is merged? | |
| 10 | What percentage of existing Silver and Gold tables are compliant with the current naming standard — has a compliance audit been done? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Naming Convention Documentation Location | |
| Abbreviation Dictionary Location | |
| Automated Linting / Enforcement Tool | |
| Compliance Rate (estimated) | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Standards Definition | | |
| Standards Communication | | |
| Enforcement / Automation | | |
| **Sub-Area Overall** | | |

---

## 5.2 Data Type & Format Standards

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are data type standards documented for common banking data types? For each of the following, state the mandated type and whether it is enforced: | |
|   | – Monetary amounts (e.g., `DECIMAL(28,8)` or `DECIMAL(18,4)`) | |
|   | – Interest rates / percentages | |
|   | – Dates (e.g., `DATE`) | |
|   | – Timestamps (e.g., `TIMESTAMP` / timezone handling) | |
|   | – Identifiers / IDs (e.g., `STRING` / `BIGINT`) | |
|   | – Boolean flags | |
|   | – Free-text / description fields (max length) | |
|   | – Currency codes (e.g., `CHAR(3)` ISO 4217) | |
|   | – Country codes (e.g., `CHAR(2)` ISO 3166) | |
| 2 | Is there a standard for NULL handling — are NULLs permitted for optional attributes, and are mandatory attributes validated as NOT NULL in the pipeline? | |
| 3 | Are sentinel / placeholder values (e.g., `9999-12-31` for open-ended dates, `0` for unknown numeric values) defined and documented, or is NULL used for all unknown cases? | |
| 4 | Is timezone handling standardised — are all timestamps stored in UTC, and is the local timezone conversion handled at the semantic/Gold layer? | |
| 5 | Are currency conversion standards defined — is there a single FX rate table in Silver, and are multi-currency amounts always stored in both original currency and a reporting currency equivalent? | |
| 6 | Are there automated data quality checks validating type conformance and range constraints in pipeline tests? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Data Type Standards Documentation | |
| NULL / Sentinel Value Policy | |
| Currency / FX Standard | |
| Timezone Standard | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Standards Definition | | |
| Standards Communication | | |
| Enforcement / Automation | | |
| **Sub-Area Overall** | | |

---

## 5.3 Key & Relationship Standards

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a documented primary key strategy for each layer — e.g., Bronze retains source natural keys; Silver uses hash surrogate keys; Gold uses Silver surrogate keys or composite keys? | |
| 2 | Is the surrogate key generation method standardised (e.g., `SHA2` hash of a defined set of business key columns)? Are the business key columns used in the hash documented per entity? | |
| 3 | Are referential integrity relationships documented even if not enforced by the engine — e.g., `silver.account.party_id` references `silver.party.party_id`? | |
| 4 | Are referential integrity violations detected by data quality checks and reported rather than silently ignored? | |
| 5 | Is there a standard for composite keys — when are composite keys acceptable vs. when must a surrogate key be used? | |
| 6 | Are natural / business keys preserved as separate columns (`source_account_id`, `source_party_id`) alongside surrogate keys to support traceability back to source systems? | |
| 7 | Is there a standard for how unique constraints are validated (e.g., dbt `unique` test, Great Expectations uniqueness check) and at what cadence? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Primary Key Strategy Documentation | |
| Surrogate Key Generation Method | |
| Referential Integrity Check Tool | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Standards Definition | | |
| Surrogate Key Consistency | | |
| RI Validation | | |
| **Sub-Area Overall** | | |

---

## 5.4 Model Documentation & Versioning

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a mandatory documentation standard for data models — specifying what must be documented before a model is deployed to production (table description, column descriptions, grain, owner, source systems)? | |
| 2 | Are data models version-controlled alongside transformation code (e.g., DDL scripts or dbt models in Git)? | |
| 3 | Is there a model versioning scheme — e.g., v1, v2 of a table when breaking changes are made — and is backward compatibility handled explicitly? | |
| 4 | Are entity-relationship (ER) diagrams maintained and kept in sync with the physical implementation, or are diagrams created once and then abandoned? | |
| 5 | Is there a schema change log or migration history (e.g., dbt model changelog, Liquibase / Flyway scripts) that records every structural change to each table over time? | |
| 6 | Is model documentation auto-generated from code annotations (e.g., dbt `description` blocks, Unity Catalog column comments) rather than maintained in a separate document that can drift out of sync? | |
| 7 | Are deprecated tables and columns clearly marked as deprecated in the catalogue and documentation, with a removal date communicated to consumers? | |
| 8 | Is there a completeness metric for documentation — i.e., what percentage of Silver and Gold tables have descriptions and column-level documentation populated? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Documentation Standard Location | |
| ER Diagram Tool | |
| Schema Migration / Versioning Tool | |
| Documentation Completeness Rate (estimated) | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Documentation Standards | | |
| Version Control | | |
| Documentation Completeness | | |
| **Sub-Area Overall** | | |

---

## 5.5 Model Review & Approval Process

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined model review process — i.e., must a data model be reviewed and approved by a data architect or modelling council before it can be deployed to production? | |
| 2 | Is there a model review checklist that reviewers must complete — covering naming conventions, data types, key design, documentation, test coverage, and security classifications? | |
| 3 | Are model reviews conducted as pull-request reviews in Git, ensuring an audit trail of what was reviewed, by whom, and when? | |
| 4 | Is there an architecture review board (ARB) or data modelling guild that governs significant model changes (new subject areas, breaking changes, cross-domain impacts)? | |
| 5 | Are data consumers (business users, risk analysts, finance team) consulted during model design to validate that the model meets their reporting needs before it is finalised? | |
| 6 | Is there a policy for managing breaking schema changes — e.g., a minimum notice period, a parallel-run period where both old and new versions coexist? | |
| 7 | Are data stewards involved in the approval of Silver model additions to ensure business definitions are correct before the model is used in Gold reports? | |
| 8 | Is model compliance (adherence to standards) measured periodically — e.g., an annual model audit or a continuous compliance score tracked in a dashboard? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Review / Approval Process Documentation | |
| Model Review Checklist Location | |
| ARB / Modelling Guild Status | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Definition | | |
| Governance Rigor | | |
| Stakeholder Involvement | | |
| **Sub-Area Overall** | | |
