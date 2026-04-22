---
title: "ServiceNow CIS-HR — Certified Implementation Specialist: HR Service Delivery"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-HR, HR-service-delivery, HRSD, employee-experience, study-guide, job-aid, intermediate]
source: "ServiceNow official HRSD documentation (docs.servicenow.com), Now Learning CIS-HR training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-HR — Certified Implementation Specialist: HR Service Delivery

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — HR Service Delivery |
| Abbreviation | CIS-HR |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | HRSD Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; HR service delivery or HR operations background helpful |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official HRSD docs:** https://docs.servicenow.com/bundle/washingtondc-hr-service-delivery/page/product/human-resources/concept/c_HRServiceManagement.html

---

## What CIS-HR Validates

CIS-HR certifies you can **implement and configure ServiceNow HR Service Delivery (HRSD)** — enabling HR departments to deliver employee services via case management, HR service catalogue, employee journeys (lifecycle events), and the Employee Service Center portal.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| HRSD Architecture | HR case types, HR profiles, COE model |
| HR Case Management | HR cases, tasks, agent desk |
| HR Service Catalogue | HR catalogue items, fulfilment, approvals |
| Lifecycle Events | Onboarding, offboarding, cross-boarding journeys |
| Employee Service Center | Portal configuration, knowledge, virtual agent |
| Confidentiality & Security | HR data security, profile visibility, role-based access |
| Integrations | Workday, Active Directory, payroll integrations |

---

## Core Concepts — Study & Job Aid

### HRSD Architecture — COE Model

HRSD is organised around **Centers of Excellence (COEs)**:
```
HR Administration
Benefits
Payroll
Recruiting
Workforce Management
```
Each COE has its own:
- HR Service Types (categories of HR cases)
- Assignment Groups
- HR Agents (fulfilment staff)
- Knowledge Base

**Navigator:** HR Administration → Configuration → HR Services

---

### HR Profiles

Every employee in ServiceNow HRSD has an **HR Profile** — separate from the User record.

```
Table: sn_hr_core_profile
```

**HR Profile fields:**
| Field | Purpose |
|---|---|
| User | Links to sys_user |
| Employment status | Active, Leave, Terminated |
| Date of hire | Start date |
| Position / Department | Job details |
| HR Business Partner | Assigned HR contact |

**Data confidentiality:**
- HR Profiles contain sensitive data — visible only to HR agents and the employee themselves
- Controlled via ACLs scoped to `sn_hr_core` application roles

---

### HR Case Management

**Table:** `sn_hr_core_case`

```
Navigator: HR Case Management → All HR Cases
```

**HR Case types (examples):**
| Case Type | Examples |
|---|---|
| HR General | General enquiry, policy question |
| Benefits | Enrol in benefits, change plan |
| Payroll | Pay discrepancy, tax form |
| Offboarding | Resignation processing, exit checklist |
| Onboarding | New hire setup, equipment request |

**HR Case lifecycle:**
```
New → Open → Work in Progress → Closed Complete / Closed Incomplete
```

**HR Tasks:**
- Child tasks assigned to HR agents or other departments (IT, Facilities)
- Navigator: HR Case → HR Tasks tab

---

### HR Service Catalogue

HRSD has its own HR-specific Service Catalogue, separate from the ITSM catalogue.

```
Navigator: HR Administration → HR Service Catalogue
```

**HR Catalogue Item examples:**
- Request parental leave
- Update personal information
- Request employment verification letter
- Benefits enrolment form

**Configuration:**
```
HR Service Catalogue Item → Variables (form fields) → Fulfilment Flow → COE assignment
```

Employees access the HR catalogue via the **Employee Service Center** portal.

---

### Lifecycle Events (Journeys)

Lifecycle Events automate multi-step HR journeys across departments.

**Common lifecycle events:**
| Event | Description |
|---|---|
| Onboarding | New hire preparation — IT setup, badge, payroll, orientation |
| Offboarding | Departing employee — access revocation, equipment return, final pay |
| Cross-boarding | Internal transfer — new role setup, access changes |
| Parental Leave | Leave approval, payroll, return-to-work checklist |
| Promotion | Role change, pay adjustment, system access update |

**Lifecycle Event architecture:**
```
Lifecycle Event (trigger)
    └── Activity Sets (phases: Pre-Day-1, Day-1, Week-1)
          └── Activities (tasks for IT, Facilities, HR, Payroll)
                └── Assigned to agents / automated flows
```

```
Navigator: HR Administration → Lifecycle Events → Lifecycle Event Rules → New
Define: trigger condition (e.g. new hire date approaching)
Add Activity Sets + Activities
Configure: which COE handles each activity
```

---

### Employee Service Center (ESC)

The **Employee Service Center** is the self-service portal for employees.

**Features:**
- Submit HR requests (catalogue items)
- Track case status
- Search HR knowledge base
- Chat with HR Virtual Agent
- View personalised announcements and tasks

```
Navigator: Employee Service Center (via Service Portal)
URL: /esc
```

**Configuring ESC:**
```
Navigator: HR Administration → Employee Service Center → Configuration
Manage: featured categories, promoted articles, announcements, branding
```

---

### HR Data Security and Confidentiality

HR data is sensitive — HRSD has specific security controls:

**HR profile visibility:**
- Employees can only see their own HR profile
- HR agents can see profiles within their COE
- HR managers can see profiles in their reporting chain

**ACL model:**
- All HR data protected by scoped application ACLs
- `sn_hr_core.basic` role: employee self-service access
- `sn_hr_core.case_writer` role: HR agents
- `sn_hr_core.admin` role: HR administrators

**Secure notes:**
- HR cases can contain secure notes visible only to HR agents — not to the employee
- Used for sensitive case documentation

---

### HRSD Roles

| Role | Access |
|---|---|
| `sn_hr_core.basic` | Employee — self-service portal, own cases only |
| `sn_hr_core.case_writer` | HR agent — manage HR cases |
| `sn_hr_core.manager` | HR manager — manage agents and cases |
| `sn_hr_core.admin` | Full HRSD configuration and administration |

---

## Common Exam Scenarios

**Scenario 1:** A new hire is joining in 2 weeks. IT needs to set up a laptop, Facilities needs to prepare a desk, and HR needs to send onboarding documents. How does HRSD automate this?
→ A **Lifecycle Event** for Onboarding — configured with Activity Sets for Day -14, Day 0, Day 1 — Activities assigned to IT (laptop), Facilities (desk), HR (documents). Triggers automatically on the hire date.

**Scenario 2:** An HR agent can see cases from all COEs but should only see Benefits cases. What should you check?
→ The agent's **HR service type assignments** — verify the agent is only assigned to the Benefits COE. COE assignment controls which case types the agent can view.

**Scenario 3:** Employees are calling HR directly instead of using the portal. The HR team wants to reduce this. What should you configure?
→ Enhance the **Employee Service Center** — add prominent catalogue items, featured knowledge articles, and configure the HR **Virtual Agent** to handle common enquiries.

**Scenario 4:** A terminated employee's HR Profile must not be accessible to other employees or line managers after departure. What controls this?
→ The HR Profile **Employment Status** field updated to "Terminated" + ACL rules that restrict profile visibility to HR agents only for non-active employees.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — HRSD path** | https://nowlearning.servicenow.com — HRSD Fundamentals |
| **ServiceNow Docs — HRSD** | https://docs.servicenow.com — search "HR Service Delivery" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-ITSM — Similar case management patterns apply

---

## References

- ServiceNow Docs — HRSD: https://docs.servicenow.com/bundle/washingtondc-hr-service-delivery/page/product/human-resources/concept/c_HRServiceManagement.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]