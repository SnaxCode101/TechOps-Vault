---
title: Portfolio Site — Daily Operations Guide
tags:
  - portfolio
  - docker
  - operations
  - reference
topic: Portfolio Website
difficulty: foundational
created: 2026-04-05
updated: 2026-04-05
source: Personal build — S24
---

# Portfolio Site — Daily Operations Guide

> This note tells you exactly what to do every time you want your portfolio site to be accessible remotely. Follow it top to bottom.

---

## The Short Version (once you know what you're doing)

```powershell
G:
cd docker-projects\portfolio
docker compose up -d
docker logs portfolio-cloudflared
```

Grab the `trycloudflare.com` URL from the last command. Done.

---
- Changed an HTML/CSS/JS file → just hard refresh (`Ctrl+Shift+R`),  no Docker touch needed
- Changed `nginx.conf` → `docker compose restart nginx`
- Changed `docker-compose.yml` → `docker compose down && docker compose up -d`

---

## Full Step-by-Step — Every Time

### Step 1 — Open PowerShell

- Press `Windows key` → type `PowerShell` → click **Windows PowerShell**
- Do NOT use Command Prompt (cmd) — it won't recognize Docker commands

---

### Step 2 — Navigate to your project folder

Type these two commands, pressing Enter after each:

```powershell
G:
```
*(switches from C: drive to G: drive)*

```powershell
cd docker-projects\portfolio
```
*(navigates into your project folder)*

Your prompt should now show:
```
G:\docker-projects\portfolio>
```

---

### Step 3 — Check if the stack is already running

```powershell
docker ps
```

**What you're looking for:**

| If you see... | What it means | What to do |
|---------------|---------------|------------|
| Three containers listed (nginx, portainer, cloudflared) | Stack is already running | Skip to Step 5 |
| Empty table (no containers) | Stack is stopped | Continue to Step 4 |
| Some but not all three | Partial start | Run `docker compose down` then continue to Step 4 |

---

### Step 4 — Start the stack

```powershell
docker compose up -d
```

Wait for it to finish. You should see:
```
✔ Container portfolio-nginx       Started
✔ Container portfolio-portainer   Started
✔ Container portfolio-cloudflared Started
```

If you see errors — stop and troubleshoot before continuing (see Troubleshooting section below).

---

### Step 5 — Get your public URL

```powershell
docker logs portfolio-cloudflared
```

Scroll through the output and find a line that looks like this:
```
| https://some-words-here.trycloudflare.com |
```

**That is your public URL for this session.** Copy it.

> ⚠️ This URL changes every time you restart the stack. Always run this command to get the current one.

---

### Step 6 — Verify the site is accessible

Open a browser and paste the URL. You should see your portfolio site load over HTTPS.

To test remote access properly — open it on your **phone using mobile data** (not home WiFi). If it loads there, it's truly publicly accessible.

---

### Step 7 — Optional: Check Portainer

If you want to visually manage your containers:

```
http://localhost:9000
```

Log in with your Portainer credentials. You can see container status, logs, and restart individual containers here.

---

## When You're Done (Shutting Down)

If you want to stop the site from being accessible (e.g., going to bed, leaving home):

```powershell
docker compose down
```

This stops all three containers. The site will be offline until you run `docker compose up -d` again.

> **Note:** You do NOT have to shut down between sessions. `restart: unless-stopped` in the config means containers automatically restart if Docker Desktop restarts or your PC reboots — as long as Docker Desktop itself is set to launch at startup.

---

## Making Sure Docker Desktop Starts Automatically

If your PC reboots, Docker Desktop needs to be running before your containers can start.

**Check this setting once:**
1. Open Docker Desktop
2. Click the gear icon (Settings) top right
3. Go to **General**
4. Make sure **"Start Docker Desktop when you sign in to your computer"** is checked ✅

If this is enabled — after a reboot, Docker Desktop starts automatically, and all containers with `restart: unless-stopped` start automatically too. Your site comes back online on its own without you doing anything.

---

## Troubleshooting

### "Cannot connect to the Docker daemon"
Docker Desktop isn't running. Open Docker Desktop from the Start menu, wait for it to fully load (whale icon in system tray stops animating), then retry.

### "Port is already allocated"
Another process is using port 8080 or 9000. Run:
```powershell
docker compose down
docker compose up -d
```
If it persists, restart Docker Desktop.

### Site loads locally (localhost:8080) but not on trycloudflare.com URL
The cloudflared container may have lost connection. Run:
```powershell
docker restart portfolio-cloudflared
docker logs portfolio-cloudflared
```
Get the new URL from the logs.

### Forgot the public URL
```powershell
docker logs portfolio-cloudflared
```
The URL is always in there.

### Stack won't start / errors on `docker compose up -d`
Make sure you're in the right folder first:
```powershell
G:
cd docker-projects\portfolio
```
Then retry. If still failing, paste the error output and troubleshoot from there.

---

## Quick Reference Card

| What you want to do | Command |
|---------------------|---------|
| Navigate to project | `G:` then `cd docker-projects\portfolio` |
| Start everything | `docker compose up -d` |
| Stop everything | `docker compose down` |
| Check what's running | `docker ps` |
| Get public URL | `docker logs portfolio-cloudflared` |
| Restart one container | `docker restart portfolio-cloudflared` |
| Open Portainer | Browser → `http://localhost:9000` |
| View site locally | Browser → `http://localhost:8080` |

---

## Vault Sync → Site Deploy (Current Workflow — S33+)

The live site runs on **Cloudflare Pages** — push to GitHub and it auto-deploys in ~30 seconds.

### Step 1 — Sync TechOps Vault to site

```powershell
$exclude = @(".obsidian", ".claude", "Session-Reference", "IT-Home-Lab", "knowledge-base", "ABS", "Table of contents", "SecOps-Vault")
Get-ChildItem "G:\Obsidian\TechOps\" -Directory |
  Where-Object { $_.Name -notin $exclude } |
  ForEach-Object {
    $dest = "G:\docker-projects\portfolio\site\vault\$($_.Name)"
    if (-not (Test-Path $dest)) { New-Item -ItemType Directory -Path $dest | Out-Null }
    Copy-Item "$($_.FullName)\*" $dest -Recurse -Force
  }
```

### Step 2 — Regenerate manifest

```powershell
powershell -ExecutionPolicy Bypass -File "G:\docker-projects\portfolio\generate-manifest.ps1"
```

### Step 3 — Commit and push

```powershell
cd G:\docker-projects\portfolio\site
git add .
git commit -m "describe changes"
git push
# Cloudflare Pages auto-deploys in ~30 seconds
```

---

*Created: 2026-04-05 | Session 24 | Part of portfolio build project | Updated S33 — vault sync section added*

---
<- [[Projects-MOC]]