---
title: Cloud Security Fundamentals
tags:
  - fundamentals
  - cloud
  - AWS
  - Azure
  - GCP
  - IAM
  - cloud-security
  - intermediate
topic: Cloud Security
difficulty: intermediate
created: 2026-03-26
updated: 2026-03-26
source: CSA cloud security guidance, NIST SP 800-144, AWS security docs, MITRE ATT&CK cloud matrix
---

# ☁️ Cloud Security Fundamentals

## Overview
Cloud security encompasses the policies, technologies, applications, and controls used to protect cloud-based infrastructure, data, and services. As organizations migrate to AWS, Azure, and GCP, attackers follow — cloud misconfigurations are now a primary attack surface.

---

## Shared Responsibility Model

> **Critical concept:** Security responsibilities are divided between the cloud provider and the customer.

| Layer | AWS / Azure / GCP Responsible | Customer Responsible |
|---|---|---|
| Physical | ✅ Datacenters, hardware, network | ❌ |
| Hypervisor / virtualization | ✅ | ❌ |
| Guest OS (IaaS) | ❌ | ✅ Patching, hardening |
| Applications | ❌ | ✅ Code, dependencies |
| Identity (IAM) | Platform provides service | ✅ Configuration, policy |
| Data | ❌ | ✅ Encryption, access |
| Network controls | Platform provides | ✅ Security groups, NACLs |

**IaaS:** Customer manages OS, apps, data → most responsibility
**PaaS:** Provider manages OS/runtime → customer manages apps/data
**SaaS:** Provider manages everything → customer manages access/data

---

## Identity and Access Management (IAM)

IAM misconfigurations are the #1 cloud attack vector (Gartner, CSA).

### AWS IAM Key Concepts
```
Users     — Long-term credentials (avoid for production; use roles)
Groups    — Collection of users with shared permissions
Roles     — Assumed by services/users; temporary credentials via STS
Policies  — JSON documents defining Allow/Deny permissions

Principle of Least Privilege: Grant only minimum required permissions.

Dangerous permissions to audit:
  iam:CreateUser, iam:AttachUserPolicy, iam:PassRole
  s3:GetObject on sensitive buckets (world-readable)
  ec2:*  (admin-equivalent)
  iam:* (full IAM control = account takeover)
```

### AWS Credential Chain
```
1. Environment variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
2. ~/.aws/credentials
3. IAM Role (Instance Metadata Service: http://169.254.169.254/latest/meta-data/)
4. Container credential provider
```

### SSRF → IMDSv1 Attack
```bash
# If an app is vulnerable to SSRF, attacker can steal IAM role credentials
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME
# Returns: AccessKeyId, SecretAccessKey, Token → full API access as that role
# Mitigation: Enforce IMDSv2 (requires PUT request with token)
```

---

## Common Cloud Misconfigurations

### S3 Bucket Exposure (AWS)
```bash
# Publicly accessible bucket — critical data exposure
aws s3 ls s3://bucket-name --no-sign-request
aws s3 cp s3://bucket-name/sensitive.txt . --no-sign-request

# Check bucket policy
aws s3api get-bucket-policy --bucket bucket-name

# Check ACL
aws s3api get-bucket-acl --bucket bucket-name
```

**Fix:** Block all public access at account level. Use bucket policies with explicit Deny.

### Azure Blob Storage Exposure
```
Publicly accessible containers can be enumerated via:
https://storageaccount.blob.core.windows.net/containername/?restype=container&comp=list

Fix: Set container access to Private. Use Shared Access Signatures (SAS) with expiry.
```

### Overprivileged IAM Roles
- EC2 instance with `AdministratorAccess` policy
- Lambda function with `iam:*` permissions
- Cross-account trust with no condition

### Security Group Misconfigs
```
0.0.0.0/0 inbound on port 22 (SSH)     → brute-force exposure
0.0.0.0/0 inbound on port 3389 (RDP)   → brute-force exposure
0.0.0.0/0 inbound on port 5985 (WinRM) → lateral movement target
All ports open (0-65535)               → excessive exposure
```

---

## Cloud Attack Techniques (MITRE ATT&CK Cloud)

| Technique | Description |
|---|---|
| T1552.005 | Cloud Instance Metadata API (SSRF → IMDSv1 credential theft) |
| T1530 | Data from Cloud Storage (S3, Azure Blob enumeration) |
| T1578 | Modify Cloud Compute Infrastructure (rogue instances) |
| T1537 | Transfer Data to Cloud Account (exfiltration) |
| T1136.003 | Create Cloud Account (persistence via new IAM user) |
| T1098.001 | Add credentials to existing account |
| T1580 | Cloud Infrastructure Discovery |

---

## Cloud Security Services & Tools

### AWS Native Security Services
| Service | Purpose |
|---|---|
| IAM Access Analyzer | Finds resources shared with external principals |
| GuardDuty | Threat detection (CloudTrail, VPC Flow, DNS logs) |
| Security Hub | Centralized security findings |
| CloudTrail | API activity logging — critical for forensics |
| Config | Configuration compliance monitoring |
| Macie | S3 data classification and PII detection |
| Inspector | EC2/container vulnerability scanning |
| WAF | Web application firewall |

### Azure Security Services
| Service | Purpose |
|---|---|
| Microsoft Defender for Cloud | CSPM + workload protection |
| Sentinel | SIEM / SOAR |
| Azure AD / Entra ID | Identity management |
| Key Vault | Secrets management |
| Activity Log | API activity logging |

### Third-Party Tools
| Tool | Purpose |
|---|---|
| ScoutSuite | Multi-cloud security posture assessment |
| Prowler | AWS/Azure/GCP security auditing |
| PMapper | IAM privilege escalation path mapping |
| CloudFox | Cloud attack surface enumeration |
| Pacu | AWS exploitation framework |
| Cloudsplaining | IAM policy analysis |

---

## Key Audit Commands (AWS CLI)

```bash
# List all IAM users
aws iam list-users

# List IAM policies attached to a user
aws iam list-attached-user-policies --user-name USERNAME

# List S3 buckets
aws s3 ls

# Check CloudTrail status
aws cloudtrail describe-trails
aws cloudtrail get-trail-status --name TRAIL_NAME

# List running EC2 instances
aws ec2 describe-instances --query 'Reservations[].Instances[].[InstanceId,PublicIpAddress,State.Name]' --output table

# Check security groups
aws ec2 describe-security-groups
```

---

## Cloud Security Best Practices

1. **Enforce MFA** on all accounts, especially root/admin
2. **Delete root access keys** — never use root for day-to-day operations
3. **Use IAM roles** instead of long-term access keys
4. **Enable CloudTrail** in all regions
5. **Block S3 public access** at the account level
6. **Enable GuardDuty** for threat detection
7. **Enforce IMDSv2** to prevent SSRF → credential theft
8. **Tag and audit** all resources for ownership
9. **Rotate credentials** and audit unused access keys (>90 days)
10. **Enable VPC Flow Logs** for network visibility

---

## Related Notes
- Active-Directory
- Vulnerability-Management
- Incident-Response
- Network-Defense-Blue-Team
- Threat-Intelligence

## Sources

Cloud Security Alliance. (2023). *Cloud controls matrix and cloud security guidance*. CSA. https://cloudsecurityalliance.org

National Institute of Standards and Technology. (2011). *Guidelines on security and privacy in public cloud computing* (NIST SP 800-144). U.S. Department of Commerce. https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-144.pdf

MITRE. (2024). *MITRE ATT&CK®: Cloud matrix*. MITRE Corporation. https://attack.mitre.org/matrices/enterprise/cloud/

Amazon Web Services. (2024). *AWS security documentation*. Amazon Web Services. https://docs.aws.amazon.com/security/

---
<- [[Fundamentals-MOC]]