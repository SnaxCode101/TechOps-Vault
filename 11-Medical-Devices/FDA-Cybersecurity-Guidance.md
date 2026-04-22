---
title: FDA Cybersecurity Guidance for Medical Devices
type: reference
category: medical-devices
created: 2026-03-27
updated: 2026-03-27
tags:
  - medical-devices
  - fda
  - cybersecurity
  - regulatory
  - sbom
  - premarket
  - postmarket
  - patch-management
  - intermediate
source: FDA Premarket Cybersecurity Guidance (2023), FDA Postmarket Management
  of Cybersecurity (2016), PATCH Act (2022), FDA.gov
difficulty: intermediate
---

# 🏛 FDA Cybersecurity Guidance for Medical Devices

## Overview

The FDA regulates medical device cybersecurity through **premarket submission requirements** (before a device is sold) and **postmarket guidance** (after a device is in use). Since 2023, cybersecurity is a mandatory component of device submissions — manufacturers must demonstrate secure design, provide a Software Bill of Materials (SBOM), and commit to ongoing vulnerability management.

---

## Regulatory Timeline

| Year | Milestone |
|---|---|
| 2014 | FDA issues first premarket cybersecurity guidance (non-binding) |
| 2016 | FDA Postmarket Management of Cybersecurity in Medical Devices guidance |
| 2022 | PATCH Act (Protecting and Transforming Cyber Healthcare) signed — gives FDA explicit authority |
| 2023 | FDA Premarket Cybersecurity Guidance updated — SBOM now required; "Refuse to Accept" authority |
| 2023+ | FDA can refuse to review (RTA) premarket submissions lacking cybersecurity documentation |

---

## Premarket Guidance (2023) — Key Requirements

The FDA's 2023 premarket guidance applies to **all new device submissions** (510(k), PMA, De Novo) for devices that contain software or are connected.

### What Manufacturers Must Provide

**1. Secure Product Development Framework (SPDF)**

Manufacturers must demonstrate a secure-by-design approach throughout the device development lifecycle:
- Threat modelling performed during design
- Security requirements derived from threat model
- Security testing (static analysis, dynamic analysis, penetration testing, fuzz testing)
- Vulnerability disclosure policy established
- Coordinated vulnerability disclosure (CVD) process

**2. Software Bill of Materials (SBOM)**

```
An SBOM lists every software component in the device:
- Commercial software (OS, libraries, frameworks)
- Open-source components (with versions)
- Custom/proprietary code modules

Format: CycloneDX or SPDX (machine-readable)
Depth: must include transitive dependencies (dependencies of dependencies)
Purpose: enables hospitals to identify affected devices when a CVE is published
```

**Why SBOM matters for hospitals:**
- When a new CVE is published (e.g., Log4Shell), hospitals can cross-reference SBOMs against their device inventory to identify which devices contain the vulnerable component
- Without SBOMs, hospitals have no way to assess exposure

**3. Cybersecurity Management Plan**

Documentation that the manufacturer will:
- Monitor for new vulnerabilities affecting the device throughout its lifecycle
- Provide patches and updates in a timely manner
- Communicate vulnerability status to customers (ICS-CERT advisories, MDS2 forms)
- Define end-of-support timelines

**4. Security Architecture Documentation**

- Network architecture diagrams showing how the device communicates
- Authentication and authorisation mechanisms
- Encryption at rest and in transit
- Logging and audit capabilities
- Default configuration (secure defaults expected)

---

### Device Categorisation

The FDA categorises devices by cybersecurity risk:

| Category | Definition | Examples |
|---|---|---|
| Cyber devices | Devices that contain software, connect to the internet, or could be vulnerable to cybersecurity threats | Infusion pumps, patient monitors, imaging systems, ventilators, implantable devices with wireless capability |
| Higher cybersecurity risk | Devices whose compromise could directly harm patients or disrupt critical healthcare infrastructure | Class III devices, devices connected to hospital networks, devices that control drug delivery |

---

## Postmarket Guidance (2016) — Key Concepts

The postmarket guidance addresses **cybersecurity management of devices already in use in healthcare settings**.

### Manufacturer Responsibilities (Postmarket)

**Ongoing vulnerability monitoring:**
- Manufacturers must monitor for new vulnerabilities affecting their fielded devices
- Sources: NVD, ICS-CERT, researcher disclosures, customer reports

**Vulnerability assessment and communication:**
- Assess each vulnerability for patient safety impact
- Controlled vs uncontrolled risk:
  - **Controlled risk:** vulnerability exists but compensating controls reduce risk to acceptable level → routine update cycle
  - **Uncontrolled risk:** vulnerability cannot be adequately mitigated → urgent action required, potential safety communication

**Coordinated vulnerability disclosure:**
- Manufacturers expected to have a vulnerability disclosure policy
- Coordinate with researchers, ICS-CERT (now CISA), and healthcare facilities
- Provide timely patches, mitigations, or safety communications

### Manufacturer Disclosure Statement for Medical Device Security (MDS2)

The **MDS2 form** is a standardised manufacturer disclosure document that hospitals request from device vendors.

```
MDS2 provides:
- Device OS and software components
- Network connectivity requirements (ports, protocols)
- Authentication capabilities
- Encryption capabilities
- Audit logging capabilities
- Data stored on device (PHI, PII)
- Automatic logoff capability
- Antimalware compatibility
- Patch/update mechanism
- End-of-life/end-of-support dates
```

**Navigator tip:** Always request MDS2 forms from vendors during procurement — they drive your network architecture and risk decisions.

> Source: MDS2 form maintained by NEMA/MITA (now managed by the Medical Imaging & Technology Alliance)

---

## PATCH Act (2022) — Legislative Authority

The **Protecting and Transforming Cyber Health (PATCH) Act** codified FDA's cybersecurity authority into law.

**Key provisions:**
- FDA can **refuse to accept** (RTA) premarket submissions that lack cybersecurity documentation
- Manufacturers of "cyber devices" must:
  - Submit a plan to monitor, identify, and address postmarket cybersecurity vulnerabilities
  - Design and maintain processes for providing updates and patches
  - Provide an SBOM (including commercial, open-source, and off-the-shelf components)
- CISA and FDA coordinate on medical device cybersecurity threat intelligence

---

## What Hospitals Should Do — Practical Guidance

### During Procurement

```
□ Request MDS2 form from every device vendor
□ Request SBOM (CycloneDX or SPDX format)
□ Review vendor's vulnerability disclosure policy
□ Confirm patch/update delivery mechanism and timeline
□ Confirm end-of-support date (plan for device lifecycle)
□ Verify device supports network segmentation (VLAN, firewall rules)
□ Confirm device can be configured with unique credentials (no hardcoded passwords)
□ Confirm encryption capabilities (TLS for data in transit, AES for data at rest)
□ Verify audit logging capability (syslog, event log)
□ Confirm compatibility with hospital security tools (NAC, vulnerability scanner)
```

### During Deployment

```
□ Place device on segmented clinical VLAN (not flat corporate network)
□ Apply principle of least privilege for network access (firewall rules: allow only required ports)
□ Change all default credentials before deployment
□ Enable audit logging and forward to SIEM
□ Register device in CMDB / asset inventory with SBOM reference
□ Apply any available vendor patches before go-live
□ Document compensating controls for devices that cannot be patched
```

### During Operations

```
□ Monitor CISA ICS-CERT advisories for medical device vulnerabilities
□ Cross-reference new CVEs against device SBOMs
□ Coordinate with vendor for patch availability
□ Apply patches during scheduled maintenance windows
□ For unpatchable devices: document compensating controls (segmentation, monitoring, ACLs)
□ Include medical devices in vulnerability scanning programme (passive/agent-based where possible)
□ Review MDS2 forms annually or when vendor provides updates
```

---

## Key FDA Resources

| Resource | URL |
|---|---|
| FDA Premarket Cybersecurity Guidance (2023) | https://www.fda.gov/regulatory-information/search-fda-guidance-documents/cybersecurity-medical-devices-quality-system-considerations-and-content-premarket-submissions |
| FDA Postmarket Cybersecurity Guidance (2016) | https://www.fda.gov/regulatory-information/search-fda-guidance-documents/postmarket-management-cybersecurity-medical-devices |
| CISA Medical Device Advisories | https://www.cisa.gov/news-events/ics-medical-advisories |
| MDS2 Form (MITA) | https://www.medicalimaging.org/policy-technical/mds2/ |
| PATCH Act text | https://www.congress.gov/bill/117th-congress/house-bill/7084 |

---

## Common Scenarios

**Scenario 1:** A vulnerability is disclosed in a third-party library used by an infusion pump. How do you assess exposure?
→ Cross-reference the pump's **SBOM** against the CVE to confirm the component is present. Check the vendor's advisory for a patch timeline. If no patch is available, implement **compensating controls** (network segmentation, IDS monitoring) and document the residual risk.

**Scenario 2:** A new imaging system is being procured. The vendor has not provided cybersecurity documentation. What should you do?
→ Request the **MDS2 form** and **SBOM**. Under the PATCH Act, the FDA can refuse to accept devices without cybersecurity documentation — the vendor should have this documentation available. If they do not, escalate to procurement and consider alternative vendors.

**Scenario 3:** A legacy ultrasound system runs Windows XP and the vendor has declared end-of-support. What are your options?
→ Implement **compensating controls**: isolate the device on a dedicated VLAN, restrict network access to only required clinical systems, deploy passive network monitoring/IDS, disable unnecessary services, and document the risk acceptance with clinical engineering and information security leadership. Plan for device replacement.

---

## Related Notes

- Medical-Devices-Overview — Section overview
- Medical-Device-Risk-Management — Risk frameworks for device deployments
- Medical-Device-Network-Security — Network architecture for clinical devices
- HIPAA-Technical-Safeguards — HIPAA technical controls
- Vulnerability-Management — General vulnerability management methodology

---

## References

- FDA Premarket Cybersecurity Guidance (2023): https://www.fda.gov/regulatory-information/search-fda-guidance-documents/cybersecurity-medical-devices-quality-system-considerations-and-content-premarket-submissions
- FDA Postmarket Cybersecurity Guidance (2016): https://www.fda.gov/regulatory-information/search-fda-guidance-documents/postmarket-management-cybersecurity-medical-devices
- PATCH Act (2022): https://www.congress.gov/bill/117th-congress/house-bill/7084
- CISA ICS-CERT Medical Device Advisories: https://www.cisa.gov/news-events/ics-medical-advisories

---
*Note created: 2026-03-27*

---
<- [[Medical-Devices-MOC]]