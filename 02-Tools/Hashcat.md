---
title: Hashcat
tags:
  - tool
  - password-cracking
  - hashing
  - offline-cracking
  - intermediate
category: password-cracking
platform: cross-platform
install: apt install hashcat / https://hashcat.net/hashcat/
created: 2026-03-23
updated: 2026-04-05
difficulty: intermediate
---

# 🛠️ Hashcat

## What It Is
Hashcat is the world's fastest password recovery tool, supporting GPU-accelerated cracking of hundreds of hash types including MD5, SHA-1, SHA-256, bcrypt, NTLM, WPA, Kerberos hashes, and many more. It supports multiple attack modes from dictionary attacks to brute-force and rule-based mangling.

## Installation
```bash
# Debian/Ubuntu / Kali (pre-installed on Kali)
sudo apt install hashcat

# Verify GPU support
hashcat -I

# Latest version / Windows / other
# https://hashcat.net/hashcat/
```

---

## Basic Syntax
```bash
hashcat -m [hash_mode] -a [attack_mode] [hashfile] [wordlist/mask]
```

---

## Hash Modes (-m) — Most Common

| Mode | Hash Type |
|---|---|
| 0 | MD5 |
| 100 | SHA-1 |
| 1400 | SHA-256 |
| 1800 | sha512crypt (Linux shadow $6$) |
| 500 | md5crypt (Linux shadow $1$) |
| 3200 | bcrypt $2*$ |
| 1000 | NTLM (Windows) |
| 5600 | NetNTLMv2 (Responder captures) |
| 5500 | NetNTLMv1 |
| 13100 | Kerberos 5 TGS-REP (Kerberoast) |
| 18200 | Kerberos 5 AS-REP (AS-REP Roast) |
| 22000 | WPA-PBKDF2-PMKID+EAPOL (WiFi — current format) |
| 2500 | WPA/WPA2 (legacy format — use 22000 for new captures) |
| 1500 | DES (descrypt) |
| 7400 | sha256crypt $5$ |
| 3000 | LM hash |
| 400 | phpass (WordPress, Joomla) |

> **Identify unknown hash:** `hashcat --example-hashes` or use `hash-identifier` / `haiti` tool

---

## Attack Modes (-a)

| Mode | Name | Description |
|---|---|---|
| 0 | Dictionary | Try each word in a wordlist |
| 1 | Combination | Combine words from two wordlists |
| 3 | Brute-Force / Mask | Try all combinations of a character set |
| 6 | Hybrid Wordlist+Mask | Wordlist word + mask suffix |
| 7 | Hybrid Mask+Wordlist | Mask prefix + wordlist word |

---

## Common Commands / Use Cases

### Dictionary attack (most common starting point)
```bash
hashcat -m 1000 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt
```

### Dictionary + rules (massively expands coverage)
```bash
hashcat -m 1000 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
hashcat -m 1000 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule
```

### Brute-force with mask
```bash
# All 4-digit PINs
hashcat -m 0 hash.txt -a 3 ?d?d?d?d

# 8-char: uppercase + lowercase + digit
hashcat -m 0 hash.txt -a 3 ?u?l?l?l?l?l?d?d

# Custom charset example (lower + digit)
hashcat -m 0 hash.txt -a 3 -1 ?l?d ?1?1?1?1?1?1?1?1
```

**Mask charset placeholders:**
| Symbol | Character Set |
|---|---|
| `?l` | lowercase a-z |
| `?u` | uppercase A-Z |
| `?d` | digits 0-9 |
| `?s` | special chars |
| `?a` | all printable ASCII |
| `?b` | all bytes 0x00-0xFF |

### Kerberoast hash cracking
```bash
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

### AS-REP Roast
```bash
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
```

### NetNTLMv2 (Responder captures)
```bash
hashcat -m 5600 netntlmv2.txt /usr/share/wordlists/rockyou.txt
```

### WPA/WPA2 WiFi
```bash
# Mode 22000 uses .hc22000 format produced by hcxpcapngtool (current workflow)
hashcat -m 22000 capture.hc22000 /usr/share/wordlists/rockyou.txt

# Workflow to get the .hc22000 file:
# 1. Capture with hcxdumptool:  hcxdumptool -i wlan0mon -o capture.pcapng
# 2. Convert:                   hcxpcapngtool -o capture.hc22000 capture.pcapng
# 3. Crack:                     hashcat -m 22000 capture.hc22000 rockyou.txt
```

### Resume a previously interrupted session
```bash
hashcat --session mysession --restore
```

### Show cracked passwords
```bash
hashcat -m 1000 ntlm_hashes.txt --show
```

---

## Important Flags / Options

| Flag | Description |
|---|---|
| `-m` | Hash mode number |
| `-a` | Attack mode (0=dict, 3=brute, 6=hybrid) |
| `-r` | Rules file |
| `-o` | Output cracked hashes to file |
| `--show` | Display previously cracked hashes |
| `--username` | Hash file includes username:hash format |
| `-w` | Workload profile (1=low, 3=high, 4=nightmare) |
| `--force` | Ignore GPU warnings (use in VMs) |
| `--session` | Name the session (for restore) |
| `--restore` | Restore interrupted session |
| `-O` | Optimized kernel — faster but limits max password length to 32 |
| `--increment` | Auto-increment mask length |
| `--increment-min` | Start increment at length N |
| `-S` | Slow candidates mode (required for some complex rules) |
| `-D 1` | Force CPU only |
| `-D 2` | Force GPU only |

---

## Useful Wordlists

| Wordlist | Location | Notes |
|---|---|---|
| rockyou.txt | `/usr/share/wordlists/rockyou.txt` | Classic; 14M passwords |
| SecLists | `/usr/share/seclists/Passwords/` | Many curated lists |
| kaonashi | GitHub | 1B+ entries; very powerful |
| OneRuleToRuleThemAll | GitHub | Best single rule file |

---

## Tips
- Always try `rockyou.txt` + `best64.rule` first — covers ~80% of weak passwords
- If that fails: `rockyou.txt` + `OneRuleToRuleThemAll.rule`
- If still failing: try a larger wordlist or mask attack
- GPU cracking is orders of magnitude faster than CPU — always prefer native GPU over VM

## Related Tools
- [[John-the-Ripper]] — alternative cracker; better for some formats
- [[Metasploit]] — dump hashes to crack
- Active-Directory — Kerberoast, AS-REP Roast context

## Related Notes
- Cryptography-Basics
- Active-Directory

## References
- Official docs: https://hashcat.net/wiki/
- Example hashes: https://hashcat.net/wiki/doku.php?id=example_hashes
- OneRuleToRuleThemAll: https://github.com/NotSoSecure/password_cracking_rules


## See Also

- Cryptography-Basics
- Penetration-Testing-Methodology
- John-the-Ripper
- Active-Directory

---
<- [[Tools-MOC]]