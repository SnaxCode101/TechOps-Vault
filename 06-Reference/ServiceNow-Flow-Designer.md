---
title: ServiceNow — Flow Designer & Flow Logic
type: reference
category: platform
date: 2026-03-25
tags:
  - servicenow
  - flow-designer
  - automation
  - itsm
  - workflow
  - low-code
  - security-operations
  - intermediate
difficulty: intermediate
---

# ServiceNow — Flow Designer & Flow Logic

## What is ServiceNow?

ServiceNow is a cloud-based platform-as-a-service (PaaS) that provides IT Service Management (ITSM), IT Operations Management (ITOM), IT Asset Management (ITAM), and Security Operations (SecOps) capabilities through a unified data model. All records live in the ServiceNow CMDB and are accessible across modules via a common relational table structure.

**Relevance to security professionals:**
- Security Incident Response (SIR) module automates security incident workflows
- Vulnerability Response (VR) module tracks and triages vulnerabilities
- Threat Intelligence integration via STIX/TAXII
- Flow Designer is the primary automation layer for all of the above

---

## Flow Designer — Overview

Flow Designer is ServiceNow's low-code/no-code process automation tool, introduced in the New York release (2019) as the successor to Workflow Editor. It provides a declarative, trigger-based automation model built on reusable components.

**Key concepts:**

| Term | Definition |
|---|---|
| Flow | A complete automated process — triggered, sequential logic with actions |
| Trigger | The event that starts a flow (record change, schedule, inbound email, REST call, etc.) |
| Action | A discrete unit of work within a flow (create record, send email, call REST API, etc.) |
| Subflow | A reusable, callable flow that can be invoked from multiple parent flows |
| Action Step | Individual operation inside an action block |
| Data Pill | A reference to a data value from a previous step — analogous to a variable |
| Flow Logic | Conditional branching, loops, and error handling within a flow |

---

## Triggers

Triggers define what starts a flow. Every flow must have exactly one trigger.

### Trigger types

| Trigger Type | Description | Common Use |
|---|---|---|
| **Record-Based** | Fires when a record is created, updated, or deleted | Incident created → start triage flow |
| **Schedule** | Fires on a cron-like schedule (daily, weekly, custom) | Daily vulnerability scan import |
| **Inbound Email** | Fires when an email matches routing rules | Email-to-incident creation |
| **REST API (Spoke)** | Fired by an external system via webhook or API call | SIEM alert → create security incident |
| **Service Catalog** | Fires when a catalog item is requested | Access request submitted → approval flow |
| **Application** | Custom trigger from a scoped application | Internal app event |
| **After Subflow** | Triggered by completion of a subflow | Post-processing after enrichment subflow |

### Record-Based trigger conditions

When using a record-based trigger, you define:
- **Table** — the ServiceNow table to watch (e.g. `sn_si_incident` for Security Incidents)
- **Condition** — field-level filters (e.g. `State is New`, `Priority is 1-Critical`)
- **Run** — `For every update` / `Once` / `Only if not previously triggered`
- **Stage** — `Before` (pre-commit, can modify record) or `After` (post-commit)

---

## Flow Logic

Flow Logic components control execution path within a flow. These are the conditional and iterative constructs.

### If / Else If / Else

Standard conditional branching based on data pill values or field comparisons.

```
IF [Trigger Record.Priority] IS [1 - Critical]
  → Action: Page on-call team
ELSE IF [Trigger Record.Priority] IS [2 - High]
  → Action: Create task and assign to tier-2
ELSE
  → Action: Log and monitor only
```

**Supported operators:**
- `is` / `is not`
- `contains` / `does not contain`
- `starts with` / `ends with`
- `is empty` / `is not empty`
- `greater than` / `less than` / `greater than or equal to` / `less than or equal to`
- `is one of` / `is not one of` (list matching)

### For Each (Loop)

Iterates over a list of records returned by a Lookup action or a Data Stream.

```
FOR EACH Record IN [Look Up Records result: Open Vulnerabilities]
  → Action: Update vulnerability state to "In Remediation"
  → Action: Notify assigned user
END FOR
```

> Flows are synchronous by default. Long-running For Each loops over large record sets can exceed the 30-minute execution timeout. Use **Flow Scheduler** or **Data Stream Actions** for bulk processing.

### Wait For Condition

Pauses flow execution until a specified condition becomes true on a record, or until a timer expires.

```
WAIT FOR [Security Incident.State] CHANGES TO [Resolved]
  OR TIMEOUT after [72 hours]
    → Action: Escalate to manager
```

**Use cases:**
- Waiting for approval before provisioning access
- Holding an incident flow until a remediation task is completed
- SLA-based escalation timers

### Try / Catch (Error Handling)

Wraps action steps in error-handling logic. If any step in the Try block fails, execution jumps to the Catch block.

```
TRY
  → Action: REST API call to threat intel feed
CATCH [Error]
  → Action: Log error to sys_log
  → Action: Set incident field "Enrichment Status" to "Failed"
  → Action: Notify SOC analyst
```

**Catch details available as data pills:**
- `Error.Message` — human-readable error string
- `Error.Stack Trace` — full stack for debugging

### Set Flow Variable

Assigns a value to a named variable usable as a data pill throughout the rest of the flow.

```
SET VARIABLE [threat_score] = [Lookup Record result.threat_score]
```

Variables persist for the lifetime of the flow execution and can be passed to subflows.

### Return Value (Subflow output)

When building a Subflow, `Return Value` defines what the subflow passes back to the calling flow.

---

## Actions

Actions are the individual work units inside a flow. ServiceNow provides built-in actions, and IntegrationHub spokes add third-party actions.

### Core built-in actions

| Action | What it does |
|---|---|
| **Create Record** | Creates a new record in any table |
| **Update Record** | Updates fields on an existing record |
| **Look Up Record** | Returns a single record matching filter conditions |
| **Look Up Records** | Returns a list of records (used with For Each) |
| **Delete Record** | Deletes a record |
| **Log** | Writes to the flow execution log (visible in Flow Execution History) |
| **Wait for Condition** | Pauses execution (see Flow Logic above) |
| **Ask for Approval** | Sends an approval request and waits for response |
| **Assign to User** | Sets the assigned_to field and optionally notifies |
| **Send Email** | Sends a notification email using a template |
| **Run Script** | Executes a server-side JavaScript snippet inline |
| **Call Subflow** | Invokes a Subflow and optionally waits for its completion |

### IntegrationHub spoke actions (security-relevant)

| Spoke | Key Actions |
|---|---|
| **Jira Spoke** | Create issue, update issue, add comment |
| **Slack Spoke** | Send message, post to channel |
| **Microsoft Teams** | Post adaptive card, send message |
| **REST Step** | Generic HTTP GET/POST/PUT/DELETE to any REST API |
| **LDAP Spoke** | Query Active Directory, modify group membership |
| **Threat Intelligence** | Lookup IP/domain/hash against threat feeds |
| **Vulnerability Response** | Trigger vuln scan, update CVE status |

> Spokes require an **IntegrationHub** license. The REST Step is available without additional licensing and covers most custom integrations.

### Run Script action — security use cases

The `Run Script` action executes server-side GlideScript (ServiceNow's JavaScript dialect based on Mozilla Rhino). This is the escape hatch for logic that cannot be expressed declaratively.

```javascript
// Example: Parse a JSON payload from a webhook trigger
var payload = JSON.parse(fd_data.trigger.rest_api_request.body.text);
var severity = payload.alert.severity;
var sourceIp = payload.alert.src_ip;

// Dynamically set output variables to be used as data pills downstream
output.severity = severity;
output.source_ip = sourceIp;
```

**GlideRecord — query and update tables from script:**

```javascript
// Look up and update a security incident
var gr = new GlideRecord('sn_si_incident');
gr.addQuery('number', fd_data.trigger.record.number);
gr.query();
if (gr.next()) {
  gr.setValue('priority', '1');
  gr.setValue('state', '2'); // In Progress
  gr.update();
}
```

---

## Flow Execution & Debugging

### Execution History

Every flow execution is logged. Access via:
**Flow Designer → Flow → Executions** or directly from the triggering record's **Flow Context** related list.

Each execution shows:
- Trigger details
- Step-by-step execution log
- Data pill values at each step
- Error messages and stack traces
- Execution duration

### Common failure modes

| Symptom | Likely cause | Resolution |
|---|---|---|
| Flow never triggers | Trigger condition not met, or flow inactive | Check trigger conditions; confirm flow is Active |
| ACL / permission error on Create Record | Flow runs as System (background) user with insufficient rights | Adjust table ACL or run flow as a service account with appropriate roles |
| REST call returning 401 | Credential alias not configured or expired | Re-authenticate the Connection & Credential alias |
| For Each loop times out | Too many records, 30-minute execution cap | Use Data Stream or batch processing via Scheduled Script Execution |
| Data pill shows empty | Previous step failed silently or field path incorrect | Add a Log action after each step during development to inspect pill values |

### Testing flows

- Use **Test** button in Flow Designer to run with a specific record in a non-production state
- Use **Flow Execution History** to inspect every step
- Add `Log` actions liberally during development — remove or disable before production
- Test trigger conditions on dev/test instances before activating in production

---

## Security Operations (SecOps) Flow Patterns

### Pattern 1 — SIEM alert → Security Incident

```
TRIGGER: Inbound REST API (from SIEM webhook)
  ↓
ACTION: Run Script (parse alert payload, extract severity/source IP/signature)
  ↓
FLOW LOGIC: IF severity = "Critical"
  ↓ YES
  ACTION: Create Record → Security Incident (sn_si_incident)
  ACTION: Assign to on-call analyst
  ACTION: Slack notification to #soc-alerts channel
  ↓ NO
  ACTION: Create Record → Security Event (log only, no incident)
```

### Pattern 2 — Vulnerability Response automation

```
TRIGGER: Schedule (daily at 06:00)
  ↓
ACTION: REST Step → Call vuln scanner API for new findings
  ↓
ACTION: For Each result in scan output
  ↓
  FLOW LOGIC: IF CVSS >= 9.0
    ACTION: Create Vulnerability Response record
    ACTION: Assign to asset owner
    ACTION: Set SLA = 7 days
  ELSE IF CVSS >= 7.0
    ACTION: Create Vulnerability Response record
    ACTION: Set SLA = 30 days
  ELSE
    ACTION: Log (low severity, monitor only)
```

### Pattern 3 — Phishing email triage

```
TRIGGER: Inbound Email (subject contains [PHISHING REPORT])
  ↓
ACTION: Create Record → Security Incident
ACTION: Run Script → Extract header artifacts (sender IP, return-path, X-Originating-IP)
  ↓
ACTION: Call Subflow → Threat Intel Lookup (sender domain, links)
  ↓
FLOW LOGIC: IF threat score > 70
  ACTION: Update Incident Priority = 1-Critical
  ACTION: Page analyst
ELSE
  ACTION: Update Incident Priority = 3-Moderate
  ACTION: Assign to phishing queue
```

---

## Key Concepts Summary

| Concept | One-line definition |
|---|---|
| Flow | Complete automated process — trigger → logic → actions |
| Trigger | Event that starts the flow |
| Action | Discrete unit of work (create, update, notify, call API) |
| Subflow | Reusable, callable child flow |
| Data Pill | Variable reference to upstream step output |
| Flow Logic | If/Else, For Each, Wait, Try/Catch |
| Run Script | Inline GlideScript for logic beyond declarative capabilities |
| IntegrationHub | Licensed add-on providing pre-built third-party action spokes |
| Execution History | Per-execution audit log for debugging |

---

## Key Takeaways

- Flow Designer replaces legacy Workflow Editor — all new automation should be built in Flow Designer
- Data Pills are the lifeblood of flows — understanding how to reference upstream step outputs determines how far you can take automation
- The `Run Script` action is the power tool — use it for JSON parsing, dynamic logic, and GlideRecord operations when declarative steps are insufficient
- For security teams: the three most impactful patterns are SIEM→Incident, Vuln Response automation, and phishing triage
- Always test in a dev/test ServiceNow instance before activating flows in production — mis-triggered flows that create thousands of records are a real risk

---

## References

- [ServiceNow Flow Designer Product Docs](https://docs.servicenow.com/bundle/washingtondc-build-workflows/page/administer/flow-designer/concept/flow-designer.html)
- [ServiceNow Security Operations Overview](https://docs.servicenow.com/bundle/washingtondc-security-management/page/product/security-incident-response/concept/security-incident-response-landing-page.html)
- [ServiceNow IntegrationHub](https://docs.servicenow.com/bundle/washingtondc-build-workflows/page/administer/integrationhub/concept/integrationhub.html)
- [GlideRecord API Reference](https://developer.servicenow.com/dev.do#!/reference/api/washingtondc/server/no-namespace/c_GlideRecordScopedAPI)

---
*Note created: 2026-03-25*

## See Also

- Incident-Response
- Python-for-Security
- Penetration-Testing-Methodology

---

## Chuck Tomasi & Key Educator Resources

**Chuck Tomasi** is a multi-year ServiceNow Community MVP, author, and one of the most widely followed ServiceNow educators in the community. His content is grounded in the official platform and is highly practical.

- **SN Pro Tips** (Chuck Tomasi): https://snprotips.com — practical tips and tutorials, scripting walkthroughs
- **Books (Packt Publishing):**
  - *Learning ServiceNow* — foundational platform reference covering tables, forms, workflows, and administration
  - *Mastering ServiceNow Scripting* — deep dive into GlideScript, Business Rules, Client Scripts, Script Includes
- **ServiceNow Developer Portal**: https://developer.servicenow.com — official developer docs, labs, and sandbox instances
- **Now Learning** (official training & cert prep): https://nowlearning.servicenow.com
- **ServiceNow Community**: https://www.servicenow.com/community/
- **ServiceNow YouTube**: https://www.youtube.com/@ServiceNow

> **Release note:** ServiceNow releases a new version approximately every 6 months using city names (Utah, Vancouver, Washington DC, Xanadu, Yokohama, etc.). Documentation URLs include the release name. Always confirm the URL matches your instance version. The Xanadu release documentation is current as of early 2026.

---
<- [[Reference-MOC]]