---
name: vru-opportunity-hunting
description: >
  This skill should be used when the user asks about the VRU, Void Reclamation Unit,
  UX opportunity hunting, qualifying a UX opportunity, logging to the VRU board,
  scanning for missed UX work, checking if a project needs UX, or anything related to
  the Caylent Experience Design pipeline. Trigger phrases include: "is this a UX opp",
  "hunt this", "qualify this opportunity", "VRU checklist", "scavenger", "interceptor",
  "reclaimer", "what's on the board", "missed UX", "position UX value".
version: 0.1.0
---

# VRU — Void Reclamation Unit

The VRU exists to hunt missed, overlooked, or under-scoped UX opportunities and ensure Experience Design has a seat at the table early, often, and with impact.

**Mission:** Go where no one is looking. Dig through the mess. Find the value others missed.

## The VRU Board

The Void Reclamation Board (Opportunity Tracker) is the single source of truth for all UX opportunities being pursued.

- **Spreadsheet ID:** `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- **URL:** https://docs.google.com/spreadsheets/d/1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc/edit

### Board Column Schema

| Column | Field | Notes |
|--------|-------|-------|
| A | Opp ID | Format: VRU-### (auto-increment) |
| B | Date Found | MM/DD/YYYY |
| C | Client Name | |
| D | Source | Slack, EVO, Salesforce, Insider Tip, Gong, etc. |
| E | Link | Salesforce URL, Slack thread, or doc link |
| F | Related Project / Initiative | |
| G | Opportunity Type | New / Expansion / Follow-on |
| H | What Fell Into the Void | The specific UX gap or missed signal |
| I | UX Value | AI UX, data visualization, workflow design, etc. |
| J | AE / CSA | Account Executive / Customer Success contact |
| K | Potential Value | Small / Medium / Large |
| L | Priority | Low / Medium / High |
| M | Assigned Role | |
| N | Owner | |
| O | Scavenger | Person who found it |
| P | Hunter | Person qualifying it |
| Q | Interceptor | Person activating it |
| R | Reclaimer | Person converting it |
| S | Reclamation Stage | See stages below |
| T | Next Action | Specific next step |
| U | Next Action Date / Deadline | MM/DD/YYYY |
| V | Status Notes | Running notes |
| W | (separator) | |
| X | Outcome Reason | If closed/lost |
| Y | Learnings | What we learned |

### Reclamation Stages

- `1. Scavenged` — Signal found, raw opportunity logged
- `2.a Needs Hunter` — Ready for qualification
- `2.b Ready for Interceptor` — Qualified, needs activation
- `3. Intercepted` — In conversation with Sales/Delivery
- `4. Reclaimed` — UX is officially part of the engagement
- `7.a Lost / Not Pursued` — Decided not to pursue
- `7.b Lost – Budget` — Lost due to budget
- `7.c Lost – Client has UX` — Client has own designers
- `7.d Parked – Follow Up Later` — Timing isn't right yet
- `7.e Won` — UX successfully added

## Crew Roles

| Role | Focus | Success Signal |
|------|-------|----------------|
| Scavenger | Discovery — scans channels/calls, logs raw signals | "I found something interesting." |
| Hunter | Qualification — validates UX relevance and value | "This is worth pursuing." |
| Interceptor | Activation — reaches out to Sales/Delivery, positions UX | "We're in the room." |
| Reclaimer | Conversion — shapes scope, strengthens positioning | "UX is officially part of the engagement." |
| Recovery Lead (Mel) | Oversees strategy, prioritization, and evolution | |

## Opportunity Qualification — Quick Checklist

Before qualifying or dismissing any opportunity, run through these signal categories. See `references/signal-patterns.md` for the full list.

**Immediate red flags that signal UX need:**
- "We'll figure out the UI later" or "Engineering can handle the UX"
- AI/GenAI features present
- Dashboards, portals, or internal tools in scope
- MVP not clearly defined
- No user personas, workflow diagrams, or adoption plan
- Web App Developer or PM is staffed but no designer listed

**Delivery Risk Check — escalate UX if 2+ apply:**
- Unclear MVP definition
- Edge cases or error states undefined
- Rework risk in later phases
- Adoption or trust issues (especially AI)
- Engineering making UX decisions by default

## "Client Has UX" Validation

When you hear "they already have UX," always clarify:
- Do they have product designers or just marketing/UI designers?
- Are they defining workflows or only visuals?
- Are they designing AI interaction and trust patterns?
- Are they validating with users?
- Do they own MVP definition?

If gaps exist → UX can add value.

## Positioning Language

**One-liner for Sales intercept:**
> "This project has user-facing workflows and AI elements. Without UX, we risk unclear MVP and rework. A short UX engagement could define workflows and reduce delivery risk."

**Right-size UX framing:**
- Discovery sprint (2–4 weeks)
- Workflow mapping
- AI interaction design
- Prototype for clarity
- Design guardrails for engineering
- Usability/risk review

Position as: *"Reduce rework + protect delivery + increase adoption"*

## Data Sources to Scan

When hunting for opportunities, check:
- **Slack** — All channels, especially: resourcing, wins, CX, delivery, sales
- **EVO (Caylent platform)** — Open opportunities, proposals, active projects
- **Google Drive** — Recent proposals, ROMs, retro docs
- **Gong** — Call recordings for missed UX signals
- **Salesforce** — Pipeline and opportunity data

For full signal patterns and the complete hunting checklist, see `references/signal-patterns.md` and `references/hunting-checklist.md`.
