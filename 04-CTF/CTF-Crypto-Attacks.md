---
title: CTF Crypto Attacks
tags:
  - intermediate
  - CTF
  - crypto
  - RSA
  - AES
  - XOR
  - hash
  - cipher
  - cryptanalysis
topic: Cryptography CTF Deep Dive
difficulty: intermediate
created: 2026-03-28
updated: 2026-03-28
---

# 🔐 CTF Crypto Attacks

## Overview
Cryptography challenges in CTFs test your ability to identify weak implementations and exploit mathematical flaws. This note covers attack patterns for the most common CTF crypto categories: classical ciphers, RSA, AES/block ciphers, XOR, and hashing.

---

## Classical Ciphers

### Caesar / ROT-N
```bash
# Brute force all 26 rotations
python3 -c "
ct = 'Gur synt vf urer'
for i in range(26):
    print(f'ROT-{i}:', ct.translate(str.maketrans(
        'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz',
        chr(65+i)*0 + ''.join(chr((j+i)%26+65) for j in range(26)) +
        ''.join(chr((j+i)%26+97) for j in range(26))
    )))
"

# Or use CyberChef → ROT13 Brute Force
```

### Vigenere
```
# Identify: polyalphabetic, key word given or discoverable
# Attack: Kasiski examination (find repeated sequences → key length)
# Tool: https://www.dcode.fr/vigenere-cipher
# Python: pip install pycipher
```

### Substitution Cipher
```
# Frequency analysis — compare letter frequencies to English
# Most common English letters: E T A O I N S H R
# Tool: https://quipqiup.com/ (automated solver)
# Manual: map most frequent ciphertext letter → 'e', next → 't', etc.
```

### Transposition Cipher
```
# Letters are correct but reordered
# Try: Rail Fence, Columnar Transposition
# Tool: https://www.dcode.fr/rail-fence-cipher
```

---

## RSA Attacks

### RSA Fundamentals (Quick Reference)
```
Key Generation:
  p, q = large primes
  n = p × q
  φ(n) = (p-1)(q-1)
  e = public exponent (commonly 65537)
  d = e⁻¹ mod φ(n) — private exponent

Encrypt:  c = m^e mod n
Decrypt:  m = c^d mod n
```

### Attack: Small Public Exponent (e=3)
```python
# If m^e < n, ciphertext is just m^e (no modular reduction)
# Cube root of c gives plaintext
from gmpy2 import iroot
c = <ciphertext>
m, exact = iroot(c, 3)
if exact:
    print(bytes.fromhex(hex(int(m))[2:]))
```

### Attack: Factoring Small n
```python
# If n is small enough to factor
# Use factordb.com or yafu or msieve
# Python: sympy
from sympy import factorint
factors = factorint(n)   # Returns {p: 1, q: 1}

# Or: http://factordb.com/index.php?query=<n>
```

### Attack: Common Factor (Multiple Keys)
```python
# If two RSA keys share a prime factor
from math import gcd
common = gcd(n1, n2)
if common > 1:
    p = common
    q1 = n1 // p
    q2 = n2 // p
    # Now compute d and decrypt
```

### Attack: Wiener's (Small d)
```python
# When d is small relative to n^(1/4)
# Tool: pip install owiener
import owiener
d = owiener.attack(e, n)
if d:
    m = pow(c, d, n)
```

### Attack: Hastad's Broadcast (Same m, Different n, e=3)
```python
# Same message encrypted with e=3 to 3 different recipients
# Chinese Remainder Theorem → recover m^3 → cube root
from sympy.ntheory.modular import crt
remainders = [c1, c2, c3]
moduli = [n1, n2, n3]
result, _ = crt(moduli, remainders)
m, _ = iroot(result, 3)
```

### Attack: Common Modulus
```python
# Same n, same m, two different e values → recover m
# Extended GCD: find a, b such that e1*a + e2*b = 1
from gmpy2 import gcdext
g, a, b = gcdext(e1, e2)
# m = (c1^a * c2^b) mod n
m = (pow(c1, int(a), n) * pow(c2, int(b), n)) % n
```

### Attack: Fermat Factorization (p ≈ q)
```python
# When p and q are close together
from gmpy2 import isqrt, is_square
a = isqrt(n) + 1
while True:
    b2 = a*a - n
    if is_square(b2):
        b = isqrt(b2)
        p = a + b
        q = a - b
        break
    a += 1
```

### RsaCtfTool (Automated)
```bash
# Tries many attacks automatically
python3 RsaCtfTool.py --publickey key.pub --uncipher ciphertext.enc
python3 RsaCtfTool.py -n <n> -e <e> --uncipher <c>

# GitHub: https://github.com/RsaCtfTool/RsaCtfTool
```

---

## XOR Attacks

### Single-Byte XOR Brute Force
```python
data = bytes.fromhex('encrypted_hex_here')
for key in range(256):
    result = bytes(b ^ key for b in data)
    # Check if result looks like English/flag
    if b'flag{' in result or all(32 <= c < 127 for c in result):
        print(f"Key 0x{key:02x}: {result}")
```

### Repeating-Key XOR
```python
# Step 1: Find key length (Hamming distance method)
def hamming(a, b):
    return sum(bin(x ^ y).count('1') for x, y in zip(a, b))

ct = bytes.fromhex('...')
for keylen in range(2, 40):
    blocks = [ct[i:i+keylen] for i in range(0, len(ct), keylen)][:4]
    distances = []
    for i in range(len(blocks)):
        for j in range(i+1, len(blocks)):
            if len(blocks[i]) == len(blocks[j]):
                distances.append(hamming(blocks[i], blocks[j]) / keylen)
    if distances:
        print(f"Keylen {keylen}: avg distance {sum(distances)/len(distances):.2f}")
# Lowest normalized distance → likely key length

# Step 2: For each position, single-byte XOR brute force
# Step 3: Combine bytes → full key
```

### Known Plaintext XOR
```python
# If you know part of plaintext (e.g., flag format "flag{")
ct = bytes.fromhex('...')
known = b'flag{'
key_fragment = bytes(c ^ p for c, p in zip(ct, known))
print(f"Key starts with: {key_fragment}")
```

---

## Block Cipher Attacks

### ECB Mode Detection
```
# ECB encrypts identical blocks identically
# Send repeated plaintext → look for repeated ciphertext blocks
# If 16-byte blocks repeat in ciphertext → ECB mode

python3 -c "
ct = bytes.fromhex('...')
blocks = [ct[i:i+16] for i in range(0, len(ct), 16)]
if len(blocks) != len(set(blocks)):
    print('ECB detected — duplicate blocks found')
"
```

### ECB Cut-and-Paste
```
# Rearrange ciphertext blocks to change decrypted result
# Example: move "admin" block into role field
# Requires understanding block alignment
```

### CBC Bit-Flipping
```python
# Flip bits in ciphertext block N to control plaintext block N+1
# XOR the target byte in block N with (current_value ^ desired_value)
# c[i] ^= ord(current) ^ ord(desired)
```

### Padding Oracle Attack
```bash
# If server leaks padding validity (different error for bad padding vs bad data)
# Tool: PadBuster
padbuster http://target/decrypt.php ENCRYPTED_VALUE 16 -encoding 0

# Or: padding-oracle-attacker (Python)
```

---

## Hash Attacks

### Hash Length Extension
```bash
# If MAC = H(secret || message), attacker can compute H(secret || message || padding || extra)
# Tool: hash_extender or HashPump
hash_extender --data "original" --secret-length 16 --append "admin=true" --signature ORIG_HASH --format sha256
```

### Hash Collision (MD5)
```bash
# Generate two files with same MD5 but different content
# Tool: fastcoll (MD5 collision generator)
# Or use known collisions from MD5 collision databases
```

### Cracking Hashes
```bash
# hashcat
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt        # MD5
hashcat -m 100 hash.txt /usr/share/wordlists/rockyou.txt       # SHA1
hashcat -m 1400 hash.txt /usr/share/wordlists/rockyou.txt      # SHA256
hashcat -m 3200 hash.txt /usr/share/wordlists/rockyou.txt      # bcrypt

# john
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
john hash.txt --format=Raw-SHA256 --wordlist=rockyou.txt

# Online: crackstation.net, hashes.com
```

---

## Useful Crypto CTF Tools

| Tool | Purpose |
|---|---|
| RsaCtfTool | Automated RSA attacks |
| CyberChef | Encoding/cipher transformations |
| hashcat | GPU hash cracking |
| john | CPU hash cracking |
| SageMath | Mathematical crypto computations |
| factordb.com | Integer factorization database |
| dcode.fr | Classical cipher solvers |
| quipqiup.com | Substitution cipher solver |
| PadBuster | Padding oracle exploitation |
| hash_extender | Hash length extension attacks |
| gmpy2 | Python arbitrary-precision math |
| pycryptodome | Python crypto library |

---

## Python Crypto Libraries
```bash
pip install pycryptodome gmpy2 sympy owiener
```

```python
from Crypto.Util.number import long_to_bytes, bytes_to_long, inverse, getPrime
from Crypto.Cipher import AES
import gmpy2
from sympy import factorint
```

---

## Related Notes
- CTF-Methodology — General CTF approach
- CTF-Quick-Reference — Rapid decode/identify reference
- Cryptography-Basics — Crypto fundamentals
- Hashcat — Hash cracking reference
- John-the-Ripper — John usage

---
*Created: 2026-03-28*
---
<- [[CTF-MOC]]