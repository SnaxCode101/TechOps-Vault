---
title: Threat Intelligence
tags:
  - fundamentals
  - threat-intelligence
  - CTI
  - MITRE
  - IOC
  - TTP
  - intermediate
topic: Threat Intelligence
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: MITRE ATT&CK enterprise matrix, NIST SP 800-150, OASIS STIX/TAXII standards
---

# 🧠 Threat Intelligence

## Overview
Cyber Threat Intelligence (CTI) is the process of collecting, processing, analyzing, and sharing information about threats to improve security decision-making. It transforms raw data into actionable intelligence that enables organizations to proactively defend against adversaries.

---

## Intelligence Types

| Type | Audience | Timescale | Purpose |
|---|---|---|---|
| **Strategic** | Executives, board | Months–years | High-level trends, APT campaigns, geopolitical risk |
| **Operational** | Security managers | Days–weeks | Specific campaigns, adversary intent, attack planning |
| **Tactical** | SOC analysts | Hours–days | TTPs — how adversaries operate |
| **Technical** | IR teams, engineers | Minutes–hours | IOCs — IPs, hashes, domains for blocking |

---

## Intelligence Lifecycle

```
1. Planning & Direction   — Define requirements: what threats matter to us?
2. Collection             — Gather data (feeds, OSINT, dark web, internal telemetry)
3. Processing             — Normalize, deduplicate, contextualize raw data
4. Analysis               — Assess relevance, confidence, attribution
5. Dissemination          — Share with appropriate teams (SIEM, firewall, SOC)
6. Feedback               — Evaluate utility, refine requirements
```

---

## Indicators of Compromise (IOCs) vs. Indicators of Attack (IOAs)

| | IOC | IOA |
|---|---|---|
| Definition | Evidence of past compromise | Evidence of active attack behavior |
| Example | SHA-256 hash of malware binary | PowerShell spawning cmd.exe, then connecting outbound |
| Value | Reactive (post-compromise detection) | Proactive (catch attacks in progress) |
| Lifespan | Short (attackers rotate IPs/domains quickly) | Longer (behaviors harder to change) |
| Pyramid of Pain level | Low (hash, IP) to Medium (domain) | High (TTPs, tools, procedures) |

### The Pyramid of Pain (David Bianco)
```
                     [TTPs]            <- Hardest for adversary to change
                   [Tools]
                [Network artifacts]
              [Host artifacts]
           [Domain names]
         [IP Addresses]
       [Hash values]                   <- Easiest to change (trivial for adversary)
```

Targeting higher levels of the pyramid causes more pain to the adversary.

---

## MITRE ATT&CK Framework

The MITRE ATT&CK framework is a globally-accessible knowledge base of adversary tactics, techniques, and procedures (TTPs) based on real-world observations.

### Enterprise Matrix Tactics (14)
| Tactic | ID | Description |
|---|---|---|
| Reconnaissance | TA0043 | Gathering info before the attack |
| Resource Development | TA0042 | Acquiring infrastructure, accounts, tools |
| Initial Access | TA0001 | Getting into the network |
| Execution | TA0002 | Running malicious code |
| Persistence | TA0003 | Maintaining foothold |
| Privilege Escalation | TA0004 | Gaining higher permissions |
| Defense Evasion | TA0005 | Avoiding detection |
| Credential Access | TA0006 | Stealing credentials |
| Discovery | TA0007 | Exploring the environment |
| Lateral Movement | TA0008 | Moving through the network |
| Collection | TA0009 | Gathering target data |
| Command & Control | TA0011 | Communicating with compromised systems |
| Exfiltration | TA0010 | Stealing data |
| Impact | TA0040 | Disrupt, destroy, or manipulate systems |

### Using ATT&CK Navigator
- Map observed techniques to ATT&CK matrix
- Identify detection gaps (heat map)
- Plan purple team exercises
- Report adversary attribution

ATT&CK Navigator: https://mitre-attack.github.io/attack-navigator/

---

## Common Threat Actor Categories

| Category | Motivation | Examples |
|---|---|---|
| Nation-state (APT) | Espionage, disruption, geopolitical | APT28 (Russia), APT41 (China), Lazarus (DPRK) |
| Cybercriminal | Financial gain | REvil, Conti, LockBit ransomware groups |
| Hacktivist | Political, ideological | Anonymous |
| Insider threat | Financial, disgruntled, espionage | Trusted user with access |
| Script kiddie | Notoriety, curiosity | Using tools without deep knowledge |

---

## Threat Intelligence Sharing Standards

### STIX (Structured Threat Information eXpression)
JSON-based language for describing cyber threat intelligence. Objects include: Indicator, Malware, Threat-Actor, Attack-Pattern, Campaign, Course-of-Action.

```json
{
  "type": "indicator",
  "id": "indicator--...",
  "name": "Malicious IP",
  "pattern": "[ipv4-addr:value = '192.168.1.1']",
  "valid_from": "2026-03-26T00:00:00Z"
}
```

### TAXII (Trusted Automated eXchange of Indicator Information)
Protocol for sharing STIX data over HTTPS. Two API endpoints: **Collection** (pull) and **Channel** (push).

---

## Threat Intelligence Feeds & Platforms

### Free Feeds
| Source | Data Type | URL |
|---|---|---|
| AlienVault OTX | Multi-type IOCs | otx.alienvault.com |
| Abuse.ch | Malware, botnet C2 | abuse.ch |
| URLhaus | Malicious URLs | urlhaus.abuse.ch |
| Feodo Tracker | Botnet C2 IPs | feodotracker.abuse.ch |
| MalwareBazaar | Malware samples | bazaar.abuse.ch |
| Shodan | Exposed infrastructure | shodan.io |
| CISA Known Exploited Vulns | KEV list | cisa.gov/known-exploited-vulnerabilities-catalog |

### Commercial Platforms
| Platform | Description |
|---|---|
| Recorded Future | Premium intel feeds, risk scoring |
| Mandiant Threat Intel | APT tracking, incident response intel |
| CrowdStrike Intel | Adversary tracking |
| IBM X-Force Exchange | Threat sharing platform |

### Open-Source TIP Platforms
| Platform | Description |
|---|---|
| MISP | Open-source threat intelligence platform |
| OpenCTI | Structured CTI management |
| TheHive | Incident response + CTI integration |
| Cortex | Automated observable analysis |

---

## CTI Integration with Security Tools

```
SIEM  <- Ingest IOC feeds as threat intelligence rules
Firewall/IPS <- Block known malicious IPs/domains from feeds
EDR <- Hunt for IOCs and TTPs across endpoints
SOAR <- Automate enrichment (look up IPs in threat feeds during alert triage)
```

---

## Related Notes
- Incident-Response
- Malware-Analysis
- Network-Defense-Blue-Team
- Vulnerability-Management
- Active-Directory

## Sources

MITRE. (2024). *MITRE ATT&CK®: Enterprise matrix*. MITRE Corporation. https://attack.mitre.org

National Institute of Standards and Technology. (2016). *Guide to cyber threat information sharing* (NIST SP 800-150). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-150.pdf

OASIS Open. (2021). *STIX and TAXII: Cyber threat intelligence standards documentation*. OASIS. https://oasis-open.github.io/cti-documentation/

Bianco, D. J. (2013). *The pyramid of pain*. Enterprise Detection & Response. https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html

---
<- [[Fundamentals-MOC]]