# Data Model Review – Maturity Model & Scoring Guide

## Purpose
This assessment evaluates the maturity of the organisation's data modelling practice across the **Medallion Architecture** (Bronze → Silver → Gold) and the supporting disciplines of standards, governance, and tooling.  
It is scoped to the banking data platform at RAKBANK and is designed to answer three fundamental questions:

1. Is the Medallion Architecture applied **correctly and consistently**?
2. Are **modelling standards defined, enforced, and automated**?
3. Does the **current model cover the expected banking domain** at each layer — especially the Silver layer's foundational subject areas and the Gold layer's risk-reporting needs?

---

## Modelling Layers Assessed

For each Medallion zone, questions are structured across **four modelling perspectives**:

| Perspective | Description |
|-------------|-------------|
| **Conceptual** | Business entities, relationships, and domain boundaries — independent of technology. Confirms the right subjects are modelled. |
| **Logical** | Attribute-level detail, keys, cardinality, and normalisation/denormalisation choices — technology-agnostic. |
| **Physical** | Concrete implementation: table/column names, data types, partitioning, clustering, file formats, Delta properties. |
| **Semantic** | Business-facing meaning: friendly names, definitions, business rules, metrics, and calculated measures exposed to consumers. |

---

## Maturity Levels

| Level | Label | Description |
|-------|-------|-------------|
| **1** | **Initial** | No modelling discipline. Tables created ad hoc with no documentation or standards. |
| **2** | **Developing** | Some modelling exists but is inconsistent, undocumented, or confined to specific teams. Standards are informal. |
| **3** | **Defined** | Modelling standards exist, are documented, and are consistently applied. Conceptual and logical models are maintained alongside physical designs. Tooling supports the practice. |
| **4** | **Managed** | Models are versioned, reviewed, and kept in sync with the physical implementation. Quality metrics (coverage, conformance to standards) are tracked. Change impact is assessed before deployment. |
| **5** | **Optimising** | Modelling is automated (schema generation from logical model, automated DDL promotion). Models drive data contracts. Self-service consumers use the semantic layer. Continuous feedback loops improve model quality. |

---

## How to Use Each Assessment File

Each sub-area assessment file follows this structure:

```
### [Sub-Area Name]

**Definition** – one paragraph describing what is being assessed.

**Questions**

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | ... | |

**Solution Mapping**

| Aspect | Details |
|--------|---------|
| Current Tool(s) / Platform(s) | |
| Vendor / Version | |
| Deployment Model | |
| Known Gaps / Limitations | |

**Maturity Score**

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Conceptual Modelling | | |
| Logical Modelling | | |
| Physical Modelling | | |
| Semantic / Business Layer | | |
| Standards Conformance | | |
| Tooling Maturity | | |
| **Sub-Area Overall** | | |
```

**Scoring Tips**
- Score each dimension independently, then derive an overall sub-area score (weighted average or consensus).
- Use the domain-level summary sheet (`summary.md`) to roll up sub-area scores.
- Attach ER diagrams, DDL scripts, dbt project links, or lineage screenshots as evidence where possible.

---

## Assessment Participants

| Role | Name | Area(s) Responsible |
|------|------|---------------------|
| Data Architect | | All layers |
| Data Modeller | | Silver & Gold |
| Platform / Data Engineer | | Bronze & Physical |
| Domain SME – Retail Banking | | Silver (Customer, Account, Product) |
| Domain SME – Risk & Finance | | Silver (Risk), Gold (Reporting) |
| Data Governance Lead | | Standards, Semantic layer |
| Analytics / BI Lead | | Gold, Semantic layer |

---

## Assessment Timeline

| Activity | Target Date | Owner | Status |
|----------|-------------|-------|--------|
| Kickoff & distribute templates | | | |
| Complete Bronze layer assessment | | | |
| Complete Silver layer assessment | | | |
| Complete Gold layer assessment | | | |
| Complete Standards & Tooling assessments | | | |
| Review & validate scores | | | |
| Consolidate into summary | | | |
| Present findings & modelling roadmap | | | |
