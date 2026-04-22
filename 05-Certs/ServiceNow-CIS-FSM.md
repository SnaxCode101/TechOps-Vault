---
title: "ServiceNow CIS-FSM — Certified Implementation Specialist: Field Service Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-FSM, field-service, work-orders, dispatch, scheduling, study-guide, job-aid, intermediate]
source: "ServiceNow official FSM documentation (docs.servicenow.com), Now Learning CIS-FSM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-FSM — Certified Implementation Specialist: Field Service Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Field Service Management |
| Abbreviation | CIS-FSM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | FSM Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; field operations or workforce management background helpful |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official FSM docs:** https://docs.servicenow.com/bundle/washingtondc-field-service-management/page/product/field-service-management/concept/c_FieldServiceMgmt.html

---

## What CIS-FSM Validates

CIS-FSM certifies you can **implement and configure ServiceNow Field Service Management** — managing field technician dispatch, work orders, scheduling, parts management, and mobile workforce operations.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| FSM Architecture | Work orders, tasks, agents, territories |
| Work Order Management | Work order creation, tasks, lifecycle |
| Scheduling & Dispatch | Dispatcher Workspace, auto-assignment, SLAs |
| Agent Mobile App | Field agent app, task execution, parts |
| Skills & Territories | Skills-based assignment, territory management |
| Parts Management | Parts, inventory, parts requests |
| Integration | CSM, ITSM, IoT-triggered work orders |

---

## Core Concepts — Study & Job Aid

### FSM Data Model

```
Work Order (high-level job request)
    └── Work Order Task (individual task to be executed by one field agent)
          └── Agent assigned
          └── Parts required
          └── Location (site/asset)
          └── Time tracked (travel, work)
```

**Key tables:**
| Table | Purpose |
|---|---|
| `wm_order` | Work Order records |
| `wm_task` | Work Order Task records |
| `wm_agent` | Field Agent records (extends `sys_user`) |
| `wm_territory` | Territory records |
| `alm_stockroom` | Parts stockroom |

---

### Work Order Lifecycle

```
New → Work In Progress → Pending Dispatch → Dispatched → Work In Progress (on-site) → Closed Complete / Closed Incomplete
```

**Work Order vs Work Order Task:**
- **Work Order** = the parent job (e.g. "Repair Elevator — Site A")
- **Work Order Task** = a specific step (e.g. "Inspect motor", "Replace component") assigned to one agent

One Work Order can have multiple Tasks assigned to different agents.

---

### Dispatcher Workspace

The Dispatcher Workspace is the central tool for managing field workforce scheduling.

```
Navigator: Field Service → Dispatcher Workspace
```

**Features:**
- **Map view** — see agent locations and work orders geographically
- **Gantt view** — schedule tasks against agent availability/shifts
- **Queue** — unassigned tasks waiting for dispatch
- **Drag and drop** — assign tasks to agents directly on the Gantt

**Auto-assignment (Dynamic Scheduling):**
```
Navigator: Field Service → Administration → Scheduling Policies
```
- Define rules: match agent skills, territory, availability, SLA urgency
- Dynamic Scheduling engine auto-assigns tasks without dispatcher intervention
- Can be used alongside manual dispatch

---

### Agents (Field Technicians)

```
Navigator: Field Service → Field Agents
```

**Agent attributes:**
| Attribute | Purpose |
|---|---|
| Skills | Competencies (e.g. Electrical, HVAC, Networking) |
| Territory | Geographic area the agent covers |
| Shift | Working hours / availability |
| Location | Real-time GPS location (via mobile app) |
| Capacity | Max tasks per shift |

**Skills-based dispatch:**
- Work Order Task has a required skill
- Dispatch assigns only agents with matching skills in the relevant territory

---

### Territories

Territories define geographic coverage areas for agent groups.

```
Navigator: Field Service → Territories → New
Fields: Name, Region, Boundary (polygon on map or postcode list)
Assign agents to territories
```

Tasks auto-routed to agents in the correct territory based on site location.

---

### Field Agent Mobile App

Field agents use the **ServiceNow Field Service Agent mobile app**:

**Features:**
- View assigned tasks and schedule
- Navigate to site (map integration)
- Log travel start/arrival/departure times
- Record work performed
- Request parts
- Capture customer signature
- Close tasks on-site

**Mobile task lifecycle:**
```
Agent receives task → Travel start → Arrive on site → Begin work → Parts used logged → Work completed → Customer signs off → Task closed
```

---

### Parts Management

```
Navigator: Field Service → Parts Management → Parts
```

**Parts workflow:**
```
Work Order Task requires part
    ↓
Parts Request created
    ↓
Part sourced from: agent's van stock / central stockroom / supplier order
    ↓
Part consumed on task completion → inventory decremented
    ↓
If sourced externally → procurement order raised
```

**Parts catalogue:**
- Each part has a model record (part number, description, unit cost)
- Stockrooms assigned to: central warehouse, agent van, regional depot

---

### SLAs in FSM

FSM SLAs define response and resolution time for work order tasks based on:
- Priority
- Customer service entitlement (if integrated with CSM)
- Work order type

**SLA breach escalation:**
- Notify dispatcher
- Auto-escalate task priority
- Alert field service manager

---

### FSM Roles

| Role | Access |
|---|---|
| `field_service_agent` | Field agent — view and update own tasks via mobile |
| `field_service_dispatcher` | Dispatcher — manage work orders, scheduling, dispatch |
| `field_service_manager` | Manager — reports, SLA oversight, agent management |
| `field_service_admin` | Full FSM configuration and administration |

---

## Common Exam Scenarios

**Scenario 1:** A work order task requires an HVAC-certified technician but keeps being assigned to general maintenance agents. What should you configure?
→ Add the **"HVAC" skill** as a required skill on the Work Order Task type → configure the **Scheduling Policy** to enforce skill matching during auto-assignment.

**Scenario 2:** A dispatcher reports that the Gantt view shows all agents as "Unavailable" despite active shifts. What should you check?
→ Agent **Shift definitions** — verify shifts are active and correctly configured for the current date. Also check agent records are linked to the correct territory.

**Scenario 3:** A field agent replaced a faulty component but the part came from their van stock and is now below minimum stock level. How does FSM handle this?
→ The **parts consumption** is logged on task closure → if stock drops below the minimum threshold, an automatic **parts replenishment request** is created to restock the van.

**Scenario 4:** A customer CSM case triggered a field visit requirement. How does CSM and FSM integrate?
→ A **Work Order is created from the CSM Case** — the case is linked to the work order. When the work order is closed, the case can be automatically updated/resolved via a flow.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — FSM path** | https://nowlearning.servicenow.com — FSM Fundamentals |
| **ServiceNow Docs — FSM** | https://docs.servicenow.com — search "Field Service Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-CSM — CSM cases can trigger FSM work orders
- ServiceNow-CIS-ITSM — ITSM change/incident can trigger FSM work orders

---

## References

- ServiceNow Docs — FSM: https://docs.servicenow.com/bundle/washingtondc-field-service-management/page/product/field-service-management/concept/c_FieldServiceMgmt.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]