# Domain 1: Data Integration – Capability Assessment

> **Assessor(s):**  
> **Date:**  
> **Version:**  

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 1.1 Event-Driven Integration

Event-driven integration covers the capture and propagation of data changes or business events in real-time or near-real-time using messaging, streaming, or change-data-capture (CDC) patterns.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Do you have a defined event-driven architecture or messaging backbone (e.g., Kafka, Pulsar, Azure Event Hubs, AWS Kinesis)? If yes, describe its scope and usage. | |
| 2 | Is Change Data Capture (CDC) implemented for source systems? Which tools are used (e.g., Debezium, Oracle GoldenGate, AWS DMS)? How many source systems are covered? | |
| 3 | How are event schemas defined, versioned, and governed (e.g., schema registry, Avro, Protobuf, JSON Schema)? | |
| 4 | What is the typical end-to-end latency from source event to consumer availability? Is this measured and monitored? | |
| 5 | How are event ordering, deduplication, and exactly-once delivery guarantees handled? | |
| 6 | Is there an event catalog or inventory of published topics/streams available to consumers? | |
| 7 | How are failed events handled (dead-letter queues, retry policies, alerting)? | |
| 8 | What is the current throughput capacity and has it been load-tested? | |
| 9 | Are event consumers decoupled from producers (pub/sub model)? How many active consumer groups exist? | |
| 10 | Is event-driven integration covered by automated tests and deployment pipelines? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model (cloud / on-prem / hybrid) | |
| Key Source Systems Connected | |
| Key Consumer Systems | |
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

## 1.2 Batch-Driven Integration

Batch-driven integration covers scheduled or triggered bulk data movement between systems (ETL/ELT pipelines, file-based transfers, database extracts).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What ETL/ELT tools or frameworks are in use (e.g., Informatica, SSIS, dbt, Glue, ADF, Spark)? | |
| 2 | How many batch pipelines exist today? Are they inventoried and documented? | |
| 3 | What is the standard scheduling mechanism (e.g., cron, Airflow, Control-M)? Is it centralized? | |
| 4 | How are batch job failures detected, alerted, and restarted? What is the SLA for resolution? | |
| 5 | Is incremental loading (delta/watermark) used, or are full-load extracts the default? | |
| 6 | How is data transformation logic versioned and promoted across environments (dev/test/prod)? | |
| 7 | Are pipeline dependencies and execution order managed (e.g., dependency graphs, wait conditions)? | |
| 8 | What is the longest-running batch job and does it meet its processing window SLA? | |
| 9 | Is batch throughput and performance monitored? Are there auto-scaling or parallelism capabilities? | |
| 10 | How are source schema changes (breaking changes) detected and handled in batch pipelines? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model (cloud / on-prem / hybrid) | |
| Number of Active Pipelines | |
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

## 1.3 API-Driven Integration

API-driven integration covers the consumption and exposure of data via REST, GraphQL, gRPC, or SOAP APIs.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there an API gateway or management platform (e.g., Apigee, MuleSoft, Azure API Management, Kong) governing data APIs? | |
| 2 | How are data APIs versioned, documented (e.g., OpenAPI/Swagger), and published to consumers? | |
| 3 | What authentication and authorization mechanisms are applied to data APIs (OAuth2, API keys, mTLS)? | |
| 4 | Are API usage metrics (call volumes, error rates, latency) monitored and available to teams? | |
| 5 | How are rate limiting, throttling, and quota management enforced? | |
| 6 | Is there a self-service process for teams to discover and subscribe to data APIs? | |
| 7 | How are breaking API changes communicated and managed (deprecation policy, versioning strategy)? | |
| 8 | Are data APIs tested automatically (contract tests, integration tests) as part of the deployment pipeline? | |
| 9 | What is the API availability SLA and how is it tracked? | |
| 10 | Are external (third-party) data APIs consumed? How are those dependencies managed and monitored? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Number of Published Data APIs | |
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

## 1.4 Data Virtualization

Data virtualization covers the ability to provide a unified, real-time logical view of data across disparate sources without physical data movement.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data virtualization layer in place (e.g., Denodo, Dremio, Starburst/Trino, Azure Synapse external tables)? | |
| 2 | How many source systems are accessible via virtualization today? | |
| 3 | How is query performance managed across federated sources (caching, query pushdown, materialized views)? | |
| 4 | How are access permissions and data security enforced within the virtualization layer? | |
| 5 | How is the virtualized layer governed (logical data model, semantic layer, business glossary mapping)? | |
| 6 | Are there SLAs defined for virtualized query performance? How are they monitored? | |
| 7 | What is the process for onboarding a new source system into the virtualization layer? | |
| 8 | Is data virtualization used for self-service analytics? If so, how widely adopted is it? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Number of Federated Sources | |
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

## 1.5 Orchestration

Orchestration covers the coordination, scheduling, and dependency management of data workflows and pipelines.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What orchestration platform(s) are used (e.g., Apache Airflow, Azure Data Factory, Prefect, Dagster, Control-M)? | |
| 2 | Is there a single, centralized orchestration platform, or are multiple tools used independently by different teams? | |
| 3 | How are pipeline dependencies (upstream/downstream) defined and enforced in the orchestrator? | |
| 4 | How are retries, backfill, and SLA breach alerts handled? | |
| 5 | Is the orchestration platform itself highly available and scalable? What is its uptime record? | |
| 6 | How are workflow definitions version-controlled and deployed (GitOps, CI/CD)? | |
| 7 | Can non-technical users create or modify workflows, or is this restricted to engineers? | |
| 8 | How many active DAGs / workflows are managed today? Is there a process for decommissioning unused pipelines? | |
| 9 | Are cross-domain or cross-team pipeline dependencies handled, and if so, how? | |
| 10 | Is there a unified operational dashboard showing the health of all orchestrated workflows? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Number of Active Workflows / DAGs | |
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

## 1.6 Data Automation

Data automation covers the use of automated mechanisms (triggers, event-based execution, low-code tools) to reduce manual intervention in data pipeline operations.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | What percentage of data pipelines run fully automatically without manual intervention? | |
| 2 | Are there self-healing mechanisms in place (auto-restart, auto-scale, auto-remediation on failure)? | |
| 3 | Is infrastructure for data pipelines provisioned automatically (IaC – Terraform, Bicep, CloudFormation)? | |
| 4 | Are new pipeline deployments fully automated via CI/CD (testing, promotion, deployment)? | |
| 5 | How are configuration and secret management handled for automated pipelines? | |
| 6 | Are data contracts or SLAs enforced automatically with notifications when breached? | |
| 7 | Is there event-triggered automation (e.g., file arrival triggers a pipeline, a quality failure triggers an alert)? | |
| 8 | How is automation coverage measured? Are there gaps being tracked and prioritized? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
