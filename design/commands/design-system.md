---
description: Audit, document, or extend your design system
argument-hint: "[audit | document | extend] <Figma URL or component>"
allowed-tools: mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_design_context, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_metadata, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_screenshot, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_variable_defs, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__search_design_system
---

# /design-system

Manage your design system — audit for consistency, document components, or design new patterns. See the **design-system-management** skill for token naming, component structure, and design system principles.

## Usage

```
/design-system audit <Figma URL>         # Full system audit
/design-system document <Figma URL>      # Document a component
/design-system extend <Figma URL>        # Design a new pattern
```

## Step 1 — Read the Figma Design

If a Figma URL is provided in $ARGUMENTS:

1. Extract the **file key** from the URL — it's the string after `/design/` or `/file/` and before the next `/`. Example: `https://www.figma.com/design/ABC123xyz/my-file` → file key is `ABC123xyz`
2. Extract the **node ID** from the URL — find `node-id=` in the URL params, take the value, and replace `-` with `:`. Example: `node-id=7-2` → node ID is `7:2`. Store this exact string for the Figma write step.
3. Call `get_design_context` to inspect naming, variants, and component structure.
4. Call `get_variable_defs` to check token coverage and identify hardcoded values.
5. Call `get_screenshot` to view the design visually.

If no Figma URL is provided, ask the user to share one before proceeding.

## Step 2 — Generate the Output

### If audit:

```markdown
## Design System Audit

### Summary
**Components reviewed:** [X] | **Issues found:** [X] | **Score:** [X/100]

### Naming Consistency
| Issue | Components | Recommendation |
|-------|------------|----------------|
| [Inconsistent naming] | [List] | [Standard to adopt] |

### Token Coverage
| Category | Defined | Hardcoded Values Found |
|----------|---------|----------------------|
| Colors | [X] | [X] instances of hardcoded hex |
| Spacing | [X] | [X] instances of arbitrary values |
| Typography | [X] | [X] instances of custom fonts/sizes |

### Component Completeness
| Component | States | Variants | Docs | Score |
|-----------|--------|----------|------|-------|
| Button | ✅ | ✅ | ⚠️ | 8/10 |

### Priority Actions
1. [Most impactful improvement]
2. [Second priority]
3. [Third priority]
```

### If document:

```markdown
## Component: [Name]

### Description
[What this component is and when to use it]

### Variants
| Variant | Use When |
|---------|----------|
| [Primary] | [Main actions] |

### Props / Properties
| Property | Type | Default | Description |
|----------|------|---------|-------------|

### States
| State | Visual | Behavior |
|-------|--------|----------|
| Default | — | — |
| Hover | [description] | [interaction] |
| Disabled | [description] | Non-interactive |

### Accessibility
- **Role**: [ARIA role]
- **Keyboard**: [Tab, Enter, Escape behavior]
- **Screen reader**: [Announced as...]

### Do's and Don'ts
| ✅ Do | ❌ Don't |
|------|---------|
| [Best practice] | [Anti-pattern] |
```

### If extend:

```markdown
## New Component: [Name]

### Problem
[What user need or gap this component addresses]

### Proposed Design

#### Props
| Property | Type | Default | Description |
|----------|------|---------|-------------|

#### Variants
| Variant | Use When |
|---------|----------|

#### States
| State | Behavior |
|-------|----------|

#### Tokens Used
- Colors: [Which tokens]
- Spacing: [Which tokens]

### Accessibility
- **Role**: [ARIA role]
- **Keyboard**: [Expected interactions]

### Open Questions
- [Decision that needs design review]
```

## Step 3 — Write the Annotation to Figma

**This step is mandatory whenever a Figma URL was provided. Do not skip it.**

Use `use_figma` with the file key from Step 1. In the JavaScript below:
- Replace `"NODE_ID_HERE"` with the node ID string you extracted in Step 1 (e.g. `"7:2"`)
- Replace all `[placeholder]` values with real data from your output above

```javascript
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

const targetNode = figma.getNodeById("NODE_ID_HERE");
const xPos = targetNode ? Math.round(targetNode.x + targetNode.width + 80) : 200;
const yPos = targetNode ? targetNode.y : 200;

const panel = figma.createFrame();
panel.name = "🧩 Design System Audit — Claude";
panel.x = xPos; panel.y = yPos; panel.resize(560, 100);
panel.fills = [{ type: "SOLID", color: { r: 0.08, g: 0.10, b: 0.15 } }];
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
  r.fills = [{type:"SOLID",color:{r:0.18,g:0.20,b:0.30}}];
  r.layoutSizingX = "FILL"; parent.appendChild(r);
}
function addSpacer(parent,h=4){const r=figma.createRectangle();r.resize(1,h);r.fills=[];parent.appendChild(r);}

addText(panel, "🧩 Design System Audit", 15, "Bold", "#FFFFFF");
addText(panel, new Date().toLocaleDateString("en-US",{year:"numeric",month:"long",day:"numeric"}), 11, "Regular", "#6677AA");
addDivider(panel);
addText(panel, "Components: [X] reviewed  •  Issues: [X]  •  Score: [X]/100", 12, "Medium", "#B8C8EE");
addDivider(panel);
const sections = [
  { label: "🔴 Naming Issues", color: "#FF7070", items: ["[Naming issue found]"] },
  { label: "🟡 Token Gaps",    color: "#F2C94C", items: ["[X] hardcoded hex values", "[X] arbitrary spacing values"] },
  { label: "🟢 Priority Actions", color: "#6FCF97", items: ["[Priority action 1]", "[Priority action 2]"] },
];
for (const s of sections) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AABBCC");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full audit in the Claude conversation.", 10, "Regular", "#445566");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Design system annotation created next to frame in Figma." };
```
