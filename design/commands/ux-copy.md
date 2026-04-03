---
description: Write or review UX copy — microcopy, error messages, empty states, CTAs
argument-hint: "<context or copy to review>"
---

# /ux-copy

> If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../CONNECTORS.md).

Write or review UX copy for any interface context. See the **ux-writing** skill for copy principles, patterns, and voice/tone guidance.

## Usage

```
/ux-copy $ARGUMENTS
```

## What I Need From You

- **Context**: What screen, flow, or feature?
- **User state**: What is the user trying to do? How are they feeling?
- **Tone**: Formal, friendly, playful, reassuring?
- **Constraints**: Character limits, platform guidelines?

## Output

```markdown
## UX Copy: [Context]

### Recommended Copy
**[Element]**: [Copy]

### Alternatives
| Option | Copy | Tone | Best For |
|--------|------|------|----------|
| A | [Copy] | [Tone] | [When to use] |
| B | [Copy] | [Tone] | [When to use] |
| C | [Copy] | [Tone] | [When to use] |

### Rationale
[Why this copy works — user context, clarity, action-orientation]

### Localization Notes
[Anything translators should know — idioms to avoid, character expansion, cultural context]
```

## Common UX Copy Types

- **CTAs**: Clear, specific, action-oriented ("Start free trial" not "Submit")
- **Error messages**: What happened, why, and how to fix it
- **Empty states**: Guide the user to take their first action
- **Confirmation dialogs**: Make the consequences clear
- **Onboarding**: Progressive disclosure, one concept at a time
- **Tooltips**: Concise, helpful, never obvious
- **Loading states**: Set expectations, reduce anxiety

## If Connectors Available

If **~~knowledge base** is connected:
- Pull your brand voice guidelines and content style guide
- Check for existing copy patterns and terminology standards

If **~~design tool** is connected:
- View the screen context in Figma to understand the full user flow
- Check character limits and layout constraints from the design

## Tips

1. **Be specific about context** — "Error message when payment fails" is better than "error message."
2. **Share your brand voice** — "We're professional but warm" helps me match your tone.
3. **Consider the user's emotional state** — Error messages need empathy. Success messages can celebrate.

## Writing Results to Figma

**When a Figma URL is provided, always write copy recommendations into the Figma file** as an annotation so writers and designers can review copy in context without switching to Claude.

Use the `use_figma` tool with the same `fileKey`. Extract the `nodeId` from the URL (e.g. `node-id=7-2` → `"7:2"`). Run this JavaScript, replacing placeholder values with real copy from your output:

```javascript
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

const targetNode = figma.getNodeById("NODE_ID_HERE");
const xPos = targetNode ? Math.round(targetNode.x + targetNode.width + 80) : 200;
const yPos = targetNode ? targetNode.y : 200;

const panel = figma.createFrame();
panel.name = "✍️ UX Copy — Claude";
panel.x = xPos; panel.y = yPos; panel.resize(560, 100);
panel.fills = [{ type: "SOLID", color: { r: 0.10, g: 0.08, b: 0.14 } }];
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
  r.fills = [{type:"SOLID",color:{r:0.22,g:0.18,b:0.28}}];
  r.layoutSizingX = "FILL"; parent.appendChild(r);
}
function addSpacer(parent,h=4){const r=figma.createRectangle();r.resize(1,h);r.fills=[];parent.appendChild(r);}

addText(panel, "✍️ UX Copy Recommendations", 15, "Bold", "#FFFFFF");
addText(panel, new Date().toLocaleDateString("en-US",{year:"numeric",month:"long",day:"numeric"}), 11, "Regular", "#887799");
addDivider(panel);
// REPLACE with the screen/element being written for
addText(panel, "Context: [Screen or element name]", 12, "Medium", "#C8B8EE");
addDivider(panel);
// REPLACE each copyItems entry with actual recommended copy from the output
const copyItems = [
  { element: "CTA / Button", copy: "Recommended copy here", note: "1-line rationale" },
  { element: "Error message", copy: "Recommended copy here", note: "1-line rationale" },
  { element: "Empty state",   copy: "Recommended copy here", note: "1-line rationale" },
];
for (const item of copyItems) {
  addSpacer(panel);
  addText(panel, item.element, 11, "Bold", "#D4B8FF");
  addText(panel, "\""+item.copy+"\"", 13, "Medium", "#F0EEFF");
  addText(panel, item.note, 10, "Regular", "#887799");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Alternatives & rationale in the Claude conversation.", 10, "Regular", "#554466");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "UX copy annotation created in Figma." };
```
