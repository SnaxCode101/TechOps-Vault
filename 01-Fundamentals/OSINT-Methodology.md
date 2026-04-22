---
title: OSINT Methodology
tags:
  - fundamentals
  - recon
  - threat-intelligence
topic: Reconnaissance & Intelligence
difficulty: foundational
created: 2026-03-26
updated: 2026-03-26
source: OSINT Framework, MITRE ATT&CK TA0043 (reconnaissance), PTES intelligence gathering, SANS OSINT paper
---

# 🔭 OSINT Methodology

## Overview
Open Source Intelligence (OSINT) is the collection and analysis of information gathered from publicly available sources. It is the first phase of most offensive operations, but is equally valuable for defenders building threat intelligence, investigators conducting due diligence, and HR teams performing background research. OSINT produces no traffic to the target — it is entirely passive.

**MITRE ATT&CK:** Tactic TA0043 — Reconnaissance covers OSINT-related techniques.

---

## OSINT Categories

| Category | Examples |
|---|---|
| **Technical** | IP ranges, domains, DNS records, SSL certs, open ports (Shodan), ASN data |
| **Organisational** | Company structure, subsidiaries, acquisitions, office locations, job postings |
| **Personnel** | Employee names, roles, email addresses, social media, professional profiles |
| **Infrastructure** | Web technologies, email servers, code repositories, exposed credentials |
| **Geospatial** | Physical locations, satellite imagery, building layouts |

---

## OSINT Process

```
1. Define Scope & Objectives
   → What exactly are you trying to find?
   → Who/what is the target?
   → What would be most valuable (credentials, infrastructure, personnel)?

2. Passive Collection
   → No direct contact with target systems
   → Google, LinkedIn, Shodan, DNS lookups, certificate transparency

3. Active Collection (semi-OSINT)
   → Some interaction: DNS zone transfer, subdomain brute-force
   → Target may see traffic from your IP

4. Analysis & Correlation
   → Connect data points across sources
   → Build an org chart, technology map, attack surface map

5. Documentation
   → Record every finding with source and timestamp
   → Use a tool like Maltego, Obsidian, or a spreadsheet
```

---

## Target: Domain / Organisation

### DNS Intelligence
```bash
# Basic DNS lookup
nslookup target.com
dig target.com ANY

# MX records (email servers — useful for phishing infrastructure intel)
dig MX target.com

# TXT records (SPF, DKIM, DMARC, verification tokens — reveals tech stack)
dig TXT target.com

# DNS zone transfer attempt (rarely succeeds on modern DNS but worth trying)
dig axfr target.com @ns1.target.com
host -t axfr target.com ns1.target.com

# Reverse DNS lookup
dig -x 93.184.216.34
host 93.184.216.34
```

### Subdomain Enumeration
```bash
# Certificate Transparency logs — best passive method
# Every TLS cert issued is publicly logged
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq '.[].name_value' | sort -u

# subfinder (fast, passive, uses many sources)
subfinder -d target.com -o subdomains.txt

# amass (comprehensive, both passive and active modes)
amass enum -passive -d target.com
amass enum -active -d target.com   # Active — sends DNS queries

# dnsx (DNS resolution of discovered subdomains)
cat subdomains.txt | dnsx -resp
```

### WHOIS
```bash
whois target.com          # Domain registration info (registrar, dates, nameservers)
whois 93.184.216.34       # IP WHOIS (ASN, organisation, abuse contacts)
```

### ASN / IP Range Discovery
```bash
# Find all IP ranges owned by an organisation
# Via BGP looking glass or ASN lookup services
curl -s "https://api.bgpview.io/search?query_term=Target+Corp" | jq .
# Then: curl -s "https://api.bgpview.io/asn/AS12345/prefixes" | jq .
```

---

## Target: People / Personnel

### Email Discovery
```bash
# theHarvester — emails, subdomains, hosts from public sources
theHarvester -d target.com -b all
theHarvester -d target.com -b linkedin,google,bing

# hunter.io (web service) — email format discovery
# https://hunter.io/domain-search — shows email pattern + verified addresses

# Common email formats to guess/validate:
# firstname.lastname@company.com
# f.lastname@company.com
# firstname@company.com
# flastname@company.com
```

### LinkedIn & Professional Profiles
```
Key data to collect:
- Employee names and titles → build org chart
- Department structure → identify key targets (IT, Finance, HR, Executives)
- Technology stack clues from job postings ("must know AWS", "experience with Okta")
- Recently departed employees → may still have access, or be willing to talk
- New hires → may not yet know internal policies

Tools:
- LinkedIn search (manual + LinkedIn Sales Navigator)
- CrossLinked: scrapes LinkedIn for email format validation
- PhoneBook.cz: aggregates LinkedIn data
```

### Username & Profile Search
```bash
# sherlock — search 300+ social media sites for a username
sherlock username

# whatsmyname.app — similar multi-site search
# https://whatsmyname.app

# Namechk.com — username availability checker (implies account exists if unavailable)
```

### Public Records & Data Breaches
```
Have I Been Pwned:   https://haveibeenpwned.com    ← check if email in known breach
Dehashed:            https://dehashed.com           ← search leaked database content (subscription)
IntelX:              https://intelx.io              ← dark web, leak search
Pastebin/Paste sites: search for email/domain       ← accidental credential exposure
```

---

## Target: Infrastructure / Technical

### Shodan — Internet-Facing Asset Discovery
```bash
# Install CLI
pip install shodan
shodan init YOUR_API_KEY

# Search by organisation
shodan search "org:Target Corporation"

# Search by domain
shodan search "hostname:target.com"

# Search for specific tech
shodan search "hostname:target.com product:nginx"
shodan search "hostname:target.com port:3389"    # RDP exposed

# Get details on a specific IP
shodan host 93.184.216.34

# On web:
# https://www.shodan.io
# Useful filters: org, hostname, port, product, os, before/after dates
```

### Censys — Similar to Shodan
```
https://search.censys.io
Searches: certificates, hosts, domain data
Particularly useful for certificate-based org discovery
```

### Google Dorks
```
Site-specific:
site:target.com                          → all indexed pages
site:target.com -www                     → non-www subdomains indexed
site:target.com filetype:pdf             → PDF documents
site:target.com filetype:xlsx OR xls     → spreadsheets (may contain data)
site:target.com inurl:admin              → admin panels
site:target.com intext:"internal use"    → accidentally public internal docs

Sensitive data:
intitle:"index of" site:target.com       → directory listings
inurl:"/phpinfo.php" site:target.com     → PHP info pages
"@target.com" "password"                 → leaked credentials on open web
site:pastebin.com "target.com"           → pastes mentioning target
site:github.com "target.com" password    → credentials in GitHub

Vulnerabilities:
inurl:".git" site:target.com             → exposed .git directories
inurl:".env" site:target.com             → .env files with credentials
inurl:"/wp-admin" site:target.com        → WordPress admin panels
```

### Wayback Machine — Historical Content
```bash
# See how a site looked historically — find old admin panels, removed pages
# https://web.archive.org

# Waybackurls CLI — extract all URLs ever archived for a domain
echo "target.com" | waybackurls

# gau (Get All URLs) — also queries Wayback, Common Crawl, OTX
gau target.com
```

### GitHub / Code Repository Search
```bash
# Search GitHub for credentials/keys
# https://github.com/search?q=target.com+password&type=code
# https://github.com/search?q=target.com+api_key&type=code

# GitDorker / TruffleHog — automated GitHub secret scanning
trufflehog github --repo https://github.com/targetorg/reponame

# Look for:
# API keys, passwords, connection strings
# Internal domain names, IP addresses
# Architecture details from .tf (Terraform) or .yml (CI/CD) files
```

### Job Postings — Technology Intelligence
```
Job ads reveal tech stack without any scanning:
"Experience with Palo Alto Networks firewalls" → firewall vendor
"Manage our Okta SSO environment" → identity provider
"AWS EC2 and S3 administration" → cloud provider
"Maintain our Splunk SIEM" → SIEM product
"CrowdStrike Falcon administration" → EDR product

Aggregate job postings from LinkedIn, Indeed, company careers page
```

---

## OSINT Tools Summary

| Tool | Category | Free? | URL/Install |
|---|---|---|---|
| theHarvester | Multi-purpose | ✅ | `apt install theharvester` |
| Maltego | Visualisation + OSINT | Freemium | maltego.com |
| Shodan | Infrastructure | Freemium | shodan.io |
| Censys | Infrastructure | Freemium | search.censys.io |
| subfinder | Subdomain enum | ✅ | github.com/projectdiscovery/subfinder |
| amass | Subdomain enum | ✅ | github.com/owasp-amass/amass |
| sherlock | Username search | ✅ | `pip install sherlock-project` |
| recon-ng | Framework | ✅ | `apt install recon-ng` |
| SpiderFoot | Automated OSINT | ✅ | spiderfoot.net |
| hunter.io | Email discovery | Freemium | hunter.io |
| crt.sh | Cert transparency | ✅ | crt.sh |
| OSINT Framework | Directory | ✅ | osintframework.com |
| IntelX | Breach/dark web | Freemium | intelx.io |
| TruffleHog | Secret scanning | ✅ | github.com/trufflesecurity/trufflehog |

---

## Operational Security During OSINT

Even passive OSINT can reveal your interest:
- Web searches on your real IP may appear in server logs if you visit target sites
- LinkedIn profile views notify the target (use private browsing or LinkedIn's "private mode")
- Some services (hunter.io, Shodan) log queries

**Defensive OSINT hygiene:**
```
Use a VPN or Tor for passive browsing recon
Set LinkedIn to private browsing mode before viewing target profiles
Use sock puppet accounts for social media recon where appropriate
Do not visit target's actual website during early passive recon
Use API access (Shodan CLI, etc.) rather than browser where possible
```

---

## OSINT Report Template

```
Target: [Organisation / Individual]
Date: [Date range of collection]
Analyst: [Your name]

1. Domain/Infrastructure Summary
   - Primary domain, IP ranges, ASN
   - Subdomains discovered
   - Technologies identified (web server, CDN, email, auth)

2. Personnel Summary
   - Key individuals (names, titles, emails)
   - Org chart / hierarchy
   - Social media presence

3. Credential Exposure
   - Breach data found (source, date, type)
   - Exposed secrets in code repositories

4. Attack Surface Summary
   - Publicly exposed services (Shodan)
   - Potentially vulnerable tech versions
   - Misconfigurations observed

5. Sources
   - [List every source used with URL and date accessed]
```

---

## Related Notes
- Social-Engineering
- Penetration-Testing-Methodology
- Threat-Intelligence
- Vulnerability-Research-Reference
- Active-Directory

## Sources

Justin Nordine. (n.d.). *OSINT framework: Open source intelligence gathering*. https://osintframework.com

MITRE. (2024). *MITRE ATT&CK®: Tactic TA0043 — reconnaissance*. MITRE Corporation. https://attack.mitre.org/tactics/TA0043/

Penetration Testing Execution Standard. (n.d.). *Intelligence gathering*. PTES. http://www.pentest-standard.org/index.php/Intelligence_Gathering

SANS Institute. (2019). *Open-source intelligence gathering*. SANS Institute. https://www.sans.org/white-papers/33100/

---
<- [[Fundamentals-MOC]]