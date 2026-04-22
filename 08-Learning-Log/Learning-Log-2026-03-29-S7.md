---
title: "Learning Log — 2026-03-29 Session 7"
type: learning-log
date: 2026-03-29
session: 7
tags: [learning-log, session-7, difficulty-classification, graph-view, visualization]
difficulty: foundational
---

# Learning Log — Session 7

**Date:** 2026-03-29
**Session:** 7

---

## What I Worked On

### Obsidian Graph View Research
- Deep-dived into Obsidian's graph view Groups system
- Learned all query operators: `path:`, `file:`, `tag:`, `[property:value]`, `line:`, `section:`, `content:`
- Discovered that groups are purely cosmetic (color only) — no spatial clustering, no node shapes
- Property search uses square bracket syntax `[difficulty:foundational]` not `property:value`
- Groups evaluate top-to-bottom, first match wins
- Mobile drag-to-reorder groups is a known bug

### Native Graph Group Configuration
- Designed 12-color scheme for all vault sections using `path:` queries
- Each section gets a distinct color for instant visual identification in graph view

### Custom Interactive Vault Map (HTML/D3.js)
- Built a full custom visualization with D3.js force-directed graph
- 12 cluster hubs (large saturated circles) with file nodes (lighter shades) pulled toward their parent
- Features: search, legend filtering, hover tooltips, zoom/pan, drag nodes
- Lives as HTML file openable alongside vault

### Batch Difficulty Classification — All 119 Notes
- Read and classified every note across all 9 classifiable sections
- Applied standardized `difficulty:` frontmatter values:
  - `foundational` (~52 notes) — Day-1 sysadmin, entry-level
  - `intermediate` (~62 notes) — Working sysadmin 1-3 years
  - `advanced` (~5 notes) — Senior/specialist
- Fixed inconsistent legacy values (`Beginner`, `Easy`, `Beginner-Intermediate`)
- Discovered `obsidian_patch_content` silently fails when frontmatter field doesn't exist — used delete/rewrite approach as workaround
- Skipped MOC notes (5), Learning Logs (7), Projects (2) — meta-content, not classified

### Dashboard + Vault-Search Quick Fixes
- Updated note count 148→149 in Dashboard tagline, stats table, footer
- Added Learning-Log-2026-03-28-S6 entry to both Dashboard and Vault-Search
- Updated dates to 2026-03-29

---

## Key Takeaways

1. **Obsidian graph view is limited by design** — force-directed physics means no folder-based clustering. Custom visualizations fill the gap.
2. **`[property:value]` syntax is powerful** — enables graph grouping by any YAML frontmatter field including difficulty levels
3. **API reliability matters** — `obsidian_patch_content` with `replace` only works on existing fields. Always verify after batch operations.
4. **Code-mode batching is essential** — updating 119 notes one-by-one would take the entire session. Batch scripts via code-mode cut it to minutes.
5. **Standardization compounds** — now that every note has `difficulty:` frontmatter, graph view can group by `[difficulty:foundational]` etc. for instant skill-level visualization.

---

## Vault State After Session 7
- **149 notes** (no new notes added this session)
- **119 notes classified** by difficulty (foundational/intermediate/advanced)
- **Dashboard + Vault-Search** updated and accurate
- **Custom vault map** (SecOps-Vault-Map.html) created

---

## Next Session Priorities
1. Add `#foundational` / `#intermediate` / `#advanced` tags to all classified notes (second pass)
2. Networking-Basics.md rewrite (practical admin sections — DNS zones, DHCP, VLAN, NTP)
3. System-Admin-Reference, Bash-Scripting, Common-Ports, Incident-Response gap fills
4. Enhance vault map with wikilink cross-section connections
5. Learning pathway notes (deferred from S5)

---
<- [[Learning-Log-MOC]]