---
title: Encoding & Decoding Reference
tags:
  - reference
  - encoding
  - decoding
  - base64
  - hex
  - ROT13
  - JWT
  - CyberChef
  - cheat-sheet
  - foundational
created: 2026-03-26
updated: 2026-03-26
difficulty: foundational
---

# 🔣 Encoding & Decoding Reference

> The most commonly encountered encodings in CTFs, pentesting, and security work — with quick decode commands for every one.

---

## Base64

```bash
# Encode
echo -n "Hello World" | base64                   # SGVsbG8gV29ybGQ=
echo -n "Hello World" | base64 -w0               # No line wrap

# Decode
echo "SGVsbG8gV29ybGQ=" | base64 -d
echo "SGVsbG8gV29ybGQ=" | base64 --decode

# File encode/decode
base64 file.bin > file.b64
base64 -d file.b64 > file.bin

# Python
import base64
base64.b64encode(b"Hello")          # b'SGVsbG8='
base64.b64decode("SGVsbG8=")        # b'Hello'

# URL-safe Base64 (uses - and _ instead of + and /)
base64.urlsafe_b64encode(b"Hello World")
base64.urlsafe_b64decode("SGVsbG8gV29ybGQ=")
```

### Base64 Variants
| Variant | Alphabet | Padding |
|---|---|---|
| Standard | A-Z, a-z, 0-9, +, / | = |
| URL-safe | A-Z, a-z, 0-9, -, _ | = |
| Base32 | A-Z, 2-7 | = |
| Base58 | A-Z, a-z, 0-9 (no 0, O, I, l) | None |
| Base85 | Printable ASCII | None |

```bash
# Base32
echo -n "Hello" | base32          # JBSWY3DP
echo "JBSWY3DP" | base32 -d       # Hello

# Python base32
import base64
base64.b32encode(b"Hello")
base64.b32decode("JBSWY3DP")
```

---

## Hexadecimal

```bash
# Text to hex
echo -n "Hello" | xxd -p              # 48656c6c6f
echo -n "Hello" | od -An -tx1 | tr -d ' \n'

# Hex to text
echo "48656c6c6f" | xxd -r -p         # Hello
printf "\x48\x65\x6c\x6c\x6f"        # Hello

# Python
"Hello".encode().hex()                # '48656c6c6f'
bytes.fromhex("48656c6c6f").decode()  # 'Hello'

# xxd hex dump
xxd file                              # Full hex dump
xxd -l 32 file                        # First 32 bytes
xxd -r hexdump.txt output.bin         # Reverse hex dump
```

---

## URL Encoding

```bash
# Python urlencode
python3 -c "import urllib.parse; print(urllib.parse.quote('Hello World&test=1'))"
# Hello%20World%26test%3D1

# Decode
python3 -c "import urllib.parse; print(urllib.parse.unquote('Hello%20World'))"

# Full encode (encode everything, not just special chars)
python3 -c "import urllib.parse; print(urllib.parse.quote('Hello World', safe=''))"

# Double URL encode
python3 -c "import urllib.parse; s=urllib.parse.quote('/etc/passwd'); print(urllib.parse.quote(s))"
```

---

## HTML Encoding

```bash
# Python
import html
html.escape('<script>alert(1)</script>')
# '&lt;script&gt;alert(1)&lt;/script&gt;'

html.unescape('&lt;b&gt;Hello&lt;/b&gt;')
# '<b>Hello</b>'

# Common HTML entities:
# &amp;  → &
# &lt;   → <
# &gt;   → >
# &quot; → "
# &#39;  → '
# &#x41; → A (hex)
# &#65;  → A (decimal)
```

---

## ROT13 / Caesar Cipher

```bash
# ROT13
echo "Uryyb" | tr 'A-Za-z' 'N-ZA-Mn-za-m'    # Hello

# ROT-N (any rotation)
python3 -c "
s='Khoor'
n=3
print(''.join(chr((ord(c)-65-n)%26+65) if c.isupper() else chr((ord(c)-97-n)%26+97) if c.islower() else c for c in s))"
# Hello (ROT3 decode)

# Brute force all 26 rotations
python3 -c "
s='Khoor'
for n in range(26):
    print(n, ''.join(chr((ord(c)-65-n)%26+65) if c.isupper() else chr((ord(c)-97-n)%26+97) if c.islower() else c for c in s))"
```

---

## Binary

```bash
# Text to binary
python3 -c "
s='Hi'
print(' '.join(format(ord(c),'08b') for c in s))"
# 01001000 01101001

# Binary to text
python3 -c "
b='01001000 01101001'
print(''.join(chr(int(x,2)) for x in b.split()))"
# Hi
```

---

## ASCII Decimal

```bash
# Text to decimal
python3 -c "print([ord(c) for c in 'Hello'])"
# [72, 101, 108, 108, 111]

# Decimal to text
python3 -c "print(''.join(chr(n) for n in [72,101,108,108,111]))"
# Hello
```

---

## XOR

```python
# XOR a byte with a key
python3 -c "print(bytes([b ^ 0x41 for b in b'secret']))"

# XOR with repeating key
def xor(data, key):
    return bytes([data[i] ^ key[i % len(key)] for i in range(len(data))])

# Brute force single-byte XOR (CTF)
ct = bytes.fromhex("1b37373331363f78151b7f2b783431333d78397828372d363c78373e783a393b3736")
for key in range(256):
    pt = bytes([b ^ key for b in ct])
    if all(chr(c) in ''.join([chr(i) for i in range(32,127)]) for c in pt):
        print(f"Key={key}: {pt.decode()}")
```

---

## JWT (JSON Web Token)

```bash
# Structure: header.payload.signature (each Base64URL-encoded)
# Decode without verification:
echo "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9" | base64 -d 2>/dev/null
# {"alg":"HS256","typ":"JWT"}

# Python — decode without verification
import jwt
decoded = jwt.decode(token, options={"verify_signature": False})

# Python — decode and verify
decoded = jwt.decode(token, "secret_key", algorithms=["HS256"])

# Common JWT attacks:
# 1. alg:none — remove signature, set algorithm to none
# 2. Weak secret — brute force with hashcat -m 16500
# 3. RS256 to HS256 — use public key as HMAC secret
# 4. Key confusion — if public key available, sign with it as HS256 secret
```

---

## Morse Code

```
A .-    B -...  C -.-.  D -..   E .     F ..-.
G --.   H ....  I ..    J .---  K -.-   L .-..
M --    N -.    O ---   P .--.  Q --.-  R .-.
S ...   T -     U ..-   V ...-  W .--   X -..-
Y -.--  Z --..
0 -----  1 .----  2 ..---  3 ...--  4 ....-
5 .....  6 -....  7 --...  8 ---..  9 ----.

# Online decoder: https://morsecode.world/international/decoder/audio-decoder-adaptive.html
```

---

## CyberChef Quick Reference

URL: **https://gchq.github.io/CyberChef/**

| Task | Recipe |
|---|---|
| Auto-detect encoding | Magic |
| From Base64 | From Base64 |
| From Hex | From Hex |
| From URL encode | URL Decode |
| XOR brute force | XOR Brute Force |
| JWT decode | JWT Decode |
| AES decrypt | AES Decrypt (need key+IV) |
| RSA decrypt | RSA Decrypt |
| Gzip decompress | Gunzip |
| Multi-step decode | Chain multiple operations |
| Entropy analysis | Entropy |

---

## Identify Unknown Encoding (Decision Tree)

```
All chars A-Za-z0-9+/= ?
  └─ Ends with = padding → Base64
  └─ No padding → Base64 or Base58 or Base32 check

Only A-Z2-7= ?
  └─ Base32

Only 0-9a-fA-F ?
  └─ Hex (length even = bytes)

Only 0-1 or with spaces?
  └─ Binary

Looks like gibberish letters?
  └─ ROT13, Vigenere, Atbash, Caesar → try CyberChef Magic

%XX sequences?
  └─ URL encoding

&amp; &#XX; ?
  └─ HTML encoding

Three dots-dashes?
  └─ Morse code

Header.Payload.Sig (three parts, dots)?
  └─ JWT

ALWAYS try: CyberChef → Magic recipe first
```

---

## Related Notes
- Cryptography-Basics
- CTF-Quick-Reference
- CTF-Methodology
- Python-for-Security
- Regex-Reference

---
<- [[Reference-MOC]]