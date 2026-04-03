---
description: Write or review UX copy — microcopy, error messages, empty states, CTAs
argument-hint: "<Figma URL or context description>"
allowed-tools: mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_design_context, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_screenshot, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma
---

# /ux-copy

Write or review UX copy for any interface context. See the **ux-writing** skill for copy principles, patterns, and voice/tone guidance.

## Step 1 — Read the Figma Design (if URL provided)

If a Figma URL is provided in $ARGUMENTS:

1. Extract the **file key** from the URL — it's the string after `/design/` or `/file/` and before the next `/`. Example: `https://www.figma.com/design/ABC123xyz/my-file` → file key is `ABC123xyz`
2. Extract the **node ID** from the URL — find `node-id=` in the URL params, take the value, and replace `-` with `:`. Example: `node-id=7-2` → node ID is `7:2`. Store this exact string for Step 3.
3. Call `get_design_context` to read the screen layout, understand the user flow, and check character constraints.
4. Call `get_screenshot` to view the design in context.

If no Figma URL is provided, ask the user for:
- **Context**: What screen, flow, or feature?
- **User state**: What is the user trying to do? How are they feeling?
- **Tone**: Formal, friendly, playful, reassuring?
- **Constraints**: Character limits, platform guidelines?

## Step 2 — Generate Copy Recommendations

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

## Step 3 — Write the Annotation to Figma

**This step is mandatory whenever a Figma URL was provided. Do not skip it.**

Use `use_figma` with the file key from Step 1. In the JavaScript below:
- Replace `"NODE_ID_HERE"` with the node ID string you extracted in Step 1 (e.g. `"7:2"`)
- Replace all `[placeholder]` values with the actual recommended copy from Step 2

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
addText(panel, "Context: [Screen or element name from design]", 12, "Medium", "#C8B8EE");
addDivider(panel);
const copyItems = [
  { element: "[Element name, e.g. CTA Button]", copy: "[Recommended copy]", note: "[1-line rationale]" },
  { element: "[Element name, e.g. Error message]", copy: "[Recommended copy]", note: "[1-line rationale]" },
  { element: "[Element name, e.g. Empty state]", copy: "[Recommended copy]", note: "[1-line rationale]" },
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
return { panelId: panel.id, message: "UX copy annotation created next to frame in Figma." };
```
