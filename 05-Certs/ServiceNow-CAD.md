---
title: "ServiceNow CAD — Certified Application Developer"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CAD, application-developer, scripting, app-engine, study-guide, job-aid, intermediate]
source: "ServiceNow official CAD documentation (docs.servicenow.com), Now Learning CAD training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🛠 ServiceNow CAD — Certified Application Developer

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Application Developer |
| Abbreviation | CAD |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Cost | ~$350 USD per attempt |
| Prerequisite training | Application Development Fundamentals (Now Learning) — required before registering |
| Recommended experience | Hands-on ServiceNow development experience; CSA knowledge strongly recommended |

**Official training path:** https://nowlearning.servicenow.com
**Official docs:** https://docs.servicenow.com
**Developer sandbox:** https://developer.servicenow.com (free Personal Developer Instance)

---

## What the CAD Validates

The CAD certifies that you can **build custom applications and automations on the ServiceNow platform** — including designing data models, writing server and client-side scripts, building integrations, and using App Engine Studio. It is the developer-track counterpart to the CSA.

> Source: [ServiceNow Learning Paths PDF 2025](https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf)

---

## Exam Domain Areas

> ⚑ Verify current domain weights and blueprint at nowlearning.servicenow.com before your exam — weights are updated with each ServiceNow release. The domains below reflect the CAD curriculum as documented in the official training path.

| Domain | Key Topics |
|---|---|
| Application Design | Tables, fields, data models, application scope, relationships |
| Scripting — Server-side | Business Rules, Script Includes, GlideRecord, GlideSystem |
| Scripting — Client-side | Client Scripts, UI Policies, UI Actions |
| Application Development | App Engine Studio, forms, views, lists, modules |
| Integration | REST API, Integration Hub, inbound/outbound REST |
| Flow Designer | Flows, subflows, actions within custom applications |
| Service Portal | Widgets, portals, Angular for custom apps |
| Security | Application scope, ACLs, roles within custom apps |
| Testing | ATF for application testing, test design |

---

## Core Concepts — Study & Job Aid

### Application Scope

Every application in ServiceNow runs in a **scope** — an isolated namespace that protects application records and scripts from other apps.

```
Global scope  → affects entire platform; use sparingly
Scoped apps   → isolated, can be published to App Store
              → prefix (e.g. x_myco_myapp_) applied to all records

Check current scope: top-right of the ServiceNow UI banner
Switch scope: click the scope selector dropdown
```

- Scoped apps cannot directly access global resources unless explicitly allowed
- Tables in a scoped app are prefixed: `x_{company}_{app}_{tablename}`
- Script Includes in a scoped app must be explicitly marked as accessible from other scopes if needed

> Source: [ServiceNow Docs — Application scope](https://docs.servicenow.com/bundle/washingtondc-application-development/page/build/applications/concept/c_ApplicationScoping.html)

### App Engine Studio (AES)

App Engine Studio is ServiceNow's low-code/no-code application builder. It provides a guided interface for creating apps without deep scripting knowledge.

```
Navigator: App Engine → App Engine Studio
OR: https://your-instance.service-now.com/now/app-engine-studio
```

**AES capabilities:**
- Create tables with point-and-click field builders
- Configure forms, lists, and related lists visually
- Build Flows (Flow Designer) within the app context
- Add roles and access control
- Deploy apps to other instances

**When to use AES vs. Studio IDE:**
- AES: simple to medium apps, citizen developers, rapid prototyping
- Studio IDE (`sys_app_editor.do`): complex apps, scripting-heavy, source control integration

> Source: [ServiceNow Docs — App Engine Studio](https://docs.servicenow.com/bundle/washingtondc-application-development/page/build/app-engine-studio/concept/aes-overview.html)

---

### Data Model — Tables and Fields

```
Create table: System Definition → Tables → New
OR: App Engine Studio → Data → Add a table

Key table fields:
- Name        → technical name (auto-prefixed in scoped apps)
- Label       → display name
- Extends     → parent table (inheritance)
- Application → scope this table belongs to

Add a field: open table → Columns tab → New
Field types: String, Integer, Boolean, Reference, List, Choice, HTML, Date/Time, Script
```

**Table inheritance — key pattern:**
```
task (parent)
├── incident         extends task
├── problem          extends task
├── change_request   extends task
└── sc_task          extends task

→ All task fields available on child tables
→ Create your own extending task for case-management style apps
```

> Source: [ServiceNow Docs — Tables and fields](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/table-administration/concept/c_TableAdministration.html)

---

### Server-Side Scripting

#### Business Rules

Business Rules execute server-side JavaScript when records are queried, inserted, updated, or deleted.

```
Navigator: System Definition → Business Rules → New
```

| When | Runs |
|---|---|
| before | Before DB operation — can modify `current` |
| after | After DB operation — `current` has new sys_id |
| async | Background — after response sent to client |
| display | Before form loads — read-only access to `current` |

**Key APIs in Business Rules:**

```javascript
// current = the record being acted on
current.getValue('field_name')     // get field value
current.setValue('field', 'val')   // set field value
current.update()                   // save changes (in after/async)
current.setAbortAction(true)       // prevent the DB operation

// previous = the record BEFORE the update (available in before/after update)
previous.getValue('state')         // what state was before

// GlideRecord query
var gr = new GlideRecord('incident');
gr.addQuery('state', 1);           // state = New
gr.query();
while (gr.next()) {
    gs.log(gr.getValue('number'));
}

// Useful GlideSystem calls
gs.getUserID()                     // current user's sys_id
gs.getUserName()                   // current user's username
gs.hasRole('itil')                 // role check
gs.addInfoMessage('Done')          // UI info message
gs.addErrorMessage('Failed')       // UI error message
```

#### Script Includes

Reusable server-side JavaScript libraries called from Business Rules, REST APIs, or other scripts.

```
Navigator: System Definition → Script Includes → New
```

```javascript
// Class pattern (most common)
var IncidentUtils = Class.create();
IncidentUtils.prototype = {
    initialize: function() {},

    getOpenCount: function(assignmentGroup) {
        var gr = new GlideRecord('incident');
        gr.addQuery('assignment_group', assignmentGroup);
        gr.addQuery('state', 'IN', '1,2');
        gr.query();
        return gr.getRowCount();
    },

    type: 'IncidentUtils'
};

// Call from anywhere server-side:
var util = new IncidentUtils();
var count = util.getOpenCount(current.assignment_group);
```

> Source: [ServiceNow Docs — Script Includes](https://docs.servicenow.com/bundle/washingtondc-application-development/page/script/server-scripting/concept/c_ScriptIncludes.html)

---

### Client-Side Scripting

Client Scripts run in the **user's browser** and react to form events.

```
Navigator: System Definition → Client Scripts → New
```

| Type | Fires When |
|---|---|
| onLoad | Form loads |
| onChange | A specific field value changes |
| onSubmit | User submits the form |
| onCellEdit | A list cell is edited |

```javascript
// onLoad example — make a field mandatory based on category
function onLoad() {
    if (g_form.getValue('category') == 'software') {
        g_form.setMandatory('subcategory', true);
    }
}

// onChange example — hide/show a field
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading) return;
    g_form.setVisible('u_custom_field', newValue == 'hardware');
}

// Key g_form methods:
g_form.getValue('field')           // get field value
g_form.setValue('field', val)      // set field value
g_form.setMandatory('field', bool) // set mandatory
g_form.setVisible('field', bool)   // show/hide
g_form.setReadOnly('field', bool)  // read-only
g_form.addInfoMessage('text')      // info banner
g_form.addErrorMessage('text')     // error banner
g_form.getReference('field', cb)   // async get reference record
```

**UI Policies** — declarative alternative to Client Scripts for simple show/hide/mandatory rules. No scripting required.

```
Navigator: System Definition → UI Policies → New
Conditions: set when the policy applies
Actions: for each field — set mandatory, visible, read-only (true/false/leave alone)
```

> Source: [ServiceNow Docs — Client Scripts](https://docs.servicenow.com/bundle/washingtondc-application-development/page/script/client-scripting/concept/c_ClientScripts.html)

---

### REST Integration

#### Outbound REST (calling external APIs)

```
Navigator: System Web Services → Outbound → REST Messages → New
```

```javascript
// In a Script Include or Business Rule:
try {
    var request = new sn_ws.RESTMessageV2();
    request.setEndpoint('https://api.example.com/data');
    request.setHttpMethod('GET');
    request.setRequestHeader('Authorization', 'Bearer ' + token);

    var response = request.execute();
    var statusCode = response.getStatusCode();
    var body = response.getBody();
    var parsed = JSON.parse(body);

    return parsed.value;
} catch(ex) {
    gs.logError('REST call failed: ' + ex.message, 'MyIntegration');
}
```

#### Inbound REST (ServiceNow as API server)

ServiceNow auto-exposes a REST API for every table:

```
Base URL: https://instance.service-now.com/api/now/table/{tablename}
GET    /api/now/table/incident?sysparm_query=active=true
POST   /api/now/table/incident  (body: JSON record data)
PUT    /api/now/table/incident/{sys_id}
DELETE /api/now/table/incident/{sys_id}

Auth: Basic auth or OAuth 2.0 Bearer token
```

**Scripted REST APIs** — custom endpoints with custom logic:

```
Navigator: System Web Services → Scripted REST APIs → New
```

> Source: [ServiceNow Docs — REST API](https://docs.servicenow.com/bundle/washingtondc-api-reference/page/integrate/inbound-rest/concept/c_RESTAPI.html)

---

### Integration Hub

Integration Hub provides pre-built **spokes** (connectors) and lets you build custom actions for Flow Designer.

```
Navigator: Integration Hub → Spokes (to browse available integrations)
Navigator: Integration Hub → Action Designer (to build custom actions)
```

**Common spokes:** Slack, Jira, GitHub, Azure DevOps, AWS, ServiceNow-to-ServiceNow, Okta, Microsoft Teams

**Custom spoke action pattern:**
1. Create a new Action in Action Designer
2. Define input variables
3. Add REST step (or Script step)
4. Map outputs
5. Call from Flow Designer

---

### ACLs Within Custom Applications

```
Navigator: Security → Access Control (ACL)
```

```
ACL evaluation order:
1. Table-level ACL (e.g. x_myco_myapp_request)
2. Field-level ACL (e.g. x_myco_myapp_request.sensitive_data)
Deny by default: if no ACL matches → access denied
```

**Best practice for custom apps:**
- Create a base role for the app (e.g. `x_myco_myapp.user`)
- Create an admin role (e.g. `x_myco_myapp.admin`)
- Write ACLs that grant `user` read access, `admin` write/delete access
- Never rely on global admin role for app-level access control

---

### Testing Custom Applications with ATF

See [[ServiceNow-ATF-Micro-Cert]] for full ATF reference.

```
Key pattern for app testing:
1. Create test data (Create Record step → your custom table)
2. Assert field defaults applied correctly
3. Trigger a Business Rule condition (update a field)
4. Assert the Business Rule outcome
5. Rollback enabled → test data cleaned up automatically
```

---

## Study Plan

| Week | Focus |
|---|---|
| 1 | Complete Application Development Fundamentals on Now Learning |
| 2 | Build a sample app end-to-end on your PDI (tables, forms, Business Rules) |
| 3 | Practice scripting: Business Rules, Script Includes, Client Scripts |
| 4 | REST integrations: outbound REST call + Scripted REST API |
| 5 | App Engine Studio, Flow Designer in app context, ATF for your app |
| 6 | Practice exam questions + review weak areas |

**Personal Developer Instance:** https://developer.servicenow.com — free, required for hands-on practice

---

## Key Exam Tips

1. **Scope matters** — always check which scope you're in; scoped app tables get a prefix
2. **Business Rule timing** — know when before/after/async/display fire and what `current`/`previous` are available
3. **Client vs. server** — Client Scripts run in the browser; Business Rules run on the server
4. **Script Includes** — use the Class.create() pattern; call them with `new MyClass()`
5. **ACL deny by default** — no matching ACL = access denied; applies at table and field level
6. **g_form vs. GlideRecord** — g_form is client-side only; GlideRecord is server-side only

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — CAD path** | https://nowlearning.servicenow.com — Application Development Fundamentals (required) |
| **ServiceNow Developer Portal** | https://developer.servicenow.com — free PDI + developer documentation |
| **ServiceNow Docs** | https://docs.servicenow.com — filter to your PDI's release version |
| **ServiceNow Community** | https://www.servicenow.com/community/ — developer forum, code snippets |
| **Chuck Tomasi — SN Pro Tips** | https://snprotips.com — practical developer tutorials |
| **Books (Packt)** | *Mastering ServiceNow Scripting* by Chuck Tomasi |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals, Update Sets, ACLs
- ServiceNow-ATF-Micro-Cert — Testing custom applications
- ServiceNow-Flow-Designer — Flow Designer used within custom apps
- ServiceNow-Micro-Cert-Flows — Flow and subflow patterns

---

## References

- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf
- ServiceNow Docs — App Engine Studio: https://docs.servicenow.com/bundle/washingtondc-application-development/page/build/app-engine-studio/concept/aes-overview.html
- ServiceNow Docs — Script Includes: https://docs.servicenow.com/bundle/washingtondc-application-development/page/script/server-scripting/concept/c_ScriptIncludes.html
- ServiceNow Docs — Client Scripts: https://docs.servicenow.com/bundle/washingtondc-application-development/page/script/client-scripting/concept/c_ClientScripts.html
- ServiceNow Docs — REST API: https://docs.servicenow.com/bundle/washingtondc-api-reference/page/integrate/inbound-rest/concept/c_RESTAPI.html
- ServiceNow Developer Portal: https://developer.servicenow.com

---
<- [[Certs-MOC]]