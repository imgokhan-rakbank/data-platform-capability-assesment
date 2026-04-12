# Domain 1: Medallion Architecture Governance – Model Review Assessment

> **Assessor(s):** Gokhan Imral
> **Date:** 12.04.2026
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 1.1 Architecture Conformance

Architecture conformance assesses whether the three-zone Medallion Architecture (Bronze / Silver / Gold) is consistently applied across all data domains and pipelines — and whether the purpose of each zone is understood and respected by all teams.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there an official, documented architecture decision record (ADR) or design document that defines the Medallion Architecture and its rationale for this platform? | Yes (architecture_patterns.txt) |
| 2 | Are the boundaries between Bronze, Silver, and Gold clearly defined in writing, and are those definitions communicated to all data engineers and analysts? | They are defined (architecture_patterns.txt) but not communicated |
| 3 | Is there any data that bypasses a layer (e.g., raw data directly consumed at Gold, or transformations applied in Bronze)? If yes, how is this governed? | Yes it exists, there is no governance on it. |
| 4 | How is architecture conformance enforced — are there automated checks, pull-request gates, or peer-review checklists that prevent non-conforming models from being deployed? | Arhitecture conformance is not checked |
| 5 | Are there documented data contracts between layers (e.g., Bronze → Silver, Silver → Gold) specifying expected schemas, SLAs, and quality thresholds? | No |
| 6 | How are cross-cutting concerns (audit columns, metadata stamps, soft-delete patterns) applied consistently across all layers? | They are defined in architecture documents but no enforcement exists, no system level checks are there |
| 7 | Has the architecture been reviewed externally or benchmarked against industry references (e.g., Databricks best practices, DAMA, vendor blueprints)? | Databrciks is going to be doing a review in the coming weeks. |
| 8 | Is there a forum or process (e.g., architecture review board) where proposed changes to the medallion model are evaluated before implementation? | Yes there is ARD |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Databricks, Erwin, Informatica |
| Architecture Documentation Location | ADO |
| Enforcement Mechanism | None |
| Known Gaps / Limitations | There are no controls on what is being implemented, no gating exits |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Clarity | 2 | Some definitions exist, but not communicated or enforced. |
| Standards Conformance | 1 | No enforcement, no data contracts, and no governance on bypasses. |
| Tooling / Enforcement | 1 | No automated checks or enforcement mechanisms. |
| People / Awareness | 2 | Some awareness (ARD exists), but not communicated or practiced. |
| **Sub-Area Overall** | 1 | Majority of practices are at the "Initial" level. |

---

## 1.2 Layer Responsibility Definition

This sub-area examines whether each layer has a precisely defined, agreed-upon responsibility — ensuring no duplication of transformation logic and no ambiguity about where a given concern lives.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the Bronze layer formally defined as a raw, unaltered landing zone? Is there any transformation applied there beyond technical ingestion concerns (e.g., encoding fixes, file parsing)? | Is is defined as raw, no real tranformation occurs other than matching to delta lake format |
| 2 | Is there a written definition of what "Silver" means on this platform — specifically which transformations, cleansing steps, and business rules are applied at Silver versus left to Gold? | There is basic architecture document but nothing is enforced. |
| 3 | Is the Gold layer reserved exclusively for consumption-ready, aggregated, or domain-specific models, and is this definition enforced? | There is no enforcement |
| 4 | Are there documented rules about which layer owns master data / reference data, and how it propagates across layers? | There is basic architecture document but nothing is enforced. |
| 5 | When a new subject area is onboarded, is there a checklist or template that prescribes what models are needed at each layer? | No |
| 6 | Is data duplication across layers intentional and documented (e.g., a table replicated from Silver to Gold for performance), or is it accidental? | No documentation |
| 7 | How are late-arriving data, restatements, and corrections handled — and is the handling policy documented per layer? |No documentation or process exists |
| 8 | Are there clearly defined owners (teams or individuals) for each layer, and are those owners accountable for model quality and SLAs? | There are owners but nothing is clear |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Documentation / Playbook Location | ADO |
| Owner of Bronze | Data platform |
| Owner of Silver | Data platform|
| Owner of Gold | Use case platform|
| Known Gaps / Limitations | Documentation is basic, not communicated well, nothing is enforced|

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Clarity | 2 | Basic definitions exist, but not enforced or communicated. |
| Documentation Quality | 2 | Documentation is basic, not communicated, and not enforced. |
| Ownership & Accountability | 2 | Owners exist, but roles and accountability are unclear. |
| **Sub-Area Overall** | 2 | Some structure, but mostly "Developing" maturity. |

---

## 1.3 Cross-Layer Design Principles

This sub-area evaluates whether shared design principles — such as key design, surrogate key strategy, historisation patterns, and audit columns — are coherently defined and uniformly applied across all three layers.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a standard surrogate key strategy defined (e.g., hash keys, identity columns, UUIDs) and consistently applied across Silver and Gold? | It is not documented |
| 2 | How is historisation / slowly changing dimension (SCD) management handled — is there a defined pattern (SCD Type 1/2/4, temporal tables) and is it consistent? | SCD2 is expected from silver, but not enforced |
| 3 | Are standard audit / metadata columns (e.g., `record_created_ts`, `record_updated_ts`, `source_system_code`, `batch_id`, `is_deleted`) mandated and present in all tables? | It i documented in architecture but not enforced at system level |
| 4 | Is there a defined convention for partition keys across layers, and is it aligned with the most common query patterns? | It is not defined |
| 5 | Are deleted / logically removed records handled consistently (soft delete vs. hard delete) and documented across all layers? | It is not defined |
| 6 | How are timezone, date/time, and currency representation standards defined and enforced across models? | It is not defined |
| 7 | Is there a cross-layer primary key traceability strategy — can a Gold record be traced back through Silver to its Bronze origin? | It is not defined. Databrciks lineage can be helpfull. |
| 8 | Are null handling policies defined and applied consistently (e.g., default values vs. nulls for missing data, sentinel values)? | It is not defined |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Surrogate Key Strategy | Not defined. Natural / source system keys are carried through all layers (e.g., `account_id`, `CIF`, `account_number`). No platform-wide hash or UUID surrogate key strategy exists. |
| Historisation Pattern | SCD Type 2 (`effective_from`, `effective_to`, `is_current`) is applied to analytical Silver tables (e.g., `customer.customer`, `account.account_agreement_master`, `loan.loan_account_master`). Silver ODS tables follow SCD Type 1 (overwrite). However, SCD2 is only present on ~11 of 148 Silver tables (~7% coverage). Source-specific schemas (finacle, flexcube, fis, prime4) have no historisation. |
| Standard Audit Columns List | Partially defined. Observed columns: `source_system_code` (~35 tables), `Source_System_Id` (~35 tables), `Create_Date` / `Update_Date` / `Delete_Date` (~24 tables), `run_id` (~20 tables), `source_ingestion_date` (~20 tables), `IsActiveFlag` (~24 tables). Not consistently applied across all 148 tables. Column naming varies (`created_date` vs `Create_Date`). |
| Known Gaps / Limitations | No surrogate key strategy; SCD2 coverage is ~7% of tables; audit columns inconsistently applied; no timezone standard (timestamps in unknown timezone); no null/sentinel value policy; no partition key convention; no cross-layer PK traceability strategy beyond Databricks Unity Catalog lineage. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Clarity | 1 | No standard strategies or conventions are defined or documented at a level sufficient for enforcement. |
| Logical Design Consistency | 1 | SCD2 partially applied (~7% of tables); no surrogate key strategy; audit columns inconsistently present. |
| Physical Implementation | 1 | No enforcement or automated checks; significant inconsistency across 148 Silver tables. |
| Standards Conformance | 1 | No standards are formally defined or enforced. Evidence from the physical model shows multiple naming styles, type inconsistencies, and missing historisation. |
| **Sub-Area Overall** | 1 | Practices are at the "Initial" level. Architecture patterns document exists but is not operationalised. |
