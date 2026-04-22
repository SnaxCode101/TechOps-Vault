---
title: Nmap
tags:
  - tool
  - recon
  - network-scanning
  - port-scanning
  - foundational
category: recon
platform: cross-platform
install: apt install nmap / brew install nmap / https://nmap.org/download.html
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# 🛠️ Nmap

## What It Is
Nmap (Network Mapper) is an open-source tool for network discovery and security auditing. It is used to discover hosts, enumerate open ports, detect running services and versions, identify operating systems, and run scripted checks against targets.

## Installation
```bash
# Debian/Ubuntu
sudo apt install nmap

# macOS
brew install nmap

# Windows / other: https://nmap.org/download.html
```

## Basic Syntax
```bash
nmap [scan type] [options] [target]
# target can be: IP, hostname, CIDR range, or file (-iL)
```

## Common Commands / Use Cases

### Basic host discovery (ping sweep, no port scan)
```bash
nmap -sn 192.168.1.0/24
```

### Default scan (top 1000 TCP ports, SYN scan if root)
```bash
nmap 192.168.1.10
```

### SYN scan (stealth, requires root/sudo)
```bash
sudo nmap -sS 192.168.1.10
```

### Scan all 65535 TCP ports
```bash
sudo nmap -sS -p- 192.168.1.10
```

### Service version detection
```bash
sudo nmap -sV 192.168.1.10
```

### OS detection
```bash
sudo nmap -O 192.168.1.10
```

### Aggressive scan (OS detect + version + scripts + traceroute)
```bash
sudo nmap -A 192.168.1.10
```

### UDP scan (slower; run on specific ports)
```bash
sudo nmap -sU -p 53,67,68,161 192.168.1.10
```

### Script scan using NSE (Nmap Scripting Engine)
```bash
# Run default scripts
sudo nmap -sC 192.168.1.10

# Run a specific script
sudo nmap --script=http-title 192.168.1.10

# Run scripts in a category
sudo nmap --script=vuln 192.168.1.10
```

### Scan from a target list file
```bash
nmap -iL targets.txt
```

### Output to file (all formats)
```bash
sudo nmap -oA scan_results 192.168.1.10
# Creates .nmap, .xml, .gnmap files
```

## Important Flags / Options
| Flag | Description |
|---|---|
| `-sS` | TCP SYN (stealth) scan — most common, requires root |
| `-sT` | TCP connect scan — no root needed, more detectable |
| `-sU` | UDP scan |
| `-sN` | Null scan — no flags set, can bypass some firewalls |
| `-sV` | Service/version detection |
| `-O` | OS fingerprinting |
| `-A` | Aggressive: OS + version + scripts + traceroute |
| `-p-` | Scan all 65535 ports |
| `-p 22,80,443` | Scan specific ports |
| `--top-ports N` | Scan top N most common ports |
| `-sn` | Ping sweep only (no port scan) |
| `-Pn` | Skip host discovery (treat all hosts as up) |
| `-T0` to `-T5` | Timing: 0=paranoid, 3=default, 5=insane |
| `--open` | Show only open ports |
| `-sC` | Run default NSE scripts |
| `--script=` | Run specified NSE script(s) |
| `-oA` | Output in all formats |
| `-oN` | Output in normal format |
| `-oX` | Output in XML format |
| `-v` / `-vv` | Verbosity |

## Output Interpretation
- **open** — port is accepting connections
- **closed** — port is accessible but no service listening
- **filtered** — firewall or filter is blocking probe packets; state unknown
- **open|filtered** — cannot determine if open or filtered (common with UDP)

## Opsec / Legal Notes
> ⚠️ Only scan systems you own or have explicit written permission to test. Unauthorized scanning is illegal in most jurisdictions.
- SYN scans (`-sS`) are less noisy than full connect scans but are still logged by modern IDS/IPS.
- Aggressive timing (`-T5`) is fast but highly detectable.
- Use `-T2` or `-T1` for slower, quieter scans in sensitive environments.
- `-Pn` avoids ICMP probes but still generates port-level traffic.

## Related Tools
- Masscan — faster but less feature-rich for large-scale scanning
- [[Wireshark]] — capture and analyze traffic during scans
- [[Gobuster]] — directory/DNS enumeration after port discovery
- [[Metasploit]] — exploitation after service enumeration

## Related Notes
- OSI-Model
- Networking-Basics
- TCP-IP-Fundamentals

## References
- Official docs: https://nmap.org/book/man.html
- NSE script reference: https://nmap.org/nsedoc/


## See Also

- Penetration-Testing-Methodology
- Networking-Basics
- TCP-IP-Fundamentals
- Linux-Commands
- Common-Ports

---
<- [[Tools-MOC]]