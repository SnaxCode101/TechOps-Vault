---
title: ServiceNow JavaScript — GlideScript & Platform Scripting
tags:
  - reference
  - servicenow
  - javascript
  - GlideScript
  - GlideRecord
  - business-rules
  - client-scripts
  - scripting
  - intermediate
created: 2026-03-26
updated: 2026-03-26
source: ServiceNow official developer docs (developer.servicenow.com), Chuck
  Tomasi — SN Pro Tips (snprotips.com) and Mastering ServiceNow Scripting
  (Packt)
difficulty: intermediate
---

# ⚙️ ServiceNow JavaScript — GlideScript & Platform Scripting

## Overview
ServiceNow uses JavaScript as its scripting language throughout the platform. However, **server-side scripts run on Mozilla Rhino** — a Java-based JavaScript engine that is largely ES5 compatible. This is important: many modern ES6+ features available in browsers or Node.js **do not work** in server-side ServiceNow scripts.

Client-side scripts (Client Scripts, UI Policies) run in the browser and support modern JS.

**Two notes cover this area:**
- [[JavaScript-Fundamentals]] — core JavaScript language (read this first)
- This note — ServiceNow-specific APIs, patterns, and GlideScript

**Key references:**
- ServiceNow Developer Portal: https://developer.servicenow.com
- ServiceNow Docs: https://docs.servicenow.com
- Chuck Tomasi — SN Pro Tips: https://snprotips.com
- Chuck Tomasi, *Mastering ServiceNow Scripting*, Packt Publishing

---

## Rhino Engine — What ES6+ Works Server-Side

**Works on server-side (Rhino):**
```javascript
var, function, for, while, if/else, switch, try/catch   // All ES5 ✅
JSON.parse(), JSON.stringify()                            // ✅
Array methods: forEach, map, filter, reduce              // ✅
Object.keys(), Object.values()                           // ✅
```

**Does NOT work on server-side (no Rhino support):**
```javascript
let, const          // ❌ Use var on server side
Arrow functions     // ❌ () => {}  — use function() {}
Template literals   // ❌ `Hello ${name}` — use "Hello " + name
Promises / async/await  // ❌ Not available server-side
Destructuring       // ❌ const {a,b} = obj — not supported
Spread operator     // ❌ [...arr] — not supported
for...of            // ❌ Use for loop or forEach
class keyword       // ❌ Use prototype pattern or Script Includes
```

**Client-side scripts (run in browser) support modern JS** — `let`, `const`, arrow functions, template literals, destructuring all work in Client Scripts.

> 💡 **Chuck Tomasi's advice:** On the server, write in ES5 style. On the client, modern JS is fine. Keep the distinction clear in your mental model.

---

## Script Types — When to Use Which

| Script Type | Where It Runs | Trigger | Primary Purpose |
|---|---|---|---|
| **Business Rule** | Server | DB operation (insert/update/delete/query) | Enforce logic, modify records server-side |
| **Script Include** | Server | Called by other server scripts | Reusable functions/classes — the server-side library |
| **Client Script** | Browser | Form event (onLoad/onChange/onSubmit) | UI manipulation, field validation |
| **UI Policy** | Browser | Field value change | Show/hide/mandatory — no scripting needed |
| **Flow Designer** | Server | Trigger (record, schedule, REST) | Process automation — preferred over Business Rules for workflows |
| **Scheduled Script Execution** | Server | Cron schedule | Batch jobs, maintenance tasks |
| **Scripted REST API** | Server | Inbound REST request | Custom API endpoints |
| **REST Message** | Server | Called by scripts | Outbound REST calls to external systems |

---

## GlideRecord — The Core API

GlideRecord is the primary API for querying and manipulating ServiceNow database tables. Every interaction with table data goes through GlideRecord.

### Reading Records

```javascript
// Get a single record by sys_id
var gr = new GlideRecord("incident");
gr.get("abc123def456abc123def456abc123de");  // sys_id
if (gr.isValid()) {
    gs.log(gr.number + ": " + gr.short_description);
}

// Query multiple records
var gr = new GlideRecord("incident");
gr.addQuery("state", 1);                    // state = 1 (New)
gr.addQuery("priority", "<=", 2);          // priority <= 2
gr.orderByDesc("sys_created_on");          // Newest first
gr.setLimit(10);                           // Maximum 10 records
gr.query();

while (gr.next()) {
    gs.log(gr.number + " | " + gr.short_description + " | " + gr.state.getDisplayValue());
}
```

### Encoded Queries

Encoded queries are the string format of query conditions — copy them from the URL bar in ServiceNow list views.

```javascript
var gr = new GlideRecord("incident");
gr.addEncodedQuery("state=1^priority<=2^assigned_toISEMPTY");
gr.query();
while (gr.next()) {
    gs.log(gr.number);
}
```

**Build encoded query strings:** Run a list view in ServiceNow, apply your filters, then copy the URL parameter `sysparm_query=...` — that value is your encoded query.

### Creating Records

```javascript
var gr = new GlideRecord("incident");
gr.initialize();                                    // Clear the object
gr.short_description = "Server is down";
gr.priority = 1;
gr.category = "hardware";
gr.caller_id.setDisplayValue("Abel Tuter");         // Set reference by display value
gr.assignment_group.setDisplayValue("Network");     // Set reference by display value

var sysId = gr.insert();                            // Returns sys_id of new record
gs.log("Created: " + sysId);
```

### Updating Records

```javascript
// Update a single record
var gr = new GlideRecord("incident");
gr.get("SYS_ID_HERE");
gr.state = 2;                              // Set numeric value
gr.setValue("state", 2);                  // Alternative — safer for dynamic field names
gr.work_notes = "Assigned to team.";
gr.update();

// Update multiple records
var gr = new GlideRecord("incident");
gr.addQuery("state", 1);
gr.query();
while (gr.next()) {
    gr.state = 2;
    gr.update();
}
```

### Deleting Records

```javascript
var gr = new GlideRecord("incident");
gr.addQuery("state", 7);                  // Closed incidents
gr.addQuery("sys_created_on", "<", "2020-01-01 00:00:00");
gr.query();
while (gr.next()) {
    gr.deleteRecord();                    // Delete current record
}
```

### Counting Records

```javascript
// Row count (efficient — does not fetch all records)
var ga = new GlideAggregate("incident");
ga.addQuery("state", 1);
ga.addAggregate("COUNT");
ga.query();
if (ga.next()) {
    var count = ga.getAggregate("COUNT");
    gs.log("Open incidents: " + count);
}
```

### Working with Reference Fields

```javascript
var gr = new GlideRecord("incident");
gr.get("SYS_ID");

// Reference field: caller_id points to sys_user table
gr.caller_id                          // sys_id of referenced record (raw value)
gr.caller_id.toString()               // sys_id as string
gr.caller_id.getDisplayValue()        // Display value (e.g., "Abel Tuter")
gr.getValue("caller_id")              // sys_id as string (same as toString)
gr.getDisplayValue("caller_id")       // Display value

// Set reference by sys_id
gr.caller_id = "sys_id_of_user";

// Set reference by display value
gr.caller_id.setDisplayValue("Abel Tuter");

// Walk into referenced record's fields
gr.caller_id.email                    // Email of the caller
gr.caller_id.department.name          // Department name (dot-walking)
```

> ⚠️ **Dot-walking in queries is expensive** — it triggers additional database joins. Use sparingly. For Business Rules, prefer working with the direct table.

### GlideRecord Methods Quick Reference

| Method | Purpose |
|---|---|
| `gr.get(sys_id)` | Get single record by sys_id |
| `gr.addQuery(field, value)` | Add WHERE condition |
| `gr.addQuery(field, operator, value)` | With operator: `=`, `!=`, `<`, `<=`, `>`, `>=`, `CONTAINS`, `STARTSWITH`, `ENDSWITH`, `IN`, `NOT IN`, `ISEMPTY`, `ISNOTEMPTY` |
| `gr.addEncodedQuery(str)` | Add complex encoded query string |
| `gr.addNullQuery(field)` | WHERE field IS NULL |
| `gr.addNotNullQuery(field)` | WHERE field IS NOT NULL |
| `gr.addOrCondition(field, value)` | OR condition (use with addQuery) |
| `gr.orderBy(field)` | Sort ascending |
| `gr.orderByDesc(field)` | Sort descending |
| `gr.setLimit(n)` | Limit result count |
| `gr.query()` | Execute query |
| `gr.next()` | Move to next record (use in while loop) |
| `gr.hasNext()` | Check if more records |
| `gr.initialize()` | Clear for new insert |
| `gr.insert()` | Create new record, return sys_id |
| `gr.update()` | Save changes to existing record |
| `gr.updateMultiple()` | Update all queried records without looping |
| `gr.deleteRecord()` | Delete current record |
| `gr.deleteMultiple()` | Delete all queried records without looping |
| `gr.getValue(field)` | Get raw value as string |
| `gr.getDisplayValue(field)` | Get display value |
| `gr.setValue(field, value)` | Set field value |
| `gr.isValid()` | True if record exists |
| `gr.getRowCount()` | Count of records (after query) |
| `gr.getTableName()` | Table name |
| `gr.getUniqueValue()` | sys_id of current record |

---

## GlideSystem (gs) — Server-Side Utility

`gs` is available in all server-side scripts. It provides logging, user information, date/time, and utility functions.

```javascript
// Logging
gs.log("Message");                    // Logs to system log (level: info)
gs.info("Info message");             // Info level
gs.warn("Warning message");          // Warning level
gs.error("Error message");           // Error level
gs.debug("Debug message");           // Debug level (verbose — disable in production)

// Current user
gs.getUserID()           // sys_id of logged-in user
gs.getUserName()         // Username (e.g., "abel.tuter")
gs.getUser().getFullName()  // Full name
gs.hasRole("admin")      // true/false — check role
gs.hasRole("itil")

// Date and time
gs.now()                 // Current datetime: "2026-03-26 14:30:00"
gs.nowDateTime()         // Same as gs.now()
gs.today()               // Current date: "2026-03-26"

// String utilities
gs.nil(value)            // true if null, undefined, or empty string
gs.isGlideObject(obj)    // true if value is a GlideElement

// Include a Script Include
var util = new MyScriptInclude();

// System properties
gs.getProperty("instance_name")           // Read system property value
gs.getProperty("glide.instance.name")     // Instance name
```

---

## Business Rules

Business Rules run server-side on database operations. They are the core mechanism for enforcing server-side logic.

### Business Rule Types

| Type | Runs | Use case |
|---|---|---|
| **Before** | Before record saves to DB | Modify field values; validate; set defaults |
| **After** | After record saves to DB | Create related records; send notifications |
| **Async** | Background, after save | Long-running tasks; avoid slowing UI |
| **Display** | Before form loads (read) | Set `g_scratchpad` values for client scripts |

### Business Rule Script Structure

```javascript
// Available objects:
// current  — the record being saved (GlideRecord)
// previous — the previous state of the record (before changes)
// g_scratchpad — pass data from Display BR to client scripts

// Example: Before Insert/Update Business Rule
// Table: incident
// When: before insert OR update
// Condition: current.priority == 1

(function executeRule(current, previous /*null when async*/) {

    // Set resolved_at when incident closes
    if (current.state == 6 && previous.state != 6) {
        current.resolved_at = gs.now();
    }

    // Auto-assign based on category
    if (gs.nil(current.assigned_to)) {
        if (current.category == "network") {
            var grUser = new GlideRecord("sys_user");
            grUser.addQuery("user_name", "network.default");
            grUser.setLimit(1);
            grUser.query();
            if (grUser.next()) {
                current.assigned_to = grUser.sys_id;
            }
        }
    }

})(current, previous);
```

### Condition Field vs Script Condition

Use the **Condition** field on the Business Rule form for simple checks — it is faster (evaluated before the script loads):
```
current.priority == 1 && current.state == 2
```

Use a Script condition only when the condition requires script logic that can't be expressed in the field.

### current vs current.getValue()

```javascript
// current.field — returns a GlideElement object
current.state          // GlideElement (object)
current.state == 2     // Works — GlideElement has valueOf() method

// current.getValue("field") — always returns a string
current.getValue("state")   // "2" (string)

// current.getDisplayValue("field") — returns display value
current.getDisplayValue("state")  // "In Progress"

// Best practice: use getValue() for comparisons in scripts
if (current.getValue("state") == "2") { ... }
```

---

## Script Includes — Reusable Server-Side Code

Script Includes are the server-side library pattern. They are instantiated and called from Business Rules, Scheduled Scripts, Flow Designer Run Script steps, and REST APIs.

```javascript
// Script Include: IncidentUtils
// Accessible from: All scopes (or: This application only)

var IncidentUtils = Class.create();
IncidentUtils.prototype = {

    initialize: function() {
        // Constructor — runs when new IncidentUtils() is called
        this.LOG_SOURCE = "IncidentUtils";
    },

    /**
     * Assign an incident to the correct group based on category.
     * @param {GlideRecord} incidentGR - The incident record
     */
    autoAssign: function(incidentGR) {
        var groupMap = {
            "network":   "Network Operations",
            "hardware":  "Hardware Support",
            "software":  "Software Support"
        };

        var groupName = groupMap[incidentGR.getValue("category")];
        if (!groupName) return;

        var grGroup = new GlideRecord("sys_user_group");
        grGroup.addQuery("name", groupName);
        grGroup.setLimit(1);
        grGroup.query();

        if (grGroup.next()) {
            incidentGR.assignment_group = grGroup.sys_id;
            gs.info(this.LOG_SOURCE + ": Assigned to " + groupName);
        }
    },

    /**
     * Get count of open incidents for a user.
     * @param {String} userSysId - sys_id of user
     * @return {Number} Count of open incidents
     */
    getOpenCount: function(userSysId) {
        var ga = new GlideAggregate("incident");
        ga.addQuery("assigned_to", userSysId);
        ga.addQuery("state", "NOT IN", "6,7");   // Not Resolved or Closed
        ga.addAggregate("COUNT");
        ga.query();
        if (ga.next()) {
            return parseInt(ga.getAggregate("COUNT"));
        }
        return 0;
    },

    type: "IncidentUtils"  // Required — must match class name
};
```

**Calling a Script Include from a Business Rule:**
```javascript
var util = new IncidentUtils();
util.autoAssign(current);
var count = util.getOpenCount(current.getValue("assigned_to"));
```

---

## Client Scripts

Client Scripts run in the browser and control form behaviour. They can read and write field values, show/hide fields, and validate input.

### Client Script Types

| Type | Fires When |
|---|---|
| `onLoad` | Form loads (read or edit mode) |
| `onChange` | A specific field's value changes |
| `onSubmit` | User submits the form (save/update) |
| `onCellEdit` | A cell in a list is edited |

### g_form — The Client-Side Form API

```javascript
// Get field value
g_form.getValue("state");               // Returns display value
g_form.getValue("assigned_to");        // Returns sys_id of referenced record

// Set field value
g_form.setValue("state", 2);           // Set by value
g_form.setValue("category", "network");

// Set reference field display value
g_form.setValue("assigned_to", sys_id, "Display Name");  // value, sys_id, display

// Field visibility
g_form.setVisible("close_notes", true);
g_form.setVisible("close_notes", false);
g_form.setDisplay("close_notes", true);   // show/hide (setDisplay preserves space)

// Mandatory
g_form.setMandatory("close_notes", true);
g_form.setMandatory("close_notes", false);

// Read-only
g_form.setReadOnly("priority", true);

// Labels and messages
g_form.setLabel("close_notes", "Resolution Details");
g_form.addInfoMessage("Please complete all required fields.");
g_form.addErrorMessage("Invalid input detected.");
g_form.clearMessages();

// Field sections
g_form.setSectionDisplay("Section Name", false);  // Hide a section

// Check values
g_form.getBooleanValue("active");        // true/false
g_form.getReference("caller_id", function(ref) {
    // Async — ref is a GlideRecord of the referenced record
    g_form.setValue("location", ref.location);
});
```

### onChange Client Script Example

```javascript
// Table: incident
// Field name: state
// Type: onChange

function onChange(control, oldValue, newValue, isLoading, isTemplate) {
    // isLoading = true when form first loads (skip to avoid false triggers)
    if (isLoading || newValue === "") return;

    // Show close_notes only when state = Resolved (6) or Closed (7)
    var showClose = (newValue === "6" || newValue === "7");
    g_form.setVisible("close_notes", showClose);
    g_form.setMandatory("close_notes", showClose);
}
```

### onLoad Client Script Example

```javascript
// Table: incident
// Type: onLoad

function onLoad() {
    var state = g_form.getValue("state");

    // Hide resolution fields on new incidents
    if (state === "1") {
        g_form.setVisible("close_notes", false);
        g_form.setVisible("resolved_at", false);
    }
}
```

### onSubmit Client Script Example

```javascript
// Table: incident
// Type: onSubmit

function onSubmit() {
    var state = g_form.getValue("state");

    // Require close_notes when closing
    if (state === "7") {
        if (g_form.getValue("close_notes") === "") {
            g_form.addErrorMessage("Close Notes are required when closing an incident.");
            g_form.setMandatory("close_notes", true);
            return false;  // Prevent form submission
        }
    }
    return true;  // Allow submission
}
```

---

## GlideAjax — Async Client-to-Server Calls

GlideAjax allows client-side scripts to call server-side Script Includes without a full page reload. This is the correct pattern for lookups and calculations that need server data.

### Server Side — Script Include (must extend AbstractAjaxProcessor)

```javascript
// Script Include: IncidentAjaxUtils
// Client callable: true (checkbox must be checked)

var IncidentAjaxUtils = Class.create();
IncidentAjaxUtils.prototype = Object.extendsObject(AbstractAjaxProcessor, {

    /**
     * Get open incident count for a user — callable from client
     */
    getOpenCount: function() {
        var userSysId = this.getParameter("sysparm_user_id");

        var ga = new GlideAggregate("incident");
        ga.addQuery("assigned_to", userSysId);
        ga.addQuery("state", "NOT IN", "6,7");
        ga.addAggregate("COUNT");
        ga.query();

        if (ga.next()) {
            return ga.getAggregate("COUNT");
        }
        return "0";
    },

    type: "IncidentAjaxUtils"
});
```

### Client Side — GlideAjax Call

```javascript
function onLoad() {
    var assignedTo = g_form.getValue("assigned_to");
    if (!assignedTo) return;

    var ga = new GlideAjax("IncidentAjaxUtils");
    ga.addParam("sysparm_name", "getOpenCount");       // Method to call
    ga.addParam("sysparm_user_id", assignedTo);        // Parameters

    ga.getXMLAnswer(function(response) {
        // Response is the return value from the Script Include method
        var count = response;
        if (parseInt(count) > 10) {
            g_form.addInfoMessage("This assignee has " + count + " open incidents.");
        }
    });
}
```

---

## GlideDateTime — Date & Time Handling

```javascript
// Server-side date/time (Business Rules, Script Includes)
var gdt = new GlideDateTime();          // Current date/time
var gdt2 = new GlideDateTime("2026-03-26 09:00:00");  // Specific datetime

gdt.getValue()                   // UTC value: "2026-03-26 14:30:00"
gdt.getDisplayValue()            // User timezone display value
gdt.getDate().getValue()         // Date only: "2026-03-26"
gdt.getTime().getValue()         // Time only: "14:30:00"

// Date arithmetic
gdt.addDays(7)                   // Add 7 days
gdt.addMonths(1)                 // Add 1 month
gdt.addSeconds(3600)             // Add 3600 seconds (1 hour)

// Comparison
var now = new GlideDateTime();
var due = new GlideDateTime(current.getValue("due_date"));
if (now.after(due)) {
    gs.log("Overdue!");
}

// Difference
var diff = GlideDateTime.subtract(start, end);  // GlideDuration
diff.getDayPart()      // Days component
diff.getHourOfDayPart()  // Hours component
```

---

## Scripted REST API

Build custom REST endpoints for ServiceNow.

```javascript
// Resource path: /api/yourcompany/incidents/{number}
// Method: GET

(function process(request, response) {

    var number = request.pathParams.number;

    var gr = new GlideRecord("incident");
    gr.addQuery("number", number);
    gr.setLimit(1);
    gr.query();

    if (!gr.next()) {
        response.setStatus(404);
        response.setBody({ error: "Incident not found", number: number });
        return;
    }

    response.setStatus(200);
    response.setBody({
        sys_id:            gr.getUniqueValue(),
        number:            gr.getValue("number"),
        short_description: gr.getValue("short_description"),
        state:             gr.getDisplayValue("state"),
        priority:          gr.getDisplayValue("priority"),
        assigned_to:       gr.getDisplayValue("assigned_to")
    });

})(request, response);
```

---

## Common Patterns & Best Practices

### From Chuck Tomasi's Guidance

```javascript
// 1. Always check gr.next() result before accessing fields
var gr = new GlideRecord("incident");
gr.get(sys_id);
if (gr.isValidRecord()) {    // More reliable than checking return value of get()
    gs.log(gr.number);
}

// 2. Use gs.nil() to check for empty/null/undefined
if (!gs.nil(current.assigned_to)) {
    // assigned_to has a value
}

// 3. Avoid infinite loops in Business Rules — use current.setWorkflow(false)
// when updating a record from within a Business Rule on the same table
current.work_notes = "Auto-updated";
current.setWorkflow(false);  // Prevent re-triggering Business Rules
current.update();

// 4. Use gr.updateMultiple() for bulk updates — much more efficient than looping
var gr = new GlideRecord("incident");
gr.addQuery("state", 1);
gr.setValue("priority", 3);
gr.updateMultiple();         // Updates ALL matching records in one DB call

// 5. Log at the right level
gs.debug("Verbose trace info — disable in production");
gs.info("Normal operational info");
gs.warn("Something unexpected but non-critical");
gs.error("Failure that needs attention");

// 6. Script Include vs Business Rule
// If logic is reused in >1 place → Script Include
// If logic runs once on a specific table event → Business Rule

// 7. Never put business logic in Client Scripts that requires security
// Client scripts run in the browser — users can bypass them
// Always enforce security logic server-side (Business Rules, ACLs)
```

### Debugging Tips

```javascript
// Use gs.log() liberally during development
gs.log("DEBUG: state = " + current.getValue("state"));

// Use System Logs to view output
// Navigator: System Log → All (or System Log → Application Log)
// Filter by Source = your script name

// Script Debugger (server-side)
// In Browser: Chrome DevTools → Sources → enable "Pause on exceptions"

// For Business Rules: enable logging in the script, run the scenario, check logs

// gs.print() also works in Groovy/script editor context for quick output
```

---

## Key Terms

| Term | Definition |
|---|---|
| GlideRecord | API for querying and manipulating database tables |
| GlideElement | Object representing a single field value on a record |
| GlideSystem (gs) | Server-side utility object — logging, user info, properties |
| GlideDateTime | Server-side date/time manipulation object |
| GlideAggregate | Aggregate queries (COUNT, SUM, AVG, MIN, MAX) |
| GlideAjax | Async client-to-server communication via Script Includes |
| AbstractAjaxProcessor | Parent class that Script Includes extend to be GlideAjax-callable |
| Script Include | Reusable server-side JavaScript class/function library |
| Business Rule | Server-side script triggered by DB operations |
| Client Script | Browser-side script triggered by form events |
| g_form | Client-side API for manipulating form fields |
| g_scratchpad | Object for passing data from Display Business Rules to Client Scripts |
| Rhino | Mozilla Rhino — Java-based JS engine used server-side in ServiceNow |

---

## Related Notes
- JavaScript-Fundamentals
- ServiceNow-Flow-Designer
- ServiceNow-CSA
- ServiceNow-Micro-Cert-Flows
- System-Admin-Reference

## References
- ServiceNow Developer Portal: https://developer.servicenow.com
- GlideRecord API: https://developer.servicenow.com/dev.do#!/reference/api/xanadu/server/no-namespace/c_GlideRecordScopedAPI
- GlideSystem API: https://developer.servicenow.com/dev.do#!/reference/api/xanadu/server/no-namespace/c_GlideSystemScopedAPI
- Client Script API (g_form): https://developer.servicenow.com/dev.do#!/reference/api/xanadu/client/no-namespace/c_GlideFormAPI
- GlideAjax: https://developer.servicenow.com/dev.do#!/reference/api/xanadu/client/no-namespace/c_GlideAjaxAPI
- Chuck Tomasi — SN Pro Tips: https://snprotips.com
- Chuck Tomasi, *Mastering ServiceNow Scripting*, Packt Publishing

---
<- [[Reference-MOC]]