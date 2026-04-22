---
title: CTF Quick Reference
tags:
  - foundational
  - CTF
  - reference
  - cheat-sheet
  - encoding
  - quick-reference
topic: CTF Reference
difficulty: foundational
created: 2026-03-27
updated: 2026-04-03
source: CTF community conventions, CyberChef docs, PicoCTF, HackTheBox, TryHackMe
---

# CTF Quick Reference

> Study reference for use in authorized CTF competitions and lab environments (HackTheBox, TryHackMe, PicoCTF, VulnHub).
> A rapid-lookup companion to [[CTF-Methodology]]. Use during active challenges for fast identification and decoding.

---

## Encoding / Format Identification

| Pattern | Encoding |
|---|---|
| Only `A-Z a-z 0-9 + / =` (ends with `=` or `==`) | Base64 |
| Only `0-9 A-F` (even length) | Hex |
| Only `0` and `1` | Binary |
| `.- -... -.-. -.` (dots and dashes) | Morse code |
| Numbers separated by spaces (32–126 range) | ASCII decimal |
| `%XX` percent-encoded characters | URL encoding |
| `&#XX;` or `&amp;` style | HTML entities |
| Three parts separated by `.` — `xxxx.xxxx.xxxx` | JWT (JSON Web Token) |
| Looks like normal text but output makes no sense | ROT13 / Caesar shift |
| `gur synt vf` / `Gur synt vf` | ROT13 (shift 13) |
| `== CERTIFICATE` / `BEGIN RSA` block | PEM-encoded key/cert |
| `UEsDBBQ` (starts with) | Base64-encoded ZIP |
| `H4sI` (starts with) | Base64-encoded GZIP |
| `7z` magic / `PK` magic | Archive (7zip / ZIP) |

---

## Magic Bytes (File Header Identification)

| Magic Bytes (hex) | File Type |
|---|---|
| `FF D8 FF` | JPEG |
| `89 50 4E 47` | PNG |
| `47 49 46 38` | GIF |
| `50 4B 03 04` | ZIP |
| `52 61 72 21` | RAR |
| `1F 8B` | GZIP |
| `7F 45 4C 46` | ELF binary (Linux) |
| `4D 5A` | PE executable (Windows .exe/.dll) |
| `25 50 44 46` | PDF |
| `FF FB` / `49 44 33` | MP3 |
| `52 49 46 46` | WAV / AVI |
| `4F 67 67 53` | OGG |

```bash
# Check magic bytes of a challenge file
xxd file | head -2
file challenge_file      # Let 'file' identify it automatically
```

---

## Hash Identification

| Hash Length (hex chars) | Algorithm |
|---|---|
| 32 | MD5 |
| 40 | SHA-1 |
| 56 | SHA-224 |
| 64 | SHA-256 |
| 96 | SHA-384 |
| 128 | SHA-512 |
| 32 — starts with `$1$` | MD5-crypt |
| 60 — starts with `$2y$` or `$2a$` | bcrypt |
| starts with `$5$` | SHA-256-crypt |
| starts with `$6$` | SHA-512-crypt |
| 32 hex (AD context) | NTLM |
| `aad3b435...` (specific pattern) | Empty LM hash / LM disabled placeholder |

```bash
# Identify hash type
hashid 'hashvalue'
hash-identifier
```

---

## Quick Decode One-Liners

```bash
# Base64 decode
echo "SGVsbG8gV29ybGQ=" | base64 -d
python3 -c "import base64; print(base64.b64decode('SGVsbG8=').decode())"

# Hex decode
echo "48656c6c6f" | xxd -r -p
python3 -c "print(bytes.fromhex('48656c6c6f').decode())"

# Binary decode (single byte example)
python3 -c "print(int('01001000',2))"
# Full string: join chr(int(b,2)) for each 8-bit chunk

# ROT13
echo "Uryyb Jbeyq" | tr 'A-Za-z' 'N-ZA-Mn-za-m'

# ROT-N (arbitrary shift)
python3 -c "from codecs import encode; print(encode('hello','rot_13'))"
# Or: CyberChef -> ROT13 with adjustable rotation

# URL decode
python3 -c "from urllib.parse import unquote; print(unquote('%48%65%6c%6c%6f'))"

# HTML entity decode
python3 -c "from html import unescape; print(unescape('&lt;b&gt;Hello&lt;/b&gt;'))"

# XOR single-byte key brute force (for CTF cipher challenges)
# Tests all 256 possible single-byte XOR keys against sample ciphertext
# Prints any key that produces printable ASCII output
python3 -c "
data = bytes.fromhex('1a0b1c0d')  # Replace with challenge ciphertext hex
for k in range(256):
    out = bytes(b ^ k for b in data)
    if all(32 <= c < 127 for c in out):
        print(f'Key {k}: {out.decode()}')"
```

---

## CyberChef Recipes (Key Operations)

URL: https://gchq.github.io/CyberChef/

| Need | CyberChef Operation |
|---|---|
| Detect encoding | Magic |
| Base64 to text | From Base64 |
| Hex to text | From Hex |
| Multiple encoding layers | Chain operations top to bottom |
| JWT decode | JWT Decode |
| XOR brute force | XOR Brute Force |
| Frequency analysis | Chi Square / Frequency Analysis |
| Hashing | MD5, SHA-256, SHA-512 etc. |

---

## CTF Default Credentials Reference

CTF challenges intentionally deploy services with weak or default credentials as part of the puzzle design. This table documents patterns commonly seen in challenge environments for study purposes.

> These credentials are placed intentionally in CTF challenges to be discovered. Do not use against real systems.

| Service | Common CTF Username | Common CTF Credential Pattern |
|---|---|---|
| Web apps (generic) | `admin` | Simple dictionary words — `admin`, `password`, numeric suffixes |
| WordPress challenges | `admin` | Often default or challenge-hinted in page source |
| MySQL | `root` | Often unconfigured (blank) in CTF containers |
| MSSQL | `sa` | Often unconfigured or blank in CTF VMs |
| Apache Tomcat | `tomcat` | Documented defaults in Apache release notes |
| Jenkins | `admin` | Check `/secrets/initialAdminPassword` on first-run installs |
| FTP | `anonymous` | RFC-standard anonymous FTP — blank or any email as pass |
| SNMP | N/A | Community strings: `public` (read), `private` (write) — RFC defaults |
| Redis | N/A | No authentication by default in older versions |
| MongoDB | N/A | No authentication by default in older versions |
| Elasticsearch | N/A | No authentication by default on older installs |

**Tip:** Always check `robots.txt`, page source, and HTTP headers for credential hints embedded by challenge designers.

---

## Python CTF Study Snippets

> Educational code samples for CTF cipher, crypto, and forensics challenges.
> All examples use toy/sample data — replace with actual challenge values.

```python
# --- File Operations ---

# Read a binary challenge file
with open('challenge', 'rb') as f:
    data = f.read()

# --- XOR Cipher (common CTF encoding) ---

# XOR with a repeating key (Vigenere-style XOR)
def xor_encrypt_decrypt(data: bytes, key: bytes) -> bytes:
    """XOR each byte of data with the repeating key. Works for both encrypt and decrypt."""
    return bytes(b ^ key[i % len(key)] for i, b in enumerate(data))

# --- Pattern Matching ---

# Search for flag-format strings in binary data
import re
flag_pattern = re.compile(b'[A-Z]{2,10}\\{[^}]+\\}')
matches = flag_pattern.findall(data)  # e.g. finds HTB{...} or CTF{...}

# --- Frequency Analysis (for classical cipher challenges) ---

from collections import Counter
# Count character frequency to identify most common letters
# In English: most common are e, t, a, o, i, n
char_freq = Counter(ciphertext.lower())
print(char_freq.most_common(10))

# --- RSA Basics (toy example for small-number CTF challenges) ---
# Reference: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
#
# Given: prime factors p and q, public exponent e, ciphertext c
# Goal: recover private key d and decrypt message m
#
# This only works on CTF challenges with intentionally small primes
# Real RSA uses primes hundreds of digits long (unfactorable)

from Crypto.Util.number import inverse   # pip install pycryptodome

# Sample values from a toy CTF problem
p, q = 61, 53          # Prime factors (given or factored from small n)
e    = 17              # Public exponent
c    = 2790            # Ciphertext (the encrypted number)

n   = p * q            # Public modulus
phi = (p - 1) * (q - 1)  # Euler's totient
d   = inverse(e, phi)  # Private exponent: d * e ≡ 1 (mod phi)
m   = pow(c, d, n)     # Decrypt: m = c^d mod n
print(f"Decrypted: {m}")

# --- Padding ---

def pkcs7_unpad(data: bytes) -> bytes:
    """Remove PKCS7 padding from decrypted block cipher output."""
    pad_len = data[-1]
    return data[:-pad_len]
```

---

## Common Flag Formats

| Platform | Format |
|---|---|
| Generic | `flag{...}` · `CTF{...}` · `FLAG{...}` |
| HackTheBox | `HTB{...}` |
| TryHackMe | `THM{...}` |
| PicoCTF | `picoCTF{...}` |
| Google CTF | `CTF{...}` |
| DEFCON | `OOO{...}` |

```bash
# Search for flag-format strings in any challenge file
strings challenge_file | grep -iE "(flag|ctf|htb|thm|pico)\{[^}]+\}"
grep -rao '[A-Z_]{2,10}{[^}]+}' challenge_file
```

---

## Steganography Quick Checks

```bash
# Check image metadata for hidden clues
exiftool image.jpg

# Check for files embedded inside an image
binwalk image.png
binwalk -e image.png    # Extract embedded files

# LSB (Least Significant Bit) steganography analysis
zsteg image.png         # PNG/BMP — checks multiple bit planes
stegsolve               # Java GUI — try all bit planes visually

# Steghide (password-protected hidden data in JPG/BMP)
steghide info file.jpg
steghide extract -sf file.jpg

# Audio: check for hidden data in frequency spectrum
# Open in Audacity: View -> Spectrogram
# Or: Sonic Visualizer -> Layer -> Add Spectrogram

# PDF: extract embedded objects
pdfextract file.pdf
pdf-parser.py file.pdf
```

---

## Reverse Engineering Quick Checks

```bash
# Identify file type and binary architecture
file binary
checksec binary          # Display security protections (NX, PIE, RELRO, canary)

# Extract readable strings from the binary
# Useful for finding hardcoded flags, error messages, and function names
strings binary | grep -i flag
strings binary | grep -iE "correct|wrong|well done|try again"

# Dynamic analysis — trace execution
ltrace ./binary          # Trace library (libc) calls
strace ./binary          # Trace system calls (open, read, write, execve)
gdb -q ./binary          # Interactive debugger

# Static analysis — no execution required
ghidra                   # Decompile to C — find main(), locate flag comparison logic
objdump -d binary        # Disassemble to assembly
readelf -a binary        # Display ELF headers, symbols, sections

# Check for packing / obfuscation
die binary               # Detect It Easy — identify packer/compiler
```

---

## Related Notes
- CTF-Methodology — Full category-by-category methodology
- Encoding-Decoding-Reference — Full encoding reference
- Python-for-Security — Python scripting for CTFs
- Reverse-Shell-Cheatsheet — Shell reference for CTF post-exploitation

---
<- [[CTF-MOC]]