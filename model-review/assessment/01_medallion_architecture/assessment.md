# Domain 1: Medallion Architecture Governance – Model Review Assessment

> **Assessor(s):**
> **Date:**
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 1.1 Architecture Conformance

Architecture conformance assesses whether the three-zone Medallion Architecture (Bronze / Silver / Gold) is consistently applied across all data domains and pipelines — and whether the purpose of each zone is understood and respected by all teams.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there an official, documented architecture decision record (ADR) or design document that defines the Medallion Architecture and its rationale for this platform? | |
| 2 | Are the boundaries between Bronze, Silver, and Gold clearly defined in writing, and are those definitions communicated to all data engineers and analysts? | |
| 3 | Is there any data that bypasses a layer (e.g., raw data directly consumed at Gold, or transformations applied in Bronze)? If yes, how is this governed? | |
| 4 | How is architecture conformance enforced — are there automated checks, pull-request gates, or peer-review checklists that prevent non-conforming models from being deployed? | |
| 5 | Are there documented data contracts between layers (e.g., Bronze → Silver, Silver → Gold) specifying expected schemas, SLAs, and quality thresholds? | |
| 6 | How are cross-cutting concerns (audit columns, metadata stamps, soft-delete patterns) applied consistently across all layers? | |
| 7 | Has the architecture been reviewed externally or benchmarked against industry references (e.g., Databricks best practices, DAMA, vendor blueprints)? | |
| 8 | Is there a forum or process (e.g., architecture review board) where proposed changes to the medallion model are evaluated before implementation? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Architecture Documentation Location | |
| Enforcement Mechanism | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Clarity | | |
| Standards Conformance | | |
| Tooling / Enforcement | | |
| People / Awareness | | |
| **Sub-Area Overall** | | |

---

## 1.2 Layer Responsibility Definition

This sub-area examines whether each layer has a precisely defined, agreed-upon responsibility — ensuring no duplication of transformation logic and no ambiguity about where a given concern lives.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the Bronze layer formally defined as a raw, unaltered landing zone? Is there any transformation applied there beyond technical ingestion concerns (e.g., encoding fixes, file parsing)? | |
| 2 | Is there a written definition of what "Silver" means on this platform — specifically which transformations, cleansing steps, and business rules are applied at Silver versus left to Gold? | |
| 3 | Is the Gold layer reserved exclusively for consumption-ready, aggregated, or domain-specific models, and is this definition enforced? | |
| 4 | Are there documented rules about which layer owns master data / reference data, and how it propagates across layers? | |
| 5 | When a new subject area is onboarded, is there a checklist or template that prescribes what models are needed at each layer? | |
| 6 | Is data duplication across layers intentional and documented (e.g., a table replicated from Silver to Gold for performance), or is it accidental? | |
| 7 | How are late-arriving data, restatements, and corrections handled — and is the handling policy documented per layer? | |
| 8 | Are there clearly defined owners (teams or individuals) for each layer, and are those owners accountable for model quality and SLAs? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Documentation / Playbook Location | |
| Owner of Bronze | |
| Owner of Silver | |
| Owner of Gold | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Clarity | | |
| Documentation Quality | | |
| Ownership & Accountability | | |
| **Sub-Area Overall** | | |

---

## 1.3 Cross-Layer Design Principles

This sub-area evaluates whether shared design principles — such as key design, surrogate key strategy, historisation patterns, and audit columns — are coherently defined and uniformly applied across all three layers.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a standard surrogate key strategy defined (e.g., hash keys, identity columns, UUIDs) and consistently applied across Silver and Gold? | |
| 2 | How is historisation / slowly changing dimension (SCD) management handled — is there a defined pattern (SCD Type 1/2/4, temporal tables) and is it consistent? | |
| 3 | Are standard audit / metadata columns (e.g., `record_created_ts`, `record_updated_ts`, `source_system_code`, `batch_id`, `is_deleted`) mandated and present in all tables? | |
| 4 | Is there a defined convention for partition keys across layers, and is it aligned with the most common query patterns? | |
| 5 | Are deleted / logically removed records handled consistently (soft delete vs. hard delete) and documented across all layers? | |
| 6 | How are timezone, date/time, and currency representation standards defined and enforced across models? | |
| 7 | Is there a cross-layer primary key traceability strategy — can a Gold record be traced back through Silver to its Bronze origin? | |
| 8 | Are null handling policies defined and applied consistently (e.g., default values vs. nulls for missing data, sentinel values)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Surrogate Key Strategy | |
| Historisation Pattern | |
| Standard Audit Columns List | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Clarity | | |
| Logical Design Consistency | | |
| Physical Implementation | | |
| Standards Conformance | | |
| **Sub-Area Overall** | | |
