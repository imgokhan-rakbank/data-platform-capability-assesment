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
| 1 | Is a centralized monitoring platform in use (e.g., Datadog, Grafana/Prometheus, Azure Monitor, CloudWatch, Dynatrace)? | No centralized monitoring platform exists. |
| 2 | Are infrastructure metrics (CPU, memory, disk, network) collected and visualized for all data platform components? | No infrastructure metrics are collected or visualized centrally. |
| 3 | Are application-level metrics (pipeline throughput, query performance, job success rates) monitored? | No application-level metrics are monitored. |
| 4 | Are logs centralized in a log aggregation platform (e.g., ELK, Splunk, Azure Log Analytics)? Are they searchable and retained per policy? | No centralized log aggregation. |
| 5 | Are distributed traces captured for data pipeline flows to identify latency bottlenecks? | No distributed tracing is implemented. |
| 6 | Are alerting rules defined with clear severity levels, thresholds, and escalation paths? | No alerting rules are defined. |
| 7 | Is there a single operational dashboard ("single pane of glass") for the health of the entire data platform? | No operational dashboard exists. |
| 8 | Are uptime and availability SLAs defined for platform components, and is compliance reported regularly? | No SLAs are defined or tracked. |
| 9 | Is there capacity planning and trend-based forecasting to proactively address resource constraints? | No capacity planning or forecasting. |
| 10 | Are on-call schedules and incident response runbooks in place and tested? | No on-call schedules or incident response runbooks. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Monitoring Platform | None |
| Log Management Platform | None |
| Tracing / APM Tool | None |
| Alerting Tool | None |
| Known Gaps / Limitations | No centralized monitoring, no SLAs, no alerting, no dashboards, no forecasting. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No monitoring processes or SLAs defined. |
| Tooling Maturity | 1 | No monitoring or alerting tools in place. |
| People / Skills | 1 | No monitoring or observability skills established. |
| Automation Level | 1 | No automation for monitoring or alerting. |
| **Sub-Area Overall** | 1 | Platform monitoring is absent; no process, tooling, or automation. |

---

## 8.2 Automation & CI/CD

Automation and CI/CD covers the use of automated pipelines to build, test, and deploy data platform components, infrastructure, and code changes.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is CI/CD implemented for all data pipeline and platform code (ETL, transformation, IaC, configuration)? | CI/CD for data pipelines is lacking; deployments and integrations require manual engineer actions. |
| 2 | What CI/CD platform is used (e.g., GitHub Actions, Azure DevOps, GitLab CI, Jenkins)? | No standardized CI/CD platform for data pipelines. |
| 3 | Are automated tests (unit, integration, data contract) executed as part of every CI pipeline? | No automated tests are executed in CI pipelines. |
| 4 | Is deployment to production fully automated, or does it require manual approval gates? If manual, are approvals tracked? | Deployments are not automated; manual actions are required. |
| 5 | Is infrastructure provisioned and managed as code (Terraform, Bicep, Pulumi, CDK)? | Automation for platform component provisioning exists but is basic. |
| 6 | Are secrets and credentials managed securely in CI/CD pipelines (no hardcoded credentials)? | No secure secrets management in CI/CD. |
| 7 | What is the average lead time from code commit to production deployment for a typical data pipeline change? | Lead time is long due to manual steps. |
| 8 | Is there a deployment rollback mechanism if a release causes issues? | No automated rollback mechanism. |
| 9 | Are environment configurations (dev / test / prod) managed consistently and promoted through the pipeline? | Environment configurations are not managed or promoted consistently. |
| 10 | Are CI/CD pipeline health and deployment metrics tracked (deployment frequency, failure rate, MTTR)? | No metrics are tracked for CI/CD pipeline health or deployments. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| CI/CD Platform | None for data pipelines |
| Source Control Platform | Exists, but not integrated with CI/CD for pipelines |
| IaC Tooling | Basic automation for platform provisioning |
| Secret Management Tool | None |
| Known Gaps / Limitations | CI/CD lacking for pipelines, manual deployments, no automation for data integrations, no secure secrets management. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No formal CI/CD process for data pipelines; manual steps dominate. |
| Tooling Maturity | 2 | Basic automation for platform provisioning; no CI/CD for pipelines. |
| People / Skills | 1 | Limited CI/CD and automation skills for data pipelines. |
| Automation Level | 1 | Minimal automation; most steps are manual. |
| **Sub-Area Overall** | 1 | Automation and CI/CD are lacking for data pipelines; only basic provisioning automation exists. |

---

## 8.3 Development Practices

Development practices cover the standards, processes, and culture used by data engineers, analysts, and scientists when building and maintaining data platform components.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined development workflow and branching strategy (e.g., Git Flow, trunk-based development)? | No defined development workflow or branching strategy. |
| 2 | Is peer code review mandatory for all data platform changes? Are review standards documented? | Peer review is not mandatory; standards are not documented. |
| 3 | Are coding standards and style guides defined for SQL, Python, Spark, and other languages in use? Are linters enforced? | No coding standards or linters are defined or enforced. |
| 4 | Is there a test-driven development (TDD) or test-first culture for data pipelines? What test coverage exists? | No TDD or test-first culture; test coverage is minimal. |
| 5 | Is documentation produced and maintained alongside code (inline comments, README, data dictionaries, runbooks)? | No documentation is maintained for shared libraries or frameworks. |
| 6 | Are shared libraries, utilities, and templates available to reduce duplication across teams? | Shared libraries exist at a basic level, but are not documented. |
| 7 | Is there an inner-source or reuse culture – teams contributing shared components to a central repository? | No framework exists and engineers are not contributing to shared components. |
| 8 | Are technical debt and deprecation backlogs actively managed? | No active management of technical debt or deprecation. |
| 9 | Do data engineers participate in architectural design reviews for new data platform components? | No formal participation in design reviews. |
| 10 | Is there an onboarding process for new team members that covers development standards, tooling, and platform architecture? | No onboarding process for development standards or tooling. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Source Control Platform | Exists, but not governed or standardized |
| Code Review Process | Not mandatory; no standards |
| Linting / Static Analysis Tools | None |
| Documentation Platform | None for shared libraries or frameworks |
| Known Gaps / Limitations | No standards, no documentation, basic shared libraries, no framework, no engineer contribution. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No defined development process, standards, or onboarding. |
| Tooling Maturity | 2 | Basic shared libraries exist, but no framework or supporting tools. |
| People / Skills | 1 | Engineers are not contributing to shared components; limited skills development. |
| Automation Level | 1 | No automation for development practices. |
| **Sub-Area Overall** | 1 | Development practices are ad hoc; no standards, documentation, or framework. |
