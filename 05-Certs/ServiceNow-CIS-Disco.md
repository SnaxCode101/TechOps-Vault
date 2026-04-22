---
title: "ServiceNow CIS-Disco — Certified Implementation Specialist: Discovery"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-Disco, discovery, CMDB, ITOM, MID-server, study-guide, job-aid, intermediate]
source: "ServiceNow official Discovery documentation (docs.servicenow.com), Now Learning CIS-Discovery training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-Disco — Certified Implementation Specialist: Discovery

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Discovery |
| Abbreviation | CIS-Disco |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | ITOM: Discovery Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; networking and infrastructure knowledge |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official Discovery docs:** https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/discovery/concept/c_Discovery.html

---

## What CIS-Disco Validates

CIS-Disco certifies you can **implement and configure ServiceNow Discovery** — scanning networks to find CIs (servers, network devices, virtual machines, cloud resources) and populating the CMDB automatically.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| Discovery Architecture | MID Server, probes, sensors, shazzam |
| Discovery Schedules | IP ranges, schedule configuration, triggers |
| Credentials | Credential types, credential affinity, credential stores |
| Probes & Sensors | Probe types (port scan, classify, explore, identify), sensor processing |
| CMDB Population | CI classes, identification rules, reconciliation |
| Cloud Discovery | AWS, Azure, GCP discovery via cloud accounts |
| Troubleshooting | Discovery logs, ECC queue, failed discoveries |

---

## Core Concepts — Study & Job Aid

### Discovery Architecture

```
ServiceNow Instance
    ↓ ECC Queue (outbound job)
MID Server (on-premise, in target network)
    ↓ Probes sent to target IPs
Target Systems (servers, network devices, VMs)
    ↑ Probe responses
MID Server
    ↑ Results sent to ECC Queue (inbound)
ServiceNow Instance
    → Sensors process data → CMDB updated
```

**MID Server role:**
- Communicates with target systems inside the network
- Executes probes (SSH, WMI, SNMP, REST)
- Sends results back to the instance via HTTPS
- Navigator: MID Server → Servers — must be **Validated** and **Up**

---

### Discovery Process Phases

```
Phase 1 — Shazzam (Port Scan)
  → Scans IP range for open ports
  → Identifies which hosts are alive and what services are running

Phase 2 — Classification
  → Determines the device type (Linux server, Windows server, Cisco switch, etc.)
  → Selects the correct probe set based on classification

Phase 3 — Identification
  → Checks if CI already exists in CMDB (by serial number, hostname, or other identifier)
  → Matches to existing CI or creates new CI

Phase 4 — Exploration
  → Runs deep probes to collect detailed attributes (CPU, RAM, OS version, installed software, NICs)
  → Populates CMDB CI fields

Phase 5 — Sensor Processing
  → Instance-side processing of probe results
  → Updates CMDB records
```

---

### Discovery Schedules

```
Navigator: Discovery → Discovery Schedules → New
```

**Key schedule fields:**

| Field | Purpose |
|---|---|
| Name | Schedule name |
| Discover | What to discover: CIs, Cloud |
| IP Ranges | CIDR notation ranges to scan (e.g. 10.0.1.0/24) |
| MID Server | Which MID Server to use |
| Schedule | When to run (cron expression or interval) |
| Credentials | Credential sets to try against discovered hosts |

**Discovery types:**
| Type | Purpose |
|---|---|
| CIs | Standard infrastructure discovery (servers, network, storage) |
| Cloud | AWS, Azure, GCP resource discovery |
| Service Mapping | Used by Service Mapping (application layer) |

---

### Credentials

**Credential types:**
| Type | Used for |
|---|---|
| SSH | Linux/Unix servers |
| WMI | Windows servers |
| SNMP | Network devices, printers |
| VMware vSphere | VMware virtualisation |
| AWS | AWS accounts |
| Azure | Azure subscriptions |
| JDBC | Databases |

**Credential configuration:**
```
Navigator: Discovery → Credentials → New
Set: Name, Credential type, Username, Password/Key
Assign to: a MID Server affinity or Discovery Schedule
```

**Credential affinity:**
- Allows specific credentials to be tried for specific IP ranges or hosts
- Reduces authentication failures and speeds up discovery

---

### CMDB CI Identification and Reconciliation

**Identification rules:**
```
Navigator: Configuration → CI Class Manager → [select class] → Identifier
```
- Define which field combination uniquely identifies a CI (e.g. serial number, IP + hostname)
- Prevents duplicate CI creation

**IRE (Identification and Reconciliation Engine):**
- Introduced to standardise CI deduplication across all data sources
- Compares incoming discovery data against existing CMDB records
- Merges duplicates and reconciles attribute priority (which source "wins" for each field)
- Precedence rules: Navigator: CMDB → Reconciliation → Precedence Rules

---

### Cloud Discovery

**Prerequisites:**
- Cloud account credentials (IAM role for AWS, service principal for Azure)
- ITOM Visibility licence

```
Navigator: Discovery → Cloud Accounts → New
Fields: Cloud type (AWS/Azure/GCP), Account ID, Credentials
After save → click "Discover" to trigger immediate discovery
```

**What cloud discovery populates:**
- Virtual machines (EC2, Azure VMs) → `cmdb_ci_vm_instance`
- Storage (S3 buckets, Azure Blob) → `cmdb_ci_cloud_object_storage`
- Networks (VPCs, subnets) → `cmdb_ci_network`
- Load balancers, security groups, databases

---

### ECC Queue

The **External Communication Channel (ECC) Queue** manages all communication between the ServiceNow instance and MID Servers.

```
Navigator: MID Server → ECC Queue
```

**ECC Queue states:**
| State | Meaning |
|---|---|
| Ready | Job waiting to be picked up by MID Server |
| Processing | MID Server is executing the probe |
| Processed | Results returned, sensors running |
| Error | Probe failed — check payload for error details |

**Troubleshooting via ECC Queue:**
- Filter by: `queue = Output` and `state = Error` to find failing probes
- Check `payload` field for the error message
- Common errors: authentication failure (wrong credentials), connection timeout (network issue), MID Server offline

---

### Discovery Logs

```
Navigator: Discovery → Discovery Log
```
- Shows all discovery activity per schedule run
- Filter by: Schedule Name, IP address, CI class, Status
- Look for: `error`, `skipped`, `warning` entries

---

### Troubleshooting Checklist

```
□ MID Server is Validated and Up
□ MID Server can reach target IPs (network connectivity / firewall rules)
□ Credentials are correct (test manually via SSH/WMI to target)
□ IP range is correct in Discovery Schedule
□ Discovery Schedule is enabled and triggered
□ ECC Queue has no stuck "Ready" jobs (MID Server may be down)
□ CI Class Manager has correct identification rules (no duplicates)
```

---

## Common Exam Scenarios

**Scenario 1:** Discovery completes but creates duplicate CIs for the same server. What is the most likely cause?
→ The **identification rule** for that CI class is not correctly defined — the system cannot match incoming data to the existing CI. Review CI Class Manager → Identifier for the class.

**Scenario 2:** Windows servers are not being discovered. Linux servers in the same subnet are discovered correctly. What should you check first?
→ **WMI credentials** — verify the Windows credential is correct and that WMI is enabled on the target servers. Check ECC Queue for authentication errors.

**Scenario 3:** A Discovery Schedule is configured and triggered, but no results appear. The ECC Queue shows jobs stuck in "Ready" state. What is the cause?
→ The **MID Server is offline or not validated** — it cannot pick up jobs. Check MID Server status and restart if needed.

**Scenario 4:** You need to discover AWS EC2 instances in a new region. What must you configure?
→ A **Cloud Account** with the appropriate AWS IAM credentials and ensure the MID Server has outbound access to AWS APIs.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — Discovery path** | https://nowlearning.servicenow.com — ITOM: Discovery Fundamentals |
| **ServiceNow Docs — Discovery** | https://docs.servicenow.com — search "Discovery" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-SM — Service Mapping (built on top of Discovery)
- ServiceNow-CIS-EM — Event Management (uses CMDB populated by Discovery)

---

## References

- ServiceNow Docs — Discovery: https://docs.servicenow.com/bundle/washingtondc-it-operations-management/page/product/discovery/concept/c_Discovery.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]