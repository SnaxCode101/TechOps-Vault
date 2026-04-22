---
title: CySA+ CS0-003 — CompTIA Cybersecurity Analyst
tags:
  - intermediate
  - cert
  - CS0-003
  - CompTIA
  - blue-team
  - SOC
  - SIEM
  - threat-detection
topic: Certification Study
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: CompTIA CySA+ CS0-003 official exam objectives
---

# 🔵 CySA+ CS0-003 — CompTIA Cybersecurity Analyst

## Overview
CySA+ (Cybersecurity Analyst+) is CompTIA's blue-team focused certification. It validates skills in threat detection, incident response, vulnerability management, and security monitoring. Positioned between Security+ and CASP+.

**Exam format:** Max 85 questions (MCQ + performance-based), 165 minutes, passing score 750/900.

**Recommended experience:** 4+ years hands-on experience in information security or a related role; Security+ or Network+ recommended first.

**Cost:** ~$392 USD exam voucher

---

## Exam Domains (CS0-003)

| Domain | Weight |
|---|---|
| 1. Security Operations | 33% |
| 2. Vulnerability Management | 30% |
| 3. Incident Response Management | 20% |
| 4. Reporting and Communication | 17% |

---

## Domain 1: Security Operations (33%)

### 1.1 System and Network Architecture
- Understand network diagrams, data flows, and asset inventories
- Identify log sources: firewall, proxy, DNS, endpoint, application logs
- Cloud architecture (IaaS, PaaS, SaaS) and shared responsibility

### 1.2 Analyze Indicators of Compromise
- Network IOCs: unusual ports, beaconing traffic, high-volume DNS, large data transfers
- Host IOCs: unusual processes, registry changes, new scheduled tasks, privilege escalation
- Application IOCs: repeated authentication failures, unusual API calls, SQL errors in logs

### 1.3 Threat Intelligence
- CTI sources (ISACs, OSINT, commercial feeds)
- MITRE ATT&CK framework navigation
- TTPs vs IOCs vs IOAs (Pyramid of Pain)
- STIX/TAXII data sharing standards

### 1.4 Threat Hunting
- Hypothesis-based hunting methodology
- Baselining normal behavior to detect anomalies
- Query SIEM for suspicious patterns
- Tools: Elastic/Splunk, Velociraptor, OSQuery

### 1.5 Scripting for Security Operations
- Python and PowerShell for automation
- Log parsing, IOC enrichment, alert triage automation
- REST API interaction with security tools

### 1.6 Application Security Monitoring
- WAF logs, DAST/SAST findings
- OWASP Top 10 identification in logs
- API security: rate limiting, auth token abuse

---

## Domain 2: Vulnerability Management (30%)

### 2.1 Vulnerability Scanning
- Unauthenticated vs authenticated scans
- Agent-based vs agentless scanning
- Scan frequency and coverage requirements
- Tools: Nessus, OpenVAS, Qualys

### 2.2 Analyze Scan Results
- Interpret CVSS scores (Base, Temporal, Environmental)
- False positive vs true positive analysis
- Prioritize using CVSS + EPSS + business context + CISA KEV

### 2.3 Remediation and Mitigation
- Patch management workflows
- Compensating controls when patching is not feasible
- Risk acceptance documentation
- SLA targets by severity

### 2.4 Vulnerability Disclosure
- Responsible disclosure / coordinated vulnerability disclosure (CVD)
- Bug bounty programs
- Vendor advisory interpretation

---

## Domain 3: Incident Response (20%)

### 3.1 Incident Response Lifecycle (PICERL + NIST)
```
Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned
```

### 3.2 Analyze Potential Indicators of Attack
- Use MITRE ATT&CK to classify observed behaviors by tactic
- Initial Access indicators: phishing, exploit public-facing app, valid accounts
- Persistence: new scheduled tasks, registry run keys, new services
- Lateral Movement: PsExec, WMI, SMB share access, RDP

### 3.3 Containment Actions
- Isolation: network isolation, endpoint quarantine, account disabling
- Evidence preservation: disk imaging, memory capture (order of volatility)
- Eradication: remove malware, patch vulnerability, reset credentials
- Recovery: restore from known-good backup, monitor for re-infection

### 3.4 Digital Forensics Concepts
- Chain of custody
- Order of volatility (CPU cache → RAM → disk)
- Write blockers and forensic imaging
- Legal hold

---

## Domain 4: Reporting and Communication (17%)

### 4.1 Vulnerability and Incident Reporting
- Executive summary vs technical report
- Metrics: MTTD, MTTR, false positive rate, patch compliance %
- Communicating risk to non-technical stakeholders

### 4.2 Recommended Controls
- Preventive, Detective, Corrective, Compensating controls
- Mapping controls to NIST CSF functions (Identify, Protect, Detect, Respond, Recover)

### 4.3 Compliance Frameworks
| Framework | Domain |
|---|---|
| NIST CSF 2.0 | General cybersecurity framework |
| NIST SP 800-61 | Incident Response |
| ISO 27001 | Information Security Management |
| PCI DSS | Payment card industry |
| HIPAA | Healthcare data |
| SOC 2 | Service organization controls |
| CIS Controls v8 | Prioritized security controls |

---

## Key Concepts to Know

### SIEM Query Patterns
```
Brute force: >5 failed logins from same source in 5 min
Pass-the-hash: Successful NTLM auth from non-standard process
Beaconing: Regular outbound connection at fixed intervals
Data staging: Compressed archive created, then moved to web-accessible dir
Privilege escalation: Low-priv process spawning admin process
```

### Key Metrics
| Metric | Formula |
|---|---|
| MTTD (Mean Time to Detect) | Time from breach to detection |
| MTTR (Mean Time to Respond) | Time from detection to containment |
| MTTI (Mean Time to Investigate) | Time to complete investigation |
| False Positive Rate | FP / (FP + TN) |
| True Positive Rate | TP / (TP + FN) |

### Security Control Types
| Type | Preventive | Detective | Corrective |
|---|---|---|---|
| Technical | Firewall | IDS | Patch |
| Administrative | Policy | Audit log review | Training |
| Physical | Lock | Camera | Restore system |

---

## Study Resources

| Resource | Notes |
|---|---|
| CompTIA CySA+ Study Guide | Sybex official guide |
| Professor Messer CySA+ | Free YouTube videos |
| TryHackMe SOC Level 1 path | Hands-on practice |
| HackTheBox Sherlocks | Blue team forensics challenges |
| Blue Team Labs Online | Free SOC scenarios |

---

## Related Notes
- Incident-Response
- Threat-Intelligence
- Network-Defense-Blue-Team
- Vulnerability-Management
- Digital-Forensics
- Security-Plus-SY0-701

## References
- CompTIA CySA+ CS0-003 Official Exam Objectives: https://www.comptia.org/certifications/cybersecurity-analyst

---
<- [[Certs-MOC]]