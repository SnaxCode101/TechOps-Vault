---
title: CIA Triad
tags:
  - foundational
topic: Security Fundamentals
difficulty: foundational
created: 2026-03-23
updated: 2026-04-04
source: NIST SP 800-12 Rev 1, ISC² CISSP CBK
---

# CIA Triad

## Overview
The CIA Triad is the foundational model of information security. It defines three core properties that secure systems must protect: **Confidentiality**, **Integrity**, and **Availability**. Every security control, policy, and threat can be evaluated against these three properties.

## Why It Matters in Security
Security practitioners use the CIA Triad as a lens to classify threats, evaluate controls, and design systems. When assessing a vulnerability or incident, identifying which property is violated immediately clarifies the impact and appropriate response.

## Core Details

### Confidentiality
Ensuring that information is accessible only to those authorized to access it. Violations expose data to unauthorized parties.

**Controls:** Encryption, access controls, authentication, least-privilege, data classification.

**Threats:** Eavesdropping, credential theft, insecure storage, man-in-the-middle attacks, data exfiltration.

---

### Integrity
Ensuring that data is accurate and has not been altered in an unauthorized or unexpected way. Violations mean data cannot be trusted.

**Controls:** Hashing (SHA-256, MD5), digital signatures, file integrity monitoring (FIM), audit logs, version control.

**Threats:** Data tampering, SQL injection, man-in-the-middle modification, malware altering files, log tampering.

---

### Availability
Ensuring that systems and data are accessible to authorized users when needed. Violations prevent legitimate access.

**Controls:** Redundancy, backups, failover systems, DDoS mitigation, patch management, disaster recovery plans.

**Threats:** Denial-of-service (DoS/DDoS), ransomware, hardware failure, power outages, misconfiguration.

---

## Extended Models

**DAD Triad** — The adversarial mirror of CIA:
- **Disclosure** (violates Confidentiality)
- **Alteration** (violates Integrity)
- **Destruction/Denial** (violates Availability)

**Parkerian Hexad** — Extends CIA with: Possession/Control, Authenticity, Utility.

## Common Attack Vectors / Misconfigurations
| Property | Example Attack | Example Misconfiguration |
|---|---|---|
| Confidentiality | Credential stuffing attack | Storing passwords in plaintext |
| Integrity | SQL injection modifying records | No input validation on web forms |
| Availability | DDoS attack on a web server | No backup for critical data |

## Key Terms
| Term | Definition |
|---|---|
| Confidentiality | Preventing unauthorized disclosure of information |
| Integrity | Ensuring data accuracy and preventing unauthorized modification |
| Availability | Ensuring authorized users can access systems and data when needed |
| Least Privilege | Granting only the minimum access rights needed to perform a task |
| Non-repudiation | The ability to prove that a specific party performed an action (often considered alongside CIA) |

## Related Notes
- OSI-Model
- Networking-Basics
- Cryptography-Basics

## Sources

National Institute of Standards and Technology. (2017). *An introduction to information security* (NIST SP 800-12 Rev 1). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-12r1.pdf

ISC². (2021). *CISSP certified information systems security professional official study guide*. ISC².

## See Also

- Cryptography-Basics
- Incident-Response
- Penetration-Testing-Methodology

---
<- [[Fundamentals-MOC]]