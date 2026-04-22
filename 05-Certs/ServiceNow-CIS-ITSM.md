---
title: "ServiceNow CIS-ITSM — Certified Implementation Specialist: IT Service Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-ITSM, ITSM, incident, problem, change, study-guide, job-aid, intermediate]
source: "ServiceNow official ITSM documentation (docs.servicenow.com), Now Learning CIS-ITSM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-ITSM — Certified Implementation Specialist: IT Service Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — IT Service Management |
| Abbreviation | CIS-ITSM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | ITSM Fundamentals + ITSM Implementation (Now Learning) |
| Recommended experience | ServiceNow administration experience; ITIL knowledge helpful |

**Official ITSM training path:** https://nowlearning.servicenow.com
**Official ITSM docs:** https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/incident-management/concept/c_IncidentManagement.html

---

## What CIS-ITSM Validates

CIS-ITSM certifies that you can **implement and configure ServiceNow ITSM** — covering Incident, Problem, Change, Request, and Knowledge Management along with SLAs, approvals, and CMDB integration.

> Source: [ServiceNow Learning Paths PDF 2025](https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf)

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| Incident Management | States, assignment, escalation, SLA, Major Incident |
| Problem Management | Problem investigations, Known Errors, root cause, linking to Incidents |
| Change Management | Change types, approval, CAB, implementation and review |
| Request Management | Service Catalog, order guides, approvals, fulfilment |
| Knowledge Management | Knowledge bases, articles, workflows, feedback |
| SLAs & Entitlements | SLA definitions, workflows, breach actions |
| Notifications | Email notifications, events, templates |
| Reporting & Dashboards | ITSM reports, PA indicators, dashboards |
| CMDB Integration | CI relationships in ITSM context, impact analysis |

---

## Core Concepts — Study & Job Aid

### Incident Management

**Table:** `incident` (extends `task`)

**States (default values):**
```
1 = New
2 = In Progress
3 = On Hold
6 = Resolved
7 = Closed
```

**Key fields:**

| Field | Purpose |
|---|---|
| Category / Subcategory | Classification of the incident type |
| Impact | Business impact (1=High, 2=Medium, 3=Low) |
| Urgency | How quickly resolution is needed |
| Priority | Auto-calculated from Impact × Urgency |
| Assignment Group | Team responsible |
| Assigned To | Individual analyst |
| SLA due | When the SLA breach will occur |
| Caller | Person who reported the incident |
| CI | Affected Configuration Item from CMDB |

**Priority matrix (default):**
```
Impact\Urgency  1-High  2-Medium  3-Low
1-High            1       2         3
2-Medium          2       3         4
3-Low             3       4         5
```

**Major Incident Management:**
```
Navigator: Incident → All → filter Priority = 1 (Critical)
Major Incident flag: tick "Major Incident" checkbox on incident form
Activates: Major Incident Communications, war room, stakeholder updates
Navigator: Major Incident Management → Active Major Incidents
```

> Source: [ServiceNow Docs — Incident Management](https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/incident-management/concept/c_IncidentManagement.html)

---

### Problem Management

**Table:** `problem`

**Purpose:** Identify and eliminate the root cause of recurring incidents. Problem Management is reactive (triggered by incidents) and proactive (identifies risks before incidents occur).

**Key states:**
```
Draft → Assess → Root Cause Analysis → Fix in Progress → Resolved → Closed
```

**Known Error Database (KEDB):**
```
When root cause is identified but fix not yet deployed:
→ Create a Known Error record from the Problem
→ Known Error visible to service desk for workaround info
→ Links back to associated Incidents
```

**Linking Incidents to Problems:**
```
On a Problem record → Affected CIs and Services tab → add related Incidents
OR: On an Incident form → Problem field → link to existing Problem
```

> Source: [ServiceNow Docs — Problem Management](https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/problem-management/concept/c_ProblemManagement.html)

---

### Change Management

**Table:** `change_request`

**Change Types:**

| Type | Description | Approval Required |
|---|---|---|
| Standard | Pre-approved, low-risk, frequently performed, documented in a template | Pre-approved (no CAB) |
| Normal | Requires assessment, scheduling, and CAB approval | Yes — CAB |
| Emergency | Urgent, expedited review — uses Emergency CAB (ECAB) | ECAB approval |

**Normal Change lifecycle:**
```
Draft → Assess → Authorize → Scheduled → Implement → Review → Closed
```

**CAB (Change Advisory Board):**
```
Navigator: Change → Change Advisory Board → CAB Meetings
Create a CAB meeting → add Change Requests for review
CAB members review and approve/reject
```

**Change Models (Standard Change templates):**
```
Navigator: Change → Change Models
Pre-approved templates for recurring standard changes
e.g. "Restart application server", "Apply monthly patch"
Creating from a model: Change → New → select Change Model
```

**Conflict Detection:**
```
ServiceNow checks for conflicting changes on the same CI
during the same maintenance window
Navigator: Change → Conflicts
```

> Source: [ServiceNow Docs — Change Management](https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/change-management/concept/c_ChangeManagement.html)

---

### Request Management and Service Catalog

**Tables:**
- `sc_request` — the top-level Request
- `sc_req_item` — individual Requested Items within a request
- `sc_task` — fulfilment tasks generated from catalog items

**Service Catalog components:**

| Component | Purpose |
|---|---|
| Catalog Item | A single orderable item (e.g. "Request a Laptop") |
| Order Guide | Groups multiple catalog items for one order |
| Record Producer | Creates records in any table via the catalog |
| Variable | A field/question on a catalog item form |
| Variable Set | Reusable group of variables shared across items |
| Workflow / Flow | Automates fulfilment after submission |

**Create a Catalog Item:**
```
Navigator: Service Catalog → Catalog Definitions → Maintain Items → New
Fields: Name, Category, Short Description, Icon, Fulfillment team, Price
Variables tab: add questions/fields
Process Engine tab: attach Flow Designer flow or Workflow
```

**Approval:**
```
Most catalog items require approval before fulfilment begins
Configure via: Catalog Item → Process Engine → attach approval flow
OR: Flow Designer → add Approval action step
```

> Source: [ServiceNow Docs — Service Catalog](https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/service-catalog-management/concept/c_ServiceCatalogManagement.html)

---

### SLA (Service Level Agreement) Configuration

SLAs define the response and resolution time targets for ITSM records.

```
Navigator: Service Level Management → SLA Definitions → New
```

**Key SLA fields:**

| Field | Purpose |
|---|---|
| Name | Descriptive SLA name |
| Table | Which table this SLA applies to (e.g. `incident`) |
| Target | Response or Resolution |
| Duration | Time allowed (e.g. 4 hours for P1 resolution) |
| Schedule | Business hours schedule or 24×7 |
| Start condition | When the SLA timer starts |
| Pause condition | When the timer pauses (e.g. On Hold) |
| Stop condition | When the SLA completes |
| Breach actions | What happens when SLA breaches |

**SLA workflow (breach actions):**
```
At 50% elapsed: notification to assignee
At 75% elapsed: notification to assignment group manager
At 100% (breach): notification + escalation rule fires
```

**SLA visible on incident:** Incident form → SLA tab → shows active SLAs, time remaining, breach status

> Source: [ServiceNow Docs — SLA](https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/service-level-management/concept/c_ServiceLevelManagement.html)

---

### Knowledge Management

```
Navigator: Knowledge → Home
OR: Knowledge → Knowledge Bases
```

**Knowledge Base → Category → Article hierarchy:**
```
Knowledge Base (e.g. "IT Support KB")
└── Category (e.g. "Network Issues")
    └── Article (individual knowledge article)
```

**Article workflow states:**
```
Draft → Review → Published → Retired
```

**Configuring a Knowledge Base:**
```
Navigator: Knowledge → Administration → Knowledge Bases → New
Fields: Title, Description, Owner group, Article versioning, Workflow
```

**Attaching articles to Incidents:**
- Analysts can search and attach KB articles from the Incident form
- Solution field on Incident can reference a KB article
- Deflection: Service Portal shows relevant articles when users submit incidents

---

### Notifications Configuration

```
Navigator: System Notification → Email → Notifications → New
```

**Notification components:**

| Component | Purpose |
|---|---|
| Event | Trigger — what fires the notification (e.g. `incident.created`) |
| Notification | The email definition — who gets it, what template |
| Email Template | The content of the email |
| Recipient | User, group, or email field on the record |

**Common ITSM notification events:**
```
incident.created         → notify assignment group
incident.assigned        → notify assignee
incident.resolved        → notify caller
incident.sla.breach      → notify manager
change_request.approved  → notify requestor
```

---

### ITSM Roles

| Role | Access |
|---|---|
| `itil` | Standard ITSM user — create/update Incidents, Problems, Changes |
| `itil_admin` | Manage ITSM configuration |
| `change_manager` | Approve and manage Changes |
| `problem_admin` | Manage Problem records |
| `catalog_admin` | Manage Service Catalog |
| `knowledge_admin` | Manage Knowledge Bases |

---

### CMDB in ITSM Context

- Every ITSM record can reference a **CI** (Configuration Item) from the CMDB
- CI on an Incident drives **impact analysis** — which services are affected
- Linking incidents to CIs enables: service degradation detection, trend analysis, auto-routing by CI owner
- **Service mapping:** connects CIs into business services — a P1 incident on a CI automatically impacts associated services

---

## Common Exam Scenarios

**Scenario 1:** An Incident SLA is not pausing when the Incident goes to On Hold. What should you check?
→ The SLA Definition's **Pause Condition** — verify it includes `state = 3 (On Hold)`.

**Scenario 2:** A change team wants a pre-approved template for their weekly server restarts. What should you create?
→ A **Change Model** (Standard Change template) — allows the team to create changes from the pre-approved template without CAB review.

**Scenario 3:** Users submitting Service Catalog requests are bypassing the approval step. What is the most likely cause?
→ The Catalog Item's Process Engine (Flow) does not include an Approval action, or the approval condition is not met by those users.

**Scenario 4:** The service desk keeps creating duplicate Incidents for the same recurring outage. What Problem Management record should be created?
→ A **Problem** record to investigate root cause, and a **Known Error** once the root cause is identified — allowing the service desk to apply the documented workaround rather than creating new incidents.

**Scenario 5:** A P1 Incident breach notification is going to the wrong group. Where do you fix this?
→ The **Notification** record for `incident.sla.breach` — update the recipient to the correct group.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — ITSM path** | https://nowlearning.servicenow.com — ITSM Fundamentals + ITSM Implementation (required) |
| **ServiceNow Docs — ITSM** | https://docs.servicenow.com — search "Incident Management", "Change Management" etc. |
| **ITIL 4 Foundation** | General ITIL knowledge complements CIS-ITSM exam content |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-DF — CMDB (used throughout ITSM)
- ServiceNow-Flow-Designer — Flow Designer used for ITSM automation
- Incident-Response — General IR methodology

---

## References

- ServiceNow Docs — Incident Management: https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/incident-management/concept/c_IncidentManagement.html
- ServiceNow Docs — Problem Management: https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/problem-management/concept/c_ProblemManagement.html
- ServiceNow Docs — Change Management: https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/change-management/concept/c_ChangeManagement.html
- ServiceNow Docs — Service Catalog: https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/service-catalog-management/concept/c_ServiceCatalogManagement.html
- ServiceNow Docs — SLA: https://docs.servicenow.com/bundle/washingtondc-it-service-management/page/product/service-level-management/concept/c_ServiceLevelManagement.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]