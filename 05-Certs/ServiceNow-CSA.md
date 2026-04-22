---
title: ServiceNow CSA — Certified System Administrator
tags:
  - intermediate
  - cert
  - servicenow
  - CSA
  - system-administrator
  - ITSM
  - platform
topic: Certification Study
difficulty: intermediate
created: 2026-03-27
updated: 2026-04-04
source: ServiceNow CSA Exam Blueprint (KB0011554, updated January 2026),
  docs.servicenow.com, Now Learning
---

# 🎓 ServiceNow CSA — Certified System Administrator

## Overview

The ServiceNow Certified System Administrator (CSA) is the foundational ServiceNow certification and the prerequisite for all advanced certs (CAD, CTA, CMA, all CIS tracks). It validates the ability to configure, maintain, and operate a ServiceNow instance.

| Field | Detail |
|---|---|
| Questions | 60 (multiple choice + multiple select) |
| Duration | 90 minutes |
| Passing score | ~70–75% (approximately 42–45/60) — ServiceNow does not publish the exact threshold |
| Cost | $300 USD per attempt |
| Delivery | Pearson VUE — online proctored or test center |
| Prerequisite training | ServiceNow Administration Fundamentals (Now Learning — free) |
| Retake policy | 1st→2nd: 3-day wait · 2nd→3rd: 30-day wait · All subsequent: 6-month wait |
| Blueprint version | Updated January 2026 — Yokohama/Zurich release |

**Official blueprint:** https://nowlearning.servicenow.com/kb?id=kb_article_view&sysparm_article=KB0011554

---

## Exam Domains (Current Blueprint — 6 Domains)

> ⚠️ The CSA blueprint was restructured. The current exam has **6 domains**, not the older 13-domain format. Verify against the official blueprint before your exam date.

| # | Domain | Weight | Est. Questions |
|---|---|---|---|
| 1 | Platform Overview and Navigation | 7% | ~4 |
| 2 | Instance Configuration | 10% | ~6 |
| 3 | Configuring Applications for Collaboration | 20% | ~12 |
| 4 | Self-Service and Automation | ~20% | ~12 |
| 5 | Database Management and Platform Security | 30% | ~18 |
| 6 | Data Migration and Integration | 13% | ~8 |

> ⚑ **Domains 3, 4, and 5 together account for nearly 70% of the exam.** Prioritize these three.

---

## Domain 1 — Platform Overview and Navigation (7%)

### ServiceNow Platform Overview

ServiceNow is a cloud-based, single-data-model platform. Everything lives in one database — no separate silos for different applications.

**Core platform capabilities:**
- ITSM — Incident, Problem, Change, Request
- ITOM — Discovery, Event Management, Service Mapping
- HR Service Delivery, Customer Service Management, SecOps
- App Engine — custom application development
- Flow Designer — automation without code

**Instance URL format:** `https://[instance-name].service-now.com`

**Three-instance model (standard):**
```
Development  →  Test/UAT  →  Production
(build here)    (verify)     (live)
Changes move via Update Sets through each environment.
```

### Next Experience Unified Navigation

The current ServiceNow UI is the **Next Experience** (also called Polaris UI):

| Element | Description |
|---|---|
| **Unified Navigation bar** | Left-side collapsible navigation — All menu, Favorites, History, workspaces |
| **Application Navigator** | Filter box at top-left — type any module name to jump to it instantly |
| **All menu** | Grid of all installed applications and their modules |
| **Favorites** | Star any form, list, or module for quick access |
| **History** | Recent records and pages visited |
| **Workspaces** | Role-specific full-page views (e.g., Agent Workspace, ITSM Workspace) |
| **Global search** | Search bar at top — searches across all records |
| **User menu** | Top-right — profile, theme, impersonation, logout |

### Lists and Forms

**List view** — multi-record view of a table:
- Columns = fields; rows = records
- Breadcrumbs show table name and current filter
- Right-click column header → personalize columns
- **Condition builder** (filter icon) — build AND/OR filter conditions
- **List editor** — inline edit directly in the list (double-click a field)

**Form view** — single-record view:
- Fields organized in sections
- Field labels, mandatory indicators (*), read-only fields
- **Form layout** — admins configure which fields appear via `sys_ui_section`
- **Related lists** — child records displayed at the bottom (e.g., Incident → Work Notes, Attachments)

---

## Domain 2 — Instance Configuration (10%)

### Applications and Plugins

| Concept | Detail |
|---|---|
| **Application** | A scoped grouping of tables, forms, flows, and logic that deliver functionality |
| **Plugin** | A ServiceNow-delivered feature that can be activated on an instance |
| **Scope** | Applications run in either Global scope or their own Scoped App scope |
| **Store app** | Installed from the ServiceNow App Store (Now Store) |

**Activating a plugin:**
```
Navigator: System Definition → Plugins
Search for plugin name → Activate/Upgrade
Note: Plugins cannot be deactivated once activated on production — always test on dev first
```

### Personalizing the Instance

**System Properties** control platform-wide behavior:
```
Navigator: System Properties → [category] OR type sys_properties.list

Key properties:
glide.ui.escape_all_script    → XSS protection
glide.email.smtp.active       → enable/disable email sending
glide.sys.date_format         → platform date format
glide.sys.time_format         → platform time format
css.base.color                → branding color
```

**Instance branding:**
- System Properties → Basic Configuration UI16 (or Next Experience settings)
- Set logo, banner color, browser tab title

**User preferences** (per-user, not global):
- Profile → Preferences
- Or set programmatically via `sys_user_preference` table

### Notifications

Notifications send email (or push/SMS) when record conditions are met.

```
Navigator: System Notification → Notifications → New

Key fields:
- Name
- Table (which table triggers this)
- When to send: Inserted, Updated, Inserted or Updated
- Conditions: filter for when notification fires
- Who will receive: specific users, roles, fields (Assigned to, Caller, etc.)
- Email template: subject + body using mail scripts and variables
- Type: Email, Push, SMS

Variables in notification body:
${assigned_to.name}     → display name of assigned user
${number}               → record number
${short_description}    → field value
```

### Service Level Agreements (SLAs)

SLAs define response and resolution time targets for records (typically incidents).

```
Navigator: Service Level Management → SLA Definitions

Key fields:
- Name
- Table (incident, etc.)
- Type: SLA, OLA, UC
- Start condition: when does the clock start?
- Stop condition: when does the clock stop? (e.g., state = Resolved)
- Pause condition: when does time pause? (e.g., state = On Hold)
- Duration: calendar or schedule based (e.g., 4 hours, 8 business hours)

SLA Task: the actual instance created on a record when the SLA fires
Navigate: incident record → SLA tab → see active SLAs and % elapsed
```

---

## Domain 3 — Configuring Applications for Collaboration (20%) ⚑

### Knowledge Management

Knowledge bases store articles that help users self-serve and agents resolve issues faster.

```
Navigator: Knowledge → Knowledge Bases

Structure:
Knowledge Base → Categories → Articles

Article states:
Draft → Review → Published → Retired
(workflow-controlled; only Published articles visible to end users by default)
```

**Key configuration:**
| Setting | Purpose |
|---|---|
| Who can read | Roles or groups that can view articles in this KB |
| Who can contribute | Roles or groups that can submit articles |
| Article versioning | Enable to track article revision history |
| Feedback | Article rating (thumbs up/down) and comments |
| Ownership groups | Group responsible for article review and publication |

**Flagging:** Users can flag articles for review if content is outdated or incorrect.

### Service Catalog

The Service Catalog lets users request goods and services through a structured, self-service interface.

```
Structure:
Catalog → Categories → Catalog Items / Order Guides / Record Producers

Navigator: Service Catalog → Maintain Items
```

**Catalog item types:**

| Type | Purpose |
|---|---|
| **Catalog Item** | Standard requestable item with variables (e.g., "Request a Laptop") |
| **Order Guide** | Groups multiple catalog items into one guided request |
| **Record Producer** | Creates a record in any table via the Service Catalog interface |

**Variables** — the fields users fill out when requesting a catalog item:
```
Variable types: Single Line Text, Multi-line Text, Checkbox, Select Box,
                Reference, Attachment, Date, Label, UI Page, Container (layout)

Variable set: reusable group of variables — attach to multiple catalog items
```

**Approval workflow:** Catalog items can trigger approval from a manager or specific group before fulfillment.

### Reporting and Dashboards

**Creating a report:**
```
Navigator: Reports → Create New
Fields: Name, Table, Type, Conditions, Grouping, Aggregation

Report types: List, Bar, Pie, Donut, Line, Trend, Pivot, Histogram,
              Heatmap, Calendar, Map, Single Score, Gauge
```

**Sharing reports:**
- Share with specific users, groups, or roles
- Publish to a dashboard
- Schedule for email delivery (Reports → My Reports → Schedule)

**Dashboards:**
```
Navigator: Dashboards → New
- Add widgets: reports, gauges, iFrames, PA scores, data tables
- Tabs: organize widgets across multiple tabs
- Interactive filters: apply conditions across all compatible widgets simultaneously
- Share: assign view/edit access to users or groups
```

**Performance Analytics (PA):**
- Licensed add-on (not included in base platform)
- Tracks KPI indicators over time (time-series, not just snapshots)
- Requires PA license to create indicators; results visible to all users
- Key difference from reports: PA stores historical scores automatically on a schedule

---

## Domain 4 — Self-Service and Automation (~20%) ⚑

### UI Policies

UI Policies control form field behavior **declaratively** (no scripting required).

```
Navigator: System UI → UI Policies

Conditions: when the policy applies (e.g., Priority = 1 - Critical)
Actions: for each field — Make Mandatory, Make Read Only, Show/Hide

Client script: toggle to run a UI Policy script as client-side JavaScript
Run scripts: optional script block runs when policy condition is met
```

> ⚑ **Key distinction:** UI Policies are client-side, declarative, and apply to forms only. Business Rules are server-side and apply to database operations.

### Business Rules

Business Rules run **server-side** JavaScript in response to database operations.

```
Navigator: System Definition → Business Rules → New

Key fields:
- Table: which table this rule monitors
- When: Before, After, Async, Display
- Operation: Insert, Update, Delete, Query (or combinations)
- Filter conditions: additional conditions to narrow when rule fires
- Script: GlideScript executed when conditions are met

When values:
Before  → runs before the record is saved; can modify field values (use current.setValue())
After   → runs after save; record is committed; cannot change current record easily
Async   → runs in background after save; good for non-urgent tasks
Display → runs when record is displayed; used to set display variables
```

**Common Business Rule patterns:**
```javascript
// Before Insert: auto-set a field
if (current.priority == 1) {
    current.assignment_group.setDisplayValue('Critical Response Team');
}

// After Update: trigger notification or create child record
var gr = new GlideRecord('incident_task');
gr.initialize();
gr.parent = current.sys_id;
gr.short_description = 'Follow-up task for ' + current.number;
gr.insert();

// Check if field changed
if (current.state.changes() && current.state == 6) {
    // Incident moved to Resolved
}
```

### Client Scripts

Client Scripts run **browser-side** JavaScript when form events occur.

```
Navigator: System Definition → Client Scripts → New

Type (when they run):
onLoad     → when form is first loaded
onChange   → when a specific field value changes
onSubmit   → when user clicks Submit
onCellEdit → when a list cell is edited (list view only)

Table: which table/form this applies to
Field name (onChange only): which field triggers this script
```

**Client Script APIs:**
```javascript
// onLoad example — hide a field on load
function onLoad() {
    g_form.setVisible('close_notes', false);
}

// onChange example — make field mandatory based on another field
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading) return; // Don't run on initial load
    if (newValue == '1') { // Priority = Critical
        g_form.setMandatory('short_description', true);
    }
}

// Get and set field values
g_form.getValue('priority');
g_form.setValue('state', '2');
g_form.setDisplay('field_name', false);   // Hide field but keep value
g_form.setVisible('field_name', false);   // Hide field entirely
g_form.addInfoMessage('Record updated.');
g_form.addErrorMessage('Please fill in all required fields.');
```

### Script Includes

Script Includes are **reusable server-side JavaScript** classes or functions, callable from Business Rules, Flows, REST APIs, and other server-side contexts.

```
Navigator: System Definition → Script Includes → New

Key fields:
- Name: the class/function name (must match the script)
- Client callable: expose this to client scripts via GlideAjax (use with caution)
- Application: scope (Global or specific app)
- Script: the JavaScript class or function body

Standard class pattern:
var MyUtils = Class.create();
MyUtils.prototype = {
    initialize: function() {},

    getStatusLabel: function(state) {
        var labels = {1: 'New', 2: 'In Progress', 6: 'Resolved'};
        return labels[state] || 'Unknown';
    },

    type: 'MyUtils'
};

// Usage in a Business Rule:
var util = new MyUtils();
var label = util.getStatusLabel(current.state);
```

### Update Sets

Update Sets capture configuration changes for promotion between instances (dev → test → prod).

```
Navigator: System Update Sets → Local Update Sets → New

Rules:
✅ Captures: forms, fields, business rules, client scripts, UI policies, ACLs,
             notifications, catalog items, flows, script includes, etc.
❌ Does NOT capture: data records, user/group records, role assignments, sys_user data

Workflow:
1. Create and set as Current update set
2. Make your configuration changes (they are auto-captured)
3. Complete the update set
4. Export XML (or use Remote Update Set to pull on target)
5. On target instance: Preview → resolve conflicts → Commit

Preview before Commit — always. Preview identifies conflicts without applying changes.
Commit in dependency order when multiple update sets are involved.
```

**Best practices:**
- One update set per feature/story — avoid mixing unrelated changes
- Name descriptively: `INC-1234 - Add SLA for P1 Incidents`
- Never commit directly to production without testing on test/UAT first

### Flow Designer

Flow Designer is the no-code/low-code automation tool. See ServiceNow-Flow-Designer for the full reference.

**Key concepts for CSA:**
```
Trigger types: Record-based (created/updated), Schedule, Inbound Email, REST API
Actions: Create Record, Update Record, Ask for Approval, Send Email, Run Script, Call Subflow
Flow Logic: If/Else, For Each, Wait for Condition, Try/Catch

Subflows: reusable flows called from other flows (like a function)
Actions: atomic building blocks — ServiceNow provides hundreds; custom ones can be built
Data Pills: variable passing between steps (drag-and-drop in UI)
```

---

## Domain 5 — Database Management and Platform Security (30%) ⚑⚑

> **Highest-weighted domain. This is the most technical section. Expect ~18 questions.**

### Tables and Records

ServiceNow stores everything in **tables** — every module, every record, every configuration object.

```
Table     = a collection of records of the same type (like a database table)
Record    = a single row in a table
Field     = a column/attribute of a record
sys_id    = globally unique 32-character hex identifier — every record has one
```

**Accessing tables directly:**
```
Type the table name + .list in the navigator to see all records:
  incident.list       → all incident records
  sys_user.list       → all user records
  sys_user_group.list → all group records
  change_request.list → all change records

Type table name + .do to open a new blank record:
  incident.do         → new blank incident
```

### Table Inheritance

Tables can **extend** parent tables, inheriting all parent fields. This is a core ServiceNow architectural pattern.

```
Base class: Task (task)
  └── Incident (incident)
  └── Problem (problem)
  └── Change Request (change_request)
  └── sc_request (service catalog request)
  └── Incident Task (incident_task)

task table fields (number, short_description, assigned_to, state, priority, etc.)
are available on ALL child tables automatically.
```

> ⚑ Frequently tested. Know that `incident` extends `task`, and `task` is the base class for all ITSM records.

**Schema map:** Visual tool showing table relationships and inheritance.
```
Navigator: System Definition → Tables → open any table → Schema Map button
```

### Dictionary

The **Dictionary** defines field metadata — type, length, behavior — for every field on every table.

```
Navigator: System Definition → Dictionary
Or: right-click any field label on a form → Configure Dictionary

Dictionary entry fields:
- Table: which table this field belongs to
- Column name: internal name (e.g., incident.priority)
- Type: data type (see table below)
- Max length: for string fields
- Default value
- Mandatory: always required?
- Read only: non-editable?
- Choices: for Choice type fields — managed via Choices related list
```

**Field types:**

| Type | Description |
|---|---|
| String | Plain text |
| Integer | Whole number |
| Boolean | True/False checkbox |
| Date/Time | Date and time value |
| Reference | Pointer to a record in another table (stores sys_id, displays display value) |
| List | Multi-value reference |
| Choice | Dropdown — values defined in dictionary choices |
| Glide List | Multi-reference field |
| HTML | Rich text editor |
| Script | GlideScript field |
| Currency | Monetary value |
| Decimal / Float | Numeric with decimals |
| Conditions | Filter condition field |

### CMDB (Configuration Management Database)

The CMDB tracks **Configuration Items (CIs)** — servers, applications, services, databases, network devices, and more.

```
Base CI table: cmdb_ci
Common CI child tables:
  cmdb_ci_computer        → computers/servers
  cmdb_ci_appl            → applications
  cmdb_ci_service         → business services
  cmdb_ci_network_adapter → network interfaces
  cmdb_ci_ip_address      → IP addresses
```

**Relationships:** CIs are linked via Relationship records (cmdb_rel_ci):
```
CI A  --[Runs on]--> CI B
CI A  --[Depends on]--> CI C
```

**Discovery:** ServiceNow Discovery tool auto-populates the CMDB by scanning the network. It identifies CIs and their relationships automatically.

**CSDM (Common Service Data Model):** ServiceNow's framework for organizing CMDB data — defines standard classes and relationships between business services, applications, and infrastructure.

### Users, Groups, and Roles

**Users (`sys_user`):**
- Every person who accesses ServiceNow is a user record
- Key fields: User ID (login), Name, Email, Department, Manager, Active (true/false)
- `admin = true` field grants full platform access

**Groups (`sys_user_group`):**
- Collections of users
- Used for: assignment (tickets assigned to a group), approval (manager group), access control
- Members: `sys_user_grmember` (junction table)

**Roles (`sys_user_role`):**
- Grant access to modules, tables, and records
- Assigned to groups (best practice) or directly to users
- Key roles:

| Role | Access |
|---|---|
| `admin` | Full platform access |
| `itil` | Standard ITSM user — create/read/write incidents, problems, changes |
| `knowledge` | Manage knowledge articles |
| `catalog_admin` | Manage service catalog |
| `report_admin` | Manage reports and dashboards |
| `security_admin` | Required (in addition to admin) to modify ACLs in scoped apps |

> ⚑ Best practice: **assign roles to groups, never directly to users**. Manage group membership, not individual role grants.

### ACLs (Access Control Lists)

ACLs control who can **read, write, create, delete** records and fields.

```
Navigator: System Security → Access Control (ACL)

ACL structure:
- Operation: read, write, create, delete
- Object type: record (whole table) or field (specific column)
- Table: which table
- Field: which field (for field-level ACLs)
- Roles: one or more roles required
- Condition: optional GlideRecord condition
- Script: optional server-side script that returns true/false

Evaluation order:
1. Table-level ACL checked first
2. Field-level ACL checked next
3. If NO ACL matches → access DENIED (deny by default)
```

> ⚑ **Deny by default** — the most commonly tested ACL concept. If no ACL matches the operation, access is denied regardless of role.

**Testing ACLs:**
```
Profile menu (top-right) → Impersonate User → search for a user → Impersonate
Navigate to the record/form → verify field visibility and editability
Exit impersonation: Profile menu → End Impersonation
```

### ITSM Core Tables and States

| Process | Table | Purpose |
|---|---|---|
| Incident Management | `incident` | Restore normal service ASAP |
| Problem Management | `problem` | Identify and eliminate root cause |
| Change Management | `change_request` | Control change to minimize disruption |
| Request Management | `sc_request` / `sc_req_item` | Handle service requests |
| CMDB | `cmdb_ci` | Track infrastructure configuration items |

**Incident states (numeric values — memorize these):**
```
1 = New
2 = In Progress
3 = On Hold
6 = Resolved
7 = Closed
```

**Change types:**
```
Standard   → Pre-approved, low-risk, repeatable — no CAB required
Normal     → Planned change — requires CAB review and approval
Emergency  → Urgent — expedited approval process, ECAB
```

---

## Domain 6 — Data Migration and Integration (13%)

### Import Sets

Import Sets provide a **staging area** for loading data from external sources into ServiceNow.

```
Workflow:
1. Load data  →  Import Set Table (staging)
2. Transform  →  Target Table (via Transform Map)

Load methods:
- CSV / Excel file upload
- JDBC connection (database)
- REST API inbound
- Scheduled import (recurring)

Navigator: System Import Sets → Load Data
```

### Transform Maps

Transform Maps define **how fields in the import set map to fields in the target table**.

```
Navigator: System Import Sets → Transform Maps → New

Key fields:
- Name
- Source table: the import set table
- Target table: the ServiceNow table to write to (e.g., incident, sys_user)
- Field maps: map each source column to a target field

Coalesce field:
The "match" key. If a record already exists with this value → UPDATE it.
If no match found → INSERT a new record.
Example: coalesce on 'email' → if user with this email exists, update; else create new.

Transform scripts:
Run script on each row during transform:
  answer = source.source_field_name;   // read from import set
  target.setValue('field', answer);     // write to target
```

### REST and Web Services Integration

```
Outbound REST (ServiceNow calls external system):
Navigator: System Web Services → Outbound → REST Messages

Inbound REST (external system calls ServiceNow):
ServiceNow Table API: POST/GET/PUT/PATCH/DELETE on any table
  https://instance.service-now.com/api/now/table/{table}
  Authentication: Basic auth or OAuth 2.0

Scripted REST APIs: custom REST endpoints built in ServiceNow
Navigator: System Web Services → Scripted REST APIs

MID Server: middleware agent running on-premise that connects
  ServiceNow cloud to internal systems/databases behind a firewall
```

**Key integration tables:**
| Table | Purpose |
|---|---|
| `sys_rest_message` | Outbound REST message definitions |
| `sys_web_service` | SOAP web service definitions |
| `ecc_queue` | MID Server communication queue |

---

## Exam Tips — Key Rules to Memorize

| Rule | Detail |
|---|---|
| Deny by default | No matching ACL = access denied |
| Roles → groups → users | Always assign roles to groups, groups to users |
| Update sets = config only | Data records do NOT move in update sets |
| Preview before Commit | Always preview an update set before committing |
| Plugins can't be deactivated | Test on dev before activating on production |
| UI Policy = client-side | Business Rule = server-side |
| Before BR = can modify values | After BR = record already saved |
| Table inheritance | `incident` extends `task` — task fields available on incident |
| Coalesce = match key | Coalesce field determines insert vs update in transform |
| Incident states | 1=New, 2=In Progress, 3=On Hold, 6=Resolved, 7=Closed |
| Passing threshold | ~70–75% — ServiceNow does not publish the exact number |

---

## Study Plan

| Week | Focus |
|---|---|
| 1 | Complete ServiceNow Administration Fundamentals on Now Learning (free, required) |
| 2 | Get a Personal Developer Instance (PDI) at developer.servicenow.com — explore UI, tables, lists, forms |
| 3 | Hands-on: Build Business Rules, Client Scripts, UI Policies, Notifications on your PDI |
| 4 | Hands-on: Create Update Sets, migrate between instances, activate plugins, build catalog items |
| 5 | Study: ACLs, Database schema, Table inheritance, Import Sets, Transform Maps |
| 6 | Practice exams + review Domain 5 (Database Management and Platform Security) deeply — it's 30% of the exam |

**Personal Developer Instance (PDI):** Free at https://developer.servicenow.com — hibernates after 10 days of inactivity. Request a new one or wake it up from the portal.

---

## Study Resources

| Resource | Notes |
|---|---|
| **Now Learning** | https://nowlearning.servicenow.com — ServiceNow Administration Fundamentals (required); free |
| **CSA Exam Blueprint** | https://nowlearning.servicenow.com/kb?id=kb_article_view&sysparm_article=KB0011554 |
| **Personal Developer Instance** | https://developer.servicenow.com — essential for hands-on practice |
| **ServiceNow Docs** | https://docs.servicenow.com — always match docs to your PDI's release version |
| **ServiceNow Community** | https://www.servicenow.com/community/ — Training & Certifications forum has real exam tips |
| **Chuck Tomasi — SN Pro Tips** | https://snprotips.com — multi-year Community MVP; practical and accurate |
| **Books (Packt)** | *Learning ServiceNow* and *Mastering ServiceNow Scripting* — Chuck Tomasi |

---

## Related Notes
- ServiceNow-Flow-Designer — Full Flow Designer reference (Domain 4)
- ServiceNow-JavaScript — JavaScript fundamentals for Business Rules and Client Scripts
- ServiceNow-ATF-Micro-Cert — ATF study guide (test your ServiceNow builds)
- ServiceNow-Micro-Cert-Welcome ✅ — Platform fundamentals (Domain 1)
- ServiceNow-Micro-Cert-Flows ✅ — Flows (Domain 4)
- ServiceNow-Micro-Cert-Platform-Analytics ✅ — Reporting and dashboards (Domain 3)
- ServiceNow-Micro-Cert-ATF ✅ — ATF (testing, Domain 4)
- Active-Directory — LDAP/AD integration with ServiceNow is a common integration topic

---

## References
- ServiceNow CSA Exam Blueprint (KB0011554) — updated January 2026: https://nowlearning.servicenow.com/kb?id=kb_article_view&sysparm_article=KB0011554
- ServiceNow Official Documentation: https://docs.servicenow.com
- ServiceNow Developer Portal: https://developer.servicenow.com
- Chuck Tomasi, *Learning ServiceNow*, Packt Publishing
- Chuck Tomasi, *Mastering ServiceNow Scripting*, Packt Publishing

---

## GlideRecord API — Server-Side Database Scripting

> Belongs conceptually with Domain 5 (Database Management and Platform Security). Added here due to document structure constraints.
> GlideRecord is the most commonly tested scripting topic in the CSA exam — expect 2–3 questions.

GlideRecord is the primary server-side JavaScript API for querying and modifying database records. Used in Business Rules, Script Includes, and Scheduled Jobs.

```javascript
// --- Query multiple records ---
var gr = new GlideRecord('incident');
gr.addQuery('state', 1);               // state = 1 (New)
gr.addQuery('priority', '<=', 2);      // priority <= 2
gr.orderByDesc('priority');            // sort descending
gr.setLimit(10);                       // cap results
gr.query();                            // execute

while (gr.next()) {
    gs.log(gr.number + ': ' + gr.short_description);
    gr.state = 2;
    gr.update();                       // save changes
}

// --- Get a single record by sys_id ---
var gr = new GlideRecord('incident');
if (gr.get('sys_id_value')) {
    gs.log('Found: ' + gr.number);
}

// --- Insert a new record ---
var gr = new GlideRecord('incident');
gr.initialize();
gr.short_description = 'Auto-created incident';
gr.caller_id.setDisplayValue('Abel Tuter');
gr.insert();

// --- Delete records ---
var gr = new GlideRecord('incident');
gr.addQuery('state', 7);
gr.query();
while (gr.next()) { gr.deleteRecord(); }

// --- Field change detection (Business Rules only) ---
if (current.state.changes()) { }          // changed at all?
if (current.state.changesFrom(1)) { }     // changed FROM New?
if (current.state.changesTo(6)) { }       // changed TO Resolved?
```

**Key GlideRecord methods:**

| Method | Purpose |
|--------|---------|
| `.addQuery(field, value)` | Filter condition (equality) |
| `.addQuery(field, operator, value)` | Filter with operator: `=` `!=` `>` `<` `CONTAINS` `STARTSWITH` |
| `.addEncodedQuery(string)` | Full encoded query string (copy from list URL) |
| `.addOrCondition(field, value)` | OR condition chained to last addQuery |
| `.query()` | Execute the query |
| `.next()` | Advance to next record; returns true/false |
| `.get(sys_id)` | Retrieve single record by sys_id |
| `.initialize()` | Prepare blank record for insert |
| `.insert()` | Insert new record; returns sys_id |
| `.update()` | Save changes to current record |
| `.deleteRecord()` | Delete current record |
| `.getRowCount()` | Count matching records |
| `.setValue(field, value)` | Set field by string name |
| `.getValue(field)` | Get raw stored value |
| `.getDisplayValue(field)` | Get display value (use for Reference fields) |
| `.changes()` | True if this field was modified this operation |

> [VERIFY: GlideRecord is server-side only. Use GlideAjax + Script Include to access server data from a Client Script.]

---

## Application Menus and Modules (Domain 2 Addition)

> Belongs conceptually with Domain 2 (Instance Configuration). Added here due to document structure constraints.

**Application Menus** define what appears in the left-hand Application Navigator. **Modules** are the individual links within each application menu.

```
Navigator: System Definition -> Application Menus
Navigator: System Definition -> Modules

Application Menu:
- Title: the name shown in the navigator (e.g., "Incident")
- Order: controls sort order in the navigator list
- Roles: which roles can see this menu
- Active: toggle visibility

Module:
- Title: the link name (e.g., "Create New", "Open", "All")
- Application: which menu this belongs to
- Link type: the type of record/action this opens:
    - List of Records  -> opens a table list view
    - New Record       -> opens a blank form
    - URL              -> external link
    - Separator        -> visual divider in menu
- Roles: which roles can see this module
- Order: sort position within the menu
```

**Creating a custom application menu for a new app:**
1. System Definition -> Application Menus -> New
2. Fill in title, order, active = true, assign roles
3. Add modules by navigating to the menu record -> Modules related list

> [VERIFY: Changes to Application Menus and Modules are captured in Update Sets.]

---

## Practice Exam Focus Areas

> High-frequency question patterns by domain. Use this section for final review before your exam.

### Domain 1 Focus (7%) — Platform and Navigation
- **Next Experience UI elements:** Know every element of the navigation bar (Application Navigator, All menu, Favorites, History, Workspaces, Global Search)
- **List vs Form view distinctions:** What you can do in each, how to personalize columns, inline editing
- **Three-instance model:** Know dev -> test -> prod and that changes move via Update Sets
- **Instance URL format:** `[instance-name].service-now.com`

**Likely question type:** "Which navigation element allows you to quickly access recently viewed records?" → History tab

---

### Domain 2 Focus (10%) — Instance Configuration
- **Notifications:** Know all four key fields (Table, When to send, Conditions, Who receives)
- **SLAs:** Know Start / Stop / Pause conditions; know what an SLA Task is; know breach = when elapsed % hits 100
- **System Properties:** Know how to find them (`sys_properties.list`) and what they control
- **Plugins:** Know they cannot be deactivated once activated in production
- **Branding:** Know where to configure logo, color, browser title

**Likely question type:** "What happens when an SLA reaches 100% elapsed?" → It is considered breached; the SLA Task state changes to Breached

**Trick:** SLA pause condition suspends the clock — time does not count while paused (e.g., On Hold state)

---

### Domain 3 Focus (20%) — Collaboration

**Knowledge Management:**
- Know the article states in order: Draft -> Review -> Published -> Retired
- Only Published articles are visible to end users by default
- Know who can read vs contribute (configured per Knowledge Base, not per article)
- Flagging = marking an article for review (does NOT remove it from view)

**Service Catalog:**
- Know the three catalog item types: Catalog Item, Order Guide, Record Producer
- Know variable types — especially Reference and Variable Set
- Variable Sets = reusable groups of variables attached to multiple items

**Reporting:**
- Know all report types: List, Bar, Pie, Line, Trend, Pivot, Single Score, Gauge, Heatmap, Calendar, Map
- Reports can be scheduled for email delivery
- PA (Performance Analytics) is a LICENSED add-on; tracks KPIs over time (time-series)

**Dashboards:**
- Interactive filters apply across all compatible widgets simultaneously
- Widgets can be reports, gauges, iFrames, or PA scores

**Likely question type:** "What type of catalog item creates a record in a table other than sc_request?" → Record Producer

**Trick:** Performance Analytics is NOT included in the base platform. It requires a license. Regular reports are included.

---

### Domain 4 Focus (20%) — Self-Service and Automation

**UI Policy vs Business Rule — the #1 most tested distinction:**

| | UI Policy | Business Rule |
|---|---|---|
| Where it runs | Client-side (browser) | Server-side (database) |
| When | Form events (load, change) | DB operations (insert, update, delete) |
| What it can do | Mandatory, Read Only, Show/Hide | Any GlideScript logic |
| Scripting required? | Optional | Yes (GlideScript) |

**Business Rule When values — memorize all four:**
- Before: runs before save; CAN modify `current` field values
- After: runs after save; record committed; cannot easily change `current`
- Async: runs in background; no impact on user response time
- Display: runs when form displayed; populates display variables

**Client Script Types — memorize all four:**
- onLoad: fires when form first loads
- onChange: fires when a specific field changes
- onSubmit: fires when user submits the form
- onCellEdit: fires when a list cell is inline-edited

**Update Sets:**
- Captures: configuration (business rules, UI policies, catalog items, flows, scripts, forms, etc.)
- Does NOT capture: data records, user/group/role assignments, `sys_user` data
- Always Preview before Commit — Preview identifies conflicts without applying

**Likely question type:** "Which Business Rule 'when' value allows you to modify a field value before the record is saved?" → Before

**Trick question:** "Which of the following IS captured in an Update Set?" → Watch for answers that include user records or data — those are NOT captured.

---

### Domain 5 Focus (30%) — Database Management and Platform Security — PRIORITIZE THIS

**Table inheritance:**
- `task` is the base class; `incident`, `problem`, `change_request`, `sc_request` all extend it
- Child tables inherit ALL parent fields
- A Business Rule on `task` applies to ALL child tables

**ACLs — most tested rule: Deny by Default**
- If no ACL matches the operation -> access is DENIED
- Field-level ACLs layer on top of table-level ACLs; both must pass
- Impersonation is the correct way to test ACL behavior

**Users, Groups, Roles — best practice rule:**
- Assign roles to groups. Add users to groups. NEVER assign roles directly to users.

**Dictionary:**
- Right-click any field label on a form -> "Configure Dictionary" = fastest way to reach it
- Choices for a Choice-type field are managed in the Dictionary Choices related list

**Incident state codes — memorize:**
```
1 = New  |  2 = In Progress  |  3 = On Hold  |  6 = Resolved  |  7 = Closed
```

**GlideRecord:**
- `.next()` must be called in a while loop for multi-record queries
- `.get()` used for single record retrieval
- `.changes()` only valid inside Business Rules (checks if field changed this operation)

**Likely question type:** "A user attempts to read a record but no ACL exists for that operation. What happens?" → Access is DENIED (deny by default)

**Trick:** `security_admin` role is required IN ADDITION to `admin` to modify ACLs in scoped apps. Admin alone is not enough.

---

### Domain 6 Focus (13%) — Data Migration and Integration

**Import Sets — the coalesce field is the most tested concept:**
- Coalesce field = the matching key
- If a record with that value exists -> UPDATE it
- If no match -> INSERT a new record
- Example: coalesce on email -> if user with that email exists, update; else create

**Transform Maps:**
- Source table = the import set staging table
- Target table = the actual ServiceNow table (incident, sys_user, etc.)
- Field maps connect source columns to target fields

**REST API:**
- Table API URL format: `https://[instance].service-now.com/api/now/table/[table_name]`
- Supports: GET (read), POST (create), PUT (full update), PATCH (partial update), DELETE
- Auth: Basic or OAuth 2.0

**MID Server:**
- On-premise agent that bridges ServiceNow cloud to internal systems behind a firewall
- Required for Discovery and JDBC connections to on-premise databases

**Likely question type:** "During a data import, what determines whether a record is inserted or updated?" → The coalesce field

---

## Enhanced Study Schedule

> More targeted than the 6-week plan above — prioritized by exam domain weight.

### Domain Weight Priority Order (study in this order)

```
1. Domain 5 — Database Management and Platform Security (30%)  <- Start here; most questions
2. Domain 3 — Collaboration (20%)                              <- Second priority
3. Domain 4 — Self-Service/Automation (20%)                    <- Third priority
4. Domain 6 — Data Migration (13%)                             <- Fourth
5. Domain 2 — Instance Configuration (10%)                     <- Fifth
6. Domain 1 — Platform/Navigation (7%)                         <- Lightest; review last
```

### PDI Hands-On Practice Checklist

Complete each of these on your Personal Developer Instance before exam day:

**Domain 5 — Database:**
- [ ] Navigate to `incident.list`, `sys_user.list`, `cmdb_ci.list` directly
- [ ] Open Dictionary for 3 different field types; create a new Choice field
- [ ] View Schema Map for the `incident` table; trace inheritance to `task`
- [ ] Create an ACL; test it by impersonating a user without the required role
- [ ] Add a user to a group; assign a role to the group

**Domain 3 — Collaboration:**
- [ ] Create a Knowledge Base with categories; publish one article
- [ ] Create a Catalog Item with 3 variable types; test ordering it
- [ ] Build a Bar chart report on incident table; add it to a Dashboard
- [ ] Configure an Interactive Filter on a Dashboard

**Domain 4 — Automation:**
- [ ] Create a Before Business Rule that sets a field value on insert
- [ ] Create an onChange Client Script that makes a field mandatory
- [ ] Create a UI Policy that hides a field based on a condition
- [ ] Create an Update Set; make a change; export it; preview it

**Domain 6 — Integration:**
- [ ] Load a CSV file via Import Sets; create a Transform Map; run the transform
- [ ] Find the `ecc_queue` table and understand MID Server communication

**Domain 2 — Configuration:**
- [ ] Create a Notification triggered by incident state change
- [ ] View and modify a System Property
- [ ] Create an SLA Definition with start/stop/pause conditions

### Exam Day Prep

**One week before:**
- Review the Practice Exam Focus Areas section (above) for each domain
- Focus extra time on Domain 5 (ACLs, table inheritance, GlideRecord)
- Do at least 2 full practice exam runs (see Resources)

**Day before:**
- Review the Exam Tips — Key Rules to Memorize table only
- Do NOT start new material the day before
- Confirm Pearson VUE appointment, test your equipment (camera, mic, ID)
- Prepare: quiet room, valid government ID, clear desk

**Exam day:**
- 60 questions, 90 minutes — that is 90 seconds per question
- Flag and skip anything uncertain; come back at the end
- For "which is the BEST answer" questions: eliminate clearly wrong answers first, then pick most precise remaining answer
- ServiceNow's preferred answers align with the platform's built-in tools (e.g., use UI Policy before Business Rule for client-side field control)

**Practice exam resources:**
- ServiceNow Now Learning — official practice assessments (free with account)
- ExamTopics.com — community-sourced CSA questions (verify answers against docs)
- ServiceNow Community — Training & Certifications forum — search "CSA study guide"

---

## Correction History

### S11 Fact-Check (2026-04-03) — Applied ✅
- **Exam cost:** Corrected from `~$350` to `$300` — confirmed via ServiceNow community sources
- **2026 process change:** Voucher model retired; exams now via ServiceNow University + Pearson VUE
- **Domain 1 weight:** Flagged as `[VERIFY]` — 7% (note) vs 6% (Udemy source)
- **Retake policy:** Flagged as `[VERIFY]` — "14-day wait" noted as potentially changed

### S12 Fact-Check (2026-04-04) — Applied ✅
- **Domain 1 weight RESOLVED:** Official blueprint KB0011554 confirms **7%** — Udemy source was wrong
- **Domain 2 weight:** Corrected from 11% → **10%**
- **Domain 5 weight:** Corrected from ~27% → **30%**; name corrected to **"Database Management and Platform Security"**
- **Domain 6 weight:** Corrected from ~15% → **13%**
- **Retake policy RESOLVED:** Official tiered policy confirmed — 3-day / 30-day / 6-month; retake fee $150

### S14 (2026-04-04) — Overview table updated ✅
All S11/S12 corrections applied to the Overview table and Domain table at the top of this note. Corrections sections preserved as history.

---

## Active Study Plan

- [[CSA-Study-Plan]] — 17-day sprint schedule (April 4–20, 2026) — daily tasks + PDI checklist

---
<- [[Certs-MOC]]