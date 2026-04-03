---
description: Check active VRU opps for updates by cross-referencing Slack, Gmail, Drive, and Gong
allowed-tools: mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_read, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_write, mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_search_public_and_private, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__slack-tools-target___slack_search, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__search_caylent_knowledge, mcp__c1fc4002-5f49-5f9d-a4e5-93c4ef5d6a75__google_drive_search, mcp__9bdeb2aa-190b-481f-a503-78592ec45295__gmail_search_messages, mcp__9bdeb2aa-190b-481f-a503-78592ec45295__gmail_read_message
argument-hint: [optional: specific opp ID or client name to focus on]
---

You are acting as the VRU Recovery Lead — checking in on active opportunities, finding out what's happened since the last update, and telling the team exactly what to do next.

If $ARGUMENTS contains a specific Opp ID (e.g., VRU-058) or client name, focus only on that opportunity. Otherwise, run a full check across all active opps.

## Step 1 — Read the Full VRU Board

Read all current data from the VRU board:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Range: `A:Y` (all columns)

Parse all rows. Skip rows where Reclamation Stage starts with "7." (closed/lost/won) unless $ARGUMENTS specifies one by ID.

For each active opportunity, extract and store:
- Opp ID, Client Name, Source, Related Project
- AE / CSA, Owner, Scavenger, Hunter, Interceptor, Reclaimer
- Reclamation Stage, Next Action, Next Action Date, Status Notes

## Step 2 — Parse Next Actions Intelligently

For each active opportunity, carefully read the **Next Action** and **Status Notes** fields. Extract the intent:

- **"reach out to [person] in [#channel]"** → Search Slack for that channel + that person's name. Look for their reply or any relevant messages since the last Status Notes entry.
- **"message [person] about [topic]"** → Search Slack for that person + the client/project name. Check if they responded.
- **"email [person]"** or **"follow up with [person]"** → Search Gmail for that person's name + client name. Look for a reply.
- **"check Gong call"** or **"review call with [client]"** → Search Gmail for `from:gong.io [client name]` to find a recent call summary.
- **"update in Drive"** or **"check proposal"** → Search Google Drive for the client name and look for recently modified docs.
- **"wait for response"** or **"pending [person]"** → Search Slack and Gmail for any message from that person related to the project.

If the Next Action is vague or missing, search broadly: Slack for the client name (last 7 days), Gmail for the client name (last 7 days), and Drive for recently modified docs mentioning the client.

## Step 3 — Find What's Actually Happened

For each opp, run the targeted searches based on Step 2. You are looking for evidence that:
- The person they were supposed to reach out to has responded
- New information has surfaced about the client or project
- The stage has advanced (UX is now in scope, proposal sent, etc.)
- Something has stalled or gone cold

Be specific in what you report. Do not say "some activity found in Slack." Instead say:
> "Matthew replied in #caylent-delivery on April 1st: 'Yes, let's set up a call this week.' You should respond and propose a time."

If nothing new was found for an opp, say so clearly.

## Step 4 — Prepare Update Summaries

For each opp where something was found, prepare a plain-language update and a suggested next action. Format as:

---
**[Opp ID] — [Client Name]** · Stage: [current stage]

**What happened:** [1-2 sentences on what was found — be specific, quote messages if relevant]

**Suggested next step:** [Concrete, actionable — e.g., "Reply to Matthew in #caylent-delivery and propose a call for this week" or "Email Sarah with the updated ROM — she's waiting on it"]

**Board update:** Update Status Notes to: "[today's date] — [brief summary of what was found]"
---

For opps with no new activity, group them at the end in a single "No updates found" list.

## Step 5 — Confirm and Write Board Updates

Present all proposed Status Notes updates to the user before writing anything:

> "Here's what I plan to update on the board — confirm to proceed, or adjust anything:"

| Opp ID | Client | Field | New Value |
|--------|--------|-------|-----------|
| VRU-### | ... | Status Notes | [today's date] — ... |

Only write to the sheet after the user confirms. Update each cell individually — do not overwrite unrelated columns.

Do NOT change Owner, Scavenger, Hunter, Interceptor, or Reclaimer fields unless the user explicitly asks.

## Step 6 — Generate Status Report

After updates are written (or if the user skips writing), present a focused action report:

---
**VRU Status Report — [Today's Date]**

**Needs Your Attention**
[Opps with clear next steps — listed with the specific action to take, in priority order]

**Active — No Blockers**
[Opps that are on track with no immediate action needed]

**No Updates Found**
[Opps where nothing new surfaced — may need a manual check]

**Pipeline Summary**
- Active opps: N
- By stage: [breakdown]
- High priority: [list]
---

Keep the report action-first. The most important thing is telling the team exactly what to do next for each opp, not describing what's already on the board.
