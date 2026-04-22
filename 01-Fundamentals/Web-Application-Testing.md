---
title: Web Application Testing
tags: '["secops", "fundamentals", "web", "appsec", "owasp", "burpsuite",
  "intermediate"]'
created: 2026-03-23
folder: 01-Fundamentals
status: complete
difficulty: intermediate
source: OWASP Testing Guide v4.2, OWASP Top 10 (2021), PortSwigger Web Security Academy
---

# Web Application Testing

> Web application testing is the systematic process of probing a web app for vulnerabilities in its logic, configuration, authentication, and data handling — before an attacker does it for you.

---

## Core Concepts

### The Attack Surface
A web application's attack surface includes every point where user input enters the system:
- URL parameters and query strings
- HTTP headers (User-Agent, Referer, X-Forwarded-For)
- Cookies and session tokens
- Form fields and file upload endpoints
- API endpoints (REST, GraphQL, SOAP)
- WebSockets

### HTTP Fundamentals for Testers
Understanding HTTP is non-negotiable. Key items:

| Component | Relevance |
|---|---|
| Methods (GET, POST, PUT, DELETE, PATCH) | Verb-based access control flaws |
| Status codes (200, 301, 403, 500) | Enumerate behaviour and errors |
| Headers | Auth tokens, CORS policy, CSP |
| Cookies (Secure, HttpOnly, SameSite flags) | Session hijacking vectors |
| Request body (JSON, XML, form data) | Injection entry points |

---

## Testing Methodology

### Phase 1 — Reconnaissance
- Identify technologies: server, framework, CMS, WAF
- Spider/crawl the application to map all endpoints
- Enumerate subdomains and virtual hosts
- Review JS files for hidden endpoints and API keys
- Check `robots.txt`, `.git/`, backup files (`index.php.bak`)

**Tools:** Burp Suite Spider, `gobuster`, `ffuf`, `whatweb`, `wappalyzer`

### Phase 2 — Authentication Testing
- Default credentials
- Username enumeration (different error messages)
- Brute force / rate limiting bypass
- Password reset flaws (token predictability, host header injection)
- Multi-factor authentication bypass
- JWT weaknesses (alg:none, weak secret, header injection)

### Phase 3 — Session Management
- Session token entropy and predictability
- Session fixation
- Insecure transmission (HTTP vs HTTPS)
- Cookie flags: missing `Secure`, `HttpOnly`, `SameSite`
- Session expiry and logout behaviour

### Phase 4 — Input Validation & Injection
See [[OWASP-Top-10]] for full coverage. Key tests:
- **SQLi** — single quote `'`, boolean-based, time-based blind
- **XSS** — reflected, stored, DOM-based: `<script>alert(1)</script>`
- **Command injection** — `; whoami`, `| id`, backticks
- **SSTI** — `{{7*7}}`, `${7*7}`, `<%= 7*7 %>`
- **XXE** — inject external entity in XML input
- **Path traversal** — `../../../etc/passwd`
- **Open redirect** — `?redirect=https://evil.com`

### Phase 5 — Access Control
- Horizontal privilege escalation: access another user's resources by changing an ID
- Vertical privilege escalation: access admin functions as a normal user
- IDOR (Insecure Direct Object References): `GET /invoice?id=1002` — change to `1001`
- Forced browsing: access `/admin`, `/backup`, `/config` directly
- HTTP method tampering: PUT/DELETE where only GET/POST expected

### Phase 6 — Business Logic
- Price manipulation in e-commerce flows
- Workflow bypass (skip payment step)
- Race conditions (double-spend, coupon reuse)
- Mass assignment: send extra fields in JSON body (`"isAdmin": true`)

### Phase 7 — Reporting
- CVSS scoring for each finding
- Proof of concept (PoC) — screenshots and request/response pairs
- Remediation recommendations
- Risk-rated findings: Critical / High / Medium / Low / Informational

---

## Key Vulnerability Classes

### SQL Injection
```sql
' OR '1'='1
' UNION SELECT null, username, password FROM users--
'; DROP TABLE users;--
```
**Detection:** Error messages, boolean differences, time delays (`SLEEP(5)`)
**Tools:** SQLmap, manual via Burp-Suite

### Cross-Site Scripting (XSS)
```javascript
<script>alert(document.cookie)</script>
<img src=x onerror=alert(1)>
"><svg onload=alert(1)>
```
**Impact:** Cookie theft, keylogging, phishing, session hijacking

### Cross-Site Request Forgery (CSRF)
- Attacker tricks authenticated user into making an unintended request
- **Bypass:** Missing or predictable CSRF token, SameSite=None cookies
- **Test:** Replay a state-changing request from a different origin

### Insecure Direct Object Reference (IDOR)
```
GET /api/user/profile?id=1337
→ change to id=1338 — can you see another user's data?
```

### Server-Side Request Forgery (SSRF)
```
https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/
```
- Targets internal services, cloud metadata APIs, localhost
- Bypass filters: `http://[::1]/`, `http://127.0.0.1:22/`, DNS rebinding

---

## Tools Quick Reference

| Tool | Use |
|---|---|
| Burp-Suite | Intercept, repeat, fuzz, scan requests |
| ffuf | Directory/parameter/vhost fuzzing |
| Gobuster | Directory and DNS enumeration |
| SQLmap | Automated SQL injection |
| OWASP ZAP | Open-source web app scanner |
| Nikto | Web server misconfiguration scanner |
| `curl` / `httpie` | Manual request crafting |

---

## HTTP Status Codes Cheat Sheet

| Code | Meaning | Testing Relevance |
|---|---|---|
| 200 | OK | Endpoint exists and responds |
| 301/302 | Redirect | Open redirect potential |
| 400 | Bad Request | Input validation response |
| 401 | Unauthorised | Auth required |
| 403 | Forbidden | Auth bypass target |
| 404 | Not Found | Enumeration response |
| 405 | Method Not Allowed | Try other HTTP methods |
| 500 | Internal Server Error | Error message disclosure / injection |

---

## Testing Checklist

- [ ] Mapped all endpoints including hidden/API routes
- [ ] Tested all auth mechanisms
- [ ] Checked all cookie flags
- [ ] Tested all input fields for injection
- [ ] Checked horizontal and vertical access control
- [ ] Tested business logic flows
- [ ] Reviewed JS source for secrets/endpoints
- [ ] Checked HTTP security headers (CSP, HSTS, X-Frame-Options)
- [ ] Tested file upload functionality
- [ ] Checked for CORS misconfigurations

---

## Sources

OWASP Foundation. (2023). *OWASP testing guide v4.2*. OWASP. https://owasp.org/www-project-web-security-testing-guide/

OWASP Foundation. (2021). *OWASP top 10: The ten most critical web application security risks*. OWASP. https://owasp.org/Top10/

PortSwigger. (2024). *Web security academy*. PortSwigger. https://portswigger.net/web-security

## Related Notes
- OWASP-Top-10
- Burp-Suite
- SQLmap
- ffuf
- Gobuster
- Penetration-Testing-Methodology
---
title: Web Application Testing
tags: [secops, fundamentals, web, appsec, owasp, burpsuite]
created: 2026-03-23
folder: 01-Fundamentals
status: complete
---

# Web Application Testing

> Web application testing is the systematic process of probing a web app for vulnerabilities in its logic, configuration, authentication, and data handling — before an attacker does it for you.

---

## Core Concepts

### The Attack Surface
A web application's attack surface includes every point where user input enters the system:
- URL parameters and query strings
- HTTP headers (User-Agent, Referer, X-Forwarded-For)
- Cookies and session tokens
- Form fields and file upload endpoints
- API endpoints (REST, GraphQL, SOAP)
- WebSockets

### HTTP Fundamentals for Testers

| Component | Relevance |
|---|---|
| Methods (GET, POST, PUT, DELETE, PATCH) | Verb-based access control flaws |
| Status codes (200, 301, 403, 500) | Enumerate behaviour and errors |
| Headers | Auth tokens, CORS policy, CSP |
| Cookies (Secure, HttpOnly, SameSite flags) | Session hijacking vectors |
| Request body (JSON, XML, form data) | Injection entry points |

---

## Testing Methodology

### Phase 1 — Reconnaissance
- Identify technologies: server, framework, CMS, WAF
- Spider/crawl the application to map all endpoints
- Enumerate subdomains and virtual hosts
- Review JS files for hidden endpoints and API keys
- Check `robots.txt`, `.git/`, backup files (`index.php.bak`)

**Tools:** Burp Suite Spider, `gobuster`, `ffuf`, `whatweb`, `wappalyzer`

### Phase 2 — Authentication Testing
- Default credentials
- Username enumeration (different error messages)
- Brute force / rate limiting bypass
- Password reset flaws (token predictability, host header injection)
- MFA bypass techniques
- JWT weaknesses (alg:none, weak secret, header injection)

### Phase 3 — Session Management
- Session token entropy and predictability
- Session fixation
- Insecure transmission (HTTP vs HTTPS)
- Cookie flags: missing `Secure`, `HttpOnly`, `SameSite`
- Session expiry and logout behaviour

### Phase 4 — Input Validation and Injection
Key tests:
- **SQLi** — `'`, boolean-based, time-based blind
- **XSS** — reflected, stored, DOM-based
- **Command injection** — `; whoami`, `| id`
- **SSTI** — `{{7*7}}`, `${7*7}`
- **XXE** — external entity injection in XML
- **Path traversal** — `../../../etc/passwd`
- **Open redirect** — `?redirect=https://evil.com`

### Phase 5 — Access Control
- Horizontal privilege escalation: access another user's data by changing an ID
- Vertical privilege escalation: access admin functions as normal user
- IDOR: `GET /invoice?id=1002` → change to `1001`
- Forced browsing: `/admin`, `/backup`, `/config`
- HTTP method tampering

### Phase 6 — Business Logic
- Price manipulation in e-commerce flows
- Workflow bypass (skip payment step)
- Race conditions (double-spend, coupon reuse)
- Mass assignment: `"isAdmin": true` in JSON body

### Phase 7 — Reporting
- CVSS scoring per finding
- PoC — screenshots, request/response pairs
- Remediation recommendations
- Risk rating: Critical / High / Medium / Low / Informational

---

## Key Vulnerability Classes

### SQL Injection
```sql
' OR '1'='1
' UNION SELECT null, username, password FROM users--
'; DROP TABLE users;--
```
Detection: error messages, boolean differences, time delays (`SLEEP(5)`)
Tools: SQLmap, Burp-Suite

### Cross-Site Scripting (XSS)
```javascript
<script>alert(document.cookie)</script>
<img src=x onerror=alert(1)>
"><svg onload=alert(1)>
```
Impact: cookie theft, keylogging, phishing, session hijacking

### CSRF
- Attacker tricks authenticated user into unintended request
- Test: replay state-changing request from different origin
- Missing/predictable CSRF token, SameSite=None cookies

### IDOR
```
GET /api/user/profile?id=1337 → change to id=1338
```

### SSRF
```
https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/
```
Targets internal services, cloud metadata APIs, localhost

---

## Tools Quick Reference

| Tool | Use |
|---|---|
| Burp-Suite | Intercept, repeat, fuzz, scan |
| ffuf | Directory/parameter/vhost fuzzing |
| Gobuster | Directory and DNS enumeration |
| SQLmap | Automated SQLi |
| Nikto | Web server misconfiguration scanner |

---

## HTTP Status Codes Cheat Sheet

| Code | Meaning | Testing Relevance |
|---|---|---|
| 200 | OK | Endpoint exists |
| 301/302 | Redirect | Open redirect potential |
| 401 | Unauthorised | Auth required |
| 403 | Forbidden | Auth bypass target |
| 404 | Not Found | Enumeration response |
| 405 | Method Not Allowed | Try other HTTP methods |
| 500 | Internal Server Error | Error disclosure / injection |

---

## Testing Checklist

- [ ] Mapped all endpoints including hidden/API routes
- [ ] Tested all auth mechanisms
- [ ] Checked all cookie flags
- [ ] Tested all input fields for injection
- [ ] Checked horizontal and vertical access control
- [ ] Tested business logic flows
- [ ] Reviewed JS source for secrets/endpoints
- [ ] Checked HTTP security headers (CSP, HSTS, X-Frame-Options)
- [ ] Tested file upload functionality
- [ ] Checked for CORS misconfigurations

---

## Related Notes
- OWASP-Top-10
- Burp-Suite
- SQLmap
- ffuf
- Gobuster
- Penetration-Testing-Methodology

## See Also

- OWASP-Top-10
- Burp-Suite
- SQLmap
- ffuf
- Gobuster
- Penetration-Testing-Methodology
- Common-Ports

---
<- [[Fundamentals-MOC]]