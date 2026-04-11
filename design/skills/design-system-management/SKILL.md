---
name: design-system-management
description: >
  Audit, document, or extend a design system — including component inventories,
  token mapping, and pattern documentation. Use this skill whenever the user
  mentions "design system", "component library", "design tokens", "style guide",
  "token gap", or asks to check whether a design is using a specific UI library
  (e.g. Nuxt UI, shadcn, MUI, Chakra). Also trigger when a Figma URL is
  provided alongside a reference to a component library, coding framework, or
  brand standards — even if the user doesn't use the phrase "design system".
---

# Design System Management

Audit, document, and evolve design systems. When a Figma file is involved, the
primary deliverable is **an annotated audit placed directly on the Figma canvas**
— numbered markers on the design itself, paired with a matching audit panel to
its right. This makes findings immediately navigable: the designer looks at a
number on their screen and jumps straight to the explanation.

---

## Workflow

### 1 · Understand the scope

Before touching Figma, establish:
- **What design system** is the design meant to use? (Nuxt UI, shadcn/ui, MUI,
  a custom in-house system, etc.) Ask if not stated.
- **Which frame(s)** to audit. If a node-id is in the URL, start there.
- **What kind of audit** — component alignment, token gaps, documentation
  coverage, or all three?

### 2 · Inventory the design

Switch to the correct page (`figma.setCurrentPageAsync`) and do this in one
`use_figma` call to avoid losing page context between calls:

```js
const instances = frame.findAll(n => n.type === "INSTANCE");
const uniqueComponents = [...new Set(instances.map(n => n.name))].sort();
// also collect absoluteBoundingBox for each component you'll annotate later
```

Use `get_screenshot` or `get_design_context` to visually inspect the frame so
you understand the layout before writing findings.

### 3 · Map components to the design system

For each unique component name, decide:

| Status | Meaning | Marker colour |
|--------|---------|---------------|
| ✅ Native | Directly maps to a design system component | 🟢 Green `#16a34a` |
| ⚠️ Custom / needs docs | Compound or bespoke — built on DS primitives but undocumented | 🟡 Amber `#f59e0b` |
| 🚨 Token gap | Uses hardcoded values instead of design tokens | 🔴 Red `#ef4444` |

Aim for 6–12 annotated findings — enough to be thorough without cluttering
the canvas.

### 4 · Place numbered annotation markers on the design

Create a **transparent overlay frame** at the exact same position and size as
the target design frame, then place numbered circle markers inside it at the
relative coordinates of each finding. Because the overlay has no fill and
`clipsContent = false`, it floats above the design without obscuring it.

```js
// ── Overlay (transparent, same bounds as design frame) ───────────────────
const overlay = figma.createFrame();
overlay.name = "🔢 DS Annotations";
overlay.resize(frameW, frameH);
overlay.x = frameAbsX;   // absolute canvas X of the design frame
overlay.y = frameAbsY;   // absolute canvas Y of the design frame
overlay.fills = [];       // transparent — does not cover the design
overlay.clipsContent = false;

// ── Marker factory ────────────────────────────────────────────────────────
// relX/relY are the component's position RELATIVE to the design frame
function mkMarker(num, fillRgb, relX, relY, label) {
  const f = figma.createFrame();
  f.name = `#${num} ${label}`;
  f.resize(28, 28);
  f.fills = [{ type: "SOLID", color: fillRgb }];
  f.cornerRadius = 100;
  f.clipsContent = false;
  f.effects = [{
    type: "DROP_SHADOW",
    color: { r:0, g:0, b:0, a:.35 },
    offset: { x:0, y:1 }, radius: 3, spread: 0,
    visible: true, blendMode: "NORMAL"
  }];
  const t = figma.createText();
  t.fontName = { family: "Inter", style: "Bold" };
  t.fontSize = num >= 10 ? 9 : 11;
  t.fills = [{ type: "SOLID", color: { r:1, g:1, b:1 } }];
  t.characters = String(num);
  t.textAutoResize = "WIDTH_AND_HEIGHT";
  t.x = Math.round((28 - t.width) / 2);
  t.y = Math.round((28 - t.height) / 2);
  f.appendChild(t);
  f.x = relX - 14;  // centre the 28px circle on the target point
  f.y = relY - 14;
  overlay.appendChild(f);
}
```

**Getting relative coordinates:**
```js
const frameAbsX = frame.absoluteBoundingBox.x;
const frameAbsY = frame.absoluteBoundingBox.y;
const comp = frame.findAll(n => n.name === "MyComponent")[0];
const relX = comp.absoluteBoundingBox.x - frameAbsX;
const relY = comp.absoluteBoundingBox.y - frameAbsY;
// Place marker at top-left of component, 14px inset
mkMarker(1, { r:.086, g:.588, b:.275 }, relX + 14, relY + 14, "MyComponent");
```

**Positioning tips:**
- Place markers at the **top-left corner** of the component (10–14px inset)
- When components are close together, stagger to the right edge or just outside
  the bounding box to avoid overlap
- Always `figma.currentPage.appendChild(overlay)` after adding all markers

### 5 · Build the audit panel

Place the audit panel **80px to the right** of the design frame's right edge,
aligned to the same top edge.

```js
panel.x = frameAbsX + frameW + 80;
panel.y = frameAbsY;
```

#### Panel structure (top to bottom, using a `cy` Y-cursor)

1. **Header badge** — `"🔬  Design System Audit · [Library Name]"` in teal
2. **Frame title** — 22px Bold
3. **Subtitle** — `"Numbers ① – ⑩ correspond to markers on the design →"`
   This line tells the designer how to navigate between the markers and the panel.
4. **Component count** — e.g. `"2,764 instances · 67 unique components · Nuxt UI v3"`
5. **Legend row** — three coloured dots: ✅ Native / ⚠️ Needs docs / 🚨 Token gap
6. **Horizontal divider**
7. **Finding rows** grouped by status (Native first, then Custom, then Token gaps)
8. **Top 3 recommendations** at the bottom

#### Finding row layout

Each row is a rounded surface card containing:

```
┌─────────────────────────────────────────────────────┐
│  [●N]  [⚠ Category pill]                            │
│        Component Name (13px Semi Bold)               │
│        <code-token> (small purple inline chip)       │
│        Explanation note (12px Regular)               │
└─────────────────────────────────────────────────────┘
```

`[●N]` is a 22×22px filled circle matching the canvas marker colour.
The code token chip uses a lavender background with dark-purple text.

```js
function mkNumBadge(num, fillRgb) {
  const f = figma.createFrame();
  f.resize(22, 22);
  f.fills = [{ type: "SOLID", color: fillRgb }];
  f.cornerRadius = 100;
  f.clipsContent = false;
  const t = figma.createText();
  t.fontName = { family: "Inter", style: "Bold" };
  t.fontSize = num >= 10 ? 9 : 10;
  t.fills = [{ type: "SOLID", color: { r:1, g:1, b:1 } }];
  t.characters = String(num);
  t.textAutoResize = "WIDTH_AND_HEIGHT";
  t.x = Math.round((22 - t.width) / 2);
  t.y = Math.round((22 - t.height) / 2);
  f.appendChild(t);
  return f;
}

function mkCode(str) {
  const f = figma.createFrame();
  f.resize(10, 22);
  f.fills = [{ type: "SOLID", color: { r:.945, g:.929, b:.992 } }]; // lavender
  f.cornerRadius = 4;
  f.clipsContent = false;
  const t = figma.createText();
  t.fontName = { family: "Inter", style: "Regular" };
  t.fontSize = 11;
  t.fills = [{ type: "SOLID", color: { r:.176, g:.098, b:.392 } }]; // dark purple
  t.characters = str;
  t.x = 6; t.y = 4;
  f.appendChild(t);
  f.resize(t.width + 12, 22);
  return f;
}
```

**Card sizing — never use auto-layout:**

```js
const numBadge = mkNumBadge(num, fillRgb);
const pill     = mkPill(tagLabel, tagTc, tagBg);
const nm       = mkT(compName, 13, "Semi Bold", C.h2,  IW - 48);
const cod      = mkCode(tokenStr);
const bod      = mkT(noteText, 12, "Regular",   C.body, IW - 48);

const bodyStartY = 14 + Math.max(numBadge.height, nm.height) + 5 + cod.height + 6;
const totalH     = bodyStartY + bod.height + 14;
const card = figma.createFrame();
card.resize(IW, totalH);
card.fills = [{ type: "SOLID", color: C.surf }];
card.cornerRadius = 8;
card.clipsContent = false;

numBadge.x = 12;  numBadge.y = 14;
pill.x     = 12;  pill.y     = 14 + numBadge.height + 4;
nm.x       = 42;  nm.y       = 14;
cod.x      = 42;  cod.y      = 14 + nm.height + 5;
bod.x      = 42;  bod.y      = bodyStartY;

[numBadge, pill, nm, cod, bod].forEach(n => card.appendChild(n));
card.x = PAD; card.y = cy;
panel.appendChild(card);
cy += totalH + 6;
```

**Critical text-sizing rule:** always call `t.textAutoResize = "HEIGHT"` **before**
`t.resize(fixedWidth, t.height)` — if you resize first, the height gets stuck.

All child positioning uses explicit `x` / `y`. Never set `layoutMode = "VERTICAL"`
on any frame — auto-layout frames collapse to height 10 in the Plugin API.

### 6 · Finalise and zoom

```js
panel.resize(PW, cy + PAD);
figma.currentPage.appendChild(overlay);   // annotation markers
figma.currentPage.appendChild(panel);     // audit panel
figma.viewport.scrollAndZoomIntoView([targetFrame, overlay, panel]);
```

**Cleanup at the start of each run** — remove stale frames so re-running replaces
rather than duplicates:

```js
figma.currentPage.children.forEach(n => {
  if (n.name === "🔢 DS Annotations" || n.name === "🔬 DS Audit") n.remove();
});
```

---

## Colour palette

```
bg:      rgb(255,255,255)   panel background — always white
surf:    rgb(246,246,251)   card surface
line:    rgb(218,218,236)   dividers
h1:      rgb(12,12,30)      primary headings
h2:      rgb(35,35,65)      item labels
body:    rgb(70,70,105)     body text
muted:   rgb(135,135,175)   subtitles, metadata
teal:    rgb(0,128,128)     DS audit badge
tgb:     rgb(224,246,246)   teal pill background

Marker / status colours (used on both canvas markers and panel badges):
gnfill:  rgb(22,163,74)     ✅ green  — native / confirmed
amfill:  rgb(234,147,6)     ⚠️ amber  — custom / needs docs
refill:  rgb(220,50,50)     🚨 red    — token gap
white:   rgb(255,255,255)   number text inside markers
```

---

## Design system reference

### Design Tokens
Colors (brand, semantic, neutral), typography scale, spacing, border radii,
shadows (elevation), motion (durations, easings).

### Components
Reusable UI elements with variants, states, sizes, behaviour, and accessibility.

### Patterns
Composed solutions: forms, navigation, data display, feedback (toasts, modals).

### Principles
1. **Consistency over creativity** — the system exists so teams don't reinvent the wheel
2. **Flexibility within constraints** — components should be composable, not rigid
3. **Document everything** — if it's not documented, it doesn't exist
4. **Version and migrate** — breaking changes need migration paths
