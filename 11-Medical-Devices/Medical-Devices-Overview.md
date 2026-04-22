---
title: Medical Device Cybersecurity — Overview
type: overview
category: medical-devices
created: 2026-03-27
updated: 2026-03-27
tags:
  - medical-devices
  - iot
  - ot
  - cybersecurity
  - healthcare
  - fda
  - overview
  - foundational
difficulty: foundational
---

# 🏥 Medical Device Cybersecurity — Overview

## What This Section Covers

Medical device cybersecurity sits at the intersection of **healthcare IT, IoT/OT security, and patient safety**. Connected medical devices — infusion pumps, imaging systems, ventilators, patient monitors — are now network-attached endpoints that require the same security rigour as traditional IT assets, plus additional considerations for clinical safety and regulatory compliance.

This section provides job-aid depth notes for anyone working in healthcare IT security, clinical engineering, or hospital IT operations.

---

## Section Notes

| Note | Focus Area |
|---|---|
| [[FDA-Cybersecurity-Guidance]] | FDA premarket and postmarket cybersecurity guidance, SBOM requirements, regulatory expectations |
| [[Medical-Device-Network-Security]] | Network segmentation, monitoring, NAC, and architecture for clinical device environments |
| [[IoT-OT-Security-Fundamentals]] | IoT/OT security principles — protocols, attack surfaces, Purdue model, applicable to healthcare |
| [[Medical-Device-Risk-Management]] | Risk frameworks: IEC 62443, IEC 80001-1, NIST CSF for healthcare, threat modelling |
| [[Medical-Device-Incident-Response]] | Incident response procedures specific to medical device and clinical environments |

---

## Why Medical Device Security Matters

**Scale:** The average hospital has 10–15 connected medical devices per bed. A 500-bed hospital may have 5,000–7,500 networked devices — many running legacy OS (Windows XP/7, embedded Linux) that cannot be patched traditionally.

**Patient safety:** A compromised infusion pump or ventilator is not just a data breach — it is a patient safety event. The threat model extends beyond confidentiality (HIPAA) to availability and integrity of life-sustaining systems.

**Regulatory landscape:** The FDA now requires cybersecurity documentation as part of premarket device submissions. Hospitals must demonstrate they manage device risk throughout the product lifecycle.

**Key challenges:**
- Legacy devices with no vendor patches or update mechanisms
- Flat clinical networks with minimal segmentation
- Devices that cannot run endpoint agents (real-time OS, embedded firmware)
- Long device lifecycles (10–20 years) vs short IT refresh cycles (3–5 years)
- Shared responsibility between manufacturer (design security) and hospital (operational security)

---

## Key Frameworks and Standards

| Framework | Scope |
|---|---|
| FDA Premarket Guidance (2023) | Cybersecurity requirements for new device submissions |
| FDA Postmarket Guidance (2016) | Managing cybersecurity vulnerabilities in fielded devices |
| NIST Cybersecurity Framework (CSF) | Risk-based security framework — widely adopted in healthcare |
| IEC 62443 | Industrial automation and control system security — applicable to medical OT |
| IEC 80001-1 | Risk management for IT networks incorporating medical devices |
| HIPAA Security Rule | Technical safeguards for ePHI — applies to devices handling patient data |
| MITRE ATT&CK for ICS | Threat intelligence framework for industrial/OT environments |
| HHS HPH CPGs | Health and Public Health Sector Cybersecurity Performance Goals |

---

## Quick-Access by Situation

| I need to... | Go to |
|---|---|
| Understand FDA cybersecurity requirements | [[FDA-Cybersecurity-Guidance]] |
| Segment clinical devices on the network | [[Medical-Device-Network-Security]] |
| Understand IoT/OT protocols and attack surfaces | [[IoT-OT-Security-Fundamentals]] |
| Assess risk for a medical device deployment | [[Medical-Device-Risk-Management]] |
| Respond to an incident involving a clinical device | [[Medical-Device-Incident-Response]] |
| Understand HIPAA technical controls | HIPAA-Technical-Safeguards |
| Understand healthcare IT integration patterns | Healthcare-IT-Integration-Patterns |

---

## Related Vault Sections

- Healthcare-IT-Overview — Broader healthcare IT context
- HIPAA-Technical-Safeguards — HIPAA technical controls
- Incident-Response — General IR methodology (NIST SP 800-61)
- Vulnerability-Management — CVE/CVSS fundamentals
- Network-Defense-Blue-Team — IDS/IPS, SIEM, SOC operations

---
*Section created: 2026-03-27*

---
<- [[Medical-Devices-MOC]]