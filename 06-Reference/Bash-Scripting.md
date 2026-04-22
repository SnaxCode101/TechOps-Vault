---
title: Bash Scripting for Security
tags:
  - secops
  - reference
  - bash
  - scripting
  - automation
  - linux
  - foundational
created: 2026-03-23
folder: 06-Reference
status: complete
difficulty: foundational
---

# Bash Scripting for Security

> Bash is the glue of offensive and defensive security. Whether you're automating enumeration, building quick tools, or chaining commands in a pentest, bash scripting is a core skill.

---

## Bash Fundamentals

### Script Structure
```bash
#!/bin/bash
# Description: What this script does
# Usage: ./script.sh <target>
# Author: You

set -e          # Exit on error
set -u          # Exit on undefined variable
set -o pipefail # Catch pipe errors

main() {
    echo "Starting..."
}

main "$@"
```

### Variables
```bash
NAME="target"
IP="192.168.1.1"
PORT=80

# Command substitution
HOSTNAME=$(hostname)
DATE=$(date +%Y-%m-%d)
WHOAMI=$(whoami)

# Read-only
readonly API_KEY="abc123"

# Arrays
PORTS=(21 22 80 443 3306 8080)
echo ${PORTS[0]}          # First element
echo ${PORTS[@]}          # All elements
echo ${#PORTS[@]}         # Array length
```

### User Input
```bash
# Positional arguments
TARGET=$1
PORT=${2:-80}    # Default value if not provided
echo "Scanning $TARGET on port $PORT"

# Read from user
read -p "Enter target IP: " TARGET
read -sp "Enter password: " PASS    # Silent input
echo ""

# Argument parsing
while getopts "t:p:v" opt; do
    case $opt in
        t) TARGET="$OPTARG" ;;
        p) PORT="$OPTARG" ;;
        v) VERBOSE=true ;;
        *) echo "Usage: $0 -t <target> -p <port> [-v]"; exit 1 ;;
    esac
done
```

---

## Control Flow

### If/Else
```bash
if [ -z "$TARGET" ]; then
    echo "[-] No target specified"
    exit 1
elif [ "$TARGET" == "localhost" ]; then
    echo "[!] Targeting localhost"
else
    echo "[*] Target: $TARGET"
fi

# File tests
[ -f file.txt ] && echo "File exists"
[ -d /tmp ]     && echo "Directory exists"
[ -r file.txt ] && echo "File is readable"
[ -x script.sh ] && echo "File is executable"

# String tests
[ -z "$var" ]  # Empty
[ -n "$var" ]  # Not empty
[ "$a" == "$b" ]  # Equal
[ "$a" != "$b" ]  # Not equal

# Numeric tests
[ $n -eq 0 ]   # Equal
[ $n -ne 0 ]   # Not equal
[ $n -gt 10 ]  # Greater than
[ $n -lt 10 ]  # Less than
```

### Loops
```bash
# For loop over list
for PORT in 21 22 80 443 8080; do
    echo "Checking port $PORT"
done

# For loop over range
for i in {1..254}; do
    echo "192.168.1.$i"
done

# For loop over array
TARGETS=("192.168.1.1" "192.168.1.2" "192.168.1.3")
for TARGET in "${TARGETS[@]}"; do
    echo "Scanning $TARGET"
done

# While loop
COUNT=0
while [ $COUNT -lt 10 ]; do
    echo $COUNT
    ((COUNT++))
done

# Read file line by line
while IFS= read -r line; do
    echo "Processing: $line"
done < wordlist.txt
```

### Functions
```bash
# Define function
banner() {
    echo "================================"
    echo "  $1"
    echo "================================"
}

check_host() {
    local host=$1
    local port=${2:-80}
    if nc -zw1 "$host" "$port" 2>/dev/null; then
        echo "[+] $host:$port is open"
        return 0
    else
        echo "[-] $host:$port is closed"
        return 1
    fi
}

# Call function
banner "Port Scanner"
check_host "192.168.1.1" 22
```

---

## Security-Specific Scripts

### Port Scanner
```bash
#!/bin/bash
# Simple TCP port scanner

TARGET=${1:?"Usage: $0 <target> [start_port] [end_port]"}
START=${2:-1}
END=${3:-1024}

echo "[*] Scanning $TARGET ports $START-$END"
echo "-----------------------------------"

for PORT in $(seq $START $END); do
    (echo >/dev/tcp/$TARGET/$PORT) 2>/dev/null && echo "[+] $PORT/tcp open"
done
```

### Host Discovery (Ping Sweep)
```bash
#!/bin/bash
# Ping sweep a /24 subnet

SUBNET=${1:?"Usage: $0 <subnet> (e.g. 192.168.1)"}

echo "[*] Scanning $SUBNET.0/24"
for i in $(seq 1 254); do
    HOST="$SUBNET.$i"
    ping -c1 -W1 "$HOST" &>/dev/null && echo "[+] $HOST is alive" &
done
wait
echo "[*] Scan complete"
```

### Directory Brute-Force
```bash
#!/bin/bash
# Web directory brute-forcer

URL=${1:?"Usage: $0 <url> <wordlist>"}
WORDLIST=${2:-"/usr/share/wordlists/dirb/common.txt"}

echo "[*] Scanning $URL"
while IFS= read -r word; do
    word=$(echo "$word" | tr -d '\r\n')
    [ -z "$word" ] && continue
    [[ "$word" == \#* ]] && continue    # Skip comments
    
    CODE=$(curl -s -o /dev/null -w "%{http_code}" "$URL/$word" --max-time 3)
    [ "$CODE" != "404" ] && [ "$CODE" != "000" ] && echo "[$CODE] /$word"
done < "$WORDLIST"
```

### Password Spray
```bash
#!/bin/bash
# SSH password spray (authorised testing only)

TARGET=$1
USERNAME=$2
WORDLIST=$3

while IFS= read -r pass; do
    pass=$(echo "$pass" | tr -d '\r\n')
    result=$(sshpass -p "$pass" ssh -o StrictHostKeyChecking=no \
             -o ConnectTimeout=3 "$USERNAME@$TARGET" "id" 2>/dev/null)
    if [ -n "$result" ]; then
        echo "[+] SUCCESS: $pass"
        echo "[+] $result"
        break
    fi
    sleep 1    # Avoid lockout
done < "$WORDLIST"
```

### Log Parser (find failed logins)
```bash
#!/bin/bash
# Parse auth.log for failed SSH logins

LOG=${1:-"/var/log/auth.log"}

echo "[*] Failed login attempts:"
grep "Failed password" "$LOG" | \
    awk '{print $11}' | \
    sort | \
    uniq -c | \
    sort -rn | \
    head -20

echo ""
echo "[*] Top source IPs:"
grep "Failed password" "$LOG" | \
    grep -oE "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" | \
    sort | uniq -c | sort -rn | head -10
```

### Reverse Shell Listener
```bash
#!/bin/bash
# Quick reverse shell listener with logging

PORT=${1:-4444}
LOGFILE="shells/session_$(date +%Y%m%d_%H%M%S).log"

mkdir -p shells
echo "[*] Listening on port $PORT — logging to $LOGFILE"
nc -lvnp "$PORT" | tee "$LOGFILE"
```

---

## Text Processing (Essential for Security)

```bash
# grep — search files/output
grep "password" config.txt
grep -r "api_key" /var/www/          # Recursive
grep -i "error" logfile.txt          # Case insensitive
grep -v "404" access.log             # Invert match
grep -oE "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" file  # Extract IPs

# awk — field extraction
awk '{print $1}' access.log          # First field
awk -F: '{print $1}' /etc/passwd     # Split on : — usernames
awk '$9 == 200' access.log           # Filter by HTTP 200

# sed — stream editor
sed 's/foo/bar/g' file.txt           # Replace all
sed -n '10,20p' file.txt             # Print lines 10-20
sed '/^#/d' config.txt               # Delete comment lines

# sort and uniq
cat ips.txt | sort | uniq -c | sort -rn   # Count unique IPs
cat wordlist.txt | sort -u                 # Deduplicate

# cut — column extraction
cut -d: -f1 /etc/passwd              # Usernames from passwd
echo "a:b:c" | cut -d: -f2           # b

# tr — translate/delete characters
echo "Hello World" | tr 'A-Z' 'a-z'  # Lowercase
echo "a b c" | tr -d ' '             # Remove spaces
cat file | tr -s '\n'                 # Squeeze newlines
```

---

## Useful Bash Tricks for Security

```bash
# Background a process
nmap -sV target.com &
NMAP_PID=$!

# Parallel execution with wait
for host in $(cat hosts.txt); do
    nmap -sV "$host" -oN "scan_$host.txt" &
done
wait
echo "All scans complete"

# Timeout a command
timeout 10 nc -zv target.com 80

# Check if running as root
[ "$EUID" -ne 0 ] && echo "Run as root" && exit 1

# Redirect stderr to /dev/null
command 2>/dev/null

# Both stdout and stderr to file
command > output.txt 2>&1

# Tee — output to screen AND file
nmap target.com | tee scan_results.txt

# Here strings
mysql -u root -p <<< "SHOW DATABASES;"

# Process substitution
diff <(ls dir1) <(ls dir2)

# Brace expansion
echo {a,b,c}.txt        # a.txt b.txt c.txt
echo {1..10}            # 1 2 3 4 5 6 7 8 9 10

# Quick IP extraction
echo "Connection from 192.168.1.50 port 4444" | grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}"
```

---

## Error Handling

```bash
#!/bin/bash

# Exit on any error
set -e

# Custom error handler
error_exit() {
    echo "[-] ERROR: $1" >&2
    exit 1
}

# Trap errors
trap 'error_exit "Error on line $LINENO"' ERR

# Check command success
if ! command -v nmap &>/dev/null; then
    error_exit "nmap not installed"
fi

# Validate input
validate_ip() {
    local ip=$1
    if [[ ! $ip =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]; then
        error_exit "Invalid IP: $ip"
    fi
}
```

---

## Script Template (Production Quality)

```bash
#!/bin/bash
# ============================================
# Script: scanner.sh
# Description: TCP port scanner
# Usage: ./scanner.sh -t <target> [-p <ports>] [-v]
# ============================================

set -euo pipefail

# Colours
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'

# Defaults
TARGET=""
PORTS="1-1024"
VERBOSE=false

usage() {
    echo "Usage: $0 -t <target> [-p <port-range>] [-v]"
    exit 1
}

log_info()    { echo -e "${GREEN}[*]${NC} $1"; }
log_success() { echo -e "${GREEN}[+]${NC} $1"; }
log_warn()    { echo -e "${YELLOW}[!]${NC} $1"; }
log_error()   { echo -e "${RED}[-]${NC} $1" >&2; }

# Parse args
while getopts "t:p:vh" opt; do
    case $opt in
        t) TARGET="$OPTARG" ;;
        p) PORTS="$OPTARG" ;;
        v) VERBOSE=true ;;
        h|*) usage ;;
    esac
done

[ -z "$TARGET" ] && log_error "Target required" && usage

log_info "Scanning $TARGET (ports $PORTS)"
# ... rest of script
```

---

## Related Notes
- Python-for-Security
- Linux-Commands
- Linux-Privilege-Escalation
- Penetration-Testing-Methodology
- Netcat

## See Also

- Linux-Commands
- Netcat
- Python-for-Security
- Penetration-Testing-Methodology
- Linux-Privilege-Escalation

---

## Cron-Ready Script Patterns

Scripts run from cron behave differently — no interactive terminal, limited PATH.

```bash
#!/bin/bash
# ── Cron-safe script template ──────────────────────────────────────
# Set explicit PATH (cron has minimal environment)
export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

# Absolute paths for all binaries and files
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
LOG="/var/log/myapp/cron_$(date +%Y%m%d).log"
LOCKFILE="/tmp/myapp.lock"

# Prevent concurrent execution
if [ -e "$LOCKFILE" ]; then
    echo "$(date): Already running, exiting." >> "$LOG"
    exit 0
fi
touch "$LOCKFILE"
trap "rm -f '$LOCKFILE'" EXIT

# Redirect all output to log (cron can't display to terminal)
exec >> "$LOG" 2>&1

echo "$(date): Starting job"

# ... your logic here ...

echo "$(date): Job complete"
```

**Crontab entries for security tasks:**
```bash
# Daily nmap scan of critical hosts
0 3 * * * /usr/local/bin/nmap-daily.sh >> /var/log/nmap_daily.log 2>&1

# Parse auth.log for failed logins every hour
0 * * * * /usr/local/bin/failed-login-report.sh

# Weekly backup of configs
0 1 * * 0 tar -czf /backup/configs_$(date +\%Y\%m\%d).tar.gz /etc/ 2>&1

# Rotate own log weekly
0 4 * * 0 find /var/log/myapp/ -name "*.log" -mtime +30 -delete
```

---

## JSON Processing with jq

`jq` is a command-line JSON processor — essential for working with APIs, alert feeds, and modern log formats.

```bash
# Install
sudo apt install jq     # Debian/Ubuntu
sudo yum install jq     # RHEL/CentOS

# Basic usage: pretty-print JSON
curl -s https://api.example.com/data | jq '.'

# Extract a field
echo '{"name":"alice","role":"admin"}' | jq '.name'
# Output: "alice"

# Extract without quotes
echo '{"name":"alice"}' | jq -r '.name'
# Output: alice

# Nested fields
echo '{"user":{"ip":"10.0.0.1","port":4444}}' | jq '.user.ip'

# Array access
echo '[{"host":"a"},{"host":"b"}]' | jq '.[0].host'
echo '[{"host":"a"},{"host":"b"}]' | jq '.[].host'   # All elements

# Filter array
echo '[{"port":22,"state":"open"},{"port":80,"state":"closed"}]' | \
    jq '.[] | select(.state == "open") | .port'

# Create new object from fields
echo '{"ip":"10.0.0.1","port":22,"state":"open"}' | \
    jq '{host: .ip, service_port: .port}'

# Count array elements
echo '[1,2,3,4,5]' | jq 'length'

# String interpolation
echo '{"host":"server1","port":22}' | jq -r '"\(.host):\(.port)"'
# Output: server1:22

# Process nmap XML output (after converting to JSON)
# Real-world: parse a JSON alert feed
curl -s https://api.threatfeed.example.com/iocs | \
    jq -r '.indicators[] | select(.type == "ip") | .value' > blocklist.txt

# Parse AWS CLI JSON output
aws ec2 describe-instances | \
    jq -r '.Reservations[].Instances[] | "\(.InstanceId) \(.State.Name) \(.PublicIpAddress)"'

# Combine with loops
curl -s https://api.example.com/hosts | jq -r '.[].ip' | while read ip; do
    echo "Scanning $ip"
    nmap -sV "$ip" -oN "scan_${ip}.txt"
done

# Parse logs in JSON format (e.g., Suricata, Zeek)
cat /var/log/suricata/eve.json | \
    jq -c 'select(.event_type == "alert")' | \
    jq -r '[.timestamp, .alert.signature, .src_ip, .dest_ip] | @tsv'
```

**Common jq patterns for security:**
```bash
# Extract all unique IPs from JSON log
cat alerts.json | jq -r '.[].src_ip' | sort -u

# Filter by severity and output CSV
cat alerts.json | jq -r '.[] | select(.severity >= 3) | [.id, .name, .src_ip] | @csv'

# Check if field exists
cat data.json | jq 'select(.malicious == true)'

# Add field to each object
cat hosts.json | jq '[.[] | . + {"scanned": true}]'

# keys — list all keys in an object
echo '{"a":1,"b":2}' | jq 'keys'
```

---
<- [[Reference-MOC]]