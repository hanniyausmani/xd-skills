---
description: Generate developer handoff specs from a design
argument-hint: "<Figma URL or design description>"
---

# /handoff

> If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../CONNECTORS.md).

Generate comprehensive developer handoff documentation from a design. See the **design-handoff** skill for guidance on what to include and handoff principles.

## Usage

```
/handoff $ARGUMENTS
```

Generate handoff specs for: @$1

If a Figma URL is provided, pull the design from Figma. Otherwise, work from the provided description or screenshot.

## Output

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

### Animation / Motion
| Element | Trigger | Animation | Duration | Easing |
|---------|---------|-----------|----------|--------|
| [Element] | [Trigger] | [Description] | [ms] | [easing] |

### Accessibility Notes
- [Focus order]
- [ARIA labels needed]
- [Keyboard interactions]
```

## If Connectors Available

If **~~design tool** is connected:
- Pull exact measurements, tokens, and component specs from Figma
- Export assets and generate a complete spec sheet

If **~~project tracker** is connected:
- Link the handoff to the implementation ticket
- Create sub-tasks for each section of the spec

## Tips

1. **Share the Figma link** — I can pull exact measurements, tokens, and component info.
2. **Mention edge cases** — "What happens with 100 items?" helps me spec boundary conditions.
3. **Specify the tech stack** — "We use React + Tailwind" helps me give relevant implementation notes.

## Writing Results to Figma

**After generating the handoff spec above, always write a summary annotation directly into the Figma file** when a Figma URL was provided. Gives developers a quick reference in Figma without needing to switch to Claude.

Use the `use_figma` tool with the same `fileKey`. Extract the `nodeId` from the URL (e.g. `node-id=7-2` → `"7:2"`). Run this JavaScript, replacing placeholder values with real spec data:

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
// REPLACE with the screen/component name and 1-line description
addText(panel, "Screen: [Name] — [1-line description]", 12, "Medium", "#C8D8EE");
addDivider(panel);
// REPLACE items[] with key spec points from handoff output, ≤100 chars each
const sections = [
  { label: "🎨 Tokens", color: "#7EB8F7", items: ["color-primary: #HEX — CTAs", "spacing-md: Xpx — gaps"] },
  { label: "📐 Layout", color: "#A78BFA", items: ["Grid: X cols, Xpx gutter", "Responsive: [key change]"] },
  { label: "🔁 States", color: "#F2C94C", items: ["Hover: [behavior]", "Error: [behavior]"] },
  { label: "♿ A11y",   color: "#6FCF97", items: ["Focus order: [desc]", "ARIA: [labels needed]"] },
];
for (const s of sections) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AABBCC");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full spec in the Claude conversation.", 10, "Regular", "#445566");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Handoff annotation created in Figma." };
```
