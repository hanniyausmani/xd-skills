# VRU Board Plugin

**Void Reclamation Unit — UX Opportunity Hunter for the Caylent Experience Design team.**

This plugin gives Claude deep knowledge of the VRU mission, board schema, and signal patterns — and adds three commands to scan for opportunities, sync the board, and log new finds.

## Commands

### `/vru-scan`
Hunt for new UX opportunities across Slack, EVO, and Google Drive.

Searches all Slack channels for language, project, risk, and behavior signals from the VRU hunting checklist. Cross-references with EVO and Google Drive for recent proposals and project activity. Surfaces a ranked list of signals with a recommended next step for each — does not write to the board automatically.

**Optional argument:** A keyword or focus area to narrow the scan (e.g., `/vru-scan GenAI` or `/vru-scan Netsmart`).

### `/vru-update`
Sync the VRU board with the latest information and generate a status report.

Reads all active opportunities from the board, searches for recent Slack/EVO/Drive activity per client, flags overdue actions, prepares updates — and asks for your confirmation before writing anything back. Ends with a structured status report showing the full pipeline by stage, priority, and owner.

**Optional argument:** A specific Opp ID or client name to focus on (e.g., `/vru-update VRU-058`).

### `/vru-log`
Log a new opportunity to the VRU board.

Walks you through capturing the key details, auto-qualifies the opportunity using VRU signal patterns (suggests UX value, priority, and next action), generates the next Opp ID, and shows a preview before writing — so nothing lands on the board without your sign-off.

**Optional argument:** A client name or quick description to pre-fill the form (e.g., `/vru-log Acme Corp - chatbot with no UX`).

## Skill: `vru-opportunity-hunting`

The plugin also loads a skill that gives Claude full knowledge of the VRU methodology. This triggers automatically when you ask things like:
- "Is this a UX opportunity?"
- "Qualify this for me"
- "What signals does this have?"
- "What's the VRU hunting checklist?"
- "How do I position UX value for this?"

## Required Connectors

This plugin uses connectors that must be active in your Claude session:

| Connector | Used For |
|-----------|----------|
| **EVO (Caylent)** | Google Sheets (read/write VRU board), Caylent knowledge search |
| **Slack** | Scanning channels for UX opportunity signals |
| **Google Drive** | Searching for proposals, ROMs, and project docs |

All connectors are assumed to already be installed. If a command fails, check that the relevant connector is active.

## The VRU Board

The board lives at:
https://docs.google.com/spreadsheets/d/1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc/edit

All three commands are hardcoded to this spreadsheet ID. If the board ever moves, update the spreadsheet ID in all three command files.

## About the VRU

The Void Reclamation Unit hunts missed, overlooked, or under-scoped UX opportunities and ensures Experience Design has a seat at the table early, often, and with impact. Operated by the Caylent Experience Design team.

Crew roles: Scavenger → Hunter → Interceptor → Reclaimer. Recovery Lead: Mel.
