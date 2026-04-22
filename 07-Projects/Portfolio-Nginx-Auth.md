---
tags:
  - project
  - portfolio
  - cloudflare
  - auth
  - security
cssclasses: foundational
---

# Portfolio Site — Authentication Reference

Reference note for managing access control on `snaxcode.dev`.

> **Auth is Cloudflare Access — not Nginx.** Nginx runs locally only (`localhost:8081`). Everything on `snaxcode.dev` is protected by Cloudflare Access via email OTP. Manage it at dash.cloudflare.com — not Docker.

---

## How It Works

Cloudflare Access sits in front of the entire site. Anyone hitting `snaxcode.dev` must verify via **email OTP** sent to `Crotty101@comcast.net` before any page loads. Zero passwords, zero `.htpasswd` files.

---

## Current Access Map

| App | Protects | Auth |
|-----|----------|------|
| `snaxcode.dev` | Entire site `*` | Email OTP → Crotty101@comcast.net |
| `Resume` | `snaxcode.dev/Resume.html` | Email OTP |
| `Family` | `snaxcode.dev/family` | Email OTP |
| `Briana` | `snaxcode.dev/family/briana` | Email OTP |
| `Sophia` | `snaxcode.dev/family/sophia` | Email OTP |

---

## Managing Access (Cloudflare Dashboard)

All access policy changes are made at:
```
dash.cloudflare.com → Zero Trust → Access → Applications
```

**To add a new protected path:** Create a new Application, set the path, add an email OTP policy.

**To remove protection from a path:** Delete or disable the Application for that path.

**To change the allowed email:** Edit the policy on the relevant Application and update the email rule.

---

## How Visitors Authenticate

1. Visitor hits any page on `snaxcode.dev`
2. Cloudflare intercepts and shows a login prompt
3. Visitor enters their email address
4. Cloudflare sends a one-time code to that email
5. Visitor enters the code — granted access for the session

Only emails explicitly allowed in the policy can get in.

---

## Notes

- Session duration is controlled by Cloudflare Access settings (default ~24 hours)
- No credentials stored locally — everything managed in Cloudflare dashboard
- Nginx Basic Auth is no longer used on the live site — Nginx runs locally only

*Last updated: 2026-04-15 | Session 33 | Rewritten — auth migrated to Cloudflare Access (S31)*

---
<- [[Projects-MOC]]