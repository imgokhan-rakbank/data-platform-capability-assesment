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
| 1 | Is there a defined enterprise data model or canonical data model? Who owns and maintains it? | |
| 2 | What modeling methodologies are used (3NF, Data Vault, Kimball dimensional, One Big Table, etc.)? Are they applied consistently? | |
| 3 | Are data models version-controlled and promoted through environments via automated pipelines? | |
| 4 | Is there a peer-review or approval process for data model changes? | |
| 5 | Are data models documented with business context, definitions, and relationships accessible to stakeholders? | |
| 6 | How are breaking model changes (column drops, type changes) detected and communicated to downstream consumers? | |
| 7 | Is there a semantic or business layer (e.g., LookML, dbt metrics, Power BI datasets) aligned to the physical model? | |
| 8 | How frequently is the data model reviewed for relevance and fitness as business requirements evolve? | |
| 9 | Are modeling standards (naming conventions, key design, null handling) documented and enforced? | |
| 10 | Is there a process to deprecate and decommission obsolete models and tables? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Modeling Tool(s) (e.g., erwin, dbt, PowerDesigner) | |
| Vendor / Version | |
| Number of Managed Models / Domains | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 2.4 DataOps

DataOps covers the practices, processes, and tooling that enable rapid, reliable, and collaborative delivery of data pipelines – analogous to DevOps for software.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are data pipelines version-controlled in Git? Is there a branching and code review process? | |
| 2 | Is there a CI/CD pipeline for data assets (automated testing, environment promotion, deployment)? | |
| 3 | What automated testing is applied to data pipelines (unit, integration, data contract, regression tests)? | |
| 4 | How many distinct environments exist (dev / test / staging / prod)? Is environment parity maintained? | |
| 5 | Is infrastructure for data platforms managed as code (Terraform, Bicep, CDK)? | |
| 6 | How are configuration and secrets managed across environments? | |
| 7 | Are data pipeline observability signals (logs, metrics, traces) centralized and actionable? | |
| 8 | What is the mean time to detect (MTTD) and mean time to recover (MTTR) for data pipeline incidents? | |
| 9 | Is there a documented on-call or incident response process for data platform issues? | |
| 10 | How are cross-team dependencies and shared data assets coordinated during development and deployment? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| CI/CD Tool(s) | |
| Source Control Platform | |
| Infrastructure-as-Code Tool | |
| Observability / Monitoring Tool | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 2.5 ML / AI Model Management

ML/AI model management covers the lifecycle of machine learning and AI models from development and training through deployment, monitoring, and retirement.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there an ML platform or MLOps tooling in place (e.g., MLflow, SageMaker, Azure ML, Vertex AI, Databricks MLflow)? | |
| 2 | How are ML experiments tracked (hyperparameters, metrics, datasets, code versions)? | |
| 3 | Is model training automated and triggered by new data or scheduled retraining? | |
| 4 | How are trained models versioned, stored, and registered (model registry)? | |
| 5 | What is the model deployment process (batch scoring, real-time inference, A/B testing, canary rollout)? | |
| 6 | How are deployed models monitored for performance degradation, data drift, and concept drift? | |
| 7 | Is there an approval / governance process before a model goes to production? | |
| 8 | How is model explainability and bias detection addressed? | |
| 9 | What is the process for retiring or replacing a model in production? | |
| 10 | Are feature engineering artifacts (feature stores) shared and reused across models? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| ML Platform / MLOps Tool(s) | |
| Feature Store | |
| Model Registry | |
| Inference / Serving Platform | |
| Number of Models in Production | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 2.6 Self-Service Data Preparation

Self-service data preparation covers the ability for business analysts and non-engineers to prepare, blend, and enrich datasets independently.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are self-service data preparation tools available (e.g., Alteryx, Trifacta/Dataprep, Power Query, Databricks notebooks)? | |
| 2 | What percentage of business users actively use self-service preparation tools? | |
| 3 | How is data access controlled in self-service tools to prevent unauthorized access to sensitive data? | |
| 4 | Are datasets prepared by business users governed and made available for reuse, or siloed? | |
| 5 | Is there a process to promote high-quality, business-prepared datasets into the curated data layer? | |
| 6 | What training and support is provided to business users for data preparation? | |
| 7 | How are resource limits (compute, storage quotas) enforced in self-service environments? | |
| 8 | Is data lineage tracked for self-service prepared datasets? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Number of Active Business Users | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |

---

## 2.7 Data Product Management

Data product management covers treating data assets as products – with defined owners, consumers, SLAs, documentation, and a lifecycle.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is the concept of a "data product" formally defined and adopted in the organization? | |
| 2 | Are data products assigned product owners accountable for quality, availability, and consumer experience? | |
| 3 | Are data products documented with schemas, SLAs, freshness guarantees, and usage examples? | |
| 4 | Is there a catalog or marketplace where data products are discoverable by potential consumers? | |
| 5 | How are data product consumer requests and feedback managed? | |
| 6 | Are data product health metrics (uptime, freshness, quality score, consumer count) tracked and published? | |
| 7 | Is there a defined process for publishing, versioning, deprecating, and retiring data products? | |
| 8 | Are data contracts used to formalize agreements between data product producers and consumers? | |
| 9 | How are cross-domain data product dependencies identified and managed? | |
| 10 | Is there a data mesh or domain ownership model governing data product responsibilities? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Data Product Platform / Registry | |
| Data Contract Tooling | |
| Number of Published Data Products | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
