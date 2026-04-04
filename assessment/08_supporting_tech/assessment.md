# Domain 8: Supporting Technology – Capability Assessment

> **Assessor(s):**  
> **Date:**  
> **Version:**  

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 8.1 Platform Monitoring

Platform monitoring covers the observability of infrastructure, services, and platform components that underpin the data platform.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a centralized monitoring platform in use (e.g., Datadog, Grafana/Prometheus, Azure Monitor, CloudWatch, Dynatrace)? | |
| 2 | Are infrastructure metrics (CPU, memory, disk, network) collected and visualized for all data platform components? | |
| 3 | Are application-level metrics (pipeline throughput, query performance, job success rates) monitored? | |
| 4 | Are logs centralized in a log aggregation platform (e.g., ELK, Splunk, Azure Log Analytics)? Are they searchable and retained per policy? | |
| 5 | Are distributed traces captured for data pipeline flows to identify latency bottlenecks? | |
| 6 | Are alerting rules defined with clear severity levels, thresholds, and escalation paths? | |
| 7 | Is there a single operational dashboard ("single pane of glass") for the health of the entire data platform? | |
| 8 | Are uptime and availability SLAs defined for platform components, and is compliance reported regularly? | |
| 9 | Is there capacity planning and trend-based forecasting to proactively address resource constraints? | |
| 10 | Are on-call schedules and incident response runbooks in place and tested? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Monitoring Platform | |
| Log Management Platform | |
| Tracing / APM Tool | |
| Alerting Tool | |
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

## 8.2 Automation & CI/CD

Automation and CI/CD covers the use of automated pipelines to build, test, and deploy data platform components, infrastructure, and code changes.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is CI/CD implemented for all data pipeline and platform code (ETL, transformation, IaC, configuration)? | |
| 2 | What CI/CD platform is used (e.g., GitHub Actions, Azure DevOps, GitLab CI, Jenkins)? | |
| 3 | Are automated tests (unit, integration, data contract) executed as part of every CI pipeline? | |
| 4 | Is deployment to production fully automated, or does it require manual approval gates? If manual, are approvals tracked? | |
| 5 | Is infrastructure provisioned and managed as code (Terraform, Bicep, Pulumi, CDK)? | |
| 6 | Are secrets and credentials managed securely in CI/CD pipelines (no hardcoded credentials)? | |
| 7 | What is the average lead time from code commit to production deployment for a typical data pipeline change? | |
| 8 | Is there a deployment rollback mechanism if a release causes issues? | |
| 9 | Are environment configurations (dev / test / prod) managed consistently and promoted through the pipeline? | |
| 10 | Are CI/CD pipeline health and deployment metrics tracked (deployment frequency, failure rate, MTTR)? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| CI/CD Platform | |
| Source Control Platform | |
| IaC Tooling | |
| Secret Management Tool | |
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

## 8.3 Development Practices

Development practices cover the standards, processes, and culture used by data engineers, analysts, and scientists when building and maintaining data platform components.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined development workflow and branching strategy (e.g., Git Flow, trunk-based development)? | |
| 2 | Is peer code review mandatory for all data platform changes? Are review standards documented? | |
| 3 | Are coding standards and style guides defined for SQL, Python, Spark, and other languages in use? Are linters enforced? | |
| 4 | Is there a test-driven development (TDD) or test-first culture for data pipelines? What test coverage exists? | |
| 5 | Is documentation produced and maintained alongside code (inline comments, README, data dictionaries, runbooks)? | |
| 6 | Are shared libraries, utilities, and templates available to reduce duplication across teams? | |
| 7 | Is there an inner-source or reuse culture – teams contributing shared components to a central repository? | |
| 8 | Are technical debt and deprecation backlogs actively managed? | |
| 9 | Do data engineers participate in architectural design reviews for new data platform components? | |
| 10 | Is there an onboarding process for new team members that covers development standards, tooling, and platform architecture? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Source Control Platform | |
| Code Review Process | |
| Linting / Static Analysis Tools | |
| Documentation Platform | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
