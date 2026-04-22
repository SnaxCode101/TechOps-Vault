---
title: "ServiceNow CIS-PA — Certified Implementation Specialist: Performance Analytics"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-PA, performance-analytics, KPIs, dashboards, indicators, study-guide, job-aid, intermediate]
source: "ServiceNow official Performance Analytics documentation (docs.servicenow.com), Now Learning CIS-PA training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-PA — Certified Implementation Specialist: Performance Analytics

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Performance Analytics |
| Abbreviation | CIS-PA |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | Performance Analytics Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; reporting and KPI concepts |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official PA docs:** https://docs.servicenow.com/bundle/washingtondc-now-intelligence/page/use/performance-analytics/concept/c_PerformanceAnalytics.html

---

## What CIS-PA Validates

CIS-PA certifies you can **implement and configure ServiceNow Performance Analytics** — creating KPI indicators, collecting time-series data, configuring dashboards and breakdowns, and enabling data-driven decision making across ITSM and other modules.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| PA Architecture | Indicators, scores, collections, data collection jobs |
| Indicator Sources | Scripted vs formula indicators, source tables |
| Breakdowns | Breakdown elements, breakdown sources, mapped breakdowns |
| Dashboards & Widgets | Analytics Hub, dashboards, widget types |
| Targets & Thresholds | Setting KPI targets, threshold colours |
| Automated Actions | PA automated actions, threshold-triggered alerts |
| Roles | PA roles, dashboard sharing |

---

## Core Concepts — Study & Job Aid

### PA Data Model

```
Indicator Source (table + filter that defines what to measure)
    ↓
Indicator (the KPI — e.g. "Open P1 Incidents")
    ↓ collected over time
Score (a data point for a specific date + breakdown element value)
    ↓ visualised as
Widget (on a Dashboard)
```

**Key tables:**
| Table | Purpose |
|---|---|
| `pa_indicators` | Indicator definitions |
| `pa_scores` | Historical score data (time-series) |
| `pa_breakdowns` | Breakdown definitions |
| `pa_cubes` | Multi-dimensional score storage |
| `pa_dashboards` | Dashboard definitions |

---

### Indicators

**Indicator types:**
| Type | Description |
|---|---|
| Scripted Indicator | Uses a GlideRecord script to count or aggregate records |
| Formula Indicator | Calculates from other indicators (e.g. A / B × 100) |
| Manual Indicator | Data entered manually — for metrics not in ServiceNow |

**Creating an Indicator:**
```
Navigator: Performance Analytics → Indicators → New
Fields:
  Name:           P1 Incident Resolution Rate
  Source:         Scripted
  Script:         GlideRecord query on `incident` where priority=1 and state=Resolved
  Frequency:      Daily
  Direction:      Maximise (higher is better) or Minimise (lower is better)
  Unit:           Count or %
```

**Data collection:**
```
Navigator: Performance Analytics → Data Collector → Jobs
- "Collect Now" button triggers immediate collection
- Scheduled jobs run daily/weekly per indicator configuration
- Scores stored in pa_scores table
```

---

### Breakdowns

Breakdowns slice indicator scores by a dimension — like an ITSM "group by".

**Example breakdown:** Slice "Open Incidents" by **Assignment Group**
→ Score for "Network Team", score for "Desktop Team", score for "Service Desk" — all tracked over time.

**Creating a Breakdown:**
```
Navigator: Performance Analytics → Breakdowns → New
Fields:
  Name:          Assignment Group Breakdown
  Source table:  sys_user_group
  Filter:        active = true
  Label field:   name
```

**Mapping a Breakdown to an Indicator:**
```
On the Indicator → Breakdown Mapping tab → Add mapping
Select: Breakdown = Assignment Group Breakdown
Field on source: assignment_group
```

**Breakdown types:**
| Type | Purpose |
|---|---|
| Basic breakdown | Single dimension (e.g. by priority, by group) |
| Mapped breakdown | Maps the indicator source field to the breakdown element |
| Automated breakdown | Dynamically creates breakdown elements from data |

---

### Targets and Thresholds

**Targets:**
- Define a KPI target value for an indicator
- Navigator: Indicator → Targets tab → New
- Fields: Target value, From date, To date, Breakdown element (if specific to a group)

**Thresholds (colours):**
| Colour | Meaning |
|---|---|
| Green | On target / good |
| Yellow | Approaching threshold / caution |
| Red | Breached threshold / bad |

- Configuration: Indicator → Thresholds tab → define ranges

---

### Analytics Hub and Dashboards

**Analytics Hub:**
```
Navigator: Performance Analytics → Analytics Hub
```
- Interactive exploration of PA data
- Ad-hoc filtering, breakdown exploration, time range selection
- No configuration required — available to all users with `pa_viewer` role

**Dashboards:**
```
Navigator: Performance Analytics → Dashboards → New
```
- Add widgets: Score widgets, chart widgets, breakdown widgets
- Arrange via drag-and-drop
- Share with groups via: Dashboard → Sharing tab

**Widget types:**
| Widget | Purpose |
|---|---|
| Score | Shows current value of one indicator |
| Time series chart | Shows indicator trend over time (line, bar) |
| Breakdown chart | Shows indicator sliced by breakdown (pie, bar) |
| Comparison | Compares multiple indicators side-by-side |
| Dial/Gauge | Shows indicator vs target as gauge |

---

### Automated Actions

```
Navigator: Performance Analytics → Automated Actions → New
```

Automated Actions trigger when an indicator threshold is crossed:
- Create an Incident, Task, or Notification
- Send email alert to a group
- Trigger a Flow Designer flow

**Example:**
- Indicator: "Open P1 Incidents" crosses threshold > 10
- Action: Create a task for the service desk manager + send notification

---

### PA Roles

| Role | Access |
|---|---|
| `pa_viewer` | View dashboards, Analytics Hub |
| `pa_power_user` | Create personal dashboards, run collections |
| `pa_analyst` | Create and configure indicators, dashboards |
| `pa_admin` | Full PA configuration and administration |

---

## Common Exam Scenarios

**Scenario 1:** An indicator shows no scores despite being configured and the schedule running. What should you check?
→ Run a **manual data collection** (Collect Now) and check the Collection Log for errors. Common causes: script error in scripted indicator, or no records matching the source filter.

**Scenario 2:** The service desk manager wants to see "Open Incidents by Priority" trend over the last 90 days. What do you build?
→ An **Indicator** for "Open Incidents" with a **Priority Breakdown** → display as a **time series chart** on a Dashboard.

**Scenario 3:** A target is set for P1 resolution time at 4 hours, but the indicator never turns red even when breached. What should you check?
→ The **Threshold configuration** on the indicator — verify that the "red" threshold is correctly set and that the indicator direction (Minimise) is appropriate.

**Scenario 4:** A new team lead wants their own dashboard showing only their team's metrics. How do you set this up?
→ Create a **Dashboard** → add widgets filtered by the relevant breakdown element (their assignment group) → share the dashboard with the team lead's group.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — PA path** | https://nowlearning.servicenow.com — Performance Analytics Fundamentals |
| **ServiceNow Docs — PA** | https://docs.servicenow.com — search "Performance Analytics" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-ITSM — PA is commonly used to measure ITSM KPIs

---

## References

- ServiceNow Docs — Performance Analytics: https://docs.servicenow.com/bundle/washingtondc-now-intelligence/page/use/performance-analytics/concept/c_PerformanceAnalytics.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]