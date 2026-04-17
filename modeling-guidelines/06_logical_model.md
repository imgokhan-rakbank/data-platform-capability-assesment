## 1. Banking Domain Coverage Requirements

### 1.1 Foundational Subject Areas (Mandatory – All Must Exist)

| Subject Area | Silver Entity |
|---|---|
| Party / Customer | `silver.customer.party`, `silver.customer.party_role`, `silver.customer.party_relationship` |
| Account | `silver.account.account` (typed hierarchy: CASA, Loan, Card, TD, TF) |
| Product | `silver.product.product`, `silver.product.product_category`, `silver.product.product_type` |
| Transaction / Event | `silver.transaction.financial_transaction` (parent supertype) |
| Contract / Agreement | `silver.contract.contract`, `silver.contract.contract_party_role` |
| General Ledger | `silver.gl.gl_account`, `silver.gl.gl_posting`, `silver.gl.subledger_posting` |
| Collateral | `silver.collateral.collateral`, `silver.collateral.collateral_valuation`, `silver.collateral.contract_collateral_link` |
| Reference Data | `silver.reference.currency`, `silver.reference.country`, `silver.reference.branch`, `silver.reference.calendar` |

### 1.2 Risk & Regulatory Subject Areas (Mandatory for Compliance)

| Subject Area | Silver Entity | Regulatory Requirement |
|---|---|---|
| Credit Risk | `silver.risk_credit.credit_exposure`, `silver.risk_credit.credit_rating`, `silver.risk_credit.ecl_component` | IFRS 9, Basel III |
| Liquidity Risk | `silver.risk_liquidity.liquidity_buffer`, `silver.risk_liquidity.cash_flow_projection` | Basel III LCR/NSFR |
| Market Risk | `silver.risk_market.position`, `silver.risk_market.market_valuation` | Basel III |
| Operational Risk | `silver.risk_operational.loss_event`, `silver.risk_operational.risk_incident` | Basel III Op Risk |
| AML / Compliance | `silver.compliance.kyc_case`, `silver.compliance.screening_result`, `silver.compliance.sar_flag` | CBUAE, FATF |
| Regulatory Metrics | `silver.regulatory.regulatory_metric`, `silver.regulatory.regulatory_submission` | CBUAE SupTech |

### 1.3 Party Model Design Standard

The Party model must follow the **Party Role** pattern (industry-standard FS-LDM / BIAN approach):

```
PARTY (supertype)
├── PERSON (subtype)
└── ORGANISATION (subtype)

PARTY_ROLE (role assignment entity)
├── role_type: CUSTOMER / GUARANTOR / BENEFICIAL_OWNER / DIRECTOR / SIGNATORY / RM
├── party_key (FK → PARTY)
└── linked_entity_key (FK → ACCOUNT or CONTRACT)
```

- Roles must not be hard-coded as columns in the Party entity (e.g., no `is_guarantor` flag on the party table)
- Relationship types (household grouping, corporate hierarchy) must be modelled in a `PARTY_RELATIONSHIP` entity, not embedded columns

---

## 2. Cross-Domain Integrity Standards

### 2.1 Canonical Entity Linkage Pattern

The following cross-domain relationships are **mandatory** and must be navigable from any Silver entity:

```
Party ──── 1:N ──── Account
Account ── 1:N ──── Transaction
Account ── 1:N ──── Contract / Agreement
Contract ─ N:M ──── Collateral  (via bridge: contract_collateral_link)
Party ───── 1:N ──── Exposure / Facility
Exposure ── N:1 ──── GL Account (subledger)
```

Each relationship must be implemented as:
- A documented foreign key constraint (logical, not enforced at engine level in Delta Lake)
- A referential integrity data quality check executed on each Silver refresh
- A lineage edge registered in Unity Catalog

### 2.2 Single Concept Definition Rule

| Principle | Standard |
|---|---|
| One definition per concept | A concept such as "Customer", "Account", "Outstanding Balance" must have exactly one Silver entity as its authoritative definition |
| No domain-level re-definition | Risk domain, Finance domain, and Retail domain must all reference the same `silver.customer.party` entity — not create their own customer entity |
| Glossary linkage | Every Silver entity must be linked to its corresponding entry in the Business Glossary in Unity Catalog / data catalogue |

---

### 3. SCD Type Mandate per Entity Class

| Entity Class | Required SCD Type | Rationale |
|---|---|---|
| Party / Customer | Type 2 | Regulatory: KYC status history, address history, risk rating history |
| Account | Type 2 | Regulatory: Account status changes, limit changes, terms modifications |
| Loan / Financing Agreement | Type 2 | IFRS 9: Stage transitions (Stage 1 → 2 → 3), ECL history |
| Credit Card Account | Type 2 | Regulatory: credit limit changes, status changes |
| Credit Exposure / Facility | Type 2 | Basel III: Exposure history, collateral re-valuation |
| Collateral | Type 2 | Valuation history for risk and regulatory reporting |
| GL Posting | Type 0 (insert-only fact) | GL postings are immutable events |
| Transaction | Type 0 (insert-only fact) | Financial transactions are immutable events |
| Reference / Code Tables | Type 1 or Type 2 (if history needed) | Determined per reference entity based on regulatory need |