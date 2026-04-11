# Data Platform Capability Assessment – Consolidated Summary

> **Instructions:** Once all domain assessments are complete, transfer the Sub-Area Overall scores here.  
> Calculate the Domain Score as the average of its sub-area scores (round to 1 decimal place).  
> The Overall Platform Score is the average of all domain scores.

---

## Domain Score Rollup

### 1. Data Integration

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Event-Driven Integration | 2 | Confluent Cloud in place; CDC unreliable, monitoring and automation lacking. |
| Batch-Driven Integration | 2 | ADF and Databricks handle core processing; supporting functions absent. |
| API-Driven Integration | 1 | No data APIs exist; no process defined. |
| Data Virtualization | 1 | No virtualization solution in place. |
| Orchestration | 2 | ADF and Databricks used; surrounding orchestration processes not established. |
| Data Automation | 2 | GitHub Actions used for provisioning; pipeline automation and process lacking. |
| **Domain Score** | **1.7** | |

---

### 2. Data Management

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Data Processing | 2 | Databricks medallion architecture in place; supporting functions not yet implemented. |
| Data Storage Management | 3 | Databricks / Delta Lake well-established; monitoring and process definitions lacking. |
| Data Modeling | 1 | Ad hoc, no standards, process, automation, or collaboration tooling. |
| DataOps | 1 | Ad hoc; no automation, monitoring, or formal DataOps process. |
| ML / AI Model Management | 1 | Ad hoc; no process, standards, automation, or monitoring for models. |
| Self-Service Data Preparation | 1 | No self-service capability; all prep is engineer-driven. |
| Data Product Management | 1 | Concept not adopted; no ownership, documentation, or tooling. |
| **Domain Score** | **1.4** | |

---

### 3. Data Quality Management

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Profiling | 1 | Ad hoc and manual; not automated for the data platform. |
| DQ Rules Management | 1 | Not formalized; no rules engine or standard process. |
| DQ Monitoring | 1 | Not implemented for the data platform. |
| Data Observability | 1 | No observability tooling or practice in place. |
| **Domain Score** | **1.0** | |

---

### 4. Analytics

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Data Marketplace | 1 | No marketplace; analytics ad hoc with no standards or automation. |
| Data Visualization | 1 | Ad hoc, IT-dependent; no standards or automation. |
| Exploratory Data Analysis | 1 | EDA not practiced; sandbox environment unused. |
| Self-Service Analytics | 1 | All reporting is IT-driven; no self-service capability. |
| **Domain Score** | **1.0** | |

---

### 5. Metadata Management

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Data Catalog | 2 | Partial cataloging exists in source systems; not integrated or platform-wide. |
| Data Discovery | 2 | Discovery limited to source systems; not integrated or platform-wide. |
| Lineage & Impact Analysis | 1 | Limited and not end-to-end; no integrated lineage for the data platform. |
| **Domain Score** | **1.7** | |

---

### 6. Security & Compliance

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Data Privacy Management | 1 | Largely absent; only partial data classification exists. |
| Data Protection | 1 | Limited to encryption at rest; other protection controls absent. |
| Access Governance | 1 | Basic and manual; lacks automation and periodic access review. |
| Audit Management | 1 | Absent; no process, tooling, or automation for audit trails. |
| **Domain Score** | **1.0** | |

---

### 7. Data Governance

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Data Strategy Management | 1 | Absent; no strategy, ownership, or process defined. |
| Data Standards Management | 1 | Only a basic business glossary exists; no enforced standards. |
| Data Stewardship | 1 | Absent; no steward roles, process, or tooling. |
| **Domain Score** | **1.0** | |

---

### 8. Supporting Technology

| Sub-Area | Score (1–5) | Notes |
|----------|-------------|-------|
| Platform Monitoring | 1 | Absent; no process, tooling, or automation for platform health monitoring. |
| Automation & CI/CD | 1 | Basic infrastructure provisioning only; no CI/CD for data pipelines. |
| Development Practices | 1 | Ad hoc; no coding standards, documentation framework, or code review process. |
| **Domain Score** | **1.0** | |

---

## Overall Platform Maturity Score

| Domain | Score |
|--------|-------|
| 1. Data Integration | 1.7 |
| 2. Data Management | 1.4 |
| 3. Data Quality Management | 1.0 |
| 4. Analytics | 1.0 |
| 5. Metadata Management | 1.7 |
| 6. Security & Compliance | 1.0 |
| 7. Data Governance | 1.0 |
| 8. Supporting Technology | 1.0 |
| **Overall Score** | **1.2** |

---

## Radar / Heat Map (Qualitative)

Copy scores above into a radar chart or heat map tool (e.g., Excel, Power BI, Tableau) to visualize capability gaps visually.

---

## Top Findings & Recommended Priorities

| Priority | Domain | Gap / Finding | Recommended Action | Owner | Target Date |
|----------|--------|---------------|--------------------|-------|-------------|
| 1 | Data Quality Management | No DQ rules, monitoring, or observability in place for the data platform. | Implement a DQ framework covering profiling, rules management, monitoring, and observability (e.g., Great Expectations, Databricks DQ). | Data Quality Lead | |
| 2 | Data Governance | No data strategy, standards enforcement, or stewardship roles defined. | Establish a data governance framework: appoint data stewards, define data standards, and create a roadmap. | Data Governance Lead | |
| 3 | Analytics | No data marketplace or self-service capability; all reporting is IT-driven. | Build a data marketplace on Unity Catalog and enable self-service analytics via Databricks SQL / Power BI. | Analytics Lead | |
| 4 | Data Integration | No API integration layer or data virtualization capability. | Define and implement a data API strategy; evaluate a virtualization/API gateway solution. | Data Engineer | |
| 5 | Supporting Technology | No platform monitoring, DataOps CI/CD for data pipelines, or development standards. | Establish platform monitoring (alerts, dashboards), extend CI/CD to data pipelines, and adopt coding / documentation standards. | DevOps / Platform Engineer | |
