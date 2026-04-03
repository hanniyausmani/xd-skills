---
description: Generate developer handoff specs from a design
argument-hint: "<Figma URL or design description>"
allowed-tools: mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_design_context, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_metadata, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_screenshot, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_variable_defs
---

# /handoff

Generate comprehensive developer handoff specs from a Figma design. See the **design-handoff** skill for handoff principles and what to include.

## Step 1 — Read the Figma Design

If a Figma URL is provided in $ARGUMENTS:

1. Extract the **file key** from the URL — it's the string after `/design/` or `/file/` and before the next `/`. Example: `https://www.figma.com/design/ABC123xyz/my-file` → file key is `ABC123xyz`
2. Extract the **node ID** from the URL — find `node-id=` in the URL params, take the value, and replace `-` with `:`. Example: `node-id=7-2` → node ID is `7:2`. Store this exact string for Step 3.
3. Call `get_design_context` with the file key to pull exact measurements, component specs, and layer structure.
4. Call `get_variable_defs` to retrieve design tokens (colors, spacing, typography).
5. Call `get_screenshot` to view the design visually.

If no Figma URL is provided, ask the user to share one before proceeding.

## Step 2 — Generate the Handoff Spec

```markdown
## Handoff Spec: [Feature/Screen Name]

### Overview
[What this screen/feature does, user context]

### Layout
[Grid system, breakpoints, responsive behavior]

### Design Tokens Used
| Token | Value | Usage |
|-------|-------|-------|
| `color-primary` | #[hex] | CTA buttons, links |
| `spacing-md` | [X]px | Between sections |
| `font-heading-lg` | [size/weight/family] | Page title |

### Components
| Component | Variant | Props | Notes |
|-----------|---------|-------|-------|
| [Component] | [Variant] | [Props] | [Special behavior] |

### States and Interactions
| Element | State | Behavior |
|---------|-------|----------|
| [CTA Button] | Hover | [Background darken 10%] |
| [CTA Button] | Loading | [Spinner, disabled] |
| [Form] | Error | [Red border, error message below] |

### Responsive Behavior
| Breakpoint | Changes |
|------------|---------|
| Desktop (>1024px) | [Default layout] |
| Tablet (768-1024px) | [What changes] |
| Mobile (<768px) | [What changes] |

### Edge Cases
- **Empty state**: [What to show when no data]
- **Long text**: [Truncation rules]
- **Loading**: [Skeleton or spinner]
- **Error**: [Error state appearance]

### Accessibility Notes
- [Focus order]
- [ARIA labels needed]
- [Keyboard interactions]
```

## Step 3 — Write the Annotation to Figma

**This step is mandatory whenever a Figma URL was provided. Do not skip it.**

Use `use_figma` with the file key from Step 1. In the JavaScript below:
- Replace `"NODE_ID_HERE"` with the node ID string you extracted in Step 1 (e.g. `"7:2"`)
- Replace all `[placeholder]` values with real spec data from your handoff above

```javascript
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

const targetNode = figma.getNodeById("NODE_ID_HERE");
const xPos = targetNode ? Math.round(targetNode.x + targetNode.width + 80) : 200;
const yPos = targetNode ? targetNode.y : 200;

const panel = figma.createFrame();
panel.name = "📋 Dev Handoff — Claude";
panel.x = xPos; panel.y = yPos; panel.resize(560, 100);
panel.fills = [{ type: "SOLID", color: { r: 0.08, g: 0.11, b: 0.16 } }];
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
  r.fills = [{type:"SOLID",color:{r:0.18,g:0.22,b:0.30}}];
  r.layoutSizingX = "FILL"; parent.appendChild(r);
}
function addSpacer(parent,h=4){const r=figma.createRectangle();r.resize(1,h);r.fills=[];parent.appendChild(r);}

addText(panel, "📋 Developer Handoff Spec", 15, "Bold", "#FFFFFF");
addText(panel, new Date().toLocaleDateString("en-US",{year:"numeric",month:"long",day:"numeric"}), 11, "Regular", "#6688AA");
addDivider(panel);
addText(panel, "Screen: [Name] — [1-line description]", 12, "Medium", "#C8D8EE");
addDivider(panel);
const sections = [
  { label: "🎨 Tokens", color: "#7EB8F7", items: ["[token-name: value — usage]", "[token-name: value — usage]"] },
  { label: "📐 Layout", color: "#A78BFA", items: ["[Grid spec]", "[Responsive behavior]"] },
  { label: "🔁 States", color: "#F2C94C", items: ["[Hover: behavior]", "[Error: behavior]"] },
  { label: "♿ A11y",   color: "#6FCF97", items: ["[Focus order]", "[ARIA labels needed]"] },
];
for (const s of sections) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AABBCC");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full spec in the Claude conversation.", 10, "Regular", "#445566");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Handoff annotation created next to frame in Figma." };
```
