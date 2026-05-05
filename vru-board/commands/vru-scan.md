---
description: Hunt Slack, EVO, Drive, Gmail & Gong for new UX opportunities using the full XD signal taxonomy
allowed-tools: mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_search_public_and_private, mcp__b2b73ebd-2c91-4eb0-a5c5-f23e2584be78__slack_read_channel, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__slack-tools-target___slack_search, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__search_caylent_knowledge, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_read, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_sheets_write, mcp__adb8d8c0-f540-4eec-994c-b78b53eaa15c__document-tools-target___google_docs_create, mcp__9bdeb2aa-190b-481f-a503-78592ec45295__gmail_search_messages, mcp__9bdeb2aa-190b-481f-a503-78592ec45295__gmail_read_message, mcp__f76c5203-e2d7-4368-9270-dce1f7fc047e__search_files
argument-hint: [optional: focus area or keyword]
---

You are acting as the VRU Scavenger — hunting for missed or overlooked UX opportunities across Caylent's data sources.

The goal is to find opportunities the XD team is **not yet part of**. This means: clients not on the VRU board at all, or clients that were previously lost/parked and now have a compelling new signal. Do not surface projects where a designer is already engaged — those aren't void; they're covered.

If the user provided a focus keyword or area via $ARGUMENTS, prioritize signals related to that topic. Otherwise, run a broad scan.

---

## Signal Reference — Know What You're Looking For

Before scanning, internalize these four signal categories. Every result you evaluate should be filtered through this lens.

### 🗣️ Language Signals (What people say)
Listen for these exact phrases or close variants in any Slack message, Gong call summary, or document:
- "We're not totally sure what the MVP is yet"
- "We'll figure out the UI later"
- "Engineering can handle the UX"
- "The client already has designers" — flag for "Client Has UX" validation (see Step 7)
- "We just need something pretty"
- "It's mostly backend… but there will be a dashboard/portal"
- "Phase 2 will focus on usability"
- "We don't want to slow things down with process"

### 🧩 Project Signals (What's in scope)
- AI / GenAI features of any kind
- Dashboards, portals, or data visualizations
- Internal tools or admin workflows
- Automation replacing manual work
- Multiple data sources being combined
- Complex rules or decision logic
- User-facing configuration or setup screens
- Any form entry or reporting surface
- New workflows being created (vs. existing ones being migrated)
- A web app developer (Erin Kovar or Rob Caluza) is staffed — **auto-qualify trigger**
- A Product Manager is staffed but no designer is listed

#### High-Signal Opportunity Name Patterns
When any of these names appear in Salesforce, EVO, Slack, or Drive — treat as an immediate project signal and read the full channel/doc:

**GenAI (always UX-relevant — chatbots, assistants, and knowledge bases always have a user interface):**
GenAI POC, GenAI Project, Gen AI, GenAI CO, GenAI Knowledge Base, GenAI KB, GenAI Strategy, GenAI PoV, GenAI Innovation Engine, GenAI Chatbot, Chatbot POC, GenAI Build Out, GenAI Voicebot, Voice bot, CX Agents

**Data & Analytics (dashboards and reporting surfaces are UX surfaces):**
Data Lake, QuickSight POC, Q for QuickSight, Data Platform POC, Data Visualization, Reporting, Analytics Platform, Enterprise Search, Semantic Search MVP, Document Search POC

**ML & MLOps (user-facing model interfaces and ops dashboards):**
ML Ops, ML MVP, ML APIs, SageMaker Pilot, Model Factory

**Application Development (explicit UI/UX work):**
App Dev, App Dev Pod, UI CO, Portal Development, Mobile App, Web App, Web Portal Build, Frontend Dev, Front End Dev, App Modernization, Application Assessment, MVP QA/QC, Platform Buildout, Marketplace Development

**Explicit UX Names (these are the dream — someone already scoped it):**
UI/UX Design, Designer, Product Strategy, Product Workshop, Application Workshop, AI Ideation & Strategy

**Common Evolution Pattern to Watch:**
GenAI Strategy → GenAI POC → GenAI Build Out → Change Orders. Each handoff is a new intercept window if XD wasn't in the previous phase.

### ⚠️ Risk Signals (What's missing)
- MVP not clearly defined
- No user personas identified or discussed
- No workflow diagrams or user journeys mentioned
- No error states or edge cases discussed
- No adoption plan or change management mentioned
- No success metrics tied to user behavior
- No validation with actual users planned
- Proposal is purely technical with no UX section
- UX was mentioned in the ROM but removed or descoped

### 🧠 Behavior Signals (How teams are acting)
- Proposal is hard to find or incomplete in Salesforce/EVO
- Opportunity data is fragmented across Salesforce, EVO, and docs
- Decisions being made without reference to user needs
- UX came up late in the cycle ("we already sent the proposal")
- No designer listed anywhere on the staffing plan
- PM or architect expressing uncertainty about what users need
- Erin Kovar or Rob Caluza staffed on a user-facing project without a designer

### 💡 Translation Rule
If you hear: **"This is a technical project"** → translate to: **"This is a UX risk project."**
If you hear: **"They already have UX"** → validate before dismissing (see Step 7).

---

## Step 1 — Slack Scan

Search Slack for recent messages (last 14 days) across all channels. Use **natural language queries** — do not use OR-separated keyword lists. Run all of these searches:

- `new project proposal client` — surface new engagement activity
- `UX design opportunity missed` — find explicit UX gap signals
- `dashboard after:YYYY-MM-DD` — find dashboard-related discussions (use the date 14 days ago)
- `web app mobile app frontend after:YYYY-MM-DD` — find user-facing build discussions
- `GenAI chatbot knowledge base after:YYYY-MM-DD` — find AI projects
- `app modernization reporting visualization after:YYYY-MM-DD` — find analytics/AppMod work
- `MVP workflow portal admin tool after:YYYY-MM-DD` — catch internal tool and workflow signals
- `figure out the UI handle the design mostly backend after:YYYY-MM-DD` — catch explicit language signals
- `GenAI POC GenAI strategy voicebot CX agents after:YYYY-MM-DD` — catch high-signal opportunity names
- `data lake QuickSight analytics platform semantic search after:YYYY-MM-DD` — catch data/analytics opps
- `ML ops model factory platform buildout marketplace after:YYYY-MM-DD` — catch ML and platform opps

For any #opp- channel that surfaces in results, read the channel directly using `slack_read_channel` to get full context. For each channel, assess:
- Which of the four signal categories are present?
- Team composition on the Caylent side (PM + SA + no designer = signal)
- Erin Kovar or Rob Caluza being staffed without a designer — **auto-qualify trigger**
- Anyone saying "the client already has designers" — flag for validation, not dismissal

For each Slack result, note: channel, date, key quote, client/project name, team composition, and which signal categories triggered.

## Step 2 — EVO / Caylent Knowledge Scan

> **Known limitation:** The EVO knowledge base (search_caylent_knowledge) frequently times out. If it does, note the timeout in the coverage summary and move on — do not retry more than once.

Search the Caylent knowledge base for recently added or updated opportunities:
- Search for: `proposal GenAI dashboard no UX designer`
- Search for: `app modernization web portal reporting`
- Search for: `MVP workflow admin tool internal`

## Step 3 — Google Drive Scan

Search Google Drive for recently updated documents (last 30 days):
- Search for: `proposal` or `statement of work` or `ROM`
- Search for: `GenAI` or `dashboard` or `analytics reporting`
- Search for: `MVP` or `admin portal` or `internal tool`
- Check if any Drive docs mention projects already seen in Slack

## Step 4 — Gmail Scan

> **Known limitation:** This scan runs from the designer's inbox, which does not receive most sales and delivery emails directly. Treat Gmail as a supplementary source.

Run targeted searches:
- `from:gong.io newer_than:14d` — Gong call summaries CC'd to the user
- `subject:(proposal OR scope OR "statement of work") newer_than:14d from:caylent.com`
- `(web app OR dashboard OR GenAI OR MVP) newer_than:14d from:caylent.com`

For any promising thread, look for: client name, project type, team composition, and whether UX is mentioned.

## Step 5 — Gong Scan (via Gmail)

> **Known limitation:** Gong digests go to call participants, not the XD team. If none are found, note it and flag that app.gong.io should be checked manually.

If any Gong emails are present, look for:
- UI, dashboard, portal, chatbot, or web app features being discussed
- Team composition — PM or dev staffed but no designer?
- Any of the language signals from the Signal Reference above

## Step 6 — Cross-reference with VRU Board

Read the full VRU board:
- Spreadsheet ID: `1Y6nRhU9BnLBk0H5o_lh3DbvhMTiKESMzsT_hIWK2Cfc`
- Range: `A:S`
- Key columns: A = Opp ID, C = Client Name, M = Assigned Role, N = Owner, S = Reclamation Stage

**Caylent XD Team (check all for Owner/Assigned Role columns):**
Hanniya Usmani, Melissa Leide, Nathan McCown, Carolina Herrera, Ella Costa, Chloe Park, Emmie Bowles, Kristen LaBarbera, Evangelina Wiens, Beatriz Araujo

**Exclusion list — drop these clients entirely:**
- Any client where Reclamation Stage contains `3. Actively Intercepting`, `4. Intercepted`, or `6. Actively Reclaiming`
- Any client where Owner or Assigned Role contains any XD team member's name

**Re-engage watchlist:**
- Clients in stage `7.a Parked`, `7.b`, `7.c`, or `7.d` where the Owner is Hanniya Usmani — surface in a separate Re-engage section if a new signal exists
- Unowned parked opps — fair game if new signal is materially different from why it was parked

## Step 6.5 — Hard Filter Before Scoring

- Signal from exclusion list client → **drop completely**
- Signal from owned parked opp → move to Re-engage section
- Signal from unowned parked opp → carry forward only if new signal is materially different

## Step 7 — Qualify and Rank Findings

For each signal that passed the filter, apply the full qualification framework:

### Auto-qualify (flag immediately) if any are true:
- AI/GenAI features + no UX mentioned anywhere in the engagement
- Dashboard, portal, or visualization in scope + no designer on the team
- MVP not defined + user-facing workflows
- A proposal promises UI deliverables but has no designer on the staffing plan
- Erin Kovar or Rob Caluza staffed on a user-facing project without a designer
- Engagement wrapping up with no designer → flag as **follow-on opportunity**

### "Client Has UX" Validation
When someone says "the client already has designers" — do not dismiss. Clarify:
- Do they have product designers or marketing/UI designers?
- Are they defining workflows or only visuals?
- Are they designing AI interactions and trust patterns?
- Are they validating with users?
- Do they own MVP definition?
If gaps exist → UX can still add value. Flag and explain the gap.

### Delivery Risk Check
Without UX involvement, check for risk of:
- Unclear MVP definition
- Edge cases missed
- Error states undefined
- Confusing user flows
- Rework in later phases
- Adoption or trust issues (especially with AI features)
- Engineering making UX decisions by default

If **2 or more** boxes checked → escalate UX. If **4 or more** → auto-qualify.

### Scoring
Count signal categories present across the four types:
- 3 points per signal category present (Language, Project, Risk, Behavior)
- Bonus 2 points if: GenAI/AI present, MVP ambiguous, no UX in scope at all, or web dev staffed without designer
- Maximum score: 14 points

### Right-size the UX Role
For each qualified opp, suggest the smallest valuable UX involvement:
- Discovery sprint (2–4 weeks)
- Workflow mapping
- AI interaction design
- Prototype for clarity
- Design guardrails for engineering
- Design system alignment
- Usability / risk review

## Step 8 — Present Findings

### New Opportunities

| # | Client / Project | Source | Signal Categories | Key Quote / Evidence | Score | Right-sized UX Role | Suggested Next Step |
|---|-----------------|--------|-------------------|---------------------|-------|---------------------|---------------------|

Include engagements wrapping up as **follow-on** opportunities, clearly labeled.

### Re-engage (Your Parked Opps with New Signals)

> **[Client] (VRU-XXX)** — New signal: [description]. Suggested action: [re-intercept / reach out / monitor].

### Filtered Out

> Filtered out: [Client] (reason), [Client] (reason), …

### After the tables:
- **Recommended top pick** — 2-sentence summary of why this opp is worth pursuing first
- **Coverage summary** — which sources were useful, which were empty, known limitations
- **Ask to log** — ask which opportunities to log to the VRU board. Do not log anything automatically.

---

## Step 9 — Deep Research Each Opp to Log

Once the user confirms which to log, research each one before writing to the board:
- Try `slack_read_channel` on `#opp-[clientname]` (lowercase, hyphenated)
- If that fails, use `slack_search_public_and_private` with the client name
- Look for: proposal details and timing, key contacts (AE, SA, PM, client names/titles), budget signals, designer/UX signals (in-house designer? SA building the UI?)

## Step 10 — Bulk Log to Board

Find the next available Opp ID from column A, then write all opps in a single batch.

**Column mapping:**
- A = Opp ID (VRU-###, zero-padded to 3 digits)
- B = Date Found (today's date, MM/DD/YYYY)
- C = Client Name
- D = Source (Slack, EVO, Google Drive, etc.)
- E = Link (Slack channel URL or relevant doc link)
- F = Related Project / Initiative
- G = Opportunity Type (New / Expansion / Follow-on)
- H = What Fell Into the Void (the specific UX gap — 1–2 sentences)
- I = UX Value (AI UX, workflow design, data visualization, etc.)
- J = AE / CSA
- K = Potential Value (Small / Medium / Large)
- L = Priority (Low / Medium / High)
- M = Assigned Role (leave blank)
- N = Owner (leave blank)
- O = Scavenger (Hanniya Usmani)
- P–R = Hunter / Interceptor / Reclaimer (leave blank)
- S = Reclamation Stage = `1. Scavenged`
- T = Next Action — "Reach out to [AE name] in #opp-[channel]"
- U = Next Action Date — 1 week from today
- V = Status Notes — "See Hunting Notes doc — link coming"

Write all rows in a single `google_sheets_write` call. Read them back to confirm.

## Step 11 — Create Hunting Notes Google Doc

Title: `VRU Hunting Notes - Hanniya Usmani`

For each opp:

```
# VRU-0XX | [Client Name]
Last updated: [Month Day, Year]

---

**WHAT [CLIENT] IS**
[Plain-English description — no AWS service names, no framework names, no jargon. Write for a designer who knows Caylent's business but not cloud infrastructure.]

**DEAL STATE**
[What is actively happening right now? Specific timing — proposals being written, calls that just happened, follow-ups scheduled.]

**CONTACTS**
[Internal Caylent contacts and client-side contacts. Format: Name (Role) — why they matter to the UX conversation.]

**UX GAPS**
[Bullet each gap with WHY it matters — not just "no designer on team" but the specific user harm or business risk.]

**SIGNAL BREAKDOWN**
[Which of the four signal categories triggered, and the specific evidence for each.]

**INTERCEPTOR ANGLE**
[Who to reach out to, which Slack channel, and the exact framing. Give the interceptor a hook the AE will care about.]

**BUDGET & DESIGNER**
[Does the client have in-house UX? What's the budget situation? Is UX already scoped or needs to be negotiated in?]

**RIGHT-SIZED UX ROLE**
[The smallest valuable UX engagement for this opp — don't oversell, just show where design adds the most with the least friction.]

---

**INTERCEPTOR MESSAGE TEMPLATE**
*Send to: [Name] in #opp-[channel]*

> [Ready-to-send Slack DM. Casual, collegial, references something specific and timely. 3–5 sentences. No em-dashes. No client namedropping in internal Slack. Don't propose joining calls uninvited.]
```

**Language rules:**
- No AWS service names — describe what they do instead
- No framework names — say "web app" or "the interface"
- No internal jargon — write it out plainly
- Keep it concrete: names, dates, dollar amounts, specific product descriptions

## Step 12 — Link Doc to Board

Update column V for all logged rows with: `See Hunting Notes: [Google Doc URL]`

Do this in a single `google_sheets_write` call.

Then confirm:
> ✅ [N] opportunities logged (VRU-0XX through VRU-0XX). Hunting Notes doc created and linked. Interceptor message templates are ready — copy, personalize, and send.
