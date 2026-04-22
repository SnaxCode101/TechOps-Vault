---
title: John the Ripper
tags:
  - tool
  - password-cracking
  - hashing
  - offline-cracking
  - foundational
category: password-cracking
platform: cross-platform
install: apt install john / https://www.openwall.com/john/
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# 🛠️ John the Ripper

## What It Is
John the Ripper (JtR) is a free, open-source password security auditing and password recovery tool. It auto-detects hash types, cracks Linux shadow files, Windows NTLM hashes, zip/rar/office file passwords, SSH key passphrases, and many more. It is CPU-based (unlike Hashcat's GPU focus) and excels at cracking file-based passwords and unusual formats.

## Installation
```bash
# Debian/Ubuntu / Kali (pre-installed on Kali)
sudo apt install john

# Jumbo version (more formats — recommended)
sudo apt install john                  # Kali includes Jumbo

# Verify version
john --version
```

---

## Basic Syntax
```bash
john [options] [hashfile]
```

---

## Common Commands / Use Cases

### Auto-detect hash type and crack (single crack mode first)
```bash
john hashes.txt
```

### Wordlist attack
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

### Wordlist + rules
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes.txt
john --wordlist=/usr/share/wordlists/rockyou.txt --rules=jumbo hashes.txt
```

### Specify hash format explicitly
```bash
john --format=NT hashes.txt                           # Windows NTLM
john --format=sha512crypt hashes.txt                  # Linux $6$
john --format=bcrypt hashes.txt                       # bcrypt
john --format=raw-md5 hashes.txt                      # Raw MD5
john --wordlist=rockyou.txt --format=NT hashes.txt
```

### Show cracked passwords
```bash
john --show hashes.txt
john --show --format=NT hashes.txt
```

### List all supported formats
```bash
john --list=formats
john --list=formats | grep -i ntlm
```

---

## Cracking Linux /etc/shadow

```bash
# Unshadow combines /etc/passwd and /etc/shadow
unshadow /etc/passwd /etc/shadow > unshadowed.txt
john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```

---

## Cracking File Passwords (SSH, ZIP, RAR, Office)

John uses helper scripts (`*2john`) to convert protected files into crackable hash format:

```bash
# SSH private key passphrase
ssh2john id_rsa > ssh_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt ssh_hash.txt

# ZIP file password
zip2john protected.zip > zip_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt

# RAR file password
rar2john protected.rar > rar_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt

# Office document (Word, Excel)
office2john document.docx > office_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt office_hash.txt

# PDF password
pdf2john protected.pdf > pdf_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt pdf_hash.txt

# KeePass database
keepass2john database.kdbx > keepass_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt keepass_hash.txt

# 7-Zip
7z2john archive.7z > 7z_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt 7z_hash.txt
```

> All `*2john` scripts are typically in `/usr/share/john/` or accessible by name in Kali.

---

## Incremental (Brute-Force) Mode

```bash
# Full brute force (slow)
john --incremental hashes.txt

# Brute force only digits
john --incremental=digits hashes.txt

# Brute force only lowercase
john --incremental=lower hashes.txt
```

---

## Important Flags / Options

| Flag | Description |
|---|---|
| `--wordlist=` | Wordlist file to use |
| `--rules` | Apply default mangling rules |
| `--rules=jumbo` | Apply extended jumbo ruleset |
| `--format=` | Specify hash format explicitly |
| `--show` | Display cracked passwords from pot file |
| `--list=formats` | List all supported hash formats |
| `--incremental` | Brute-force mode |
| `--incremental=digits` | Brute-force digits only |
| `--session=name` | Save session under a name |
| `--restore=name` | Restore a named session |
| `--fork=N` | Use N CPU processes (parallel) |
| `--node=M/N` | Distributed cracking (this node is M of N) |
| `--pot=` | Specify a custom pot file location |
| `--no-log` | Suppress log file |

---

## John's Pot File

Cracked passwords are saved in `~/.john/john.pot`. Run `--show` to read them:

```bash
john --show hashes.txt
cat ~/.john/john.pot
```

---

## Hash Format Reference (Common)

```bash
john --list=formats | grep -i "md5\|ntlm\|sha\|bcrypt\|crypt"
```

| John Format String | Hash |
|---|---|
| `raw-md5` | Plain MD5 |
| `raw-sha1` | Plain SHA-1 |
| `raw-sha256` | Plain SHA-256 |
| `NT` | Windows NTLM |
| `mscash2` | Domain cached credentials |
| `sha512crypt` | Linux /etc/shadow $6$ |
| `sha256crypt` | Linux /etc/shadow $5$ |
| `md5crypt` | Linux /etc/shadow $1$ |
| `bcrypt` | bcrypt $2a$/$2b$ |
| `krb5tgs` | Kerberoast tickets |
| `krb5asrep` | AS-REP Roast |
| `zip` | ZIP password |
| `rar` / `rar5` | RAR password |
| `office` | MS Office |
| `pdf` | PDF |
| `ssh` | SSH private key passphrase |

---

## Tips
- John is best for file-based passwords (SSH, ZIP, KeePass) — for raw hash cracking at speed, prefer [[Hashcat]] with GPU
- Always run `--show` with the same `--format` you used to crack
- If auto-detection fails, identify hash type with `hash-identifier` or `haiti`, then specify `--format`
- The jumbo community edition (standard in Kali) has far more formats than vanilla John

## Related Tools
- [[Hashcat]] — GPU-accelerated; faster for bulk hash cracking
- Active-Directory — where NTLM, Kerberoast hashes come from

## Related Notes
- Cryptography-Basics
- Linux-Privilege-Escalation

## References
- Official docs: https://www.openwall.com/john/doc/
- Formats list: https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats


## See Also

- Cryptography-Basics
- Penetration-Testing-Methodology
- Hashcat
- Hydra
- Active-Directory

---
<- [[Tools-MOC]]