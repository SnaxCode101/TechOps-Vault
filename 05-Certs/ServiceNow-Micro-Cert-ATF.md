---
title: "ServiceNow Micro-Certification — Automated Test Framework"
type: cert-earned
category: certification
date: 2026-03-27
issued: 2026-03-27
tags: [cert, servicenow, atf, testing, automation, earned, micro-cert, intermediate]
difficulty: intermediate
---

# ServiceNow Micro-Certification — Automated Test Framework

## Certification Details

| Field | Value |
|---|---|
| Certification | Micro-Certification — Automated Test Framework |
| Issued by | ServiceNow |
| Issued | March 27, 2026 |
| Status | ✅ Earned |
| Signed by | Jayney Howson, SVP Global Learning & Development |

---

## What This Covers

This micro-certification validates competency in ServiceNow's Automated Test Framework (ATF) — the built-in platform tool for creating, running, and managing automated regression tests for ServiceNow customizations.

**Core competencies demonstrated:**

- Understanding ATF architecture: Tests, Test Steps, Test Suites
- Creating automated tests using the ATF Test Designer (UI-based)
- Using built-in Test Step Configurations (form interaction, assertions, navigation)
- Writing custom test step configurations with server-side scripts
- Organizing tests into Test Suites for batch execution
- Configuring ATF to run in CI/CD pipelines (automated on deployment)
- Interpreting Test Results and failure analysis
- Using Impersonation within tests to validate role-based access
- ATF best practices for upgrade and release validation

---

## ATF Architecture

```
Test Suite
  └── Tests (ordered)
        └── Test Steps (ordered)
              ├── Open a Form
              ├── Set Field Values
              ├── Submit a Record
              ├── Assert Field Value
              ├── Assert Form Section Visible
              ├── Navigate to URL
              ├── Run Server Script
              └── Impersonate User
```

---

## Key ATF Concepts

| Concept | Detail |
|---|---|
| **Test** | Single automated scenario (e.g., "Create P1 Incident and verify SLA triggered") |
| **Test Step** | Individual action within a test (fill field, click button, assert value) |
| **Test Suite** | Ordered collection of tests — run together for regression |
| **Step Configuration** | Template defining what a step type does (UI action, server assertion, etc.) |
| **ATF Runner** | Browser-based executor; runs tests in the Now Platform UI |
| **Headless Runner** | Runs ATF without a browser UI — required for CI/CD |
| **Test Data** | Managed via test setup steps; isolated from production data |
| **Rollback** | ATF can be configured to roll back test records after execution |

---

## Common ATF Test Scenarios

| Scenario | Steps Involved |
|---|---|
| Incident creation validation | Open form → Set fields → Submit → Assert state = New |
| SLA trigger verification | Create incident with P1 → Assert SLA record exists |
| Flow execution test | Trigger record → Wait → Assert downstream record created |
| Role-based access test | Impersonate restricted user → Assert field not editable |
| Approval workflow test | Create change request → Assert approval record created |
| UI policy validation | Set field to trigger UI policy → Assert dependent field hidden/shown |

---

## ATF in CI/CD Pipeline

```
Developer pushes update set
        │
        ▼
CI pipeline picks up (Jenkins / GitHub Actions / ServiceNow Pipeline)
        │
        ▼
ATF Test Suite executes (headless)
        │
        ├── PASS → Promote to next environment
        └── FAIL → Block promotion + notify developer
```

**ServiceNow CI/CD Application:** Available on ServiceNow Store; integrates ATF with external CI tools via REST API.

---

## ATF vs Manual Testing

| Aspect | ATF (Automated) | Manual Testing |
|---|---|---|
| Speed | Fast (minutes for full suite) | Slow (hours/days) |
| Repeatability | Identical every run | Human variation |
| Regression coverage | High with proper suite | Limited by time |
| Setup effort | High initially | Low initially |
| Best for | Regression, upgrades, releases | Exploratory, UX review |

---

## ATF in Healthcare / Epic Environments

ATF is especially valuable in healthcare ServiceNow deployments:
- **Epic upgrade coordination:** Run ATF suite before/after Epic-related ServiceNow changes
- **ITSM workflow validation:** Ensure incident/change flows behave correctly after upgrades
- **Compliance evidence:** ATF test results serve as documented regression testing for change management records
- **Access control validation:** Test that PHI-adjacent fields are restricted to correct roles

---

## Related Study Notes

- [[ServiceNow-CSA]] — ATF covered in CSA exam; testing and debugging domain
- ServiceNow-Flow-Designer — Flows can be tested via ATF
- [[ServiceNow-Micro-Cert-Flows]] — Flow cert; pairs with ATF for full automation coverage
- ServiceNow-Epic-Integration — ATF validates Epic-adjacent workflows

---

## Next Steps

- [ ] ServiceNow Certified System Administrator (CSA) — ATF in exam scope
- [ ] Certified Application Developer (CAD) — ATF for custom app testing
- [ ] Explore ServiceNow CI/CD Application for pipeline integration

---

## Resources

- **Now Learning** (official): https://nowlearning.servicenow.com
- **ServiceNow Docs — ATF**: https://docs.servicenow.com/bundle/atf-landing
- **ServiceNow Developer Portal**: https://developer.servicenow.com
- **ServiceNow Community — ATF**: https://www.servicenow.com/community/

---
*Cert earned: 2026-03-27*

---
<- [[Certs-MOC]]