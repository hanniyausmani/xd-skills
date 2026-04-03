---
description: Get structured design feedback on usability, hierarchy, and consistency
argument-hint: "<Figma URL, screenshot, or description>"
allowed-tools: mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_design_context, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_metadata, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_screenshot, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma
---

# /critique

Get structured design feedback across usability, visual hierarchy, and consistency. See the **design-critique** skill for the full evaluation framework.

## Step 1 — Read the Figma Design

If a Figma URL is provided in $ARGUMENTS:

1. Extract the **file key** from the URL — it's the string after `/design/` or `/file/` and before the next `/`. Example: `https://www.figma.com/design/ABC123xyz/my-file` → file key is `ABC123xyz`
2. Extract the **node ID** from the URL — find `node-id=` in the URL params, take the value, and replace `-` with `:`. Example: `node-id=7-2` → node ID is `7:2`. Store this exact string for Step 4.
3. Call `get_design_context` with the file key to read the design, inspect components, tokens, and layer structure.
4. Call `get_screenshot` to view the design visually.

If no Figma URL is provided, ask the user to share a Figma URL, screenshot, or detailed description before proceeding.

## Step 2 — Generate the Critique

Using what you read from Figma (or from the provided screenshot/description), produce:

```markdown
## Design Critique: [Design Name]

### Overall Impression
[1-2 sentence first reaction — what works, what's the biggest opportunity]

### Usability
| Finding | Severity | Recommendation |
|---------|----------|----------------|
| [Issue] | 🔴 Critical / 🟡 Moderate / 🟢 Minor | [Fix] |

### Visual Hierarchy
- **What draws the eye first**: [Element] — [Is this correct?]
- **Reading flow**: [How does the eye move through the layout?]
- **Emphasis**: [Are the right things emphasized?]

### Consistency
| Element | Issue | Recommendation |
|---------|-------|----------------|
| [Typography/spacing/color] | [Inconsistency] | [Fix] |

### Accessibility
- **Color contrast**: [Pass/fail for key text]
- **Touch targets**: [Adequate size?]
- **Text readability**: [Font size, line height]

### What Works Well
- [Positive observation 1]
- [Positive observation 2]

### Priority Recommendations
1. **[Most impactful change]** — [Why and how]
2. **[Second priority]** — [Why and how]
3. **[Third priority]** — [Why and how]
```

## Step 3 — Write the Annotation to Figma

**This step is mandatory whenever a Figma URL was provided. Do not skip it.**

Use `use_figma` with the file key from Step 1. In the JavaScript below:
- Replace `"NODE_ID_HERE"` with the node ID string you extracted in Step 1 (e.g. `"7:2"`)
- Replace all `[placeholder]` values with real findings from your critique above

```javascript
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

const targetNode = figma.getNodeById("NODE_ID_HERE");
const xPos = targetNode ? Math.round(targetNode.x + targetNode.width + 80) : 200;
const yPos = targetNode ? targetNode.y : 200;

const panel = figma.createFrame();
panel.name = "🔍 Design Critique — Claude";
panel.x = xPos; panel.y = yPos; panel.resize(560, 100);
panel.fills = [{ type: "SOLID", color: { r: 0.09, g: 0.09, b: 0.16 } }];
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
  const r = figma.createRectangle(); r.resize(512,1);
  r.fills = [{type:"SOLID",color:{r:0.20,g:0.20,b:0.34}}];
  r.layoutSizingX = "FILL"; parent.appendChild(r);
}
function addSpacer(parent,h=4){const r=figma.createRectangle();r.resize(1,h);r.fills=[];parent.appendChild(r);}

addText(panel, "🔍 Design Critique", 15, "Bold", "#FFFFFF");
addText(panel, new Date().toLocaleDateString("en-US",{year:"numeric",month:"long",day:"numeric"}), 11, "Regular", "#7777AA");
addDivider(panel);
addText(panel, "[Overall impression — 1-2 sentences from critique]", 12, "Regular", "#C8C8EE");
addDivider(panel);
const sections = [
  { label: "🔴 Critical",    color: "#FF7070", items: ["[Critical finding from critique]"] },
  { label: "🟡 Moderate",    color: "#F2C94C", items: ["[Moderate finding from critique]"] },
  { label: "✅ What Works",  color: "#6FCF97", items: ["[Strength 1]", "[Strength 2]"] },
];
for (const s of sections) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AAAACE");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full critique in the Claude conversation.", 10, "Regular", "#555577");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Critique annotation created next to frame in Figma." };
```
