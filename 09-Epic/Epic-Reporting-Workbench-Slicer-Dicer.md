---
title: Epic Reporting Workbench & Slicer Dicer
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - reporting
  - workbench
  - slicer-dicer
  - analytics
  - clarity
  - caboodle
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic Reporting and Analytics guides,
  Clarity/Caboodle documentation
difficulty: intermediate
---

# 📊 Epic Reporting Workbench & Slicer Dicer

## Overview

Epic provides multiple reporting tools for operational and clinical analytics. **Reporting Workbench** is Epic's built-in list/report tool inside Hyperspace for operational reports. **Slicer Dicer** is Epic's self-service cohort exploration and data visualization tool. Understanding both is essential for supporting analysts, clinical staff, and leadership who need data without going to a formal BI/DBA team.

> ⚠️ **Flag:** Feature names, field names, and security model details vary by Epic version and org configuration. The concepts below are accurate to broadly used Epic versions (2020–2024 range). Verify specifics in your Epic Test/Train environment or Epic UserWeb.

---

## Core Concepts

### Epic Reporting Ecosystem

```
Reporting Tools by Complexity
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Self-Service (Hyperspace)
  ├── Reporting Workbench    — list/tabular reports in Hyperspace
  ├── Slicer Dicer           — cohort exploration, trend charts
  └── Dashboards / Radar     — performance dashboards, tiles

Analyst / IT Level
  ├── Clarity (SQL)          — relational SQL database, Crystal/SSRS reports
  ├── Caboodle (EDW)         — Epic's data warehouse, Radar dashboards
  └── Cogito                 — Epic's analytics suite (PA, Radar, Slicer Dicer backend)

External BI
  └── Tableau / Power BI / Qlik   — connected to Clarity or Caboodle
```

### Data Sources
| Source | Type | Best For |
|---|---|---|
| **Chronicles** | Epic proprietary INI DB | Real-time data; accessed inside Epic only |
| **Clarity** | SQL Server relational DB | Structured reporting, Crystal/SSRS, custom SQL |
| **Caboodle** | SQL Server EDW | Complex analytics, Cogito, Slicer Dicer backend |

---

## Reporting Workbench

### What Is Reporting Workbench?
Reporting Workbench (RWB) is Epic's primary in-application report builder. It produces **list-based, tabular reports** from Epic data directly within Hyperspace. No SQL knowledge required — users select records, apply filters, and choose columns.

### Accessing Reporting Workbench
- Navigate via Epic menu: **Epic button > Reports > Reporting Workbench**
- Or via Navigator shortcut (type unique letters for your org's nav)
- Reports are organized by **report type** (Appointment, Patient, Order, Encounter, etc.)

### Key RWB Concepts

#### Report Types (Templates)
Each report is based on a **report template** that defines which master file/data set is queried. Common report types:
| Template | Primary Record | Example Use |
|---|---|---|
| Appointment Reports | Scheduled appointments | No-show analysis, scheduling utilization |
| Patient Reports | Patient demographics | Panel management, population lists |
| Encounter Reports | Clinical encounters | Visit volume, encounter diagnosis |
| Order Reports | Orders (lab, med, referral) | Order tracking, pending orders |
| In Basket Reports | In Basket messages | Message volume, response times |
| Referral Reports | Referrals | Referral tracking, completion rates |

#### Filters
- Filters narrow results (e.g., department, date range, visit type, provider, diagnosis)
- **Required filters** — must be filled in before running (prevents full-table scans)
- **Optional filters** — narrow results further

#### Columns / Fields
- Select which fields appear as columns in report output
- Fields map to Epic master file items (INI items)
- Calculated/custom columns possible with experience

#### Grouping & Sorting
- Reports can group rows (e.g., group by provider, then by department)
- Sort ascending or descending by any column

#### Sharing Reports
- **Private** — only visible to the creator
- **Shared to Group** — shared with an Epic user group or group of employees
- **Published** — org-wide report (requires appropriate security)
- Reports are stored as Epic records; library of shared reports available

#### Running & Output
- Reports can be run immediately or **scheduled** (run overnight, results emailed or stored)
- Output can be exported to: Excel (.xlsx), comma-delimited (.csv), printed
- Large reports should be scheduled off-hours to avoid system performance impact

### Security Model for RWB
- Access to RWB is controlled by **Epic Security Templates** (user's security class)
- Report visibility is based on: security class + report-level sharing settings
- Sensitive reports (e.g., employee health, behavioral health) should have restricted sharing
- Security item: `Reporting Workbench - Access` [VERIFY: Epic UserWeb]

---

## Slicer Dicer

### What Is Slicer Dicer?
Slicer Dicer is Epic's **self-service cohort exploration tool**. It allows clinical and operational users to slice a patient population by multiple dimensions (diagnoses, demographics, orders, visits, etc.) and visualize trends over time. It is more powerful than RWB for population-level questions but less suited for row-level lists.

### Accessing Slicer Dicer
- Epic Hyperspace: **Epic button > Reports > Slicer Dicer**
- Or via Epic Dashboard tiles (if configured)
- Also accessible in **MyChart for Physicians** (limited version in some org configurations)

### Core Slicer Dicer Concepts

#### Data Models
Slicer Dicer queries run against **Caboodle** (or Clarity in older versions). The data is organized into **Data Models** — pre-built subject areas:
| Data Model | What It Covers |
|---|---|
| Patients | Demographics, age, gender, race, zip code |
| Encounters | Visit types, dates, departments, diagnoses |
| Appointments | Scheduled visits, no-shows, cancellations |
| Orders | Lab, med, referral, imaging orders |
| Diagnoses | Problem list, encounter diagnoses (ICD-10) |
| Medications | Prescriptions, med administration |
| Lab Results | Lab values, trends |
| Procedures | CPT-coded procedures |
| Social History | SDOH, smoking, alcohol |

#### Slices and Filters
- **Slice** — the dimension you're analyzing (e.g., "Patients with Diabetes")
- **Filter** — narrows the population (e.g., age > 18, active patient in your network)
- **Measure** — the metric you're counting or averaging (e.g., count of patients, avg A1c value)

#### Building a Cohort in Slicer Dicer
1. Open Slicer Dicer
2. Select a **Data Model** (e.g., Patients)
3. Add **slices** from the left panel (drag or click to add filters like "Diagnosis = Type 2 Diabetes")
4. Apply additional filters (department, date range, provider)
5. Select visualization type (bar chart, trend line, table)
6. **Save** the slice definition for reuse or sharing
7. **Export** results to Excel or send to Reporting Workbench for row-level detail

#### Visualization Options
| Type | Use Case |
|---|---|
| Count over time | Trend of patients/encounters over months |
| Bar chart by group | Breakdown by department, race, age group |
| Funnel | Care gaps — how many patients completed each step |
| Table | Numeric breakdown by dimension |

### Slicer Dicer Security
- Access controlled by **Epic Security Template**
- Data model visibility is filtered by the user's assigned patients/departments/security
- A provider sees their panel; a department manager sees their department; an analyst may see org-wide
- PHI exposure: Slicer Dicer shows aggregate counts by default; drill-down to patient lists may require additional security rights
- Security item: `Slicer Dicer - Access` and `Slicer Dicer - Patient List Drill Down` [VERIFY: Epic UserWeb]

---

## Clarity — SQL-Based Reporting

### What Analysts Need to Know
- **Clarity** is a SQL Server relational database that receives nightly (or near-real-time) extracts from Chronicles
- Analysts write SQL queries or use Crystal Reports / SSRS against Clarity
- Epic provides a **Clarity Data Dictionary** (downloadable from Epic UserWeb) with all table and column definitions
- Key Clarity tables:
| Table | Content |
|---|---|
| `PAT_ENC` | Patient encounters |
| `PATIENT` | Patient demographics |
| `CLARITY_DEP` | Departments |
| `CLARITY_SER` | Providers (SER records) |
| `ORDER_PROC` | Procedural/lab orders |
| `CLARITY_EAP` | Visit types (EAP) |
| `PAT_ENC_RSN_VISIT` | Encounter diagnoses |
| `HSP_ACCOUNT` | Hospital account/billing |

> ⚠️ **Flag:** Clarity table and column names vary between Epic versions and may be customized per org. Always reference your org's specific Clarity data dictionary from Epic UserWeb rather than relying on generic sources.

---

## Common Scenarios & Job-Aid

### Scenario: Building a No-Show Report
1. Open Reporting Workbench > New Report
2. Select **Appointment** report type
3. Filter: Date range = last 30 days, Status = No-Show
4. Add columns: Patient Name, MRN, Department, Provider, Appointment Date, Visit Type
5. Group by Department
6. Run, review, export to Excel
7. Schedule to run automatically if needed weekly

### Scenario: Identifying Patients Due for Flu Vaccine (Slicer Dicer)
1. Open Slicer Dicer > Patients data model
2. Slice: Age ≥ 6 months (or ≥ 18 for adult focus)
3. Filter: Active patient status, seen in last 24 months in your network
4. Add slice: Has NOT received flu vaccine in current season (immunization order/admin)
5. Review cohort count
6. Export patient list to InBasket outreach workflow or MyChart messaging campaign

### Scenario: RWB Report Not Showing Expected Results
1. Check filter values — required filters set correctly?
2. Confirm data model scope — is department filter set correctly?
3. Check run date/time — Clarity is a nightly extract; same-day data may not appear
4. Check security — can the user running the report see all relevant departments/providers?
5. Run with broader filters first, then narrow

---

## Related Notes
- Epic-Overview
- Epic-Content-Management
- Epic-Department-Build-Facility-Structure

## References
- Epic UserWeb — Reporting Workbench Build and Configuration
- Epic UserWeb — Slicer Dicer Configuration and Security Guide
- Epic UserWeb — Clarity Data Dictionary (version-specific)
- Epic Cogito Analytics: https://www.epic.com/software/cogito (marketing overview)

---
<- [[Epic-MOC]]