---
title: "ServiceNow CTA — Certified Technical Architect"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CTA, technical-architect, architecture, platform, enterprise, study-guide, job-aid, intermediate]
source: "ServiceNow official CTA programme documentation, Now Learning CTA path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CTA — Certified Technical Architect

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Technical Architect |
| Abbreviation | CTA |
| Issued by | ServiceNow |
| Exam / Assessment | Written exam + Architecture Design Submission (ADS) |
| Duration | Written exam: 110 minutes |
| Questions | Written: 75 (multiple choice and multiple select) |
| Passing score | Written: 70% |
| Prerequisites | Multiple CIS certifications + significant ServiceNow implementation experience |
| Audience | Experienced technical architects designing ServiceNow platforms |
| Level | Senior/expert — above CIS, below CMA |

**Official CTA programme:** https://www.servicenow.com/services/training-and-certification/certified-technical-architect.html
**Official Now Learning path:** https://nowlearning.servicenow.com

> ⚑ The CTA has two components: a written exam AND an Architecture Design Submission (ADS) — a practical architecture exercise reviewed by ServiceNow. Both must be passed. Verify current requirements at nowlearning.servicenow.com.

---

## What CTA Validates

The CTA certifies that a candidate can:

- Design **scalable, secure, and maintainable** ServiceNow platform architectures
- Make informed **technology decisions** with appropriate trade-offs
- Architect solutions across **multiple product areas** of the ServiceNow platform
- Apply **platform best practices** — instance strategy, upgrade management, integration patterns
- Lead **technical governance** of a ServiceNow implementation

> The CTA bridges deep product implementation knowledge (CIS level) with enterprise architecture thinking — it is the entry point to the architect track, with CMA as the next level.

---

## Prerequisites

Before sitting the CTA:

1. **Minimum 2–3 CIS certifications** — demonstrating breadth across product areas
2. **CSA (Certified System Administrator)** — typically expected as a baseline
3. **3–5+ years hands-on ServiceNow experience** at implementation/architect level
4. **CTA candidacy application** — reviewed by ServiceNow via Now Learning

---

## Exam Components

### Component 1 — Written Exam (75 questions, 110 minutes)

**Domain areas:**

| Domain | Key Topics |
|---|---|
| Platform Architecture | Instance strategy, upgrade management, performance, scalability |
| Integration Architecture | Integration Hub, REST/SOAP, MID Server, event-driven patterns |
| Data Architecture | CMDB, IRE, data model design, retention policies |
| Security Architecture | ACL design, role strategy, encryption, compliance |
| Application Architecture | Scoped vs global apps, update sets, plugin management |
| ServiceNow Product Suite | Cross-product integration patterns, ITSM/ITOM/SecOps/HRSD/CSM |
| Governance | Development standards, instance hygiene, technical debt |

---

### Component 2 — Architecture Design Submission (ADS)

The ADS is a **practical architecture exercise**:

```
Stage 1: Scenario provided (complex enterprise architecture problem)
Stage 2: Candidate prepares architecture design documentation
  → Diagrams, decision rationale, trade-off analysis
  → Covers: integration, data model, security, scalability, governance
Stage 3: Submission reviewed by ServiceNow CTA review panel
Stage 4: Outcome: Pass / Fail with feedback
```

**ADS evaluation criteria:**
- Completeness of the architecture
- Quality of decision rationale and trade-off analysis
- Adherence to ServiceNow best practices
- Scalability and maintainability of the proposed solution
- Security and compliance considerations

---

## Core Concepts — Study & Job Aid

### Instance Strategy

**Single instance vs multi-instance:**

| Scenario | Recommendation |
|---|---|
| One organisation, multiple departments | Single instance + domain separation if needed |
| Separate legal entities / strict data isolation | Multiple instances |
| Subsidiary acquisition | Federated approach: integrate separate instances via IntegrationHub |
| Managed service provider | Single instance + domain separation (CIS-SP) |

**Sub-production strategy:**
```
Production instance
    ↓ clone
Pre-Production / UAT
    ↓ clone
Test / QA
    ↓ refresh
Development
```

**Instance refresh cadence:**
- Dev: refresh from prod every 2–4 weeks
- Test/QA: refresh before major release cycles
- Pre-Prod: refresh before each prod deployment

---

### Upgrade Management

**ServiceNow release cadence:**
- Two major releases per year (Washington DC, Xanadu, etc.)
- Patch releases between major versions

**Upgrade best practices:**
```
1. Review upgrade guide for breaking changes
2. Upgrade Dev first → run ATF (Automated Test Framework) tests
3. Upgrade Test/QA → regression testing
4. Upgrade Pre-Prod → UAT sign-off
5. Schedule Production upgrade (maintenance window)
6. Post-upgrade: monitor logs, validate integrations
```

**ATF (Automated Test Framework):**
- Navigator: Automated Test Framework → Tests
- Critical for: validating business-critical workflows survive upgrades
- Recommend: build ATF tests for all customisations before upgrade

---

### Integration Architecture

**Integration patterns:**

| Pattern | Use case | ServiceNow tool |
|---|---|---|
| REST inbound | External system calls ServiceNow API | Scripted REST API |
| REST outbound | ServiceNow calls external API | REST Message + HTTP Method |
| SOAP | Legacy system integration | SOAP Message |
| Event-driven | Trigger actions on events | IntegrationHub + Event |
| File-based (SFTP) | Batch data exchange | Import Sets |
| MID Server | On-premise system integration | MID Server + JDBC/WMI/SSH |
| iPaaS | Complex orchestration | IntegrationHub spokes |

**MID Server topology:**
```
One MID Server per network segment (DMZ, internal, cloud)
MID Server clusters for HA (High Availability)
MID Server selection: use affinity rules (ECC Queue routing)
```

**IntegrationHub spokes:**
- Pre-built connectors: Jira, Slack, Azure, AWS, ServiceNow-to-ServiceNow
- Navigator: IntegrationHub → Spokes

---

### Data Architecture — CMDB at Scale

**CMDB governance model:**
```
CMDB Manager owns: data quality, deduplication, class strategy
Data sources: Discovery (authoritative), imports (reconciled), manual (least preferred)
IRE (Identification and Reconciliation Engine):
  → defines which source "wins" per attribute (precedence rules)
  → deduplicates CIs from multiple sources
```

**CMDB health:**
```
Navigator: Configuration → CMDB Dashboard
Metrics: correctness, compliance, completeness
Target: health score > 80% for enterprise readiness
```

**CI class strategy:**
```
Extend: create subclasses only when justified (unique attributes needed)
Avoid: creating too many custom CI classes (maintenance burden)
Use: out-of-box classes wherever possible
```

---

### Security Architecture

**ACL (Access Control List) design principles:**

| Principle | Detail |
|---|---|
| Least privilege | Grant minimum access needed for each role |
| Role hierarchy | Build roles in layers (base → elevated → admin) |
| Script-based ACLs | Use only when condition-based ACLs are insufficient |
| Avoid: direct user grants | Always use groups + roles, not individual user grants |
| Audit ACL changes | All ACL modifications should go through change control |

**Encryption:**
- Edge Encryption: encrypts data before it reaches ServiceNow (for highly sensitive fields)
- Column-level encryption: encrypt specific fields on specific tables
- Navigator: System Security → Column Encryption

**Platform security hardening:**
```
□ Disable guest access to Service Portal if not needed
□ Enforce MFA for all administrator accounts
□ Review and restrict `admin` role assignments
□ Implement IP allowlisting for admin login (if applicable)
□ Enable audit trail for sensitive tables
□ Regular review of ACLs and role assignments
```

---

### Application Architecture

**Scoped vs Global:**

| Aspect | Scoped App | Global |
|---|---|---|
| Isolation | Separate application scope | Shared global scope |
| Best for | Custom applications, ISV products | Simple customisations |
| Upgrade safety | Higher (isolated from core upgrades) | Lower (more collision risk) |
| Recommended approach | Always prefer scoped for new development |

**Update Sets:**
```
Navigator: System Update Sets → Local Update Sets → New
Best practices:
  → One update set per feature/story (not per developer)
  → Meaningful names: "ITSM-1234 - Add SLA rule for VIP customers"
  → Test preview on target instance before committing
  → Never commit to production directly — always promote through pipeline
```

**Plugin management:**
```
Navigator: System Definition → Plugins
Activate only required plugins
Deactivate unused plugins (reduces upgrade risk and instance bloat)
```

---

### Governance and Technical Standards

**Development standards (recommended):**
```
□ Naming convention: prefix all custom fields/tables with company prefix (e.g. u_ or x_companyname_)
□ All server-side scripts: no inline SQL, use GlideRecord API
□ Business Rules: avoid complex logic — use Script Includes
□ Script Includes: always client-callable = false unless explicitly needed
□ Flow Designer: preferred over Workflows for new development
□ ATF coverage: all critical business workflows have automated tests
□ Code review: peer review before merge to integration branch
```

**Instance hygiene:**
```
□ Scheduled job audit: deactivate unused scheduled jobs
□ Orphaned records: clean up inactive users, expired ACLs
□ Update set backlog: resolve stale/uncommitted update sets
□ Script error log: monitor System Logs → Script Errors weekly
□ Memory/performance: review slow queries in Performance Analytics
```

---

## CTA Preparation Path

1. Hold **CSA + 2–3 CIS certs** across different product areas
2. Complete **CTA learning path** on Now Learning
3. Study **ServiceNow Architecture Guides** on docs.servicenow.com
4. Practise **ADS-style problems** — design architectures for complex scenarios
5. Review **upgrade best practices** and **integration patterns** documentation
6. Engage with **ServiceNow Community** — CTA forum and architecture threads
7. Attend **ServiceNow Knowledge conference** — architecture sessions

---

## Common Exam Scenarios (Written)

**Scenario 1:** A customer has two subsidiaries that must not share any data but want one IT operations team. What instance strategy do you recommend?
→ **Two instances** (strict data isolation) + **ServiceNow-to-ServiceNow (SNc) integration** via IntegrationHub to allow the shared IT team to work across both — OR single instance + domain separation if data separation requirements can be met at the domain level.

**Scenario 2:** After an upgrade, three business rules stopped working. What process should have prevented this?
→ **ATF (Automated Test Framework)** — tests should have been written for those business rules and run in Dev/Test after the upgrade before promoting to production.

**Scenario 3:** An integration with a legacy SFTP file drop needs to import 50,000 records nightly into the CMDB. What is the recommended approach?
→ **Import Sets with Transform Maps** — scheduled import set job reads the SFTP file, Transform Map maps columns to CI fields, IRE handles deduplication. MID Server handles SFTP file retrieval if on-premise.

**Scenario 4:** A developer has been building directly in the production instance. What governance control should be implemented?
→ Enforce a **development pipeline** — Dev → Test → Pre-Prod → Production — with update sets/source control promotion. Lock Production update set creation to a release manager role.

---

## Study Resources

| Resource | URL |
|---|---|
| **CTA Programme Info** | https://www.servicenow.com/services/training-and-certification/certified-technical-architect.html |
| **Now Learning — CTA path** | https://nowlearning.servicenow.com — CTA learning path |
| **ServiceNow Architecture Guides** | https://docs.servicenow.com — Architecture section |
| **ServiceNow Community** | https://www.servicenow.com/community/ — Architect forum |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals (prerequisite baseline)
- ServiceNow-CAD — Application development (complements architecture)
- ServiceNow-CMA — Certified Master Architect (next level above CTA)

---

## References

- ServiceNow CTA Programme: https://www.servicenow.com/services/training-and-certification/certified-technical-architect.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]