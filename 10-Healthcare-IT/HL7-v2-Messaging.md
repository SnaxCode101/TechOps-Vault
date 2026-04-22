---
title: HL7 v2 Messaging
type: reference
tags:
  - hl7
  - v2
  - messaging
  - interoperability
  - healthcare-it
  - mllp
  - intermediate
section: 10-Healthcare-IT
created: 2026-03-27
difficulty: intermediate
---

# 📨 HL7 v2 Messaging

> HL7 version 2 (v2) is the dominant healthcare messaging standard for the past 30+ years. Despite FHIR's rise, v2 messages underpin most hospital integration engines and remain critical for ADT, lab, and order workflows.

---

## What Is HL7 v2?

HL7 v2 is a **text-based, pipe-delimited messaging standard** for exchanging clinical and administrative data between healthcare systems. A message is a structured string of segments, fields, and components.

**Common uses:**
- ADT (Admit, Discharge, Transfer) notifications
- Lab orders and results (ORM, ORU)
- Scheduling messages (SIU)
- MDM — medical document management
- DFT — financial transactions

---

## Message Structure

```
MSH|^~\&|SendingApp|SendingFacility|ReceivingApp|ReceivingFacility|20260327120000||ADT^A01|MSG00001|P|2.5.1
EVN|A01|20260327120000
PID|1||MRN12345^^^Hospital^MR||Smith^John^A||19800101|M|||123 Main St^^Springfield^IL^62701||555-555-1234
PV1|1|I|4N^401^A^Hospital||||12345^Jones^Sarah|||SUR||||ADM|A0
```

### Delimiters (MSH-1, MSH-2)
| Symbol | Use |
|---|---|
| `\|` | Field separator |
| `^` | Component separator |
| `~` | Repetition separator |
| `\` | Escape character |
| `&` | Subcomponent separator |

---

## Key Segments

| Segment | Name | Description |
|---|---|---|
| **MSH** | Message Header | Sender, receiver, datetime, message type, control ID |
| **EVN** | Event Type | Trigger event, recorded datetime |
| **PID** | Patient Identification | MRN, name, DOB, sex, address, phone |
| **PV1** | Patient Visit | Location, attending provider, admission type |
| **ORC** | Common Order | Order control, order ID, status |
| **OBR** | Observation Request | Test/procedure ordered |
| **OBX** | Observation Result | Lab result, value, units, reference range, status |
| **NTE** | Notes & Comments | Free-text comments attached to segments |
| **DG1** | Diagnosis | ICD code, description, type |
| **AL1** | Allergy Information | Allergen, severity, reaction |
| **IN1** | Insurance | Payer, plan, subscriber info |
| **SCH** | Scheduling Activity | Appointment scheduling (SIU messages) |
| **ZSG** | Z-Segment | Custom/local extension segments (Z = local use) |

---

## Message Types & Trigger Events

### ADT — Admission, Discharge, Transfer
| Event | Meaning |
|---|---|
| ADT^A01 | Admit patient |
| ADT^A02 | Transfer patient |
| ADT^A03 | Discharge patient |
| ADT^A04 | Register outpatient |
| ADT^A08 | Update patient info |
| ADT^A11 | Cancel admit |
| ADT^A13 | Cancel discharge |
| ADT^A28 | Add person info |
| ADT^A31 | Update person info |
| ADT^A40 | Merge patient |

### ORM/ORU — Orders and Results
| Event | Meaning |
|---|---|
| ORM^O01 | General order (lab, radiology) |
| ORU^R01 | Unsolicited observation result (lab result) |
| ORL^O22 | General lab order response |

### SIU — Scheduling
| Event | Meaning |
|---|---|
| SIU^S12 | Notification of new appointment |
| SIU^S13 | Notification of appointment rescheduling |
| SIU^S15 | Notification of appointment cancellation |
| SIU^S17 | Notification of appointment modification |

### MDM — Medical Document Management
| Event | Meaning |
|---|---|
| MDM^T02 | Original document notification |
| MDM^T04 | Document addendum notification |
| MDM^T10 | Document replacement |

---

## Transport: MLLP

HL7 v2 messages are typically transmitted over **MLLP (Minimal Lower Layer Protocol)** — a lightweight TCP wrapper.

```
MLLP Frame:
[0x0B] <HL7 message content> [0x1C][0x0D]
 │                                  │
 └─ Start Block (VT)     End Block (FS) + Carriage Return
```

| Port | Use |
|---|---|
| 2575 | Default MLLP (unencrypted) |
| 6661 | Common alternative |
| Custom | Site-specific |

**Security note:** MLLP itself has **no encryption or authentication**. In production, MLLP traffic must be tunneled over VPN, TLS wrapper (MLLP/S), or dedicated private network. Unprotected MLLP on port 2575 is a significant PHI exposure risk.

---

## Integration Engines

HL7 v2 messages are typically routed through an **Integration Engine** (middleware):

| Product | Vendor |
|---|---|
| Mirth Connect | NextGen (open source available) |
| Rhapsody | Rhapsody Health |
| Ensemble / HealthShare | InterSystems |
| Cloverleaf | Infor |
| Azure Health Data Services | Microsoft |

**Integration engine functions:**
- Message receipt and ACK generation
- Transformation (v2 ↔ FHIR, field mapping)
- Routing (which system receives which message)
- Filtering, deduplication
- Error handling and alerting

---

## ACK — Acknowledgement

Every HL7 v2 message expects an ACK response:

```
MSH|^~\&|ReceivingApp|ReceivingFacility|SendingApp|SendingFacility|20260327120001||ACK^A01|ACK00001|P|2.5.1
MSA|AA|MSG00001
```

| MSA-1 Code | Meaning |
|---|---|
| AA | Application Accept (success) |
| AE | Application Error (message received but failed processing) |
| AR | Application Reject (message rejected) |

---

## OBX Result Interpretation

```
OBX|1|NM|4548-4^HbA1c^LN||7.2|%|4.8-5.9|H|||F
           │                  │   │   │      │  └─ F = Final
           │                  │   │   └──────┤── H = High
           │                  │   └──────────── Reference range
           │                  └──────────────── Units
           └─────────────────────────────────── Value
```

| OBX-8 Code | Meaning |
|---|---|
| N | Normal |
| H | High |
| L | Low |
| HH | Critically high |
| LL | Critically low |
| A | Abnormal (non-numeric) |

---

## Common Issues & Troubleshooting

| Issue | Cause | Resolution |
|---|---|---|
| AE on ADT^A08 | PID field mismatch / MRN not found | Verify MRN format, check receiving system patient index |
| Duplicate messages | Integration engine resending on timeout | Check ACK receipt; deduplicate on MSH-10 control ID |
| Encoding errors | Non-ASCII characters in name/address fields | Enforce UTF-8 or Latin-1; escape special chars |
| Z-segment rejection | Receiving system doesn't parse Z-segments | Add Z-segment parser or strip in transform |
| Timezone issues | MSH-7 datetime without timezone | Standardize to UTC or local with offset (e.g., 20260327120000-0600) |

---

## HL7 v2 vs FHIR Quick Compare

| Feature | HL7 v2 | FHIR R4 |
|---|---|---|
| Age | 1989–present | 2012–present |
| Format | Pipe-delimited text | JSON / XML |
| Transport | MLLP (TCP) | REST HTTP |
| Human readable | No | Yes |
| Web-native | No | Yes |
| Best for | Legacy, high-volume, real-time | APIs, patient access, new integrations |

---

## Related Notes
- Healthcare-IT-Overview
- HL7-FHIR-Fundamentals
- Healthcare-IT-Integration-Patterns
- HIPAA-Technical-Safeguards
- Epic-Overview

---

## References
- HL7.org — v2.9 specification
- NIST HL7 v2 conformance tools
- Mirth Connect documentation — NextGen Health
- HL7 v2 to FHIR mapping project — hl7.org/fhir/uv/v2mappings

---
<- [[Healthcare-IT-MOC]]