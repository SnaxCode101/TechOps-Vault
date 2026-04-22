---
title: Medical Device Risk Management
type: reference
category: medical-devices
created: 2026-03-27
updated: 2026-03-27
tags:
  - medical-devices
  - risk-management
  - iec-62443
  - iec-80001
  - nist
  - threat-modeling
  - healthcare
  - intermediate
source: IEC 62443, IEC 80001-1, NIST CSF, NIST SP 800-30, AAMI TIR57
difficulty: intermediate
---

# ⚖️ Medical Device Risk Management

## Overview

Medical device risk management balances **cybersecurity risk** with **clinical safety risk**. A vulnerability in a patient monitor is not just a data breach concern — it may affect the availability or integrity of a life-sustaining system. Risk decisions must involve both information security and clinical engineering stakeholders.

---

## Key Frameworks

### IEC 80001-1 — Risk Management for IT Networks Incorporating Medical Devices

The primary international standard for managing risk when medical devices connect to hospital IT networks.

**Scope:** Applies to the **healthcare delivery organisation (HDO)** — the hospital is responsible for managing risk when integrating networked medical devices into their environment.

**Three safety properties:**
| Property | Definition |
|---|---|
| Safety | No harm to patients, operators, or third parties |
| Effectiveness | Device continues to perform its intended clinical function |
| Data and System Security | Confidentiality, integrity, and availability of data and systems |

**Key IEC 80001-1 concepts:**

**Responsible Organisation (RO):** The hospital — accountable for risk management of the medical IT network.

**Risk Management Process:**
```
1. Identify hazards (what can go wrong when the device connects to the network)
2. Estimate risk (likelihood × severity of harm)
3. Evaluate risk (is it acceptable?)
4. Control risk (implement controls to reduce risk)
5. Monitor residual risk (ongoing surveillance)
```

**When to apply IEC 80001-1:**
- New medical device connecting to the network
- Network change that affects medical devices (firmware update, VLAN change, firewall rule change)
- New integration between medical device and clinical system
- Medical device vulnerability disclosed

---

### IEC 62443 — Industrial Automation and Control System Security

Originally designed for industrial OT, IEC 62443 is increasingly applied to medical device environments.

**Key concepts for healthcare:**

**Security Levels (SL):**
| SL | Protection Against |
|---|---|
| SL 1 | Casual or coincidental violation |
| SL 2 | Intentional violation using simple means with low resources |
| SL 3 | Intentional violation using sophisticated means with moderate resources |
| SL 4 | Intentional violation using sophisticated means with extended resources (nation-state) |

**Zones and Conduits:**
- **Zone:** A grouping of assets that share the same security requirements (maps to VLANs/network segments)
- **Conduit:** A communication path between zones with defined security controls (maps to firewall rules)

```
Healthcare mapping:
Zone: Medical Device Zone (SL 2-3)
Zone: Clinical Application Zone (SL 2)
Zone: Enterprise IT Zone (SL 1-2)
Conduit: Firewall between Medical Device Zone and Clinical Zone (allow HL7/DICOM only)
```

---

### NIST Cybersecurity Framework (CSF) — Healthcare Application

NIST CSF is widely adopted in healthcare as the overarching risk management framework.

**Five functions applied to medical devices:**

| Function | Medical Device Application |
|---|---|
| **Identify** | Inventory all medical devices; classify by risk (device type, data handled, network connectivity); map to CMDB |
| **Protect** | Network segmentation, access control, encryption, secure configuration, vendor patch management |
| **Detect** | Passive network monitoring, IDS, anomaly detection, SIEM correlation |
| **Respond** | Medical device incident response procedures, vendor coordination, clinical safety assessment |
| **Recover** | Device restoration from backup config, replacement procedures, lessons learned |

---

### AAMI TIR57 — Principles for Medical Device Security Risk Management

AAMI TIR57 is a technical information report that aligns medical device security risk management with ISO 14971 (medical device risk management for safety).

**Key contribution:** Provides a process for manufacturers to perform **security risk assessment** as part of the device design process — the methodology the FDA expects in premarket submissions.

**AAMI TIR57 process:**
```
1. Define device context (intended use, environment, users)
2. Identify assets (data, functions, interfaces)
3. Identify threats (threat modelling)
4. Identify vulnerabilities
5. Assess risk (likelihood × impact on safety/effectiveness/security)
6. Define risk controls (design controls + operational controls)
7. Evaluate residual risk
8. Document and maintain throughout device lifecycle
```

---

## Threat Modelling for Medical Devices

### STRIDE Applied to Medical Devices

| Threat | Medical Device Example |
|---|---|
| **S**poofing | Attacker impersonates a drug library server → pump downloads malicious drug library |
| **T**ampering | Attacker modifies HL7 message in transit → patient allergy data altered |
| **R**epudiation | Device does not log admin access → cannot prove who changed a setting |
| **I**nformation Disclosure | Unencrypted DICOM traffic → patient images intercepted on the network |
| **D**enial of Service | Ransomware encrypts device management server → pumps cannot download updates |
| **E**levation of Privilege | Attacker exploits device vulnerability → gains root/admin access to embedded OS |

### Attack Trees for Healthcare

```
Root goal: Compromise infusion pump
├── Network attack
│   ├── Exploit unpatched vulnerability (CVE in embedded Linux)
│   ├── MITM drug library download (HTTP, no TLS)
│   └── ARP spoofing on flat clinical VLAN
├── Physical attack
│   ├── USB malware insertion
│   └── Serial console access (JTAG/UART)
├── Supply chain
│   ├── Compromised firmware update from vendor
│   └── Backdoored third-party library in SBOM
└── Credential attack
    ├── Default credentials (admin/admin)
    └── Stolen biomed engineer credentials
```

---

## Risk Assessment Process — Hospital Perspective

### Step 1: Device Inventory and Classification

```
For each medical device on the network:
- Device type and model
- Manufacturer and firmware version
- Network connectivity (wired/wireless, ports, protocols)
- Data handled (PHI, PII, clinical data, none)
- Clinical criticality: Life-sustaining / Life-supporting / Non-critical
- Current security posture (patched, segmented, monitored)
```

**Clinical criticality classification:**

| Category | Definition | Examples |
|---|---|---|
| Life-sustaining | Device directly sustains patient life; failure = immediate patient harm | Ventilators, cardiac monitors, dialysis machines |
| Life-supporting | Device supports clinical decision-making; failure = delayed patient harm | Infusion pumps, imaging systems, lab analysers |
| Non-critical | Device supports operations but failure does not directly impact patient safety | Asset tracking, scheduling displays, environmental monitors |

### Step 2: Risk Scoring

```
Risk = Likelihood × Impact

Likelihood factors:
- Is the device internet-facing? (increases likelihood)
- Does the device have known unpatched CVEs? (increases likelihood)
- Is the device on a segmented network? (decreases likelihood)
- Does the device have default credentials? (increases likelihood)

Impact factors:
- Clinical criticality (life-sustaining = highest impact)
- Data sensitivity (PHI = high impact)
- Number of patients affected
- Availability of backup/alternative workflow
```

### Step 3: Risk Treatment

| Treatment | When to Use |
|---|---|
| Mitigate | Apply controls to reduce risk (segmentation, monitoring, patching) |
| Accept | Risk is low and cost of mitigation exceeds benefit — document and sign off |
| Transfer | Insurance, vendor contract (SLA for patch delivery) |
| Avoid | Decommission the device or do not deploy |

### Step 4: Document and Review

```
Risk register entry per device / device type:
- Risk description
- Likelihood and impact scores
- Current controls
- Residual risk level
- Risk owner (clinical engineering manager, CISO, etc.)
- Review date (at least annual, or when new CVE / vendor advisory published)
```

---

## Compensating Controls Matrix

For devices that **cannot be patched** (legacy OS, vendor no longer supports):

| Control | Effect |
|---|---|
| Network segmentation (dedicated VLAN + firewall) | Reduces attack surface, limits lateral movement |
| Passive network monitoring (IDS) | Detects anomalous behaviour |
| Virtual patching (IPS rules for specific CVEs) | Blocks known exploit traffic without touching the device |
| Disable unnecessary services | Reduces exposed attack surface |
| Restricted admin access (MFA, jump server) | Prevents credential-based attacks |
| Increased logging (network-level) | Improves detection and forensic capability |
| Scheduled replacement plan | Defines timeline to replace with a supported device |
| Formal risk acceptance | Documents that leadership accepts the residual risk |

---

## Regulatory Compliance Mapping

| Requirement | Framework | Medical Device Relevance |
|---|---|---|
| Risk assessment | HIPAA §164.308(a)(1) | Required for all systems handling ePHI — includes medical devices |
| Technical safeguards | HIPAA §164.312 | Access control, audit, integrity, encryption for devices handling PHI |
| Device security plan | FDA Premarket (2023) | Manufacturers must submit cybersecurity risk documentation |
| Postmarket monitoring | FDA Postmarket (2016) | Manufacturers must monitor and address vulnerabilities in fielded devices |
| Network risk management | IEC 80001-1 | Hospital responsible for risk when connecting medical devices to network |
| Zone/conduit security | IEC 62443 | Network architecture must enforce security zones and conduits |

---

## Related Notes

- Medical-Devices-Overview — Section overview
- FDA-Cybersecurity-Guidance — Regulatory requirements for device security
- Medical-Device-Network-Security — Network controls implementation
- Vulnerability-Management — CVE/CVSS methodology
- Incident-Response — General IR framework

---

## References

- IEC 80001-1:2021: Application of risk management for IT-networks incorporating medical devices
- IEC 62443: Industrial communication networks — Network and system security
- NIST Cybersecurity Framework: https://www.nist.gov/cyberframework
- NIST SP 800-30: Guide for Conducting Risk Assessments: https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final
- AAMI TIR57:2016: Principles for medical device security — Risk management
- HHS HPH Cybersecurity Performance Goals: https://hphcyber.hhs.gov/performance-goals.html

---
*Note created: 2026-03-27*

---
<- [[Medical-Devices-MOC]]