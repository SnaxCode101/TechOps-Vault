---
title: "ServiceNow CIS-SP — Certified Implementation Specialist: Service Provider"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-SP, service-provider, managed-services, multi-tenancy, domain-separation, study-guide, job-aid, intermediate]
source: "ServiceNow official Service Provider documentation (docs.servicenow.com), Now Learning CIS-SP training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-SP — Certified Implementation Specialist: Service Provider

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Service Provider |
| Abbreviation | CIS-SP |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | Service Provider Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; managed service provider (MSP) operations; domain separation concepts |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official Domain Separation docs:** https://docs.servicenow.com/bundle/washingtondc-platform-administration/page/administer/domain-separation/concept/c_DomainSeparation.html

---

## What CIS-SP Validates

CIS-SP certifies you can **implement and configure ServiceNow for Managed Service Providers (MSPs)** — using domain separation to host multiple customer tenants on a single ServiceNow instance while keeping data isolated.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| Domain Separation Architecture | Domain hierarchy, domain path, domain visibility |
| Domain Administration | Creating domains, domain maps, domain switching |
| Data Segregation | Record-level isolation, table visibility per domain |
| User and Group Management | Domain-aware roles, cross-domain access |
| Process Domains | ITSM in multi-tenant context |
| Reports and Dashboards | Domain-aware reporting |
| Scripting in Domain Context | GlideRecord and domain-aware queries |

---

## Core Concepts — Study & Job Aid

### Domain Separation Architecture

Domain Separation allows one ServiceNow instance to serve multiple organisations (tenants) with isolated data.

```
Global Domain (top-level — MSP provider)
    ├── Customer A Domain
    │     ├── Customer A — East Region Domain
    │     └── Customer A — West Region Domain
    ├── Customer B Domain
    └── Customer C Domain
```

**Key principle:**
- Data in a domain is visible **up** the hierarchy (child → parent)
- Data is **not** visible across sibling domains (Customer A cannot see Customer B data)
- Global domain can see all data in all child domains

---

### Domain Path

Every record has a **domain** field (the `sys_domain` field) that determines its domain membership.

```
sys_domain = "Customer A"
```

**Domain path syntax:** `global/customer_a/customer_a_east`
- Used to check inheritance and visibility

---

### Creating and Managing Domains

```
Navigator: Domain Separation → Domains → New
Fields:
  Name:       Customer A
  Parent:     Global
  Domain path: auto-populated
```

**Domain map:**
```
Navigator: Domain Separation → Domain Maps
Visual representation of the domain hierarchy
```

**Domain switching (impersonation for admin):**
```
Navigator: System Settings → Domains → Switch to domain
Allows a Global admin to act within a specific customer domain context
```

---

### Data Segregation

**How records are isolated:**
- Every record has `sys_domain` field pointing to its domain
- ACL and domain filters prevent cross-domain reads
- When a user in Customer A queries incidents, the query is automatically scoped to Customer A domain

**Domain visibility on tables:**
```
Tables can be configured as:
- Global only: visible in all domains
- Domain separated: each domain sees only its own records
```

**Configuring table domain separation:**
```
Navigator: System Definition → Tables → [select table] → Domain Separation tab
Set to: Domain Separated = true
```

---

### User and Group Management in Domains

- Users are assigned to a domain — they can only see data in their domain (and parent domains if configured)
- Groups are domain-aware — a Customer A group is scoped to Customer A
- Roles can be granted within domain context

**Cross-domain access (controlled):**
- MSP technicians in Global domain can see all customer domains
- Customer users are restricted to their domain
- Special "cross-domain" role grants: `domain_admin` role in Global

---

### ITSM in Multi-Tenant Context

Each customer domain has isolated:
- Incidents, Problems, Changes (their own records)
- Assignment Groups (their IT teams)
- Configuration Items (their CMDB)
- Service Catalogue (their services)
- SLAs (their service levels)

**Shared vs dedicated:**
| Resource | Shared or Dedicated |
|---|---|
| ServiceNow instance | Shared (one instance) |
| Domain-separated tables | Dedicated per customer |
| Global tables (roles, groups) | Shared but domain-filtered |
| Workflows / Flows | Can be global (shared) or domain-specific |

---

### Reporting in Domain-Separated Instance

- Reports run in the user's domain context — they only see their domain's data
- Global admin reports span all domains
- Report sharing: reports can be shared within a domain

**Cross-domain reporting (MSP use case):**
```
Navigator (as Global admin): Reports → run cross-domain aggregated reports
Filter: add domain field to group/filter by customer
```

---

### Scripting and Domain Awareness

GlideRecord queries automatically apply domain filtering for domain-separated tables:
```javascript
// This query only returns records in the current user's domain:
var gr = new GlideRecord('incident');
gr.addEncodedQuery('active=true');
gr.query();
while (gr.next()) {
    gs.info(gr.number + ' - ' + gr.sys_domain);
}
```

To query across domains (Global admin only):
```javascript
// Force query to a specific domain:
gr.addDomainQuery('customer_a_domain_id');
```

---

### CIS-SP Roles

| Role | Access |
|---|---|
| `domain_admin` | Administer all domains (Global MSP admin) |
| `customer_admin` | Administer one customer domain |
| `customer_user` | Standard user in customer domain |
| `msp_technician` | MSP technician — cross-domain read access |

---

## Common Exam Scenarios

**Scenario 1:** A Global admin creates a new incident but it becomes visible to all customer tenants. What went wrong?
→ The incident was created in the **Global domain** — it should have been created in the specific Customer domain. Verify the admin's domain context was switched to the correct customer domain before creating the record.

**Scenario 2:** Customer A's service desk can see Customer B's incidents. What should you check?
→ The **`sys_domain` field** on the incidents — they may have been assigned the wrong domain. Also verify that the incident table has **domain separation enabled** and that ACLs are correctly scoped.

**Scenario 3:** An MSP wants a single dashboard showing incident volumes across all customer domains. How is this achieved?
→ A **Global admin** creates a cross-domain report with domain as a grouping dimension — Global admin visibility spans all child domains.

**Scenario 4:** A workflow runs correctly in Customer A's domain but not in Customer B's. The same workflow is used for both. What is likely happening?
→ The workflow may reference resources (groups, approval users, SLAs) that are domain-specific to Customer A. Create domain-specific overrides for Customer B's context or use domain-aware workflow variables.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — Service Provider path** | https://nowlearning.servicenow.com — Service Provider Fundamentals |
| **ServiceNow Docs — Domain Separation** | https://docs.servicenow.com — search "Domain Separation" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-ITSM — ITSM processes run within domain context for MSPs

---

## References

- ServiceNow Docs — Domain Separation: https://docs.servicenow.com/bundle/washingtondc-platform-administration/page/administer/domain-separation/concept/c_DomainSeparation.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]