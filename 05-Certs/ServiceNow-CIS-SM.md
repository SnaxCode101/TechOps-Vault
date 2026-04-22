---
title: "ServiceNow CIS-SM — Certified Implementation Specialist: Service Mapping"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-SM, service-mapping, CMDB, discovery, study-guide, job-aid, intermediate]
source: "ServiceNow official Service Mapping documentation (docs.servicenow.com), Now Learning CIS-SM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-SM — Certified Implementation Specialist: Service Mapping

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Service Mapping |
| Abbreviation | CIS-SM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | Service Mapping Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; CMDB knowledge; network/application architecture understanding |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official Service Mapping docs:** https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/service-mapping/concept/c_ServiceMappingConcept.html

---

## What CIS-SM Validates

CIS-SM certifies you can **implement and configure ServiceNow Service Mapping** — automatically discovering application services and mapping relationships between CIs (servers, databases, load balancers, applications) to build accurate service maps in the CMDB.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| Service Mapping Architecture | Entry points, patterns, probes, sensors |
| Discovery Integration | How Service Mapping extends Discovery |
| Service Map Configuration | Creating and managing application services |
| Patterns | Pattern Editor, horizontal/top-down discovery |
| CMDB Population | CI creation, relationships, service map validation |
| Troubleshooting | Mapping issues, logs, debugging patterns |
| Tag-based Service Mapping | Cloud-native, tag-based discovery (AWS, Azure) |

---

## Core Concepts — Study & Job Aid

### Service Mapping vs Discovery

| Concept | Discovery | Service Mapping |
|---|---|---|
| Purpose | Find infrastructure CIs | Map application services end-to-end |
| Starting point | IP ranges | Application entry points (URL, hostname) |
| Output | Individual CI records | CI records + relationship map |
| Scope | Infrastructure layer | Business service layer |

Service Mapping **depends on Discovery** — Discovery must populate the CMDB with infrastructure CIs before Service Mapping can relate them into services.

---

### Key Architecture Components

**Entry Points:**
- The starting point for a service map — typically a URL, hostname, or IP that represents the user-facing application
- Example: `https://crm.company.com:443`

**Patterns:**
- Rules that define how to discover a specific application component
- ServiceNow ships with patterns for common technologies: Apache, Tomcat, JBoss, Oracle, SQL Server, IIS, nginx
- Custom patterns written in **Pattern Designer**

**Probes and Sensors:**
- **Probe** — sends a request to a CI (SSH, WMI, SNMP, REST) to collect raw data
- **Sensor** — processes the probe response and populates CMDB fields
- Service Mapping uses application-layer probes to detect processes and connections

**MID Server:**
- Required for Service Mapping — must be in-network to reach target systems
- Navigator: MID Server → Servers — verify MID Server is Up and validated

---

### Creating an Application Service

```
Navigator: Service Mapping → Services → Application Services → New
Fields:
  Name:        CRM Application Service
  Entry Points: add URL / hostname entry points
  Credentials: configure for SSH (Linux), WMI (Windows), SNMP (network)

After save → click "Start Mapping" to trigger the service map process
```

**Mapping process flow:**
```
Entry point defined
→ Service Mapping sends probe to entry point
→ Pattern matches application stack (e.g. nginx → Tomcat → Oracle)
→ CIs created/updated in CMDB
→ Relationships created (nginx "Runs on" Server, Tomcat "Depends on" Oracle)
→ Service Map visualised in topology view
```

---

### Pattern Editor

```
Navigator: Service Mapping → Administration → Patterns → Application Patterns
```

**Pattern structure:**
```
Pattern
├── Identification section (find the CI on a host)
├── Connection section (find downstream connections)
└── Attribute section (populate CI fields from probe data)
```

**Pattern types:**
| Type | Purpose |
|---|---|
| Application Pattern | Discovers application-layer components (web server, app server, DB) |
| Connection Pattern | Discovers TCP/IP connections between CIs |
| Infrastructure Pattern | Discovers infrastructure details (CPU, memory, NICs) |

**Custom pattern example (identify nginx process):**
```
Step: Run command → "ps aux | grep nginx"
Condition: output contains "nginx: master process"
→ Create CI of class "Linux/Unix Web Server (nginx)"
→ Populate CI name, version from output
```

---

### Tag-Based Service Mapping (Cloud)

For AWS, Azure, GCP environments:
```
Navigator: Service Mapping → Administration → Tag-Based Service Mapping
```

- Uses cloud resource **tags** (e.g. `app=crm`, `env=prod`) to group CIs into services
- No entry point required — tags drive service groupings
- Requires: ITOM Visibility licence + cloud MID Server with cloud credentials

---

### Service Map Validation

**Checking map completeness:**
```
Navigator: Service Mapping → Services → [select service] → View Map
- Green CI = mapped successfully
- Yellow CI = partially mapped (missing attributes)
- Red CI = mapping failed or CI not found
```

**Troubleshooting failed mappings:**
```
Navigator: Service Mapping → Logs → Mapping Logs
- Check pattern matching errors
- Check credential failures (401, access denied)
- Check MID Server connectivity
- Check discovery_status on CI record
```

---

### CMDB Relationships Created by Service Mapping

| Relationship Type | Example |
|---|---|
| `Runs on::Runs` | Apache web server runs on Linux server |
| `Depends on::Used by` | Tomcat app server depends on Oracle DB |
| `Hosted on::Hosts` | Virtual machine hosted on ESX server |
| `Connected by::Connects` | Application connected by load balancer |

Relationships visible in: CMDB → CI record → Relationships tab → or Service Map topology view.

---

## Common Exam Scenarios

**Scenario 1:** A service map is not discovering the Oracle database tier. The web and app layers map correctly. What should you check first?
→ Verify the **credentials** for the database host — Oracle discovery typically requires JDBC credentials + SSH/WMI access. Check the Mapping Logs for credential failures.

**Scenario 2:** Your organisation uses AWS with tags to identify production services. How should you configure Service Mapping?
→ Use **Tag-Based Service Mapping** — configure tag rules matching `env=prod` to auto-group cloud CIs into application services.

**Scenario 3:** A custom application runs on a non-standard port. ServiceNow's default patterns don't detect it. What do you create?
→ A **custom Application Pattern** in the Pattern Editor — define identification steps that match the process name and port, then create the CI accordingly.

**Scenario 4:** A service map shows yellow CIs for several application servers. What does this indicate?
→ **Partial mapping** — the CIs were discovered but some attributes could not be populated (e.g. version, owner). Review the pattern's attribute section and probe output.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — Service Mapping path** | https://nowlearning.servicenow.com — Service Mapping Fundamentals |
| **ServiceNow Docs — Service Mapping** | https://docs.servicenow.com — search "Service Mapping" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-ITSM — ITSM uses Service Maps for impact analysis
- ServiceNow-CIS-Disco — Discovery (prerequisite for Service Mapping)

---

## References

- ServiceNow Docs — Service Mapping: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/service-mapping/concept/c_ServiceMappingConcept.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]