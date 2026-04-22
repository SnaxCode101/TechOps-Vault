---
title: BloodHound
tags: '["secops", "tools", "active-directory", "recon", "privilege-escalation",
  "red-team", "intermediate"]'
created: 2026-03-23
folder: 02-Tools
status: complete
difficulty: intermediate
---

# BloodHound

> BloodHound uses graph theory to reveal the hidden and often unintended relationships within an Active Directory environment. It maps attack paths from any compromised user to Domain Admin, showing exactly how privilege escalation can occur.

---

## What BloodHound Does

BloodHound ingests Active Directory data collected by **SharpHound** (or BloodHound.py for Linux) and visualises it as a graph database (Neo4j). It answers the critical question:

> "Given that I have compromised this account — what is the shortest path to Domain Admin?"

It identifies:
- Attack paths to high-value targets (Domain Admins, Enterprise Admins)
- Kerberoastable accounts
- AS-REP roastable accounts
- DCSync rights
- Unconstrained/constrained delegation
- Local admin rights across the domain
- ACL-based attack paths (WriteDACL, GenericAll, GenericWrite, etc.)

---

## Architecture

```
SharpHound (data collector)
        ↓
   JSON/ZIP files
        ↓
   BloodHound UI
        ↓
   Neo4j graph DB
```

- **SharpHound** — C# ingestor, runs on Windows inside the domain
- **BloodHound.py** — Python ingestor, runs from Linux with valid credentials
- **Neo4j** — graph database storing relationships
- **BloodHound GUI** — Electron app for querying and visualising the graph

---

## Installation

### BloodHound CE (Community Edition — current)
```bash
# Docker install (recommended)
git clone https://github.com/SpecterOps/BloodHound.git
cd BloodHound
docker compose up -d
# Access at http://localhost:8080
# Default creds: admin / check docker logs for generated password
```

### Legacy BloodHound (still widely used in labs/CTFs)
```bash
# Install Neo4j
sudo apt install neo4j
sudo neo4j start
# Browse to http://localhost:7474 — change default password (neo4j/neo4j)

# Install BloodHound
sudo apt install bloodhound
bloodhound
```

---

## Data Collection with SharpHound

### On Windows (domain-joined machine)
```powershell
# Download SharpHound
# https://github.com/BloodHoundAD/SharpHound

# Basic collection (all methods)
.\SharpHound.exe -c All

# Specific collection methods
.\SharpHound.exe -c DCOnly          # DC-only, less noise
.\SharpHound.exe -c Session         # Logged-on sessions only
.\SharpHound.exe -c ACL             # ACLs only

# Stealth collection (slower, less noise)
.\SharpHound.exe -c All --Stealth

# Output to specific directory
.\SharpHound.exe -c All --OutputDirectory C:\Temp\

# With specific domain controller
.\SharpHound.exe -c All --DomainController DC01.corp.local
```

### From Linux (BloodHound.py)
```bash
# Install
pip install bloodhound

# Collect with credentials
bloodhound-python -u username -p password -d corp.local -ns 192.168.1.10 -c All

# With NTLM hash (pass-the-hash)
bloodhound-python -u username --hashes :NTLMhash -d corp.local -ns DC_IP -c All

# Output: JSON files ready to import
```

---

## Importing Data

1. Open BloodHound GUI
2. Click **Upload Data** (top right, or drag and drop)
3. Select the ZIP file (or individual JSON files) from SharpHound
4. Wait for import to complete — check node/edge counts

---

## Key Queries (Pre-Built)

Access via the **Queries** panel in the BloodHound GUI.

### High Value Targets
- **Find all Domain Admins** — shows all DA group members
- **Find Shortest Paths to Domain Admins** — critical attack path query
- **Find Principals with DCSync Rights** — who can dump NTDS
- **Find Computers with Unsupported Operating Systems** — legacy/vulnerable hosts

### Kerberos Attacks
- **List all Kerberoastable Accounts** — SPNs set on user accounts
- **Find AS-REP Roastable Users** — accounts with pre-auth disabled
- **Shortest Paths to Domain Admins from Kerberoastable Users**

### Delegation
- **Find Computers with Unconstrained Delegation** — high-value targets
- **Find Computers with Constrained Delegation**

### ACL Abuse
- **Find Principals with DCSync Rights**
- **Find Interesting Domain ACLs**
- **Shortest Paths from Owned Principals**

---

## Custom Cypher Queries

BloodHound uses **Cypher** (Neo4j query language) for custom queries.

```cypher
// Find all users with GenericAll over a group
MATCH (u:User)-[:GenericAll]->(g:Group) RETURN u.name, g.name

// Find users with path to DA via ACL abuse
MATCH p=shortestPath((u:User)-[r:GenericAll|WriteDACL|WriteOwner|GenericWrite*1..]->(da:Group {name:"DOMAIN ADMINS@CORP.LOCAL"}))
RETURN p

// Find all computers where Domain Users are local admins
MATCH (u:Group {name:"DOMAIN USERS@CORP.LOCAL"})-[:AdminTo]->(c:Computer) RETURN c.name

// Find users with local admin rights on any computer
MATCH (u:User)-[:AdminTo]->(c:Computer) RETURN u.name, c.name

// Shortest path from owned user to DA
MATCH p=shortestPath((u:User {owned:true})-[*1..]->(da:Group {name:"DOMAIN ADMINS@CORP.LOCAL"}))
RETURN p
```

---

## Key Attack Paths to Know

### GenericAll / GenericWrite
Full control or write access over an object — can reset passwords, add to groups, add SPNs.

### WriteDACL
Can modify the ACL of an object — grant yourself GenericAll.

### WriteOwner
Can take ownership — then modify ACL.

### ForceChangePassword
Can reset a user's password without knowing the current one.

### AddMember
Can add members to a group — add yourself to Domain Admins.

### DCSync (GetChangesAll)
Can request domain replication — effectively dumps all hashes from NTDS.dit.

### Unconstrained Delegation
Any computer with this set can impersonate any user that authenticates to it — including Domain Admins.

---

## Marking Owned Nodes

When you compromise an account or computer:
1. Search for the object in BloodHound
2. Right-click → **Mark as Owned**
3. Re-run "Shortest Paths from Owned Principals" to see new attack paths

---

## Common Workflow in a Pentest

```
1. Gain initial foothold (low-priv user)
2. Run SharpHound / BloodHound.py
3. Import data into BloodHound
4. Mark initial foothold as Owned
5. Run "Shortest Paths to DA from Owned Principals"
6. Identify attack path (Kerberoast → ACL abuse → DA)
7. Execute attack path step by step
8. Re-mark newly owned objects
9. Repeat until Domain Admin achieved
```

---

## Defensive Use (Blue Team)

BloodHound is equally valuable for defenders:
- Identify and remove unnecessary ACL privileges
- Find and fix Kerberoastable accounts (use long random passwords or MSAs)
- Identify admin accounts used for daily tasks (tiering model)
- Detect SharpHound collection via event logs (LDAP queries, large Kerberos ticket requests)

---

## Related Notes
- Active-Directory
- Windows-Privilege-Escalation
- Penetration-Testing-Methodology
- Netcat


## See Also

- Active-Directory
- Windows-Privilege-Escalation
- Penetration-Testing-Methodology
- Metasploit

---
<- [[Tools-MOC]]