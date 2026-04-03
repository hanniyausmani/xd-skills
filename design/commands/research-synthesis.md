---
description: Synthesize user research into themes, insights, and recommendations
argument-hint: "<research data, transcripts, or survey results>"
---

# /research-synthesis

> If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../CONNECTORS.md).

Synthesize user research data into actionable insights. See the **user-research** skill for research methods, interview guides, and analysis frameworks.

## Usage

```
/research-synthesis $ARGUMENTS
```

## What I Accept

- Interview transcripts or notes
- Survey results (CSV, pasted data)
- Usability test recordings or notes
- Support tickets or feedback
- NPS/CSAT responses
- App store reviews

## Output

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

### User Segments Identified
| Segment | Characteristics | Needs | Size |
|---------|----------------|-------|------|
| [Name] | [Description] | [Key needs] | [Rough %] |

### Recommendations
1. **[High priority]** — [Why, based on which findings]
2. **[Medium priority]** — [Why]
3. **[Lower priority]** — [Why]

### Questions for Further Research
- [What we still don't know]

### Methodology Notes
[How the research was conducted, any limitations or biases to note]
```

## If Connectors Available

If **~~user feedback** is connected:
- Pull support tickets, feature requests, and NPS responses to supplement research data
- Cross-reference themes with real user complaints and requests

If **~~product analytics** is connected:
- Validate qualitative findings with usage data and behavioral metrics
- Quantify the impact of identified pain points

If **~~knowledge base** is connected:
- Search for prior research studies and findings to compare against
- Publish the synthesis to your research repository

## Tips

1. **Include raw quotes** — Direct participant quotes make insights credible and memorable.
2. **Separate observations from interpretations** — "5 of 8 users clicked the wrong button" is an observation. "The button placement is confusing" is an interpretation.
3. **Quantify where possible** — "Most users" is vague. "7 of 10 users" is specific.

## Writing Results to Figma

**When a FigJam board or Figma file URL is provided, always write a synthesis summary into the board** so the team can review findings alongside journey maps and wireframes without switching tools.

Use the `use_figma` tool with the `fileKey` from the board URL. Extract `nodeId` if present; use a root page node ID if not. Run this JavaScript, replacing placeholder values with real data from your synthesis:

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
// REPLACE with study metadata and 2-3 sentence executive summary
addText(panel, "Study: [Name]  •  Method: [Type]  •  Participants: X", 12, "Medium", "#B8D8EE");
addText(panel, "[2-3 sentence executive summary from synthesis output]", 11, "Regular", "#99BBCC");
addDivider(panel);
addText(panel, "📌 Key Themes", 12, "Bold", "#7EB8D4");
// REPLACE with actual themes from synthesis, ≤100 chars each
const themes = [
  { name: "Theme 1: [Name] (X of Y participants)", implication: "Implication for product" },
  { name: "Theme 2: [Name] (X of Y participants)", implication: "Implication for product" },
];
for (const t of themes) {
  addSpacer(panel,2);
  addText(panel, t.name, 11, "Medium", "#D0E8F0");
  addText(panel, "→ "+t.implication, 10, "Regular", "#7799AA");
}
addSpacer(panel); addDivider(panel);
addText(panel, "🎯 Top Recommendations", 12, "Bold", "#6FCF97");
// REPLACE with actual top 3 recommendations
const recs = ["1. [High priority]", "2. [Medium priority]", "3. [Lower priority]"];
for (const r of recs) addText(panel, r, 11, "Regular", "#AACCBB");
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full synthesis with quotes & evidence in the Claude conversation.", 10, "Regular", "#335566");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Research synthesis annotation created in Figma." };
```
