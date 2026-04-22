---
title: OpenVAS / Nessus
tags: |-
  - tool
  - OpenVAS
  - Nessus
  - vulnerability-scanner
  - GVM
  - authenticated-scan
  - intermediate
category: vulnerability-management
platform: cross-platform
install: apt install gvm / https://www.tenable.com/products/nessus
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# 🔍 OpenVAS / Nessus

## What They Are

**OpenVAS (Open Vulnerability Assessment System)** — Free, open-source vulnerability scanner. Now part of the **Greenbone Vulnerability Management (GVM)** platform. Maintained by Greenbone Networks.

**Nessus** — Commercial vulnerability scanner by Tenable. Industry standard. **Nessus Essentials** is free (limited to 16 IPs). Nessus Professional is paid.

Both provide: authenticated/unauthenticated scanning, CVE correlation, CVSS scoring, policy compliance checks, detailed remediation guidance.

---

## OpenVAS / GVM Installation (Kali Linux)

```bash
# Install GVM
sudo apt install gvm

# Initialize (downloads vulnerability feed — takes time)
sudo gvm-setup

# Start services
sudo gvm-start

# Get admin password (set during setup)
sudo gvm-setup   # Shows password if first run

# Access web UI
# https://localhost:9392

# Check status
sudo gvm-check-setup
```

---

## OpenVAS / GVM Web Interface

```
1. Login: https://localhost:9392 (admin + password from setup)
2. Scans → Tasks → New Task
3. Configure:
   - Name: give it a meaningful name
   - Scan Targets: add IPs/ranges
   - Scanner: OpenVAS Default
   - Scan Config: Full and Fast (recommended) or Empty (custom)
4. Save → Start Task (▶)
5. Results: Reports → Show all reports
```

### Scan Configurations
| Config | Description |
|---|---|
| Full and Fast | Comprehensive, reasonably quick — default for most assessments |
| Full and Fast Ultimate | Everything including dangerous checks |
| Full and Very Deep | Slower, more thorough |
| System Discovery | Host discovery only |
| Host Discovery | Ping-only |

### Authenticated Scanning (Higher Coverage)
```
Configuration → Credentials → New Credential
  Type: Username + Password (SSH or SMB)
  Login: target_username
  Password: target_password

Then in Scan Target → Credentials → add your credential
Authenticated scan finds ~2–4x more vulnerabilities than unauthenticated
```

---

## Nessus Setup & Usage

### Installation
```bash
# Download from https://www.tenable.com/products/nessus/nessus-essentials (free, 16 IPs)
# or: Nessus Professional (paid)

# Debian/Kali
sudo dpkg -i Nessus-*.deb
sudo systemctl start nessusd
sudo systemctl enable nessusd

# Access: https://localhost:8834
# First run: register activation code, download plugins (15–20 min)
```

### Nessus Scan Types
| Scan Template | Purpose |
|---|---|
| **Basic Network Scan** | General purpose; common vulnerabilities; ports |
| **Advanced Scan** | Full customization of all settings |
| **Credentialed Patch Audit** | Authenticated check for missing patches |
| **Web Application Tests** | HTTP/HTTPS application vulnerabilities |
| **Compliance Audit** | CIS benchmarks, STIG, PCI DSS compliance |
| **Malware Scan** | Detect malware indicators on Windows |
| **Host Discovery** | Find live hosts only |

### Running a Nessus Scan
```
1. Login → New Scan → Select Template
2. General tab:
   - Name: descriptive name
   - Targets: IPs, ranges, or hostname
3. Credentials tab (for authenticated scan):
   - SSH or Windows credentials
4. Plugins tab: review/customize which checks run
5. Save → Launch (▶)
6. Results: click scan → Vulnerabilities tab
7. Export: Report → PDF/CSV/HTML
```

---

## Understanding Scan Results

### CVSS-Based Severity (Both Tools)
| Color | Severity | CVSS Range |
|---|---|---|
| 🔴 Red | Critical | 9.0–10.0 |
| 🟠 Orange | High | 7.0–8.9 |
| 🟡 Yellow | Medium | 4.0–6.9 |
| 🔵 Blue | Low | 0.1–3.9 |
| ⚪ Grey | Info | 0.0 |

### Key Fields Per Finding
```
Name: Vulnerability name
Synopsis: Brief description
Description: Detailed explanation
Solution: How to fix it
CVSS Score: Severity score
CVE: Related CVE IDs
See Also: Reference URLs
Plugin Output: Evidence from the scan
```

---

## GVM CLI Usage (Automation)

```bash
# List tasks
gvm-cli --gmp-username admin --gmp-password pass socket --xml "<get_tasks/>"

# Run a scan via CLI (requires gvm-cli)
pip3 install gvm-cli

# Connect to GVM
gvm-cli socket

# Create and run a task (XML API)
gvm-cli socket --xml "
<create_task>
  <name>My Scan</name>
  <target id='TARGET_UUID'/>
  <config id='CONFIG_UUID'/>
</create_task>"
```

---

## Nessus CLI / API

```bash
# Nessus API (REST, port 8834)
# All API calls require X-Cookie session token

# Authenticate
curl -s -k -X POST https://localhost:8834/session \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"pass"}' | jq .token

# List scans
curl -s -k https://localhost:8834/scans \
  -H "X-Cookie: token=TOKEN" | jq .

# Launch a scan
curl -s -k -X POST https://localhost:8834/scans/SCAN_ID/launch \
  -H "X-Cookie: token=TOKEN"
```

---

## Key Differences: OpenVAS vs Nessus

| Feature | OpenVAS/GVM | Nessus Essentials | Nessus Pro |
|---|---|---|---|
| Cost | Free | Free (16 IPs max) | Paid (~$3k/yr) |
| Plugin count | ~85,000+ | ~180,000+ | ~180,000+ |
| Update frequency | Daily | Daily | Daily |
| Compliance checks | Limited | Limited | Yes |
| False positive rate | Higher | Lower | Lowest |
| Ease of use | Moderate | Easy | Easy |
| Best for | Labs, budget assessments | Small targets | Enterprise |

---

## Related Notes
- Vulnerability-Management
- Nmap
- Vulnerability-Research-Reference
- Penetration-Testing-Methodology

## References
- Greenbone/OpenVAS: https://www.greenbone.net/en/
- Nessus: https://www.tenable.com/products/nessus
- CVSS v3.1 spec: https://www.first.org/cvss/specification-document

---
<- [[Tools-MOC]]