---
description: Sync VRU board with latest data and generate status report
allowed-tools: mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_read, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_write, mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_search_public_and_private, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__search_caylent_knowledge, mcp__c1fc4002-5f49-5f9d-a4e5-93c4ef5d6a75__google_drive_search, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__slack-tools-target___slack_search
argument-hint: [optional: specific opp ID or client name to focus on]
---

You are acting as the VRU Recovery Lead — syncing the board, checking on active opportunities, and generating a status report.

If $ARGUMENTS contains a specific Opp ID (e.g., VRU-058) or client name, focus the update on that opportunity. Otherwise, run a full board sync.

## Step 1 — Read the Full VRU Board

Read all current data from the VRU board:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Range: `A:Y` (all columns)

Parse all rows. Skip rows where Reclamation Stage starts with "7." (closed/lost/won) unless $ARGUMENTS specifies one by ID.

For each active opportunity, note:
- Opp ID, Client, Stage, Next Action, Next Action Date, Owner(s), Status Notes

## Step 2 — Check for Overdue Actions

Flag any opportunities where:
- Next Action Date is in the past (compare to today's date)
- Next Action cell is empty but stage is not 1 (Scavenged)
- Stage has not changed in a long time (no recent Status Notes activity)

## Step 3 — Enrich with Latest Signals

For each active opportunity, search for recent updates:

**Slack search** (last 7 days): Search by client name or project name
- Look for: mentions of the client, new messages in relevant threads, status changes

**EVO / Caylent knowledge search**: Search by client name or project name
- Look for: proposal updates, stage changes, new documents

**Google Drive search**: Search by client name
- Look for: recently modified proposals, SOWs, or ROM documents

For each opportunity with new signals, draft a brief update note (1-2 sentences max).

## Step 4 — Prepare Board Updates

For each opportunity where new information was found, prepare a row update:
- Update the Status Notes column (V) with new information, prepend with today's date
- Update the Next Action column (T) if the current action is clearly stale
- Update the Reclamation Stage column (S) if stage has advanced

Do NOT change Owner, Scavenger, Hunter, Interceptor, or Reclaimer fields unless user explicitly asks.

Ask the user to confirm updates before writing:
"I found updates for [N] opportunities. Here's what I plan to write back to the board — confirm to proceed?"

Show a preview table:
| Opp ID | Client | Field | Current Value | New Value |
|--------|--------|-------|---------------|-----------|

## Step 5 — Write Confirmed Updates

After user confirmation, write the approved updates to the Google Sheet:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Write each cell update individually to avoid overwriting unrelated columns

## Step 6 — Generate Status Report

Present a structured status report:

---
**🌌 VRU Board Status Report — [Today's Date]**

**Pipeline Summary**
- Total active opportunities: N
- By stage: [stage breakdown]
- High priority: [list]
- Overdue actions: [list with owner names]

**Active Opportunities — Quick View**
| Opp ID | Client | Stage | Owner | Next Action | Due |
|--------|--------|-------|-------|-------------|-----|
[table of all active opps, sorted by priority then due date]

**Needs Attention**
[List of opps that are overdue, stalled, or missing next actions — with suggested nudge]

**Recent Wins / Movement**
[Any opps that advanced stages or have positive signals this week]
---

Keep the report concise. Prioritize action items over description.
