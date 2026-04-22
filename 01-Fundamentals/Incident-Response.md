---
title: Incident Response
tags:
  - fundamentals
  - incident-response
  - IR
  - blue-team
  - DFIR
  - intermediate
topic: Blue Team / DFIR
difficulty: intermediate
created: 2026-03-23
updated: 2026-04-04
source: NIST SP 800-61 Rev 2, MITRE ATT&CK, SANS Incident Handler's Handbook
---

# Incident Response

## Overview
Incident Response (IR) is the structured methodology for handling security incidents — from detecting a breach to restoring normal operations. A well-executed IR process minimizes damage, reduces recovery time, preserves evidence, and informs future defenses.

## Why It Matters in Security
Every security practitioner — not just blue teamers — needs to understand IR. Pentesters produce findings that IR teams act on. SOC analysts run the IR process daily. Understanding the lifecycle, evidence handling, and forensic techniques is required for certifications, career growth, and real-world effectiveness.

---

## NIST IR Lifecycle (SP 800-61)

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────────┐     ┌────────────────┐
│ Preparation  │────>│  Detection &      │────>│  Containment,        │────>│  Post-Incident │
│              │     │  Analysis         │     │  Eradication &       │     │  Activity      │
└──────────────┘     └──────────────────┘     │  Recovery            │     └────────────────┘
                                               └──────────────────────┘
```

### Phase 1 — Preparation
Build the capability before incidents occur.
- Develop and maintain an IR plan and playbooks
- Establish an IR team (CSIRT / CIRT)
- Set up logging, SIEM, and monitoring
- Conduct tabletop exercises and IR drills
- Prepare forensic tools and jump bags
- Define roles, escalation paths, and communication channels
- Establish legal and law enforcement contacts

### Phase 2 — Detection and Analysis
Identify that an incident has occurred and understand its scope.

**Detection sources:**
- SIEM alerts
- IDS/IPS alerts
- Antivirus / EDR alerts
- Log anomalies (failed logins, unusual outbound traffic)
- User reports
- Threat intelligence feeds
- Third-party notification (e.g., law enforcement, ISAC)

**Key questions during analysis:**
- What systems are affected?
- What is the attack vector?
- What is the timeline of events?
- What data or services are impacted?
- Is the attacker still active?
- What is the severity / business impact?

**Incident severity classification (example):**
| Severity | Description | Response Time |
|---|---|---|
| Critical (P1) | Active breach, data exfiltration, ransomware | Immediate |
| High (P2) | Confirmed compromise, lateral movement | Within 1 hour |
| Medium (P3) | Suspected compromise, policy violation | Within 4 hours |
| Low (P4) | Anomalous activity, low confidence | Within 24 hours |

### Phase 3 — Containment
Stop the spread of the incident.

**Short-term containment:**
- Isolate affected systems (network quarantine)
- Block malicious IPs/domains at firewall
- Disable compromised accounts
- Preserve evidence before taking action

**Long-term containment:**
- Apply patches or configuration changes
- Replace compromised systems with clean images
- Implement enhanced monitoring on adjacent systems

> ⚠️ Take forensic images BEFORE wiping or reimaging — evidence is lost otherwise.

### Phase 3b — Eradication
Remove the threat from the environment.
- Identify and remove all malware, backdoors, and persistence mechanisms
- Patch vulnerabilities that were exploited
- Reset all potentially compromised credentials
- Audit and revoke unnecessary access

### Phase 3c — Recovery
Restore systems to normal operations.
- Restore from known-good backups
- Verify integrity of restored systems
- Monitor closely for re-infection or reattack
- Gradually return systems to production
- Document all actions taken

### Phase 4 — Post-Incident Activity (Lessons Learned)
Improve future response based on this incident.
- Hold a post-incident review meeting (usually within 2 weeks)
- Document: timeline, root cause, indicators, response actions, gaps
- Update IR plan, playbooks, and detection rules
- Brief stakeholders and management
- File reports as required (regulatory, legal)

---

## Order of Volatility (Digital Forensics)

When collecting evidence, gather the most volatile (shortest-lived) data first:

```
1. CPU registers and cache
2. RAM (running processes, network connections, encryption keys)
3. Swap / paging file
4. Running processes (tasklist, netstat output)
5. Network connections (active sessions)
6. Disk (file system, logs, artifacts)
7. Remote / cloud logs
8. Archived / backup media
```

> RAM is the most valuable and most perishable. Use tools like `winpmem`, `LiME`, or `FTK Imager` to capture it before powering off.

---

## Evidence Handling

**Chain of custody:** A documented record of who accessed evidence, when, and for what purpose. Required for legal proceedings.

**Key principles:**
- Never work on original evidence — always work on a forensic copy (image)
- Hash evidence immediately: `sha256sum image.dd`
- Document every action taken
- Store evidence securely with access controls
- Maintain chain of custody forms

**Disk imaging:**
```bash
# Linux — dd
dd if=/dev/sda of=/mnt/external/image.dd bs=4M status=progress

# Linux — dc3dd (better; logs hash automatically)
dc3dd if=/dev/sda of=/mnt/external/image.dd hash=sha256 log=image.log

# Verify integrity
sha256sum image.dd
```

---

## Key Log Sources to Examine

### Windows
| Log | Location | What to Look For |
|---|---|---|
| Security | Event Viewer > Windows Logs > Security | Logon events (4624, 4625), privilege use (4672), account changes (4720) |
| System | Event Viewer > Windows Logs > System | Service changes, driver loads, reboots |
| PowerShell | `Microsoft-Windows-PowerShell/Operational` | Encoded commands, download cradles |
| Sysmon | `Microsoft-Windows-Sysmon/Operational` | Process creation, network connections, file creation |
| Prefetch | `C:\Windows\Prefetch\` | Evidence of program execution |
| Registry | HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Run | Persistence |

**Key Windows Event IDs:**
| Event ID | Meaning |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4648 | Logon with explicit credentials (lateral movement indicator) |
| 4672 | Special privileges assigned (admin logon) |
| 4688 | New process created |
| 4698 | Scheduled task created |
| 4720 | User account created |
| 4732 | User added to security-enabled local group |
| 7045 | New service installed |

### Linux
| Log | Location | Content |
|---|---|---|
| Auth log | `/var/log/auth.log` or `/var/log/secure` | SSH logins, sudo usage, su |
| Syslog | `/var/log/syslog` | General system events |
| Cron | `/var/log/cron` | Scheduled task execution |
| Bash history | `~/.bash_history` | Commands run (can be cleared) |
| Web server | `/var/log/apache2/` or `/var/log/nginx/` | HTTP requests, web shell usage |
| Lastlog | `lastlog` command | Last login per user |
| Wtmp | `last` command | Login history |

---

## Common Malware Types

| Type | Description | Detection Indicators |
|---|---|---|
| **Virus** | Self-replicating; attaches to legitimate files | File hash changes; unexpected file modifications |
| **Worm** | Self-replicating; spreads across network without user action | High network traffic; unexpected connections between hosts |
| **Trojan** | Disguised as legitimate software; opens backdoor | Unexpected process; outbound connections |
| **Ransomware** | Encrypts files; demands payment | Mass file encryption; ransom note; shadow copy deletion |
| **RAT** | Remote Access Trojan; full remote control | Outbound C2 traffic; unexpected processes |
| **Rootkit** | Hides itself and other malware from OS/AV | Discrepancies between OS tools and forensic tools |
| **Keylogger** | Records keystrokes | Unexpected processes; data exfiltration traffic |
| **Spyware** | Collects user data; sends to attacker | Unexpected outbound traffic; browser modifications |
| **Botnet** | Infected host becomes part of attacker-controlled network | Regular beaconing traffic; C2 communication |
| **Dropper** | Delivers and installs other malware | Temporary files; child processes spawned |
| **Fileless Malware** | Lives only in memory; no files on disk | PowerShell in memory; LOLbin abuse; no file artifacts |

---

## IR Tools Reference

| Tool | Use |
|---|---|
| **Wireshark** / tcpdump | Packet capture and analysis |
| **Volatility** | Memory forensics (RAM image analysis) |
| **Autopsy** / **FTK** | Disk forensics |
| **KAPE** | Rapid Windows artifact collection |
| **Sysmon** | Enhanced Windows logging |
| **Velociraptor** | Endpoint hunting and forensic collection at scale |
| **TheHive** | Incident case management |
| **MISP** | Threat intelligence platform |
| **Splunk** / **Elastic (ELK)** | SIEM and log analysis |
| **Yara** | Malware pattern matching |
| **CyberChef** | Data transformation and decoding |

---

## MITRE ATT&CK Framework

ATT&CK is a globally accessible knowledge base of adversary tactics and techniques based on real-world observations.

**Tactics (the "why"):** Reconnaissance → Resource Development → Initial Access → Execution → Persistence → Privilege Escalation → Defense Evasion → Credential Access → Discovery → Lateral Movement → Collection → Command and Control → Exfiltration → Impact

**Techniques (the "how"):** Specific methods within each tactic. Example: T1059.001 = PowerShell (under Execution tactic).

Reference: https://attack.mitre.org/

---

## Key Terms
| Term | Definition |
|---|---|
| CSIRT | Computer Security Incident Response Team |
| IOC | Indicator of Compromise — evidence that a system has been breached |
| TTPs | Tactics, Techniques, and Procedures — attacker behavior patterns |
| DFIR | Digital Forensics and Incident Response |
| C2 / C&C | Command and Control — attacker infrastructure controlling malware |
| Beaconing | Regular outbound connections from malware to C2 server |
| Lateral Movement | Moving from one compromised system to others within the network |
| Persistence | Mechanisms malware uses to survive reboots |
| Exfiltration | Unauthorized transfer of data out of the target network |
| Triage | Initial rapid assessment to prioritize incident response actions |

## Related Notes
- CIA-Triad
- Active-Directory
- Wireshark
- Linux-Commands
- Windows-Commands

## Sources

National Institute of Standards and Technology. (2012). *Computer security incident handling guide* (NIST SP 800-61 Rev 2). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

MITRE. (2024). *MITRE ATT&CK®: Enterprise matrix*. MITRE Corporation. https://attack.mitre.org/

SANS Institute. (2021). *Incident handler's handbook*. SANS Institute. https://www.sans.org/white-papers/incident-handlers-handbook/

## See Also

- CIA-Triad
- Penetration-Testing-Methodology
- Wireshark
- Nmap
- ServiceNow-Flow-Designer

---

## IR Containment Checklist

Use during Phase 3 — run top-to-bottom, check off each action, document timestamps.

### Immediate (0–15 min)
- [ ] Confirm incident is real (not a false positive)
- [ ] Assign incident commander and scribe
- [ ] Open incident ticket / case in tracking system
- [ ] Notify IR team lead and management per escalation path
- [ ] Begin timeline log — record every action with UTC timestamps

### Network Isolation
- [ ] Identify affected host(s) — IP, hostname, owner, business function
- [ ] Determine blast radius — what else could be compromised?
- [ ] Isolate host at network level (VLAN quarantine, firewall ACL, switch port shutdown)
- [ ] **Do NOT power off** unless instructed — RAM forensics first
- [ ] Block malicious IPs/domains at perimeter firewall and DNS
- [ ] Disable compromised service accounts / API keys immediately

### Evidence Preservation
- [ ] Capture RAM image before any shutdown: `winpmem`, `LiME`, or `FTK Imager`
- [ ] Collect running process list: `tasklist /svc` (Win) / `ps aux` (Linux)
- [ ] Collect active network connections: `netstat -ano` / `ss -tulnp`
- [ ] Export relevant event logs (Security, System, PowerShell, Sysmon)
- [ ] Hash all collected artifacts: `sha256sum <file>`
- [ ] Photograph screen if physical access — document chain of custody

### Credential & Access Triage
- [ ] Reset password of any confirmed-compromised accounts
- [ ] Revoke and rotate any exposed API keys, certificates, tokens
- [ ] Audit privileged group membership — remove anomalous accounts
- [ ] Check for new local admin accounts or scheduled tasks

### Communication & Legal
- [ ] Notify legal counsel if data exfiltration is suspected
- [ ] Assess regulatory notification obligations (HIPAA 60-day, GDPR 72-hour, etc.)
- [ ] Preserve all communication records (do not delete emails/tickets)
- [ ] Coordinate with HR if insider threat is suspected

---

## IR Communication Templates

### Initial Notification (to management / stakeholders)
```
Subject: Security Incident Notification — [Severity] — [Brief Description]

Summary:
At [HH:MM UTC], [team/tool] detected a [type of incident] affecting [system/service].

Current Status:
- Scope: [# systems / users affected]
- Containment: [In progress / Achieved]
- Business Impact: [Service degraded / no user impact / data at risk]

Immediate Actions Taken:
1. [Action 1 + timestamp]
2. [Action 2 + timestamp]

Next Steps:
- [Action] by [time/owner]
- Next update at [HH:MM UTC]

Incident Ticket: [Link]
Incident Commander: [Name]
```

### Stakeholder Update (every 30–60 min during active incident)
```
Subject: UPDATE [#N] — Security Incident — [Brief Description]

Time: [HH:MM UTC]
Status: [Active / Contained / Eradicating / Recovering]

What we know:
- [Finding 1]
- [Finding 2]

What we've done since last update:
- [Action + timestamp]

Current blockers:
- [Blocker if any]

ETA for next update: [HH:MM UTC]
```

### All-Clear / Resolution Notice
```
Subject: RESOLVED — Security Incident — [Brief Description]

The security incident reported at [date/time] has been resolved as of [date/time UTC].

Root Cause: [Brief description]
Systems Affected: [List]
Duration: [X hours/minutes]
Data Impact: [None confirmed / Under investigation / [specific impact]]

Remediation Actions:
1. [Action]
2. [Action]

A full post-incident report will be distributed within [X] business days.

Questions: Contact [IR lead / email]
```

---

## Sources (updated)

National Institute of Standards and Technology. (2012). *Computer security incident handling guide* (NIST SP 800-61 Rev 2). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

SANS Institute. (2021). *Incident handler's handbook*. SANS Institute. https://www.sans.org/white-papers/incident-handlers-handbook/

MITRE. (2024). *MITRE ATT&CK®: Enterprise matrix*. MITRE Corporation. https://attack.mitre.org/

---
<- [[Fundamentals-MOC]]