---
title: ServiceNow + Epic Integration
type: reference
tags:
  - servicenow
  - epic
  - integration
  - itsm
  - healthcare-it
  - ehr
  - intermediate
section: 10-Healthcare-IT
created: 2026-03-27
difficulty: intermediate
---

# 🔗 ServiceNow + Epic Integration

> ServiceNow is the dominant ITSM platform in healthcare. Epic is the dominant EHR. Understanding how they connect is essential for Healthcare IT professionals managing both platforms.

---

## Why These Two Systems Integrate

| Healthcare IT Need | Platform |
|---|---|
| Clinical system availability & incidents | Epic (the affected system) + ServiceNow (ticket) |
| Change management for EHR upgrades | ServiceNow CAB + Epic upgrade schedule |
| Access provisioning for EHR accounts | ServiceNow catalog request → Epic Security |
| CMDB accuracy for Epic infrastructure | ServiceNow CMDB ← Epic server/interface inventory |
| Epic downtime communication | ServiceNow major incident → notification workflows |

---

## Integration Use Cases

### 1. Epic Downtime → ServiceNow Incident (Auto-Create)

**Pattern:** Epic infrastructure monitoring (Datadog, Nagios, Epic monitoring tools) detects an issue → webhook or API call → ServiceNow incident auto-created.

```
Epic Monitoring Alert
        │
        ▼
  REST API POST → ServiceNow /api/now/table/incident
        │
        ▼
  Incident created:
  - Category: Application
  - Subcategory: EHR / Epic
  - Priority: 1 - Critical (for downtime)
  - Assignment Group: Epic Application Team
  - CI: Epic Production (from CMDB)
```

**ServiceNow Scripted REST or MID Server** can be used depending on network access.

---

### 2. Change Management — Epic Upgrades

Epic releases major upgrades twice yearly (typically May and November). These must go through Change Management:

| Step | ServiceNow Object |
|---|---|
| Upgrade planning | Change Request (type: Standard or Normal) |
| CAB review | CAB Workbench approval |
| Implementation tasks | Change Tasks (pre-work, downtime window, post-validation) |
| Rollback plan | Change Request field (rollback steps) |
| Post-implementation review | PIR task on Change Record |

**Epic-specific change types:**
- `Epic Upgrade` — biannual major release
- `Weekly Build` — minor updates pushed weekly
- `Epic Patch` — urgent hotfix
- `Interface Change` — HL7/FHIR interface modification
- `Template Build` — scheduling or clinical content update

---

### 3. Epic Access Request → ServiceNow Catalog

**Pattern:** New user needs Epic access → submits ServiceNow catalog item → approval workflow → Epic Security team provisions.

```
ServiceNow Service Catalog
  "Epic Access Request" Catalog Item
        │
        ├── Variables: User, Department, Role, Justification, Supervisor
        ├── Approval: Manager + Epic Security Manager
        └── Fulfillment Task → Epic Security team
              │
              └── Epic Security provisions:
                    - User record in Epic
                    - Security class assignment
                    - Provider/staff template assignment
                    - Access report verification
```

**RBAC in Epic:** Epic security uses Security Classes (analogous to roles) + User Records. Not integrated to AD groups by default — some sites implement AD → Epic sync but many still provision manually.

---

### 4. CMDB — Epic Infrastructure Records

ServiceNow CMDB should contain Configuration Items (CIs) for:

| CI Type | Examples |
|---|---|
| Application | Epic Production, Epic UAT, Epic Training |
| Server | Epic application servers (Caché/InterSystems), print servers, web servers |
| Database | Chronicles (Caché) instances |
| Interface | Each HL7 interface (ADT feed, lab interface, etc.) |
| Network | VLANs dedicated to Epic, firewall rules |

**Epic ↔ CMDB Mapping:**
```
Epic Environment
  ├── Epic Production App Server  →  CMDB: Server CI
  ├── Chronicles DB               →  CMDB: Database CI
  ├── Interconnect (FHIR/web)     →  CMDB: Application Service CI
  ├── Bridges interfaces (×N)     →  CMDB: Business Application CI (per interface)
  └── Hyperspace client           →  CMDB: Software CI (installed on endpoints)
```

---

### 5. Major Incident — Epic Downtime Communication

When Epic goes down (unplanned or planned), ServiceNow drives communication:

```
Major Incident Declared
  ├── P1 incident created, CMDB CI = Epic Production
  ├── Major Incident Workbench activated
  ├── Communication Plan:
  │     ├── Email/SMS to clinical staff (Notify module or integration)
  │     ├── Status Page update (if configured)
  │     └── Exec bridge established
  ├── Work Notes log every 15 minutes (status updates)
  └── Resolution + PIR upon restore
```

**Epic Downtime Procedures:** Epic has built-in downtime workflows (paper-based downtime forms, Downtime Viewer). ServiceNow tracks the IT side; Epic tracks the clinical side.

---

## Technical Integration Methods

### REST API (ServiceNow ↔ Epic)

**ServiceNow → Epic Interconnect:**
```javascript
// ServiceNow Scripted REST Action calling Epic FHIR
var request = new sn_ws.RESTMessageV2('Epic FHIR', 'get_patient');
request.setStringParameterNoEscape('patient_id', '12345');
var response = request.execute();
var body = response.getBody();
```

**Epic Interconnect → ServiceNow:**
```
Epic Interconnect calls ServiceNow Table API:
POST https://instance.service-now.com/api/now/table/incident
Authorization: Basic (service account)
Content-Type: application/json
{
  "short_description": "Epic interface failure: ADT feed to downstream system",
  "category": "application",
  "urgency": "1",
  "assignment_group": "Epic Integration Team"
}
```

### ServiceNow MID Server (for on-premise Epic)

Most Epic environments are **on-premise** (hospital data center) or **hosted by Epic** — not in a public cloud. ServiceNow's cloud instance needs a **MID Server** to communicate with on-premise systems:

```
ServiceNow Cloud ──► MID Server (on-premise) ──► Epic Interconnect (internal)
                    (runs inside hospital network)
```

---

## Governance & Access Considerations

| Topic | Detail |
|---|---|
| Shared service accounts | Epic and ServiceNow integration accounts must be dedicated, not shared |
| Least privilege | ServiceNow API user in Epic: read-only unless write operations are required |
| Audit trail | All automated actions (incident creation, CI updates) logged in both systems |
| PHI in ServiceNow | Avoid storing PHI in ServiceNow tickets — use patient ID + MRN only, not full record data |
| Epic UserWeb API keys | Track expiration; rotate regularly; store in secrets manager |

> ⚠️ **PHI risk:** ServiceNow is an ITSM tool — it is typically not a HIPAA-covered system by default. Only store the minimum identifiers needed in tickets. Full clinical data must stay in Epic.

---

## Quick Reference: ServiceNow Objects for Epic

| Scenario | ServiceNow Object |
|---|---|
| Epic is down | Incident (P1), CI = Epic Production |
| Epic upgrade | Change Request, type = Normal or Standard |
| New Epic user | Service Catalog Item → Task → Epic Security |
| Interface broken | Incident, CI = specific interface CI |
| Epic server patched | Change Request, CI = server CI |
| Epic enhancement request | Enhancement / Idea (if PPM/ITBM) or Change |
| Epic training issue | Knowledge Article + optional Incident |

---

## Related Notes
- ServiceNow-CSA
- ServiceNow-Flow-Designer
- Healthcare-IT-Integration-Patterns
- Epic-Overview
- HIPAA-Technical-Safeguards
- Healthcare-IT-Overview

---

## References
- ServiceNow Docs — REST API, Table API, CMDB
- Epic UserWeb — Interconnect REST documentation [VERIFY: Epic UserWeb access]
- ITIL 4 — Change enablement, Incident Management
- ServiceNow Store — Epic-specific integration apps (if available)

---
<- [[Healthcare-IT-MOC]]