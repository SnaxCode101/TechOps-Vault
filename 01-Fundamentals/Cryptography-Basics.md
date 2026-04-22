---
title: Cryptography Basics
tags:
  - foundational
topic: Cryptography
difficulty: foundational
created: 2026-03-23
updated: 2026-04-04
source: NIST SP 800-175B, RFC 8446 (TLS 1.3), OWASP Cryptographic Storage Cheat Sheet
---

# Cryptography Basics

## Overview
Cryptography is the practice of securing information through mathematical techniques that control access, verify identity, and ensure data integrity. It is the backbone of secure communications, authentication systems, and data protection across all of computing.

## Why It Matters in Security
Every security practitioner encounters cryptography daily — in TLS/HTTPS, password hashing, digital signatures, VPNs, and more. Understanding cryptographic principles is required to evaluate whether implementations are secure, identify weaknesses, and understand attacks like SSL stripping, hash collision attacks, and padding oracle attacks.

---

## Core Details

### Goals of Cryptography (maps to CIA + extensions)
| Goal | Description |
|---|---|
| **Confidentiality** | Prevent unauthorized reading of data |
| **Integrity** | Detect unauthorized modification of data |
| **Authentication** | Verify the identity of a party |
| **Non-repudiation** | Prevent denial of having sent or signed data |

---

### Symmetric Encryption

One key is used to both **encrypt and decrypt**. Fast and efficient; the challenge is secure key exchange.

| Algorithm | Key Size | Block Size | Notes |
|---|---|---|---|
| AES-128 | 128-bit | 128-bit | Current standard; considered secure |
| AES-256 | 256-bit | 128-bit | Stronger; used where high assurance needed |
| 3DES | 112/168-bit | 64-bit | Legacy; deprecated, avoid |
| DES | 56-bit | 64-bit | Broken; do not use |
| ChaCha20 | 256-bit | Stream | Fast on mobile/low-power; used in TLS 1.3 |

**Modes of Operation (AES):**
| Mode | Notes |
|---|---|
| ECB | Insecure — identical plaintext blocks produce identical ciphertext blocks |
| CBC | Common; requires random IV; vulnerable to padding oracle attacks |
| CTR | Turns block cipher into stream cipher; parallelizable |
| GCM | Authenticated encryption (provides integrity + confidentiality); preferred |

**Security relevance:** Weak modes (ECB) or poor IV reuse in CBC are exploitable. GCM is the modern standard.

---

### Asymmetric Encryption (Public-Key Cryptography)

Two mathematically linked keys: a **public key** (share freely) and a **private key** (keep secret).
- Encrypt with public key → only private key can decrypt (confidentiality)
- Sign with private key → anyone with public key can verify (authentication/non-repudiation)

| Algorithm | Use Case | Notes |
|---|---|---|
| RSA | Encryption, signatures | 2048-bit minimum; 4096 preferred; slow |
| ECDSA | Digital signatures | Elliptic curve; smaller keys, faster than RSA |
| ECDH / ECDHE | Key exchange | Used in TLS; ECDHE provides forward secrecy |
| Diffie-Hellman | Key exchange | Allows two parties to establish shared secret over insecure channel |

**Forward Secrecy:** ECDHE and DHE generate a new session key per connection. Compromise of the server's private key does NOT decrypt past sessions.

---

### Hashing

A hash function maps data of any size to a fixed-size digest. Hashes are **one-way** — you cannot reverse a hash to recover the original input (without brute force).

Properties of a secure hash:
- **Deterministic:** Same input always produces same output
- **Preimage resistance:** Cannot recover input from hash
- **Collision resistance:** Cannot find two inputs with the same hash
- **Avalanche effect:** Small input change → drastically different output

| Algorithm | Output Size | Status |
|---|---|---|
| MD5 | 128-bit | **Broken** — collision attacks known; do not use for security |
| SHA-1 | 160-bit | **Deprecated** — collision demonstrated (SHAttered, 2017) |
| SHA-256 | 256-bit | Secure; current standard |
| SHA-512 | 512-bit | Secure; stronger margin |
| SHA-3 (Keccak) | Variable | Secure; different construction from SHA-2 |
| bcrypt | Variable | Password hashing; intentionally slow; includes salt |
| Argon2 | Variable | Password hashing; winner of Password Hashing Competition; preferred |

**Password hashing:** Never store plaintext passwords. Never use MD5 or unsalted SHA for passwords. Use bcrypt, Argon2, or scrypt.

---

### Salting
A **salt** is a random value added to input before hashing. It prevents:
- **Rainbow table attacks** — precomputed hash lookups
- **Identical passwords producing identical hashes** across users

Good password storage: `hash(password + unique_random_salt)` stored alongside the salt.

---

### Digital Signatures
A signature proves a message came from a specific sender and was not modified.

```
Signing:   hash(message) → encrypt with sender's private key = signature
Verifying: decrypt signature with sender's public key → compare to hash(message)
```

Used in: code signing, TLS certificates, email (DKIM), SSH keys.

---

### PKI and Certificates

**Public Key Infrastructure (PKI)** is the system for issuing, managing, and revoking digital certificates that bind public keys to identities.

| Component | Role |
|---|---|
| Certificate Authority (CA) | Trusted entity that signs certificates |
| Certificate (X.509) | Binds a public key to an identity; signed by CA |
| Root CA | Self-signed; the ultimate trust anchor |
| Intermediate CA | Signed by Root CA; issues end-entity certificates |
| CRL / OCSP | Certificate Revocation List / Online Certificate Status Protocol — check if cert is revoked |

**TLS Handshake (simplified TLS 1.3):**
```
Client → ClientHello (supported ciphers, key share)
Server → ServerHello (chosen cipher, key share, certificate)
Client → verifies certificate → derives session keys
Both → exchange Finished messages → encrypted data begins
```

---

### Common Cryptographic Attacks

| Attack | Target | Description |
|---|---|---|
| Brute Force | Any cipher/hash | Try all possible keys or inputs |
| Dictionary Attack | Passwords | Try words from a wordlist |
| Rainbow Table | Unsalted hashes | Precomputed hash → plaintext lookup |
| Padding Oracle | CBC mode | Decrypt ciphertext by manipulating padding and observing errors |
| BEAST / POODLE | SSL/TLS | Protocol-level attacks on old TLS versions |
| SSL Stripping | HTTPS | Downgrade HTTPS to HTTP via MITM |
| Birthday Attack | Hash functions | Find two inputs with the same hash using probability |
| Key Reuse | Stream ciphers / nonces | XOR two ciphertexts encrypted with same key → recover plaintext |
| Weak RNG | Key generation | Predictable randomness → keys can be guessed |

---

## Key Terms
| Term | Definition |
|---|---|
| Plaintext | Unencrypted data |
| Ciphertext | Encrypted data |
| Key | Secret value used to encrypt/decrypt |
| IV / Nonce | Initialization Vector — random value ensuring different ciphertext for same plaintext |
| Cipher Suite | A named combination of algorithms for key exchange, encryption, and MAC (e.g., `TLS_AES_256_GCM_SHA384`) |
| MAC | Message Authentication Code — keyed hash that verifies integrity and authenticity |
| HMAC | Hash-based MAC using a cryptographic hash function |
| Entropy | Measure of randomness; high entropy = harder to predict |

## Related Notes
- CIA-Triad
- OSI-Model
- TCP-IP-Fundamentals
- Burp-Suite

## Sources

National Institute of Standards and Technology. (2016). *Guideline for using cryptographic standards in the federal government* (NIST SP 800-175B). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-175B.pdf

Internet Engineering Task Force. (2018). *The transport layer security (TLS) protocol version 1.3* (RFC 8446). IETF. https://www.rfc-editor.org/rfc/rfc8446

OWASP Foundation. (2024). *Cryptographic storage cheat sheet*. OWASP. https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html


## See Also

- John-the-Ripper
- Hashcat
- Hydra
- CIA-Triad

---
<- [[Fundamentals-MOC]]