---
title: Custom Lab — Network Enumeration with Nmap
type: lab-writeup
platform: Custom
difficulty: foundational
os: Mixed
status: Complete
date: 2026-03-24
tags:
  - foundational
  - lab
  - custom
  - nmap
  - enumeration
  - network-scanning
  - home-lab
---

# Custom Lab — Network Enumeration with Nmap

## Lab Info

| Field | Value |
|---|---|
| Platform | Custom / Home Lab |
| Exercise | Structured Nmap enumeration workflow |
| Difficulty | Easy |
| Scope | Your local lab network only (e.g. 192.168.x.x/24) |
| Date | 2026-03-24 |

> ⚠️ **Legal reminder:** Only scan networks and systems you own or have explicit written permission to test. Unauthorised scanning is illegal under the CFAA (US), Computer Misuse Act (UK), and equivalent laws globally.

---

## Objectives

- [ ] Discover all live hosts on the lab subnet
- [ ] Enumerate open ports on each host
- [ ] Identify service versions and operating systems
- [ ] Run NSE script checks
- [ ] Save structured output for later analysis
- [ ] Build a repeatable enumeration script

---

## Setup

```bash
# Confirm your attacker IP and subnet
ip addr show eth0
# VirtualBox Host-Only example: 192.168.56.0/24
```

---

## Phase 1 — Host Discovery

### Ping sweep

```bash
nmap -sn 192.168.56.0/24
```

`-sn` — ping scan only, no port scan. Sends ICMP echo, TCP SYN to 443, TCP ACK to 80, ICMP timestamp.

### ARP scan (more reliable on local networks)

```bash
sudo nmap -sn -PR 192.168.56.0/24
# or
sudo arp-scan --localnet
```

> ARP cannot be blocked by host-based firewalls — more reliable than ICMP for local discovery.

---

## Phase 2 — Port Scanning

### Quick scan (top 1000 ports)

```bash
nmap -sV 192.168.56.101 -oN nmap/quick.txt
```

### Full port scan (all 65535 TCP ports)

```bash
sudo nmap -p- -sV --min-rate 1000 192.168.56.101 -oN nmap/full.txt
```

### UDP scan

```bash
sudo nmap -sU --top-ports 20 192.168.56.101 -oN nmap/udp.txt
```

> UDP is slow and often skipped — but SNMP (161), DNS (53), TFTP (69), NFS (2049) only run over UDP. Always scan at minimum the top 20 UDP ports.

---

## Phase 3 — Service and OS Detection

```bash
sudo nmap -sC -sV -O 192.168.56.101 -oN nmap/detailed.txt
```

| Flag | Purpose |
|---|---|
| `-sC` | Default NSE scripts (safe category) |
| `-sV` | Service version detection |
| `-O` | OS fingerprinting (requires root) |

---

## Phase 4 — NSE Script Scanning

### Vulnerability scan

```bash
sudo nmap --script vuln 192.168.56.101 -oN nmap/vuln.txt
```

### SMB checks

```bash
sudo nmap --script smb-vuln*,smb-enum-shares,smb-enum-users -p 139,445 192.168.56.101
```

### HTTP checks

```bash
nmap --script http-title,http-headers,http-methods,http-enum -p 80,443,8080 192.168.56.101
```

### FTP anonymous login

```bash
nmap --script ftp-anon -p 21 192.168.56.101
```

---

## Phase 5 — Structured Output

```bash
sudo nmap -sC -sV -O 192.168.56.101 \
  -oN nmap/result.txt \
  -oX nmap/result.xml \
  -oG nmap/result.gnmap
```

### Parsing grepable output

```bash
grep "80/open" nmap/result.gnmap
grep "open" nmap/result.gnmap | awk '{print $2}'
```

---

## Repeatable Enumeration Script

```bash
#!/usr/bin/env bash
# Usage: ./enum.sh <target_ip>
set -euo pipefail

TARGET="${1:?Usage: $0 <target_ip>}"
OUTPUT_DIR="nmap_${TARGET//./_}"
mkdir -p "$OUTPUT_DIR"

echo "[*] Phase 1: Quick scan"
sudo nmap -sC -sV -oN "$OUTPUT_DIR/quick.txt" "$TARGET"

echo "[*] Phase 2: Full port scan"
sudo nmap -p- -sV --min-rate 1000 -oN "$OUTPUT_DIR/full.txt" "$TARGET"

echo "[*] Phase 3: UDP top-20"
sudo nmap -sU --top-ports 20 -oN "$OUTPUT_DIR/udp.txt" "$TARGET"

echo "[*] Phase 4: Vuln scripts"
sudo nmap --script vuln -oN "$OUTPUT_DIR/vuln.txt" "$TARGET"

echo "[+] Done. Results in $OUTPUT_DIR/"
```

```bash
chmod +x enum.sh && ./enum.sh 192.168.56.101
```

---

## Result Documentation Table

| Host | Open Ports | Key Services | Version | Potential Vulns |
|---|---|---|---|---|
| 192.168.56.101 | 21, 22, 80, 445 | FTP, SSH, HTTP, SMB | vsftpd 2.3.4, Apache 2.2.8 | CVE-2011-2523, CVE-2007-2447 |

---

## Key Takeaways

- Enumeration quality directly determines which attack vectors you find — thoroughness over speed
- Always scan UDP — missing it is a common gap that leads to missed attack paths
- Save all output from the start in multiple formats — re-scanning costs time
- `--min-rate 1000` is for lab environments — use `-T2` or `-T3` on production networks
- A repeatable bash script enforces consistency across targets

---

## Tools Used

- Nmap
- Bash-Scripting

---

## References

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Nmap NSE Documentation](https://nmap.org/nsedoc/)
- [PTES Intelligence Gathering](http://www.pentest-standard.org/index.php/Intelligence_Gathering)

---
*Write-up completed: 2026-03-24*

## See Also

- Penetration-Testing-Methodology
- Nmap
- Bash-Scripting
- Networking-Basics
- Common-Ports
- Linux-Commands

---
<- [[Labs-MOC]]