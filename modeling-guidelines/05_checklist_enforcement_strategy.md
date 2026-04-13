# LDM Compliance Checklist – Enforcement Strategy

> **Version:** 1.0  
> **Owner:** Data Architecture & Modelling  
> **Applies to:** All Medallion layer model changes  
> **Purpose:** Define how the LDM Compliance Checklist (`04_ldm_compliance_checklist.md`) is enforced in the development and deployment lifecycle  
> **Industry references:** GitOps principles, DevSecOps, dbt best practices, Databricks Unity Catalog policy, DAMA-DMBOK2 governance controls

---

## 1. Enforcement Philosophy

Enforcement must be **layered, graduated, and proportional to risk**:

| Layer | Nature | When | Who |
|---|---|---|---|
| **Prevention** | Stops the problem before it occurs | At development time (IDE, pre-commit, PR checks) | Automated tooling |
| **Detection** | Identifies violations after the fact | Continuous monitoring on deployed models | Automated monitoring |
| **Response** | Remediates violations | Triggered by detection; tracked in ADO/Jira | Data Architecture team + domain owners |

The order of enforcement priority is: **Prevent first → Detect second → Remediate third**. Relying only on post-hoc detection is insufficient; critical controls (SCD Type 2, naming conventions, surrogate keys) must be prevented at the source.

---

## 2. Tier 1 – Developer Environment (Prevention)

### 2.1 sqlfluff – Naming Convention Linter

**Purpose:** Catch naming convention violations before code is even committed.

**Configuration file:** `.sqlfluff` in the repository root.

**Rules to configure:**

| Violation | sqlfluff Rule |
|---|---|
| Non-snake_case column or table names | `L014` (capitalisation of identifiers) |
| Trailing whitespace | `L001` |
| Missing `snake_case` for column aliases | `L014` |
| Reserved words used as identifiers | `L029` |

**Custom dbt rules (via sqlfluff-templater-dbt):**

```ini
# .sqlfluff
[sqlfluff]
templater = dbt
dialect = sparksql
max_line_length = 120

[sqlfluff:rules:L014]
extended_capitalisation_policy = lower
```

**Enforcement point:** Pre-commit hook (see Section 2.3). Developers get instant feedback at commit time, not at PR review time.

---

### 2.2 dbt Model Structure Checks (dbt-project-evaluator)

**Purpose:** Enforce dbt model structure and documentation standards.

**Tool:** [`dbt-project-evaluator`](https://github.com/dbt-labs/dbt-project-evaluator) — an open-source dbt package that tests dbt project hygiene.

**Checks to enable:**

| Check | Rule | Checklist Controls Covered |
|---|---|---|
| All models have a description | `fct_missing_documentation` | 0.3.1, 0.3.2 |
| All models have at least one test | `fct_missing_primary_key_tests` | S.6.1, G.5.1 |
| Sources have documentation | `fct_undocumented_sources` | B.4.2 |
| Models have an owner defined in `meta` | Custom check | 0.1.4 |
| No model has more than N direct parent models (limits spaghetti lineage) | `fct_model_fanout` | Architecture conformance |

**Configuration in `packages.yml`:**

```yaml
packages:
  - package: dbt-labs/dbt_project_evaluator
    version: [">=0.8.0", "<1.0.0"]
```

**Enforcement point:** Run in CI (Section 3) and optionally as a pre-commit hook.

---

### 2.3 Pre-commit Hooks

**Purpose:** Block non-compliant code from being committed locally.

**Tool:** [`pre-commit`](https://pre-commit.com/) framework.

**`.pre-commit-config.yaml`:**

```yaml
repos:
  - repo: https://github.com/sqlfluff/sqlfluff
    rev: 3.0.0
    hooks:
      - id: sqlfluff-lint
        args: [--dialect, sparksql]

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json

  - repo: local
    hooks:
      - id: check-bronze-technical-columns
        name: Verify Bronze tables have required technical columns
        entry: scripts/check_bronze_columns.py
        language: python
        files: '^models/bronze/.*\.sql$'

      - id: check-silver-scd-columns
        name: Verify Silver Type 2 tables have SCD columns
        entry: scripts/check_scd_columns.py
        language: python
        files: '^models/silver/.*\.sql$'

      - id: check-naming-convention
        name: Verify naming conventions (snake_case, suffixes)
        entry: scripts/check_naming.py
        language: python
        files: '^models/.*\.sql$'
```

**Custom scripts location:** `scripts/pre-commit/` — Python scripts that parse dbt model DDL and schema YAML to validate specific checklist items.

---

### 2.4 dbt Schema YAML Template

**Purpose:** Ensure every new dbt model starts with all required metadata populated.

Provide a Jinja template `templates/new_model_schema.yml` that engineers copy and fill in when creating a new model. The template pre-populates all required fields with `TODO:` placeholders so that missing documentation is visually obvious.

```yaml
# templates/new_model_schema.yml
models:
  - name: TODO_entity_name
    description: "TODO: Describe this entity. Grain: one row per [X] per [Y]."
    meta:
      layer: TODO  # bronze | silver_ods | silver_analytical | gold
      domain: TODO
      owner: TODO
      scd_type: TODO  # 0 | 1 | 2
      certification_status: draft
    columns:
      - name: TODO_entity_key
        description: "SHA2-256 surrogate key. Hash inputs: [list key columns here]."
        tests:
          - unique
          - not_null
      - name: effective_from_date
        description: "Business effective start date of this version."
        tests:
          - not_null
      - name: effective_to_date
        description: "Business effective end date. '9999-12-31' for current version."
        tests:
          - not_null
      - name: is_current
        description: "true if this is the current (latest) version."
        tests:
          - not_null
          - accepted_values:
              values: [true, false]
```

---

## 3. Tier 2 – Pull Request Checks (Prevention + Detection)

### 3.1 CI Pipeline – dbt Test Suite

**Purpose:** Every PR that changes a model triggers automated tests before merge.

**Orchestration:** Azure DevOps Pipelines or GitHub Actions.

**Pipeline steps:**

```yaml
# .github/workflows/model-ci.yml (GitHub Actions example)
name: Data Model CI
on:
  pull_request:
    paths:
      - 'models/**'
      - 'macros/**'
      - '*.yml'

jobs:
  dbt-ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install dbt-databricks dbt-project-evaluator sqlfluff

      - name: sqlfluff lint
        run: sqlfluff lint models/ --dialect sparksql --fail-fast

      - name: dbt deps
        run: dbt deps

      - name: dbt compile
        run: dbt compile --target ci

      - name: dbt project evaluator
        run: dbt build --select package:dbt_project_evaluator

      - name: dbt test (changed models only)
        run: |
          dbt test --select state:modified+ --defer --state prod-manifest/

      - name: Check compliance checklist metadata
        run: python scripts/ci/validate_checklist_metadata.py

      - name: Breaking change detection
        run: python scripts/ci/detect_breaking_changes.py
```

### 3.2 Breaking Change Detection

**Purpose:** Automatically flag PRs that contain breaking schema changes (column rename, type change, column removal) so they cannot be merged silently.

**Implementation approach:**

```python
# scripts/ci/detect_breaking_changes.py
# Compares the current PR's compiled DDL against the production manifest
# Flags: column removals, type changes, renames (removal + addition of same semantic column)
# If breaking changes detected: sets PR label "breaking-change", 
# posts a PR comment listing the broken columns and impacted downstream models
# Pipeline FAILS if breaking changes detected without an approved ARB waiver in PR description
```

**PR label enforcement:** Require the `breaking-change` label to trigger mandatory ARB reviewer assignment in the `CODEOWNERS` file:

```
# .github/CODEOWNERS
models/silver/  @data-architecture-team
models/gold/    @data-architecture-team @data-domain-stewards
# Breaking changes require additional review:
# (enforced via PR label rules in branch protection settings)
```

### 3.3 PR Description Checklist Template

**Purpose:** Ensure every model PR has a structured description that maps to the compliance checklist.

**GitHub/ADO PR template (`.github/pull_request_template.md`):**

```markdown
## Model Change Summary

**Layer affected:** Bronze / Silver ODS / Silver Analytical / Gold (delete as appropriate)

**Type of change:** New entity / New attribute / Schema change / Breaking change / Metric addition

**Work item:** ADO/Jira link

---

## LDM Compliance Checklist – Quick Declaration

*Self-assessed by the author. Automated CI checks enforce the technical items.*

### All Changes
- [ ] Table/column naming follows snake_case and suffix conventions (Section 0.3)
- [ ] Unity Catalog table comment and column comments populated (Sections 0.3.1, 0.3.2)
- [ ] Data classification tags applied to PII/sensitive columns (Section 0.4.1)
- [ ] Change impact assessed using Unity Catalog lineage; downstream impacts listed below (Section 0.2.5)

### Silver Analytical Changes (fill in if applicable)
- [ ] SCD type declared in table comment (Section S.3.1)
- [ ] If SCD Type 2: `effective_from_date`, `effective_to_date = '9999-12-31'`, `is_current` present (Section S.3.3, S.3.4)
- [ ] SHA2-256 surrogate key generated; key manifest file updated (Section S.2.2, S.2.3)
- [ ] All 6 mandatory audit columns present (Section S.4)
- [ ] DQ tests defined for uniqueness, not-null, RI (Section S.6.1–S.6.3)

### Gold Model Changes (fill in if applicable)
- [ ] Grain declared in table comment (Section G.2.1)
- [ ] All new metrics registered in Metrics Catalogue (Section G.3.1)
- [ ] Financial reconciliation to Silver completed (Section G.5.2)
- [ ] Business owner sign-off obtained (Section G.5.3)
- [ ] If regulatory: restatement immutability confirmed (Section G.6.1)

### Breaking Change Declaration (fill in if breaking)
- [ ] This IS / IS NOT a breaking change (delete as appropriate)
- [ ] ARB waiver reference (if breaking): ___________
- [ ] Consumer notification sent on (date): ___________
- [ ] All downstream consumers confirmed updated: Yes / No / In progress

---

## Downstream Impact

| Downstream Model | Consumer Team | Impact | Mitigation |
|---|---|---|---|
| | | | |

---

## Evidence Attached

- [ ] dbt test results screenshot / CI link
- [ ] Unity Catalog screenshot of populated metadata
- [ ] Reconciliation results (Gold models only)
```

---

## 4. Tier 3 – Deployment Gate (Prevention)

### 4.1 Databricks Asset Bundle Policy Checks

**Purpose:** Prevent deployment to production if the model fails declared deployment checks.

**Implementation:** Use [Databricks Asset Bundles](https://docs.databricks.com/en/dev-tools/bundles/index.html) with a `pre-deploy` hook that:
1. Validates that all Unity Catalog metadata (table comments, column comments) are populated for models being deployed
2. Validates that the model exists in the compliance checklist sign-off register
3. Validates that the model has passed dbt tests in the staging environment

```yaml
# databricks.yml (Asset Bundle)
bundle:
  name: data-platform

targets:
  prod:
    pre-deploy:
      - sh: python scripts/deployment/validate_compliance_signoff.py --env prod
      - sh: python scripts/deployment/validate_unity_catalog_metadata.py --env prod
```

### 4.2 Unity Catalog Policy Enforcement

**Purpose:** Use Unity Catalog table properties and tags as enforcement signals.

| Policy | Implementation |
|---|---|
| Uncertified Gold models inaccessible to BI tools | Unity Catalog: revoke `SELECT` grant on `gold_*` schemas for `analyst` role until model has `certified = true` property set by the data architect (not the model author) |
| Bronze write access locked to ingestion service principals | Unity Catalog: `GRANT WRITE PRIVILEGE ON SCHEMA bronze.* TO ingestion_sp ONLY` |
| Sensitive column masking | Unity Catalog column masking functions applied to PII columns; masking is deployed alongside the model as part of the CI/CD pipeline |
| Required table properties | Custom Unity Catalog table property `ldm_compliance_version` set to the checklist version used; blocks downstream grants if absent |

---

## 5. Tier 4 – Continuous Monitoring (Detection)

### 5.1 dbt Model Health Dashboard

**Purpose:** Continuous monitoring of the deployed model estate for compliance drift.

**Implementation:** A scheduled dbt job (daily) that runs a set of `dbt_audit_helper` and custom compliance models that produce a `compliance_dashboard` Gold schema:

```
gold_control.model_compliance_score
gold_control.column_documentation_coverage
gold_control.scd_compliance_audit
gold_control.naming_convention_audit
gold_control.dq_test_coverage
gold_control.certification_register
gold_control.reconciliation_log
```

**Key metrics to track:**

| Metric | Target | Alert Threshold |
|---|---|---|
| Column documentation coverage | 100% | < 95% triggers alert |
| SCD Type 2 coverage for core entities | 100% | < 100% triggers critical alert |
| Surrogate key adoption (Silver Analytical) | 100% | < 100% triggers critical alert |
| Naming convention compliance | 100% | < 98% triggers alert |
| DQ test coverage (models with at least 1 test) | 100% | < 100% triggers alert |
| Gold model certification rate | 100% for production | Any uncertified model in prod triggers alert |

### 5.2 Schema Drift Detection

**Purpose:** Detect when the physical schema deployed in a Databricks catalog drifts from the expected schema defined in dbt (or the logical model).

**Implementation:**

```python
# scripts/monitoring/detect_schema_drift.py
# Runs on a schedule (e.g., hourly for Silver, daily for Gold)
# Compares: Unity Catalog information_schema.columns vs. dbt catalog.json
# Alerts on:
#   - New column in production not in dbt model definition
#   - Column present in dbt definition missing from production
#   - Data type mismatch between dbt and production
# Publishes results to: gold_control.schema_drift_log
# Sends alert to: data-platform-alerts Slack channel / email distribution list
```

### 5.3 Data Quality Monitoring

**Purpose:** Ensure DQ checks continue to pass in production after deployment.

**Implementation:** Use **Databricks Lakehouse Monitoring** or **Elementary** (dbt observability package) to:
- Track DQ test pass/fail rates over time per model
- Alert when a previously passing DQ test starts failing
- Dashboard: `gold_control.dq_test_history` with daily snapshots

**Alert routing:**

| Alert Type | Severity | Channel |
|---|---|---|
| CRITICAL DQ test failure (PK uniqueness, not-null on mandatory column) | P1 | PagerDuty + Slack `#data-platform-critical` |
| HIGH DQ test failure (RI check, accepted values) | P2 | Slack `#data-platform-alerts` + email to data owner |
| Schema drift detected | P2 | Slack `#data-platform-alerts` |
| Documentation coverage below threshold | P3 | Daily digest email to data architecture team |
| Naming convention violations | P3 | Weekly report to data architecture team |

### 5.4 Compliance Score Dashboard

**Purpose:** Provide the Data Architecture team and leadership with a real-time view of model estate compliance.

**Proposed Power BI / Tableau dashboard panels:**

| Panel | Data Source | Refresh |
|---|---|---|
| Overall compliance score (% of CRITICAL controls passing across all models) | `gold_control.model_compliance_score` | Daily |
| Models with open CRITICAL failures | `gold_control.model_compliance_score` | Daily |
| Column documentation coverage by layer/domain | `gold_control.column_documentation_coverage` | Daily |
| SCD Type 2 adoption heatmap (by domain) | `gold_control.scd_compliance_audit` | Daily |
| DQ test pass rate over time | `gold_control.dq_test_history` | Daily |
| Open waivers (with expiry dates) | `gold_control.waiver_register` | Real-time |
| Schema drift incidents (open and resolved) | `gold_control.schema_drift_log` | Hourly |

---

## 6. Tier 5 – Governance Process Enforcement

### 6.1 Architecture Review Board (ARB) Process

**Triggers:** The following changes must be submitted to the ARB before the PR is merged:
- New Silver Analytical domain (first entity in a new domain)
- Cross-domain breaking change
- Any CRITICAL control waiver request
- Changes to conformed Gold dimensions
- New regulatory reporting model

**ARB review SLA:** 5 business days for standard changes; 2 business days for urgent (requires justification).

**Evidence required for ARB submission:**
1. Completed compliance checklist (Section 0 minimum, plus relevant layer sections)
2. Impact assessment using Unity Catalog lineage
3. Proposed waiver (if applicable) with risk justification and mitigating controls

### 6.2 Model Review Board (Quarterly)

**Purpose:** Periodic review of the entire model estate — not just new changes.

**Cadence:** Quarterly, aligned with regulatory reporting cycles (end of Q1, Q2, Q3, Q4).

**Scope:**
- Review compliance dashboard for systemic gaps
- Review all open waivers approaching expiry
- Prioritise remediation backlog items
- Review naming convention audit report and assign remediation owners
- Review SCD Type 2 coverage and gap closure progress

**Output:** Updated remediation backlog in ADO/Jira; published compliance score to data leadership.

### 6.3 New Engineer Onboarding

**Purpose:** Enforce compliance standards through education, not just tooling.

**Mandatory onboarding checklist for new data engineers:**

| # | Training Item |
|---|---|
| 1 | Read all five `modeling-guidelines/*.md` documents |
| 2 | Complete the Databricks Unity Catalog fundamentals training |
| 3 | Attend the "Silver Modelling 101" workshop (recorded if asynchronous) |
| 4 | Complete at least one pairing session with a senior data architect before merging a first Silver model |
| 5 | Set up `pre-commit` hooks locally using the provided `.pre-commit-config.yaml` |
| 6 | Submit a test PR through the full CI/CD pipeline in the dev environment before touching Silver prod |

### 6.4 Compliance as a Team KPI

**Purpose:** Create organisational accountability for compliance.

**Proposed metrics in team OKRs / reporting:**

| Metric | Target | Review Frequency |
|---|---|---|
| % of Silver Analytical core entities with SCD Type 2 | 100% | Monthly |
| % of Silver/Gold columns with documentation populated | ≥ 95% | Monthly |
| Mean time to remediate HIGH compliance findings | ≤ 30 days | Monthly |
| % of Gold models with business owner sign-off | 100% | Quarterly |
| Schema drift incidents opened vs. resolved | 0 open P1 | Weekly |

---

## 7. Enforcement Toolchain Summary

| Control Category | Tool / Mechanism | When Enforced | Severity Gated |
|---|---|---|---|
| Naming conventions | sqlfluff (pre-commit + CI) | Commit + PR | HIGH |
| Model documentation | dbt-project-evaluator (CI) | PR | HIGH |
| SCD columns | Pre-commit custom script + CI | Commit + PR | CRITICAL |
| Surrogate key pattern | Pre-commit custom script + CI | Commit + PR | CRITICAL |
| Audit columns | Pre-commit custom script + CI | Commit + PR | HIGH |
| Breaking change detection | CI custom script + PR label | PR | CRITICAL |
| dbt tests (uniqueness, not-null, RI) | dbt test suite (CI + scheduled prod) | PR + Daily prod | CRITICAL |
| Unity Catalog metadata population | Deployment gate script | Pre-deploy | HIGH |
| Gold model certification | Unity Catalog access grant policy | At grant time | CRITICAL |
| PII classification | Unity Catalog + CI script | PR + Deployment | CRITICAL |
| Financial reconciliation | Scheduled reconciliation job | Post-load (daily) | CRITICAL |
| Schema drift | Scheduled drift detection script | Hourly | HIGH |
| Compliance score dashboard | dbt scheduled models | Daily | Informational |
| ARB review | Process gate (PR cannot merge without ARB label if triggered) | PR merge | CRITICAL (for triggered changes) |
| Quarterly model review | Calendar process | Quarterly | Governance |

---

## 8. Quick-Start Implementation Roadmap

The following phased plan brings the enforcement stack to full maturity:

### Phase 1 – Foundational (Weeks 1–4)
- [ ] Set up `pre-commit` with sqlfluff and the three custom scripts (bronze columns, SCD columns, naming)
- [ ] Create PR description template (`.github/pull_request_template.md`)
- [ ] Set up CI pipeline with `dbt compile`, `dbt test --select state:modified+`, and `dbt-project-evaluator`
- [ ] Establish `gold_control` schema with `model_certification_register` and `waiver_register` tables

### Phase 2 – Detection (Weeks 5–8)
- [ ] Deploy schema drift detection script as a scheduled Databricks job (daily)
- [ ] Deploy compliance score models in `gold_control`
- [ ] Connect compliance dashboard in Power BI
- [ ] Configure alert routing (PagerDuty / Slack)

### Phase 3 – Full Enforcement (Weeks 9–12)
- [ ] Implement Unity Catalog access grant policy for uncertified Gold models
- [ ] Implement breaking change detection with automatic `breaking-change` PR label
- [ ] Backfill documentation and SCD coverage for existing models (remediation sprint)
- [ ] Conduct first quarterly Model Review Board

### Phase 4 – Optimise (Ongoing)
- [ ] Add column-level lineage to schema drift detection
- [ ] Integrate compliance score into team OKR reporting
- [ ] Expand dbt-project-evaluator custom rules based on findings from Phase 3 retrospective
- [ ] Annual review of this enforcement strategy document
