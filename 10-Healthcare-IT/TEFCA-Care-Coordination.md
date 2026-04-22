---
title: "TEFCA & Care Coordination"
type: reference
category: healthcare-it
created: 2026-03-27
updated: 2026-03-27
tags: [healthcare-it, tefca, care-coordination, interoperability, qhin, onc, fhir, information-blocking, trusted-exchange, intermediate]
source: "ONC TEFCA documentation (healthit.gov), 21st Century Cures Act, ONC Information Blocking Rule, Sequoia Project (RCE), healthit.gov"
difficulty: intermediate
---

# 🔗 TEFCA & Care Coordination

## Overview

The **Trusted Exchange Framework and Common Agreement (TEFCA)** is the US national framework for nationwide health information exchange. It establishes a universal governance, policy, and technical infrastructure so that healthcare organisations can share patient data electronically across network boundaries — without requiring point-to-point agreements between every pair of organisations.

TEFCA is the practical answer to a decades-old problem: a patient's records are scattered across dozens of providers, payers, and systems with no standardised way to query and retrieve them across organisational boundaries.

---

## Why TEFCA Exists — The Interoperability Problem

```
Before TEFCA:
Hospital A (Epic) ←→ custom interface ←→ Hospital B (Cerner)
Hospital A (Epic) ←→ different custom interface ←→ Clinic C (athenahealth)
Hospital A (Epic) ←→ yet another interface ←→ Lab D (Quest)
Hospital A (Epic) ←→ regional HIE ←→ Hospital E (Meditech)

Each connection = separate legal agreement + custom technical integration
N organisations = N×(N-1)/2 potential point-to-point connections
Result: fragmented, expensive, incomplete data sharing
```

```
With TEFCA:
Hospital A → connects once to a QHIN → can exchange with ALL other QHINs
Hospital B → connects once to a QHIN → can exchange with ALL other QHINs
Clinic C   → connects once to a QHIN → can exchange with ALL other QHINs

One agreement. One technical standard. Nationwide reach.
```

---

## Key Components

### Trusted Exchange Framework (TEF)

The **policy framework** — a set of non-binding principles established by ONC (Office of the National Coordinator for Health IT) that describe how health information networks should operate.

**Core principles:**
- Standardised data formats (FHIR, C-CDA)
- Patient access to their own records
- No information blocking
- Privacy and security protections
- Transparent governance

### Common Agreement (CA)

The **binding legal agreement** that organisations sign to participate in TEFCA. Managed by the **Recognized Coordinating Entity (RCE)** — currently **The Sequoia Project**.

The Common Agreement establishes:
- Governance rules for all participants
- Technical requirements (standards, protocols, security)
- Privacy and security obligations
- Dispute resolution procedures
- Permitted purposes for data exchange (Treatment, Payment, Healthcare Operations, Public Health, etc.)

### Qualified Health Information Networks (QHINs)

**QHINs** are the designated network nodes in TEFCA. They are health information networks that have been approved by the RCE to participate in TEFCA.

```
TEFCA hierarchy:
RCE (The Sequoia Project) — oversees the framework
    └── QHINs (approved national networks)
          └── Participants (healthcare organisations connected to a QHIN)
                └── Sub-Participants (smaller entities connected via a Participant)
```

**How a hospital participates:**
1. Hospital connects to one or more **QHINs** (e.g., eHealth Exchange, KONZA, Epic Nexus, Kno2, MedAllies, CommonWell/Carequality via QHIN)
2. QHIN handles routing queries to other QHINs nationwide
3. Hospital can query/retrieve records from any other TEFCA-connected organisation

**Designated QHINs (as of early 2026):** [VERIFY: current QHIN list at rce.sequoiaproject.org — QHINs are being onboarded on a rolling basis]
- eHealth Exchange
- KONZA National Health Information Network
- Epic Nexus
- Kno2
- MedAllies
- Health Gorilla
- CommonWell/Carequality (via QHIN designation)
- Additional QHINs being onboarded

---

## TEFCA Exchange Purposes

TEFCA defines specific **permitted purposes** for querying and exchanging health information:

| Exchange Purpose | Description |
|---|---|
| Treatment | Provision, coordination, or management of healthcare |
| Payment | Activities related to health plan coverage and reimbursement |
| Healthcare Operations | Quality assessment, competency assurance, auditing |
| Public Health | Public health surveillance, reporting, investigation |
| Government Benefits Determination | Eligibility for government health programmes |
| Individual Access Services (IAS) | Patients requesting their own records via a third-party app |

**Individual Access Services (IAS)** is notable — it enables patients to use third-party apps (e.g., Apple Health, patient-facing FHIR apps) to pull their records from any TEFCA-connected provider.

---

## Technical Standards

### Data Format

TEFCA exchanges use standardised clinical document formats:

| Standard | Use |
|---|---|
| **C-CDA (Consolidated Clinical Document Architecture)** | Primary document format for care summaries — a structured XML document containing demographics, problems, medications, allergies, procedures, results |
| **FHIR R4** | Modern API-based exchange — increasingly used for real-time queries and IAS |
| **USCDI (US Core Data for Interoperability)** | Defines the minimum dataset that must be exchangeable — maintained by ONC, updated annually |

### USCDI — What Data Must Be Shared

The **USCDI** defines the data classes and elements that are required for interoperability:

**USCDI v4 (current) data classes:**
```
- Patient Demographics (name, DOB, address, race, ethnicity, sex, gender identity)
- Allergies and Intolerances
- Assessment and Plan of Treatment
- Care Team Members
- Clinical Notes (progress notes, discharge summaries, procedure notes, consult notes)
- Clinical Tests (imaging, pulmonary function, etc.)
- Diagnostic Imaging Reports
- Encounter Information
- Goals
- Health Concerns
- Health Insurance Information
- Immunizations
- Laboratory Results
- Medications
- Problems (diagnosis list)
- Procedures
- Provenance (who created the data and when)
- Smoking Status
- Social Determinants of Health (SDOH)
- Unique Device Identifiers (for implants)
- Vital Signs
```

> Source: https://www.healthit.gov/isa/united-states-core-data-interoperability-uscdi

### Exchange Patterns

TEFCA supports multiple exchange patterns:

| Pattern | Description |
|---|---|
| **Query/Response** | Requesting organisation queries a QHIN for a specific patient's records; responding organisation returns matching documents |
| **Directed Exchange** | Sending a document directly to a known recipient (like secure email for clinical documents) |
| **Broadcast / Notification** | Event notifications (e.g., ADT notifications — patient admitted/discharged/transferred) |

---

## 21st Century Cures Act — Information Blocking

TEFCA exists in the context of the **21st Century Cures Act (2016)** and its **Information Blocking Rule (effective April 2021)**.

### What Is Information Blocking?

A practice by a healthcare provider, health IT developer, or health information network/exchange that is likely to **interfere with, prevent, or materially discourage** access, exchange, or use of electronic health information (EHI).

**Who it applies to:**
| Actor | Consequence of Violation |
|---|---|
| Healthcare providers | Investigation by HHS OIG; potential civil monetary penalties |
| Health IT developers (EHR vendors) | Investigation by ONC; potential decertification of EHR product |
| Health Information Networks/Exchanges | Investigation by ONC |

### Exceptions (Permissible Reasons to Limit Sharing)

The rule defines **eight exceptions** where limiting data sharing is NOT considered information blocking:

| Exception | Example |
|---|---|
| Preventing Harm | Withholding data if sharing would endanger the patient (e.g., domestic violence context) |
| Privacy | Honouring patient's request to restrict sharing (with limitations) |
| Security | Data not shared due to a security threat (must be temporary and proportionate) |
| Infeasibility | Technical inability to share (legacy system, no interface available) |
| Health IT Performance | Temporary downtime for maintenance |
| Content and Manner | Data available in alternative format or timeline |
| Fees | Reasonable fees for data access (cannot be prohibitive) |
| Licensing | Reasonable licensing terms for interoperability interfaces |

**Key takeaway:** The default is that data MUST be shared. Exceptions are narrow and must be justified.

---

## Care Coordination Workflows

### ADT Notifications (Admit-Discharge-Transfer)

ADT event notifications are a cornerstone of care coordination — alerting a patient's primary care provider, care manager, or health plan when the patient is admitted to or discharged from a hospital.

```
Patient admitted to Hospital A (ED visit)
    ↓ ADT notification sent via TEFCA/HIE
Primary Care Physician notified
Care Manager notified
Health Plan notified
    ↓
Enables: post-discharge follow-up, medication reconciliation, care plan updates
```

**CMS Interoperability and Patient Access Final Rule** requires hospitals to send electronic ADT notifications to relevant community providers.

### Transition of Care (ToC) Documents

When a patient transitions between care settings (hospital → rehab, hospital → home with home health):

```
Discharge from Hospital
    ↓ C-CDA Transition of Care document generated
Document contains: discharge summary, medication list, follow-up instructions, active problems
    ↓ Sent via TEFCA directed exchange to receiving provider
Receiving provider imports into their EHR
    ↓
Continuity of care maintained
```

### Referral Management

```
PCP refers patient to Specialist
    ↓ Referral document (C-CDA or FHIR) sent via directed exchange
Specialist receives referral + relevant clinical history
    ↓ Specialist visit occurs
Specialist sends consult note back to PCP
    ↓
Closed-loop referral tracking
```

### Social Determinants of Health (SDOH) Coordination

USCDI v4 includes SDOH data elements. Care coordination increasingly involves:
- Screening patients for SDOH needs (food insecurity, housing, transportation)
- Referring to community-based organisations (CBOs) via closed-loop referral platforms
- Tracking outcomes of SDOH interventions

**Standards:** Gravity Project (HL7) defines SDOH-specific FHIR profiles and value sets.

---

## TEFCA and FHIR

TEFCA is increasingly FHIR-aligned:

- **FHIR R4** is the technical standard for IAS (Individual Access Services)
- QHINs must support FHIR-based queries for patient access use cases
- The HL7 Da Vinci Project provides FHIR implementation guides for payer-provider exchange under TEFCA
- **SMART on FHIR** enables patient-authorised third-party app access to their records via TEFCA

```
Patient uses SMART on FHIR app
    ↓ Authenticates with their provider's patient portal
    ↓ App requests data via FHIR API
    ↓ Provider's FHIR server returns USCDI dataset
    ↓ Patient can aggregate records from multiple providers
```

> See also: [[HL7-FHIR-Fundamentals]] for FHIR technical details.

---

## Security and Privacy in TEFCA

| Control | Requirement |
|---|---|
| Encryption in transit | TLS 1.2+ required for all TEFCA exchanges |
| Authentication | QHINs must authenticate Participants; mutual TLS or OAuth 2.0 |
| Authorisation | Role-based access; exchange limited to permitted purposes |
| Audit logging | All TEFCA exchanges must be logged with: who, what, when, why (purpose) |
| Patient consent | State-specific consent laws honoured; TEFCA does not override stricter state privacy laws |
| Breach notification | HIPAA breach notification requirements apply to all TEFCA participants |
| Minimum necessary | Only data needed for the stated purpose should be exchanged |

**State law considerations:**
Some states have stricter privacy laws than HIPAA (e.g., substance abuse records under 42 CFR Part 2, mental health records in certain states). TEFCA requires compliance with the most restrictive applicable law.

---

## Implementation Considerations for Hospitals

### Connecting to TEFCA

```
Step 1: Evaluate which QHIN(s) serve your region and clinical needs
Step 2: Execute a Participant Agreement with the chosen QHIN
Step 3: Implement technical connection (FHIR API, C-CDA exchange, or both)
Step 4: Configure your EHR to query/respond to TEFCA requests
Step 5: Train staff on new workflows (query-based exchange, ADT notifications)
Step 6: Monitor exchange volume, errors, and patient matching accuracy
```

### Patient Matching

TEFCA does not (yet) have a national patient identifier. Patient matching relies on **demographic-based matching algorithms**:

```
Matching fields: First name, Last name, DOB, Sex, Address, Phone, SSN (last 4)
Challenge: variations in name spelling, address formats, maiden names
False positives: matching the wrong patient (safety risk)
False negatives: failing to match the right patient (missed data)
```

**Best practices:**
- Use high-quality demographic data in your EHR (standardised addresses, verified phone numbers)
- Implement a Master Patient Index (MPI) or Enterprise MPI (EMPI)
- Monitor match rates and investigate false-positive/false-negative trends

### Duplicate Record Management

When external records are retrieved via TEFCA, they may not perfectly match your internal patient chart. Establish workflows for:
- Reviewing externally retrieved documents before incorporation
- Reconciling medication lists, problem lists, and allergies
- Flagging potential duplicate patients for manual review

---

## Key Resources

| Resource | URL |
|---|---|
| ONC TEFCA Overview | https://www.healthit.gov/topic/interoperability/policy/trusted-exchange-framework-and-common-agreement-tefca |
| Sequoia Project (RCE) | https://rce.sequoiaproject.org |
| USCDI | https://www.healthit.gov/isa/united-states-core-data-interoperability-uscdi |
| Information Blocking Rule | https://www.healthit.gov/topic/information-blocking |
| 21st Century Cures Act | https://www.congress.gov/bill/114th-congress/house-bill/34 |
| HL7 Gravity Project (SDOH) | https://thegravityproject.net |
| HL7 Da Vinci Project (payer exchange) | https://www.hl7.org/about/davinci/ |

---

## Common Scenarios

**Scenario 1:** A patient arrives at your ED unconscious with no medical history available. How does TEFCA help?
→ Query the patient's records via your QHIN connection using available demographics (name, DOB from ID). TEFCA-connected providers nationwide can respond with the patient's medications, allergies, and active problems — enabling safer treatment decisions.

**Scenario 2:** Your hospital's EHR vendor says they will charge $50,000 for a FHIR API interface. Is this information blocking?
→ Potentially. Under the Cures Act, EHR vendors must provide interoperability interfaces. The **Fees Exception** allows reasonable fees but prohibits fees that are designed to discourage access. Report concerns to ONC.

**Scenario 3:** A patient requests that their substance abuse treatment records not be shared via TEFCA. Must you comply?
→ Yes. **42 CFR Part 2** provides specific privacy protections for substance use disorder (SUD) treatment records. These records require explicit patient consent for disclosure, and TEFCA must honour this stricter standard.

**Scenario 4:** Your organisation wants to send ADT notifications to community providers when patients are admitted or discharged. What standard and workflow apply?
→ Use HL7v2 ADT messages or FHIR-based event notifications routed through your QHIN. CMS requires hospitals to send these notifications. Configure your EHR to trigger notifications on admit/discharge/transfer events and route them to the patient's care team via TEFCA or your regional HIE.

---

## Related Notes

- Healthcare-IT-Overview — Broader healthcare IT context
- HL7-FHIR-Fundamentals — FHIR technical details (R4, resources, SMART on FHIR)
- HL7-v2-Messaging — HL7v2 ADT messaging (used for event notifications)
- Healthcare-IT-Integration-Patterns — Integration architecture patterns
- HIPAA-Technical-Safeguards — Privacy and security controls
- ServiceNow-Epic-Integration — EHR integration with IT service management

---

## References

- ONC TEFCA: https://www.healthit.gov/topic/interoperability/policy/trusted-exchange-framework-and-common-agreement-tefca
- Sequoia Project (RCE): https://rce.sequoiaproject.org
- USCDI: https://www.healthit.gov/isa/united-states-core-data-interoperability-uscdi
- 21st Century Cures Act: https://www.congress.gov/bill/114th-congress/house-bill/34
- ONC Information Blocking: https://www.healthit.gov/topic/information-blocking
- 42 CFR Part 2 (SUD records): https://www.ecfr.gov/current/title-42/chapter-I/subchapter-A/part-2

---
*Note created: 2026-03-27*

---
<- [[Healthcare-IT-MOC]]