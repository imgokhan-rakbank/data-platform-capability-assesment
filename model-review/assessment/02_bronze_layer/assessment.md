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
| 1 | Is there a conceptual inventory (diagram or catalogue) of all source systems feeding Bronze, and the subject areas they represent (e.g., Core Banking, Cards, CRM, GL, Risk Systems)? | It is tracked in an excel |
| 2 | Are source systems categorised by domain (e.g., origination, payments, customer, risk, reference data), and is this categorisation reflected in the Bronze namespace / schema design? | It is tracked in excel, in bronze layer has schema for each source system |
| 3 | Are there any source systems that are not yet represented in Bronze but are required for complete banking domain coverage? If so, are gaps tracked? | Gaps are not tracked, systems are onboarded as use cases requires it |
| 4 | Is there a decision record on which source systems use event-driven (CDC/streaming) ingestion versus batch ingestion — and is this reflected in the Bronze model design? | Tracked in excel |
| 5 | Are all critical banking source systems identified and prioritised (e.g., core banking system, card management, loan origination, trade finance, treasury)? | No prioritisation, it is use case driven |

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
| Conceptual Modelling | 2 | Inventory exists in Excel, but not formalized or complete. |
| Domain Coverage | 2 | No gap tracking or prioritization; onboarding is use-case driven. |
| **Sub-Area Overall** | 2 | Some structure, but mostly "Developing" maturity. |

---

## 2.2 Logical Design

Logical design at Bronze examines how raw source schemas are represented as logical structures in the platform — specifically whether schema-on-read or schema-on-write is applied, and how structural metadata is captured.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are source schemas (tables, columns, data types) documented in a logical model or data catalogue, or does the Bronze layer operate entirely schema-on-read? | Source schemas are being documented at Informatica ayer but it is not integrated into data platform. Bronze layer schema is dynamic |
| 2 | How are source schema changes (new columns, column renames, type changes, dropped columns) detected and handled? Is schema evolution governed? | No governance exists |
| 3 | Is there a defined logical structure for how Bronze tables are named — does the naming reflect source system, schema, and entity unambiguously? | Architecture documentation defines the naming convention but it is not enforced at system level |
| 4 | Are relationships between source tables (foreign keys, parent-child) captured in documentation even if not enforced at the physical layer? | bronze layer FKs are not captured in data platform |
| 5 | How are CDC events (insert / update / delete operations) captured and represented in the logical Bronze model — are operation codes and before/after images preserved? | No they are not preserved. before values are dropped |
| 6 | Is there a documented strategy for handling multi-source data for the same entity (e.g., customer data from core banking and CRM) at Bronze — are they kept separate or merged? | They are seperate at bronze layer |
| 7 | Are binary, unstructured, or semi-structured payloads (e.g., JSON blobs, XML messages) documented in terms of expected structure and handled consistently? | No support for unstructured data exists, semi structured data is flattend into delta lake |

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
| Logical Modelling | 2 | Some documentation exists, but not integrated or enforced. |
| Schema Governance | 1 | No governance for schema evolution or foreign keys. |
| Documentation Quality | 2 | Documentation exists but is not comprehensive or enforced. |
| **Sub-Area Overall** | 2 | Basic practices, but not consistent or governed. |

---

## 2.3 Physical Design

Physical design at Bronze evaluates the concrete implementation choices: storage format, partitioning, table properties, and how these align with downstream processing needs.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What is the storage format used in Bronze (e.g., Delta Lake, Parquet, Avro, raw JSON/CSV)? Is this standardised across all ingestions, and is the choice documented? | DeltaTable is used |
| 2 | How are Bronze tables partitioned? Is partitioning strategy defined based on ingestion patterns (e.g., by ingestion date, source system, event type)? | No partitionoing defined.  |
| 3 | Are Delta Lake table properties (e.g., `delta.logRetentionDuration`, `delta.deletedFileRetentionDuration`, Auto Optimize, Liquid Clustering) defined and applied consistently? | No they are not |
| 4 | Are standard technical columns (`_ingest_timestamp`, `_source_file_name`, `_batch_id`, `_op_type` for CDC) present and consistently populated across all Bronze tables? | They are present but not enforced or consistent |
| 5 | Is there a defined naming convention for Bronze databases, schemas, and tables (e.g., `bronze.<source_system>.<entity_name>`)? Is it consistently followed? | Yes it exist but not enforced at system level |
| 6 | How is data immutability ensured in Bronze? Are UPDATE and DELETE operations prevented or at least audited? Is Time Travel / VACUUM policy defined? | UPDATE and DELETE are not audited. Vacumming is enabled for every 30 days. |
| 7 | Are data types mapped consistently from source to Bronze (e.g., all source strings remain strings, source decimals preserved without implicit casting)? | Yes |
| 8 | Is there a defined maximum Bronze table size / retention policy to manage storage growth, and is it enforced? | No |
| 9 | Are Z-ORDER or clustering keys applied to Bronze tables where range-scan performance is critical for Silver processing? | No |
| 10 | How is the Bronze physical model reviewed and approved before a new source is deployed to production? | No model review is there, model is source aligned |

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
| Physical Modelling | 2 | DeltaTable is used, but partitioning and technical columns are not enforced. |
| Storage Optimisation | 1 | No partitioning, clustering, or retention policy enforcement. |
| Standards Conformance | 2 | Naming conventions exist but are not enforced; technical columns are inconsistent. |
| **Sub-Area Overall** | 2 | Some standards, but not enforced or optimized. |

---

## 2.4 Semantic / Catalogue Coverage

The semantic perspective at Bronze is minimal by design (Bronze is raw), but there are basic catalogue and lineage requirements that support downstream comprehension and debugging.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are Bronze tables registered in a data catalogue (e.g., Unity Catalog, Apache Atlas, Collibra) with at minimum source system, ingestion type, and update frequency documented? | No |
| 2 | Are table and column descriptions populated in the catalogue for Bronze assets — even minimal descriptions indicating source system origin and meaning? | No |
| 3 | Is data lineage captured from source system through ingestion pipeline to Bronze table (at least table-level lineage)? | No |
| 4 | Are data classification tags (e.g., PII, Confidential, Public) applied to Bronze columns where sensitive data is present? | No |
| 5 | Is there a documented SLA for Bronze data freshness (e.g., CDC lag < 5 minutes, batch updated by 06:00 AM) and is it published to consumers? | No |
| 6 | Are Bronze assets discoverable by Silver layer developers without requiring tribal knowledge — can a developer find the right Bronze table without asking a colleague? | No |

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
| Semantic / Catalogue Coverage | 1 | No catalogue registration, descriptions, or classification. |
| Lineage Completeness | 1 | No lineage captured. |
| Discoverability | 1 | Assets are not discoverable without tribal knowledge. |
| **Sub-Area Overall** | 1 | Practices are at the "Initial" level. |

---

## 2.5 Bronze Best Practices

This sub-area checks adherence to widely accepted best practices for the Bronze / raw layer in a lakehouse architecture.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the Bronze layer truly raw — i.e., is data stored exactly as received from the source with no business transformation or filtering applied? | No value changing transformation is happening but, before values of CDC events are dropped |
| 2 | Is idempotent ingestion implemented so that re-running a pipeline does not produce duplicate records in Bronze? | No, bronze is append only or truncate insert |
| 3 | Is full history retained in Bronze (append-only or insert+CDC), enabling point-in-time replay for reprocessing Silver? | No for batch workloads some data is ingested by truncate insert losing the history |
| 4 | Is there a clear separation between the ingestion pipeline (responsible for writing to Bronze) and the transformation pipeline (responsible for reading from Bronze and writing Silver), ensuring single-responsibility? | Yes |
| 5 | Are schema-on-read tables documented with sample records and expected schemas to reduce the risk of silent data quality issues flowing downstream? | Unity catalog has sample values |
| 6 | Is there a mechanism to detect and quarantine records that fail basic technical validation (e.g., mandatory ingestion columns missing, malformed payloads) before they land in Bronze? | No |
| 7 | Are GDPR / data residency / data sovereignty requirements respected at the Bronze layer — is PII data handled appropriately from the point of ingestion? | No |
| 8 | Is the Bronze layer access-controlled so that only ingestion pipelines can write, and Silver pipelines / auditors can read, but end-users or analysts cannot access raw sensitive data directly? | No |

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
| Best Practices Adherence | 2 | Raw data mostly preserved, but CDC before values are dropped and history is not always retained. |
| Data Integrity | 1 | No idempotency, quarantine, or validation mechanisms. |
| Security at Ingestion | 1 | No access controls or GDPR compliance. |
| **Sub-Area Overall** | 1 | Majority of best practices are not implemented. |
