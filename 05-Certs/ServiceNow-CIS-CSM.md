---
title: "ServiceNow CIS-CSM — Certified Implementation Specialist: Customer Service Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-CSM, customer-service, CSM, cases, accounts, study-guide, job-aid, intermediate]
source: "ServiceNow official CSM documentation (docs.servicenow.com), Now Learning CIS-CSM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-CSM — Certified Implementation Specialist: Customer Service Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Customer Service Management |
| Abbreviation | CIS-CSM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | CSM Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; customer service or CRM background helpful |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official CSM docs:** https://docs.servicenow.com/bundle/washingtondc-customer-service-management/page/product/customer-service-management/concept/c_CustomerServiceMgmt.html

---

## What CIS-CSM Validates

CIS-CSM certifies you can **implement and configure ServiceNow Customer Service Management** — enabling external-facing customer service operations with case management, account/contact management, customer portal, and integration with ITSM and FSM.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| CSM Architecture | Accounts, contacts, cases, products, entitlements |
| Case Management | Case creation, routing, SLA, escalation |
| Customer Portal (CSP) | Self-service portal, case submission, knowledge |
| Account & Contact Management | Account hierarchy, contacts, B2B vs B2C |
| Entitlements & SLAs | Service entitlements, SLA per account/product |
| Omni-Channel | Email, chat, telephony integration |
| Integration | ITSM linking, FSM, proactive customer service |

---

## Core Concepts — Study & Job Aid

### CSM vs ITSM — Key Difference

| Aspect | ITSM | CSM |
|---|---|---|
| Audience | Internal employees | External customers |
| Record type | Incident, Problem, Change | Case |
| Account model | Employee / department | Customer Account / Contact |
| Portal | Service Portal / ESC | Customer Service Portal (CSP) |
| SLAs | Based on priority/impact | Based on entitlements/contracts |

---

### CSM Data Model

```
Account (customer company)
    └── Contact (individual at the account)
    └── Product (what the customer has purchased)
    └── Service Entitlement (support contract / SLA tier)
          └── Case (customer support request)
                └── Case Tasks (work items)
                └── Agent interactions (email, chat, phone log)
```

**Key tables:**
| Table | Purpose |
|---|---|
| `customer_account` | Account records |
| `customer_contact` | Contact records |
| `sn_customerservice_case` | CSM Case records |
| `service_entitlement` | Entitlement/contract records |
| `sn_customerservice_product` | Product records |

---

### Case Management

**Table:** `sn_customerservice_case`

```
Navigator: Customer Service → Cases → All Cases
```

**Case lifecycle:**
```
New → Open → Awaiting Info → Resolved → Closed
```

**Case routing:**
- Cases assigned to **Agent Groups** (specialised by product, region, tier)
- Routing rules: Navigator → Customer Service → Administration → Assignment Rules
- Skills-based routing: match case category to agent skills

**Case escalation:**
```
SLA breach → escalation rule fires → case assigned to supervisor
OR: Customer requests escalation → case flagged for manager review
```

---

### Accounts and Contacts

**Account types:**
| Type | Description |
|---|---|
| Business Account | A company (B2B) |
| Consumer | An individual customer (B2C) |

**Account hierarchy:**
```
Parent Account (e.g. Acme Corp Global)
    └── Child Account (e.g. Acme Corp UK)
          └── Child Account (e.g. Acme Corp London Office)
```

**Contact:**
- An individual associated with an Account
- Has portal login credentials
- Contacts raise cases on behalf of their account

---

### Service Entitlements

Entitlements define what level of support a customer is entitled to based on their contract/purchase.

```
Navigator: Customer Service → Entitlements → New
Fields:
  Account:        Acme Corp
  Product:        Enterprise Support Plan
  SLA:            4-hour response, 24/7
  Start / End:    contract term
  Number of cases: unlimited or capped
```

**Entitlement verification:**
- When a case is created, ServiceNow checks the account's active entitlement
- Determines applicable SLA
- If no entitlement → case may be flagged or routed to a paid support queue

---

### Customer Service Portal (CSP)

The CSP is the self-service portal for external customers.

**Features:**
- Submit and track cases
- Search knowledge base
- View account information
- Live chat with agents
- Community forums (optional)

```
Navigator: Customer Service → Service Portal
URL: /csm
```

**CSP configuration:**
```
Navigator: Customer Service → Administration → Customer Portal
Manage: branding, featured articles, catalogue items, announcements
```

---

### Omni-Channel

CSM supports multiple communication channels, unified in the **Agent Workspace**:

| Channel | Integration |
|---|---|
| Email | Inbound email → create/update case |
| Live Chat | ServiceNow embedded chat |
| Telephony | CTI (Computer Telephony Integration) via connector |
| Social Media | Twitter/Facebook DM → create case |
| Web form | CSP case submission |

**Agent Workspace:**
```
Navigator: Agent Workspace → Home
Unified view: cases, queue, live chat, customer info
Real-time: incoming cases and chats route to agent
```

---

### Proactive Customer Service Operations

Connects ITSM (operations) with CSM (customers) proactively:
```
ITSM Incident detected (e.g. outage affecting customer accounts)
    ↓
Proactive Case created for affected customers automatically
    ↓
Customers notified before they call in
    ↓
Case linked to underlying Incident for tracking
```

Navigator: Customer Service → Proactive Customer Service Operations

---

### CSM Roles

| Role | Access |
|---|---|
| `csm_agent` | Handle customer cases |
| `csm_manager` | Manage agents, case queues, escalations |
| `sn_customerservice.customer` | External customer — portal access only |
| `csm_admin` | Full CSM configuration and administration |

---

## Common Exam Scenarios

**Scenario 1:** A customer creates a case but the system cannot determine their SLA. What should you check?
→ The customer's **Service Entitlement** — verify an active entitlement exists for their account and the correct product is linked.

**Scenario 2:** The support team handles different product lines and cases are being routed to the wrong agents. How do you fix routing?
→ Configure **Assignment Rules** — match case product/category to the correct Agent Group. Consider skills-based routing for complex routing requirements.

**Scenario 3:** A major outage is affecting 500 customers. The team wants to notify all affected accounts before they call in. What feature do you use?
→ **Proactive Customer Service Operations** — create a proactive case linked to the ITSM incident and bulk-notify all affected account contacts.

**Scenario 4:** An enterprise customer complains they can only submit cases via the portal but need phone support per their contract. What should you check?
→ The **Service Entitlement** — verify the entitlement includes phone/telephony support. If not, update the entitlement to reflect the contracted service level.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — CSM path** | https://nowlearning.servicenow.com — CSM Fundamentals |
| **ServiceNow Docs — CSM** | https://docs.servicenow.com — search "Customer Service Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-ITSM — ITSM underpins CSM for internal operations
- ServiceNow-CIS-FSM — Field Service Management integrates with CSM for on-site service

---

## References

- ServiceNow Docs — CSM: https://docs.servicenow.com/bundle/washingtondc-customer-service-management/page/product/customer-service-management/concept/c_CustomerServiceMgmt.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]