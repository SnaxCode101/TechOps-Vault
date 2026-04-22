---
title: "ServiceNow CIS-SPM — Certified Implementation Specialist: Strategic Portfolio Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-SPM, strategic-portfolio-management, ITBM, demand, projects, resources, study-guide, job-aid, intermediate]
source: "ServiceNow official SPM documentation (docs.servicenow.com), Now Learning CIS-SPM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-SPM — Certified Implementation Specialist: Strategic Portfolio Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Strategic Portfolio Management |
| Abbreviation | CIS-SPM (formerly CIS-ITBM) |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | SPM Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; project portfolio management or IT governance background |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official SPM docs:** https://docs.servicenow.com/bundle/washingtondc-strategic-portfolio-management/page/product/strategic-planning-workspace/concept/c_SPMOverview.html

---

## What CIS-SPM Validates

CIS-SPM certifies you can **implement and configure ServiceNow Strategic Portfolio Management** — aligning IT investments with business strategy through demand management, project portfolio management, resource management, and investment/budget tracking.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| SPM Architecture | Portfolios, programmes, projects, demands |
| Demand Management | Idea/demand intake, scoring, approval |
| Project Management | Projects, tasks, milestones, Agile/Waterfall |
| Resource Management | Resource plans, capacity, allocation |
| Investment & Budget | Business cases, cost tracking, ROI |
| Agile Development | Stories, epics, sprints, release plans |
| Reporting & Portfolio View | Portfolio boards, roadmaps, PA integration |

---

## Core Concepts — Study & Job Aid

### SPM Hierarchy

```
Strategy (business objectives)
    └── Portfolio (group of related programmes)
          └── Programme (group of related projects)
                └── Project (discrete work effort)
                      └── Project Tasks / Stories (work items)
```

**Navigator:** Strategic Planning → Portfolios

---

### Demand Management

Demand captures **ideas, requests, and proposals** before they become projects.

```
Navigator: Demand Management → Demands → New
```

**Demand lifecycle:**
```
Idea → Submitted → Under Review → Approved → Convert to Project
                                → Rejected → Archived
```

**Demand scoring:**
- Demands scored on: Strategic Alignment, Business Value, Risk, Cost
- Scoring matrix configured by portfolio managers
- Ranked demand list helps prioritise which ideas proceed to projects

**Converting demand to project:**
```
On Demand record → Actions → Convert to Project
→ Project record created with demand data pre-populated
→ Demand linked to project for traceability
```

---

### Project Management

```
Navigator: Project Management → Projects → New
```

**Project fields:**
| Field | Purpose |
|---|---|
| Name | Project name |
| Programme | Parent programme |
| Portfolio | Parent portfolio |
| Project Manager | Accountable person |
| Start / End Date | Planned schedule |
| Budget | Total approved budget |
| Status | On Track / At Risk / Off Track |
| Methodology | Waterfall or Agile |

**Project lifecycle (Waterfall):**
```
Initiating → Planning → Executing → Monitoring & Controlling → Closing
```

**Project Tasks:**
- Navigator: Project → Tasks tab
- Define: task name, dependency, assigned to, start/end, % complete
- Gantt chart view shows task schedule and critical path

**Milestones:**
- Zero-duration tasks representing key deliverables
- Visible on project timeline/roadmap

---

### Agile Development (Scrum)

```
Navigator: Agile Development → Backlogs
```

**Agile hierarchy:**
```
Epic (large feature)
    └── Story (user story / feature)
          └── Task (technical work item)
```

**Sprint planning:**
```
Navigator: Agile Development → Sprints → New
Define: Sprint name, start/end date, associated release
Add stories from backlog → estimate story points
Sprint board: columns (To Do → In Progress → Done)
```

**Velocity tracking:**
- Points completed per sprint tracked automatically
- Used to forecast future sprint capacity

---

### Resource Management

Resource management tracks who is working on what and at what capacity.

```
Navigator: Resource Management → Resource Plans
```

**Resource Plan:**
- Attached to a project
- Defines: which skills/roles needed, when, for how many hours
- Navigator: Project → Resource Plans tab → New

**Resource allocation:**
```
Resource Plan created → Resource Manager reviews
→ Named resources allocated → hours confirmed
→ Capacity view: see resource utilisation across all projects
```

**Capacity view:**
```
Navigator: Resource Management → Capacity Forecasting
Shows: resource utilisation (%) over time, over/under-allocated periods
```

---

### Investment and Budget

**Business case:**
```
Navigator: Business Case Management → Business Cases → New
Fields: Benefits (financial + non-financial), Costs, Risk, NPV/ROI calculation
```

**Budget tracking:**
- Planned budget vs actual spend tracked on project
- Cost plans: break budget into time-phased allocations
- Navigator: Project → Cost Plans tab

**Financial forecasting:**
```
Portfolio view → shows total investment across all projects
Allows: compare planned vs actual spend, reforecast if needed
```

---

### Portfolio Boards and Roadmaps

**Portfolio board:**
```
Navigator: Strategic Planning → Portfolio Boards
Visual board: projects displayed by phase, status, priority
Drag-and-drop to reprioritise
```

**Roadmap:**
```
Navigator: Strategic Planning → Roadmaps
Timeline view of projects, milestones, and dependencies across the portfolio
```

**Strategic alignment view:**
- Shows how each project maps to business objectives/OKRs
- Helps leadership prioritise based on strategic value

---

### SPM Roles

| Role | Access |
|---|---|
| `spm_user` | View projects and demands |
| `project_manager` | Create and manage projects |
| `portfolio_manager` | Manage portfolios, approve demands |
| `resource_manager` | Manage resource plans and allocation |
| `spm_admin` | Full SPM configuration and administration |

---

## Common Exam Scenarios

**Scenario 1:** A business unit submits 20 ideas. The portfolio manager needs to prioritise the top 5 for funding. What SPM feature facilitates this?
→ **Demand Scoring** — configure a scoring matrix (strategic alignment, value, risk, cost) and score all 20 demands. The ranked list identifies the top candidates for project conversion.

**Scenario 2:** A project is on-track on the Gantt chart but its budget is at 80% with 40% of work complete. How does the project manager see this in SPM?
→ **Budget tracking** on the project record — the Cost Plan vs Actuals view will show the financial burn rate. A project status report should flag this as "At Risk" due to budget burn.

**Scenario 3:** A resource is allocated to 3 projects simultaneously and is at 150% capacity. Where does the Resource Manager see this?
→ **Capacity Forecasting** view — shows over-allocation visually. The Resource Manager can negotiate with project managers to defer work or reallocate tasks.

**Scenario 4:** The CTO wants a single view of all active projects, their status, and alignment to this year's strategic goals. What do you show?
→ A **Portfolio Board** with strategic alignment view — shows all projects, RAG status, and mapped business objectives. Supplement with a **Roadmap** for timeline visibility.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — SPM path** | https://nowlearning.servicenow.com — SPM Fundamentals |
| **ServiceNow Docs — SPM** | https://docs.servicenow.com — search "Strategic Portfolio Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-ITSM — ITSM changes can trigger demands in SPM

---

## References

- ServiceNow Docs — SPM: https://docs.servicenow.com/bundle/washingtondc-strategic-portfolio-management/page/product/strategic-planning-workspace/concept/c_SPMOverview.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]