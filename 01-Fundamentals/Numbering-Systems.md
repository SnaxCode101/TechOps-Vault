---
title: Numbering Systems
tags:
  - fundamentals
  - binary
  - octal
  - hexadecimal
  - numbering
  - foundational
topic: Computing Fundamentals
difficulty: foundational
created: 2026-04-19
updated: 2026-04-19
source: CompTIA (2022), Patterson & Hennessy (2020), The Open Group (2018), IEEE (2019)
---

# Numbering Systems

## Overview
Every piece of data a computer processes is ultimately represented as numbers. Humans use decimal (base 10) because we have ten fingers, but computers operate on electrical signals that are either on or off — making binary (base 2) the native language of hardware. Octal and hexadecimal exist as shorthand: they are compact, human-readable ways to represent binary without writing out every single bit.

## Why It Matters in IT and Security
Numbering systems appear constantly in real work:
- **Binary** — subnet masks, CPU registers, bitwise operations, permissions (chmod 755)
- **Octal** — Unix/Linux file permissions
- **Hex** — MAC addresses, IPv6, memory addresses, color codes, malware analysis, packet captures, cryptographic hashes

Fluency here is not optional. A misconfigured permission or a misread memory address can mean the difference between a secure system and a compromised one.

---

## Binary (Base 2)

Binary uses only two digits: **0** and **1**. Each digit is called a **bit**. Groups of 8 bits form a **byte**.

### Place Values
Each position in a binary number represents a power of 2, increasing right to left:

| Position | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|----------|---|---|---|---|---|---|---|---|
| Value    | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |

### How to Convert Binary → Decimal
Add up the place values wherever a **1** appears.

**Example:** `1011 0110`
- 128 + 32 + 16 + 4 + 2 = **182**

### How to Convert Decimal → Binary
Divide by 2 repeatedly, recording remainders bottom-up.

**Example:** 182 ÷ 2
```
182 ÷ 2 = 91  r 0
 91 ÷ 2 = 45  r 1
 45 ÷ 2 = 22  r 1
 22 ÷ 2 = 11  r 0
 11 ÷ 2 =  5  r 1
  5 ÷ 2 =  2  r 1
  2 ÷ 2 =  1  r 0
  1 ÷ 2 =  0  r 1
```
Read remainders bottom-up: **1011 0110** ✓

---

## Octal (Base 8)

Octal uses digits **0–7**. It was common in early computing and survives today primarily in Unix/Linux file permissions.

### How to Convert Octal → Decimal
Multiply each digit by its power of 8.

**Example:** `726` (octal)
- (7 × 64) + (2 × 8) + (6 × 1) = 448 + 16 + 6 = **470**

### Binary ↔ Octal Shortcut
Group binary digits in sets of **3** from the right — each group maps directly to one octal digit.

**Example:** `101 110 110`
- 101 = 5, 110 = 6, 110 = 6 → **566** (octal)

### Real-World Use — Linux Permissions
`chmod 755` sets:
- **7** = 111 binary = read + write + execute (owner)
- **5** = 101 binary = read + execute (group)
- **5** = 101 binary = read + execute (others)

---

## Hexadecimal (Base 16)

Hex uses **0–9** then **A–F** (where A=10, B=11, C=12, D=13, E=14, F=15). One hex digit represents exactly 4 binary bits (a **nibble**), making it the most efficient shorthand for binary.

### How to Convert Hex → Decimal
Multiply each digit by its power of 16.

**Example:** `2F` (hex)
- (2 × 16) + (15 × 1) = 32 + 15 = **47**

### Binary ↔ Hex Shortcut
Group binary digits in sets of **4** from the right — each group maps to one hex digit.

**Example:** `1011 1110`
- 1011 = B, 1110 = E → **BE** (hex)

### Real-World Use
- MAC addresses: `A4:C3:F0:85:AC:2D`
- IPv6: `fe80::1`
- Memory addresses in debuggers: `0x7fff5fbff5a0`
- Color codes in web/design: `#FF5733`

---

## Full Conversion Chart (0–15)

| Decimal | Binary | Octal | Hex |
|---------|--------|-------|-----|
| 0       | 0000   | 0     | 0   |
| 1       | 0001   | 1     | 1   |
| 2       | 0010   | 2     | 2   |
| 3       | 0011   | 3     | 3   |
| 4       | 0100   | 4     | 4   |
| 5       | 0101   | 5     | 5   |
| 6       | 0110   | 6     | 6   |
| 7       | 0111   | 7     | 7   |
| 8       | 1000   | 10    | 8   |
| 9       | 1001   | 11    | 9   |
| 10      | 1010   | 12    | A   |
| 11      | 1011   | 13    | B   |
| 12      | 1100   | 14    | C   |
| 13      | 1101   | 15    | D   |
| 14      | 1110   | 16    | E   |
| 15      | 1111   | 17    | F   |

---

## Quick Comparison

| System      | Base | Digits Used | Bits per Digit | Common Use |
|-------------|------|-------------|----------------|------------|
| Binary      | 2    | 0–1         | 1              | CPU, RAM, storage, networking |
| Octal       | 8    | 0–7         | 3              | Unix/Linux permissions |
| Hexadecimal | 16   | 0–9, A–F    | 4              | Addresses, MACs, hashes, color |
| Decimal     | 10   | 0–9         | ~3.32          | Human-readable output |

---

## Memory Tricks

- **Octal rolls over at 8** — if you see an 8 or 9, it's not octal.
- **Hex goes to F** — after 9, count A B C D E F before rolling to 10.
- **4 binary bits = 1 hex digit** — always. This is the most useful conversion shortcut you'll use.
- **3 binary bits = 1 octal digit** — same logic, less common today.

---

## Sources
- CompTIA. (2022). *CompTIA A+ core 1 exam objectives (220-1101)*. CompTIA.
- Patterson, D. A., & Hennessy, J. L. (2020). *Computer organization and design: ARM edition*. Morgan Kaufmann.
- The Open Group. (2018). *The Single UNIX specification, version 4*. The Open Group. https://pubs.opengroup.org/onlinepubs/9699919799/
- IEEE. (2019). *Standard for floating-point arithmetic* (IEEE Std 754-2019). IEEE.
