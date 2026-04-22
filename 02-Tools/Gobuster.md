---
title: Gobuster
tags:
  - tool
  - recon
  - web
  - directory-busting
  - dns-enumeration
  - foundational
category: recon
platform: cross-platform
install: apt install gobuster / go install github.com/OJ/gobuster/v3@latest
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# 🛠️ Gobuster

## What It Is
Gobuster is a fast, open-source brute-force tool written in Go used to discover hidden directories and files on web servers (dir mode), enumerate DNS subdomains (dns mode), and brute-force virtual hostnames (vhost mode). It is a standard part of web application reconnaissance.

## Installation
```bash
# Debian/Ubuntu (Kali includes it by default)
sudo apt install gobuster

# From source (requires Go)
go install github.com/OJ/gobuster/v3@latest

# Verify
gobuster version
```

## Basic Syntax
```bash
gobuster [mode] -u [URL] -w [wordlist] [options]
```

---

## Common Commands / Use Cases

### Directory/File Brute-Force (dir mode)
```bash
# Basic directory scan
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt

# With file extensions
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak

# Follow redirects, add threads
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,txt -t 50 -r

# With authentication (Basic Auth)
gobuster dir -u http://10.10.10.10 -w /path/to/wordlist -U admin -P password

# With a custom cookie (authenticated session)
gobuster dir -u http://10.10.10.10 -w /path/to/wordlist \
  -c "PHPSESSID=abc123; security=low"

# Output to file
gobuster dir -u http://10.10.10.10 -w /path/to/wordlist -o results.txt
```

### DNS Subdomain Enumeration (dns mode)
```bash
gobuster dns -d example.com -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Show IP addresses of discovered subdomains
gobuster dns -d example.com -w /path/to/wordlist -i
```

### Virtual Host Enumeration (vhost mode)
```bash
# Useful when multiple sites hosted on same IP
gobuster vhost -u http://10.10.10.10 -w /path/to/wordlist --append-domain
```

---

## Important Flags / Options
| Flag | Description |
|---|---|
| `-u` | Target URL |
| `-w` | Path to wordlist |
| `-x` | File extensions to append (e.g., `php,txt,html`) |
| `-t` | Number of concurrent threads (default: 10) |
| `-r` | Follow HTTP redirects |
| `-k` | Skip TLS/SSL certificate verification |
| `-o` | Output results to a file |
| `-c` | Add a cookie header |
| `-H` | Add a custom HTTP header |
| `-U` / `-P` | Username / password for Basic Auth |
| `-b` | Blacklist status codes (e.g., `-b 404,403`) |
| `-s` | Whitelist status codes to show (e.g., `-s 200,204,301`) |
| `--timeout` | HTTP timeout per request |
| `-v` | Verbose output (show all results including 404s) |
| `-q` | Quiet mode (suppress banner) |

---

## Recommended Wordlists

| Wordlist | Location (Kali) | Use Case |
|---|---|---|
| `common.txt` | `/usr/share/wordlists/dirb/` | Quick dir scan |
| `directory-list-2.3-medium.txt` | `/usr/share/wordlists/dirbuster/` | Thorough dir scan |
| `big.txt` | `/usr/share/wordlists/dirb/` | Larger coverage |
| SecLists DNS subdomains | `/usr/share/seclists/Discovery/DNS/` | Subdomain enum |
| SecLists Web Content | `/usr/share/seclists/Discovery/Web-Content/` | API paths, common files |

> Install SecLists: `sudo apt install seclists` or `git clone https://github.com/danielmiessler/SecLists`

---

## Output Interpretation
- **Status 200** — File/directory exists and is accessible
- **Status 301/302** — Redirect (may reveal real location)
- **Status 403** — Forbidden — resource exists but access is denied (still worth noting)
- **Status 401** — Unauthorized — requires authentication
- **Status 500** — Server error — may indicate vulnerable endpoint worth investigating

---

## Opsec / Legal Notes
> ⚠️ Only run against systems you own or have explicit written authorization to test. Directory brute-forcing generates significant HTTP traffic and will appear in web server logs.
- High thread counts (`-t 100+`) can cause service disruption on fragile targets.
- Some WAFs and rate limiters will block or ban your IP after detecting scanning patterns.
- For stealth, reduce threads (`-t 5`) and add delays if supported.

## Related Tools
- [[Burp-Suite]] — intercept and replay discovered endpoints
- [[ffuf]] — alternative fuzzer with more flexible syntax
- [[Nmap]] — discover open HTTP/HTTPS ports before running Gobuster
- [[Nikto]] — web server vulnerability scanner (run after dir discovery)

## Related Notes
- Networking-Basics
- OSI-Model

## References
- GitHub: https://github.com/OJ/gobuster
- SecLists: https://github.com/danielmiessler/SecLists


## See Also

- Web-Application-Testing
- Penetration-Testing-Methodology
- ffuf
- Burp-Suite
- Common-Ports

---
<- [[Tools-MOC]]