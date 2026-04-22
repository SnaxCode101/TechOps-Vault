---
title: "CompTIA A+ (220-1201 / 220-1202) V15"
type: cert-study
category: certification
date: 2026-03-26
tags: [cert, comptia, aplus, 220-1201, 220-1202, hardware, networking, os, security, troubleshooting, foundational]
difficulty: foundational
---

# CompTIA A+ (220-1201 / 220-1202) V15

## Exam Overview

| Field | Core 1 (220-1201) | Core 2 (220-1202) |
|---|---|---|
| Max Questions | 90 | 90 |
| Time | 90 minutes | 90 minutes |
| Passing Score | 675 / 900 | 700 / 900 |
| Launch Date | March 25, 2025 | March 25, 2025 |
| Question Types | MCQ, drag-and-drop, PBQs | MCQ, drag-and-drop, PBQs |

> **Both exams must be passed to earn the A+ certification.**

---

## Exam Strategy

**Keyword traps — know these cold:**

| Keyword | What it changes |
|---|---|
| BEST / MOST appropriate | Pick the professional best-practice, not just one that works |
| FIRST / NEXT | Order matters — safety before anything, document before touching |
| LEAST / NOT / EXCEPT | You're looking for the wrong answer |
| Given a scenario | Apply knowledge to a real-world situation |
| Quickest / cheapest / easiest | Simpler solution often wins over technically correct one |

**Performance-Based Questions (PBQs):** Appear first on the exam. Skip and return if stuck — they take longer but are worth the same points.

**Safety rules always true:** Power off + unplug first. ESD wrist strap or touch metal chassis. Never work on carpet.

---

## Core 1 (220-1201) — Domains

### Domain 1 — Mobile Devices (13%)

**Laptop hardware:**

| Component | Key Facts |
|---|---|
| RAM | SO-DIMM (not DIMM). DDR4/DDR5. Match speed of existing RAM |
| Storage | M.2 NVMe (fastest, PCIe), M.2 SATA, 2.5" SATA HDD/SSD |
| Battery | Li-Ion / Li-Polymer. No heat or puncture. Calibrate by full discharge→charge |
| Wi-Fi/BT card | Mini PCIe or M.2 A/E key |
| Screen | CCFL (needs inverter) vs LED backlit (no inverter). Dim screen + faint image with flashlight = backlight/inverter failure |
| Keyboard/Trackpad | Ribbon cable, ZIF connector — lift latch, don't yank |
| DC Jack | Laptop works on battery but not AC → test adapter first, then DC jack |

> **Exam tip:** Laptop RAM = SO-DIMM. Desktop RAM = DIMM. Not interchangeable.

**Mobile connectivity:**

| Type | Speed/Range | Use |
|---|---|---|
| USB-C | Up to 40 Gbps (USB4) | Universal — charging, data, video |
| Bluetooth | ~30 feet | Headsets, keyboards, mice |
| NFC | ~4 cm | Mobile payments, pairing |
| IR | Line of sight only | TV remote apps |
| Hotspot/Tethering | Cellular speed | Phone as mobile Wi-Fi router |

---

### Domain 2 — Networking (23%)

**Critical ports — memorise:**

| Port | Protocol | Use |
|---|---|---|
| 20/21 | FTP | File transfer (20=data, 21=control) |
| 22 | SSH | Secure remote access |
| 23 | Telnet | Unencrypted remote (legacy, avoid) |
| 25 | SMTP | Email sending |
| 53 | DNS | Domain name resolution |
| 67/68 | DHCP | IP assignment (67=server, 68=client) |
| 80 | HTTP | Unencrypted web |
| 110 | POP3 | Email retrieval (downloads, deletes from server) |
| 143 | IMAP | Email retrieval (syncs, stays on server) |
| 443 | HTTPS | Encrypted web (TLS) |
| 445 | SMB | Windows file/printer sharing |
| 3389 | RDP | Windows remote desktop |

**IP Addressing:**

- **IPv4:** 32-bit, dotted decimal (e.g. 192.168.1.1)
- **IPv6:** 128-bit, hexadecimal (e.g. fe80::1)
- **Private ranges:** 10.x.x.x, 172.16–31.x.x, 192.168.x.x
- **APIPA:** 169.254.x.x — self-assigned when DHCP fails
- **Loopback:** 127.0.0.1 (IPv4), ::1 (IPv6)

**Wireless standards:**

| Standard | Band | Max Speed | Range | Notes |
|---|---|---|---|---|
| 802.11a | 5 GHz | 54 Mbps | Short | Legacy |
| 802.11b | 2.4 GHz | 11 Mbps | Long | Legacy |
| 802.11g | 2.4 GHz | 54 Mbps | Medium | Legacy |
| 802.11n (Wi-Fi 4) | 2.4/5 GHz | 600 Mbps | Good | MIMO |
| 802.11ac (Wi-Fi 5) | 5 GHz | ~3.5 Gbps | Good | MU-MIMO |
| 802.11ax (Wi-Fi 6) | 2.4/5/6 GHz | ~9.6 Gbps | Best | OFDMA |

**Network troubleshooting commands:**

```cmd
ipconfig /all         -- Full IP config including MAC
ipconfig /release     -- Release DHCP lease
ipconfig /renew       -- Renew DHCP lease
ipconfig /flushdns    -- Clear DNS cache
ping <host>           -- Test connectivity
tracert <host>        -- Trace route (Windows)
traceroute <host>     -- Trace route (Linux/Mac)
nslookup <domain>     -- DNS query
netstat -an           -- Active connections and ports
```

---

### Domain 3 — Hardware (25%)

**RAM types:**
- DDR4: 288-pin DIMM, 260-pin SO-DIMM
- DDR5: Faster, higher capacity, built-in error correction
- ECC RAM: Error-correcting, used in servers

**Storage:**
- **HDD:** Magnetic, moving parts, 5400/7200 RPM. Slower, cheaper, larger capacity
- **SSD (SATA):** No moving parts, uses AHCI, faster than HDD
- **NVMe (M.2 PCIe):** Fastest consumer storage — 3–5 GB/s read speeds
- **RAID levels:** 0 (striping, no redundancy), 1 (mirroring), 5 (striping+parity, min 3 disks), 10 (1+0, min 4 disks)

**PSU:**
- Wattage must exceed total system load
- 80 Plus certification = efficiency rating (Bronze/Silver/Gold/Platinum)
- Modular PSU = only attach cables you need (better airflow)

---

### Domain 4 — Virtualization & Cloud (11%)

**Cloud service models:**

| Model | Provider manages | Customer manages | Example |
|---|---|---|---|
| IaaS | Hardware, network | OS, apps, data | AWS EC2, Azure VMs |
| PaaS | Hardware, OS, runtime | Apps, data | Azure App Service, Heroku |
| SaaS | Everything | Just use it | Microsoft 365, Salesforce |

**Cloud deployment models:** Public, Private, Hybrid, Community

**Virtualization:**
- **Hypervisor Type 1 (bare metal):** Runs directly on hardware — VMware ESXi, Hyper-V, Xen
- **Hypervisor Type 2 (hosted):** Runs on an OS — VMware Workstation, VirtualBox
- **Snapshot:** Point-in-time VM state — use before risky changes
- **Resource pooling:** Multiple VMs share physical CPU/RAM/storage

---

### Domain 5 — Hardware & Network Troubleshooting (28%)

**CompTIA troubleshooting methodology (memorise this order):**

1. Identify the problem
2. Establish a theory of probable cause
3. Test the theory
4. Establish a plan of action and implement
5. Verify full system functionality
6. Document findings, actions, and outcomes

> This order appears on the exam frequently. "Document" is always last.

**Common hardware symptoms:**

| Symptom | Likely cause |
|---|---|
| No POST, no beeps | RAM not seated, dead PSU |
| Continuous beeps | RAM failure (POST codes vary by BIOS) |
| Overheating/thermal shutdown | Failing fan, dried thermal paste, blocked vents |
| Grinding noise | Failing HDD (back up immediately) |
| Capacitor bulge on motherboard | Motherboard failure, replace |
| BSOD (Windows) | Driver issue, hardware failure, overheating |
| Dim screen, image visible with flashlight | Backlight/inverter failure |

---

## Core 2 (220-1202) — Domains

### Domain 1 — Operating Systems (28%)

**Windows editions (exam focus):**

| Edition | Key Feature |
|---|---|
| Home | Consumer, no domain join |
| Pro | Domain join, BitLocker, Remote Desktop |
| Enterprise | All Pro features + advanced management |
| Education | Enterprise features for academic use |

**Windows administrative tools:**

| Tool | Purpose |
|---|---|
| `msconfig` | System configuration, startup programs |
| `devmgmt.msc` | Device Manager |
| `diskmgmt.msc` | Disk Management |
| `services.msc` | Windows Services |
| `regedit` | Registry Editor |
| `gpedit.msc` | Local Group Policy Editor (Pro+) |
| `eventvwr.msc` | Event Viewer |
| `taskmgr` | Task Manager |
| `resmon` | Resource Monitor |
| `perfmon` | Performance Monitor |
| `lusrmgr.msc` | Local Users and Groups |

**Windows CLI tools:**

```cmd
sfc /scannow          -- System File Checker
DISM /Online /Cleanup-Image /RestoreHealth  -- Repair Windows image
chkdsk C: /f /r       -- Check + repair disk errors
diskpart              -- Disk partitioning
format                -- Format a volume
robocopy              -- Robust file copy
net use               -- Map network drives
netsh                 -- Network configuration
```

**File systems:**

| FS | Max File | Max Volume | Use |
|---|---|---|---|
| FAT32 | 4 GB | 2 TB | USB drives, compatibility |
| exFAT | 16 EB | 128 PB | Large USB drives, SD cards |
| NTFS | 16 TB | 256 TB | Windows primary FS — permissions, encryption |
| ext4 | 16 TB | 1 EB | Linux primary FS |
| APFS | Large | Large | macOS/iOS primary FS |

---

### Domain 2 — Security (28%)

**Security threats:**

| Threat | Description |
|---|---|
| Phishing | Email/message tricking user into credentials/malware |
| Spear phishing | Targeted phishing at specific individual/org |
| Vishing | Voice phishing (phone calls) |
| Smishing | SMS phishing |
| Ransomware | Encrypts files, demands payment |
| Keylogger | Records keystrokes — captures passwords |
| Rootkit | Hides malware at OS/firmware level |
| Botnet | Network of compromised machines |
| Man-in-the-Middle | Intercepts communication between two parties |
| SQL Injection | Malicious SQL in input fields |
| XSS | Cross-site scripting — injects scripts into web pages |
| Zero-day | Exploit for unknown/unpatched vulnerability |
| Social engineering | Manipulating humans rather than attacking systems |

**Wireless security protocols:**

| Protocol | Status | Notes |
|---|---|---|
| WEP | Broken — never use | RC4 cipher, easily cracked |
| WPA | Deprecated | TKIP cipher, vulnerable |
| WPA2 | Current standard | AES/CCMP, use when WPA3 unavailable |
| WPA3 | Recommended | SAE handshake, stronger encryption |

**Malware removal procedure (10 steps):**

1. Investigate and verify malware symptoms
2. Quarantine the infected system (disconnect from network)
3. Disable System Restore in Windows Home
4. Remediate infected systems
5. Update anti-malware software
6. Scan and use removal techniques (Safe Mode, preinstallation environment)
7. Reimage/reinstall if remediation fails
8. Schedule scans and run updates
9. Enable System Restore and create restore point
10. Educate the end user

---

### Domain 3 — Software Troubleshooting (23%)

**Windows troubleshooting tools:**

| Tool | Use |
|---|---|
| Safe Mode | Boot with minimal drivers — isolate driver/software issues |
| Safe Mode with Networking | Safe mode + network access |
| System Restore | Roll back to previous working state |
| Windows Recovery Environment (WinRE) | Boot repair, command prompt, restore |
| Startup Repair | Auto-fix boot issues |
| SFC / DISM | Repair corrupted system files |

**Mobile device troubleshooting:**

| Issue | Likely Cause / Fix |
|---|---|
| Short battery life | Background apps, screen brightness, bad battery |
| Overheating | Heavy app usage, charging while using, case blocking vents |
| Touchscreen unresponsive | Screen protector, moisture, digitizer failure |
| App crashes | Storage full, app update needed, OS compatibility |
| Cannot connect to Wi-Fi | Forget and rejoin network, reset network settings |
| No cellular signal | Carrier outage, SIM issue, airplane mode on |

---

### Domain 4 — Operational Procedures (21%)

**Backup types:**

| Type | What it backs up | Clears archive bit | Restore process |
|---|---|---|---|
| Full | Everything | Yes | Full backup only |
| Incremental | Changes since last full OR incremental | Yes | Full + all incrementals |
| Differential | Changes since last full | No | Full + last differential |

> **Incremental = faster backup, slower restore. Differential = slower backup, faster restore.**

**3-2-1 Backup Rule:** 3 copies of data, on 2 different media types, with 1 copy offsite.

**Change management documentation:**
- Network diagrams, topology maps
- Asset inventory / CMDB
- KB articles — known error database
- Incident tickets and resolution documentation

**Scripting basics:**

| Language | Extension | Use |
|---|---|---|
| PowerShell | .ps1 | Windows automation |
| Bash | .sh | Linux/macOS automation |
| Python | .py | Cross-platform scripting |
| Batch | .bat | Legacy Windows automation |
| VBScript | .vbs | Legacy Windows scripting |

---

## Quick Reference — Critical Memorisation

**OSI Model (All People Seem To Need Data Processing):**

| Layer | Name | Protocol Examples |
|---|---|---|
| 7 | Application | HTTP, HTTPS, FTP, DNS, SMTP |
| 6 | Presentation | SSL/TLS, JPEG, MPEG |
| 5 | Session | NetBIOS, RPC |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP, ICMP, ARP |
| 2 | Data Link | Ethernet, MAC, Switches |
| 1 | Physical | Cables, hubs, NIC |

**TCP vs UDP:**

| TCP | UDP |
|---|---|
| Connection-oriented (3-way handshake) | Connectionless |
| Reliable, ordered delivery | Unreliable, no ordering |
| Slower | Faster |
| HTTP/S, FTP, SSH, SMTP | DNS, DHCP, TFTP, VoIP, streaming |

**Ethernet cable categories:**

| Cat | Max Speed | Max Length |
|---|---|---|
| Cat 5 | 100 Mbps | 100m |
| Cat 5e | 1 Gbps | 100m |
| Cat 6 | 1 Gbps (10 Gbps up to 55m) | 100m |
| Cat 6a | 10 Gbps | 100m |
| Cat 7 | 10 Gbps | 100m |
| Cat 8 | 25/40 Gbps | 30m |

---

## Study Resources

- CompTIA-Aplus-Practice-Questions — 72 practice questions with detailed explanations
- [CompTIA A+ Official Exam Objectives](https://www.comptia.org/certifications/a)
- [[Security-Plus-SY0-701]] — Next cert in the CompTIA pathway

---

## Key Takeaways

- A+ covers breadth over depth — hardware, networking, OS, security, and operational procedures
- The troubleshooting methodology (6 steps) appears repeatedly — memorise the exact order
- Safety procedures and documentation are always the first and last steps respectively
- Core 2 Domain 2 (Security, 28%) has significant overlap with Security+ — studying both together is efficient
- PBQs are worth attempting — skip them first, return when you've answered all MCQs

---
*Note created: 2026-03-26*

---

> ⚠️ **Verify before studying:** CompTIA updates exam objectives periodically. Always confirm current exam numbers, domain weights, and passing scores directly at **https://www.comptia.org/certifications/a** before purchasing a voucher or beginning study. The objectives in this note reflect the 220-1201/220-1202 versions as documented; CompTIA is the authoritative source.
---
<- [[Certs-MOC]]