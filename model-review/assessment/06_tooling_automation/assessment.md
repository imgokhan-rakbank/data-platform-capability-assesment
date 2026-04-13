# Domain 6: Tooling & Automation – Model Review Assessment

> **Assessor(s):**
> **Date:**
> **Version:** 1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

Effective data modelling at scale requires appropriate tooling for design, documentation, development, testing, and deployment. This domain assesses whether the right tools are in place to support the full modelling lifecycle across all three Medallion layers, and whether model development and promotion is sufficiently automated.

---

## 6.1 Modelling Tool Coverage

This sub-area evaluates whether appropriate tools exist for creating, visualising, and maintaining data models at the conceptual, logical, and physical levels.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a dedicated data modelling tool in use (e.g., erwin Data Modeler, IBM InfoSphere Data Architect, SAP PowerDesigner, Vertabelo, dbdiagram.io, dbt)? If not, how are models designed and documented? | |
| 2 | Does the modelling tool support all three modelling levels — conceptual, logical, and physical — or is it limited to physical DDL management? | |
| 3 | Can the modelling tool generate Delta Lake / Databricks-compatible DDL directly from the logical model, reducing manual translation errors? | |
| 4 | Is the modelling tool integrated with version control (Git) so that model definitions are versioned alongside transformation code? | |
| 5 | Is there a tool for creating and maintaining entity-relationship diagrams that are kept in sync with the physical implementation (not just a one-time diagram in Visio or draw.io)? | |
| 6 | Is there a transformation framework (e.g., dbt Core / Cloud, Spark structured streaming with schema enforcement) used as the primary vehicle for defining and managing Silver and Gold models? | |
| 7 | If dbt is used: are dbt models, sources, tests, and documentation committed to Git? Is dbt docs site published and accessible to the team? | |
| 8 | Is there a data catalogue tool (e.g., Unity Catalog, Databricks Unity Catalog, Apache Atlas, Collibra, Atlan, DataHub) that serves as the system of record for model metadata across all layers? | |
| 9 | Are the modelling tool, transformation framework, and data catalogue integrated — so that metadata flows automatically from the model definition into the catalogue without manual re-entry? | |
| 10 | Is there a semantic layer tool (e.g., dbt Semantic Layer, Cube.dev, AtScale, Looker LookML) used to define and serve business metrics above the Gold physical tables? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Data Modelling Tool | |
| Transformation Framework | |
| Data Catalogue Tool | |
| Semantic Layer Tool | |
| ER Diagram Tool | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Modelling Tool Maturity | | |
| Transformation Framework Maturity | | |
| Catalogue & Documentation Tooling | | |
| Semantic Layer Tooling | | |
| Tool Integration | | |
| **Sub-Area Overall** | | |

---

## 6.2 DDL / Schema Automation

This sub-area assesses whether the physical creation, evolution, and validation of schemas is automated rather than manual.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are DDL scripts (CREATE TABLE, ALTER TABLE) generated automatically from the logical model or transformation framework definition, rather than hand-crafted? | |
| 2 | Is there a schema migration tool (e.g., dbt model versioning, Flyway, Liquibase, Delta Lake schema evolution) used to apply and track structural changes to existing tables? | |
| 3 | Are ADD COLUMN migrations applied automatically by the pipeline on schema evolution detection, or do they require manual intervention? | |
| 4 | Are BREAKING changes (column rename, type change, column removal) detected automatically and prevented from silently propagating downstream without a managed change process? | |
| 5 | Is there an automated schema registry or contract enforcement mechanism that validates that a Silver producer's output schema matches what Silver-to-Gold consumers expect? | |
| 6 | Are table properties (partitioning, clustering, Delta properties, access controls) applied consistently and automatically as part of model deployment, rather than set manually per table? | |
| 7 | Is there an automated process that compares the deployed physical schema against the logical model definition to detect schema drift — and alerts when drift is found? | |
| 8 | Are database / schema / catalogue objects provisioned through Infrastructure-as-Code (e.g., Terraform for Unity Catalog resources, Databricks provider) rather than manually via the UI? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| DDL Generation Approach | |
| Schema Migration Tool | |
| Schema Drift Detection Mechanism | |
| IaC for Catalogue / Schema Objects | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| DDL Automation | | |
| Schema Migration Maturity | | |
| Schema Drift Detection | | |
| **Sub-Area Overall** | | |

---

## 6.3 CI/CD for Data Models

This sub-area evaluates whether the development, testing, and promotion of data models follows a governed, automated CI/CD pipeline.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a CI/CD pipeline for data model changes — i.e., does every change to a Silver or Gold model go through automated testing before being merged and deployed? | |
| 2 | What automated tests are executed on model changes in CI? (e.g., dbt test suite — uniqueness, not-null, referential integrity, accepted values, custom business rule tests) | |
| 3 | Are model changes tested against a representative data slice or a dedicated CI environment before being promoted to production, rather than being deployed directly? | |
| 4 | Is there a promotion workflow (dev → test → prod) for data models, with gates between environments requiring test passage and approval? | |
| 5 | Are breaking schema changes flagged automatically in the PR/MR process — e.g., a CI check that detects column renames or type changes and requires additional approval? | |
| 6 | Is the CI pipeline integrated with the data catalogue — so that when a model is deployed, its metadata (table description, column comments, lineage) is automatically updated in the catalogue? | |
| 7 | Are deployment rollbacks possible — can the previous version of a Gold or Silver model be restored quickly if a bad deployment is detected? | |
| 8 | Are model deployment events logged and auditable — is there a record of who deployed what model version, when, and to which environment? | |
| 9 | Is there a data contract testing step in CI that validates a model's output against an agreed schema/contract before deployment? | |
| 10 | Are end-to-end pipeline tests executed on a schedule in production to validate that Silver and Gold models are producing expected results after each full refresh cycle? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| CI/CD Tool for Models (e.g., GitHub Actions, Azure DevOps, dbt Cloud) | |
| Test Framework | |
| Environment Promotion Process | |
| Rollback Mechanism | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| CI/CD Pipeline Maturity | | |
| Test Coverage | | |
| Environment Management | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 6.4 Collaboration & Discovery

This sub-area assesses whether the tooling supports team collaboration, knowledge sharing, and self-service discovery of data models.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Can a new data engineer or analyst find all Silver and Gold tables, their descriptions, column definitions, and sample data in one place without asking a colleague? | |
| 2 | Is there a process for data engineers to propose new model additions or changes, and a place for that proposal to be visible, commented on, and tracked? | |
| 3 | Are data lineage graphs (table-to-table and column-to-column lineage) available in the catalogue, so that the impact of a Bronze schema change on downstream Gold reports can be assessed? | |
| 4 | Are model change notifications sent to downstream consumers when an upstream Silver table changes — e.g., via Slack, email, or a data contract management tool? | |
| 5 | Is there a shared library or component repository for reusable modelling patterns (e.g., a dbt macro for SCD Type 2, a standard surrogate key generation macro, a standard audit column seed) that all teams use rather than duplicating? | |
| 6 | Can business users (risk analysts, finance team) access model documentation and metric definitions through a non-technical interface — e.g., a published dbt docs site, a Collibra business glossary, or a data marketplace? | |
| 7 | Is there a defined onboarding process for new data engineers that includes training on the modelling standards, tools, and the banking domain model? | |
| 8 | Are modelling decisions and architectural choices recorded in Architecture Decision Records (ADRs) or a similar format so that the rationale for key design choices is preserved for future team members? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Self-Service Catalogue / Discovery Tool | |
| Lineage Visualisation Tool | |
| Shared Macro / Pattern Library | |
| Consumer Notification Mechanism | |
| ADR / Decision Log Location | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Discoverability | | |
| Collaboration Tooling | | |
| Knowledge Management | | |
| **Sub-Area Overall** | | |
