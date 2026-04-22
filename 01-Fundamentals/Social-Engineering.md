---
title: Social Engineering
tags:
  - fundamentals
topic: Social Engineering
difficulty: foundational
created: 2026-03-26
updated: 2026-03-26
source: Cialdini (2006) influence, NIST SP 800-50, Verizon DBIR
---

# 🎭 Social Engineering

## Overview
Social engineering is the manipulation of people into performing actions or revealing confidential information. It exploits human psychology rather than technical vulnerabilities. According to Verizon DBIR, social engineering is involved in the majority of data breaches — making it one of the most important attack vectors to understand and defend against.

---

## Attack Types

### Phishing
Mass email campaigns impersonating trusted entities to steal credentials or deliver malware.

**Indicators of phishing:**
- Urgent / threatening language ("Your account will be suspended")
- Mismatched sender domain (support@paypa1.com)
- Generic greeting ("Dear Customer")
- Suspicious links (hover to preview URL)
- Unexpected attachments
- Requests for credentials or personal data

### Spear Phishing
Targeted phishing using personal details about the victim (name, role, colleague names, recent projects). Far more effective than mass phishing.

### Whaling
Spear phishing targeting senior executives (CEO, CFO). Often combined with **BEC (Business Email Compromise)** to authorize wire transfers.

### Vishing (Voice Phishing)
Phone-based social engineering. Attacker impersonates IT support, bank, government agency.

**Example script:**
> "Hi, this is Mike from IT. We've detected suspicious activity on your account. I need to verify your identity — can you confirm your password?"

### Smishing (SMS Phishing)
SMS-based phishing. Often uses urgency: "Your package is delayed. Verify here: [malicious URL]"

### Pretexting
Creating a fabricated scenario (pretext) to extract information. Example: attacker poses as a new IT vendor who needs network access credentials.

### Baiting
Leaving infected USB drives in parking lots or lobbies. Curiosity causes targets to plug them in.

### Quid Pro Quo
Offering something valuable (tech support, gift) in exchange for information or access.

### Tailgating / Piggybacking
Physically following an authorized person through a secured door.

### Watering Hole Attack
Compromising a website frequently visited by the target group. Victims get infected just by visiting.

---

## Psychological Principles (Cialdini)

Social engineers exploit these universal psychological triggers:

| Principle | How It's Used |
|---|---|
| **Authority** | Impersonate IT, CEO, government, bank |
| **Urgency/Scarcity** | "Act now or your account will be locked" |
| **Social Proof** | "Everyone in your department has already done this" |
| **Liking** | Build rapport before making the request |
| **Reciprocity** | Offer something first to create obligation |
| **Commitment** | Small ask first; escalate once committed |

---

## OSINT for Social Engineering

Attackers use Open Source Intelligence to research targets before launching attacks:

```
LinkedIn       → Job titles, colleagues, org structure, technologies used
Facebook/IG    → Personal details, relationships, locations
Company website → Email format, staff names, phone numbers
Hunter.io      → Email address discovery
Shodan         → Exposed infrastructure
Google dorks   → site:company.com filetype:pdf, "staff directory"
WHOIS          → Domain registration info
```

Common email format patterns:
- firstname.lastname@company.com
- f.lastname@company.com
- firstname@company.com

---

## Phishing Simulation & Testing Tools

| Tool | Purpose |
|---|---|
| GoPhish | Open-source phishing simulation platform |
| SET (Social Engineer Toolkit) | Full SE attack toolkit (Kali Linux) |
| King Phisher | Phishing campaign framework |
| Evilginx2 | Phishing proxy for 2FA bypass |
| Modlishka | Reverse proxy phishing (captures 2FA tokens) |

### GoPhish Quick Start
```bash
# Run GoPhish
./gophish

# Access dashboard: http://localhost:3333
# Default admin:gophish (change immediately)
# Configure: sending profile, landing page, email template, campaign
```

---

## Defense & Awareness

### Technical Controls
| Control | What It Addresses |
|---|---|
| Email filtering (SEG) | Block phishing emails at gateway |
| SPF / DKIM / DMARC | Prevent email spoofing of your domain |
| MFA (multi-factor auth) | Mitigate credential theft |
| URL filtering / proxy | Block malicious links |
| Anti-malware | Block phishing payloads |
| DNS filtering | Block C2 and phishing domains |

### SPF / DKIM / DMARC
```
SPF   (Sender Policy Framework)  — DNS TXT record listing authorized mail servers
DKIM  (DomainKeys Identified Mail) — Cryptographic signature on outgoing emails
DMARC (Domain-based Message Auth) — Policy for handling SPF/DKIM failures (none/quarantine/reject)

Check with:
nslookup -type=TXT _dmarc.domain.com
nslookup -type=TXT domain.com (for SPF record)
```

### Human Controls
1. **Security awareness training** — regular, realistic phishing simulations
2. **Verify requests** via separate channel (call back on known number, not provided one)
3. **Report mechanisms** — easy one-click reporting of suspicious emails
4. **Clear policies** — IT will never ask for passwords
5. **Physical security** — badge access, tailgating awareness
6. **USB policy** — never plug in unknown USB devices

---

## Phishing Email Analysis

```bash
# Analyze email headers (find true sending server)
# Look for: X-Originating-IP, Received: from
# Tool: MXToolbox Email Header Analyzer

# Check link reputation
# virustotal.com, urlscan.io, urlhaus.abuse.ch

# Sandbox attachment
# any.run, hybrid-analysis.com, triage.abuse.ch
```

---

## Related Notes
- OSINT-Recon ← (create if needed)
- Penetration-Testing-Methodology
- Incident-Response
- Threat-Intelligence
- Web-Application-Testing

## Sources

Cialdini, R. B. (2006). *Influence: The psychology of persuasion* (revised ed.). Harper Business.

National Institute of Standards and Technology. (2003). *Building an information technology security awareness and training program* (NIST SP 800-50). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-50.pdf

Verizon. (2024). *Data breach investigations report*. Verizon. https://www.verizon.com/business/resources/reports/dbir/

---
<- [[Fundamentals-MOC]]