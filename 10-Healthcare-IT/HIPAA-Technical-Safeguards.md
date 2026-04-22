---
title: HIPAA Technical Safeguards
type: reference
tags: [hipaa, security, compliance, phi, technical-safeguards, hitech, foundational]
section: 10-Healthcare-IT
created: 2026-03-27
difficulty: foundational
---

# 🔒 HIPAA Technical Safeguards

> The HIPAA Security Rule (45 CFR §164.312) defines Technical Safeguards as the technology and policy/procedures that protect ePHI and control access to it. This note is a job-aid reference mapping each standard to practical implementation.

---

## Overview

The Security Rule applies to **ePHI (electronic Protected Health Information)** — any PHI created, received, maintained, or transmitted electronically.

**Three categories of safeguards:**
1. **Administrative** — policies, training, risk analysis (§164.308)
2. **Physical** — facility access, workstation controls (§164.310)
3. **Technical** — this note (§164.312)

---

## Technical Safeguards — §164.312

### A. Access Control (Required)

| Implementation Spec | R/A | Practical Implementation |
|---|---|---|
| Unique User Identification | **Required** | Unique usernames per user; no shared accounts; AD accounts tied to individuals |
| Emergency Access Procedure | **Required** | Break-glass accounts; documented procedure; audit trail; MFA bypass only with approval |
| Automatic Logoff | **Addressable** | Screen lock after inactivity (typically 15 min); enforced via GPO or endpoint policy |
| Encryption & Decryption | **Addressable** | AES-256 for data at rest; TLS 1.2+ for data in transit; disk encryption on endpoints |

> **R = Required** (must implement). **A = Addressable** (must implement or document why not + equivalent alternative).

---

### B. Audit Controls (Required)

| Requirement | Detail |
|---|---|
| Hardware activity logs | System access, login/logout, hardware changes |
| Software activity logs | Application access, query logs, report generation |
| Data activity logs | Who accessed which record, when, from where |

**Epic-Specific:** Epic Audit Logs capture user-level access to patient records. Security access logs in Epic Chronicle database. Run via Reporting Workbench → Access Reports.

**SIEM Correlation:**
```
PHI Access Audit:
  Source: Epic, EHR, EMR, or medical app logs
  Alert on: Access outside patient care relationship
  Alert on: Bulk record access (>50 records/hour by single user)
  Alert on: Access after-hours for clinical staff
  Alert on: Access from unexpected geolocation
```

---

### C. Integrity Controls (Addressable)

| Implementation Spec | Practical Implementation |
|---|---|
| Mechanism to authenticate ePHI | Checksums, digital signatures, hash verification on data transfers |
| Error-correcting storage | RAID, ECC memory for servers storing ePHI |
| Transmission integrity | TLS with certificate validation; no self-signed certs in production |

---

### D. Person or Entity Authentication (Required)

Must verify user identity before granting access to ePHI.

| Method | Notes |
|---|---|
| Password | Minimum complexity; not sufficient alone for remote access |
| MFA | Required for all remote access per modern interpretation |
| Smart Card / CAC | Common in government/VA healthcare |
| Biometric | Fingerprint used in some clinical environments (e.g. Imprivata) |
| SSO | Acceptable if underlying auth is strong; Imprivata, Okta common in healthcare |

**Imprivata OneSign:** Common healthcare SSO solution. Tap-and-go using proximity badge. Integrates with Epic. Audit trail maintained.

---

### E. Transmission Security (Required)

| Spec | Detail |
|---|---|
| Encryption | TLS 1.2 minimum (TLS 1.3 preferred); ePHI must not traverse public networks unencrypted |
| Integrity controls | Ensure ePHI is not improperly modified in transit |

**Prohibited:** Sending ePHI via unencrypted email, SMS, or unsecured FTP.  
**Allowed:** Encrypted email (S/MIME, Virtru), SFTP, HTTPS, VPN + TLS.

---

## HIPAA Breach Rule — Quick Reference

| Category | Threshold | Notification |
|---|---|---|
| Individual notification | Any breach of unsecured PHI | Within 60 days of discovery |
| Media notification | Breach affecting >500 in a state | Prominent media outlet + HHS |
| HHS notification | Any breach | Annual report (small) or within 60 days (>500) |
| Business Associate | Any breach | Notify Covered Entity without unreasonable delay |

**Safe Harbor:** PHI rendered unreadable/unusable/indecipherable (encrypted with valid key management) does NOT require breach notification.

---

## HITECH Act Additions (2009)

- Extended HIPAA obligations to **Business Associates (BAs)** directly (not just via contract)
- Increased civil penalties: up to $1.9M per violation category per year
- Mandated breach notification requirements (above)
- Required HHS to post breaches >500 on "Wall of Shame": [hhs.gov/hipaa/for-professionals/breach-notification/breach-reporting](https://www.hhs.gov/hipaa/for-professionals/breach-notification/breach-reporting/index.html)

---

## Risk Analysis Requirement (§164.308(a)(1))

Every covered entity must:
1. Identify all ePHI (scope)
2. Identify threats and vulnerabilities
3. Assess current controls
4. Determine likelihood and impact
5. Assign risk level
6. Document and review regularly

**Maps to:** NIST SP 800-30 Risk Assessment, NIST SP 800-66r2 (HIPAA-specific guidance)

---

## HIPAA vs. Other Frameworks

| Framework | Relationship |
|---|---|
| NIST CSF | Voluntary; widely used as HIPAA risk analysis structure |
| NIST SP 800-66r2 | HHS-endorsed guidance for implementing HIPAA Security Rule |
| HITRUST CSF | Certifiable framework that incorporates HIPAA; common in healthcare M&A due diligence |
| SOC 2 Type II | Business Associate compliance evidence; not HIPAA equivalent but overlapping |
| ISO 27001 | International standard; similar controls; not HIPAA certification |

---

## Common Audit Findings

| Finding | Standard | Remediation |
|---|---|---|
| Shared accounts in use | §164.312(a)(2)(i) | Enforce unique user IDs; disable shared accounts |
| No MFA on remote access | §164.312(d) | Implement MFA via Okta/Duo/Imprivata |
| Unencrypted laptops | §164.312(a)(2)(iv) | BitLocker/FileVault enforcement via MDM |
| Excessive access to PHI | §164.312(a)(1) | RBAC review; apply minimum necessary principle |
| No audit log review process | §164.312(b) | Implement SIEM; schedule regular access reviews |
| ePHI in unencrypted email | §164.312(e) | Encrypted email gateway; policy + training |

---

## Related Notes
- Healthcare-IT-Overview
- HL7-FHIR-Fundamentals
- Epic-Overview
- Threat-Intelligence
- Incident-Response
- Cloud-Security-Fundamentals

---

## References
- 45 CFR §164.312 — HIPAA Security Rule Technical Safeguards
- NIST SP 800-66r2 — Implementing the HIPAA Security Rule (2023)
- HHS.gov — HIPAA Security Rule guidance and FAQs
- CISA — Healthcare Cybersecurity guidance

---
<- [[Healthcare-IT-MOC]]