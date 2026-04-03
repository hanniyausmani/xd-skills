---
description: Hunt Slack, EVO, Drive, Gmail & Gong for new UX opportunities
allowed-tools: mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_search_public_and_private, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__slack-tools-target___slack_search, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__search_caylent_knowledge, mcp__c1fc4002-5f49-5f9d-a4e5-93c4ef5d6a75__google_drive_search, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_read, mcp__9bdeb2aa-190b-481f-a503-78592ec45295__gmail_search_messages, mcp__9bdeb2aa-190b-481f-a503-78592ec45295__gmail_read_message
argument-hint: [optional: focus area or keyword]
---

You are acting as the VRU Scavenger — hunting for missed or overlooked UX opportunities across Caylent's data sources.

If the user provided a focus keyword or area via $ARGUMENTS, prioritize signals related to that topic. Otherwise, run a broad scan.

## Step 1 — Slack Scan

Search Slack for recent messages (last 14 days) across all channels. Run multiple targeted searches to catch different signal types:

- "figure out the UI later" OR "engineering can handle the UX" OR "already have designers"
- "dashboard" OR "portal" OR "admin tool" OR "internal tool"
- "MVP" OR "not sure what the MVP is" OR "phase 2 usability"
- "GenAI" OR "AI chatbot" OR "knowledge base" OR "voicebot"
- "app modernization" OR "web app" OR "mobile app" OR "frontend"
- "data visualization" OR "reporting" OR "analytics platform"

For each result, note: channel, date, key quote, client or project name mentioned.

## Step 2 — EVO / Caylent Knowledge Scan

Search the Caylent knowledge base for recently added or updated opportunities that may not yet have UX involvement:
- Search for: "proposal" OR "ROM" OR "scope" — look for anything with app dev, GenAI, dashboards, or portals
- Search for: "no UX" OR "no designer" OR "technical only"
- Search for recent project names or clients mentioned in Slack results

## Step 3 — Google Drive Scan

Search Google Drive for recently updated documents that may contain UX signals:
- Search for: "proposal" OR "statement of work" OR "ROM" — docs updated in the last 30 days
- Search for: "GenAI" OR "chatbot" OR "dashboard" OR "portal" — filter to recent files
- Check if any Drive docs mention projects already seen in Slack

## Step 4 — Gmail Scan

Search Gmail for recent emails (last 14 days) containing UX opportunity signals:

- `subject:(proposal OR "statement of work" OR ROM OR scope)`
- `subject:(dashboard OR portal OR "web app" OR "mobile app" OR "internal tool")`
- `(GenAI OR "AI chatbot" OR voicebot OR "knowledge base") newer_than:14d`
- `("no designer" OR "no UX" OR "engineering will handle" OR "figure out the UI") newer_than:14d`
- `(MVP OR "phase 2" OR "app modernization" OR frontend) newer_than:14d`

For promising threads, open the full message and look for client name, project description, team composition, and whether UX is mentioned. Note: sender/recipient, date, subject, key signal, and client/project name.

## Step 5 — Gong Scan (via Gmail Digests)

Search Gmail for recent Gong call summaries:
- `from:gong.io newer_than:14d`
- `from:gong.io (dashboard OR portal OR "web app" OR GenAI OR MVP) newer_than:14d`
- `from:gong.io ("no designer" OR "no UX" OR "figure out the UI" OR "engineering can handle") newer_than:14d`

For any Gong digest mentioning a relevant project, open the full email and look for:
- Client name and project type
- Whether UX, design, or a designer was mentioned
- Unclear MVP, user-facing workflows, or AI features
- Team composition (PM or dev staffed but no designer?)

Note: call date, participants, client/project, and key signal. If no Gong digest emails are found, flag that Gong may need to be checked manually at app.gong.io.

## Step 6 — Cross-reference with VRU Board

Read the current VRU board to avoid flagging things already logged:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Range: `A:C` (Opp ID, Date Found, Client Name) — just enough to check for duplicates

Remove any findings that match a client already on the board.

## Step 7 — Qualify and Rank Findings

For each new signal found, apply the VRU signal filters:

**Auto-qualify (flag immediately) if:**
- AI/GenAI features + no UX mentioned
- Dashboard or portal in scope + no designer listed
- MVP not defined + user-facing workflows
- "Client has UX" language present

**Scoring:**
- 3 points per signal category present (language, project, risk, behavior)
- Bonus 2 points if: GenAI/AI, MVP ambiguity, or no UX in scope at all

## Step 8 — Present Findings

Present a ranked summary table of new opportunities found:

| # | Client / Project | Source | Key Signal(s) | Score | Suggested Next Step |
|---|-----------------|--------|---------------|-------|---------------------|

After the table, include:
- **Recommended top pick** with a 2-sentence summary of why it's worth pursuing
- **Coverage summary**: Brief note on which sources returned results and which came up empty

## Step 9 — Output Pre-filled Log Entries

For each qualifying opportunity, output a pre-filled log entry block so the user can immediately run `/vru-log` without re-entering details.

Format each block as:

---
**Pre-filled Log Entry — Opp [#] · [Client Name]**
- Client: [client name]
- Source: [where found]
- Link: [URL or thread link if available]
- Related Project: [project or initiative name]
- What Fell Into the Void: [specific UX gap]
- Opportunity Type: [New / Expansion / Follow-on]
- AE / CSA: [if known]
- Potential Value: [Small / Medium / Large]
- UX Value: [AI UX / data visualization / workflow design / etc.]
- Priority: [Low / Medium / High]
---

After all blocks, say:
> "To log any of these, run `/vru-log` and tell me which opp number (e.g. 'log opp 2'). I'll use the pre-filled details above — you won't need to re-enter anything."

Do not write anything to the VRU board automatically. The user must explicitly run `/vru-log` to confirm and log an opportunity.
