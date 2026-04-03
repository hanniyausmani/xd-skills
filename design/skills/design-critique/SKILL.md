---
name: design-critique
description: Evaluate designs for usability, visual hierarchy, consistency, and adherence to design principles. Trigger with "what do you think of this design", "give me feedback on", "critique this", "review this mockup", or when the user shares a design and asks for opinions.
---

# Design Critique

Provide structured, actionable design feedback.

## Critique Framework

### 1. First Impression (2 seconds)
- What draws the eye first? Is that correct?
- What's the emotional reaction?
- Is the purpose immediately clear?

### 2. Usability
- Can the user accomplish their goal?
- Is the navigation intuitive?
- Are interactive elements obvious?
- Are there unnecessary steps?

### 3. Visual Hierarchy
- Is there a clear reading order?
- Are the right elements emphasized?
- Is whitespace used effectively?
- Is typography creating the right hierarchy?

### 4. Consistency
- Does it follow the design system?
- Are spacing, colors, and typography consistent?
- Do similar elements behave similarly?

### 5. Accessibility
- Color contrast ratios
- Touch target sizes
- Text readability
- Alternative text for images

## How to Give Feedback

- **Be specific**: "The CTA competes with the navigation" not "the layout is confusing"
- **Explain why**: Connect feedback to design principles or user needs
- **Suggest alternatives**: Don't just identify problems, propose solutions
- **Acknowledge what works**: Good feedback includes positive observations
- **Match the stage**: Early exploration gets different feedback than final polish

## Output: Always Write to Figma

When a Figma URL or node is provided, **always** output the critique directly onto the Figma canvas using `mcp__c559ff4b-9d7d-4192-8308-d9036a7e621f__use_figma`. Do NOT output the critique as text in the conversation — place it in Figma instead. Only fall back to text output if no Figma file is available.

### Figma Panel Rules

- Place the critique panel to the **right** of the target node, with an 80px gap
- Use a **white background** (`rgb(255,255,255)`) — never dark backgrounds; text becomes invisible on dark
- Use **absolute positioning only** — do NOT use `layoutMode = "VERTICAL"` auto-layout on the panel or its children. Auto-layout frames collapse to height 10 in the Plugin API and are unreliable
- **CRITICAL text sizing fix**: Always set `t.textAutoResize = "HEIGHT"` BEFORE calling `t.resize(fixedWidth, t.height)`. If you call `resize(w, 999)` first, the text node gets stuck at height 999
- After building all nodes, call `panel.resize(PW, cy + PAD)` where `cy` is the final Y cursor and `PAD` is bottom padding
- Delete any existing "🎨 Design Critique" frame before placing a new one
- Call `figma.viewport.scrollAndZoomIntoView([targetNode, panel])` at the end so both are visible

### Panel Structure (stack top-to-bottom using a `cy` Y-cursor)

1. **"Design Critique" pill badge** — 11px Semi Bold purple text on light purple bg, pill shape (cornerRadius 100)
2. **Screen/page title** — 24px Bold, near-black
3. **Subtitle** — 13px Regular, muted color
4. **Horizontal divider** (1px rectangle)
5. **Score row** — three cards side by side (First Impression / Usability / Accessibility), each with a colored value and a muted label
6. **Horizontal divider**
7. **Six sections** in this order: First Impression, Usability, Visual Hierarchy, Consistency, Accessibility, Quick Wins. Each section contains:
   - A 3px-wide accent bar + section title (14px Semi Bold)
   - Feedback item cards, each with: a colour-coded pill tag (✓ Works Well / ⚠ Issue / 💡 Quick Win), a bold label (13px Semi Bold), and body text (12px Regular)

### Colour Palette

```
bg:    rgb(255,255,255)   // panel background — always white
surf:  rgb(246,246,251)   // card/item surface
line:  rgb(218,218,236)   // dividers and borders
h1:    rgb(12,12,30)      // primary headings
h2:    rgb(35,35,65)      // item labels
body:  rgb(70,70,105)     // body text
muted: rgb(135,135,175)   // subtitles, metadata
pur:   rgb(100,50,230)    // purple accent
grn:   rgb(22,150,70)     // positive / works well
amb:   rgb(158,108,0)     // warning / quick win
red:   rgb(188,35,35)     // issue
pgb:   rgb(237,228,255)   // purple pill background
ggb:   rgb(213,250,226)   // green pill background
agb:   rgb(255,242,195)   // amber pill background
rgb_:  rgb(254,222,222)   // red pill background
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
const lbl  = mkT(item.label, 13, "Semi Bold", C.h2,  IW - 32);
const bod  = mkT(item.body,  12, "Regular",   C.body, IW - 32);
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
  if (n.name === "🎨 Design Critique") n.remove();
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
