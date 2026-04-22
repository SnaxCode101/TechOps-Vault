---
title: Reverse Engineering Basics
tags:
  - fundamentals
  - reverse-engineering
  - advanced
topic: Reverse Engineering
difficulty: advanced
created: 2026-03-26
updated: 2026-03-26
source: SANS FOR610, OpenSecurityTraining2, Intel 64 and IA-32 architecture developer manuals
---

# ⚙️ Reverse Engineering Basics

## Overview
Reverse engineering (RE) is the process of analyzing a compiled binary to understand its functionality without access to source code. Used for malware analysis, vulnerability research, CTF challenges, and understanding proprietary software. Core skills: reading assembly, using disassemblers/decompilers, and understanding program execution.

---

## Why Reverse Engineer?

- **Malware analysis** — Understand what malware does
- **Vulnerability research** — Find bugs in closed-source software
- **CTF challenges** — Crack license checks, find hidden flags
- **Interoperability** — Understand proprietary protocols/file formats
- **Exploit development** — Find gadgets for ROP chains

---

## Key Concepts

### Compilation Pipeline
```
Source (.c/.cpp) → Preprocessor → Compiler → Assembler → Linker → Executable
                                    ↓
                              Assembly (.asm) → Object file (.o) → Binary (.exe/.elf)
```

Reverse engineering works backwards: Binary → Disassembly → (Decompilation) → Pseudo-C

### File Formats
| Format | Platform | Notes |
|---|---|---|
| PE (Portable Executable) | Windows (.exe, .dll, .sys) | Has sections: .text, .data, .rsrc |
| ELF (Executable and Linkable Format) | Linux/Unix | Has segments and sections |
| Mach-O | macOS / iOS | Universal binary support |

### Magic Bytes (File Signatures)
```
PE:  4D 5A (MZ) — "This program cannot be run in DOS mode"
ELF: 7F 45 4C 46 (0x7F ELF)
PDF: 25 50 44 46 (%PDF)
ZIP: 50 4B 03 04 (PK)
```

---

## x86/x64 Assembly Fundamentals

### Registers (x86-64)
| Register | 64-bit | 32-bit | 16-bit | Purpose |
|---|---|---|---|---|
| Accumulator | RAX | EAX | AX | Return values, arithmetic |
| Base | RBX | EBX | BX | General purpose |
| Counter | RCX | ECX | CX | Loop counter, function arg 4 (Windows) |
| Data | RDX | EDX | DX | Data, function arg 3 (Windows) |
| Source Index | RSI | ESI | SI | Source pointer, arg 2 (Linux) |
| Dest Index | RDI | EDI | DI | Destination pointer, arg 1 (Linux) |
| Stack Pointer | RSP | ESP | SP | Top of stack |
| Base Pointer | RBP | EBP | BP | Stack frame base |
| Instruction Ptr | RIP | EIP | IP | Current instruction |
| Args | R8–R15 | — | — | Additional arguments |

### Calling Conventions
**Windows x64:** Arguments in RCX, RDX, R8, R9, then stack
**Linux x64 (System V AMD64):** Arguments in RDI, RSI, RDX, RCX, R8, R9, then stack
**Return value:** RAX (or RAX:RDX for 128-bit)

### Common Instructions
```asm
mov rax, 0x10     ; Move immediate value into register
mov rax, [rbx]    ; Load value from memory address in rbx into rax
push rax          ; Push rax onto stack (RSP -= 8)
pop rbx           ; Pop from stack into rbx (RSP += 8)

add rax, rbx      ; rax = rax + rbx
sub rax, 5        ; rax = rax - 5
mul rbx           ; rdx:rax = rax * rbx (unsigned)
imul rax, rbx     ; rax = rax * rbx (signed)
xor rax, rax      ; rax = 0 (common zero pattern)
and rax, 0xFF     ; Bitwise AND
or  rax, rbx      ; Bitwise OR
shl rax, 2        ; Shift left by 2 (multiply by 4)
shr rax, 1        ; Shift right by 1 (divide by 2)

cmp rax, rbx      ; Compare (sets flags, doesn't store result)
test rax, rax     ; AND without storing (test if zero)
jmp label         ; Unconditional jump
je  label         ; Jump if equal (ZF=1)
jne label         ; Jump if not equal
jl  label         ; Jump if less
jge label         ; Jump if >= 
call function     ; Push return address, jump to function
ret               ; Pop return address, jump to it
```

### Stack Frame (Function Call)
```asm
; Function prologue
push rbp
mov rbp, rsp
sub rsp, 0x20     ; Allocate local variables

; Function body
; Local vars at [rbp-8], [rbp-16], etc.
; Arguments at [rbp+16], [rbp+24], etc. (if on stack)

; Function epilogue  
mov rsp, rbp
pop rbp
ret
```

---

## Tools

### Disassemblers / Decompilers
| Tool | Type | Cost | Notes |
|---|---|---|---|
| Ghidra | Disassembler + decompiler | Free (NSA/open-source) | Excellent for beginners |
| IDA Pro | Disassembler + decompiler | Commercial ($$$) | Industry standard |
| IDA Free | Limited version | Free | 32/64-bit PE only |
| Binary Ninja | Disassembler + decompiler | Commercial (free tier) | Good API/scripting |
| Radare2 / Cutter | Disassembler | Free | CLI (r2) + GUI (Cutter) |
| RetDec | Decompiler | Free (online/CLI) | Retargetable decompiler |

### Debuggers
| Tool | Platform | Notes |
|---|---|---|
| x64dbg / x32dbg | Windows | Most popular; WinDbg alternative |
| GDB | Linux | GNU debugger; use with pwndbg/peda/gef plugins |
| WinDbg | Windows | Kernel debugging; MSFT official |
| OllyDbg | Windows 32-bit | Legacy; still used |
| pwndbg | Linux | GDB plugin for exploit dev |

### Other Useful Tools
```bash
# Identify binary
file binary
strings binary
xxd binary | head

# Linux dynamic analysis
strace ./binary    # System calls
ltrace ./binary    # Library calls
ldd binary         # Shared library dependencies

# PE analysis (Windows/Linux with wine-utils)
pestudio binary.exe
DIE (Detect It Easy) binary.exe   # Packer/compiler detection

# Identify packer
Exeinfo PE
PEiD
```

---

## Ghidra Quick Start

```
1. Install: Download from https://ghidra-sre.org, extract, run ghidraRun.bat/.sh
2. New Project → Import File → Select binary
3. Auto-analyze (accept defaults) → Double-click to open
4. CodeBrowser: Left = Symbol Tree, Center = Disassembly, Right = Decompiler
5. Press 'L' on a function/variable to rename
6. Press 'K' to change data type
7. Add comments with ';' or right-click
8. Search for strings: Search → For Strings
9. Find function cross-references: Right-click → References → Show References to
```

---

## CTF Reverse Engineering Approach

```
1. file binary          → Identify type, architecture, stripped/not
2. strings binary       → Find flag-like strings, interesting output
3. ltrace/strace        → See what syscalls/libs it uses
4. Run it               → What does it do? What input does it expect?
5. Open in Ghidra/IDA   → Find main(), input handling, comparison functions
6. Look for strcmp/memcmp → Check what your input is compared against
7. Patch or keygen      → Bypass the check or reproduce the key algorithm
```

### Common RE Patterns in CTFs
```asm
; License key check — compare input to hardcoded string
lea rdi, [input]
lea rsi, [hardcoded_key]
call strcmp
test eax, eax      ; If EAX=0, strings match
jne wrong          ; Jump if not equal → wrong key

; Anti-debugging — IsDebuggerPresent check
call IsDebuggerPresent
test eax, eax
jnz being_debugged  ; Patch this JNZ to JZ or NOP

; XOR decryption loop (obfuscation)
xor byte [rdi], 0x42   ; XOR each byte with 0x42
inc rdi
dec rcx
jnz loop
```

---

## Anti-Analysis Techniques

| Technique | Description | Bypass |
|---|---|---|
| Packing/compression | Binary encrypted/compressed at rest | Unpack: UPX -d, dump from memory |
| Anti-debugging | IsDebuggerPresent, timing checks | NOP/patch check; ScyllaHide plugin |
| Obfuscation | Junk instructions, opaque predicates | Manual cleanup; symbolic execution |
| Anti-VM | Detect sandbox/VM artifacts | Disable checks; use bare-metal |
| String encryption | XOR/RC4 encoded strings | Decode manually or with YARA/scripts |
| Control flow flattening | Replace if/else with switch dispatcher | Symbolic execution (angr, miasm) |

---

## Related Notes
- Malware-Analysis
- CTF-Methodology
- CTF-Quick-Reference
- Volatility

## Sources

National Security Agency. (2024). *Ghidra software reverse engineering framework*. NSA. https://ghidra-sre.org

OpenSecurityTraining2. (2024). *OpenSecurityTraining2: Free security courses*. OST2. https://ost2.fyi

SANS Institute. (2024). *FOR610: Reverse-engineering malware: Malware analysis tools and techniques*. SANS Institute.

Intel. (2024). *Intel 64 and IA-32 architectures software developer manuals*. Intel. https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html

---
<- [[Fundamentals-MOC]]