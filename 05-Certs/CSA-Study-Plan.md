---
title: CSA Study Plan — 17-Day Sprint (Apr 4–20, 2026)
tags:
  - cert
  - servicenow
  - CSA
  - study-plan
  - active
topic: Certification Study
difficulty: intermediate
created: 2026-04-04
updated: 2026-04-04
source: ServiceNow CSA Exam Blueprint KB0011554 — domain weights and priorities
---

# 📅 CSA Study Plan — 17-Day Sprint

> **Exam target:** Before or shortly after University of Phoenix IAS Certificate starts (April 21, 2026)
> **Study time:** ~2–3 hours/day
> **Pivot point:** April 21 — UoP begins; vault becomes primary reference library for coursework

---

## How to Use This Note

- Tick checkboxes (`- [x]`) as you complete each task in Obsidian
- Each day has a **Read** task (theory from [[ServiceNow-CSA]]) and a **Do** task (hands-on PDI)
- Days 1–5 are heaviest — Domain 5 is 30% of the exam; front-load it
- Days 15–17 are practice exam and review only — no new material
- Your PDI: https://developer.servicenow.com — wake it up if hibernating before Day 1

---

## Domain Priority Reference

| Priority | Domain | Weight | Days |
|---|---|---|---|
| 1 | Domain 5 — Database Management & Platform Security | 30% | Days 1–5 |
| 2 | Domain 3 — Configuring Applications for Collaboration | 20% | Days 6–8 |
| 3 | Domain 4 — Self-Service and Automation | 20% | Days 9–11 |
| 4 | Domain 6 — Data Migration and Integration | 13% | Days 12–13 |
| 5 | Domain 2 — Instance Configuration | 10% | Day 14 |
| 6 | Domain 1 — Platform Overview and Navigation | 7% | Day 15 |
| — | Practice Exams + Full Review | — | Days 16–17 |

---

## Week 1 — Domain 5: Database, Security, Tables (April 4–8)

> **Goal:** Master the 30% domain. ACLs, table inheritance, GlideRecord, users/groups/roles, CMDB. These are the most-tested topics on the exam.

### Day 1 — Saturday April 4 — Tables, Records, sys_id

**Read:**
- [ ] CSA note → Domain 5 → "Tables and Records" section
- [ ] CSA note → Domain 5 → "Table Inheritance" section
- [ ] Understand: task → incident/problem/change_request inheritance chain

**Do (PDI):**
- [ ] Navigate directly: type `incident.list`, `sys_user.list`, `cmdb_ci.list` in the navigator
- [ ] Open any Incident record — note `sys_id` in the URL
- [ ] Open Schema Map for `incident` table → trace inheritance to `task`
- [ ] Find and open a `task` record — confirm shared fields appear on `incident`

---

### Day 2 — Sunday April 5 — Dictionary and Field Types

**Read:**
- [ ] CSA note → Domain 5 → "Dictionary" section
- [ ] Review the Field Types table — know all 12+ types cold
- [ ] CSA note → Domain 5 Practice Focus — "Dictionary" and "Incident state codes"

**Do (PDI):**
- [ ] Right-click a field label on an Incident form → "Configure Dictionary"
- [ ] Open Dictionary (`sys_dictionary.list`) — explore 5 different field types
- [ ] Create a new Choice field on the `incident` table (on your PDI only — use a `u_` prefix)
- [ ] Add 3 choices to it; view the result on the form

---

### Day 3 — Monday April 6 — Users, Groups, Roles

**Read:**
- [ ] CSA note → Domain 5 → "Users, Groups, and Roles" section
- [ ] Memorize: assign roles to groups → groups to users (never direct role-to-user)
- [ ] Know key roles: `admin`, `itil`, `catalog_admin`, `report_admin`, `security_admin`

**Do (PDI):**
- [ ] Create a test user (`sys_user.do`) — set active = true
- [ ] Create a test group (`sys_user_group.do`) — add your test user as a member
- [ ] Assign the `itil` role to the group
- [ ] Log in as your test user (impersonate) — confirm Incident module appears; confirm admin functions are missing

---

### Day 4 — Tuesday April 7 — ACLs

**Read:**
- [ ] CSA note → Domain 5 → "ACLs (Access Control Lists)" section
- [ ] **Memorize:** Deny by default — no matching ACL = access DENIED
- [ ] Know: table-level ACL checked first; field-level ACL second; both must pass
- [ ] CSA note → Exam Tips table — review all ACL rows

**Do (PDI):**
- [ ] Navigate: System Security → Access Control (ACL)
- [ ] Find the `incident` table read ACL — review its role requirements
- [ ] Create a test field-level ACL that restricts a field to `itil_admin` role only
- [ ] Impersonate your test user (itil only) — confirm the restricted field is hidden
- [ ] End impersonation — confirm field is visible again as admin

---

### Day 5 — Wednesday April 8 — GlideRecord + CMDB + Incident States

**Read:**
- [ ] CSA note → "GlideRecord API" section (full reference at bottom of note)
- [ ] CSA note → Domain 5 → "CMDB" section
- [ ] Memorize incident state codes: 1=New, 2=In Progress, 3=On Hold, 6=Resolved, 7=Closed
- [ ] CSA note → Domain 5 Practice Focus (full section) — review all likely question types

**Do (PDI):**
- [ ] Open a Background Script (`sys.scripts.do`) — run a GlideRecord query:
  ```javascript
  var gr = new GlideRecord('incident');
  gr.addQuery('state', 1);
  gr.query();
  while (gr.next()) { gs.log(gr.number + ': ' + gr.short_description); }
  ```
- [ ] Modify the query to filter by priority; run again
- [ ] Navigate: Configuration → CMDB Dashboard — review CI classes listed
- [ ] Open `cmdb_ci_computer.list` — examine 3 CI records and their fields

---

## Week 2 — Domains 3, 4, 6 (April 9–15)

### Day 6 — Thursday April 9 — Knowledge Management

**Read:**
- [ ] CSA note → Domain 3 → "Knowledge Management" section
- [ ] Know article state flow: Draft → Review → Published → Retired
- [ ] Know: only Published articles visible to end users by default
- [ ] Know: flagging ≠ removing — flagging marks for review only

**Do (PDI):**
- [ ] Create a Knowledge Base with 2 categories
- [ ] Create an article in Draft state; publish it
- [ ] Log in as a non-admin user (impersonate) — verify article is visible
- [ ] Flag the article — confirm it stays visible

---

### Day 7 — Friday April 10 — Service Catalog

**Read:**
- [ ] CSA note → Domain 3 → "Service Catalog" section
- [ ] Memorize the 3 catalog item types: Catalog Item, Order Guide, Record Producer
- [ ] Know variable types; know what a Variable Set is
- [ ] CSA note → Domain 3 Practice Focus — "Record Producer" trick question

**Do (PDI):**
- [ ] Create a Catalog Item with: 1 text variable, 1 reference variable, 1 checkbox variable
- [ ] Order the item as a test user — verify the sc_request record is created
- [ ] Create a Variable Set with 2 variables; attach it to your catalog item
- [ ] Create a Record Producer that creates an Incident record

---

### Day 8 — Saturday April 11 — Reporting, Dashboards, Performance Analytics

**Read:**
- [ ] CSA note → Domain 3 → "Reporting and Dashboards" section
- [ ] Know all report types (List, Bar, Pie, Line, Trend, Pivot, Single Score, Gauge, Heatmap, Calendar, Map)
- [ ] Key distinction: PA requires a license; regular reports do not
- [ ] Know: Interactive Filters apply across all compatible dashboard widgets simultaneously

**Do (PDI):**
- [ ] Create a Bar chart report on `incident` table grouped by Priority
- [ ] Create a Pie chart report grouped by Assignment Group
- [ ] Create a Dashboard; add both reports as widgets
- [ ] Add an Interactive Filter to the Dashboard — test that it filters both widgets
- [ ] Schedule one report for email delivery

---

### Day 9 — Sunday April 12 — UI Policies and Business Rules

**Read:**
- [ ] CSA note → Domain 4 → "UI Policies" section
- [ ] CSA note → Domain 4 → "Business Rules" section
- [ ] **Memorize the table:** UI Policy = client-side/declarative; Business Rule = server-side/scripted
- [ ] Know all 4 Business Rule "when" values and what each can do

**Do (PDI):**
- [ ] Create a UI Policy: if Priority = 1 (Critical), make Short Description mandatory and Close Notes visible
- [ ] Test it by opening an Incident and setting Priority to Critical
- [ ] Create a Before Business Rule on `incident`: if state changes to Resolved, set Close Notes to "Auto-closed"
- [ ] Test by resolving an incident — verify the field is auto-populated

---

### Day 10 — Monday April 13 — Client Scripts and Script Includes

**Read:**
- [ ] CSA note → Domain 4 → "Client Scripts" section
- [ ] Know all 4 Client Script types: onLoad, onChange, onSubmit, onCellEdit
- [ ] CSA note → Domain 4 → "Script Includes" section
- [ ] Know the Class.create() pattern and how to call a Script Include

**Do (PDI):**
- [ ] Create an onLoad Client Script: hide the "Close Notes" field when form loads
- [ ] Create an onChange Client Script: when Category changes to "Software", make Subcategory mandatory
- [ ] Create a Script Include with one method that counts open incidents for a group
- [ ] Call it from a Background Script to verify it works

---

### Day 11 — Tuesday April 14 — Update Sets and Flow Designer

**Read:**
- [ ] CSA note → Domain 4 → "Update Sets" section
- [ ] Know what IS and IS NOT captured in an update set (data records are NOT captured)
- [ ] Always Preview before Commit — know why (conflict detection)
- [ ] CSA note → Domain 4 → "Flow Designer" key concepts for CSA

**Do (PDI):**
- [ ] Create a new Update Set; set it as current
- [ ] Make 3 changes (a UI Policy, a Notification, a Catalog Item)
- [ ] Complete the update set; export as XML
- [ ] Preview the update set on the same instance — review what was captured
- [ ] In Flow Designer: create a simple flow triggered by Incident creation that sends an email

---

### Day 12 — Wednesday April 15 — Import Sets and Transform Maps

**Read:**
- [ ] CSA note → Domain 6 → "Import Sets" section
- [ ] CSA note → Domain 6 → "Transform Maps" section
- [ ] **Memorize the Coalesce field:** match key → UPDATE if exists, INSERT if not
- [ ] Know import methods: CSV/Excel upload, JDBC, REST, Scheduled

**Do (PDI):**
- [ ] Create a CSV with 5 fake user records (name, email, department)
- [ ] Load it via System Import Sets → Load Data
- [ ] Create a Transform Map: source = import set table, target = `sys_user`
- [ ] Set coalesce field = email
- [ ] Run the transform — verify user records created or updated in `sys_user`

---

### Day 13 — Thursday April 16 — REST Integration and MID Server

**Read:**
- [ ] CSA note → Domain 6 → "REST and Web Services Integration" section
- [ ] Know the Table API URL format cold: `https://[instance].service-now.com/api/now/table/[table]`
- [ ] Know the 5 HTTP methods: GET, POST, PUT, PATCH, DELETE
- [ ] Know MID Server purpose: on-premise agent bridging ServiceNow cloud to internal systems

**Do (PDI):**
- [ ] Use the ServiceNow REST API Explorer (in instance → REST API Explorer) to make a GET call to the `incident` table
- [ ] Make a POST call to create a test incident via the REST API
- [ ] Review the `ecc_queue` table — understand MID Server communication queue
- [ ] Navigate System Web Services → Outbound REST Messages — review an existing definition

---

### Day 14 — Friday April 17 — Domain 2: Instance Configuration

**Read:**
- [ ] CSA note → Domain 2 → "Applications and Plugins" section
- [ ] CSA note → Domain 2 → "Notifications" section (know all 4 key fields)
- [ ] CSA note → Domain 2 → "Service Level Agreements" section
- [ ] CSA note → "Application Menus and Modules" section
- [ ] Know: plugins cannot be deactivated in production once activated

**Do (PDI):**
- [ ] Create a Notification triggered when an Incident is resolved — email the Caller
- [ ] Create an SLA Definition on `incident`: P1 = 4-hour resolution; pause on On Hold; stop on Resolved
- [ ] Find and modify a System Property (e.g., set the browser tab title)
- [ ] Create a custom Application Menu with one module linking to `incident.list`

---

### Day 15 — Saturday April 18 — Domain 1: Navigation + Full Note Review

**Read:**
- [ ] CSA note → Domain 1 → complete section (lightest domain — 7%)
- [ ] Know every Next Experience navigation element: Unified Nav bar, Application Navigator, All menu, Favorites, History, Workspaces, Global Search, User menu
- [ ] CSA note → "Exam Tips — Key Rules to Memorize" table — read all 10 rules aloud
- [ ] CSA note → Domain 1 Practice Focus section

**Do (PDI):**
- [ ] Spend 30 minutes navigating the UI only — use Favorites, History, Workspaces, Global Search
- [ ] Impersonate 3 different users; observe what changes in the navigator for each role
- [ ] Review your own PDI changes from the past 14 days — list everything you built

**Review:**
- [ ] Re-read the Exam Tips — Key Rules to Memorize table one more time
- [ ] Identify your 3 weakest areas from the past 2 weeks; flag them for Day 16 review

---

## Final Push — Practice Exams (April 19–20)

### Day 16 — Sunday April 19 — First Full Practice Exam

**Before:**
- [ ] Review your 3 flagged weak areas from Day 15

**Practice exam:**
- [ ] Complete one full practice exam (Now Learning practice assessment — free)
- [ ] Also try: ExamTopics.com CSA questions (verify answers against the CSA note)
- [ ] Target: 70%+ before moving on; if below 65%, identify failing domains

**After:**
- [ ] Score by domain — which domain had the most wrong answers?
- [ ] Re-read that domain's section in [[ServiceNow-CSA]] (focus: Practice Focus subsection only)
- [ ] Re-do any PDI tasks from that domain if time allows

---

### Day 17 — Monday April 20 — Final Review + Exam Day Prep

> Last day before UoP starts. Keep it light — no new material.

**Morning:**
- [ ] Read "Exam Tips — Key Rules to Memorize" one final time
- [ ] Review incident state codes: 1/2/3/6/7
- [ ] Review the domain weight table at the top of this note
- [ ] Do 20 practice questions only — stop at 20

**Afternoon:**
- [ ] Confirm Pearson VUE appointment (online proctored)
- [ ] Test your webcam, microphone, and valid government ID
- [ ] Clear your desk — no second monitors, no notes, no phones
- [ ] Check your ServiceNow PDI is awake and accessible

**Evening:**
- [ ] Rest. Do not study. You're ready.

---

## April 21 — UoP Information Assurance & Security Certificate Begins

> The vault pivots from CSA prep to academic reference. Your notes map directly to the IAS curriculum:

| UoP IAS Topic Area | Vault Notes |
|---|---|
| Security fundamentals | CIA-Triad, Cryptography-Basics, OSI-Model |
| Network security | Networking-Basics, TCP-IP-Fundamentals, Wireless-Security |
| Threat landscape | Threat-Intelligence, Malware-Analysis, Social-Engineering |
| Vulnerability management | Vulnerability-Management, OWASP-Top-10 |
| Incident response | Incident-Response, Digital-Forensics |
| Access control | Active-Directory, Linux-Privilege-Escalation, Windows-Privilege-Escalation |
| Cloud security | Cloud-Security-Fundamentals, Container-Docker-Security |
| Compliance & risk | HIPAA-Technical-Safeguards, FDA-Cybersecurity-Guidance |
| Tools & labs | Entire `02-Tools/` and `03-Labs/` sections |

**When UoP coursework assigns reading or topics:** search the vault first — chances are the note already exists. If a topic is missing, that's what the next session is for.

---

## Progress Tracker

| Day | Date | Domain | Read ✅ | PDI ✅ | Notes |
|---|---|---|---|---|---|
| 1 | Apr 4 | D5 — Tables & Inheritance | | | |
| 2 | Apr 5 | D5 — Dictionary & Field Types | | | |
| 3 | Apr 6 | D5 — Users, Groups, Roles | | | |
| 4 | Apr 7 | D5 — ACLs | | | |
| 5 | Apr 8 | D5 — GlideRecord + CMDB + States | | | |
| 6 | Apr 9 | D3 — Knowledge Management | | | |
| 7 | Apr 10 | D3 — Service Catalog | | | |
| 8 | Apr 11 | D3 — Reporting & Dashboards | | | |
| 9 | Apr 12 | D4 — UI Policies & Business Rules | | | |
| 10 | Apr 13 | D4 — Client Scripts & Script Includes | | | |
| 11 | Apr 14 | D4 — Update Sets & Flow Designer | | | |
| 12 | Apr 15 | D6 — Import Sets & Transform Maps | | | |
| 13 | Apr 16 | D6 — REST & MID Server | | | |
| 14 | Apr 17 | D2 — Instance Configuration | | | |
| 15 | Apr 18 | D1 — Navigation + Full Review | | | |
| 16 | Apr 19 | Practice Exam #1 | | | |
| 17 | Apr 20 | Final Review + Exam Prep | | | |

---

*Created: 2026-04-04 | Sprint window: April 4–20, 2026 | Linked: [[ServiceNow-CSA]]*

---
<- [[Certs-MOC]]