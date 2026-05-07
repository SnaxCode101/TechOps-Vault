---
title: Rapid Rounds — Project Roadmap
tags:
  - project
  - healthcare-it
  - portfolio
  - prototype
topic: Projects
created: 2026-05-07
updated: 2026-05-07
status: Planning
source: Primary research — Kassie Crotty RN, Lowell General Hospital ED
---

# Rapid Rounds — Project Roadmap

> Primary source: Kassie Crotty, RN — ER Nurse, Lowell General Hospital. Interview conducted May 2026. Responses filed in [[Healthcare project response from Kassie]].

---

## Problem Statement

Five core workflow failures documented from primary source interview:

**1. Fragmented patient status**
Labs, imaging, consults, and dispo plans live in separate places inside Epic. No single clean status view exists. Nurses build their own mental model using handwritten brain sheets because navigating Epic for quick checks during a busy shift is too slow.

**2. No real-time order visibility**
When a CT scan, transport request, or consult is placed, there is no way to see where that request stands. Nurses call, walk across the department, or ask in person to get updates — all while managing other patients.

**3. Handoff failures**
Shift-to-shift and department-to-department handoffs have no confirmation loop. Information is buried in long notes with inconsistent documentation styles. Nurses re-verify everything themselves after receiving report regardless of what was said — because they have to.

**4. Third-party vendor blindspot**
The Stryker cyberattack did not disrupt Epic directly, but disrupted supply chain and surgical scheduling, creating downstream boarding delays in the ED. Internal system stability does not equal operational stability.

**5. Unofficial workarounds are the actual workflow**
Handwritten brain sheets, grabbing providers in person, and asking for verbal updates before orders are documented — these are not workarounds. They are how the ED actually runs. The system forces them.

**The nurse's ask (verbatim):** *"Where is everything for this patient?" — one click, one view.*

---

## Project Concept

**Project name:** Rapid Rounds *(named by Kassie Crotty, RN — May 2026)*

**What it is:** A web-based prototype of a unified patient status dashboard for ED nurses. No real Epic integration — functional mockup using simulated HL7/FHIR-structured data to demonstrate the architecture, UX concept, and healthcare IT knowledge behind the solution.

**What it is not:** Production EHR software. HIPAA-covered software. A real Epic module. All patient data is synthetic and de-identified by design.

**Portfolio story:** *"I interviewed a licensed ER nurse, mapped her actual workflow gaps, and built a prototype demonstrating what a real solution would look like — grounded in HL7/FHIR data standards, HIPAA-aware design, and real Epic workflow context."*

---

## Project Phases

### Phase 1 — Research and Documentation (COMPLETE)
- [x] Primary source interview — Kassie Crotty, RN (Lowell General Hospital ED)
- [x] Five core workflow failures documented
- [x] Kassie response note filed → [[Healthcare project response from Kassie]]
- [x] Project roadmap created (this note)

---

### Phase 2 — Design
- [ ] Define patient data model using mock HL7 FHIR R4 resources:
  - `Patient` — room, name, attending, age
  - `Observation` — lab results with status (ordered / in-progress / resulted)
  - `DiagnosticReport` — imaging (CT, X-ray) with status + read status
  - `ServiceRequest` — consults, transport, procedures with elapsed time
  - `Encounter` — dispo plan, barriers, estimated disposition
- [ ] Wireframe the dashboard — single-screen layout:
  - Patient header bar (room, name, attending, time in ED)
  - Pending results panel — labs and imaging status at a glance
  - Active orders panel — consults, transport, procedures with elapsed time + delay flags
  - Dispo plan panel — admit / discharge / transfer with barriers clearly flagged
  - Handoff summary panel — shift-change ready view, consistent format
- [ ] Define delay thresholds — what triggers a "delayed" status flag per order type
- [ ] Get wireframe feedback from Kassie before building

---

### Phase 3 — Build
- [ ] Tech stack decision (React preferred for portfolio weight — see stack table below)
- [ ] Mock data generator — Python or JS producing synthetic FHIR-structured JSON
- [ ] Dashboard frontend — functional, not just a static screenshot
- [ ] Status polling simulation — mimic real-time updates via JS interval or mock WebSocket
- [ ] Responsive layout — ED tracking boards are wide-format, but nurses also check on mobile

---

### Phase 4 — Security and HIPAA Layer
- [ ] Document HIPAA Technical Safeguard alignment (even as a prototype this matters for the portfolio story):
  - Access controls — authentication required, role-based view
  - Audit controls — what actions would be logged in production
  - Integrity controls — data validation, no manipulation without authentication
  - Transmission security — HTTPS only, no PHI over plain HTTP
- [ ] Design note section: what a production deployment would require (BAA, encryption at rest, audit logging, Epic SMART on FHIR authorization)
- [ ] Stryker attack tie-in — vendor dependency risk section in the project write-up

---

### Phase 5 — Portfolio Integration
- [ ] Write project case study for snaxcode.dev:
  - Problem → Research (nurse interview) → Design → Build → Security layer
  - Anchor with Kassie's real-world insight (paraphrased or with permission)
- [ ] Deploy demo to snaxcode.dev — decide: public demo or Cloudflare-gated
- [ ] Add Healthcare IT project card to index.html
- [ ] Update vault learning log

---

## Technical Stack (Planned)

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Frontend | React + Tailwind | Portfolio weight, component-based patient cards, clean responsive layout |
| Mock data | Python + FHIR JSON | Demonstrates HL7 FHIR R4 knowledge directly |
| Hosting | snaxcode.dev / Cloudflare Pages | Already live, consistent with existing portfolio infrastructure |
| Auth (optional) | Cloudflare Access | Matches existing site security model if demo is gated |

---

## Vault Integration

This project draws directly from existing vault knowledge:

| Vault Note | Relevance |
|-----------|-----------|
| [[HL7-FHIR-Fundamentals]] | Data model for mock patient records |
| [[HL7-v2-Messaging]] | Background on how systems actually exchange clinical data |
| [[Healthcare-IT-Integration-Patterns]] | Architecture reference for order tracking design |
| [[HIPAA-Technical-Safeguards]] | Security layer design and compliance write-up |
| [[Epic-Overview]] | Context for what the dashboard supplements |
| [[ServiceNow-Epic-Integration]] | Reference for order status tracking patterns |
| [[Medical-Device-Incident-Response]] | Stryker attack context for vendor dependency section |

---

## Portfolio Value

- Backed by a primary source interview with a licensed ER nurse — not a public dataset or hypothetical
- Translates real clinical workflow pain into a concrete technical design
- Connects HIPAA, HL7/FHIR, and Epic knowledge from the vault into one artifact
- Differentiator: most portfolio projects are technical exercises — this one solves an actual problem a real person described

---

## Open Questions

1. Deploy as public demo or Cloudflare-gated — depends on how clearly synthetic the mock data reads
2. Is Kassie available for a second round of feedback once a wireframe exists?

---

*Created: 2026-05-07 | Status: Planning | Source: [[Healthcare project response from Kassie]]*

<- [[Projects-MOC]]
