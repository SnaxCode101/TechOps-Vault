---
title: "ServiceNow CIS-HAM — Certified Implementation Specialist: Hardware Asset Management"
type: cert-study
category: certification
created: 2026-03-27
updated: 2026-03-27
tags: [cert, servicenow, CIS-HAM, asset-management, hardware, CMDB, lifecycle, study-guide, job-aid, intermediate]
source: "ServiceNow official HAM documentation (docs.servicenow.com), Now Learning CIS-HAM training path, ServiceNow Learning Paths PDF 2025"
difficulty: intermediate
---

# 🎫 ServiceNow CIS-HAM — Certified Implementation Specialist: Hardware Asset Management

## Certification Details

| Field | Value |
|---|---|
| Certification | Certified Implementation Specialist — Hardware Asset Management |
| Abbreviation | CIS-HAM |
| Issued by | ServiceNow |
| Exam delivery | Proctored — via Pearson VUE |
| Duration | 90 minutes |
| Questions | 60 (multiple choice and multiple select) |
| Passing score | 70% (42/60) |
| Prerequisite training | HAM Fundamentals (Now Learning) |
| Recommended experience | ServiceNow administration; IT asset lifecycle knowledge |

**Official Now Learning path:** https://nowlearning.servicenow.com
**Official HAM docs:** https://docs.servicenow.com/bundle/washingtondc-it-asset-management/page/product/hardware-asset-management/concept/c_HardwareAssetMgmt.html

---

## What CIS-HAM Validates

CIS-HAM certifies you can **implement and configure ServiceNow Hardware Asset Management** — managing the full lifecycle of physical IT assets from procurement through disposal, integrating with procurement, CMDB, and ITSM.

> Source: ServiceNow Learning Paths PDF 2025

---

## Exam Domain Areas

> ⚑ Verify current domain weights at nowlearning.servicenow.com before your exam.

| Domain | Key Topics |
|---|---|
| HAM Architecture | Asset vs CI, asset record structure, ITAM data model |
| Asset Lifecycle | Procurement → Deploy → Maintain → Retire → Dispose |
| Procurement Integration | Purchase orders, receiving, asset creation |
| Model Management | Hardware models, model categories, normalization |
| Stockrooms and Locations | Stockroom management, asset transfers |
| Contract and Lease Management | Maintenance contracts, lease tracking, expiry alerts |
| Disposal | Asset disposal workflow, data sanitisation |

---

## Core Concepts — Study & Job Aid

### Asset vs CI — Key Distinction

| Concept | Asset Record | CI Record |
|---|---|---|
| Table | `alm_asset` | `cmdb_ci` |
| Purpose | Financial/lifecycle tracking | Technical/configuration tracking |
| Key fields | Cost, purchase date, vendor, warranty | IP, OS, serial number, relationships |
| Owner | Asset Management (finance/procurement) | CMDB (operations/IT) |
| Relationship | Asset ↔ CI (linked via `ci` field on asset) |

> An asset can exist without a CI (pre-deployment), but an asset in use typically has a linked CI.

---

### Asset Lifecycle States

```
On Order → In Stock → In Transit → Deployed → Maintenance → Retired → Disposed
```

| State | Meaning |
|---|---|
| On Order | PO raised, asset not yet received |
| In Stock | Received, in stockroom, unassigned |
| In Transit | Being moved between stockrooms/locations |
| Deployed | Assigned to a user or CI and in active use |
| Maintenance | Temporarily out of service for repair |
| Retired | No longer in active use but not yet disposed |
| Disposed | Permanently removed from inventory |

---

### Procurement Workflow

```
Purchase Request created
    ↓
Purchase Order (PO) created and approved
    ↓
PO sent to vendor
    ↓
Receiving: Items received → Asset records auto-created
    ↓
Assets move to "In Stock" state in Stockroom
    ↓
Assets deployed to users via Request/Fulfilment workflow
```

**Navigator:** Asset → Procurement → Purchase Orders

**Receiving assets:**
```
Navigator: Asset → Procurement → Receiving Slips
Match received items to PO line items
→ Asset records created with serial numbers
→ State set to "In Stock"
```

---

### Model Management

**Hardware Model:**
- Represents a specific make/model of hardware (e.g. "Dell Latitude 5540 Laptop")
- All assets of the same type share a Model record
- Model drives: default attributes, depreciation, warranty terms

```
Navigator: Asset → Model Management → Hardware Models → New
Fields: Name, Manufacturer, Model number, Model category (Computer/Laptop/Server/Printer)
Cost field: list price (can be overridden per asset)
```

**Model categories:**
- Computer, Laptop, Server, Printer, Network Gear, Mobile Device, Peripheral
- Drive asset form layout and available fields

**Model Normalization:**
- Ensures consistent model naming across data sources
- Navigator: Asset → Model Management → Model Normalization
- Maps variant names ("Dell Latitude 5540", "Latitude-5540") to one canonical model

---

### Stockrooms

```
Navigator: Asset → Stockroom Management → Stockrooms
```

- Physical or logical storage locations (warehouse, IT cupboard, off-site store)
- Assets in stock are assigned to a Stockroom
- Transfers between stockrooms tracked via Transfer Order

**Transfer Order workflow:**
```
Transfer Order created (from stockroom → to stockroom / location)
    ↓
Transfer Order Line items added (which assets)
    ↓
Transfer initiated → State: In Transit
    ↓
Receiving confirmed at destination → State: In Stock or Deployed
```

---

### Contracts and Warranties

```
Navigator: Contract Management → Contracts → Hardware Maintenance Contracts
```

**Contract fields:**
| Field | Purpose |
|---|---|
| Vendor | Who provides maintenance/support |
| Start / End Date | Contract term |
| Renewal notification | Days before expiry to alert |
| Associated Assets | Which hardware assets are covered |

**Warranty tracking:**
- Warranty expiry date on asset record
- Automated task/notification for expiring warranties
- Navigator: Asset → Administration → Contract Notification Rules

---

### Asset Disposal

```
Navigator: Asset → Asset Disposal → Disposal Orders
```

**Disposal workflow:**
```
Asset retired → Disposal Order created
→ Data sanitisation documented (disk wipe certification)
→ Physical disposal method selected (recycle, resell, destroy)
→ Disposal Order signed off
→ Asset state → Disposed
→ Asset record archived/retired from CMDB
```

**Compliance note:** data sanitisation must be documented to meet regulations (GDPR, HIPAA) — asset disposal without this record creates audit risk.

---

### HAM Roles

| Role | Access |
|---|---|
| `asset` | Basic asset user — view assets |
| `asset_manager` | Manage asset records, procurement, stockrooms |
| `inventory_admin` | Manage stockrooms and receiving |
| `contract_manager` | Manage contracts and warranties |
| `itam_admin` | Full ITAM/HAM administration |

---

## Common Exam Scenarios

**Scenario 1:** Assets were received but are not appearing in the stockroom. What should you check?
→ The **Receiving Slip** — verify the receipt was completed and matched to the PO. If incomplete, assets remain "On Order" rather than moving to "In Stock".

**Scenario 2:** The finance team needs to track depreciation for all laptops. What must be configured on the Hardware Model?
→ The **depreciation method** on the Model record (Straight Line or Double Declining Balance) + cost and useful life — ServiceNow will calculate asset value over time.

**Scenario 3:** A batch of laptops needs to move from the London warehouse to the New York office. What ServiceNow record tracks this?
→ A **Transfer Order** — tracks the movement, asset states change to "In Transit" until confirmed at destination.

**Scenario 4:** An asset is at end of life. The team must document data sanitisation before disposal. Where is this recorded?
→ On the **Disposal Order** record — attach the data sanitisation certificate and record the disposal method before marking the asset as Disposed.

---

## Study Resources

| Resource | URL |
|---|---|
| **Now Learning — HAM path** | https://nowlearning.servicenow.com — HAM Fundamentals |
| **ServiceNow Docs — HAM** | https://docs.servicenow.com — search "Hardware Asset Management" |
| **ServiceNow Community** | https://www.servicenow.com/community/ |

---

## Related Notes

- ServiceNow-CSA — Platform fundamentals
- ServiceNow-CIS-SAM — Software Asset Management (sister certification)
- ServiceNow-CIS-Disco — Discovery populates CMDB CIs linked to assets

---

## References

- ServiceNow Docs — HAM: https://docs.servicenow.com/bundle/washingtondc-it-asset-management/page/product/hardware-asset-management/concept/c_HardwareAssetMgmt.html
- ServiceNow Learning Paths PDF 2025: https://www.servicenow.com/content/dam/servicenow-assets/public/en-us/doc-type/infographic/learning-paths.pdf

---
<- [[Certs-MOC]]