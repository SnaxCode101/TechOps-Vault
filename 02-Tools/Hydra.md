---
title: Hydra
tags:
  - tool
  - password-cracking
  - brute-force
  - online-cracking
  - authentication
  - foundational
category: exploitation
platform: cross-platform
install: apt install hydra
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# 🛠️ Hydra

## What It Is
Hydra is a fast, parallelized network login cracker supporting 50+ protocols including SSH, FTP, HTTP, SMB, RDP, MySQL, and many more. Unlike Hashcat and John (offline cracking), Hydra attacks **live services** over the network — making it an online brute-force tool.

## Installation
```bash
sudo apt install hydra       # Kali pre-installed
hydra -h                     # Verify
```

## Basic Syntax
```bash
hydra -l [username] -P [wordlist] [target] [protocol]
hydra -L [userlist] -P [wordlist] [target] [protocol]
```

---

## Common Commands / Use Cases

### SSH brute-force
```bash
# Single username
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10

# Username list
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10

# Custom port
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10 -s 2222
```

### FTP brute-force
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.10
```

### HTTP POST form (login page)
```bash
# Syntax: http-post-form "path:post_params:fail_string"
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-post-form "/login:username=^USER^&password=^PASS^:Invalid credentials"

# With cookies
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-post-form "/login:username=^USER^&password=^PASS^:Invalid:H=Cookie: PHPSESSID=abc123"

# HTTPS
hydra -l admin -P rockyou.txt https-post-form://10.10.10.10/login:user=^USER^&pass=^PASS^:Wrong
```

### HTTP GET form / Basic Auth
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt http-get://10.10.10.10/admin/
```

### SMB brute-force (Windows)
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://10.10.10.10
```

### RDP brute-force
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10
```

### MySQL brute-force
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt mysql://10.10.10.10
```

### SMTP brute-force
```bash
hydra -l user@example.com -P /usr/share/wordlists/rockyou.txt smtp://10.10.10.10
```

### Telnet brute-force
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt telnet://10.10.10.10
```

---

## How to Find POST Parameters
1. Open Burp Suite → intercept the login request
2. Note the POST body: e.g., `username=admin&password=test`
3. Note the failure string from the response: e.g., `Invalid credentials`
4. Replace values with `^USER^` and `^PASS^` for Hydra

---

## Important Flags / Options

| Flag | Description |
|---|---|
| `-l` | Single username |
| `-L` | Username list file |
| `-p` | Single password |
| `-P` | Password list file |
| `-s` | Custom port |
| `-t` | Number of parallel tasks/threads (default: 16) |
| `-f` | Stop after first valid found |
| `-F` | Stop when any pair found (across all targets) |
| `-v` / `-V` | Verbose / very verbose (show each attempt) |
| `-d` | Debug mode |
| `-o` | Output results to file |
| `-e nsr` | Try no password (n), same as username (s), reversed username (r) |
| `-u` | Loop around users, not passwords (try all users per password) |
| `-w` | Wait time between requests (seconds) |
| `-W` | Wait time between connections per thread |
| `-x min:max:charset` | Generate password mask (brute-force mode) |

---

## Supported Protocols
`adam6500`, `asterisk`, `cisco`, `cisco-enable`, `cvs`, `firebird`, `ftp`, `ftps`, `http[s]-{get,post,get-form,post-form}`, `http-proxy`, `http-proxy-urlenum`, `icq`, `imap`, `imap[s]`, `irc`, `ldap2`, `ldap3`, `mssql`, `mysql`, `mysql5`, `nntp`, `oracle-listener`, `oracle-sid`, `pcanywhere`, `pcnfs`, `pop3`, `pop3[s]`, `postgres`, `radmin2`, `rdp`, `redis`, `rexec`, `rlogin`, `rpcap`, `rsh`, `rtsp`, `s7-300`, `sip`, `smb`, `smtp`, `smtp[s]`, `snmp`, `socks5`, `ssh`, `sshkey`, `svn`, `teamspeak`, `telnet`, `vnc`, `xmpp`

---

## Opsec / Legal Notes
> ⚠️ Hydra generates significant network noise. Account lockout policies will trigger after failed attempts. Only use against systems you have explicit written authorization to test.
- Use `-t 4` or lower on sensitive targets to reduce lockout risk
- Use `-w 5` to add delays between attempts
- Many services log authentication attempts — assume you will be detected

## Related Tools
- [[Hashcat]] — offline cracking (faster, no lockout risk)
- [[Burp-Suite]] — Intruder for HTTP brute-force with more control
- [[Metasploit]] — `auxiliary/scanner/ssh/ssh_login` and similar

## Related Notes
- Active-Directory
- OWASP-Top-10

## References
- GitHub: https://github.com/vanhauser-thc/thc-hydra


## See Also

- Penetration-Testing-Methodology
- Cryptography-Basics
- John-the-Ripper
- Active-Directory
- Common-Ports

---
<- [[Tools-MOC]]