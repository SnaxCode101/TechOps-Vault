---
title: Nikto
tags: |-
  - tool
  - nikto
  - web-scanner
  - vulnerability
  - recon
  - foundational
category: recon
platform: cross-platform (Perl)
install: apt install nikto / https://github.com/sullo/nikto
created: 2026-03-26
updated: 2026-03-26
difficulty: foundational
---

# 🕷️ Nikto

## What It Is
Nikto is an open-source web server scanner that performs comprehensive tests against web servers for dangerous files, outdated software, misconfigurations, default credentials, and security header weaknesses. It is not a stealthy tool — it generates significant log entries but is excellent for quick surface-area assessment.

## Installation
```bash
# Kali / Debian / Ubuntu
sudo apt install nikto

# macOS
brew install nikto

# From source
git clone https://github.com/sullo/nikto
cd nikto/program
perl nikto.pl -h
```

---

## Basic Syntax
```bash
nikto -h TARGET [-p PORT] [OPTIONS]
```

---

## Common Commands

### Basic scan
```bash
nikto -h http://TARGET_IP
nikto -h https://TARGET_IP    # HTTPS
nikto -h TARGET_IP -p 8080    # Custom port
```

### Scan with output saved
```bash
nikto -h http://TARGET_IP -o nikto_output.txt
nikto -h http://TARGET_IP -o nikto_output.html -Format htm
nikto -h http://TARGET_IP -o nikto_output.xml -Format xml
```

### Specify host header (virtual hosting)
```bash
nikto -h http://TARGET_IP -vhost subdomain.target.com
```

### Scan through Burp proxy
```bash
nikto -h http://TARGET_IP -useproxy http://127.0.0.1:8080
```

### Tune scan (select test categories)
```bash
nikto -h http://TARGET_IP -Tuning x
# Tuning values:
# 0 - File Upload
# 1 - Interesting File / Seen in logs
# 2 - Misconfiguration / Default File
# 3 - Information Disclosure
# 4 - Injection (XSS/Script/HTML)
# 5 - Remote File Retrieval - Inside Web Root
# 6 - Denial of Service
# 7 - Remote File Retrieval - Server Wide
# 8 - Command Execution / Remote Shell
# 9 - SQL Injection
# a - Authentication Bypass
# b - Software Identification
# c - Remote Source Inclusion
# x - Reverse Tuning Options (exclude this test)
```

### SSL certificate check
```bash
nikto -h https://TARGET_IP -ssl
```

### Scan with authentication
```bash
nikto -h http://TARGET_IP -id admin:password
```

### Update plugin database
```bash
nikto -update
```

---

## What Nikto Checks

| Category | Examples |
|---|---|
| **Server software** | Apache/Nginx version disclosure, outdated versions |
| **Security headers** | Missing X-Frame-Options, CSP, X-XSS-Protection, HSTS |
| **Default files** | /test.php, /phpinfo.php, /admin/, /backup/ |
| **Dangerous files** | Known exploitable scripts, CGI scripts |
| **Default credentials** | Common admin:admin, tomcat:tomcat on management interfaces |
| **Directory listing** | Enabled directory browsing |
| **SSL/TLS** | Weak ciphers, SSL v2/v3, certificate issues |
| **HTTP methods** | PUT, DELETE, TRACE enabled |
| **Application info** | Server software, framework disclosure |
| **Cookies** | Missing Secure/HttpOnly flags |

---

## Reading Nikto Output

```
+ Server: Apache/2.4.41 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present.
+ /: The X-Content-Type-Options header is not set.
+ /phpinfo.php: Output from the phpinfo() function was found.
+ OSVDB-3268: /backup/: Directory indexing found.
+ /admin/: Admin login page/area found.
+ /README.txt: README File found, may contain application version info.

Legend:
+ = Informational finding
OSVDB-XXXX = OSVDB ID (legacy vulnerability DB reference)
```

---

## Nikto vs Other Scanners

| Tool | Type | Best For |
|---|---|---|
| Nikto | Generic web server scanner | Quick surface scan, misconfigs, headers |
| Burp Suite Scanner | Deep web app scanner | SQLi, XSS, authentication flaws |
| OWASP ZAP | Deep web app scanner | OWASP issues, automation |
| Nessus/OpenVAS | Network + web vulnerability scanner | Authenticated scanning |
| Gobuster/ffuf | Directory/file discovery | Finding hidden content |

**Use Nikto for:** Fast initial assessment, header checks, default content discovery
**Use Burp for:** Deep application-layer vulnerability testing

---

## Opsec Notes
> ⚠️ Nikto is very noisy. It makes thousands of requests and will appear in web server access logs. Do not use without authorization.
- Not designed for stealth — use Burp/manual testing for low-noise assessments
- May trigger IDS/WAF blocks mid-scan

---

## Related Notes
- Burp-Suite
- Gobuster
- ffuf
- Web-Application-Testing
- OWASP-Top-10
- Nmap

## References
- Nikto GitHub: https://github.com/sullo/nikto
- Nikto documentation: https://cirt.net/Nikto2

---
<- [[Tools-MOC]]