---
title: Burp Suite
tags:
  - tool
  - web
  - proxy
  - webapp-testing
  - exploitation
  - intermediate
category: web
platform: cross-platform
install: https://portswigger.net/burp/releases
created: 2026-03-23
updated: 2026-04-04
difficulty: intermediate
---

# 🛠️ Burp Suite

## What It Is
Burp Suite is an integrated platform for web application security testing developed by PortSwigger. It operates as an intercepting proxy between a browser and a web server, allowing a tester to inspect, modify, and replay HTTP/HTTPS requests and responses. The Community Edition is free; the Pro edition adds the active scanner, Burp Intruder without throttling, and additional tooling.

## Installation
```
Download from: https://portswigger.net/burp/releases
Available for Linux, macOS, Windows
Requires Java (bundled in installer)
```
> Kali Linux includes Burp Suite Community pre-installed.

## Core Components

### Proxy
The central feature. Intercepts HTTP/HTTPS traffic between your browser and the target. You must configure your browser to route traffic through Burp's proxy listener (default: `127.0.0.1:8080`).

**Browser setup:**
- Use FoxyProxy (browser extension) to toggle proxy on/off
- Or configure manual proxy in browser network settings: Host `127.0.0.1`, Port `8080`

**HTTPS setup:**
- Navigate to `http://burpsuite` in the proxied browser → download PortSwigger CA certificate → install in browser's trusted certificate store

### Repeater
Manually resend and modify individual HTTP requests. Essential for testing payloads one at a time. Send a request from Proxy intercept or HTTP history → right-click → **Send to Repeater**.

### Intruder
Automated payload delivery against a target request. Mark insertion points → choose attack type → load a payload list → run.

**Attack types:**
| Type | Description |
|---|---|
| Sniper | One payload list, one insertion point at a time |
| Battering Ram | Same payload in all insertion points simultaneously |
| Pitchfork | Multiple payload lists, one per insertion point (paired) |
| Cluster Bomb | Multiple payload lists, all combinations (cartesian product) |

> ⚠️ Intruder is rate-throttled in Community Edition. Use Turbo Intruder (extension) for speed.

### Decoder
Encode/decode data in common formats: URL encoding, Base64, HTML entities, hex, gzip. Useful for transforming and understanding obfuscated payloads.

### Comparer
Diff two requests or responses to identify subtle differences (useful for blind injection or enumeration).

### Logger (formerly HTTP History in Proxy)
Logs all requests/responses passing through the proxy. Filter by host, method, status code, MIME type, etc.

### Target → Site Map
Builds a map of all discovered content for the target application.

### Scanner (Pro only)
Active and passive scanning for common web vulnerabilities (SQLi, XSS, SSRF, XXE, etc.).

## Common Workflows

### Intercept and modify a request
1. Enable Intercept in Proxy tab
2. Trigger the action in your browser
3. Modify the request in Burp
4. Click Forward

### Test for SQL injection manually
1. Capture a request with a parameter → Send to Repeater
2. Append `'` to the parameter value → observe error
3. Try `' OR '1'='1` → observe behavior change

### Brute-force a login form
1. Capture login POST request → Send to Intruder
2. Clear auto-marked positions → mark password field
3. Set payload type: Simple List → load wordlist (e.g., `rockyou.txt`)
4. Run attack → sort by response length or status code to find hits

### Discover hidden directories
Use Burp's built-in content discovery (Pro) or pair with [[Gobuster]] externally.

## Important Settings
| Setting | Location | Notes |
|---|---|---|
| Proxy listener | Proxy → Options | Default 127.0.0.1:8080 |
| CA certificate | `http://burpsuite` | Must install for HTTPS interception |
| Scope | Target → Scope | Set to avoid proxying unrelated traffic |
| Intercept rules | Proxy → Options → Intercept | Filter what gets intercepted |

## Opsec / Legal Notes
> ⚠️ Only test web applications you own or have explicit written authorization to test. Unauthorized testing is illegal and can cause unintended harm to production systems.
- Always define a scope in the Target tab to avoid accidentally testing out-of-scope systems.
- Use a dedicated browser profile for Burp to avoid leaking personal browsing data.

## Related Tools
- OWASP ZAP — free open-source alternative
- [[Gobuster]] / [[ffuf]] — directory and parameter fuzzing
- [[Nmap]] — pre-engagement port and service discovery

## Related Notes
- OSI-Model
- Networking-Basics
- OWASP-Top-10

## References
- Official docs: https://portswigger.net/burp/documentation
- Web Security Academy (free labs): https://portswigger.net/web-security


## See Also

- Web-Application-Testing
- OWASP-Top-10
- Penetration-Testing-Methodology
- ffuf
- Gobuster
- SQLmap

---
<- [[Tools-MOC]]