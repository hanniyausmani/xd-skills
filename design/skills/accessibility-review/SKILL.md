---
name: accessibility-review
description: Audit designs and code for WCAG 2.1 AA compliance. Trigger with "is this accessible", "accessibility check", "WCAG audit", "can screen readers use this", "color contrast", or when the user asks about making designs or code accessible to all users.
---

# Accessibility Review

Evaluate designs and implementations against WCAG 2.1 AA standards.

## WCAG 2.1 AA Quick Reference

### Perceivable
- **1.1.1** Non-text content has alt text
- **1.3.1** Info and structure conveyed semantically
- **1.4.3** Contrast ratio >= 4.5:1 (normal text), >= 3:1 (large text)
- **1.4.11** Non-text contrast >= 3:1 (UI components, graphics)

### Operable
- **2.1.1** All functionality available via keyboard
- **2.4.3** Logical focus order
- **2.4.7** Visible focus indicator
- **2.5.5** Touch target >= 44x44 CSS pixels

### Understandable
- **3.2.1** Predictable on focus (no unexpected changes)
- **3.3.1** Error identification (describe the error)
- **3.3.2** Labels or instructions for inputs

### Robust
- **4.1.2** Name, role, value for all UI components

## Common Issues

1. Insufficient color contrast
2. Missing form labels
3. No keyboard access to interactive elements
4. Missing alt text on meaningful images
5. Focus traps in modals
6. Missing ARIA landmarks
7. Auto-playing media without controls
8. Time limits without extension options

## Testing Approach

1. Automated scan (catches ~30% of issues)
2. Keyboard-only navigation
3. Screen reader testing (VoiceOver, NVDA)
4. Color contrast verification
5. Zoom to 200% — does layout break?

## Output: Always Write to Figma

When a Figma URL or node is provided, **always** output the accessibility review directly onto the Figma canvas using `mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma`. Do NOT output as text in the conversation — place it in Figma instead. Only fall back to text output if no Figma file is available.

### Figma Panel Rules

- Place the panel to the **right** of the target node, with an 80px gap
- Use a **white background** (`rgb(255,255,255)`) — never dark backgrounds; text becomes invisible on dark
- Use **absolute positioning only** — do NOT use `layoutMode = "VERTICAL"` auto-layout. Auto-layout frames collapse to height 10 in the Plugin API and are unreliable
- **CRITICAL text sizing fix**: Always set `t.textAutoResize = "HEIGHT"` BEFORE calling `t.resize(fixedWidth, t.height)`. If you call `resize(w, 999)` first, the node gets stuck at height 999
- After building all content, call `panel.resize(PW, cy + PAD)` to set the final height
- Delete any existing "♿ Accessibility Review" frame before placing a new one
- Call `figma.viewport.scrollAndZoomIntoView([targetNode, panel])` so both are visible

### Panel Structure (stack top-to-bottom using a `cy` Y-cursor)

1. **"Accessibility Review" pill badge** — red text on light red bg, pill shape (cornerRadius 100)
2. **Screen/page title** — 24px Bold, near-black
3. **Subtitle** — 13px Regular, muted: "WCAG 2.1 AA audit across contrast, keyboard, semantics & touch targets"
4. **Horizontal divider**
5. **Overall result card** — a single wide card showing pass/partial/fail verdict with a brief summary sentence
6. **Horizontal divider**
7. **Four sections** in this order: Perceivable, Operable, Understandable, Robust. Each section contains:
   - A 3px-wide accent bar + section title (14px Semi Bold)
   - Findings cards, each with: a colour-coded pill tag (✅ Pass / ⚠ Needs Attention / ❌ Fail), the WCAG criterion (e.g. "1.4.3 — Contrast"), and the specific finding as body text
8. **"Recommendations" section** — prioritised list of actionable fixes (High / Medium / Low priority tags)

### Colour Palette

```
bg:    rgb(255,255,255)   // panel background — always white
surf:  rgb(246,246,251)   // card/item surface
line:  rgb(218,218,236)   // dividers
h1:    rgb(12,12,30)      // primary headings
h2:    rgb(35,35,65)      // item labels
body:  rgb(70,70,105)     // body text
muted: rgb(135,135,175)   // subtitles, metadata
red:   rgb(188,35,35)     // fail / high priority / header accent
amb:   rgb(158,108,0)     // needs attention / medium priority
grn:   rgb(22,150,70)     // pass / low priority
pur:   rgb(100,50,230)    // recommendations section accent
rgb_:  rgb(254,222,222)   // red pill background
agb:   rgb(255,242,195)   // amber pill background
ggb:   rgb(213,250,226)   // green pill background
pgb:   rgb(237,228,255)   // purple pill background
```

### Working mkT Helper — Copy Exactly

The order of operations matters. Set `textAutoResize` before `resize()`:

```js
function mkT(str, size, style, color, fixW) {
  const t = figma.createText();
  t.fontName   = { family: "Inter", style };
  t.fontSize   = size;
  t.fills      = [{ type: "SOLID", color }];
  t.characters = str;
  if (fixW) {
    t.textAutoResize = "HEIGHT";   // MUST come before resize()
    t.resize(fixW, t.height);     // sets width; height auto-calculates correctly
  }
  return t; // t.height is now the real rendered height — safe to use in layout math
}
```

### Card Sizing — Use Actual Text Heights

Calculate card height from real text heights, then create the frame at that exact size. Never rely on auto-layout:

```js
const pill = mkPill(tagLabel, tagTc, tagBg); // always 20px tall
const lbl  = mkT(criterion, 13, "Semi Bold", C.h2,  IW - 32);
const bod  = mkT(finding,   12, "Regular",   C.body, IW - 32);
const iH   = 12 + pill.height + 6 + lbl.height + 5 + bod.height + 12;
const card = figma.createFrame();
card.resize(IW, iH);
card.fills        = [{ type: "SOLID", color: C.surf }];
card.cornerRadius = 8;
card.clipsContent = false;
pill.x = 16; pill.y = 12;
lbl.x  = 16; lbl.y  = 12 + pill.height + 6;
bod.x  = 16; bod.y  = 12 + pill.height + 6 + lbl.height + 5;
card.appendChild(pill); card.appendChild(lbl); card.appendChild(bod);
card.x = PAD; card.y = cy;
panel.appendChild(card);
cy += iH + 8;
```

### Cleanup & Finish

```js
// Before building — remove any old panel
figma.currentPage.children.forEach(n => {
  if (n.name === "♿ Accessibility Review") n.remove();
});

// After building — size panel and zoom to show both
panel.resize(PW, cy + PAD);
panel.x = targetNode.x + targetNode.width + 80;
panel.y = targetNode.y;
figma.currentPage.appendChild(panel);
figma.viewport.scrollAndZoomIntoView([targetNode, panel]);
```

### Fonts to Load

Always load these before creating any text nodes:

```js
await figma.loadFontAsync({ family: "Inter", style: "Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" });
await figma.loadFontAsync({ family: "Inter", style: "Regular" });
```
