---
title: Network Defense & Blue Team
tags:
  - fundamentals
  - blue-team
  - ids
  - SIEM
  - SOC
  - threat-detection
  - firewall
  - intermediate
topic: Network Defense & Blue Team Operations
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: NIST SP 800-94, MITRE D3FEND, CIS Controls v8, NIST CSF 2.0
---

# 🔵 Network Defense & Blue Team

## Overview
Blue team operations encompass the defensive security activities that protect an organization: monitoring, detection, incident response, threat hunting, and continuous improvement. This note covers the core technologies, processes, and frameworks used by defenders.

---

## Defense-in-Depth

Defense-in-depth layers multiple security controls so that if one fails, others remain. No single control is sufficient.

```
┌─────────────────────────────────┐
│         Policies & Procedures   │  ← Administrative controls
│   ┌─────────────────────────┐   │
│   │    Physical Security     │   │  ← Access control, CCTV
│   │  ┌───────────────────┐  │   │
│   │  │  Network Security  │  │   │  ← Firewall, IDS/IPS, segmentation
│   │  │ ┌───────────────┐ │  │   │
│   │  │ │ Host Security  │ │  │   │  ← EDR, patching, hardening
│   │  │ │ ┌───────────┐ │ │  │   │
│   │  │ │ │Application│ │ │  │   │  ← WAF, code review, DAST
│   │  │ │ │  ┌─────┐  │ │ │  │   │
│   │  │ │ │  │Data │  │ │ │  │   │  ← Encryption, DLP, access control
│   │  │ │ │  └─────┘  │ │ │  │   │
```

---

## Firewall Types

| Type | Description | Layer |
|---|---|---|
| Packet filter | Filters on IP/port/protocol; stateless | L3/L4 |
| Stateful inspection | Tracks connection state; blocks unexpected responses | L4 |
| Application-layer (NGFW) | Deep packet inspection; application awareness | L7 |
| WAF | Specifically protects web apps (HTTP/S); OWASP rules | L7 |
| Host-based firewall | Windows Defender Firewall, iptables, ufw | Host |

### iptables Quick Reference (Linux)
```bash
# View rules
sudo iptables -L -v -n

# Block IP
sudo iptables -A INPUT -s 10.10.10.10 -j DROP

# Allow port 22 from specific IP only
sudo iptables -A INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j DROP

# Allow established connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Save rules (Debian/Ubuntu)
sudo iptables-save > /etc/iptables/rules.v4
```

---

## IDS vs. IPS

| Feature | IDS (Intrusion Detection System) | IPS (Intrusion Prevention System) |
|---|---|---|
| Action | Alerts only — passive | Blocks traffic — inline |
| Placement | Out-of-band (span/tap) | Inline (traffic flows through) |
| Latency impact | None | Slight (inline processing) |
| False positive risk | Lower concern (just alerts) | Higher concern (blocks legit traffic) |

### Detection Methods
- **Signature-based:** Matches known attack patterns (fast; misses zero-days)
- **Anomaly-based:** Detects deviations from baseline (catches novel attacks; high FP rate)
- **Behavior-based:** Monitors process/network behavior patterns

### Common IDS/IPS Tools
| Tool | Type |
|---|---|
| Snort | Open-source NIDS/NIPS (signature-based) |
| Suricata | Open-source high-performance NIDS (multi-threaded) |
| Zeek (Bro) | Network analysis framework; generates logs |
| Security Onion | Full IDS/NSM platform (Suricata + Zeek + SIEM) |

---

## SIEM (Security Information and Event Management)

SIEM aggregates logs from across the environment, correlates events, and generates alerts.

### SIEM Core Functions
1. **Log collection** — ingest from firewalls, endpoints, servers, apps
2. **Normalization** — parse logs into common format
3. **Correlation** — match events across sources against rules
4. **Alerting** — generate alerts when rules match
5. **Dashboards** — visibility into security posture
6. **Retention** — long-term storage for forensics

### Common SIEM Platforms
| Platform | Type |
|---|---|
| Splunk | Commercial; most widely deployed |
| Microsoft Sentinel | Cloud-native SIEM (Azure) |
| IBM QRadar | Enterprise SIEM |
| Elastic SIEM (ELK) | Open-source stack |
| Wazuh | Open-source SIEM + EDR |
| Graylog | Open-source log management |

### SIEM Use Cases
```
Failed login brute force: >10 failed logins from same IP in 5 minutes
Impossible travel: Login from NY, then Moscow 30 minutes later
Lateral movement: SMB connections to multiple hosts from single source
Data exfiltration: Large outbound transfer to non-standard destination
Privilege escalation: Process running as NT AUTHORITY\SYSTEM spawned by user process
New admin account: User added to Domain Admins group outside change window
```

---

## EDR (Endpoint Detection and Response)

EDR monitors endpoints for malicious behavior, provides threat hunting capability, and enables response actions (isolation, kill process, collect forensics).

| Platform | Notes |
|---|---|
| CrowdStrike Falcon | Industry leader; cloud-native |
| Microsoft Defender for Endpoint | Integrated with Windows; strong at MSFT stack |
| SentinelOne | Strong autonomous response |
| Carbon Black (VMware) | Behavioral analysis focus |
| Wazuh | Open-source; good for labs |
| Velociraptor | Open-source DFIR + threat hunting |

---

## Log Analysis & Detection

### Key Log Sources
```
Windows Security Log   → 4624 (logon), 4688 (process creation), 4698 (sched task)
Windows Sysmon         → Enhanced process/network/file logging (MUST DEPLOY)
Linux /var/log/auth.log → SSH, sudo, su
Linux /var/log/syslog  → General system
Apache/Nginx access log → HTTP requests
Firewall logs          → Allow/deny, connection state
DNS logs               → Domain lookups (detect C2 beaconing, DGA)
```

### Sysmon — Key Event IDs
| Event ID | Description |
|---|---|
| 1 | Process creation |
| 3 | Network connection |
| 7 | Image loaded (DLL) |
| 8 | CreateRemoteThread (injection) |
| 10 | ProcessAccess (credential dumping) |
| 11 | FileCreate |
| 12/13/14 | Registry event |
| 22 | DNS query |

Deploy Sysmon with SwiftOnSecurity config: https://github.com/SwiftOnSecurity/sysmon-config

---

## Threat Hunting

Threat hunting is proactive searching for threats that have evaded automated detection.

### Hypothesis-Driven Hunting Process
```
1. Develop hypothesis    — "Attacker may be using living-off-the-land binaries for persistence"
2. Define IOAs/TTPs      — Scheduled tasks via schtasks.exe, powershell -EncodedCommand
3. Collect & query data  — SIEM/EDR query for those behaviors
4. Analyze findings      — Triage results, separate noise from signal
5. Respond               — Escalate confirmed threats
6. Improve               — Create detection rule to catch this automatically next time
```

### Common Hunting Queries (Splunk SPL)
```spl
# Find PowerShell encoded commands
index=windows EventCode=4688 Process_Name="*powershell*" CommandLine="*-enc*"

# Find lateral movement via SMB
index=windows EventCode=4624 LogonType=3 | stats count by src_ip, dest_ip

# Find scheduled task creation
index=windows EventCode=4698

# Find large DNS responses (potential tunneling)
index=dns | where answer_len > 1000 | stats count by query
```

---

## SOC Triage Process

When an alert fires:
```
1. Acknowledge alert
2. Initial triage — Is it a true positive or false positive?
3. Determine scope — Single endpoint or widespread?
4. Escalate if confirmed
5. Containment — Isolate affected system(s)
6. Investigation — Collect evidence, determine root cause
7. Remediation — Remove threat, patch, reset credentials
8. Post-incident review
```

---

## Network Segmentation

```
DMZ:     Public-facing servers (web, mail, DNS) — isolated from internal network
VLANs:   Separate user, server, IoT, management traffic
Zero Trust: Never trust, always verify — assume breach at all times
Microsegmentation: Firewall controls between individual workloads
```

---

## Key Frameworks for Blue Teams

| Framework | Use |
|---|---|
| MITRE ATT&CK | Map detections to attacker TTPs |
| MITRE D3FEND | Defensive countermeasures catalog |
| NIST CSF | Cybersecurity Framework: Identify/Protect/Detect/Respond/Recover |
| CIS Controls | 18 prioritized security controls |
| PICERL | Incident response lifecycle |

---

## Related Notes
- Incident-Response
- Threat-Intelligence
- Network-Defense-Blue-Team
- Vulnerability-Management
- Wireshark
- Linux-Commands
- Windows-Commands

## Sources

National Institute of Standards and Technology. (2007). *Guide to intrusion detection and prevention systems (IDPS)* (NIST SP 800-94). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-94.pdf

MITRE. (2024). *MITRE D3FEND™: A knowledge graph of cybersecurity countermeasures*. MITRE Corporation. https://d3fend.mitre.org

Center for Internet Security. (2021). *CIS controls v8*. CIS. https://www.cisecurity.org/controls

National Institute of Standards and Technology. (2024). *Cybersecurity framework 2.0*. U.S. Department of Commerce. https://www.nist.gov/cyberframework

---
<- [[Fundamentals-MOC]]