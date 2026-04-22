---
title: ServiceNow Navigator Unique Letter Shortcuts
type: reference
category: servicenow
created: 2026-03-27
updated: 2026-03-27
tags:
  - servicenow
  - navigator
  - shortcuts
  - quick-reference
  - admin
  - job-aid
  - foundational
source: ServiceNow official documentation (docs.servicenow.com), ServiceNow
  Community, Chuck Tomasi / ServiceNow training materials
difficulty: foundational
---

# ⚡ ServiceNow Navigator Unique Letter Shortcuts

## How It Works

Type **unique letters** from an application or module name into the **Navigator filter box** (top-left search field) to instantly jump to it. The letters must appear in sequence within the application path — they do not need to be consecutive.

**Example:** Type `pt in` → Navigator filters to **Script Includes**
Because "Scri**pt** **In**cludes" contains those letters in order.

> 💡 **Tip:** This works for any module — if you can't remember the exact shortcut, type any unique substring of the module name and the filter will narrow it down.

---

## System Definition

| Letters | Result |
|---|---|
| `pt in` | Script Includes (`sys_script_include.list`) |
| `ss ru` | Business Rules (`sys_script.list`) |
| `nt sc` | Client Scripts — System Definition > Client Scripts (`sys_script_client.list`) |
| `i pa` | UI Pages — System UI > Pages (`sys_ui_page.list`) |
| `i ac` | UI Actions (`sys_ui_action.list`) |
| `i po` | UI Policies (`sys_ui_policy.list`) |
| `i sc` | UI Scripts (`sys_ui_script.list`) |
| `x ma` | Fix Scripts (`sys_script_fix.list`) |
| `ta di` | Dictionary (`sys_dictionary.list`) |
| `ys ta` | Tables (`sys_db_object.list`) |
| `he ma` | Schema Map |
| `le po` | Scheduled Jobs / Scheduled Script Executors (`sysauto_script.list`) |
| `ys pr` | System Properties (`sys_properties.list`) |
| `lu gi` | Plugins (`v_plugin.list`) |
| `st fo` | Transform Maps (Import Sets > Transform Maps) |
| `mp se` | Import Sets |
| `ge lo` | Application Cross-Scope Access |
| `sp ap` | Application Registry |

---

## Update Sets

| Letters | Result |
|---|---|
| `al up` | Local Update Sets (`sys_update_set.list`) |
| `ed up` | Retrieved Update Sets (`sys_remote_update_set.list`) |
| `er ge` | Merge Update Sets |
| `at ch` | Batch Install |

---

## Security / Access Control

| Letters | Result |
|---|---|
| `ce ru` | Access Controls — ACLs (`sys_security_acl.list`) |
| `se ro` | Roles (`sys_user_role.list`) |
| `se gr` | Groups (`sys_user_group.list`) |
| `p wh` | IP Address Whitelist |
| `mi ss` | Role Assignments |

---

## User Administration

| Letters | Result |
|---|---|
| `ll ac` | All Active Transactions (`sys_user.list` filtered) |
| `se us` | Users (`sys_user.list`) |
| `ct se` | Active Sessions (`sys_user_session.list`) |
| `og se` | Log Sessions |
| `im pe` | Impersonate User |

---

## System Logs & Diagnostics

| Letters | Result |
|---|---|
| `og st` | Script Log Statements (`syslog_script.list`) |
| `og al` | All System Logs (`syslog.list`) |
| `og er` | Application Errors |
| `og wa` | Warnings |
| `og tr` | Transactions |
| `og em` | Email Logs |
| `bg jo` | Background Jobs |
| `he al` | Health Analytics |
| `he ch` | Instance Health Check |
| `ta ti` | Stats — Stats File |
| `ca ch` | Cache Flush (`cache.do`) |

---

## Service Catalog

| Letters | Result |
|---|---|
| `in it` | Maintain Items — Catalog Items (`sc_cat_item.list`) |
| `nt sc` | Catalog Client Scripts |
| `rd gu` | Order Guides (`sc_cat_item_guide.list`) |
| `ec pr` | Record Producers (`sc_cat_item_producer.list`) |
| `ri ab` | Variable Sets (`item_option_new_set.list`) |
| `ar bl` | Variables (`item_option_new.list`) |
| `at eg` | Categories (`sc_category.list`) |
| `al og` | Catalog (`sc_catalog.list`) |
| `rt fl` | Cart Fulfillment |

---

## ITSM — Incidents, Problems, Changes, Requests

| Letters | Result |
|---|---|
| `ci en` | Incidents (`incident.list`) |
| `ro le` | Problem (`problem.list`) |
| `ha ge` | Change Request (`change_request.list`) |
| `as ta` | Task (`task.list`) |
| `ew re` | New Request (`sc_request.list`) |
| `aj or` | Major Incident Management |
| `no wn` | Known Errors |
| `ha mo` | Change Models |
| `ha ca` | CAB (Change Advisory Board) |

---

## CMDB & Configuration Management

| Letters | Result |
|---|---|
| `fi ci` | Configuration Items (`cmdb_ci.list`) |
| `fi se` | CI Service (`cmdb_ci_service.list`) |
| `fi se` | Services |
| `la ti` | Relationship Types (`cmdb_rel_type.list`) |
| `db he` | CMDB Health |
| `db da` | CMDB Dashboard |
| `en if` | Identification Rules (IRE) |
| `ci cl` | CI Class Manager |

---

## Discovery (ITOM)

| Letters | Result |
|---|---|
| `sc he` | Discovery Schedules (`discovery_schedule.list`) |
| `id se` | MID Servers (`ecc_agent.list`) |
| `cc qu` | ECC Queue (`ecc_queue.list`) |
| `rd en` | Discovery Credentials (`discovery_credential.list`) |
| `sc lo` | Discovery Log |
| `tt er` | Pattern Editor |

---

## Event Management (ITOM)

| Letters | Result |
|---|---|
| `le rt` | Alerts (`em_alert.list`) |
| `ve nt` | Events (`em_event.list`) |
| `le rt` | Alert Rules |
| `in di` | CI Binding Rules |

---

## Flow Designer / Workflow / IntegrationHub

| Letters | Result |
|---|---|
| `lo de` | Flow Designer |
| `wo fl` | Workflow Editor (`wf_workflow.list`) |
| `ti vi` | Workflow Activities |
| `eg ra` | Integration Hub |
| `po ke` | Spokes |
| `ct io` | Actions |
| `ri gg` | Triggers |

---

## Notifications / Email

| Letters | Result |
|---|---|
| `fi no` | Notifications (`sysevent_email_action.list`) |
| `ma te` | Email Templates (`sysevent_email_template.list`) |
| `ut bo` | Outbound Email |
| `ai bo` | Inbound Email Actions (`sysevent_in_email_action.list`) |
| `nd em` | Send Email |
| `sy ev` | Events (`sysevent.list`) |

---

## Performance Analytics (PA)

| Letters | Result |
|---|---|
| `in di` | Indicators (`pa_indicators.list`) |
| `re ak` | Breakdowns (`pa_breakdowns.list`) |
| `as bo` | Dashboards (`pa_dashboards.list`) |
| `at co` | Data Collection Jobs |
| `co re` | Scores (`pa_scores.list`) |
| `to ma` | Automated Actions |

---

## Knowledge Management

| Letters | Result |
|---|---|
| `wl ed` | Knowledge Bases (`kb_knowledge_base.list`) |
| `wl ar` | Articles (`kb_knowledge.list`) |
| `no wl` | Knowledge Home |

---

## Service Level Management (SLAs)

| Letters | Result |
|---|---|
| `la de` | SLA Definitions (`contract_sla.list`) |
| `la ta` | SLA Tasks |
| `la br` | SLA Breaches |

---

## Asset Management (HAM/SAM)

| Letters | Result |
|---|---|
| `as se` | Assets (`alm_asset.list`) |
| `rd wa` | Hardware Assets |
| `ft wa` | Software Assets |
| `od el` | Hardware Models |
| `ft mo` | Software Models |
| `oc kr` | Stockrooms (`alm_stockroom.list`) |
| `ns fe` | Transfer Orders |
| `os al` | Disposal Orders |
| `ti le` | Entitlements (SAM) (`samp_sw_entitlement.list`) |
| `on ci` | Reconciliation (SAM) |

---

## GRC (Governance, Risk, Compliance)

| Letters | Result |
|---|---|
| `li ci` | Policies (`sn_compliance_policy.list`) |
| `nt ro` | Controls (`sn_compliance_control.list`) |
| `is sk` | Risks (`sn_risk_risk.list`) |
| `di en` | Audit Engagements (`sn_audit_engagement.list`) |
| `ho ri` | Authority Documents |
| `ta ti` | Citations |

---

## HR Service Delivery (HRSD)

| Letters | Result |
|---|---|
| `hr ca` | HR Cases (`sn_hr_core_case.list`) |
| `hr pr` | HR Profiles (`sn_hr_core_profile.list`) |
| `fe ev` | Lifecycle Events |
| `yi vi` | Activity Sets |
| `pl oy` | Onboarding |

---

## Customer Service Management (CSM)

| Letters | Result |
|---|---|
| `st ca` | Customer Cases (`sn_customerservice_case.list`) |
| `ou nt` | Accounts (`customer_account.list`) |
| `on ta` | Contacts (`customer_contact.list`) |
| `ti le` | Entitlements (`service_entitlement.list`) |

---

## Field Service Management (FSM)

| Letters | Result |
|---|---|
| `or de` | Work Orders (`wm_order.list`) |
| `or ta` | Work Order Tasks (`wm_task.list`) |
| `el ag` | Field Agents (`wm_agent.list`) |
| `er ri` | Territories (`wm_territory.list`) |
| `sp at` | Dispatcher Workspace |

---

## Strategic Portfolio Management (SPM)

| Letters | Result |
|---|---|
| `ro je` | Projects (`pm_project.list`) |
| `ro ta` | Project Tasks (`pm_project_task.list`) |
| `an de` | Demands (`pc_demand.list`) |
| `or tf` | Portfolios |
| `og ra` | Programme |
| `ou rc` | Resource Plans |

---

## Self-Service / Portal

| Letters | Result |
|---|---|
| `k bo` | Visual Task Boards (`vtb_board.list`) |
| `ss ap` | Business Applications |
| `ic se` | Service Portal |
| `or ta` | Portal Pages (`sp_page.list`) |
| `or wi` | Portal Widgets (`sp_widget.list`) |
| `or th` | Portal Themes (`sp_theme.list`) |

---

## ATF — Automated Test Framework

| Letters | Result |
|---|---|
| `es su` | Test Suites (`sys_atf_suite.list`) |
| `tf te` | Tests (`sys_atf_test.list`) |
| `es ru` | Test Runs |
| `tf st` | Test Steps |

---

## Table / .list Suffix Trick

You can also navigate directly to **any table's list view** by typing `tablename.list` in the Navigator:

```
incident.list           → All Incidents
problem.list            → All Problems
change_request.list     → All Change Requests
sc_request.list         → All Service Requests
sys_user.list           → All Users
sys_user_group.list     → All Groups
sys_user_role.list      → All Roles
cmdb_ci.list            → All Configuration Items
sys_script_include.list → All Script Includes
sys_script.list         → All Business Rules
sys_script_client.list  → All Client Scripts
sys_update_set.list     → All Local Update Sets
kb_knowledge.list       → All Knowledge Articles
em_alert.list           → All EM Alerts
pa_indicators.list      → All PA Indicators
alm_asset.list          → All Assets
sn_hr_core_case.list    → All HR Cases
wm_order.list           → All Work Orders
pm_project.list         → All Projects
```

---

## .do Utility URLs (type in Navigator or browser)

| URL | Action |
|---|---|
| `cache.do` | Flush all server-side caches |
| `stats.do` | Instance statistics (memory, DB, threads) |
| `threads.do` | Active thread overview |
| `lock.do` | Show locked records |
| `xmlstats.do` | XML statistics feed |
| `sys.scripts.do` | Run a background script (admin only) |
| `sys_log.do` | System log viewer |
| `nav_to.do?uri=` | Navigate to any URL |
| `login.do` | Force login page |
| `logout.do` | Force logout |
| `home.do` | Home page |

---

## Quick Reference Card

| I want to get to... | Type |
|---|---|
| Script Includes | `pt in` |
| Business Rules | `ss ru` |
| Client Scripts | `nt sc` |
| UI Pages | `i pa` |
| UI Actions | `i ac` |
| UI Policies | `i po` |
| Fix Scripts | `x ma` |
| Scheduled Jobs | `le po` |
| System Properties | `ys pr` |
| Local Update Sets | `al up` |
| Retrieved Update Sets | `ed up` |
| ACLs | `ce ru` |
| Roles | `se ro` |
| Groups | `se gr` |
| Users | `se us` |
| All Active Transactions | `ll ac` |
| Script Log | `og st` |
| All System Log | `og al` |
| Background Flush Cache | `ca ch` |
| Maintain Catalog Items | `in it` |
| Order Guides | `rd gu` |
| Record Producers | `ec pr` |
| Flow Designer | `lo de` |
| Workflows | `wo fl` |
| Notifications | `fi no` |
| Email Templates | `ma te` |
| PA Indicators | `in di` |
| PA Dashboards | `as bo` |
| Knowledge Bases | `wl ed` |
| Knowledge Articles | `wl ar` |
| SLA Definitions | `la de` |
| Discovery Schedules | `sc he` |
| MID Servers | `id se` |
| ECC Queue | `cc qu` |
| All Assets | `as se` |
| Stockrooms | `oc kr` |
| Visual Task Boards | `k bo` |
| ATF Tests | `tf te` |
| Dictionary | `ta di` |
| Tables | `ys ta` |
| Transform Maps | `st fo` |
| Plugins | `lu gi` |

---

## References

- ServiceNow Docs — Navigator: https://docs.servicenow.com
- ServiceNow Community — Navigator tips: https://www.servicenow.com/community/
- Chuck Tomasi / ServiceNow training resources
- Source image: provided screenshot (ServiceNow navigator unique letters cheat sheet)

---
<- [[Reference-MOC]]