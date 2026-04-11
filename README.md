# Data Platform Capability Assessment

This repository contains a structured assessment framework for evaluating the maturity of an organisation's data platform across eight capability domains.

---

## Folder Structure

```
assessment/
├── 00_overview/
│   ├── maturity_model.md      ← Scoring guide, maturity levels, participant register, timeline
│   └── summary.md             ← Consolidated domain score rollup and priority findings
├── 01_data_integration/
│   └── assessment.md          ← Event-Driven, Batch, API, Virtualization, Orchestration, Automation
├── 02_data_management/
│   └── assessment.md          ← Processing, Storage, Modeling, DataOps, ML/AI, Self-Service, Data Products
├── 03_data_quality_management/
│   └── assessment.md          ← Profiling, DQ Rules, DQ Monitoring, Data Observability
├── 04_analytics/
│   └── assessment.md          ← Data Marketplace, Visualization, EDA, Self-Service Analytics
├── 05_metadata_management/
│   └── assessment.md          ← Data Catalog, Discovery, Lineage & Impact Analysis
├── 06_security_compliance/
│   └── assessment.md          ← Privacy, Data Protection, Access Governance, Audit Management
├── 07_data_governance/
│   └── assessment.md          ← Data Strategy, Data Standards, Data Stewardship
└── 08_supporting_tech/
    └── assessment.md          ← Platform Monitoring, Automation & CI/CD, Development Practices
```

---

## How to Use

1. **Start with** `assessment/00_overview/maturity_model.md` to understand the maturity model, scoring dimensions, and assessment process.
2. **Assign assessors** to each domain using the participant table in `maturity_model.md`.
3. **Complete each domain assessment file** – answer the questions, map the current solution/tools, and score each sub-area across four dimensions (Process, Tooling, People, Automation).
4. **Transfer scores** to `assessment/00_overview/summary.md` to produce the consolidated platform maturity score.
5. **Identify priorities** and populate the "Top Findings & Recommended Priorities" table in `summary.md`.

---

## Maturity Scale (Summary)

| Level | Label | Description |
|-------|-------|-------------|
| 1 | Initial | Ad-hoc, no defined process or tooling |
| 2 | Developing | Informal, manual, isolated |
| 3 | Defined | Standardized, documented, repeatable |
| 4 | Managed | Measured, monitored, automated |
| 5 | Optimizing | Continuously improved, self-service, innovation-driven |

---

## Output Deliverables

The `output/` folder contains generated deliverables from the completed assessment:

| File | Description |
|------|-------------|
| [`heatmap.svg`](output/heatmap.svg) | SVG heat-map diagram showing all 8 domains and 36 sub-areas with colour-coded maturity scores |
| [`heatmap.html`](output/heatmap.html) | HTML wrapper for viewing the heat map in a browser |
| [`executive-presentation.pptx`](output/executive-presentation.pptx) | PowerPoint executive presentation (16 slides) built from the deck template |
| [`full-assessment-report.docx`](output/full-assessment-report.docx) | Word document with full assessment detail – current state, key gaps, and recommendations |
| [`maturity-backlog.md`](output/maturity-backlog.md) | Comprehensive improvement backlog with Bronze/Silver/Gold/Platinum maturity levels and prioritised items |
