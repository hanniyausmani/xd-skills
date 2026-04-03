---
description: Run a WCAG accessibility audit on a design or page
argument-hint: "<Figma URL, URL, or description>"
---

# /accessibility

> If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../CONNECTORS.md).

Audit a design or page for WCAG 2.1 AA accessibility compliance. See the **accessibility-review** skill for WCAG criteria reference and common issues checklist.

## Usage

```
/accessibility $ARGUMENTS
```

Audit for accessibility: @$1

## Output

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

### Keyboard Navigation
| Element | Tab Order | Enter/Space | Escape | Arrow Keys |
|---------|-----------|-------------|--------|------------|
| [Element] | [Order] | [Behavior] | [Behavior] | [Behavior] |

### Screen Reader
| Element | Announced As | Issue |
|---------|-------------|-------|
| [Element] | [What SR says] | [Problem if any] |

### Priority Fixes
1. **[Critical fix]** — Affects [who] and blocks [what]
2. **[Major fix]** — Improves [what] for [who]
3. **[Minor fix]** — Nice to have
```

## If Connectors Available

If **~~design tool** is connected:
- Inspect color values, font sizes, and touch targets directly from Figma
- Check component ARIA roles and keyboard behavior in the design spec

If **~~project tracker** is connected:
- Create tickets for each accessibility finding with severity and WCAG criterion
- Link findings to existing accessibility remediation epics

## Tips

1. **Start with contrast and keyboard** — These catch the most common and impactful issues.
2. **Test with real assistive technology** — My audit is a great start, but manual testing with VoiceOver/NVDA catches things I can't.
3. **Prioritize by impact** — Fix issues that block users first, polish later.

## Writing Results to Figma

**After generating the output above, always write a summary annotation directly into the Figma file** when a Figma URL was provided. This keeps findings visible alongside the design for side-by-side review without switching to Claude.

Use the `use_figma` tool with the same `fileKey`. Extract the `nodeId` from the URL (e.g. `node-id=7-2` → `"7:2"`). Run this JavaScript, replacing placeholder values with real data from your audit:

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
// REPLACE with real counts from your audit output
addText(panel, "Total: X issues  •  🔴 Critical: X  •  🟡 Major: X  •  🟢 Minor: X", 12, "Medium", "#C8C8EE");
addDivider(panel);
// REPLACE items[] with actual findings, ≤100 chars each
const findings = [
  { label: "🔴 Critical", color: "#FF7070", items: ["Finding — WCAG X.X.X", "Finding — WCAG X.X.X"] },
  { label: "🟡 Major",    color: "#F2C94C", items: ["Finding — WCAG X.X.X"] },
  { label: "🟢 Minor",    color: "#6FCF97", items: ["Finding — WCAG X.X.X"] },
];
for (const s of findings) {
  addSpacer(panel);
  addText(panel, s.label, 12, "Bold", s.color);
  for (const item of s.items) addText(panel, "• "+item, 11, "Regular", "#AAAACE");
}
addSpacer(panel,2); addDivider(panel);
addText(panel, "Full detail in the Claude conversation.", 10, "Regular", "#555577");
figma.viewport.scrollAndZoomIntoView([panel, ...(targetNode ? [targetNode] : [])]);
return { panelId: panel.id, message: "Audit annotation created in Figma." };
```
