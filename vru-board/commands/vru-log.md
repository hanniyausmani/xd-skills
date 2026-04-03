---
description: Log a new opportunity to the VRU board
allowed-tools: mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_read, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_write, mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_search_public_and_private, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__search_caylent_knowledge
argument-hint: [client name or opportunity description]
---

You are acting as the VRU Scavenger logging a new opportunity to the Void Reclamation Board.

The user wants to log a new opportunity. Use $ARGUMENTS as the starting point — it may be a client name, a project description, or a rough signal they spotted.

## Step 1 — Gather Details Conversationally

If $ARGUMENTS provides enough context to pre-fill fields, do so and confirm with the user. Otherwise, ask for what you need.

Collect the following (ask in a single grouped message, not one by one):

**Required:**
- Client name
- Source (where was it found — Slack, EVO, Salesforce, Gong, Insider Tip, etc.)
- Related project or initiative name
- What fell into the void (the specific UX gap or missed signal — be specific)
- Your name (for the Scavenger field)

**Optional but helpful:**
- Link (Salesforce URL, Slack thread, or Google Drive doc)
- AE / CSA contact names
- Opportunity type (New / Expansion / Follow-on)
- Potential value (Small / Medium / Large)

## Step 2 — Auto-qualify the Opportunity

Based on what the user described, apply the VRU signal checklist silently and then state:

1. **UX Value** — What type of UX work is relevant? (AI UX, data visualization, workflow design, adoption design, etc.)
2. **Priority** — Based on signals present: Low / Medium / High
3. **Reclamation Stage** — Start at `1. Scavenged`
4. **Suggested Next Action** — Based on what's known, what should happen next?

Present your proposed values to the user:
"Here's how I'd qualify this opportunity — adjust anything before I log it:"

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

Present the full row preview before writing:

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

Once confirmed, append the new row to the VRU board:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Append after the last row with data

## Step 5 — Confirm Success

After writing, confirm:
"✅ [Opp ID] — [Client Name] logged to the VRU board. Stage: 1. Scavenged. Next action: [action] by [date]."

Remind the user they can use `/vru-update` to sync the board and generate a full status report anytime.
