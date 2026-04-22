---
title: SecOps-Vault Master Index & Search Reference
type: index
tags:
  - search
  - navigation
  - index
  - moc
  - master-index
updated: 2026-03-29
note_count: "148"
---

# 🔎 SecOps-Vault Master Index

> Use **Ctrl + Shift + F** (Obsidian global search) to find anything. This note exists as a Ctrl+F–searchable full keyword index. Every note title, tag, CVE, technique, tool flag, and acronym is listed here. **Also open `TechOps-Vault-Search.html` from your vault folder in any browser for a clickable search interface.**

---

## 01-Fundamentals (23 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[CIA-Triad]] | confidentiality integrity availability DAD triad Parkerian hexad least privilege non-repudiation |
| [[Networking-Basics]] | OSI TCP/IP subnetting CIDR NAT ARP routing switching VLANs |
| [[OSI-Model]] | physical data-link network transport session presentation application layers PDU encapsulation |
| [[TCP-IP-Fundamentals]] | TCP UDP handshake SYN ACK FIN RST IP headers TTL checksum |
| [[Cryptography-Basics]] | symmetric asymmetric AES RSA ECC DH ECDH hashing SHA-256 MD5 PKI TLS certificates |
| [[Active-Directory]] | AD LDAP Kerberos GPO OU domain forest trust NTLM DCSync Kerberoasting AS-REP |
| [[Linux-Privilege-Escalation]] | SUID SGID sudo cron PATH GTFOBins LinPEAS LD_PRELOAD capabilities |
| [[Windows-Privilege-Escalation]] | UAC token impersonation SeImpersonatePrivilege Potato WinPEAS unquoted service |
| [[OWASP-Top-10]] | injection XSS CSRF SSRF IDOR broken-auth insecure-design A01-A10 2021 |
| [[Web-Application-Testing]] | recon fuzzing directory-busting SQLi XSS SSRF LFI RFI authentication-bypass Burp |
| [[Penetration-Testing-Methodology]] | recon scanning enumeration exploitation post-exploitation reporting PTES rules-of-engagement |
| [[Incident-Response]] | PICERL preparation identification containment eradication recovery lessons-learned NIST 800-61 |
| [[Malware-Analysis]] | static dynamic sandbox PE-header strings YARA behavioral indicators IOC |
| [[Digital-Forensics]] | disk-imaging chain-of-custody timeline FTK Autopsy log-analysis memory-forensics DFIR |
| [[Cloud-Security-Fundamentals]] | AWS Azure GCP IAM S3 misconfig shared-responsibility CSPM CWPP cloud-attack |
| [[Social-Engineering]] | phishing spear-phishing vishing smishing pretexting OSINT human-factors awareness |
| [[Threat-Intelligence]] | CTI IOC IOA MITRE-ATT&CK threat-actor TTP STIX TAXII feeds |
| [[Vulnerability-Management]] | CVE CVSS NVD scanning patching remediation risk-acceptance lifecycle |
| [[Network-Defense-Blue-Team]] | IDS IPS SIEM SOC firewall WAF NDR EDR log-analysis detection-engineering |
| [[Reverse-Engineering-Basics]] | assembly x86 x64 disassembly Ghidra IDA strings decompilation obfuscation |
| [[OSINT-Methodology]] | OSINT open-source-intelligence recon Maltego Shodan theHarvester Google-dork passive-recon social-media |
| [[Wireless-Security]] | Wi-Fi WPA WPA2 WPA3 aircrack-ng deauth evil-twin 802.11 wireless handshake PMKID |
| [[Container-Docker-Security]] | Docker container Kubernetes pod image namespace cgroup seccomp AppArmor escape breakout |

---

## 02-Tools (20 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[Nmap]] | port-scan SYN stealth -sS -sV -O -A -p- NSE scripts -oA version-detection |
| [[Gobuster]] | directory-busting DNS-enum wordlist -w -u -t -x extensions vhosts |
| [[ffuf]] | fuzzing web FUZZ -w -u -H -fc -fs -fw -fl -mc virtual-host |
| [[Burp-Suite]] | proxy intercept repeater intruder scanner spider CSRF BApp extensions |
| [[Metasploit]] | msfconsole msfvenom exploit payload handler sessions meterpreter post-exploitation |
| [[Netcat]] | nc banner-grab file-transfer reverse-shell bind-shell listener relay |
| [[Hydra]] | brute-force SSH FTP HTTP login wordlist -l -L -p -P -t |
| [[John-the-Ripper]] | password-cracking hash wordlist rockyou john --format --wordlist --rules |
| [[Hashcat]] | GPU hash-cracking -m -a mask attack wordlist hybrid hashcat modes |
| [[SQLmap]] | SQL-injection automated -u --dbs --tables --dump --os-shell tamper |
| [[Wireshark]] | packet-capture display-filter BPF protocol-dissection tshark pcap follow-stream |
| [[BloodHound]] | AD-enumeration attack-paths SharpHound neo4j ACLs DCSync Kerberoasting graph |
| [[Impacket]] | secretsdump psexec wmiexec smbclient GetNPUsers GetSPN SMB RPC Python AD |
| [[CrackMapExec]] | CME smb winrm ldap pass-the-hash spray enum-users shares sessions crackmapexec |
| [[Volatility]] | memory-forensics RAM dump process network-connections malfind imageinfo vol.py vol3 |
| [[Nikto]] | web-scanner vulnerability headers misconfig CGI SSL nikto -h -p -o |
| [[Responder]] | LLMNR NBT-NS poisoning NTLMv2 capture relay hash responder.py -I |
| [[Evil-WinRM]] | WinRM PS-remoting lateral-movement evil-winrm -i -u -p -H pass-the-hash |
| [[Autopsy]] | disk-forensics image-analysis timeline keyword-search DFIR Sleuth-Kit |
| [[OpenVAS-Nessus]] | vulnerability-scanner OpenVAS Nessus GVM scan-policies authenticated-scan CVSS |

---

## 03-Labs (16 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[THM-Blue]] | EternalBlue MS17-010 SMB Windows-7 Metasploit exploit meterpreter hashdump |
| [[HTB-Lame]] | Samba CVE-2007-2447 distcc Linux usermap_script remote-code-execution |
| [[Custom-Lab-Nmap-Enumeration]] | Nmap lab enumeration port-scan service-version NSE custom network |
| [[HTB-Jerry]] | Tomcat default-creds WAR-deploy msfvenom manager reverse-shell Windows-Server |
| [[HTB-Nibbles]] | Nibbleblog CMS CVE-2015-6967 file-upload PHP phtml sudo NOPASSWD writable-script |
| [[HTB-Shocker]] | Shellshock CVE-2014-6271 CGI bash User-Agent header-injection sudo perl |
| [[HTB-Bashed]] | phpbash webshell dev-directory sudo scriptmanager cron Python reverse-shell |
| [[HTB-Netmon]] | PRTG CVE-2018-9276 anonymous-FTP config-backup default-creds notification-RCE monitoring |
| [[THM-Kenobi]] | Samba ProFTPd mod_copy CVE-2015-3306 NFS SSH-key SUID PATH-manipulation |
| [[THM-Ice]] | Icecast CVE-2004-1561 buffer-overflow token-impersonation SeImpersonatePrivilege Mimikatz kiwi |
| [[THM-Vulnversity]] | file-upload extension-bypass phtml systemctl SUID GTFOBins Gobuster non-standard-port |
| [[HTB-Optimum]] | Rejetto HFS CVE-2014-6287 HttpFileServer MS16-032 kernel-exploit Windows-Server-2012 |
| [[HTB-Bastard]] | Drupal Drupalgeddon2 CVE-2018-7600 IIS JuicyPotato MS15-051 SeImpersonatePrivilege token-impersonation |
| [[HTB-Devel]] | FTP anonymous-write IIS webroot ASPX webshell MS11-046 afd.sys kernel-exploit Windows-7 |
| [[THM-Steel-Mountain]] | Rejetto HFS CVE-2014-6287 unquoted-service-path PowerUp service-restart privilege-escalation |
| [[THM-Relevant]] | SMB anonymous-write IIS ASPX webshell PrintSpoofer SeImpersonatePrivilege Windows-Server-2016 |

---

## 04-CTF (6 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[CTF-Methodology]] | CTF recon enumeration exploitation web crypto forensics pwn reverse steganography flags |
| [[CTF-Quick-Reference]] | base64 ROT13 hex binary URL-encode magic-bytes file-signatures common-creds |
| [[CTF-Web-Exploitation]] | SQLi XSS SSRF SSTI LFI RFI deserialization JWT command-injection filter-bypass web-CTF |
| [[CTF-Crypto-Attacks]] | RSA XOR AES Caesar Vigenere hash-cracking padding-oracle Wiener Hastad block-cipher ECB CBC |
| [[CTF-Forensics-Techniques]] | PCAP Wireshark memory-forensics Volatility disk-forensics steganography LSB binwalk file-carving |
| [[CTF-Binary-Exploitation]] | pwn buffer-overflow ROP ret2libc format-string heap pwntools GDB shellcode NX PIE canary |

---

## 05-Certs (32 notes)

### CompTIA
| Note | Key Topics / Search Terms |
|---|---|
| [[Security-Plus-SY0-701]] | CompTIA Security+ SY0-701 domains threats vulnerabilities cryptography PKI network-security |
| [[CompTIA-Aplus-220-1201-1202]] | CompTIA A+ 220-1201 220-1202 hardware networking OS troubleshooting security operational |
| [[CySA-Plus-CS0-003]] | CySA+ CS0-003 blue-team threat-detection SOC SIEM incident-response analytics |

### Offensive / Practical
| Note | Key Topics / Search Terms |
|---|---|
| [[eJPT-INE]] | eJPT INE junior-penetration-tester recon exploitation web-app network assessment |
| [[OSCP-PEN-200]] | OSCP OffSec PEN-200 buffer-overflow exploitation AD active-directory PWK |
| [[CEH-v12]] | CEH v12 EC-Council ethical-hacker 20-domains footprinting scanning enumeration |

### ServiceNow — Core Certs
| Note | Key Topics / Search Terms |
|---|---|
| [[ServiceNow-CSA]] | CSA Certified-System-Administrator 6-domains Yokohama Zurich platform-overview user-interface collaboration lists filters |
| [[ServiceNow-CAD]] | CAD Certified-Application-Developer scripting GlideRecord business-rules client-scripts UI-policies ACL |
| [[ServiceNow-CTA]] | CTA Certified-Technical-Architect design-patterns integration performance scalability architecture |
| [[ServiceNow-CMA]] | CMA Certified-Master-Architect enterprise-architecture governance strategy complex-solutions |

### ServiceNow — Micro-Certs ✅ Earned
| Note | Key Topics / Search Terms |
|---|---|
| [[ServiceNow-Micro-Cert-Welcome]] | micro-cert welcome platform-overview navigation UI earned 2026-03-25 ✅ |
| [[ServiceNow-Micro-Cert-Flows]] | micro-cert Flow-Designer automation subflow trigger action earned 2026-03-26 ✅ |
| [[ServiceNow-Micro-Cert-Platform-Analytics]] | micro-cert Platform-Analytics PA dashboards indicators scores widgets earned 2026-03-26 ✅ |
| [[ServiceNow-Micro-Cert-ATF]] | micro-cert ATF Automated-Test-Framework test-suite assertions earned 2026-03-27 ✅ |
| [[ServiceNow-ATF-Micro-Cert]] | ATF study-guide test-steps parameterized-tests quick-start-tests server-side-testing |

### ServiceNow — CIS: ITSM & SecOps
| Note | Key Topics / Search Terms |
|---|---|
| [[ServiceNow-CIS-ITSM]] | CIS-ITSM IT-Service-Management incident problem change request SLA CMDB |
| [[ServiceNow-CIS-SIR]] | CIS-SIR Security-Incident-Response SOAR threat-intelligence sighting observable IOC playbook |
| [[ServiceNow-CIS-VR]] | CIS-VR Vulnerability-Response NVD CVE scanner-integration remediation risk-scoring |
| [[ServiceNow-CIS-DF]] | CIS-DF Data-Foundations CMDB CSDM Discovery IRE CI-identification reconciliation |
| [[ServiceNow-CIS-RC]] | CIS-RC Risk-Compliance GRC risk-assessment policy-management audit control-testing |
| [[ServiceNow-CIS-TPRM]] | CIS-TPRM Third-Party-Risk-Management vendor-risk assessment-questionnaire tiering |

### ServiceNow — CIS: ITOM
| Note | Key Topics / Search Terms |
|---|---|
| [[ServiceNow-CIS-Disco]] | CIS-Discovery ITOM discovery MID-server credentials CMDB service-mapping patterns |
| [[ServiceNow-CIS-SM]] | CIS-Service-Mapping ITOM service-map application-dependency topology business-service |
| [[ServiceNow-CIS-EM]] | CIS-Event-Management ITOM event alert operator-workspace correlation rules connectors |
| [[ServiceNow-CIS-PA]] | CIS-Performance-Analytics ITOM PA dashboards indicators breakdowns scores widgets |

### ServiceNow — CIS: ITAM
| Note | Key Topics / Search Terms |
|---|---|
| [[ServiceNow-CIS-HAM]] | CIS-HAM Hardware-Asset-Management lifecycle procurement stockroom disposal CMDB-asset |
| [[ServiceNow-CIS-SAM]] | CIS-SAM Software-Asset-Management license-compliance entitlement normalization reclamation |

### ServiceNow — CIS: Business Apps
| Note | Key Topics / Search Terms |
|---|---|
| [[ServiceNow-CIS-HR]] | CIS-HR Human-Resources HRSD case-management lifecycle-events employee-center COE |
| [[ServiceNow-CIS-CSM]] | CIS-CSM Customer-Service-Management case account contact product entitlement portal |
| [[ServiceNow-CIS-FSM]] | CIS-FSM Field-Service-Management work-order dispatch scheduling crew mobile agent |
| [[ServiceNow-CIS-SPM]] | CIS-SPM Strategic-Portfolio-Management project portfolio demand resource PPM |
| [[ServiceNow-CIS-SP]] | CIS-Service-Portal domain-separation multi-tenancy MSP portal widget theme CSS Angular |

---

## 06-Reference (18 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[Common-Ports]] | port 21 22 23 25 53 80 88 110 135 139 143 389 443 445 3306 3389 5985 |
| [[Linux-Commands]] | ls cd grep find chmod chown netstat ps kill awk sed curl wget ssh |
| [[Windows-Commands]] | ipconfig net user whoami icacls reg query schtasks tasklist wmic |
| [[Bash-Scripting]] | bash script shebang variables loops functions if-else heredoc pipe redirect |
| [[Python-for-Security]] | python3 socket requests scapy pwntools subprocess os sys CTF exploit-dev |
| [[File-Transfer-Techniques]] | wget curl scp python-http nc base64 certutil bitsadmin smb impacket |
| [[JavaScript-Fundamentals]] | JavaScript JS DOM XSS prototype closure async fetch API browser console |
| [[ServiceNow-Flow-Designer]] | Flow-Designer trigger action subflow integration spoke REST API ServiceNow |
| [[ServiceNow-JavaScript]] | ServiceNow GlideRecord GlideSystem GlideAjax client-script business-rule script-include server-side |
| [[ServiceNow-Navigator-Shortcuts]] | ServiceNow navigator filter favorites module keyboard shortcuts application-menu URL |
| [[System-Admin-Reference]] | sysadmin Windows Linux services logs permissions networking DNS DHCP |
| [[Reverse-Shell-Cheatsheet]] | reverse-shell bash nc python perl php ruby powershell msfvenom one-liner |
| [[PowerShell-Security]] | PowerShell AMSI bypass constrained-language-mode execution-policy Invoke-Expression encoded |
| [[AD-Attack-Reference]] | kerberoasting AS-REP DCSync pass-the-hash golden-ticket silver-ticket bloodhound ACL |
| [[Regex-Reference]] | regex regexp grep -E sed awk python re anchors quantifiers groups lookahead |
| [[Encoding-Decoding-Reference]] | base64 hex URL ROT13 binary ASCII UTF-8 JWT decode CyberChef |
| [[Vulnerability-Research-Reference]] | CVE NVD MITRE exploit-db searchsploit PoC patch advisory CVSS scoring |
| [[Database-Security-Reference]] | SQL database MySQL PostgreSQL MSSQL Oracle injection hardening permissions audit encryption |

---

## 07-Projects (2 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[Home-Lab-Setup]] | home-lab VirtualBox VMware Kali Parrot Metasploitable DVWA network-setup |
| [[Project-Template]] | project template scope objectives deliverables timeline tools findings |

---

## 08-Learning-Log (8 entries)

| Note | Key Topics / Search Terms |
|---|---|
| [[Learning-Log-Index]] | learning log index progress tracking daily entries |
| [[Learning-Log-2026-03-23]] | session 1 vault build fundamentals tools setup |
| [[Learning-Log-2026-03-23-S2]] | session 2 labs write-ups reference notes |
| [[Learning-Log-2026-03-26]] | session 3 audit expansion 74 notes search-tool CTF projects certs |
| [[Learning-Log-2026-03-27]] | session 4 Epic Healthcare-IT CSA-rebuild micro-certs audit 123 notes |
| [[Learning-Log-2026-03-27-S5]] | session 5 labs medical-devices TEFCA vault-search 139 notes |
| [[Learning-Log-2026-03-28-S6]] | session 6 linux-commands windows-commands active-directory sysadmin rewrites difficulty-tagging 150 notes |
| [[Learning-Log-2026-03-29-S7]] | session 7 difficulty-classification graph-view vault-map visualization batch-tagging 150 notes |

---

## 09-Epic EHR (12 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[Epic-Overview]] | Epic EHR EMR electronic-health-record modules Hyperspace Caboodle Chronicles MyChart ClinDoc |
| [[Epic-MyChart-Rules]] | MyChart patient-portal activation proxy-access ticket-scheduling messaging eVisits rules-engine |
| [[Epic-Session-Definitions-Limits]] | Epic session timeout concurrent-sessions SSO context-launch Hyperspace web |
| [[Epic-Reporting-Workbench-Slicer-Dicer]] | Reporting-Workbench Slicer-Dicer Radar dashboards Caboodle Clarity SQL Crystal-Reports |
| [[Epic-Content-Management]] | CMS content-management SmartText templates questionnaires documentation clinical-content |
| [[Epic-Department-Build-Facility-Structure]] | department facility location service-area ADT beds room-board revenue-location hierarchy |
| [[Epic-Patient-Self-Service]] | patient-self-service check-in kiosk eCheck-in Welcome MyChart scheduling questionnaire |
| [[Epic-Pools-Blocks-Modifiers]] | scheduling pools blocks modifiers templates provider-availability visit-type restriction |
| [[Epic-Questionnaire-Build]] | questionnaire smartform clinical-assessment screening branching-logic scoring flowsheet |
| [[Epic-Provider-Masterfile]] | provider-masterfile SER credentialing NPI taxonomy authorization coverage panel department |
| [[Epic-SmartText-SmartPhrases]] | SmartText SmartPhrase SmartLink wildcard dot-phrase template auto-text clinical-documentation |
| [[Epic-Clinic-Workflow]] | clinic-workflow ambulatory check-in rooming vitals orders encounter visit-navigator |

---

## 10-Healthcare IT (7 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[Healthcare-IT-Overview]] | healthcare-IT HIT health-informatics EHR EMR interoperability meaningful-use ONC HITECH |
| [[HIPAA-Technical-Safeguards]] | HIPAA 164.312 technical-safeguards access-control audit-controls integrity encryption transmission |
| [[HL7-FHIR-Fundamentals]] | FHIR R4 REST resource bundle patient observation SMART-on-FHIR JSON HL7 interoperability |
| [[HL7-v2-Messaging]] | HL7v2 ADT ORM ORU ACK MSH PID PV1 segments fields pipes interface-engine Mirth |
| [[Healthcare-IT-Integration-Patterns]] | integration HL7 FHIR API interface-engine Mirth Rhapsody EAI SOA ESB point-to-point hub-spoke |
| [[ServiceNow-Epic-Integration]] | ServiceNow Epic integration CMDB ITSM incident clinical-device MID-server bridge |
| [[TEFCA-Care-Coordination]] | TEFCA Trusted-Exchange QHINs USCDI information-blocking ADT care-coordination ONC interoperability |

---

## 11-Medical Devices (6 notes)

| Note | Key Topics / Search Terms |
|---|---|
| [[Medical-Devices-Overview]] | medical-device cybersecurity IoT OT healthcare clinical-engineering network-connected FDA patient-safety |
| [[FDA-Cybersecurity-Guidance]] | FDA premarket postmarket SBOM PATCH-Act MDS2 vulnerability-disclosure CycloneDX SPDX device-submission |
| [[Medical-Device-Network-Security]] | clinical-network segmentation VLAN NAC DICOM HL7 micro-segmentation IDS SPAN passive-monitoring vendor-remote-access |
| [[IoT-OT-Security-Fundamentals]] | IoT OT SCADA Purdue-model Modbus BACnet MQTT CoAP Zigbee BLE OWASP-IoT MITRE-ATT&CK-ICS IEC-62443 |
| [[Medical-Device-Risk-Management]] | IEC-80001 IEC-62443 NIST-CSF AAMI-TIR57 STRIDE threat-model risk-assessment compensating-controls clinical-criticality |
| [[Medical-Device-Incident-Response]] | medical-device IR patient-safety clinical-downtime ransomware containment FDA-MedWatch vendor-coordination forensics |

---

## Quick Tag Index

```
#fundamentals #tools #labs #ctf #certs #reference #projects #learning-log
#epic #healthcare-it #ehr #hipaa #hl7 #fhir
#medical-devices #iot #ot #fda #clinical-network #sbom
#recon #exploitation #post-exploitation #blue-team #forensics #malware
#web #network #crypto #active-directory #privilege-escalation #lateral-movement
#windows #linux #cloud #social-engineering #threat-intel #vuln-management
#servicenow #csa #cad #cta #cis #micro-cert #itom #itsm #secops
#cheat-sheet #methodology #framework #NIST #OWASP #MITRE #CVE #CVSS
#pwn #binary #buffer-overflow #ROP #format-string #heap #shellcode
#SQLi #XSS #SSRF #SSTI #LFI #deserialization #JWT
#PCAP #memory-forensics #disk-forensics #steganography #LSB
```

---

## How To Use This Note

- **In Obsidian:** `Ctrl + Shift + F` → search any keyword → click result
- **In this note:** `Ctrl + F` → search this page directly for quick lookup
- **Browser search:** Open `TechOps-Vault-Search.html` from vault folder for clickable card-based search
- **Graph view:** `Ctrl + G` — colour-coded by section, hub notes appear larger

---
*Master Index last updated: 2026-03-29 | 149 notes | Sections: Fundamentals · Tools · Labs · CTF · Certs · Reference · Projects · Learning-Log · Epic · Healthcare-IT · Medical-Devices*