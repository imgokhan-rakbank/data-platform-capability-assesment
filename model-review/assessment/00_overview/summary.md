# Data Model Review – Consolidated Summary

> **Instructions:** Once all domain assessments are complete, transfer the Sub-Area Overall scores here.  
> Calculate the Domain Score as the average of its sub-area scores (round to 1 decimal place).  
> The Overall Model Maturity Score is the average of all domain scores.

---

## Domain Score Rollup

### 1. Medallion Architecture Governance

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Architecture Conformance | 1 | ADR exists; boundaries not enforced; no data contracts; data bypasses layers without governance. |
| Layer Responsibility Definition | 2 | Basic definitions exist; owners assigned but accountability unclear; no templates or checklists. |
| Cross-Layer Design Principles | 1 | No surrogate key strategy; SCD2 on ~7% of Silver tables; audit columns inconsistent; no timezone/null standards. |
| **Domain Score** | **1.3** | |

---

### 2. Bronze Layer

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Conceptual Design | 2 | Inventory tracked in Excel; no gap tracking; onboarding is use-case driven with no prioritisation. |
| Logical Design | 2 | Schema-on-read; no governance for schema evolution; naming convention defined but not enforced; CDC before-images not preserved. |
| Physical Design | 2 | Delta Lake used consistently; no partitioning; Delta table properties not standardised; technical columns inconsistently applied; VACUUM every 30 days; no physical review gate. |
| Semantic / Catalogue Coverage | | |
| Best Practices Adherence | | |
| **Domain Score** | **2.0** | Partial — semantic and best practices sub-areas pending assessment. |

---

### 3. Silver Layer

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Conceptual Design & Subject Area Coverage | 2 | Models exist for most foundational areas but Product, unified Transaction, and Risk-Operational are absent. No unified Party model. |
| Logical Design – Core Banking Entities | 2 | Core entities foundationally covered but fragmented. No unified Account, Transaction, or Product model. Risk datasets critically incomplete (PD, ECL, liquidity, market risk absent). |
| Physical Design | 1 | No surrogate keys; no partitioning; SCD2 on only 7% of tables; data type inconsistencies (status as TIMESTAMP, amounts as STRING/BIGINT); PascalCase / snake_case mixed; no automated DDL. |
| Semantic / Business Layer | 1 | Unity Catalog present but column descriptions sparse; no business glossary linkage; no data classification; no stewardship model; no self-service discovery. |
| Banking Domain Completeness (Foundational) | 2 | Most foundational areas have some tables but Product and Employee/HR absent; no unified Account, Transaction, or Party model; no inter-entity RI validation. |
| Banking Domain Completeness (Extended / Risk) | 1 | Only partial credit risk (NPL, LGD/LTV, provisions); liquidity, market risk, OpRisk, AML transaction monitoring, and large exposure entirely absent. |
| Best Practices Adherence | 1 | Aggregations in Silver; multiple tables per entity; PII unmasked; no reconciliation; no late-data policy; no schema change management. |
| **Domain Score** | **1.4** | |

---

### 4. Gold Layer

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Conceptual Design – Reporting Subjects | | |
| Logical Design – Aggregation Models | | |
| Physical Design | | |
| Semantic / Metric Layer | | |
| Risk Reporting Coverage | | |
| Best Practices Adherence | | |
| **Domain Score** | | |

---

### 5. Modelling Standards & Governance

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Naming Conventions | | |
| Data Type & Format Standards | | |
| Key & Relationship Standards | | |
| Model Documentation & Versioning | | |
| Review & Approval Process | | |
| **Domain Score** | | |

---

### 6. Tooling & Automation

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Modelling Tool Coverage | | |
| DDL / Schema Automation | | |
| CI/CD for Data Models | | |
| Collaboration & Discovery | | |
| **Domain Score** | | |

---

## Overall Model Maturity Score

| Domain | Score |
|--------|-------|
| 1. Medallion Architecture Governance | |
| 2. Bronze Layer | |
| 3. Silver Layer | |
| 4. Gold Layer | |
| 5. Modelling Standards & Governance | |
| 6. Tooling & Automation | |
| **Overall Score** | |

---

## Top Findings & Recommended Priorities

| Priority | Domain | Gap / Finding | Recommended Action | Owner | Target Date |
|----------|--------|---------------|--------------------|-------|-------------|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
