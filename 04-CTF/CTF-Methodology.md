---
title: CTF Methodology
tags:
  - foundational
  - CTF
  - methodology
  - web
  - crypto
  - forensics
  - pwn
  - reverse
  - steganography
topic: CTF Approach & Methodology
difficulty: foundational
created: 2026-03-26
updated: 2026-03-26
---

# 🚩 CTF Methodology

## Overview
Capture The Flag (CTF) competitions present security challenges where participants find hidden flags (strings like `flag{...}` or `CTF{...}`). This note covers the systematic approach to each CTF category so you never start a challenge blind.

---

## General CTF Approach

```
1. READ the challenge description carefully — it usually contains hints
2. Download/examine any provided files (file type, strings, metadata)
3. Identify the category (web, crypto, forensics, pwn, reverse, misc)
4. Apply category-specific methodology (sections below)
5. Google known techniques — CTF problems often reuse patterns
6. Search writeups for similar challenges (after trying first!)
7. If stuck: take a break, try a different approach, or ask team members
```

### Universal First Steps
```bash
# Identify file type
file challenge_file

# Extract printable strings
strings challenge_file
strings -n 8 challenge_file   # Min 8 chars
strings -e l challenge_file   # Unicode (Windows)

# Check for embedded files
binwalk challenge_file
binwalk -e challenge_file     # Extract

# Check hex for magic bytes / structure
xxd challenge_file | head -40

# Check EXIF metadata (images/docs)
exiftool challenge_file
```

---

## Category: Web

### Tools
```
Burp Suite — proxy, intercept, repeater, intruder
gobuster / ffuf — directory/vhost fuzzing
SQLmap — automated SQLi
Nikto — quick scan
Browser Dev Tools — inspect source, cookies, storage, requests
```

### Checklist
```
□ View page source (Ctrl+U) — comments, hidden fields, JS variables
□ Check robots.txt and sitemap.xml
□ Directory fuzzing: gobuster dir -u URL -w wordlist.txt
□ Virtual host fuzzing: ffuf -w wordlist.txt -u http://IP -H "Host: FUZZ.domain.com"
□ Check cookies — base64? JWT? Editable?
□ Check request headers — X-Forwarded-For, debug headers, unusual values
□ Test for SQL injection: ' " OR 1=1 -- -
□ Test for XSS: <script>alert(1)</script>
□ Test for SSRF: http://127.0.0.1, http://169.254.169.254 (AWS metadata)
□ Test for LFI: ../../etc/passwd, php://filter/convert.base64-encode/resource=
□ Test for command injection: ; id, | id, `id`, $(id)
□ Check JavaScript source files in Network tab
□ Check for GraphQL endpoint: /graphql, /api
□ Try admin/admin, admin/password, default creds
```

---

## Category: Cryptography

### Common Cipher Identification
| Pattern | Cipher |
|---|---|
| Only letters, all same case, no spaces | Classic substitution (Caesar, Vigenere, Atbash) |
| `gur synt vf...` | ROT13 |
| `Gur synt vf...` | ROT13 |
| Only A-Z, key word given | Vigenere |
| Lots of `=` at end | Base64 |
| Only 0-9 A-F | Hex |
| Only 0 and 1 | Binary |
| Morse code | `.-  -...  -.-. -.` |
| Numbers with spaces | ASCII decimal |
| Letters in grid | Polybius square |
| `BEGIN RSA PRIVATE KEY` | RSA |
| JWT (3 parts, dots) | Base64-encoded JSON |

### Decoding Quick Reference
```bash
# Base64
echo "SGVsbG8=" | base64 -d
python3 -c "import base64; print(base64.b64decode('SGVsbG8=').decode())"

# Hex
echo "48656c6c6f" | xxd -r -p
python3 -c "print(bytes.fromhex('48656c6c6f').decode())"

# ROT13
echo "Uryyb" | tr 'A-Za-z' 'N-ZA-Mn-za-m'

# CyberChef (browser tool — does everything)
# https://gchq.github.io/CyberChef/
```

### RSA CTF Attacks
```python
# Small exponent (e=3) → cube root attack
# Same n, different e → common modulus attack
# Small private key → Wiener's attack
# n = p*q where p≈q → Fermat factorization
# Tools: RsaCtfTool, factor.db

# RsaCtfTool (automated RSA attacks)
python3 RsaCtfTool.py --publickey key.pub --uncipher ciphertext
```

---

## Category: Forensics

### Checklist
```
□ file — identify format
□ strings — look for flags, URLs, credentials
□ exiftool — metadata (GPS, timestamps, author, comments)
□ binwalk — check for embedded files (PNG inside JPEG, ZIP in image)
□ steghide — extract hidden data from images/audio
□ stegsolve — visualize LSB steganography
□ foremost / scalpel — file carving from disk image
□ Wireshark / pcap analysis — look for HTTP, DNS, FTP, cleartext
□ volatility — memory forensics (if .dmp/.raw file)
□ Autopsy — disk image analysis
□ strings on pcap — grep for flag{
□ log files — grep for flag, unusual entries, base64
```

### Steganography
```bash
# steghide (images/audio)
steghide info image.jpg
steghide extract -sf image.jpg          # No password
steghide extract -sf image.jpg -p pass  # With password

# stegsolve (Java GUI) — LSB visualization
java -jar stegsolve.jar

# zsteg (PNG/BMP LSB)
zsteg image.png

# Check audio spectrograms (Audacity, Sonic Visualizer)
# Open audio → View → Spectrogram → look for visible text/image in frequency domain

# Outguess
outguess -r image.jpg output.txt
```

### PCAP Analysis
```bash
# Open in Wireshark
wireshark capture.pcap

# Filter HTTP traffic
http
http.request.method == "GET"

# Extract files from HTTP streams
File → Export Objects → HTTP

# Find credentials
Edit → Find Packet → String → "Authorization" or "password"

# Follow TCP stream (right-click packet → Follow → TCP Stream)

# DNS analysis
dns
# Look for long DNS queries (potential tunneling)
dns.qry.name.len > 50
```

---

## Category: Reverse Engineering

```
□ file binary           → architecture, stripped?
□ strings binary        → flag-like strings?
□ ltrace / strace       → library calls, syscalls
□ run it                → what does it do? What input format?
□ Ghidra / IDA          → disassemble + decompile main()
□ Find input comparison → strcmp, memcmp, custom check
□ Patch or keygen       → bypass check or reproduce key
□ Check for anti-debug  → IsDebuggerPresent, timing checks
□ Check for packing     → high entropy sections, DIE tool
```

See also: Reverse-Engineering-Basics

---

## Category: Binary Exploitation (Pwn)

### Prerequisites
- Understand x86/x64 assembly
- Understand stack layout, calling conventions
- Tools: pwntools (Python), GDB with pwndbg/peda, checksec

```bash
# Check binary protections
checksec binary
# RELRO, Stack Canary, NX, PIE, ASLR

# Run with pwntools template
from pwn import *
p = process('./binary')        # local
p = remote('host', port)       # remote

# Buffer overflow pattern
python3 -c "from pwn import *; print(cyclic(200))"  # Find offset
```

### Exploit Types
| Type | Description |
|---|---|
| Buffer Overflow (ret2win) | Overwrite return address to call win function |
| ROP Chain | Chain gadgets to bypass NX (no executable stack) |
| Format String | `printf(user_input)` → read/write arbitrary memory |
| Heap exploitation | UAF, double-free, heap overflow |
| ret2libc | Return to system("/bin/sh") in libc |

---

## Category: OSINT

```bash
# Username search
sherlock username

# Email/phone search
theHarvester -d domain.com -b all

# Reverse image search: TinEye, Google Images, Yandex Images

# Metadata on images
exiftool photo.jpg    # GPS coordinates → Google Maps

# Find person: LinkedIn, Facebook, Instagram, GitHub, Twitter
# Company: WHOIS, Shodan, LinkedIn, website

# Google dorks
site:target.com filetype:pdf
"target person" "email"
intext:"confidential" site:target.com
```

---

## Category: Misc / Steganography

```bash
# Try everything:
# - strings, hexdump, file, exiftool, binwalk
# - Base64, hex, ROT13 decode
# - QR code reader (zbar, online)
# - Morse code decoder
# - Braille decoder
# - Whitespace steganography (Whitespace language)
# - Unicode steganography (zero-width characters)
```

---

## Common Flag Formats

| Platform | Format |
|---|---|
| Generic | `flag{...}`, `CTF{...}`, `FLAG{...}` |
| HackTheBox | `HTB{...}` |
| TryHackMe | `THM{...}` |
| PicoCTF | `picoCTF{...}` |
| Google CTF | `CTF{...}` |

When stuck: search for uppercase strings with `{}`:
```bash
strings file | grep -E "[A-Z]{2,}\{[^}]+\}"
```

---

## CTF Tools List

| Tool | Category | Use |
|---|---|---|
| CyberChef | All | Encoding/decoding/transformation |
| Burp Suite | Web | HTTP proxy and testing |
| Ghidra / IDA | Reverse | Disassembly/decompilation |
| pwntools | Pwn | Python exploit framework |
| Volatility | Forensics | Memory analysis |
| Wireshark | Forensics/Net | PCAP analysis |
| steghide / stegsolve | Stego | Image steganography |
| hashcat / john | Crypto | Hash cracking |
| RsaCtfTool | Crypto | RSA attacks |
| binwalk | Forensics | Embedded file detection |
| exiftool | Forensics | Metadata analysis |
| GDB + pwndbg | Pwn | Dynamic binary analysis |

---

## Related Notes
- CTF-Quick-Reference
- Reverse-Engineering-Basics
- Web-Application-Testing
- Cryptography-Basics
- Encoding-Decoding-Reference
- Python-for-Security

---
<- [[CTF-MOC]]