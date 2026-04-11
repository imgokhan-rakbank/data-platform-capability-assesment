# Domain 1: Data Integration – Capability Assessment

> **Assessor(s):**  Gokhan Imral
> **Date:**  11/04/2026
> **Version:**  1.0

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 1.1 Event-Driven Integration

Event-driven integration covers the capture and propagation of data changes or business events in real-time or near-real-time using messaging, streaming, or change-data-capture (CDC) patterns.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Do you have a defined event-driven architecture or messaging backbone (e.g., Kafka, Pulsar, Azure Event Hubs, AWS Kinesis)? If yes, describe its scope and usage. |Confluent Cloud is used as messaging backbone. Currently it is used to collect source database changes.|
| 2 | Is Change Data Capture (CDC) implemented for source systems? Which tools are used (e.g., Debezium, Oracle GoldenGate, AWS DMS)? How many source systems are covered? |Debezium offering from Confluent is used. Primary DB instances of the source systems are used, there have been a few times this caused problems with the source system as the database log were not getting truncated. |
| 3 | How are event schemas defined, versioned, and governed (e.g., schema registry, Avro, Protobuf, JSON Schema)? |COnfluent schema registry is used. Currently only raw events are stored in Kafka. Schemas are automatically creaetd/managed by the CDC connectors. |
| 4 | What is the typical end-to-end latency from source event to consumer availability? Is this measured and monitored? |It is not measured or monitored. |
| 5 | How are event ordering, deduplication, and exactly-once delivery guarantees handled? |There is no process handling this. At least once delivery is the expectation. |
| 6 | Is there an event catalog or inventory of published topics/streams available to consumers? |No there is not. Events are only at the database raw extract level and used to feed to the Databricks directly. |
| 7 | How are failed events handled (dead-letter queues, retry policies, alerting)? |Confluent Debezium mechanisms are used for DQL,retries. No conphrehensive alerting or monitoring exists. |
| 8 | What is the current throughput capacity and has it been load-tested? |It is not tested but load is very light compared to what Confluent can handle. |
| 9 | Are event consumers decoupled from producers (pub/sub model)? How many active consumer groups exist? |No consumers other than Data platform ingestion to Databricks exists. |
| 10 | Is event-driven integration covered by automated tests and deployment pipelines? |No |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |Confluent Cloud Kafka, Confluent Debezium Connectors,Confluent FlinkSQL |
| Vendor / Version |Confluent |
| Deployment Model (cloud / on-prem / hybrid) |Cloud |
| Key Source Systems Connected |Core banking, Card System, BPM, etc. 20+ systems are integrated |
| Key Consumer Systems |Databricks |
| Known Gaps / Limitations |Primary instance of source systems are integrated. Monitoring is lacking. End to End automation is lacking. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |2 |Process of onboarding new integration is not documented/well defined nor communicated. |
| Tooling Maturity |3 |Confluent Cloud is a SAAS fully managed solution, supports out of the box support for most of the non functional requirements. But no monitoring tooling exists. |
| People / Skills |3 |There are not enough people supporting the core platform. Confluent support is very useful covering any gaps that are in the skills. |
| Automation Level |2 |While there is automation for some operations, it is very primitive and does not cover whole process and requires human approvals creating botlenecks  |
| **Sub-Area Overall** |2 |Confluent Cloud is a mature SAAS solution. CDC solutions are not reliable and lacking monitoring will make it unreliable. Automation needs to be increased to have a more streamlined process. |

---

## 1.2 Batch-Driven Integration

Batch-driven integration covers scheduled or triggered bulk data movement between systems (ETL/ELT pipelines, file-based transfers, database extracts).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What ETL/ELT tools or frameworks are in use (e.g., Informatica, SSIS, dbt, Glue, ADF, Spark)? |ADF is used. |
| 2 | How many batch pipelines exist today? Are they inventoried and documented? |They are not documented nor invertoried. |
| 3 | What is the standard scheduling mechanism (e.g., cron, Airflow, Control-M)? Is it centralized? |ADF triggers are used in addition to the databricks scheduling, no standards exists.  |
| 4 | How are batch job failures detected, alerted, and restarted? What is the SLA for resolution? |No compherensive monitoring exists. |
| 5 | Is incremental loading (delta/watermark) used, or are full-load extracts the default? |Full loads are common instead of the optimized incremental loads. |
| 6 | How is data transformation logic versioned and promoted across environments (dev/test/prod)? |There are environments but versioning or promotion is not documented, automated. |
| 7 | Are pipeline dependencies and execution order managed (e.g., dependency graphs, wait conditions)? |No it is not.No dependecy flagging exists. |
| 8 | What is the longest-running batch job and does it meet its processing window SLA? |There is no defined SLA. Job execution time are not monitored. |
| 9 | Is batch throughput and performance monitored? Are there auto-scaling or parallelism capabilities? |Jobs are not monitored. Databricks supports autoscaling and parallelism. |
| 10 | How are source schema changes (breaking changes) detected and handled in batch pipelines? |They are not handled. Ingestion happen in a schemaless manner using Databricks capabilities. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |ADF for data movement and scheduling, Databricks for storage. |
| Vendor / Version |Microsoft ADF, Databricks |
| Deployment Model (cloud / on-prem / hybrid) |Cloud |
| Number of Active Pipelines |100s |
| Known Gaps / Limitations |Monitoring is lacking, automation does not exist, process is not defined. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |2 |Process of onboarding new integration is not documented/well defined nor communicated. |
| Tooling Maturity |3 |ADF and Databricks are fully managed solutions, support out of the box support for most of the non functional requirements. But no monitoring tooling exists. |
| People / Skills |3 |There are not enough people supporting the core platform. Microsoft and Databricks support is very useful covering any gaps that are in the skills. |
| Automation Level |1 |There are no automation done for integrating new sources.|
| **Sub-Area Overall** |2 |ADF and Databricks does the heavy lifting but surronding supporting function does not exist. |

---

## 1.3 API-Driven Integration

API-driven integration covers the consumption and exposure of data via REST, GraphQL, gRPC, or SOAP APIs.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there an API gateway or management platform (e.g., Apigee, MuleSoft, Azure API Management, Kong) governing data APIs? | Kong is available at company level. |
| 2 | How are data APIs versioned, documented (e.g., OpenAPI/Swagger), and published to consumers? |No data APIs currently exists. No process is defined. |
| 3 | What authentication and authorization mechanisms are applied to data APIs (OAuth2, API keys, mTLS)? |No data APIs currently exists. No process is defined. |
| 4 | Are API usage metrics (call volumes, error rates, latency) monitored and available to teams? |No data APIs currently exists. No process is defined. |
| 5 | How are rate limiting, throttling, and quota management enforced? |No data APIs currently exists. No process is defined. |
| 6 | Is there a self-service process for teams to discover and subscribe to data APIs? |No data APIs currently exists. No process is defined. |
| 7 | How are breaking API changes communicated and managed (deprecation policy, versioning strategy)? | No data APIs currently exists. No process is defined.|
| 8 | Are data APIs tested automatically (contract tests, integration tests) as part of the deployment pipeline? |No data APIs currently exists. No process is defined. |
| 9 | What is the API availability SLA and how is it tracked? |No data APIs currently exists. No process is defined. |
| 10 | Are external (third-party) data APIs consumed? How are those dependencies managed and monitored? |There is an external API being consumed but pattern is not defined on it. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |No data APIs currently exists. No process is defined. |
| Vendor / Version |No data APIs currently exists. No process is defined. |
| Deployment Model |No data APIs currently exists. No process is defined. |
| Number of Published Data APIs |No data APIs currently exists. No process is defined. |
| Known Gaps / Limitations |No data APIs currently exists. No process is defined. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |1 |No data APIs currently exists. No process is defined. |
| Tooling Maturity |1 |No data APIs currently exists. No process is defined. |
| People / Skills |1 | No data APIs currently exists. No process is defined.|
| Automation Level |1 |No data APIs currently exists. No process is defined. |
| **Sub-Area Overall** |1 |No data APIs currently exists. No process is defined. |

---

## 1.4 Data Virtualization

Data virtualization covers the ability to provide a unified, real-time logical view of data across disparate sources without physical data movement.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data virtualization layer in place (e.g., Denodo, Dremio, Starburst/Trino, Azure Synapse external tables)? |No Virtualization solution exits.  |
| 2 | How many source systems are accessible via virtualization today? |No Virtualization solution exits. |
| 3 | How is query performance managed across federated sources (caching, query pushdown, materialized views)? |No Virtualization solution exits. |
| 4 | How are access permissions and data security enforced within the virtualization layer? |No Virtualization solution exits. |
| 5 | How is the virtualized layer governed (logical data model, semantic layer, business glossary mapping)? |No Virtualization solution exits. |
| 6 | Are there SLAs defined for virtualized query performance? How are they monitored? |No Virtualization solution exits. |
| 7 | What is the process for onboarding a new source system into the virtualization layer? |No Virtualization solution exits. |
| 8 | Is data virtualization used for self-service analytics? If so, how widely adopted is it? |No Virtualization solution exits. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |No Virtualization solution exits. |
| Vendor / Version |No Virtualization solution exits. |
| Deployment Model |No Virtualization solution exits. |
| Number of Federated Sources |No Virtualization solution exits. |
| Known Gaps / Limitations |No Virtualization solution exits. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |1 |No Virtualization solution exits. |
| Tooling Maturity |1 |No Virtualization solution exits. |
| People / Skills |1 |No Virtualization solution exits. |
| Automation Level |1 |No Virtualization solution exits. |
| **Sub-Area Overall** |1 |No Virtualization solution exits. |

---

## 1.5 Orchestration

Orchestration covers the coordination, scheduling, and dependency management of data workflows and pipelines.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What orchestration platform(s) are used (e.g., Apache Airflow, Azure Data Factory, Prefect, Dagster, Control-M)? |ADF and Databricks |
| 2 | Is there a single, centralized orchestration platform, or are multiple tools used independently by different teams? |There is not single tool and no clear defition of when to use what |
| 3 | How are pipeline dependencies (upstream/downstream) defined and enforced in the orchestrator? |They are not defined. |
| 4 | How are retries, backfill, and SLA breach alerts handled? |No monitoring exists, no backfill process is implemented. |
| 5 | Is the orchestration platform itself highly available and scalable? What is its uptime record? |It is HA. ADF and Databricks are managed cloud solutions. |
| 6 | How are workflow definitions version-controlled and deployed (GitOps, CI/CD)? |They are on git but no process exists definign the flow and no controls are baked into CI/CD |
| 7 | Can non-technical users create or modify workflows, or is this restricted to engineers? |No, only engineers. |
| 8 | How many active DAGs / workflows are managed today? Is there a process for decommissioning unused pipelines? |There is no process for decommissioning. Hundreds of workslows exists. |
| 9 | Are cross-domain or cross-team pipeline dependencies handled, and if so, how? |They are not handled. |
| 10 | Is there a unified operational dashboard showing the health of all orchestrated workflows? |No |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |ADF, Databricks |
| Vendor / Version |Microsoft, Databricks |
| Deployment Model |Cloud |
| Number of Active Workflows / DAGs |100s |
| Known Gaps / Limitations |No process is defined,no monitoring exists, no dependecy management |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |2 |Process of creatign new worklows is not documented/well defined nor communicated.  |
| Tooling Maturity |3 |ADF and Databricks are fully managed solutions, support out of the box support for most of the non functional requirements. But no monitoring tooling exists. |
| People / Skills |3 |There are not enough people supporting the core platform. Microsoft and Databricks support is very useful covering any gaps that are in the skills. |
| Automation Level |1 |There are no automation done for integrating new worklows. |
| **Sub-Area Overall** |2 |ADF and Databricks does the heavy lifting but surronding supporting function does not exist. |

---

## 1.6 Data Automation

Data automation covers the use of automated mechanisms (triggers, event-based execution, low-code tools) to reduce manual intervention in data pipeline operations.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What percentage of data pipelines run fully automatically without manual intervention? |All once it is setup |
| 2 | Are there self-healing mechanisms in place (auto-restart, auto-scale, auto-remediation on failure)? |Yes |
| 3 | Is infrastructure for data pipelines provisioned automatically (IaC – Terraform, Bicep, CloudFormation)? |Yes |
| 4 | Are new pipeline deployments fully automated via CI/CD (testing, promotion, deployment)? |No |
| 5 | How are configuration and secret management handled for automated pipelines? |They are defined on the worklow definitions |
| 6 | Are data contracts or SLAs enforced automatically with notifications when breached? |No |
| 7 | Is there event-triggered automation (e.g., file arrival triggers a pipeline, a quality failure triggers an alert)? |No |
| 8 | How is automation coverage measured? Are there gaps being tracked and prioritized? |No |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) |Github Actions is used for CI/CD, ADF is used for workflows |
| Vendor / Version |Microsoft |
| Deployment Model |Cloud |
| Known Gaps / Limitations |Self service data integration automation is lacking, no well defined process exists for promotions or development. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity |2 |Automation process is not well defined. |
| Tooling Maturity |3 |Github Actions and ADF are managed solution doing the heavy lifting but no monitoring, no streamlined triggering support exists |
| People / Skills |3 |There are not enough people supporting the core platform. Microsoft and Databricks support is very useful covering any gaps that are in the skills. |
| Automation Level |1 |No self service process exists automating the data integration completly. |
| **Sub-Area Overall** |2 |Github Actions does the heavy lifing but supporting automation pipelines and the process does not exist. |
