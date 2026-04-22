---
title: Regex Reference
tags:
  - reference
  - regex
  - grep
  - sed
  - awk
  - python
  - cheat-sheet
  - foundational
created: 2026-03-26
updated: 2026-03-26
difficulty: foundational
---

# 🔤 Regex Reference

## Quick Syntax

| Pattern | Matches |
|---|---|
| `.` | Any single character (except newline) |
| `\d` | Digit (0–9) |
| `\D` | Non-digit |
| `\w` | Word character (a-z, A-Z, 0-9, _) |
| `\W` | Non-word character |
| `\s` | Whitespace (space, tab, newline) |
| `\S` | Non-whitespace |
| `\b` | Word boundary |
| `\B` | Non-word boundary |
| `^` | Start of string/line |
| `$` | End of string/line |
| `[abc]` | Character class — a, b, or c |
| `[^abc]` | Negated class — NOT a, b, or c |
| `[a-z]` | Range — lowercase a through z |
| `[A-Z0-9]` | Uppercase + digits |

## Quantifiers

| Quantifier | Meaning |
|---|---|
| `*` | 0 or more (greedy) |
| `+` | 1 or more (greedy) |
| `?` | 0 or 1 (optional) |
| `{n}` | Exactly n times |
| `{n,}` | n or more |
| `{n,m}` | Between n and m |
| `*?` `+?` `??` | Non-greedy (lazy) versions |

## Groups & Alternation

| Syntax | Meaning |
|---|---|
| `(abc)` | Capturing group |
| `(?:abc)` | Non-capturing group |
| `(?P<name>abc)` | Named group (Python) |
| `a\|b` | Alternation — a or b |
| `\1` | Backreference to group 1 |
| `(?=abc)` | Positive lookahead |
| `(?!abc)` | Negative lookahead |
| `(?<=abc)` | Positive lookbehind |
| `(?<!abc)` | Negative lookbehind |

---

## Security-Relevant Patterns

```regex
# IPv4 address
\b(?:\d{1,3}\.){3}\d{1,3}\b

# IPv4 with CIDR
\b(?:\d{1,3}\.){3}\d{1,3}/\d{1,2}\b

# IPv6 (simplified)
([0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}

# Email address
[a-zA-Z0-9._%+\-]+@[a-zA-Z0-9.\-]+\.[a-zA-Z]{2,}

# URL (http/https)
https?://[^\s/$.?#].[^\s]*

# Domain name
(?:[a-zA-Z0-9](?:[a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?\.)+[a-zA-Z]{2,}

# MD5 hash
\b[a-fA-F0-9]{32}\b

# SHA-1 hash
\b[a-fA-F0-9]{40}\b

# SHA-256 hash
\b[a-fA-F0-9]{64}\b

# Windows file path
[A-Za-z]:\\(?:[^\\/:*?"<>|\r\n]+\\)*[^\\/:*?"<>|\r\n]*

# Linux file path
/(?:[^/\0]+/)*[^/\0]*

# CTF flag formats
(?:flag|CTF|HTB|THM|picoCTF)\{[^}]+\}
[A-Z]{2,}\{[^}]+\}

# Base64 string
(?:[A-Za-z0-9+/]{4})*(?:[A-Za-z0-9+/]{2}==|[A-Za-z0-9+/]{3}=|[A-Za-z0-9+/]{4})

# Credit card (common formats)
\b(?:\d{4}[- ]?){3}\d{4}\b

# CVE ID
CVE-\d{4}-\d{4,7}

# AWS Access Key
(?:A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}

# JWT token
[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+

# UUID
[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}
```

---

## grep Usage

```bash
# Basic match
grep "pattern" file.txt

# Case insensitive
grep -i "password" file.txt

# Extended regex (ERE) — enables +, ?, |, (), {}
grep -E "error|warning|critical" logfile

# Perl-compatible regex (PCRE) — full lookahead/lookbehind
grep -P "(?<=password:\s)\S+" file.txt

# Recursive search
grep -r "pattern" /var/log/

# Show line numbers
grep -n "pattern" file.txt

# Invert match (show non-matching lines)
grep -v "pattern" file.txt

# Count matches
grep -c "pattern" file.txt

# Show only matching part (not whole line)
grep -o "pattern" file.txt

# Surrounding context
grep -A 3 "error" file.txt    # 3 lines after
grep -B 3 "error" file.txt    # 3 lines before
grep -C 3 "error" file.txt    # 3 lines before and after

# Search for IPs in log file
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' access.log | sort | uniq -c | sort -rn

# Find hashes in file
grep -oE '\b[a-fA-F0-9]{32}\b' file.txt   # MD5
grep -oE '\b[a-fA-F0-9]{64}\b' file.txt   # SHA-256

# Find credentials pattern
grep -rE "(password|passwd|pwd|secret|api_key)\s*[:=]\s*\S+" . --include="*.txt"
```

---

## sed Usage

```bash
# Basic substitution (first occurrence per line)
sed 's/old/new/' file.txt

# Global substitution (all occurrences per line)
sed 's/old/new/g' file.txt

# Case insensitive substitution
sed 's/old/new/gi' file.txt

# In-place editing
sed -i 's/old/new/g' file.txt
sed -i.bak 's/old/new/g' file.txt  # With backup

# Delete lines matching pattern
sed '/pattern/d' file.txt

# Print specific line numbers
sed -n '10,20p' file.txt

# Remove blank lines
sed '/^$/d' file.txt

# Remove trailing whitespace
sed 's/[[:space:]]*$//' file.txt

# Extract IP addresses
sed -n 's/.*\b\([0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\)\b.*/\1/p' file.txt
```

---

## awk Usage

```bash
# Print specific column
awk '{print $1}' file.txt    # First field
awk '{print $NF}' file.txt   # Last field

# Custom field separator
awk -F: '{print $1}' /etc/passwd   # Colon-separated

# Pattern match
awk '/error/ {print}' logfile

# Conditional
awk '$3 > 100 {print $1, $3}' file.txt

# Count lines matching pattern
awk '/error/ {count++} END {print count}' logfile

# Sum a column
awk '{sum += $4} END {print sum}' access.log

# Print unique IPs and count from Apache log
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -20

# Extract username from /etc/passwd
awk -F: '{print $1}' /etc/passwd

# Print lines between patterns
awk '/START/,/END/ {print}' file.txt
```

---

## Python re Module

```python
import re

# Basic match (start of string)
m = re.match(r'\d+', '123abc')
m.group()  # '123'

# Search (anywhere in string)
m = re.search(r'\d+', 'abc123def')
m.group()  # '123'

# Find all matches
re.findall(r'\d+', 'a1b2c3')   # ['1', '2', '3']

# Find with groups
re.findall(r'(\w+)@(\w+)', 'user@host')  # [('user', 'host')]

# Substitution
re.sub(r'\d+', 'X', 'a1b2c3')  # 'aXbXcX'

# Split
re.split(r'\s+', 'a  b   c')   # ['a', 'b', 'c']

# Compile for reuse (performance)
pattern = re.compile(r'CVE-\d{4}-\d{4,7}', re.IGNORECASE)
matches = pattern.findall(text)

# Flags
re.IGNORECASE    # Case insensitive
re.MULTILINE     # ^ and $ match line boundaries
re.DOTALL        # . matches newline too
re.VERBOSE       # Allow whitespace/comments in pattern

# Named groups
m = re.match(r'(?P<year>\d{4})-(?P<month>\d{2})', '2026-03')
m.group('year')   # '2026'
m.group('month')  # '03'
```

---

## Related Notes
- Bash-Scripting
- Python-for-Security
- Linux-Commands
- Encoding-Decoding-Reference

---
<- [[Reference-MOC]]