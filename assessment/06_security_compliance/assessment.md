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
| 1 | Is a data privacy framework or policy formally defined and aligned to relevant regulations (GDPR, local privacy laws)? | |
| 2 | Is sensitive and personal data automatically discovered and classified across the data platform? | |
| 3 | What tool is used for data classification (e.g., Microsoft Purview, Informatica CDGC, Collibra Privacy, BigID)? | |
| 4 | Is a Record of Processing Activities (RoPA) or equivalent data inventory maintained and kept current? | |
| 5 | How are data subject rights requests (access, erasure, portability) handled and tracked? Is the process automated? | |
| 6 | Are privacy-by-design principles applied during the development of new pipelines and data products? | |
| 7 | Is data minimization enforced – only collecting and retaining data that is necessary for a defined purpose? | |
| 8 | How are data retention and deletion policies enforced technically, not just as documentation? | |
| 9 | Is consent management integrated with the data platform where applicable? | |
| 10 | Are privacy impact assessments (PIAs / DPIAs) conducted for new data processing activities? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Privacy Management Tool(s) | |
| Classification Tool(s) | |
| Applicable Regulations | |
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

## 6.2 Data Protection

Data protection covers the technical controls that safeguard data from unauthorized access, loss, or corruption (encryption, masking, tokenization, backup).

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is data encrypted at rest across all storage systems (databases, data lakes, backups)? What encryption standard is used? | |
| 2 | Is data encrypted in transit (TLS/mTLS) for all data movement within and outside the platform? | |
| 3 | Is dynamic data masking or static data masking applied to sensitive fields in analytics and reporting environments? | |
| 4 | Is tokenization or pseudonymization used for PII in non-production environments (dev, test, analytics)? | |
| 5 | How are encryption keys managed (KMS, HSM, key rotation policy)? | |
| 6 | Are backup and disaster recovery procedures tested regularly? What are the documented RPO and RTO targets? | |
| 7 | Is data loss prevention (DLP) tooling in place to detect and prevent exfiltration of sensitive data? | |
| 8 | Are there controls to prevent sensitive data from being written to logs or error outputs? | |
| 9 | How is data protection enforced in self-service and exploration environments where users query raw data? | |
| 10 | Is there a data breach response plan, and has it been tested? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Encryption Platform / KMS | |
| Masking / Tokenization Tool | |
| DLP Tool | |
| Backup / DR Platform | |
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

## 6.3 Access Governance

Access governance covers the policies, processes, and controls that manage who can access what data, under what conditions, and for how long.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Is there a defined access control model (RBAC, ABAC, PBAC) applied consistently across data platform components? | |
| 2 | Is a central identity and access management (IAM) system used across all data platform tools? | |
| 3 | Is the principle of least privilege enforced? How is this reviewed and validated? | |
| 4 | Are access requests and approvals managed through an automated workflow? What is the average provisioning time? | |
| 5 | Is access reviewed periodically (access recertification / re-certification campaigns)? How often and by whom? | |
| 6 | Is privileged access (DBA, admin) managed separately with just-in-time (JIT) access or PAM tooling? | |
| 7 | Is fine-grained access control enforced at the row or column level for sensitive data? | |
| 8 | How are service accounts and machine identities governed (rotation, usage monitoring)? | |
| 9 | Are orphaned or over-privileged accounts detected and remediated? | |
| 10 | Are access control decisions and changes logged and available for audit? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| IAM / Directory Platform | |
| PAM (Privileged Access) Tool | |
| Access Governance / IGA Tool | |
| Fine-Grained Access Control Mechanism | |
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

## 6.4 Audit Management

Audit management covers the systematic capture, retention, and review of audit logs documenting who accessed, modified, or moved data.

### Questions

| # | Question | Answer / Evidence |
|---|----------|-------------------|
| 1 | Are comprehensive audit logs captured for all data access, modification, and movement events across the platform? | |
| 2 | Are audit logs stored in a tamper-evident, immutable store separate from the systems being audited? | |
| 3 | What is the audit log retention period, and does it meet regulatory and contractual requirements? | |
| 4 | Are audit logs centralized in a SIEM or log management platform (e.g., Splunk, Microsoft Sentinel, Elastic SIEM)? | |
| 5 | Are alerts configured for suspicious access patterns (e.g., bulk download, access outside business hours, access by terminated users)? | |
| 6 | Is there a defined process for responding to audit findings and tracking remediation to closure? | |
| 7 | Are audit reports produced for regulatory or internal compliance reviews on demand? | |
| 8 | Are data access logs granular enough to attribute actions to individual users (not just service accounts or groups)? | |
| 9 | Are audit logs included in regular internal audit or compliance reviews? | |
| 10 | Is there a chain-of-custody process for sensitive data used in investigations or regulatory submissions? | |

### Solution Mapping

| Aspect | Details |
|--------|---------|
| Audit Logging Platform | |
| SIEM / Log Management Tool | |
| Audit Log Retention Period | |
| Known Gaps / Limitations | |

### Maturity Score

| Dimension | Score (1–5) | Justification |
|-----------|-------------|---------------|
| Process Maturity | | |
| Tooling Maturity | | |
| People / Skills | | |
| Automation Level | | |
| **Sub-Area Overall** | | |
