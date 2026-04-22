---
title: Epic SmartTexts & SmartPhrases
type: reference
category: epic
created: 2026-03-27
updated: 2026-03-27
tags:
  - epic
  - smarttext
  - smartphrase
  - smartlink
  - smartlist
  - documentation
  - build
  - job-aid
  - intermediate
source: Epic UserWeb training materials, Epic SmartTools build guides
difficulty: intermediate
---

# ✍️ Epic SmartTexts & SmartPhrases

## Overview

Epic's **SmartTools** are the documentation productivity features that allow clinicians to build, reuse, and dynamically populate clinical note content. They dramatically reduce documentation time and promote consistency in clinical notes. As an analyst, you build, maintain, and support these tools at both the individual provider and organizational level.

The SmartTools family includes:
- **SmartPhrases** — text shortcuts that expand to blocks of pre-written text
- **SmartTexts** — more complex, organization-managed documentation templates
- **SmartLinks** — dynamic data pulls that insert patient-specific data into notes
- **SmartLists** — pick lists embedded in SmartPhrases/SmartTexts for structured choices

---

## SmartPhrases

### What Is a SmartPhrase?
A SmartPhrase is a **text abbreviation** that expands to a larger block of pre-written text when the provider types the phrase's shortcut and presses a trigger key (typically **period + Enter** or **F2**).

**Example:**
- Shortcut: `.followup`
- Expands to: *"Patient is scheduled for follow-up in 4–6 weeks. Please call the office if symptoms worsen or new symptoms develop before your next appointment."*

### SmartPhrase Types
| Type | Scope | Built By |
|---|---|---|
| **Personal SmartPhrase** | Individual user only | Individual provider (self-service) |
| **Shared SmartPhrase** | Group or org-wide | Analyst / clinical informatics |

### SmartPhrase Anatomy
| Component | Description |
|---|---|
| Abbreviation (shortcut) | The text shortcut the provider types |
| Phrase body | The full text that expands |
| SmartLinks | Dynamic data fields embedded in the phrase |
| SmartLists | Pick lists embedded in the phrase |
| Wild cards | Cursor stopping points that prompt provider to fill in a value |

### Wild Cards (Cursor Stops)
Wild cards appear in SmartPhrases as `***` or a configurable token. When the phrase expands, the cursor stops at each wild card and the provider fills in patient-specific information.

**Example with wild card:**
`.hpi` expands to:
*"Patient presents with *** for *** days. Associated symptoms include ***. Denies ***."*
Provider tabs through each `***` to fill in the chief complaint, duration, associated symptoms, and denials.

### Building a Shared SmartPhrase
1. Navigate to **Epic > SmartPhrase Manager** (accessible in Hyperspace for users with appropriate security)
2. New SmartPhrase > set abbreviation and name
3. Type phrase body
4. Insert SmartLinks where dynamic data is needed (e.g., patient name, date, current medications)
5. Insert SmartLists where provider should make a pick-list selection
6. Add wild cards (`***`) where free-text fill-in is needed
7. Set as shared (org-level or group-level)
8. Test by typing abbreviation in a note in Test/Train
9. Promote or share with provider group

### Provider Self-Service SmartPhrase
Providers can create personal SmartPhrases directly in Hyperspace:
1. In a note, type the desired phrase text
2. Right-click or use the Tools menu > Create SmartPhrase
3. Set abbreviation
4. Save as personal phrase
5. Analyst may assist or create on behalf if provider is not comfortable

---

## SmartTexts

### What Is a SmartText?
A **SmartText** is a more complex, organization-managed documentation template — typically a **complete note section or entire note template** built by the informatics/analyst team. SmartTexts can include:
- Pre-written paragraphs
- SmartLinks (dynamic data)
- SmartLists (pick lists)
- Embedded questionnaire responses
- Care plan content

### SmartText vs. SmartPhrase
| Feature | SmartPhrase | SmartText |
|---|---|---|
| Complexity | Simple text block | Full note templates with complex logic |
| Management | Provider-self or analyst | Typically analyst / informatics |
| Scope | Individual or shared | Usually shared / org-level |
| Use case | Quick note fragments | Complete note sections or templates |
| Approval needed | Usually not for personal | Often requires governance |

### SmartText Build
1. Navigate to **Epic > SmartText Editor** (or Chronicles > SmartText record)
2. Create new SmartText record
3. Build structured note content
4. Embed SmartLinks, SmartLists, wild cards
5. Associate with relevant note types / specialties
6. Test in Train environment
7. Governance review
8. Promote to production

---

## SmartLinks

### What Is a SmartLink?
A **SmartLink** is a **dynamic data pull** embedded in a SmartPhrase or SmartText that automatically inserts real patient data when the note is created or rendered.

### Common SmartLinks
| SmartLink | What It Pulls |
|---|---|
| `.NAME` | Patient's full name |
| `.FIRSTNAME` | Patient's first name |
| `.DOB` | Patient's date of birth |
| `.AGE` | Patient's age |
| `.SEX` | Patient's sex (as documented) |
| `.MEDS` | Active medication list |
| `.ALLERGIES` | Allergy list |
| `.PROBLEMS` | Active problem list |
| `.VITALS` | Most recent vitals |
| `.DATE` | Today's date |
| `.PROVIDER` | Documenting provider's name |
| `.DEPT` | Department name |
| `.RESULTS` | Recent lab results (configurable) |

> **Note:** SmartLink names are preceded by a period in Epic notation. The actual token format varies slightly by Epic version and context. [VERIFY: Epic UserWeb — SmartLink Library for your version]

### Inserting SmartLinks
- In SmartPhrase or SmartText editor: use the SmartLink picker (button in editor toolbar)
- Or type the SmartLink directly if you know the token name
- SmartLinks render with real patient data when the note is opened in a patient's chart

---

## SmartLists

### What Is a SmartList?
A **SmartList** is an **embedded pick list** inside a SmartPhrase or SmartText that prompts the provider to make one or more selections from a predefined list of options.

### SmartList Example
A SmartPhrase for ROS (Review of Systems) might include a SmartList:
`Patient denies [SMARTLIST:CONSTITUTIONAL]`
When the note opens, the provider clicks the SmartList and selects from: `fever, chills, fatigue, weight loss, night sweats` (each of which may be pre-checked as denied, or provider selects positive findings).

### SmartList Configuration
| Setting | Description |
|---|---|
| List items | The selectable options |
| Default selected | Items pre-selected (provider can change) |
| Single vs. multi-select | Whether one or multiple items can be chosen |
| "None of the above" option | Configurable |
| Free text fallback | Allow provider to type custom entry |

### Building a SmartList
1. Navigate to **Epic > SmartList Editor**
2. Create new SmartList record
3. Add items (may be plain text or ICD/CPT codes for diagnosis/procedure lists)
4. Set defaults and selection behavior
5. Embed in a SmartPhrase or SmartText using the SmartList token
6. Test in Train

---

## Auto-Text & Dot Phrases

### Dot Phrases (`.`)
In Epic terminology, **dot phrases** are interchangeable with SmartPhrases — they are triggered by typing `.abbreviation`. This is the common colloquial term providers use. When providers ask for "dot phrases," they mean SmartPhrases.

### Auto-Text (Nuance Dragon Ambient + Epic)
Some organizations use **Nuance DAX** (Dragon Ambient eXperience) integrated with Epic for ambient documentation (AI listens to encounter, generates note). This is a separate product from SmartPhrases but related to the documentation ecosystem.
- [VERIFY: Epic UserWeb — Dragon / Nuance DAX integration if applicable to your org]

---

## Common Scenarios & Job-Aid

### Scenario: Provider Requests a New Shared SmartPhrase for Their Department
1. Gather phrase text from provider
2. Identify if SmartLinks or SmartLists would enhance the phrase
3. Build in Test environment
4. Send test access to provider for review
5. Clinical approval
6. Promote to production and share with relevant provider group
7. Communicate to department staff with abbreviation to use

### Scenario: SmartLink Showing Incorrect / No Data in Notes
1. Verify SmartLink token syntax is correct
2. Confirm data exists in the patient's chart for that SmartLink (e.g., no meds = empty meds SmartLink)
3. Check if SmartLink is pulling from the correct encounter context
4. Test with a patient who has known data populated in that field
5. Escalate to Epic team if token appears broken

### Scenario: Provider's Personal SmartPhrase Needs to Be Shared Org-Wide
1. Obtain the phrase from the provider (they can share/copy the text or transfer ownership)
2. Create a new shared SmartPhrase record with the content
3. Governance review if this is a clinical note template with patient care implications
4. Share with appropriate user group or make org-wide
5. Notify affected staff

### Scenario: SmartPhrase Is Pulling Old/Stale Text
1. Identify which record is being used (shared or personal version — multiple copies can coexist)
2. Edit the correct record (shared in SmartPhrase Manager or personal in Hyperspace)
3. Update text, SmartLinks, and SmartLists as needed
4. If shared: governance review, test, promote
5. If personal: provider can self-manage or request analyst assistance

---

## Related Notes
- Epic-Content-Management
- Epic-Provider-Masterfile
- Epic-Clinic-Workflow

## References
- Epic UserWeb — SmartPhrase Build and Management Guide
- Epic UserWeb — SmartText Build Guide
- Epic UserWeb — SmartLink Library Reference
- Epic UserWeb — SmartList Configuration Guide

---
<- [[Epic-MOC]]