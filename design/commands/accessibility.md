---
description: Run a WCAG accessibility audit on a design or page
argument-hint: "<Figma URL, URL, or description>"
allowed-tools: mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_design_context, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_metadata, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__get_screenshot, mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma
---

# /accessibility

Audit a design for WCAG 2.1 AA accessibility compliance. See the **accessibility-review** skill for WCAG criteria and common issues checklist.

## Step 1 — Read the Figma Design

If a Figma URL is provided in $ARGUMENTS:

1. Extract the **file key** from the URL — it's the string after `/design/` or `/file/` and before the next `/`. Example: `https://www.figma.com/design/ABC123xyz/my-file` → file key is `ABC123xyz`
2. Extract the **node ID** from the URL — find `node-id=` in the URL params, take the value, and replace `-` with `:`. Example: `node-id=7-2` → node ID is `7:2`. Store this exact string for Step 3.
3. Call `get_design_context` with the file key to inspect color values, font sizes, touch targets, and component structure.
4. Call `get_screenshot` to view the design visually.

If no Figma URL is provided, ask the user to share one before proceeding.

## Step 2 — Generate the Audit

```markdown
## Accessibility Audit: [Design/Page Name]
**Standard:** WCAG 2.1 AA | **Date:** [Date]

### Summary
**Issues found:** [X] | **Critical:** [X] | **Major:** [X] | **Minor:** [X]

### Findings

#### Perceivable
| # | Issue | WCAG Criterion | Severity | Recommendation |
|---|-------|---------------|----------|----------------|
| 1 | [Issue] | [1.4.3 Contrast] | 🔴 Critical | [Fix] |

#### Operable
| # | Issue | WCAG Criterion | Severity | Recommendation |
|---|-------|---------------|----------|----------------|
| 1 | [Issue] | [2.1.1 Keyboard] | 🟡 Major | [Fix] |

#### Understandable
| # | Issue | WCAG Criterion | Severity | Recommendation |
|---|-------|---------------|----------|----------------|
| 1 | [Issue] | [3.3.2 Labels] | 🟢 Minor | [Fix] |

#### Robust
| # | Issue | WCAG Criterion | Severity | Recommendation |
|---|-------|---------------|----------|----------------|
| 1 | [Issue] | [4.1.2 Name, Role, Value] | 🟡 Major | [Fix] |

### Color Contrast Check
| Element | Foreground | Background | Ratio | Required | Pass? |
|---------|-----------|------------|-------|----------|-------|
| [Body text] | [color] | [color] | [X]:1 | 4.5:1 | ✅/❌ |

### Priority Fixes
1. **[Critical fix]** — Affects [who] and blocks [what]
2. **[Major fix]** — Improves [what] for [who]
3. **[Minor fix]** — Nice to have
```

## Step 3 — Write the Annotation to Figma

**This step is mandatory whenever a Figma URL was provided. Do not skip it.**

Use `use_figma` with the file key from Step 1. In the JavaScript below:
- Replace `"NODE_ID_HERE"` with the node ID string you extracted in Step 1 (e.g. `"7:2"`)
- Replace all `[placeholder]` values with real findings from your audit above

```javascript
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Medium" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });

const targetNode = figma.getNodeById("NODE_ID_HERE");
const xPos = targetNode ? Math.round(targetNode.x + targetNode.width + 80) : 200;
const yPos = targetNode ? targetNode.y : 200;

const panel = figma.createFrame();
panel.name = "♿ A11y Audit — Claude";
panel.x = xPos; panel.y = yPos; panel.resize(560, 100);
panel.fills = [{ type: "SOLID", color: { r: 0.10, g: 0.09, b: 0.18 } }];
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
  r.fills = [{type:"SOLID",color:{r:0.22,g:0.22,b:0.36}}];
  r.layoutSizingX = "FILL"; parent.appendChild(r);
}
function addSpacer(parent,h=4){const r=figma.createRectangle();r.resize(1,h);r.fills=[];parent.appendChild(r);}

addText(panel, "♿ Accessibility Audit — WCAG 2.1 AA", 15, "Bold", "#FFFFFF");
addText(panel, new Date().toLocaleDateString("en-US",{year:"numeric",month:"long",day:"numeric"}), 11, "Regular", "#7777AA");
addDivider(panel);
addText(panel, "Total: [X] issues  •  🔴 Critical: [X]  •  🟡 Major: [X]  •  🟢 Minor: [X]", 12, "Medium", "#C8C8EE");
addDivider(panel);
const findings = [
  { label: "🔴 Critical", color: "#FF7070", items: ["[Critical finding — WCAG criterion]"] },
  { label: "🟡 Major",    color: "#F2C94C", items: ["[Major finding — WCAG criterion]"] },
  { label: "🟢 Minor",    color: "#6FCF97", items: ["[Minor finding — WCAG criterion]"] },
];
for (const s of findings) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AAAACE");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full audit in the Claude conversation.", 10, "Regular", "#555577");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Audit annotation created next to frame in Figma." };
```
