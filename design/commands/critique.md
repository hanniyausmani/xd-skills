---
description: Get structured design feedback on usability, hierarchy, and consistency
argument-hint: "<Figma URL, screenshot, or description>"
---

# /critique

> If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../CONNECTORS.md).

Get structured design feedback across multiple dimensions. See the **design-critique** skill for the evaluation framework and feedback principles.

## Usage

```
/critique $ARGUMENTS
```

Review the design: @$1

If a Figma URL is provided, pull the design from Figma. If a file is referenced, read it. Otherwise, ask the user to describe or share their design.

## What I Need From You

- **The design**: Figma URL, screenshot, or detailed description
- **Context**: What is this? Who is it for? What stage (exploration, refinement, final)?
- **Focus** (optional): "Focus on mobile" or "Focus on the onboarding flow"

## Output

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

## If Connectors Available

If **~~design tool** is connected:
- Pull the design directly from Figma and inspect components, tokens, and layers
- Compare against the existing design system for consistency

If **~~user feedback** is connected:
- Cross-reference design decisions with recent user feedback and support tickets

## Tips

1. **Share the context** — "This is a checkout flow for a B2B SaaS" helps me give relevant feedback.
2. **Specify your stage** — Early exploration gets different feedback than final polish.
3. **Ask me to focus** — "Just look at the navigation" gives you more depth on one area.

## Writing Results to Figma

**After generating the output above, always write a summary annotation directly into the Figma file** when a Figma URL was provided. Keeps critique findings visible next to the design for easy side-by-side review.

Use the `use_figma` tool with the same `fileKey`. Extract the `nodeId` from the URL (e.g. `node-id=7-2` → `"7:2"`). Run this JavaScript, replacing placeholder values with real findings from your critique:

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
// REPLACE with the 1-2 sentence overall impression from your critique
addText(panel, "Overall: [Replace with impression from critique output]", 12, "Regular", "#C8C8EE");
addDivider(panel);
// REPLACE items[] with actual findings from the critique, ≤100 chars each
const sections = [
  { label: "🔴 Critical",    color: "#FF7070", items: ["Finding — usability or hierarchy issue"] },
  { label: "🟡 Moderate",    color: "#F2C94C", items: ["Finding — consistency or accessibility issue"] },
  { label: "✅ What Works",  color: "#6FCF97", items: ["Strength 1", "Strength 2"] },
];
for (const s of sections) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AAAACE");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full critique in the Claude conversation.", 10, "Regular", "#555577");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Critique annotation created in Figma." };
```
