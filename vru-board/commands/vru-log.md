---
description: Log a new opportunity to the VRU board
allowed-tools: mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_read, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_write, mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_search_public_and_private, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__search_caylent_knowledge
argument-hint: [opp number from scan, client name, or opportunity description]
---

You are acting as the VRU Scavenger logging a new opportunity to the Void Reclamation Board.

## Step 1 — Check for Scan Results

First, check if the user just ran `/vru-scan` in this conversation and whether pre-filled log entries are present.

**If scan results exist and the user referenced a specific opp number** (e.g. "log opp 2", "log the second one"):
- Pull the pre-filled details for that opp from the scan output
- Skip straight to Step 2 — do not ask for information already gathered
- Confirm to the user: "I'm using the pre-filled details from the scan for [Client Name]. Let me know if anything needs adjusting."

**If no scan results exist**, use $ARGUMENTS as the starting point — it may be a client name, a project description, or a rough signal the user spotted. Collect the following in a single grouped message (not one by one):

**Required:**
- Client name
- Source (where was it found — Slack, EVO, Gmail, Gong, Insider Tip, etc.)
- Related project or initiative name
- What fell into the void (the specific UX gap or missed signal)
- Your name (for the Scavenger field)

**Optional but helpful:**
- Link (Salesforce URL, Slack thread, or Google Drive doc)
- AE / CSA contact names
- Opportunity type (New / Expansion / Follow-on)
- Potential value (Small / Medium / Large)

## Step 2 — Auto-qualify the Opportunity

Based on the details gathered (from scan or from the user), apply the VRU signal checklist silently and propose:

1. **UX Value** — What type of UX work is relevant? (AI UX, data visualization, workflow design, adoption design, etc.)
2. **Priority** — Based on signals present: Low / Medium / High
3. **Reclamation Stage** — Always start at `1. Scavenged`
4. **Suggested Next Action** — Based on what's known, what should happen next?

Present your proposed values:
> "Here's how I'd qualify this opportunity — adjust anything before I log it:"

| Field | Proposed Value |
|-------|----------------|
| UX Value | ... |
| Priority | ... |
| Stage | 1. Scavenged |
| Next Action | ... |
| Next Action Date | [suggest 1 week from today] |

## Step 3 — Determine Next Opp ID

Read the VRU board to find the highest existing Opp ID:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Range: `A:A` (Opp ID column only)

Parse all VRU-### IDs, find the max number, and increment by 1. Format as VRU-0## (zero-padded to 3 digits for numbers under 100, plain ### for 100+).

## Step 4 — Confirm and Write

Present the full row preview before writing anything:

**New Entry Preview — [Opp ID]**
| Field | Value |
|-------|-------|
| Opp ID | VRU-### |
| Date Found | [today] |
| Client | ... |
| Source | ... |
| Link | ... |
| Related Project | ... |
| Opportunity Type | ... |
| What Fell Into the Void | ... |
| UX Value | ... |
| AE / CSA | ... |
| Potential Value | ... |
| Priority | ... |
| Scavenger | [user's name] |
| Reclamation Stage | 1. Scavenged |
| Next Action | ... |
| Next Action Date | ... |

Ask: "Ready to log this to the board?"

Only write to the sheet after the user confirms. Append the new row to the VRU board:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Append after the last row with data

## Step 5 — Confirm Success

After writing:
> "✅ [Opp ID] — [Client Name] logged to the VRU board. Stage: 1. Scavenged. Next action: [action] by [date]."

If there are more pre-filled opps from a scan still waiting to be logged, remind the user:
> "There are [N] more opps from the scan ready to log. Say 'log opp [#]' to continue."
