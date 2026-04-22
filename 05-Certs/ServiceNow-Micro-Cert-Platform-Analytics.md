---
title: "ServiceNow Micro-Certification — Platform Analytics"
type: cert-earned
category: certification
date: 2026-03-26
issued: 2026-03-26
tags: [cert, servicenow, analytics, reporting, dashboards, earned, micro-cert, intermediate]
difficulty: intermediate
---

# ServiceNow Micro-Certification — Platform Analytics

## Certification Details

| Field | Value |
|---|---|
| Certification | Micro-Certification — Platform Analytics |
| Issued by | ServiceNow |
| Issued | March 26, 2026 |
| Status | ✅ Earned |
| Signed by | Jayney Howson, SVP Global Learning & Development |

---

## What This Covers

This micro-certification validates competency in ServiceNow Platform Analytics — including Performance Analytics, Reports, and Dashboards for operational visibility and data-driven decision making.

**Core competencies demonstrated:**

- Creating and configuring Reports (bar, pie, trend, pivot, heatmap, etc.)
- Building Dashboards with multiple widgets and interactive filters
- Performance Analytics (PA) — indicators, breakdowns, scores, and time-series data
- Configuring PA Indicators: automated data collection on a schedule
- PA Dashboards vs standard ServiceNow Dashboards
- Using Analytics Hub for guided analytics
- Sharing and scheduling report delivery
- Applying conditions and filters to reports
- Understanding data sources: tables, database views

---

## Key Analytics Concepts

### Reports

| Report Type | Best For |
|---|---|
| Bar / Column | Comparing categories (incidents by priority, changes by state) |
| Pie / Donut | Showing proportions |
| Line / Trend | Change over time |
| Pivot Table | Multi-dimensional data |
| Heatmap | Density by two dimensions (e.g., day × hour) |
| List | Tabular data export |
| Single Score | KPI highlight (total open incidents) |

### Performance Analytics vs Reports

| Feature | Standard Reports | Performance Analytics |
|---|---|---|
| Historical data | Snapshot only | Time-series history |
| Scheduling | Manual or email | Automated scores on a schedule |
| Breakdowns | Limited | Multi-dimensional breakdowns |
| License | Included | PA license required |
| Best for | Ad hoc queries | KPI tracking, trend analysis |

### Dashboards

```
Dashboard
  └── Tabs (optional grouping)
        └── Widgets
              ├── Reports (embedded)
              ├── PA Indicators / Scores
              ├── Gauge widgets
              ├── Data tables
              └── Custom HTML
```

**Interactive filters:** Apply conditions across all compatible widgets on a dashboard simultaneously — useful for filtering by assignment group, time range, or category.

---

## Common Analytics Use Cases in SecOps / Healthcare IT

| Use Case | Analytics Approach |
|---|---|
| Incident volume by priority trend | PA Indicator — time series |
| Open incidents by assignment group | Bar report on `incident` table |
| SLA compliance rate | PA Indicator with SLA task breakdown |
| Epic downtime frequency | Trend report filtered to Epic CI |
| Change success rate | Pie chart — change_request by close_code |
| KB article utilization | Report on `kb_use` table |

---

## Related Study Notes

- [[ServiceNow-CSA]] — Reporting and PA covered in CSA exam domains
- ServiceNow-Flow-Designer — Automate report delivery via flows
- [[ServiceNow-Micro-Cert-Welcome]] — Platform foundations

---

## Next Steps

- [ ] ServiceNow Certified System Administrator (CSA) — includes Reporting domain
- [ ] Performance Analytics (PA) Implementation Specialist

---

## Resources

- **Now Learning** (official): https://nowlearning.servicenow.com
- **ServiceNow Docs — Performance Analytics**: https://docs.servicenow.com/bundle/pa-landing
- **ServiceNow Community — Analytics**: https://www.servicenow.com/community/analytics-intelligence-forum/ct-p/analytics-intelligence

---
*Cert earned: 2026-03-26*

---
<- [[Certs-MOC]]