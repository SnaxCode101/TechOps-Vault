---
title: ffuf
tags: |-
  - tool
  - ffuf
  - fuzzing
  - web
  - recon
  - enumeration
  - foundational
created: 2026-03-23
folder: 02-Tools
status: complete
difficulty: foundational
---

# ffuf (Fuzz Faster U Fool)

> ffuf is a fast web fuzzer written in Go. It excels at directory discovery, parameter fuzzing, vhost enumeration, and any task where you need to iterate a wordlist against a URL pattern at high speed.

---

## Installation

```bash
# Kali / Parrot (usually pre-installed)
sudo apt install ffuf

# Go install
go install github.com/ffuf/ffuf/v2@latest

# Verify
ffuf -V
```

---

## Core Concept

ffuf uses the keyword `FUZZ` as a placeholder anywhere in a request — URL path, parameters, headers, POST body. It replaces `FUZZ` with each line from your wordlist and analyses the responses.

```
ffuf -u http://target.com/FUZZ -w wordlist.txt
```

---

## Common Use Cases

### Directory and File Brute-Force
```bash
# Basic directory discovery
ffuf -u http://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt

# Include file extensions
ffuf -u http://target.com/FUZZ -w wordlist.txt -e .php,.html,.txt,.bak

# Recursive mode
ffuf -u http://target.com/FUZZ -w wordlist.txt -recursion -recursion-depth 2
```

### GET Parameter Fuzzing
```bash
# Find hidden parameters
ffuf -u "http://target.com/page?FUZZ=value" -w wordlist.txt

# Fuzz parameter values
ffuf -u "http://target.com/page?id=FUZZ" -w /usr/share/seclists/Fuzzing/integers.txt
```

### POST Body Fuzzing
```bash
# Form field fuzzing
ffuf -u http://target.com/login -X POST \
  -d "username=admin&password=FUZZ" \
  -w /usr/share/wordlists/rockyou.txt \
  -H "Content-Type: application/x-www-form-urlencoded"

# JSON body fuzzing
ffuf -u http://target.com/api/login -X POST \
  -d '{"username":"admin","password":"FUZZ"}' \
  -w wordlist.txt \
  -H "Content-Type: application/json"
```

### Virtual Host (VHost) Enumeration
```bash
ffuf -u http://target.com -H "Host: FUZZ.target.com" \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -fs 4242
```
Note: `-fs` filters by response size to remove false positives.

### Subdomain Enumeration
```bash
ffuf -u http://FUZZ.target.com -w subdomains.txt
```

### Multiple Wordlists (Clusterbomb / Pitchfork)
```bash
# Two positions — FUZZ and FUZ2Z
ffuf -u http://target.com/FUZZ/FUZ2Z \
  -w wordlist1.txt:FUZZ \
  -w wordlist2.txt:FUZ2Z \
  -mode clusterbomb
```

---

## Key Flags Reference

| Flag | Description | Example |
|---|---|---|
| `-u` | Target URL | `-u http://target.com/FUZZ` |
| `-w` | Wordlist path | `-w /usr/share/seclists/...` |
| `-e` | File extensions | `-e .php,.txt,.bak` |
| `-X` | HTTP method | `-X POST` |
| `-d` | POST data | `-d "user=FUZZ"` |
| `-H` | Header | `-H "Cookie: session=abc"` |
| `-b` | Cookie | `-b "session=token"` |
| `-t` | Threads | `-t 50` |
| `-rate` | Rate limit (req/s) | `-rate 100` |
| `-timeout` | Request timeout | `-timeout 10` |
| `-mc` | Match HTTP codes | `-mc 200,301,302` |
| `-fc` | Filter HTTP codes | `-fc 404,403` |
| `-ms` | Match response size | `-ms 1234` |
| `-fs` | Filter response size | `-fs 4242` |
| `-mw` | Match word count | `-mw 20` |
| `-fw` | Filter word count | `-fw 10` |
| `-ml` | Match line count | `-ml 5` |
| `-fl` | Filter line count | `-fl 3` |
| `-mr` | Match regex | `-mr "Welcome"` |
| `-fr` | Filter regex | `-fr "Not Found"` |
| `-o` | Output file | `-o results.json` |
| `-of` | Output format | `-of json` |
| `-v` | Verbose output | `-v` |
| `-recursion` | Recursive fuzzing | `-recursion` |
| `-recursion-depth` | Recursion depth | `-recursion-depth 3` |
| `-ac` | Auto-calibrate filters | `-ac` |
| `-ic` | Ignore comments in wordlist | `-ic` |
| `-c` | Coloured output | `-c` |

---

## Filtering Responses

Filtering is critical — without it you'll be buried in false positives.

```bash
# Filter by status code (exclude 404s)
ffuf -u http://target.com/FUZZ -w wordlist.txt -fc 404

# Filter by response size (useful for vhost enumeration)
ffuf -u http://target.com/FUZZ -w wordlist.txt -fs 1234

# Auto-calibrate — sends fake requests to determine baseline response
ffuf -u http://target.com/FUZZ -w wordlist.txt -ac

# Match only successful hits
ffuf -u http://target.com/FUZZ -w wordlist.txt -mc 200,201,204,301,302,307
```

---

## Authenticated Scanning

```bash
# Session cookie
ffuf -u http://target.com/FUZZ -w wordlist.txt \
  -b "session=eyJhbGciOiJIUzI1NiJ9..."

# Bearer token
ffuf -u http://target.com/api/FUZZ -w wordlist.txt \
  -H "Authorization: Bearer <token>"
```

---

## Output and Reporting

```bash
# Save to JSON
ffuf -u http://target.com/FUZZ -w wordlist.txt -o results.json -of json

# Save to CSV
ffuf -u http://target.com/FUZZ -w wordlist.txt -o results.csv -of csv

# Save to Markdown
ffuf -u http://target.com/FUZZ -w wordlist.txt -o results.md -of md
```

---

## Recommended Wordlists (SecLists)

```bash
# Install SecLists
sudo apt install seclists
# Location: /usr/share/seclists/

# Key wordlists for ffuf
/usr/share/seclists/Discovery/Web-Content/common.txt          # General dirs/files
/usr/share/seclists/Discovery/Web-Content/big.txt             # Larger list
/usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
/usr/share/seclists/Fuzzing/                                  # Parameter fuzzing
/usr/share/wordlists/dirb/common.txt                          # Classic dirb list
/usr/share/wordlists/rockyou.txt                              # Password fuzzing
```

---

## Practical Examples

```bash
# Full web recon combo
ffuf -u http://target.com/FUZZ \
  -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt \
  -e .php,.html,.txt,.js,.json,.bak \
  -fc 404 -ac -c -v \
  -o web_recon.json -of json

# API endpoint discovery
ffuf -u http://api.target.com/v1/FUZZ \
  -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt \
  -H "Content-Type: application/json" \
  -mc 200,201,400,401,403

# Password spray (use responsibly and with authorisation)
ffuf -u http://target.com/login -X POST \
  -d "username=admin&password=FUZZ" \
  -w /usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -fc 200 -mc 302
```

---

## Tips and Gotchas

- Always use `-ac` (auto-calibrate) on sites with custom 404 pages
- Rate-limit with `-rate` when testing production systems to avoid detection/disruption
- Use `-t 50` for a good balance of speed and stability; higher threads can cause false negatives
- `FUZZ` is case-sensitive — must be uppercase
- Pipe output through `grep` or save to JSON for downstream processing
- Combine with [[Burp-Suite]] — send interesting ffuf hits to Burp for manual investigation

---

## Related Notes
- Gobuster
- Burp-Suite
- Web-Application-Testing
- Penetration-Testing-Methodology


## See Also

- Web-Application-Testing
- Penetration-Testing-Methodology
- Gobuster
- Burp-Suite
- Common-Ports

---
<- [[Tools-MOC]]