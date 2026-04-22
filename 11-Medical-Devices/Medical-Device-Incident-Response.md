---
title: Medical Device Incident Response
type: reference
category: medical-devices
created: 2026-03-27
updated: 2026-03-27
tags:
  - medical-devices
  - incident-response
  - clinical-safety
  - ransomware
  - healthcare
  - ir
  - forensics
  - intermediate
source: NIST SP 800-61, HHS HPH Cyber Performance Goals, FDA postmarket
  guidance, CISA healthcare advisories
difficulty: intermediate
---

# 🚨 Medical Device Incident Response

## Overview

Incident response involving medical devices adds a critical dimension absent from standard IT IR: **patient safety**. Every containment decision must balance security objectives with clinical care continuity. Disconnecting a compromised network switch might stop an attacker — but it might also disable patient monitors in an ICU.

This note covers IR procedures adapted for medical device and clinical environments, building on the general Incident-Response framework (NIST SP 800-61).

---

## Medical Device IR vs Standard IT IR

| Aspect | Standard IT IR | Medical Device IR |
|---|---|---|
| Primary concern | Data confidentiality | Patient safety + data |
| Containment speed | Isolate immediately | Clinical safety assessment FIRST |
| Stakeholders | IT, security, legal | + Clinical engineering, nursing, physicians, risk management |
| Evidence collection | Standard forensics | May be limited (embedded OS, no disk image possible) |
| Device replacement | Swap hardware | Requires clinical approval; may need vendor involvement |
| Regulatory reporting | Breach notification (HIPAA) | + FDA MedWatch if device malfunction affects patient safety |
| Communication | IT leadership, legal | + Clinical leadership, patient safety officer |

---

## Medical Device IR Phases

### Phase 1 — Preparation

**Build the team before the incident:**

```
Medical Device IR Team:
□ Information Security (IR lead, SOC analyst)
□ Clinical Engineering / Biomedical Engineering (device expertise)
□ Network Engineering (segmentation, containment)
□ Clinical Leadership (nursing director, physician champion)
□ Risk Management / Patient Safety Officer
□ Legal / Compliance (HIPAA breach determination)
□ Vendor contacts (device manufacturer emergency line)
□ Communications (internal and external)
```

**Pre-incident preparation checklist:**
```
□ Medical device inventory maintained and current (CMDB / asset registry)
□ Network diagrams showing clinical device placement and VLAN assignments
□ Vendor emergency contact list for all major device manufacturers
□ Clinical downtime procedures documented and tested (paper-based workflows)
□ Backup configurations for device management servers (drug libraries, alarm settings)
□ SIEM rules and IDS signatures for medical device anomalies
□ Tabletop exercises conducted annually involving clinical and IT staff
□ MDS2 forms on file for all network-connected devices
□ SBOMs on file for high-risk devices
```

**Clinical downtime procedures:**
Every unit should have tested procedures for operating without network-connected medical devices:
- Paper medication administration records (MARs)
- Manual vital signs documentation
- Phone-based lab ordering and results
- Printed patient wristbands
- Manual inventory tracking

---

### Phase 2 — Detection and Analysis

**Detection sources for medical device incidents:**

| Source | Example Alert |
|---|---|
| Passive network monitor (Claroty/Medigate) | Infusion pump communicating with unknown IP |
| IDS/IPS (Snort/Suricata) | Exploit signature detected targeting medical device CVE |
| SIEM correlation | Multiple failed authentications to device management server |
| Clinical staff report | "The monitor screen looks different" or "The pump is behaving strangely" |
| Vendor advisory | Manufacturer issues urgent security bulletin |
| CISA ICS-CERT advisory | New vulnerability disclosed affecting deployed device model |

**Triage questions (ask immediately):**
```
1. Is patient safety currently at risk? (YES → activate clinical safety protocol)
2. Which device(s) are affected? (model, location, count)
3. Is the device currently in use on a patient? (YES → coordinate with clinical staff before any action)
4. What is the device's network connectivity? (VLAN, IP, connected systems)
5. Is PHI potentially exposed? (YES → trigger HIPAA breach assessment)
6. Is the device communicating with known-bad indicators? (C2, known malicious IP)
```

**Clinical safety assessment (MANDATORY before containment):**
```
Before disconnecting or isolating ANY medical device:
□ Is the device currently in use on a patient?
□ Is there an alternative device or workflow available?
□ What is the clinical impact of taking the device offline?
□ Has the clinical team (nurse, physician) been notified and agreed?
□ Has clinical engineering confirmed the device can be safely disconnected?

NEVER unilaterally disconnect a life-sustaining device without clinical coordination.
```

---

### Phase 3 — Containment

**Containment strategies (least disruptive first):**

| Strategy | Use When | Risk |
|---|---|---|
| Network ACL restriction | Device is communicating with suspicious IP; restrict to known-good peers only | Low — device continues clinical function |
| VLAN quarantine | Device compromised but not actively harming patients; move to isolated VLAN with clinical traffic only | Medium — may disrupt some integrations |
| Port shutdown | Device is actively propagating malware or attacking other systems | High — device loses all network connectivity |
| Physical disconnection | Imminent patient safety risk from compromised device | Highest — device goes offline; clinical downtime activated |

**Containment decision tree:**
```
Is patient safety at immediate risk from the device?
├── YES → Physical disconnect + activate clinical downtime + notify clinical team
└── NO → Is the device actively spreading malware?
          ├── YES → Port shutdown or VLAN quarantine + activate downtime for affected unit
          └── NO → Apply network ACL restriction + increase monitoring
                    → Coordinate with vendor for investigation
```

**Vendor coordination during containment:**
```
□ Contact vendor emergency support line
□ Share indicators (network logs, error messages) — do NOT share PHI without BAA
□ Request: known vulnerability status, available patches, recommended mitigations
□ Coordinate: remote access for vendor investigation (through secure jump server)
□ Document: all vendor communications and recommendations
```

---

### Phase 4 — Eradication

**For compromised medical devices:**

```
Option A — Vendor-provided fix:
1. Vendor provides patch or firmware update
2. Test on non-clinical device first (if spare available)
3. Schedule update during maintenance window
4. Apply update with clinical engineering present
5. Verify device function post-update (clinical validation)
6. Return device to production

Option B — Factory reset:
1. Export device configuration (if possible)
2. Factory reset device to known-good state
3. Re-apply configuration from verified backup
4. Re-image if full OS involved (vendor-approved image only)
5. Clinical validation before returning to patient care

Option C — Device replacement:
1. Device is end-of-life or irreparably compromised
2. Deploy replacement device from inventory
3. Configure replacement per clinical specifications
4. Decommission compromised device (document and dispose per policy)
```

**For compromised device management infrastructure:**
```
Drug library servers, alarm management servers, device integration engines:
1. Isolate from network
2. Forensic image (if possible) before wiping
3. Rebuild from known-good backup or vendor-provided media
4. Verify integrity of clinical data (drug libraries, alarm settings)
5. Re-establish connections to medical devices
6. Clinical validation of end-to-end workflow
```

---

### Phase 5 — Recovery

```
Recovery checklist:
□ Affected devices restored to clinical operation
□ Clinical teams confirm device function (clinical validation sign-off)
□ Network monitoring confirms normal traffic patterns
□ SIEM/IDS tuned with new indicators from the incident
□ Compensating controls in place for any remaining vulnerabilities
□ Vendor patch timeline confirmed for long-term fix
□ Clinical downtime procedures deactivated
□ Patient safety officer confirms no patient harm occurred
```

---

### Phase 6 — Post-Incident / Lessons Learned

**Post-incident review (within 2 weeks):**
```
□ Timeline of events documented
□ Root cause identified
□ Clinical impact assessed (any patient safety events?)
□ Detection effectiveness evaluated (how was it found? how fast?)
□ Containment effectiveness evaluated (did the clinical safety assessment work?)
□ Communication effectiveness evaluated (were the right people notified in time?)
□ Improvement actions identified and assigned
□ Regulatory reporting completed (HIPAA breach? FDA MedWatch?)
```

**Regulatory reporting considerations:**

| Trigger | Report To | Timeline |
|---|---|---|
| PHI breach affecting ≥500 individuals | HHS OCR + affected individuals + media | Within 60 days |
| PHI breach affecting <500 individuals | HHS OCR (annual log) + affected individuals | Within 60 days (individuals); annual (HHS) |
| Device malfunction causing patient harm or death | FDA MedWatch (mandatory for manufacturers) | Per FDA reporting requirements |
| Device vulnerability exploitation | CISA ICS-CERT (voluntary but recommended) | As soon as practical |

---

## Ransomware Scenario — Medical Devices

Ransomware in a hospital environment is the highest-impact scenario for medical device IR.

**Immediate actions:**
```
1. Activate Hospital Incident Command (if ransomware is widespread)
2. Clinical downtime procedures for ALL affected units
3. Isolate affected network segments (contain spread)
4. Assess medical device impact:
   - Which devices are on affected network segments?
   - Are life-sustaining devices still operational?
   - Can devices operate in standalone mode (without network)?
5. Prioritise restoration: life-sustaining devices → life-supporting → non-critical
6. Coordinate with vendor for device-specific recovery guidance
7. Do NOT pay ransom without executive and legal review
8. Report to: FBI, CISA, HHS
```

**Medical device resilience during ransomware:**
- Most bedside monitors and infusion pumps can operate in **standalone mode** (without network) — alarms and therapy continue, but data does not flow to central systems
- Drug library servers being down means pumps may revert to basic mode (no dose limits) — HIGH RISK
- Imaging systems (CT, MRI) typically require network for full function — may need to divert patients
- Lab analysers may queue results locally but cannot send to EHR — manual result reporting needed

---

## Related Notes

- Medical-Devices-Overview — Section overview
- Incident-Response — General IR methodology (NIST SP 800-61)
- Medical-Device-Network-Security — Containment via network controls
- FDA-Cybersecurity-Guidance — Regulatory reporting requirements
- Medical-Device-Risk-Management — Risk assessment post-incident
- HIPAA-Technical-Safeguards — Breach determination and notification

---

## References

- NIST SP 800-61: Computer Security Incident Handling Guide: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final
- HHS HPH Cybersecurity Performance Goals: https://hphcyber.hhs.gov/performance-goals.html
- CISA Healthcare Cybersecurity: https://www.cisa.gov/topics/cybersecurity-best-practices/healthcare
- FDA Postmarket Cybersecurity Guidance: https://www.fda.gov/regulatory-information/search-fda-guidance-documents/postmarket-management-cybersecurity-medical-devices
- FDA MedWatch Reporting: https://www.fda.gov/safety/medwatch-fda-safety-information-and-adverse-event-reporting-program

---
*Note created: 2026-03-27*

---
<- [[Medical-Devices-MOC]]