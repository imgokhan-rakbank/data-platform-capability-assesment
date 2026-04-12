# Domain 2: Bronze Layer – Model Review Assessment

> **Assessor(s):**
> **Date:**
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

The Bronze layer is the **raw landing zone** of the Medallion Architecture. Its primary purpose is to preserve a faithful, immutable copy of data as received from source systems — no business logic, no complex transformations. Assessment questions focus on whether the layer achieves this reliably, is well-structured, and is properly documented at all four modelling perspectives.

---

## 2.1 Conceptual Design

Conceptual design at Bronze validates that the right source domains are represented and that the scope of the layer is clearly understood.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a conceptual inventory (diagram or catalogue) of all source systems feeding Bronze, and the subject areas they represent (e.g., Core Banking, Cards, CRM, GL, Risk Systems)? | |
| 2 | Are source systems categorised by domain (e.g., origination, payments, customer, risk, reference data), and is this categorisation reflected in the Bronze namespace / schema design? | |
| 3 | Are there any source systems that are not yet represented in Bronze but are required for complete banking domain coverage? If so, are gaps tracked? | |
| 4 | Is there a decision record on which source systems use event-driven (CDC/streaming) ingestion versus batch ingestion — and is this reflected in the Bronze model design? | |
| 5 | Are all critical banking source systems identified and prioritised (e.g., core banking system, card management, loan origination, trade finance, treasury)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Source System Inventory Location | |
| Number of Source Systems Currently in Bronze | |
| Known Missing Source Systems | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Modelling | | |
| Domain Coverage | | |
| **Sub-Area Overall** | | |

---

## 2.2 Logical Design

Logical design at Bronze examines how raw source schemas are represented as logical structures in the platform — specifically whether schema-on-read or schema-on-write is applied, and how structural metadata is captured.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are source schemas (tables, columns, data types) documented in a logical model or data catalogue, or does the Bronze layer operate entirely schema-on-read? | |
| 2 | How are source schema changes (new columns, column renames, type changes, dropped columns) detected and handled? Is schema evolution governed? | |
| 3 | Is there a defined logical structure for how Bronze tables are named — does the naming reflect source system, schema, and entity unambiguously? | |
| 4 | Are relationships between source tables (foreign keys, parent-child) captured in documentation even if not enforced at the physical layer? | |
| 5 | How are CDC events (insert / update / delete operations) captured and represented in the logical Bronze model — are operation codes and before/after images preserved? | |
| 6 | Is there a documented strategy for handling multi-source data for the same entity (e.g., customer data from core banking and CRM) at Bronze — are they kept separate or merged? | |
| 7 | Are binary, unstructured, or semi-structured payloads (e.g., JSON blobs, XML messages) documented in terms of expected structure and handled consistently? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Schema Management Approach (on-read vs. on-write) | |
| Schema Evolution Handling Tool / Process | |
| Data Catalogue / Metadata Repository | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Logical Modelling | | |
| Schema Governance | | |
| Documentation Quality | | |
| **Sub-Area Overall** | | |

---

## 2.3 Physical Design

Physical design at Bronze evaluates the concrete implementation choices: storage format, partitioning, table properties, and how these align with downstream processing needs.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What is the storage format used in Bronze (e.g., Delta Lake, Parquet, Avro, raw JSON/CSV)? Is this standardised across all ingestions, and is the choice documented? | |
| 2 | How are Bronze tables partitioned? Is partitioning strategy defined based on ingestion patterns (e.g., by ingestion date, source system, event type)? | |
| 3 | Are Delta Lake table properties (e.g., `delta.logRetentionDuration`, `delta.deletedFileRetentionDuration`, Auto Optimize, Liquid Clustering) defined and applied consistently? | |
| 4 | Are standard technical columns (`_ingest_timestamp`, `_source_file_name`, `_batch_id`, `_op_type` for CDC) present and consistently populated across all Bronze tables? | |
| 5 | Is there a defined naming convention for Bronze databases, schemas, and tables (e.g., `bronze.<source_system>.<entity_name>`)? Is it consistently followed? | |
| 6 | How is data immutability ensured in Bronze? Are UPDATE and DELETE operations prevented or at least audited? Is Time Travel / VACUUM policy defined? | |
| 7 | Are data types mapped consistently from source to Bronze (e.g., all source strings remain strings, source decimals preserved without implicit casting)? | |
| 8 | Is there a defined maximum Bronze table size / retention policy to manage storage growth, and is it enforced? | |
| 9 | Are Z-ORDER or clustering keys applied to Bronze tables where range-scan performance is critical for Silver processing? | |
| 10 | How is the Bronze physical model reviewed and approved before a new source is deployed to production? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Storage Format | |
| Partitioning Strategy | |
| Standard Technical Columns | |
| Naming Convention Documentation | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Physical Modelling | | |
| Storage Optimisation | | |
| Standards Conformance | | |
| **Sub-Area Overall** | | |

---

## 2.4 Semantic / Catalogue Coverage

The semantic perspective at Bronze is minimal by design (Bronze is raw), but there are basic catalogue and lineage requirements that support downstream comprehension and debugging.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are Bronze tables registered in a data catalogue (e.g., Unity Catalog, Apache Atlas, Collibra) with at minimum source system, ingestion type, and update frequency documented? | |
| 2 | Are table and column descriptions populated in the catalogue for Bronze assets — even minimal descriptions indicating source system origin and meaning? | |
| 3 | Is data lineage captured from source system through ingestion pipeline to Bronze table (at least table-level lineage)? | |
| 4 | Are data classification tags (e.g., PII, Confidential, Public) applied to Bronze columns where sensitive data is present? | |
| 5 | Is there a documented SLA for Bronze data freshness (e.g., CDC lag < 5 minutes, batch updated by 06:00 AM) and is it published to consumers? | |
| 6 | Are Bronze assets discoverable by Silver layer developers without requiring tribal knowledge — can a developer find the right Bronze table without asking a colleague? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Catalogue Tool | |
| Lineage Tool | |
| Classification / Tagging Status | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Semantic / Catalogue Coverage | | |
| Lineage Completeness | | |
| Discoverability | | |
| **Sub-Area Overall** | | |

---

## 2.5 Bronze Best Practices

This sub-area checks adherence to widely accepted best practices for the Bronze / raw layer in a lakehouse architecture.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the Bronze layer truly raw — i.e., is data stored exactly as received from the source with no business transformation or filtering applied? | |
| 2 | Is idempotent ingestion implemented so that re-running a pipeline does not produce duplicate records in Bronze? | |
| 3 | Is full history retained in Bronze (append-only or insert+CDC), enabling point-in-time replay for reprocessing Silver? | |
| 4 | Is there a clear separation between the ingestion pipeline (responsible for writing to Bronze) and the transformation pipeline (responsible for reading from Bronze and writing Silver), ensuring single-responsibility? | |
| 5 | Are schema-on-read tables documented with sample records and expected schemas to reduce the risk of silent data quality issues flowing downstream? | |
| 6 | Is there a mechanism to detect and quarantine records that fail basic technical validation (e.g., mandatory ingestion columns missing, malformed payloads) before they land in Bronze? | |
| 7 | Are GDPR / data residency / data sovereignty requirements respected at the Bronze layer — is PII data handled appropriately from the point of ingestion? | |
| 8 | Is the Bronze layer access-controlled so that only ingestion pipelines can write, and Silver pipelines / auditors can read, but end-users or analysts cannot access raw sensitive data directly? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Idempotency Mechanism | |
| History Retention Policy | |
| Access Control Design | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Best Practices Adherence | | |
| Data Integrity | | |
| Security at Ingestion | | |
| **Sub-Area Overall** | | |
