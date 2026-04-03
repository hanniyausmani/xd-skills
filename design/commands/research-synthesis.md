---
description: Synthesize user research into themes, insights, and recommendations
argument-hint: "<research data, transcripts, or survey results> [optional: Figma/FigJam URL]"
allowed-tools: mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_design_context, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_screenshot, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma
---

# /research-synthesis

Synthesize user research data into actionable insights. See the **user-research** skill for research methods, interview guides, and analysis frameworks.

## What I Accept

- Interview transcripts or notes
- Survey results (CSV, pasted data)
- Usability test recordings or notes
- Support tickets or feedback
- NPS/CSAT responses
- App store reviews

## Step 1 — Read Figma Context (if URL provided)

If a Figma or FigJam URL is included in $ARGUMENTS:

1. Extract the **file key** from the URL — it's the string after `/design/`, `/file/`, or `/board/` and before the next `/`.
2. Extract the **node ID** from the URL — find `node-id=` in the URL params, take the value, and replace `-` with `:`. Example: `node-id=7-2` → node ID is `7:2`. Store this for Step 3.
3. Call `get_design_context` to understand the existing artifacts on the board (journey maps, wireframes, etc.)
4. Call `get_screenshot` to view the board visually.

## Step 2 — Generate the Synthesis

```markdown
## Research Synthesis: [Study Name]
**Method:** [Interviews / Survey / Usability Test] | **Participants:** [X]
**Date:** [Date range] | **Researcher:** [Name]

### Executive Summary
[3-4 sentence overview of key findings]

### Key Themes

#### Theme 1: [Name]
**Prevalence:** [X of Y participants]
**Summary:** [What this theme is about]
**Supporting Evidence:**
- "[Quote]" — P[X]
- "[Quote]" — P[X]
**Implication:** [What this means for the product]

#### Theme 2: [Name]
[Same format]

### Insights → Opportunities

| Insight | Opportunity | Impact | Effort |
|---------|-------------|--------|--------|
| [What we learned] | [What we could do] | High/Med/Low | High/Med/Low |

### Recommendations
1. **[High priority]** — [Why, based on which findings]
2. **[Medium priority]** — [Why]
3. **[Lower priority]** — [Why]

### Questions for Further Research
- [What we still don't know]
```

## Step 3 — Write the Annotation to Figma

**This step is mandatory whenever a Figma or FigJam URL was provided. Do not skip it.**

Use `use_figma` with the file key from Step 1. In the JavaScript below:
- Replace `"NODE_ID_HERE"` with the node ID string you extracted in Step 1 (e.g. `"7:2"`)
- Replace all `[placeholder]` values with real data from your synthesis above

```javascript
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

const targetNode = figma.getNodeById("NODE_ID_HERE");
const xPos = targetNode ? Math.round(targetNode.x + targetNode.width + 80) : 200;
const yPos = targetNode ? targetNode.y : 200;

const panel = figma.createFrame();
panel.name = "🔬 Research Synthesis — Claude";
panel.x = xPos; panel.y = yPos; panel.resize(580, 100);
panel.fills = [{ type: "SOLID", color: { r: 0.08, g: 0.12, b: 0.14 } }];
panel.cornerRadius = 12; panel.layoutMode = "VERTICAL";
panel.primaryAxisSizingMode = "AUTO"; panel.counterAxisSizingMode = "FIXED";
panel.paddingTop = 24; panel.paddingBottom = 28;
panel.paddingLeft = 24; panel.paddingRight = 24; panel.itemSpacing = 10;
figma.currentPage.appendChild(panel);

function hex(h) { h=h.replace("#",""); return {r:parseInt(h.slice(0,2),16)/255,g:parseInt(h.slice(2,4),16)/255,b:parseInt(h.slice(4,6),16)/255}; }
function addText(parent, chars, size, style, color) {
  const t = figma.createText(); t.fontName = {family:"Inter",style};
  t.characters = chars; t.fontSize = size;
  t.fills = [{type:"SOLID",color:hex(color)}];
  t.layoutSizingX = "FILL"; t.textAutoResize = "HEIGHT";
  parent.appendChild(t); return t;
}
function addDivider(parent) {
  const r = figma.createRectangle(); r.resize(532,1);
  r.fills = [{type:"SOLID",color:{r:0.18,g:0.24,b:0.28}}];
  r.layoutSizingX = "FILL"; parent.appendChild(r);
}
function addSpacer(parent,h=4){const r=figma.createRectangle();r.resize(1,h);r.fills=[];parent.appendChild(r);}

addText(panel, "🔬 Research Synthesis", 15, "Bold", "#FFFFFF");
addText(panel, new Date().toLocaleDateString("en-US",{year:"numeric",month:"long",day:"numeric"}), 11, "Regular", "#558899");
addDivider(panel);
addText(panel, "Study: [Name]  •  Method: [Type]  •  Participants: [X]", 12, "Medium", "#B8D8EE");
addText(panel, "[2-3 sentence executive summary]", 11, "Regular", "#99BBCC");
addDivider(panel);
addText(panel, "📌 Key Themes", 12, "Bold", "#7EB8D4");
const themes = [
  { name: "[Theme 1: Name] ([X] of [Y] participants)", implication: "[Implication for product]" },
  { name: "[Theme 2: Name] ([X] of [Y] participants)", implication: "[Implication for product]" },
];
for (const t of themes) {
  addSpacer(panel,2);
  addText(panel, t.name, 11, "Medium", "#D0E8F0");
  addText(panel, "→ "+t.implication, 10, "Regular", "#7799AA");
}
addSpacer(panel); addDivider(panel);
addText(panel, "🎯 Top Recommendations", 12, "Bold", "#6FCF97");
const recs = ["1. [High priority recommendation]", "2. [Medium priority recommendation]", "3. [Lower priority recommendation]"];
for (const r of recs) addText(panel, r, 11, "Regular", "#AACCBB");
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full synthesis with quotes & evidence in the Claude conversation.", 10, "Regular", "#335566");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Research synthesis annotation created next to frame in Figma." };
```
