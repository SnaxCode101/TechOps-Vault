---
title: PowerShell Task Automation — Windows Task Scheduler
tags:
  - reference
  - powershell
  - automation
  - windows
topic: Reference
difficulty: foundational
created: 2026-04-15
updated: 2026-04-15
source: TechOps — S33
---

# PowerShell Task Automation — Windows Task Scheduler

> How to automate any PowerShell script using Windows Task Scheduler. No extra software needed — it's built into Windows. The vault sync workflow is used as the concrete example throughout.

---

## The Pattern

1. Write your logic as a `.ps1` script file
2. Register a Task Scheduler job to run it
3. Choose a trigger (schedule, login, unlock, etc.)
4. Run silently — no PowerShell window pops up

---

## Step 1 — Write the Script

Save your logic as a `.ps1` file — never a one-liner passed directly to Task Scheduler. Easier to edit, test, and AV-friendly.

**Example — vault sync script:**
Save as `G:\scripts\vault-sync.ps1`

```powershell
# vault-sync.ps1 — sync TechOps vault to portfolio site and deploy

$exclude = @(".obsidian", ".claude", "Session-Reference", "IT-Home-Lab", "knowledge-base", "ABS", "Table of contents", "SecOps-Vault")

Get-ChildItem "G:\Obsidian\TechOps\" -Directory |
  Where-Object { $_.Name -notin $exclude } |
  ForEach-Object {
    $dest = "G:\docker-projects\portfolio\site\vault\$($_.Name)"
    if (-not (Test-Path $dest)) { New-Item -ItemType Directory -Path $dest | Out-Null }
    Copy-Item "$($_.FullName)\*" $dest -Recurse -Force
  }

powershell -ExecutionPolicy Bypass -File "G:\docker-projects\portfolio\generate-manifest.ps1"

Set-Location "G:\docker-projects\portfolio\site"
git add .
git commit -m "Auto-sync $(Get-Date -Format 'yyyy-MM-dd HH:mm')"
git push
```

---

## Step 2 — Register the Task

Open PowerShell **as Administrator** and run:

```powershell
$action = New-ScheduledTaskAction `
  -Execute "powershell.exe" `
  -Argument "-ExecutionPolicy Bypass -WindowStyle Hidden -File G:\scripts\vault-sync.ps1"

$trigger = New-ScheduledTaskTrigger -Daily -At "9:00AM"

$settings = New-ScheduledTaskSettingsSet `
  -ExecutionTimeLimit (New-TimeSpan -Minutes 5) `
  -StartWhenAvailable

Register-ScheduledTask `
  -TaskName "VaultSync" `
  -Action $action `
  -Trigger $trigger `
  -Settings $settings `
  -RunLevel Highest
```

> `-WindowStyle Hidden` — runs silently with no visible PowerShell window.
> `-StartWhenAvailable` — if the machine was off at the scheduled time, runs it on next boot.

---

## Trigger Options

| Trigger | When it fires | Best for |
|---------|--------------|----------|
| `-Daily -At "9:00AM"` | Every day at 9am | Regular scheduled sync |
| `-AtLogOn` | Every time you log in | Always fresh on login |
| `-AtStartup` | On system boot | Services, not user scripts |
| Multiple triggers | Combine any of the above | Maximum coverage |

**Add a second trigger (e.g., also on login):**
```powershell
$trigger2 = New-ScheduledTaskTrigger -AtLogOn
Set-ScheduledTask -TaskName "VaultSync" -Trigger @($trigger, $trigger2)
```

---

## Managing the Task

```powershell
# Run it manually right now
Start-ScheduledTask -TaskName "VaultSync"

# Check last run result
Get-ScheduledTaskInfo -TaskName "VaultSync"

# Disable without deleting
Disable-ScheduledTask -TaskName "VaultSync"

# Re-enable
Enable-ScheduledTask -TaskName "VaultSync"

# Delete permanently
Unregister-ScheduledTask -TaskName "VaultSync" -Confirm:$false
```

---

## Verify Git Credentials First

Unattended `git push` requires stored credentials — Task Scheduler has no way to enter a password interactively. Verify credentials are cached before automating:

```powershell
cd G:\docker-projects\portfolio\site
git push
```

If it pushes without prompting for a password — you're good. If it prompts — log in once manually and Windows Credential Manager will cache it.

---

## Skip Commit If Nothing Changed

Prevents empty commits when the vault hasn't changed:

```powershell
git add .
$status = git status --porcelain
if ($status) {
  git commit -m "Auto-sync $(Get-Date -Format 'yyyy-MM-dd HH:mm')"
  git push
}
```

---

## Important Considerations

| Factor | Detail |
|--------|--------|
| **Machine must be on** | Task Scheduler only runs when the PC is awake. `-StartWhenAvailable` catches missed runs on next boot |
| **Cloudflare Pages limit** | 500 deploys/month on free tier. Daily = 30/month (fine). Hourly = 720/month (hits cap) |
| **AV (Norton)** | Keep logic in a `.ps1` file — not inline one-liners. Add `G:\scripts\` to Norton Auto-Protect exclusions if flagged |
| **Commit messages** | Auto-commits are generic (`Auto-sync 2026-04-15 09:00`) — no context. Acceptable for vault notes |
| **Git conflicts** | If you manually push while automation runs, git may reject the auto-push. Low risk with daily scheduling |

---

## Related
- Portfolio-Site-Daily-Operations — manual vault sync workflow
- PowerShell-Security — PowerShell execution policy and security reference

---
<- [[Reference-MOC]]