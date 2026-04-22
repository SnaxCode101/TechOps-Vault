---
title: Claude Code
tags:
  - tool
  - ai
  - workflow
  - productivity
topic: Tools
difficulty: foundational
created: 2026-04-14
updated: 2026-04-15 S33
source: Anthropic — Claude Code CLI
---

# Claude Code

## What Is It?
Claude Code is Anthropic's official AI assistant CLI (command line interface). It runs directly in your terminal, has access to your files, and remembers context between sessions via memory files.

---

## Launch It

```powershell
claude
```

That's it. Run it from the right directory and it automatically loads the right context.

---

## The Directory Trick — Most Important Thing to Know

> [!important] Always launch from the right folder
> Claude Code ties memory and context to **whatever directory you launch from**.
> Wrong directory = wrong context. Always `cd` first, then `claude`.

```powershell
# Home Lab sessions (VMs, networking, exploits)
cd G:\VirtualBoxVMs
claude

# Website / Portfolio sessions (snaxcode.dev, Cloudflare, Nginx)
cd G:\docker-projects\portfolio
claude

# TechOps Vault sessions — notes, certs, tools, home lab docs
cd "G:\Obsidian\TechOps"
claude

# ABS project sessions
cd "G:\Obsidian\TechOps\ABS"
claude

# Knowledge Base sessions (AI/agent projects)
cd "G:\Obsidian\TechOps\knowledge-base"
claude

# Docker projects (general)
cd G:\docker-projects
claude

# Home Life vault (personal, recipes, calendar)
cd "G:\Obsidian\Home Life"
claude
```

Each project stays in its own lane — no mixing context.

---

## Useful Commands (Inside Claude Code)

| Command | What It Does |
|---------|-------------|
| `/help` | Show all available commands |
| `/clear` | Clear the current conversation context (fresh start, keeps memory) |
| `/memory` | View what Claude remembers about you and your projects |
| `/cost` | Show token usage for the current session |
| `Ctrl+C` | Cancel current response |
| `exit` | Close Claude Code |

---

## Quick One-Liner Mode (No Interactive Session)

```powershell
claude "explain what pfSense is in simple terms"
```

Runs a single prompt and exits — useful for quick questions without opening a full session.

---

## How Memory Works

> [!warning] Claude has ZERO memory between sessions without these files
> If it's not saved to a memory file or session reference — it's gone next session.

- Memory lives at: `C:\Users\Crott\.claude\projects\[project]\memory\`
- TechOps vault memory: `C:\Users\Crott\.claude\projects\G--Obsidian-TechOps\memory\`
- Automatically loaded at session start based on working directory
- Types: `user` (your profile), `feedback` (your preferences), `project` (current work state), `reference` (where to find things)
- Always update your **Obsidian session reference** + ask Claude to update memory at end of session

---

## Session Workflow (Best Practice)

> [!tip] Follow this every single session — this is the whole system

```
1. Open PowerShell
2. cd to the right project folder
3. claude
4. Upload your session reference file
5. State your goal for the session
6. Work
7. End of session — ask Claude to update session reference + memory
```

---

## How to "Upload" a Session Reference File

> [!tip] You don't actually upload anything — you just tell Claude to read it

In the terminal there's no drag-and-drop. Instead just type this at the start of your session:

```
Please read my session reference file at:
G:\Obsidian\TechOps\Session-Reference\Session-Reference(New).md
```

Or for the Home Lab:
```
Please read my session reference file at:
G:\Obsidian\TechOps\IT-Home-Lab\Session-Reference.md
```

Claude will read it and instantly have full context. That's it — no uploading needed.

---

## Tips

- **Be specific** — the more context you give upfront, the better the output
- **One goal per session** — focused sessions get better results than scattered ones
- **Always confirm before destructive actions** — Claude will ask, but you can always say no

---

## Related
- Session-Reference(New) — TechOps project master reference
- IT-Home-Lab/Session-Reference — Home lab session reference

---
<- [[Tools-MOC]]