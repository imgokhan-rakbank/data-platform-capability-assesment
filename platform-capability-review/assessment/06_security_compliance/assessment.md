# Domain 6: Security & Compliance – Capability Assessment

> **Assessor(s):**  
> **Date:**  
> **Version:**  

Refer to `assessment/00_overview/maturity_model.md` for scoring guidance.

---

## 6.1 Data Privacy Management

Data privacy management covers the identification, classification, and controlled handling of personal and sensitive data in compliance with applicable regulations (e.g., GDPR, CCPA, PDPL).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is a data privacy framework or policy formally defined and aligned to relevant regulations (GDPR, local privacy laws)? | No data privacy framework or policy is defined or documented. |
| 2 | Is sensitive and personal data automatically discovered and classified across the data platform? | PII classification is only done for some source systems in Informatica Catalog; not covering the data platform. |
| 3 | What tool is used for data classification (e.g., Microsoft Purview, Informatica CDGC, Collibra Privacy, BigID)? | Informatica Catalog (partial coverage). |
| 4 | Is a Record of Processing Activities (RoPA) or equivalent data inventory maintained and kept current? | No RoPA or equivalent inventory is maintained. |
| 5 | How are data subject rights requests (access, erasure, portability) handled and tracked? Is the process automated? | No automation for access, erasure, or portability requests. |
| 6 | Are privacy-by-design principles applied during the development of new pipelines and data products? | Not applied or documented. |
| 7 | Is data minimization enforced – only collecting and retaining data that is necessary for a defined purpose? | No retention policies are defined or implemented. |
| 8 | How are data retention and deletion policies enforced technically, not just as documentation? | No enforcement of retention or deletion policies. |
| 9 | Is consent management integrated with the data platform where applicable? | Not integrated. |
| 10 | Are privacy impact assessments (PIAs / DPIAs) conducted for new data processing activities? | Not conducted. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Privacy Management Tool(s) | None |
| Classification Tool(s) | Informatica Catalog (partial, source systems only) |
| Applicable Regulations | Not mapped or tracked |
| Known Gaps / Limitations | No privacy framework, partial PII classification, no automation, no retention, no RoPA, no consent management. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No privacy framework, policy, or process; only partial classification. |
| Tooling Maturity | 2 | Informatica Catalog used for some source systems; not covering the platform. |
| People / Skills | 1 | No defined roles or skills for privacy management. |
| Automation Level | 1 | No automation for privacy processes. |
| **Sub-Area Overall** | 1 | Data privacy management is largely absent; only partial classification exists. |

---

## 6.2 Data Protection

Data protection covers the technical controls that safeguard data from unauthorized access, loss, or corruption (encryption, masking, tokenization, backup).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is data encrypted at rest across all storage systems (databases, data lakes, backups)? What encryption standard is used? | Data is encrypted at rest (TDE). |
| 2 | Is data encrypted in transit (TLS/mTLS) for all data movement within and outside the platform? | Data is encrypted in transit (TLS). |
| 3 | Is dynamic data masking or static data masking applied to sensitive fields in analytics and reporting environments? | No data masking framework exists. |
| 4 | Is tokenization or pseudonymization used for PII in non-production environments (dev, test, analytics)? | Not implemented. |
| 5 | How are encryption keys managed (KMS, HSM, key rotation policy)? | Not documented. |
| 6 | Are backup and disaster recovery procedures tested regularly? What are the documented RPO and RTO targets? | No backups or disaster recovery procedures. |
| 7 | Is data loss prevention (DLP) tooling in place to detect and prevent exfiltration of sensitive data? | Not implemented. |
| 8 | Are there controls to prevent sensitive data from being written to logs or error outputs? | No controls for sensitive data logging. |
| 9 | How is data protection enforced in self-service and exploration environments where users query raw data? | Not enforced. |
| 10 | Is there a data breach response plan, and has it been tested? | No data breach response plan. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Encryption Platform / KMS | TDE, TLS (platform default) |
| Masking / Tokenization Tool | None |
| DLP Tool | None |
| Backup / DR Platform | None |
| Known Gaps / Limitations | No masking, no tokenization, no DLP, no backup/DR, no logging controls. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No process for masking, backup, DR, or DLP. |
| Tooling Maturity | 2 | Encryption at rest and in transit; no other tooling. |
| People / Skills | 1 | No roles or skills for data protection beyond encryption. |
| Automation Level | 1 | No automation for data protection controls. |
| **Sub-Area Overall** | 1 | Data protection is limited to encryption; other controls are absent. |

---

## 6.3 Access Governance

Access governance covers the policies, processes, and controls that manage who can access what data, under what conditions, and for how long.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined access control model (RBAC, ABAC, PBAC) applied consistently across data platform components? | RBAC is defined but not at a granular level. |
| 2 | Is a central identity and access management (IAM) system used across all data platform tools? | Not consistently used. |
| 3 | Is the principle of least privilege enforced? How is this reviewed and validated? | Not enforced or reviewed. |
| 4 | Are access requests and approvals managed through an automated workflow? What is the average provisioning time? | No automation for access requests. |
| 5 | Is access reviewed periodically (access recertification / re-certification campaigns)? How often and by whom? | Not reviewed periodically. |
| 6 | Is privileged access (DBA, admin) managed separately with just-in-time (JIT) access or PAM tooling? | Not implemented. |
| 7 | Is fine-grained access control enforced at the row or column level for sensitive data? | Not enforced. |
| 8 | How are service accounts and machine identities governed (rotation, usage monitoring)? | Not governed. |
| 9 | Are orphaned or over-privileged accounts detected and remediated? | Not detected or remediated. |
| 10 | Are access control decisions and changes logged and available for audit? | Not logged centrally. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| IAM / Directory Platform | Exists, but not consistently used |
| PAM (Privileged Access) Tool | None |
| Access Governance / IGA Tool | None |
| Fine-Grained Access Control Mechanism | Not implemented |
| Known Gaps / Limitations | RBAC not granular, no automation, no periodic review, no fine-grained controls, no central logging. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No formal access governance process; RBAC is basic and not reviewed. |
| Tooling Maturity | 2 | Basic RBAC exists; no advanced tooling or automation. |
| People / Skills | 1 | No roles or skills for access governance. |
| Automation Level | 1 | No automation for access requests or reviews. |
| **Sub-Area Overall** | 1 | Access governance is basic and manual; lacks automation and review. |

---

## 6.4 Audit Management

Audit management covers the systematic capture, retention, and review of audit logs documenting who accessed, modified, or moved data.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are comprehensive audit logs captured for all data access, modification, and movement events across the platform? | Audit logs are not captured centrally. |
| 2 | Are audit logs stored in a tamper-evident, immutable store separate from the systems being audited? | Not implemented. |
| 3 | What is the audit log retention period, and does it meet regulatory and contractual requirements? | Not defined. |
| 4 | Are audit logs centralized in a SIEM or log management platform (e.g., Splunk, Microsoft Sentinel, Elastic SIEM)? | Not centralized. |
| 5 | Are alerts configured for suspicious access patterns (e.g., bulk download, access outside business hours, access by terminated users)? | Not configured. |
| 6 | Is there a defined process for responding to audit findings and tracking remediation to closure? | No defined process exists. |
| 7 | Are audit reports produced for regulatory or internal compliance reviews on demand? | Not produced. |
| 8 | Are data access logs granular enough to attribute actions to individual users (not just service accounts or groups)? | Not granular or attributed. |
| 9 | Are audit logs included in regular internal audit or compliance reviews? | Not included. |
| 10 | Is there a chain-of-custody process for sensitive data used in investigations or regulatory submissions? | Not implemented. |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Audit Logging Platform | None |
| SIEM / Log Management Tool | None |
| Audit Log Retention Period | Not defined |
| Known Gaps / Limitations | No central audit logging, no SIEM, no process, no reporting, no chain-of-custody. |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | 1 | No audit process or central logging. |
| Tooling Maturity | 1 | No audit logging or SIEM tooling. |
| People / Skills | 1 | No roles or skills for audit management. |
| Automation Level | 1 | No automation for audit management. |
| **Sub-Area Overall** | 1 | Audit management is absent; no process, tooling, or automation. |
