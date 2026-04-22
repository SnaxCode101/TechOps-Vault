---
title: OWASP Top 10
tags:
  - fundamentals
  - web-security
  - OWASP
  - vulnerabilities
  - foundational
topic: Web Application Security
difficulty: intermediate
created: 2026-03-23
updated: 2026-04-04
source: OWASP Top 10 (2021), OWASP Testing Guide, PortSwigger Web Security Academy
---

# OWASP Top 10

## Overview
The OWASP Top 10 is a standard awareness document representing the most critical web application security risks, maintained by the Open Web Application Security Project (OWASP). It is updated periodically based on real-world data from organizations globally. The current edition is the **2021** version.

## Why It Matters in Security
The OWASP Top 10 is the universal language for web application vulnerabilities. It appears in penetration testing reports, bug bounty programs, compliance frameworks (PCI-DSS, ISO 27001), and certification exams (CEH, OSCP). Understanding each category at a technical level is mandatory for any security practitioner.

---

## A01 — Broken Access Control
**What it is:** Users can act outside their intended permissions — accessing other users' data, admin functions, or performing unauthorized actions.

**Examples:**
- Accessing `https://example.com/admin/users` as a non-admin by guessing the URL
- Changing `user_id=123` to `user_id=124` in a request to view another user's records (IDOR — Insecure Direct Object Reference)
- Privilege escalation from regular user to admin by modifying a role parameter
- CORS misconfiguration allowing unauthorized cross-origin API access

**How to test:**
- Intercept requests in Burp Suite; modify user IDs, role parameters, object references
- Log in as a low-privilege user; attempt to access admin URLs directly
- Try to access other users' resources by incrementing IDs

**Prevention:** Server-side access control enforcement; deny by default; log and alert on violations.

---

## A02 — Cryptographic Failures
**What it is:** Sensitive data (passwords, PII, payment data) is exposed due to weak or missing cryptography.

**Examples:**
- Passwords stored in plaintext or hashed with MD5/SHA-1 without salt
- HTTP (not HTTPS) used for transmitting sensitive data
- Weak cipher suites in TLS (e.g., DES, RC4, export-grade ciphers)
- Hard-coded cryptographic keys in source code
- Predictable IVs or nonce reuse in encryption

**How to test:**
- Check for HTTP on login/payment pages
- Inspect SSL/TLS config with `testssl.sh` or `sslscan`
- Check password hashes in leaked databases or exported data
- Search source code for hard-coded secrets

**Prevention:** TLS everywhere; use bcrypt/Argon2 for passwords; never roll your own crypto; rotate and protect keys.

---

## A03 — Injection
**What it is:** Untrusted data is sent to an interpreter as part of a command or query. The interpreter executes the attacker-controlled data.

**Sub-types:**
| Type | Description | Example |
|---|---|---|
| SQL Injection | Malicious SQL sent to database | `' OR '1'='1` in login field |
| Command Injection | OS commands injected | `; cat /etc/passwd` in a ping field |
| LDAP Injection | Malicious LDAP queries | Bypass auth in directory services |
| XPath Injection | Manipulate XML queries | Similar to SQLi but for XML databases |
| SSTI | Server-Side Template Injection | `{{7*7}}` renders as 49 — code execution |

**SQLi quick detection:**
```
' → error or behavior change
'' → normalized (no error)
' OR '1'='1 → always true
' AND '1'='2 → always false
' UNION SELECT null,null,null-- → column count probe
```

**How to test:** Manual payloads in Burp Suite Repeater; automate with SQLmap.

**Prevention:** Parameterized queries / prepared statements; input validation; ORMs; least-privilege DB accounts.

---

## A04 — Insecure Design
**What it is:** Security flaws baked into design and architecture — not just implementation bugs. Missing threat modeling, insecure design patterns.

**Examples:**
- Password reset via secret questions (guessable)
- No rate limiting on authentication endpoints (enables brute force)
- Business logic flaws: applying a coupon code unlimited times
- Trusting client-supplied data for pricing in e-commerce

**Prevention:** Threat modeling during design; secure design patterns; defense-in-depth from the architecture stage.

---

## A05 — Security Misconfiguration
**What it is:** Insecure default configurations, incomplete configuration, open cloud storage, unnecessary features enabled, verbose error messages.

**Examples:**
- Default credentials (admin/admin) not changed
- Directory listing enabled on web server
- Verbose stack traces exposed in production error pages
- Cloud storage bucket publicly readable (S3 misconfiguration)
- Unnecessary services/ports left open
- Missing security headers (CSP, X-Frame-Options, HSTS)

**How to test:**
- Check HTTP response headers with `curl -I` or Burp Suite
- Look for `/robots.txt`, `/.git/`, `/.env`, `/backup/`, `/admin/`
- Test for default credentials on login panels
- Check cloud storage with tools like `aws s3 ls s3://bucket-name`

**Prevention:** Harden all configurations; disable defaults; implement security headers; automate config audits.

---

## A06 — Vulnerable and Outdated Components
**What it is:** Using components (libraries, frameworks, OS, servers) with known vulnerabilities.

**Examples:**
- Running WordPress with outdated plugins
- Using Log4j 2.x before 2.17.1 (Log4Shell — CVE-2021-44228)
- Apache Struts with known RCE vulnerabilities
- Outdated jQuery with known XSS vulnerabilities

**How to test:**
- Check versions of all dependencies
- Search CVE databases (NVD, Exploit-DB) for known issues
- Use tools like `npm audit`, `pip-audit`, `snyk`, `retire.js`

**Prevention:** Software composition analysis (SCA) tools; patch management process; monitor CVE feeds.

---

## A07 — Identification and Authentication Failures
**What it is:** Weaknesses in authentication and session management that allow account compromise.

**Examples:**
- No account lockout after failed login attempts (brute force possible)
- Weak or default passwords permitted
- Insecure "forgot password" flows
- Session tokens exposed in URLs
- Session not invalidated after logout
- Missing MFA on sensitive accounts

**How to test:**
- Test login with common credentials (`admin/admin`, `admin/password`)
- Test session token entropy; try reusing tokens after logout
- Check if password reset tokens expire and are single-use

**Prevention:** MFA; strong password policy; secure session management; account lockout; bcrypt passwords.

---

## A08 — Software and Data Integrity Failures
**What it is:** Code and infrastructure that does not protect against integrity violations — insecure CI/CD pipelines, insecure deserialization, unsigned updates.

**Examples:**
- Insecure deserialization: passing malicious serialized objects that execute code when deserialized (Java, PHP, Python pickle)
- Fetching plugins/updates over HTTP without signature verification
- Compromised CI/CD pipeline pushing malicious code (supply chain attack)

**Deserialization attack indicator:** Application deserializes user-controlled data (cookies, POST body, API parameters) in Java, PHP, Python, Ruby, .NET.

**Prevention:** Verify digital signatures of updates; use integrity checks; secure and monitor CI/CD pipelines; avoid deserializing untrusted data.

---

## A09 — Security Logging and Monitoring Failures
**What it is:** Insufficient logging, monitoring, and alerting — attackers can operate undetected.

**Examples:**
- Failed logins not logged
- No alerts for suspicious activity (mass scanning, repeated 403s)
- Log files accessible or unprotected
- No centralized SIEM or log aggregation
- Incident response plan does not exist

**What should be logged:** Authentication events, access control failures, input validation failures, admin actions, high-value transactions.

**Prevention:** Centralized logging (SIEM); alerting on anomalous patterns; protect log integrity; incident response plan.

---

## A10 — Server-Side Request Forgery (SSRF)
**What it is:** The server can be tricked into making requests to unintended locations — internal services, cloud metadata endpoints, or external systems.

**Examples:**
- A URL fetch feature that accepts user input: `fetch?url=http://169.254.169.254/latest/meta-data/` (AWS metadata endpoint)
- Bypassing firewalls by using the server as a proxy to internal services
- Accessing internal APIs not exposed externally: `http://localhost:8080/admin`

**SSRF test payloads:**
```
http://127.0.0.1/
http://localhost/
http://169.254.169.254/latest/meta-data/    # AWS
http://metadata.google.internal/             # GCP
http://169.254.169.254/metadata/v1/         # DigitalOcean
http://192.168.1.1/                          # Internal network probe
```

**How to test:** Any feature that fetches URLs or makes server-side HTTP requests; test with Burp Collaborator or interactsh for out-of-band detection.

**Prevention:** Allowlist permitted destinations; disable unnecessary URL-fetch features; block requests to internal ranges at firewall level.

---

## Quick Reference — Attack → OWASP Category

| Attack | Category |
|---|---|
| SQL Injection | A03 Injection |
| IDOR | A01 Broken Access Control |
| XSS | A03 Injection |
| CSRF | A01 Broken Access Control |
| SSRF | A10 SSRF |
| Default credentials | A05 Misconfiguration |
| Log4Shell | A06 Outdated Components |
| Session fixation | A07 Auth Failures |
| Insecure deserialization | A08 Integrity Failures |
| Exposed S3 bucket | A05 Misconfiguration |

## Related Notes
- CIA-Triad
- Burp-Suite
- Gobuster
- Networking-Basics

## Sources

OWASP Foundation. (2021). *OWASP top 10: The ten most critical web application security risks*. OWASP. https://owasp.org/Top10/

OWASP Foundation. (2023). *OWASP testing guide v4.2*. OWASP. https://owasp.org/www-project-web-security-testing-guide/

PortSwigger. (2024). *Web security academy*. PortSwigger. https://portswigger.net/web-security

## See Also

- Web-Application-Testing
- Burp-Suite
- SQLmap
- ffuf
- Gobuster
- Penetration-Testing-Methodology

---
<- [[Fundamentals-MOC]]