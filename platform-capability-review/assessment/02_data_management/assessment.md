# Domain 2: Data Management – Capability Assessment

> **Assessor(s):**  Gokhan Imral 
> **Date:**  11/04/2026
> **Version:**  1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 2.1 Data Processing

Data processing covers the transformation, enrichment, aggregation, and computation of data at scale (batch and streaming).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What compute engines are used for data processing (e.g., Spark, Flink, dbt, Snowflake, BigQuery, Databricks)? |Databricks PySpark, Confluent FlinkSQL |
| 2 | How is data processing logic (transformation code) version-controlled, tested, and deployed? |There are no clear process defined. Code is in git. |
| 3 | Are there defined processing tiers (e.g., raw/bronze, curated/silver, conformed/gold)? Are they consistently applied? |No, clear responsibility and rules for the layers are not defined. |
| 4 | How is processing performance monitored (job duration, resource utilization, throughput)? |No monitoring exists. |
| 5 | Is streaming processing (real-time transformation) in use alongside batch processing? |Yes. Pyspark streaming, FlinkSQL isused. |
| 6 | How are processing failures handled (partial failures, reruns, data consistency)? |No clear process exists. Job are just reexecuted. |
| 7 | Are there cost management mechanisms to optimize compute spend (spot instances, auto-scaling, query optimization)? |Cost tagging and management does not exists, it is being implemented, clear process is not fully defined. |
| 8 | How is processing logic reused across teams (shared libraries, dbt packages, common transformation modules)? |Core team creates reusable libraries, but they are not defined nor documented. There is no base framework defined to handle processing. |
| 9 | Are data processing SLAs defined and tracked per pipeline or domain? |No |
| 10 | How is data lineage captured at the transformation level (column-level lineage, audit trails)? |Databricks captures the lineage|

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |Databricks |
| Vendor / Version |Databricks |
| Deployment Model |Cloud |
| Processing Volumes (daily / monthly) |Not known nor tracked |
| Known Gaps / Limitations |No clear rules or expectations defined for each processing layer. No process is defined on how to handle late arriving data. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |2 |Process of building new pipelines is not documented/well defined nor communicated. |
| Tooling Maturity |3 |Databricks is a fully managed solution, has out of the box support for most of the non functional requirements. But no monitoring tooling exists. |
| People / Skills |2 |There are not enough people supporting the core platform.Data engineers are not well versed in Databricks.|
| Automation Level |1 |No automation and no framework exists supporting pipeline development |
| **Sub-Area Overall** |2 |Databrciks with medallion architecture is well known but supprting function are not implemented. |

---

## 2.2 Data Storage Management

Data storage management covers the design, provisioning, management, and optimization of storage systems (data lakes, warehouses, lakehouses, databases, object stores).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What storage platforms are in use (e.g., S3/ADLS/GCS data lake, Snowflake/BigQuery/Redshift data warehouse, Delta Lake/Iceberg/Hudi lakehouse)? |ADLS, DeltaLake |
| 2 | Is there a defined multi-tier storage architecture (hot / warm / cold)? How is data lifecycle managed? |No |
| 3 | How is storage capacity monitored, forecasted, and rightsized? |It is not monitored |
| 4 | How is data partitioning, clustering, and indexing strategy defined and governed? |Databricks liquid clustering is used but not applied to all tables. |
| 5 | What is the backup and recovery strategy (RPO/RTO targets, tested restore procedures)? |None |
| 6 | How are storage costs tracked and optimized (compression, tiering, retention policies)? |No process exists. |
| 7 | Is data format standardization enforced (e.g., Parquet, ORC, Delta) and governed? |Delta Lake and parquet are used but no clear standard documnetation nor enforment happens |
| 8 | How is storage access controlled (bucket policies, VNet service endpoints, encryption at rest)? |TDE is enabled. Access rll are defined at group levels but they are not granular enough. |
| 9 | Is there a defined data retention and archival policy applied consistently across storage systems? |No |
| 10 | How are storage performance SLAs (query latency, throughput) defined and monitored? |They are not monitored. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Storage Platform(s) |Databricks, ADLS |
| Vendor / Version |Databricks, Microsoft |
| Deployment Model |Cloud |
| Total Managed Data Volume |A few TBs |
| Known Gaps / Limitations |Monitoring is lacking, process for data retention/ archival are not defined. Access controls are not defined properly. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |2 |Process is not defined nor documented. |
| Tooling Maturity |3 |Databricks is a fully managed solutions, has out of the box support for most of the non functional requirements. But no monitoring tooling exists. |
| People / Skills |3 |There are not enough people supporting the core platform. Microsoft and Databricks support is very useful covering any gaps that are in the skills. |
| Automation Level |3 |No automation exits for access controls, but platfrom provisioning is automated |
| **Sub-Area Overall** |3 |Databricks does the heavy lifting but monitoring and process definitions are lacking. |

---

## 2.3 Data Modeling

Data modeling covers the logical and physical design of data structures to support business and analytical needs.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined enterprise data model or canonical data model? Who owns and maintains it? | Models for Silver and Gold layers are defined in Erwin. Ownership is not clearly established. |
| 2 | What modeling methodologies are used (3NF, Data Vault, Kimball dimensional, One Big Table, etc.)? Are they applied consistently? | Silver layer resembles Kimball but is not properly implemented; Gold layer uses star schemas. No well-defined process for methodology selection in Gold. |
| 3 | Are data models version-controlled and promoted through environments via automated pipelines? | Versioning is managed in SharePoint, making collaboration difficult and causing drifts/conflicts. No automation for versioning, merging, or deployment. |
| 4 | Is there a peer-review or approval process for data model changes? | Peer review process is being put in place but not fully established. |
| 5 | Are data models documented with business context, definitions, and relationships accessible to stakeholders? | Data dictionary and high-level relationships are defined, but Erwin models are not integrated with the governance portal, limiting accessibility. |
| 6 | How are breaking model changes (column drops, type changes) detected and communicated to downstream consumers? | No process exists for handling or communicating model changes. |
| 7 | Is there a semantic or business layer (e.g., LookML, dbt metrics, Power BI datasets) aligned to the physical model? | Semantic layer exists at the physical layer, but not clearly documented or aligned for business use. |
| 8 | How frequently is the data model reviewed for relevance and fitness as business requirements evolve? | No formal review process; ad hoc updates occur as needed. |
| 9 | Are modeling standards (naming conventions, key design, null handling) documented and enforced? | Modeling standards are not well defined or documented. |
| 10 | Is there a process to deprecate and decommission obsolete models and tables? | No process exists for deprecating or decommissioning obsolete models/tables. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Modeling Tool(s) (e.g., erwin, dbt, PowerDesigner) | Erwin |
| Vendor / Version | Erwin (version not tracked centrally) |
| Number of Managed Models / Domains | Silver and Gold layers modeled; number not tracked |
| Known Gaps / Limitations | Versioning in SharePoint (no collaboration), no integration with governance portal, no automation, standards not defined, Silver layer too wide/BI-specific, heavy business semantics in Silver, excessive denormalization, attributes dropped/filtered, no process for changes or communication. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No formal processes for versioning, change management, or methodology selection. Peer review is being introduced. |
| Tooling Maturity | 2 | Erwin is used but not integrated with governance tools; versioning is manual and not collaborative. |
| People / Skills | 2 | Some modeling knowledge exists, but lack of standards and collaboration limits effectiveness. |
| Automation Level | 1 | No automation for versioning, merging, or deployment. |
| **Sub-Area Overall** | 1 | Modeling is ad hoc, lacks process, standards, and automation; collaboration is poor and models are not broadly accessible. |

---

## 2.4 DataOps

DataOps covers the practices, processes, and tooling that enable rapid, reliable, and collaborative delivery of data pipelines – analogous to DevOps for software.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are data pipelines version-controlled in Git? Is there a branching and code review process? | Data pipelines are in Git, but no clear process exists for management, branching, or code review. |
| 2 | Is there a CI/CD pipeline for data assets (automated testing, environment promotion, deployment)? | Pipeline deployments use GitHub Actions but are not fully automated. No CI steps for controls or tests. |
| 3 | What automated testing is applied to data pipelines (unit, integration, data contract, regression tests)? | No automated testing; all tests are manual. |
| 4 | How many distinct environments exist (dev / test / staging / prod)? Is environment parity maintained? | UAT, test, and prod environments exist but are not properly maintained or kept in parity. |
| 5 | Is infrastructure for data platforms managed as code (Terraform, Bicep, CDK)? | Infrastructure deployments use Terraform. |
| 6 | How are configuration and secrets managed across environments? | Configurations are baked into control tables and pipelines; no secrets management exists. |
| 7 | Are data pipeline observability signals (logs, metrics, traces) centralized and actionable? | No monitoring or observability solution exists. |
| 8 | What is the mean time to detect (MTTD) and mean time to recover (MTTR) for data pipeline incidents? | Incidents are not tracked or documented. |
| 9 | Is there a documented on-call or incident response process for data platform issues? | No documented on-call or incident response process. |
| 10 | How are cross-team dependencies and shared data assets coordinated during development and deployment? | Managed over emails; no self-service or formal coordination process. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| CI/CD Tool(s) | GitHub Actions (not fully automated) |
| Source Control Platform | Git |
| Infrastructure-as-Code Tool | Terraform |
| Observability / Monitoring Tool | None |
| Known Gaps / Limitations | No CI/testing automation, no monitoring, no secrets management, environments not maintained, incident management lacking, cross-team coordination is manual. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No formal processes for pipeline management, testing, or incident response. |
| Tooling Maturity | 2 | Git, GitHub Actions, and Terraform are used, but automation and monitoring are lacking. |
| People / Skills | 2 | Basic skills exist for Git and Terraform, but lack of process and automation limits effectiveness. |
| Automation Level | 1 | Minimal automation; deployments are not fully automated, no CI/testing, no monitoring. |
| **Sub-Area Overall** | 1 | DataOps is ad hoc, lacks automation, monitoring, and formal processes; coordination is manual. |

---

## 2.5 ML / AI Model Management

ML/AI model management covers the lifecycle of machine learning and AI models from development and training through deployment, monitoring, and retirement.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there an ML platform or MLOps tooling in place (e.g., MLflow, SageMaker, Azure ML, Vertex AI, Databricks MLflow)? | Databricks MLflow is used, but no formal process exists. |
| 2 | How are ML experiments tracked (hyperparameters, metrics, datasets, code versions)? | No process or documentation for experiment tracking. |
| 3 | Is model training automated and triggered by new data or scheduled retraining? | No automation; models use data directly from source systems. |
| 4 | How are trained models versioned, stored, and registered (model registry)? | No formal versioning or registration process. |
| 5 | What is the model deployment process (batch scoring, real-time inference, A/B testing, canary rollout)? | No defined or documented deployment process. |
| 6 | How are deployed models monitored for performance degradation, data drift, and concept drift? | No monitoring is in place. |
| 7 | Is there an approval / governance process before a model goes to production? | No approval or governance process exists. |
| 8 | How is model explainability and bias detection addressed? | Not addressed; no standards or documentation. |
| 9 | What is the process for retiring or replacing a model in production? | No process exists for model retirement or replacement. |
| 10 | Are feature engineering artifacts (feature stores) shared and reused across models? | No feature store or sharing of artifacts. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| ML Platform / MLOps Tool(s) | Databricks MLflow |
| Feature Store | None |
| Model Registry | None |
| Inference / Serving Platform | None |
| Number of Models in Production | Not tracked |
| Known Gaps / Limitations | No process, no documentation, no standards, no monitoring, no feature store, no model registry, no automation. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No formal processes, standards, or documentation for ML lifecycle. |
| Tooling Maturity | 2 | Databricks MLflow is available but not used in a structured or integrated way. |
| People / Skills | 2 | Some ML skills exist, but lack of process and standards limits effectiveness. |
| Automation Level | 1 | No automation for training, deployment, or monitoring. |
| **Sub-Area Overall** | 1 | ML/AI model management is ad hoc, lacks process, standards, automation, and monitoring. |

---

## 2.6 Self-Service Data Preparation

Self-service data preparation covers the ability for business analysts and non-engineers to prepare, blend, and enrich datasets independently.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are self-service data preparation tools available (e.g., Alteryx, Trifacta/Dataprep, Power Query, Databricks notebooks)? | Power BI is available for reporting only; no self-service data preparation tools are accessible to business users. |
| 2 | What percentage of business users actively use self-service preparation tools? | 0%. All data preparation is handled by engineers. |
| 3 | How is data access controlled in self-service tools to prevent unauthorized access to sensitive data? | No business user access to data sources; all access is managed by engineers. |
| 4 | Are datasets prepared by business users governed and made available for reuse, or siloed? | No datasets are prepared by business users. |
| 5 | Is there a process to promote high-quality, business-prepared datasets into the curated data layer? | No process exists; all data curation is engineer-driven. |
| 6 | What training and support is provided to business users for data preparation? | No training or support for business user data preparation. |
| 7 | How are resource limits (compute, storage quotas) enforced in self-service environments? | Not applicable; no self-service environment exists. |
| 8 | Is data lineage tracked for self-service prepared datasets? | Not applicable; no self-service datasets exist. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | Power BI (reporting only) |
| Vendor / Version | Microsoft Power BI |
| Number of Active Business Users | 0 for self-service data preparation |
| Known Gaps / Limitations | No self-service data preparation, no business user access to data sources, all delivery is engineer-driven. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process for self-service data preparation; all work is handled by engineers. |
| Tooling Maturity | 2 | Power BI is available for reporting, but no self-service data preparation tools are provided. |
| People / Skills | 1 | Business users are not enabled or trained for self-service data preparation. |
| Automation Level | 1 | No automation or self-service capability exists. |
| **Sub-Area Overall** | 1 | No self-service data preparation; all activities are engineer-driven. |

---

## 2.7 Data Product Management

Data product management covers treating data assets as products – with defined owners, consumers, SLAs, documentation, and a lifecycle.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the concept of a "data product" formally defined and adopted in the organization? | No definition or adoption of data product concept. |
| 2 | Are data products assigned product owners accountable for quality, availability, and consumer experience? | No product owners assigned; no accountability structure. |
| 3 | Are data products documented with schemas, SLAs, freshness guarantees, and usage examples? | No documentation or SLAs for data products. |
| 4 | Is there a catalog or marketplace where data products are discoverable by potential consumers? | No catalog or marketplace exists. |
| 5 | How are data product consumer requests and feedback managed? | No formal process for managing requests or feedback. |
| 6 | Are data product health metrics (uptime, freshness, quality score, consumer count) tracked and published? | No monitoring or health metrics tracked. |
| 7 | Is there a defined process for publishing, versioning, deprecating, and retiring data products? | No process exists for lifecycle management of data products. |
| 8 | Are data contracts used to formalize agreements between data product producers and consumers? | No data contracts in place. |
| 9 | How are cross-domain data product dependencies identified and managed? | No dependency management for data products. |
| 10 | Is there a data mesh or domain ownership model governing data product responsibilities? | No data mesh or domain ownership model. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Data Product Platform / Registry | None |
| Data Contract Tooling | None |
| Number of Published Data Products | None |
| Known Gaps / Limitations | No definition, governance, marketplace, monitoring, or dependency management for data products. |
| Process Maturity | 1 | No processes or governance for data product management. |
| Tooling Maturity | 1 | No tooling or platform for data product management. |
| People / Skills | 1 | No roles or skills defined for data product management. |
| Automation Level | 1 | No automation or lifecycle management for data products. |
| **Sub-Area Overall** | 1 | Data product management is absent; no definition, governance, or supporting tools/processes. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity  | 1 | No processes or governance for data product management. |
| Tooling Maturity  | 1 | No tooling or platform for data product management. |
| People / Skills   | 1 | No roles or skills defined for data product management. |
| Automation Level  | 1 | No automation or lifecycle management for data products. |
| **Sub-Area Overall** | 1 | Data product management is absent; no definition, governance, or supporting tools/processes. |
