---
title: "ServiceNow Micro-Certification — Automated Test Framework (ATF)"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, ATF, automated-testing, micro-cert, study-guide, intermediate]
source: "ServiceNow official ATF documentation (docs.servicenow.com), Now Learning ATF Micro-Certification Assessment training path"
difficulty: intermediate
---

# 🎯 ServiceNow Micro-Certification — Automated Test Framework (ATF)

## Exam Details

| Field | Value |
|---|---|
| Certification | Micro-Certification — Automated Test Framework |
| Issued by | ServiceNow |
| Delivery | Non-proctored, via Now Learning |
| Duration | 60 minutes |
| Questions | ~23 (multiple choice — select most correct answer) |
| Prerequisite | Complete the ATF Micro-Certification Assessment training path on Now Learning before registering |
| Registration | Now Learning → search "ATF Micro-Certification Assessment" |

**Official training path:** https://nowlearning.servicenow.com

---

## Exam Blueprint

| # | Domain | Weight | Est. Questions |
|---|---|---|---|
| 1 | ATF Overview | 13% | ~3 |
| 2 | Run ATF Tests and Test Suites | 17% | ~4 |
| 3 | Create ATF Tests | 13% | ~3 |
| 4 | Create ATF Test Suites | 13% | ~3 |
| 5 | Schedule ATF Test Suites | 9% | ~2 |
| 6 | ATF Administration | **22%** ⚑ | ~5 |
| 7 | ATF Tips and Techniques | 13% | ~3 |

> ⚑ **Domain 6 (Administration) carries the highest weight at 22%.** Prioritise Custom Test Step Configurations and ATF system properties when studying.

---

## Domain 1 — ATF Overview (13%)

### What is ATF?

The **Automated Test Framework (ATF)** is a ServiceNow-native application that enables teams to create, run, and manage automated tests against a ServiceNow instance. It validates that platform behaviour remains correct after upgrades, patches, or configuration changes.

> Source: [ServiceNow Docs — Automated Test Framework](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/concept/automated-test-framework.html)

### Goals and Benefits

- **Regression testing** — detect breakage introduced by upgrades, patches, or customisations
- **Upgrade confidence** — automatically verify functionality after ServiceNow releases
- **Consistency** — tests execute identically every run; eliminates manual variability
- **Shift-left quality** — catch defects before they reach production
- **CI/CD integration** — ATF can be invoked by external pipelines via the REST API

### Key Constraints

- ATF tests must run on a **sub-production instance** — never production
- Client-side tests require a **Client Test Runner** (browser component)
- ATF is not a load/performance testing tool
- ATF tests the ServiceNow platform from within — it is not an external testing framework like Selenium

---

## Domain 2 — Run ATF Tests and Test Suites (17%)

### Running Tests

```
Navigator: Automated Test Framework → Tests
Open a Test record → click Run Test
```

- Tests can be run individually or triggered as part of a Test Suite
- Each execution creates a **Test Result** record

### Running Test Suites

```
Navigator: Automated Test Framework → Test Suites
Open a Test Suite record → click Run Test Suite
```

- Creates a **Test Suite Result** record aggregating all child test outcomes
- Tests within a suite execute in the order defined by the **Order** field

### Test Result Statuses

| Status | Meaning |
|---|---|
| Success | All steps passed |
| Failure | One or more assertion steps did not match expected value |
| Error | A script exception occurred during execution |
| Cancelled | Execution was manually stopped |

### Analysing Results

- Navigate to the **Test Result** record for overall status
- Drill into **Test Step Result** records to identify exactly which step failed
- **Screenshots** can be captured automatically on failure (requires configuration)
- The **Output Variables** tab shows data passed between steps — useful for debugging

> Source: [ServiceNow Docs — Run ATF tests](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/task/run-atf-test.html)

### Client Test Runner (CTR)

> ⚑ **Frequently examined.** Know what it is, when it is required, and how it communicates.

The **Client Test Runner** is a browser-based component that executes **client-side test steps** — those that interact with the ServiceNow UI (forms, UI actions, client scripts). It opens a dedicated browser window and simulates user interactions.

**When it is required:**
- Any step that opens a form
- Set Field Value (UI)
- Submit a form
- Click a UI Action (button/link)
- Steps that test Client Scripts or UI Policies

**Communication:** The CTR communicates with the server-side ATF engine via **WebSocket**.

**Operational rules:**
- The CTR browser window must remain open for the duration of the test run
- Configure the CTR URL via: ATF → Administration → Properties → `atf.client.test.runner.url`

> Source: [ServiceNow Docs — Client Test Runner](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/concept/client-test-runner.html)

### Server-side vs Client-side Steps

| Server-side (no CTR needed) | Client-side (CTR required) |
|---|---|
| Create or Update Record | Open a Form |
| Assert Record Fields (table query) | Set Field Value (UI) |
| Run Server Script | Submit Record |
| REST Step | Click UI Action |
| Impersonate User | Wait for Form to Load |

---

## Domain 3 — Create ATF Tests (13%)

### Test Structure

```
Test
└── Test Step 1  (uses a Test Step Configuration)
└── Test Step 2  (uses a Test Step Configuration)
└── Test Step N
```

A **Test** is an ordered sequence of Test Steps that validates a specific scenario. Tests can accept **Input Variables** and expose **Output Variables** for use by parent Test Suites.

### Key Test Record Fields

| Field | Purpose |
|---|---|
| Name | Descriptive identifier |
| Active | Must be checked for the test to execute within a suite |
| Run as | Impersonated user — test executes under this user's permissions |
| Rollback | When enabled, ATF reverts DB changes made during the run |
| Input Variables | Parameters passed into the test from a Suite |
| Output Variables | Values exposed to subsequent steps or parent Suite |

### Test Steps

- Each step references a **Test Step Configuration (TSC)** — the template defining what the step does
- Steps execute in **Order** field sequence
- **Output variables** from Step N can be consumed as inputs to Step N+1

### Test Step Configuration (TSC)

A **Test Step Configuration** is the reusable definition of a step type. It contains:

- The step's **HTML form template** (inputs presented to the test designer)
- The **execution script** (server-side or client-side JavaScript)
- **Input variable** definitions — what the step accepts
- **Output variable** definitions — what the step produces

> ⚑ Understand the distinction: a **Test Step** is the instance within a specific test (with its configured values); the **Test Step Configuration** is the reusable template defining the step type's logic.

### Common Built-in Test Step Types

| Step | Type | Purpose |
|---|---|---|
| Create or Update Record | Server | Insert or modify a record on any table |
| Assert Record Fields | Server | Verify field values on a record match expected values |
| Open a Form | Client | Navigate to a specific record form |
| Set Field Value | Client | Set a field on the open form |
| Submit Record | Client | Submit the current form |
| Run Server Script | Server | Execute custom GlideScript |
| REST Step | Server | Make an outbound REST call and assert response |
| Log | Server | Write to the test execution log |
| Wait for Condition | Server | Pause until a GlideRecord condition is true |
| Impersonate User | Server | Switch running user mid-test |

> Source: [ServiceNow Docs — ATF step types](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/reference/atf-step-configurations.html)

---

## Domain 4 — Create ATF Test Suites (13%)

### What is a Test Suite?

A **Test Suite** groups related Tests (and optionally child Test Suites) for collective execution. Suites are the primary unit of scheduled and organised test execution.

```
Navigator: Automated Test Framework → Test Suites → New
```

### Suite Configuration

- Add Tests via the **Tests related list**
- Set the **Order** field on each test entry to control sequence
- Test Suites can be **nested** — a Suite can contain other Suites

### Suite Hierarchy Pattern

```
Master Regression Suite
├── Incident Management Suite
│   ├── Test: Incident auto-assign on P1
│   └── Test: Incident SLA breach notification
├── Change Management Suite
│   └── Test: CAB approval workflow
└── Service Catalog Suite
    └── Test: Order laptop request
```

### Suite vs Test

| Test Suite | Test |
|---|---|
| Contains Tests (or sub-Suites) | Contains Test Steps |
| Produces a Suite Result record | Produces a Test Result record |
| Used for grouping and scheduling | Validates a specific scenario |
| Can be scheduled | Runs manually or via a Suite |

### Rollback in Suites

- The **Rollback** setting on each Test controls whether DB changes from that test are reverted
- Enable rollback to prevent test data accumulating across suite runs
- Not all table operations can be rolled back (e.g., audit log entries)

---

## Domain 5 — Schedule ATF Test Suites (9%)

### Why Schedule?

- Automate post-upgrade regression validation
- Integrate with CI/CD deployment gates
- Run smoke tests on a recurring cadence

### Schedule Types

| Type | Trigger |
|---|---|
| **Scheduled (time-based)** | Recurring cron-style schedule (daily, weekly, etc.) |
| **Upgrade trigger** | Fires automatically when a ServiceNow upgrade is applied to the instance |
| **API-triggered** | Invoked via the ServiceNow REST API from an external CI/CD pipeline |

> ⚑ Know the difference between a time-based schedule and the **Upgrade trigger** — these are distinct options on the Schedule record.

### Configuring a Schedule

```
1. Open the Test Suite record
2. Navigate to the Schedules related list
3. Create a new Schedule record
4. Set: Frequency, Time Zone, Run as user, Trigger type
```

**Important:** Scheduled runs that include client-side test steps still require a **Client Test Runner** to be active and connected when the schedule fires.

---

## Domain 6 — ATF Administration (22%) ⚑

> **Highest-weighted domain. Expect ~5 questions here.**

### System Properties

```
Navigator: Automated Test Framework → Administration → Properties
```

| Property | Purpose |
|---|---|
| `atf.enabled` | Master on/off switch for ATF on this instance. Set to **false** in production. |
| `atf.rollback.enabled` | Enables the rollback/teardown feature for tests |
| `atf.client.test.runner.url` | URL of the Client Test Runner browser endpoint |
| `sn_atf.hide_all_tests_from_operators` | Hides test records from users without admin/designer roles |

> ⚑ **`atf.enabled` must be `false` in production.** This is a frequently tested administration rule.

### ATF Roles

| Role | Capabilities |
|---|---|
| `atf_test_admin` | Full ATF access — create, edit, delete Tests, Suites, and Test Step Configurations |
| `atf_test_designer` | Create and edit Tests and Suites — cannot modify Test Step Configurations |
| `atf_test_runner` | Run tests and view results only — cannot create or edit |
| `admin` | Inherits full ATF privileges |

> Source: [ServiceNow Docs — ATF user roles](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/reference/atf-roles.html)

### Custom Test Step Configurations

Creating a custom TSC extends ATF with reusable step types beyond the built-in library.

```
Navigator: Automated Test Framework → Administration → Test Step Configurations → New
```

**Key fields when creating a custom TSC:**

| Field | Description |
|---|---|
| Name | Descriptive step type name shown in the step picker |
| Step environment | **Server** (runs GlideScript on server) or **Client** (runs in CTR browser) |
| HTML Template | Angular-based form definition — defines the inputs presented to the test designer |
| Step Execution Script | JavaScript executed when this step runs |
| Input Variables | Variable definitions the step accepts |
| Output Variables | Variable definitions the step exposes to subsequent steps |

**Table:** Custom TSCs are stored in `sys_atf_step_config`

**Execution Script APIs:**

```javascript
// Read an input variable
var recordId = inputs.record_sys_id;

// Set an output variable
outputs.result_value = gr.getValue('state');

// Pass/fail the step
stepResult.setSuccess();
stepResult.setFailed('Expected state 6, got ' + actual);

// Assertion helper
assertEqual(expected, actual, 'State did not match expected resolved value');
```

> Source: [ServiceNow Docs — Create a custom test step configuration](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/task/create-custom-step-config.html)

### Maintaining the ATF Environment

- **Disable ATF in production:** `atf.enabled = false` — prevents accidental test execution against live data
- **Data isolation:** Use rollback or dedicated test data; never depend on production data
- **Update Sets:** ATF Tests and Suites are customisations — capture them in Update Sets and promote through environments like any other config
- **Application Scope:** Tests belong to an app scope. Verify your scope before creating tests in a scoped app context
- **Test independence:** Tests should not depend on data created by other tests — each test creates its own prerequisites

### ATF Key Tables

| Table | Content |
|---|---|
| `sys_atf_test` | Test records |
| `sys_atf_step` | Test Step records (instances within a test) |
| `sys_atf_step_config` | Test Step Configuration records (built-in and custom) |
| `sys_atf_test_suite` | Test Suite records |
| `sys_atf_test_result` | Test Result records (execution outcomes) |
| `sys_atf_test_suite_result` | Test Suite Result records |

---

## Domain 7 — ATF Tips and Techniques (13%)

### Core Best Practices

- **Independent tests** — each test sets up its own data; no test depends on another test having run
- **Atomic tests** — one test validates one scenario (one business rule, one workflow path)
- **Dynamic test data** — never hardcode `sys_id` values; create records in the test and pass the `sys_id` via output variables
- **Enable rollback** — keeps the instance clean between runs
- **Descriptive names** — name tests so a failure is self-describing (e.g., `Incident – SLA breach fires at 80% elapsed`)
- **Use impersonation** — test role-based access controls by running steps as a restricted user

### Chaining Steps with Output Variables

```
Step 1: Create Record (Incident)
        → Output: sys_id → incident_sys_id

Step 2: Open a Form
        → Input: incident_sys_id (from Step 1 output)

Step 3: Assert Field Value
        → Input: incident_sys_id

Step 4: Submit
Step 5: Assert Record Fields (server-side assertion on final state)
```

### Test Suite Organisation

| Suite Type | Purpose | Run Frequency |
|---|---|---|
| Smoke Suite | 10–20 critical path tests | After every deployment / daily |
| Module Suite | All tests for one application area | Before promoting an Update Set |
| Regression Suite | Full test library | After upgrades, before UAT |

### Improving Reliability

- Use **Wait for Condition** steps instead of fixed-time delays — polls until a condition is met rather than sleeping for a fixed period
- Avoid asserting exact timestamps — these change and cause intermittent failures
- For async operations (Flow Designer flows, Business Rules with async mode) — add a Wait for Condition step that polls for the expected record state
- After a ServiceNow upgrade: re-run suites; if client-side steps fail, check whether UI selectors or field labels changed

---

## Execution Flow (End to End)

```
1. User triggers run (manual click or schedule fires)
2. ATF engine loads the test, resolves variables, captures rollback snapshot
3. Steps execute in Order sequence
   ├── Server steps → execute on ServiceNow application server
   └── Client steps → sent to CTR via WebSocket → browser executes UI action → result returned
4. Each assertion step: compares actual vs expected → sets step status (pass/fail)
5. Output variables from each step are available to subsequent steps
6. Test completes → Test Result record created with final status
7. Rollback executes (if enabled) → DB changes from this test are reverted
8. Suite Result updated with this test's outcome
```

---

## Study Resources

| Resource | URL / Notes |
|---|---|
| **ServiceNow Docs — ATF** | https://docs.servicenow.com — search "Automated Test Framework" — filter to your instance version |
| **Now Learning — ATF path** | https://nowlearning.servicenow.com — complete the ATF Micro-Certification Assessment training path (prerequisite) |
| **ServiceNow Developer Portal** | https://developer.servicenow.com — free Personal Developer Instance for hands-on ATF practice |
| **ServiceNow Community** | https://www.servicenow.com/community/ — ATF-tagged posts for real-world usage patterns |
| **Chuck Tomasi — SN Pro Tips** | https://snprotips.com — practical ServiceNow content by multi-year Community MVP |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals, Update Sets, roles
- ServiceNow-Micro-Cert-Flows — Flow Designer; ATF can test flows end-to-end
- ServiceNow-Flow-Designer — Flow reference used when writing ATF tests that validate flow outcomes

---

## References

- ServiceNow Official Documentation — Automated Test Framework: https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/concept/automated-test-framework.html
- ServiceNow Docs — Client Test Runner: https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/concept/client-test-runner.html
- ServiceNow Docs — ATF Roles: https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/reference/atf-roles.html
- ServiceNow Docs — Custom Test Step Configurations: https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/task/create-custom-step-config.html
- Now Learning — ATF Micro-Certification Assessment: https://nowlearning.servicenow.com

---

## Migrating ATF Tests Between Instances — Users and Groups

> This section addresses a gap: what to consider about users and groups when ATF tests are moved between instances via Update Sets.

When ATF Tests and Suites are promoted between instances (dev → test → UAT) via Update Sets, the **configuration records move but user and group data does not**. This is one of the most common causes of tests that pass in dev but fail after migration.

> Source: [ServiceNow Docs — Automated Test Framework](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/concept/automated-test-framework.html) · [ServiceNow Docs — Update Sets](https://docs.servicenow.com/bundle/washingtondc-application-development/page/build/system-update-sets/concept/c_SystemUpdateSets.html)

### What Moves — and What Doesn't

| Item | Moves via Update Set? | Notes |
|---|---|---|
| Test records (`sys_atf_test`) | ✅ Yes | Configuration moves |
| Test Step records | ✅ Yes | Configuration moves |
| Test Suite records | ✅ Yes | Configuration moves |
| Custom Test Step Configurations | ✅ Yes | Configuration moves |
| ATF role assignments (users/groups) | ❌ No | Must be set up on each instance independently |
| Users referenced in "Run as" fields | ❌ No | User must exist on target instance |
| Groups referenced inside test steps | ❌ No | Group data is not config — must exist on target |
| Scheduled Suite "Run as" user | ❌ No | Must exist on target with correct roles |

### The "Run as" User Problem

The **Run as** field on a Test record stores a direct `sys_id` reference to a `sys_user` record. `sys_user` records are **data**, not configuration — they are not captured in Update Sets.

**If that user does not exist on the target instance:**
- The test may fail to execute
- It may run as the wrong user, producing incorrect access-control results
- Scheduled suites using that user will error

**Solution:** Use a dedicated ATF service account created consistently across all instances.

```
Best practice:
- Create a user (e.g. atf.runner@yourcompany.com) on every instance
- Assign it the atf_test_runner role on every instance
- Set this account as "Run as" on tests that don't require specific user context
- For role-testing scenarios: use an Impersonate User step mid-test rather
  than setting a specific user in the "Run as" field
```

### ATF Role Assignments — Per Instance

ATF roles are user/group assignments — they live in `sys_user_has_role` and `sys_group_has_role` tables, which are **data**, not configuration. They do not move in an Update Set.

After every instance promotion, verify:

| Role | Who needs it |
|---|---|
| `atf_test_admin` | ATF tool owners, senior developers who manage step configurations |
| `atf_test_designer` | Developers and QA engineers who create and edit tests |
| `atf_test_runner` | Service accounts used for scheduled execution; QA engineers who only run tests |

**Assign roles to groups, not individual users** — this is the ServiceNow platform best practice and makes instance-to-instance consistency easier to manage.

### Hardcoded Group / User sys_ids in Test Steps

If a test step hardcodes a `sys_id` for a user or group (e.g., in a Create Record step setting `assignment_group`), that value will only be valid on the instance it was created on. On the target instance, the same group will have a different `sys_id`.

**Wrong approach:**
```javascript
// Hardcoded sys_id — will break on any other instance
inputs.assignment_group = '8a5055c9c61122780043563ef53438e3';
```

**Correct approach — dynamic lookup by name:**
```javascript
// In a Run Server Script step:
var gr = new GlideRecord('sys_user_group');
gr.get('name', 'Service Desk');
outputs.group_sys_id = gr.getUniqueValue();
// Then pass outputs.group_sys_id into the Create Record step
```

This keeps tests portable across all instances regardless of sys_id differences.

### Post-Migration Verification Checklist

After promoting ATF tests via Update Set to a new instance:

```
[ ] "Run as" user exists on target instance
[ ] "Run as" user has the atf_test_runner role on target instance
[ ] All Impersonate User step targets exist as users on target
[ ] Scheduled Suite "Run as" users exist and have correct roles
[ ] ATF roles assigned to correct groups on target (atf_test_admin, atf_test_designer, atf_test_runner)
[ ] Any group references inside test steps resolve by name (not hardcoded sys_id)
[ ] atf.enabled = true on target (sub-prod), false on production
[ ] Client Test Runner URL configured correctly on target if client-side steps are used
```

> ⚑ **Exam angle (Domain 6 — Administration, 22%):** Scenario questions may describe a test that works in dev but fails after migration to test or UAT. The root cause is almost always: missing "Run as" user, unassigned ATF roles, or unresolved group/user references. The correct answer involves verifying user existence and role assignment on the target instance — not re-creating the test.

---

## ATF Roles — Complete Reference

> Source: [ServiceNow Docs — ATF Roles](https://docs.servicenow.com/bundle/washingtondc-application-development/page/administer/auto-test-framework/reference/atf-roles.html)

### The Four ATF Roles

| Role | Full Name | Capability Level |
|---|---|---|
| `atf_test_admin` | ATF Test Admin | Full ATF access |
| `atf_test_designer` | ATF Test Designer | Create/edit tests and suites only |
| `atf_test_runner` | ATF Test Runner | Run tests and view results only |
| `admin` | Platform Administrator | Inherits full ATF access via platform admin role |

---

### Granular Permissions Per Role

| Action | atf_test_runner | atf_test_designer | atf_test_admin | admin |
|---|---|---|---|---|
| View test records | ✅ | ✅ | ✅ | ✅ |
| View test results | ✅ | ✅ | ✅ | ✅ |
| Run a test manually | ✅ | ✅ | ✅ | ✅ |
| Run a test suite | ✅ | ✅ | ✅ | ✅ |
| Create a new test | ❌ | ✅ | ✅ | ✅ |
| Edit a test | ❌ | ✅ | ✅ | ✅ |
| Delete a test | ❌ | ❌ | ✅ | ✅ |
| Create a test suite | ❌ | ✅ | ✅ | ✅ |
| Edit a test suite | ❌ | ✅ | ✅ | ✅ |
| Delete a test suite | ❌ | ❌ | ✅ | ✅ |
| View Test Step Configurations | ✅ (read-only) | ✅ (read-only) | ✅ | ✅ |
| Create/Edit/Delete Test Step Configurations | ❌ | ❌ | ✅ | ✅ |
| Manage ATF system properties | ❌ | ❌ | ✅ | ✅ |

> ⚑ **Key exam distinction:** `atf_test_designer` can create and edit Tests and Suites but **cannot modify Test Step Configurations**. That boundary is the most commonly tested role difference.

---

### Role Hierarchy — Are the Roles Nested?

The ATF roles are **not strictly nested/inherited** from each other — they are separate grants. Assigning `atf_test_admin` does **not** automatically grant `atf_test_designer` or `atf_test_runner` as named roles on the user record. However, `atf_test_admin` has a **superset of capabilities** — a user with only `atf_test_admin` can do everything `atf_test_designer` and `atf_test_runner` can do, plus more.

In practice this means:
- A user who only needs to **run** tests → assign `atf_test_runner`
- A user who needs to **create and run** tests → assign `atf_test_designer` (running is included in designer access)
- A user who needs **full control** including managing TSCs → assign `atf_test_admin`
- You do **not** need to stack multiple ATF roles on the same user — assign the highest role needed

```
Capability superset (most → least):
atf_test_admin > atf_test_designer > atf_test_runner
```

---

### The `admin` Role and ATF

The platform `admin` role is a ServiceNow system role that grants unrestricted access to the entire instance. It inherits full ATF privileges automatically — there is no separate ATF role needed for an admin user.

**Difference between `admin` and `atf_test_admin`:**

| | `atf_test_admin` | `admin` |
|---|---|---|
| Full ATF access | ✅ | ✅ |
| Access outside ATF module | ❌ (limited to ATF) | ✅ (full platform) |
| Best for | Dedicated ATF tool owners / test leads | Platform administrators |

Use `atf_test_admin` for team members whose scope is ATF testing. Reserve `admin` for platform administrators — it grants far broader access than ATF work requires (principle of least privilege).

---

### What "Operators" Means — `sn_atf.hide_all_tests_from_operators`

In ServiceNow, **"operators"** refers to standard ITSM end-users — typically users who hold only the `itil` role (service desk agents, fulfillment staff). They have no ATF roles and have no need to see test records.

The `sn_atf.hide_all_tests_from_operators` property, when set to `true`, hides ATF test and suite records from these users so ATF content does not appear in their navigation or list views.

```
Set to: true  → ATF records hidden from users without an ATF role
Set to: false → ATF records visible to anyone with table-level read access

Navigator: ATF → Administration → Properties → sn_atf.hide_all_tests_from_operators
```

This is a housekeeping/security property — it prevents non-ATF users from accidentally seeing or interacting with test infrastructure.

---

### How to Assign ATF Roles to a User

```
Method 1 — Directly on a user record:
  1. Navigator: System Security → Users and Groups → Users
  2. Open the user record
  3. Scroll to the Roles related list
  4. Click Edit → search for the ATF role → Add to list → Save

Method 2 — Via a Group (recommended):
  1. Navigator: System Security → Users and Groups → Groups
  2. Open the group (e.g. "ATF Designers")
  3. Scroll to the Roles related list
  4. Click Edit → add the ATF role → Save
  5. Add users to the group — they inherit the role automatically

Method 3 — Via the user record Roles tab (UI16/Next Experience):
  1. Open user record
  2. Roles tab → Edit
  3. Search atf_ → select role → Save
```

> ⚑ **Best practice:** Always assign roles to **groups**, not individual users. This makes onboarding, offboarding, and cross-instance consistency much easier — add the group on each instance, then manage membership rather than individual role grants.

---

### Which Role for Which Job Function

| Job Function | Assign This Role | Reasoning |
|---|---|---|
| Service desk agent / ITSM user | *(none)* — set `hide_all_tests_from_operators = true` | No ATF access needed |
| Developer writing tests | `atf_test_designer` | Create/edit tests and suites; run to verify |
| QA engineer running existing tests | `atf_test_runner` | Run and view results; cannot accidentally modify tests |
| ATF tool owner / test lead | `atf_test_admin` | Manages TSCs, can delete, full oversight |
| Scheduled execution service account | `atf_test_runner` | Minimum role needed to execute scheduled suite runs |
| Platform administrator | `admin` (already has full ATF access) | No additional ATF role needed |

---

### Common Role-Related Exam Scenarios

**Scenario 1:** A developer reports they can run tests but cannot create new ones. What role do they have?
→ `atf_test_runner`. They need `atf_test_designer` to create tests.

**Scenario 2:** A test lead needs to create a custom Test Step Configuration. What is the minimum role required?
→ `atf_test_admin`. `atf_test_designer` cannot modify TSCs.

**Scenario 3:** After migrating tests to the UAT instance, the scheduled suite fails. The test worked in dev. What is the most likely role-related cause?
→ The `atf_test_runner` role has not been assigned to the service account used in the Schedule's "Run as" field on the UAT instance. Role assignments do not migrate via Update Sets.

**Scenario 4:** You want to prevent service desk agents from seeing ATF test records in their lists. What should you configure?
→ Set `sn_atf.hide_all_tests_from_operators = true` in ATF Administration → Properties.

**Scenario 5:** A new QA team member needs to run the regression suite daily but must not be able to edit any tests. What role do you assign?
→ `atf_test_runner` — can run tests and view results only.

---
<- [[Certs-MOC]]