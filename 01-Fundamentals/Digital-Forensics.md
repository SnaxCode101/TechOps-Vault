---
title: Digital Forensics
tags:
  - intermediate
topic: Digital Forensics & Incident Response
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: NIST SP 800-86, SWGDE best practices, RFC 3227
---

# 🔍 Digital Forensics

## Overview
Digital forensics is the process of identifying, acquiring, preserving, analyzing, and presenting digital evidence in a manner that is legally admissible. It encompasses **disk forensics**, **memory forensics**, **network forensics**, and **log analysis**. Often paired with incident response under the acronym **DFIR** (Digital Forensics and Incident Response).

---

## Core Principles

### Chain of Custody
A documented record proving that evidence was collected, handled, and stored in a way that preserves its integrity. Every person who handles the evidence must be logged. If chain of custody is broken, evidence may be inadmissible.

### Order of Volatility (RFC 3227)
Collect evidence in order from most volatile (lost first on power-off) to least volatile:
1. CPU registers and cache
2. Routing tables, ARP cache, process table, kernel statistics
3. Memory (RAM)
4. Temporary file systems / swap / pagefile
5. Non-local filesystem data
6. Backups, remote logs

### Write Blockers
Hardware or software devices that prevent any writes to storage media during acquisition. Preserve the integrity of the original evidence.

---

## Disk Forensics

### Imaging a Drive (Linux)
```bash
# Create bit-for-bit image with dd
sudo dd if=/dev/sda of=/mnt/evidence/disk.img bs=512 status=progress

# Create image with error handling (preferred)
sudo dcfldd if=/dev/sda of=/mnt/evidence/disk.img bs=512 hash=sha256 hashlog=/mnt/evidence/disk.hash

# Using ewfacquire (Expert Witness Format / E01)
sudo ewfacquire /dev/sda
```

### Verify Image Integrity
```bash
md5sum /dev/sda
md5sum /mnt/evidence/disk.img
# Hashes must match — proves no modification during acquisition
```

### Mount Image Read-Only
```bash
sudo mount -o ro,loop disk.img /mnt/analysis
```

### Key Filesystem Artifacts (Windows)
| Artifact | Location | What It Shows |
|---|---|---|
| MFT ($MFT) | Root of NTFS volume | Every file ever created; timestamps; deleted files |
| Prefetch | C:\Windows\Prefetch\ | Programs that have been run |
| Recent Files | C:\Users\%user%\AppData\Roaming\Microsoft\Windows\Recent | Recently accessed files |
| LNK Files | Same as Recent Files | Shortcut metadata: target path, MAC timestamps |
| Shellbags | NTUSER.DAT registry hive | Folders the user has browsed |
| USN Journal | $Extend\$UsnJrnl | File system change log |
| Recycle Bin | C:\$Recycle.Bin | Deleted files |
| Event Logs | C:\Windows\System32\winevt\Logs\ | System, Security, Application, PowerShell logs |
| Registry Hives | C:\Windows\System32\config\ | SAM, SYSTEM, SOFTWARE, SECURITY, DEFAULT |

### Key Windows Event IDs
| Event ID | Description |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4648 | Logon with explicit credentials (runas) |
| 4672 | Special privilege logon (admin) |
| 4688 | New process created |
| 4698 | Scheduled task created |
| 4720 | User account created |
| 4732 | User added to security group |
| 7045 | New service installed |
| 1102 | Audit log cleared |

---

## Memory Forensics

Memory forensics acquires and analyzes the contents of RAM to find running processes, network connections, injected code, and encryption keys.

### Acquiring Memory (Windows)
```bash
# WinPMEM (free)
winpmem_mini_x64.exe -o memory.raw

# DumpIt
DumpIt.exe

# FTK Imager (GUI)
# File → Capture Memory Image
```

### Volatility 3 Analysis
```bash
# Identify OS profile
python3 vol.py -f memory.raw windows.info

# List processes
python3 vol.py -f memory.raw windows.pslist
python3 vol.py -f memory.raw windows.pstree

# Find injected code / hollowed processes
python3 vol.py -f memory.raw windows.malfind

# Network connections
python3 vol.py -f memory.raw windows.netstat

# Command history
python3 vol.py -f memory.raw windows.cmdline
python3 vol.py -f memory.raw windows.consoles

# Dump a suspicious process
python3 vol.py -f memory.raw windows.dumpfiles --pid 1234

# Registry keys
python3 vol.py -f memory.raw windows.registry.hivelist
```

See also: Volatility

---

## Log Analysis

### Key Linux Log Files
| Log | Location | Purpose |
|---|---|---|
| Auth log | /var/log/auth.log | SSH logins, sudo, su, authentication |
| Syslog | /var/log/syslog | General system messages |
| Apache | /var/log/apache2/access.log | HTTP requests |
| Cron | /var/log/cron | Scheduled job execution |
| Messages | /var/log/messages | General kernel/system messages |
| Audit | /var/log/audit/audit.log | SELinux/auditd security events |

### Log Analysis Commands
```bash
# Find failed SSH logins
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn

# Find successful logins
grep "Accepted" /var/log/auth.log

# Timeline of commands (bash history)
cat ~/.bash_history
cat /home/*/.bash_history

# Find files modified in last 24 hours
find / -mtime -1 -type f 2>/dev/null

# Find SUID files
find / -perm -4000 -type f 2>/dev/null
```

---

## Timeline Analysis

Creating a super-timeline correlates events across multiple artifact types:

```bash
# Using log2timeline / Plaso (Linux)
log2timeline.py --parsers win7 evidence.E01 plaso.db
psort.py plaso.db "SELECT * FROM l2t_csv" > timeline.csv

# Filter timeline to relevant timeframe
grep "2026-03-" timeline.csv | head -200
```

---

## Forensic Tools Summary

| Tool | Type | Platform | Purpose |
|---|---|---|---|
| Autopsy | GUI | Cross-platform | Disk image analysis, timeline, keyword search |
| FTK Imager | GUI | Windows | Disk imaging, memory capture |
| Volatility 3 | CLI | Cross-platform | Memory forensics |
| The Sleuth Kit | CLI | Cross-platform | Disk analysis library |
| Plaso / log2timeline | CLI | Linux | Super-timeline creation |
| Wireshark | GUI | Cross-platform | Network forensics |
| Eric Zimmermann Tools | GUI | Windows | MFT, registry, event log analysis |
| Regripper | CLI | Cross-platform | Registry parsing |
| CyberChef | Web | Browser | Data decoding, transformation |

---

## DFIR Process

```
1. Preparation    — Tools ready, policies defined
2. Identification — Detect the incident, determine scope
3. Acquisition    — Image disks, dump memory, capture network (order of volatility)
4. Preservation   — Hash evidence, chain of custody, write-protect
5. Analysis       — Timeline, artifact analysis, find IOCs
6. Reporting      — Document findings, timeline of events, attribution
7. Lessons Learned— Improve detection and response
```

---

## Related Notes
- Malware-Analysis
- Incident-Response
- Volatility
- Autopsy
- Threat-Intelligence
- Linux-Commands
- Windows-Commands

## Sources

National Institute of Standards and Technology. (2006). *Guide to integrating forensic techniques into incident response* (NIST SP 800-86). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-86.pdf

Internet Engineering Task Force. (2002). *Guidelines for evidence collection and archiving* (RFC 3227). IETF. https://www.rfc-editor.org/rfc/rfc3227

Scientific Working Group on Digital Evidence. (2013). *Best practices for digital evidence collection*. SWGDE. https://www.swgde.org/

---
<- [[Fundamentals-MOC]]