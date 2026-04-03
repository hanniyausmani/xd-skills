---
name: design-handoff
description: Create comprehensive developer handoff documentation from designs. Trigger with "handoff to engineering", "developer specs", "implementation notes", "design specs for developers", or when a design needs to be translated into detailed implementation guidance.
---

# Design Handoff — Visual Screen Layout

This skill creates a dedicated **"🛠 Dev Handoff" page** directly inside the Figma file, displaying all design screens organised into labelled sections on a dark background — matching the Caylent standard handoff format.

---

## What This Produces

A new Figma **page** (not a side panel — a full Figma page) containing:
1. **Cover frame** — deep navy background, large 📐 project title, designer / date / design-system metadata
2. **Section frames** — dark grey panels labelled "1 · SECTION NAME", each showing related screens at their actual design dimensions
3. **Screen-name labels** — the frame name in muted white below each screen
4. **Arrow annotations** — amber leader lines with native `ARROW_LINES` caps extending outward from the screen's right edge, each paired with a component name + spec label. Nothing is placed on top of the design.

---

## Step-by-Step Process

### Step 1 — Gather design data
- Call `get_metadata` on the target URL to read **all top-level frame names, IDs, and pixel dimensions**
- Note the Figma file name (used as the project title)
- Note today's date for "Last updated"

### Step 2 — Group frames into sections
Analyse frame names to form logical sections:
- Split on `" / "` — the text **before** the slash is the section key
  e.g. `"Dashboard / Overview"` → section **"DASHBOARD"**
- If no slash exists, group all frames under one section named after the project
- Preserve the original order frames appear in the page

### Step 3 — Infer project metadata
- **Project name**: Figma file name, or the most common frame-name prefix
- **Designer**: Pull from the file description if present; otherwise `"[Designer]"`
- **Design system**: Look for a mention in the file description; otherwise `"Custom"`
- **Date**: Use `new Date().toLocaleDateString("en-US", { month: "long", year: "numeric" })`

### Step 4 — Execute the Figma script
Use `use_figma` with the **Figma Script Template** below. Always substitute real values — never leave placeholder strings.

---

## Visual Specifications

### Cover Frame
| Property | Value |
|---|---|
| Width | `Math.max(widest section width, 1440)` px |
| Height | 248 px |
| Fill | `rgb(18, 18, 31)` — deep navy `#12121F` |
| Title | Inter Bold 48px, white — `"📐 [Project Name] — Dev Handoff"` |
| Sub-line 1 | Inter Regular 18px, white 55% opacity — `"Designer: [Name] · [Month Year] · ⚠️ DO NOT EDIT ORIGINALS"` |
| Sub-line 2 | Same style — `"Design system: [System]"` |
| Sub-line 3 | Same style — `"Last updated: [Month Year]"` |
| Text x | 48 px from left |
| First text y | 48 px, sub-lines stacked with 8 px gap |

### Section Frames
| Property | Value |
|---|---|
| Width | Same as cover (`overallW`) |
| Fill | `rgb(60, 60, 60)` — dark grey `#3C3C3C` |
| Section label | Inter Semi Bold 20px, white — `"N · SECTION NAME"` at x=80, y=44 |
| Inner padding | 80 px all sides |
| Gap between screens (horizontal) | 80 px |
| Gap between rows (vertical) | 100 px (includes room for screen label text beneath) |
| Screen name label | Inter Regular 13px, white 45% opacity, 10 px below screen, aligned to screen's x |

### Screens Per Row
| Screen width | Columns |
|---|---|
| ≥ 1200 px | 1 |
| 600 – 1199 px | 2 |
| 300 – 599 px | 3 |
| < 300 px | 4 |

### Spacing Between Sections
80 px gap between consecutive sections, and between cover and first section.

---

## Figma Script Template

```javascript
(async () => {
  await figma.loadFontAsync({ family: "Inter", style: "Bold" });
  await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" });
  await figma.loadFontAsync({ family: "Inter", style: "Regular" });

  // ── CONFIG — substitute real values from get_metadata ──────────────
  const PROJECT_NAME  = "CryptoVault Dashboard";  // ← replace with real name
  const DESIGNER_NAME = "Hanniya Usmani";          // ← replace with real designer
  const DESIGN_SYS    = "Custom";                  // ← replace with real design system
  const MONTH_YEAR    = new Date().toLocaleDateString("en-US", { month: "long", year: "numeric" });

  // ── COLOURS ────────────────────────────────────────────────────────
  const NAVY  = { r: 18/255, g: 18/255, b: 31/255 };
  const DGREY = { r: 60/255, g: 60/255, b: 60/255 };
  const WHITE = { r: 1, g: 1, b: 1 };
  const solidW = [{ type: "SOLID", color: WHITE }];
  const mutedW = [{ type: "SOLID", color: WHITE, opacity: 0.55 }];
  const dimW   = [{ type: "SOLID", color: WHITE, opacity: 0.45 }];

  // ── TEXT HELPER ────────────────────────────────────────────────────
  function mkT(str, size, style, fills, fixW) {
    const t = figma.createText();
    t.fontName   = { family: "Inter", style };
    t.fontSize   = size;
    t.fills      = fills;
    t.characters = str;
    if (fixW) {
      t.textAutoResize = "HEIGHT";   // MUST be set before resize()
      t.resize(fixW, t.height);
    }
    return t;
  }

  // ── GATHER SOURCE FRAMES ───────────────────────────────────────────
  const sourcePage   = figma.currentPage;
  const sourceFrames = sourcePage.children.filter(n =>
    (n.type === "FRAME" || n.type === "COMPONENT") &&
    !n.name.startsWith("🛠") &&
    !n.name.startsWith("🎨") &&
    !n.name.startsWith("♿")
  );

  if (sourceFrames.length === 0) {
    figma.notify("No design frames found on this page.", { error: true });
    return;
  }

  // ── GROUP BY SECTION ───────────────────────────────────────────────
  const groupOrder = [];
  const groups     = new Map();
  for (const frame of sourceFrames) {
    const key = frame.name.includes(" / ")
      ? frame.name.split(" / ")[0].trim().toUpperCase()
      : PROJECT_NAME.toUpperCase();
    if (!groups.has(key)) { groups.set(key, []); groupOrder.push(key); }
    groups.get(key).push(frame);
  }

  // ── LAYOUT CONSTANTS ───────────────────────────────────────────────
  const S_PAD   = 80;   // section inner padding
  const H_GAP   = 80;   // horizontal gap between screens
  const V_GAP   = 100;  // vertical gap between rows (label + breathing room)
  const LBL_OFF = 10;   // gap from bottom of screen to label
  const LBL_H   = 20;   // reserved height for screen label
  const HDR_H   = 80;   // section header area height

  function colsFor(w) {
    if (w >= 1200) return 1;
    if (w >= 600)  return 2;
    if (w >= 300)  return 3;
    return 4;
  }

  function sectionDims(frames) {
    const maxW  = Math.max(...frames.map(f => f.width));
    const maxH  = Math.max(...frames.map(f => f.height));
    const cols  = Math.min(frames.length, colsFor(maxW));
    const rows  = Math.ceil(frames.length / cols);
    const contW = cols * maxW + (cols - 1) * H_GAP;
    const contH = rows * (maxH + LBL_OFF + LBL_H) + (rows - 1) * (V_GAP - LBL_OFF - LBL_H);
    const secW  = S_PAD * 2 + contW;
    const secH  = HDR_H + S_PAD + contH + S_PAD;
    return { maxW, maxH, cols, rows, secW, secH };
  }

  // ── OVERALL WIDTH ──────────────────────────────────────────────────
  const overallW = Math.max(
    1440,
    ...[...groupOrder].map(k => sectionDims(groups.get(k)).secW)
  );

  // ── FIND OR CREATE HANDOFF PAGE ────────────────────────────────────
  let hp = figma.root.children.find(p => p.name === "🛠 Dev Handoff");
  if (hp) {
    [...hp.children].forEach(c => c.remove());
  } else {
    hp = figma.createPage();
    hp.name = "🛠 Dev Handoff";
  }

  // ── COVER ──────────────────────────────────────────────────────────
  const cover = figma.createFrame();
  cover.resize(overallW, 248);
  cover.fills        = [{ type: "SOLID", color: NAVY }];
  cover.clipsContent = false;
  cover.x = 0; cover.y = 0;
  hp.appendChild(cover);

  const cTitle = mkT(`📐 ${PROJECT_NAME} — Dev Handoff`, 48, "Bold", solidW, overallW - 96);
  cTitle.x = 48; cTitle.y = 48;
  cover.appendChild(cTitle);

  let subtitleY = 48 + cTitle.height + 16;
  for (const line of [
    `Designer: ${DESIGNER_NAME} · ${MONTH_YEAR} · ⚠️ DO NOT EDIT ORIGINALS`,
    `Design system: ${DESIGN_SYS}`,
    `Last updated: ${MONTH_YEAR}`,
  ]) {
    const t = mkT(line, 18, "Regular", mutedW, overallW - 96);
    t.x = 48; t.y = subtitleY;
    cover.appendChild(t);
    subtitleY += t.height + 8;
  }

  // ── SECTIONS ───────────────────────────────────────────────────────
  let pageY = cover.height + 80;

  for (let sIdx = 0; sIdx < groupOrder.length; sIdx++) {
    const key    = groupOrder[sIdx];
    const frames = groups.get(key);
    const { maxW, maxH, cols, secH } = sectionDims(frames);

    const sec = figma.createFrame();
    sec.resize(overallW, secH);
    sec.fills        = [{ type: "SOLID", color: DGREY }];
    sec.clipsContent = false;
    sec.x = 0; sec.y = pageY;
    hp.appendChild(sec);

    // Section label
    const sLbl = mkT(`${sIdx + 1} · ${key}`, 20, "Semi Bold", solidW, overallW - 160);
    sLbl.x = S_PAD; sLbl.y = 44;
    sec.appendChild(sLbl);

    // Place clones + labels
    for (let i = 0; i < frames.length; i++) {
      const col = i % cols;
      const row = Math.floor(i / cols);
      const fx  = S_PAD + col * (maxW + H_GAP) + Math.round((maxW - frames[i].width) / 2);
      const fy  = HDR_H + S_PAD + row * (maxH + LBL_OFF + LBL_H + (V_GAP - LBL_OFF - LBL_H));

      const clone = frames[i].clone();
      clone.x = fx; clone.y = fy;
      sec.appendChild(clone);

      const nLbl = mkT(frames[i].name, 13, "Regular", dimW, frames[i].width);
      nLbl.x = fx; nLbl.y = fy + frames[i].height + LBL_OFF;
      sec.appendChild(nLbl);
    }

    pageY += secH + 80;
  }

  // ── FINISH ─────────────────────────────────────────────────────────
  await figma.setCurrentPageAsync(hp);
  figma.viewport.scrollAndZoomIntoView(hp.children);
  figma.notify(`✅ Dev Handoff page ready — ${sourceFrames.length} screen(s) in ${groupOrder.length} section(s)`);
})();
```

---

## Critical Rules

1. **Call `get_metadata` FIRST** — read actual frame names, IDs, and dimensions before running the script
2. **Substitute all CONFIG values** — replace every placeholder with real data from the metadata
3. **Run from the source design page** — the script uses `figma.currentPage` to find frames; do not navigate away before executing
4. **Font loading is mandatory** — always load all three Inter styles before creating text
5. **`textAutoResize` before `resize()`** — setting it after will lock text height incorrectly
6. **Do not use auto-layout** (`layoutMode = "VERTICAL"`) — it collapses frames unpredictably; use the absolute-position pattern above

---

## Edge Cases

| Situation | Handling |
|---|---|
| Only 1 frame on the page | Still creates full cover + 1 section — correct behaviour |
| Frames have no " / " in names | All frames go in one section named after the project |
| Page already has a "🛠 Dev Handoff" page | Script clears and rebuilds it cleanly |
| Very tall scroll views (height > 1500 px) | Clone as-is; add `"(scrollable)"` to the screen label |
| Mixed screen sizes (mobile + desktop) | Name-prefix grouping naturally separates them into sections |

---

## Arrow Annotation Pass

After the screen layout is placed, run a **second `use_figma` call** to add arrow annotations. This pass:
- Derives component positions from `get_metadata` on the source frame
- Draws amber `←→` leader lines using Figma's native `strokeCap = "ARROW_LINES"` — no manual shape construction
- Places each line **outside** the cloned screen (starting at `screenRightEdge + gap`) so nothing overlays the design
- Names every element with `"__ann_"` prefix so they can be cleanly removed/re-run

### Arrow Annotation Visual Style
| Property | Value |
|---|---|
| Line colour | Amber `rgb(251, 146, 60)` — `#FB923C` |
| Stroke weight | 1.5 px |
| Cap style | `"ARROW_LINES"` — native Figma arrow (both ends) |
| Gap from screen edge | 32 px |
| Line length | 80 px |
| Label gap after line | 14 px |
| Component name | Inter Semi Bold 12px, white 80% opacity |
| Spec text | Inter Regular 11px, white 48% opacity |
| Label column width | 340 px |

### Key Constants (must match the screen layout pass)
```javascript
const CLONE_X  = 80;   // S_PAD — where the cloned screen starts in the section
const CLONE_Y  = 168;  // HDR_H + S_PAD — vertical start of clone in section
const SCREEN_W = /* actual width of source frame */;
```

### Arrow Annotation Script Template

```javascript
(async () => {
  await figma.loadFontAsync({ family: "Inter", style: "Regular" });
  await figma.loadFontAsync({ family: "Inter", style: "Semi Bold" });

  // Navigate to handoff page
  const hp = figma.root.children.find(p => p.name === "🛠 Dev Handoff");
  if (!hp) { figma.notify("No Dev Handoff page found", { error: true }); return; }
  await figma.setCurrentPageAsync(hp);

  const AMBER  = { r: 251/255, g: 146/255, b: 60/255 };
  const WHITE  = { r: 1, g: 1, b: 1 };
  const aStroke = [{ type: "SOLID", color: AMBER }];
  const w80    = [{ type: "SOLID", color: WHITE, opacity: 0.80 }];
  const w48    = [{ type: "SOLID", color: WHITE, opacity: 0.48 }];

  function mkT(str, size, style, fills, fixW) {
    const t = figma.createText();
    t.fontName = { family: "Inter", style };
    t.fontSize = size; t.fills = fills; t.characters = str;
    if (fixW) { t.textAutoResize = "HEIGHT"; t.resize(fixW, t.height); }
    return t;
  }

  // ── Constants matching the layout pass ──────────────────────────────
  const CLONE_X  = 80;
  const CLONE_Y  = 168;
  const SCREEN_W = 1440;   // ← replace with actual source frame width
  const LINE_GAP = 32;
  const LINE_LEN = 80;
  const LBL_GAP  = 14;
  const LBL_W    = 340;
  const NEW_W    = CLONE_X + SCREEN_W + LINE_GAP + LINE_LEN + LBL_GAP + LBL_W + 60;

  // ── Annotation data ─────────────────────────────────────────────────
  // cy = vertical centre of the component within the source frame
  // Derive from get_metadata: cy = component.y + component.height / 2
  // Offset pairs that share the same cy by ±18px to avoid label overlap
  const annots = [
    { name: "Component Name",  spec: "W × H px · key spec",  cy: 100 },
    // ... add one entry per key component ...
  ];

  // ── Find section frames (dark grey fill, r ≈ 60/255) ────────────────
  const sections = hp.children.filter(n =>
    n.type === "FRAME" &&
    n.fills?.length > 0 &&
    Math.round((n.fills[0].color?.r || 0) * 255) === 60
  );

  // Widen cover to match
  const cover = hp.children.find(n =>
    n.type === "FRAME" && Math.round((n.fills?.[0]?.color?.b || 0) * 255) === 31
  );
  if (cover && cover.width < NEW_W) cover.resize(NEW_W, cover.height);

  for (const sec of sections) {
    // Remove previous annotation pass
    [...sec.children].filter(n => n.name?.startsWith("__ann")).forEach(n => n.remove());
    if (sec.width < NEW_W) sec.resize(NEW_W, sec.height);

    for (const ann of annots) {
      const ay     = CLONE_Y + ann.cy;
      const startX = CLONE_X + SCREEN_W + LINE_GAP;
      const endX   = startX + LINE_LEN;

      // Arrow line — native Figma ARROW_LINES cap (no manual shape building)
      const line = figma.createLine();
      line.name         = "__ann_line";
      line.x            = startX; line.y = ay;
      line.resize(LINE_LEN, 0);
      line.strokes      = aStroke;
      line.strokeWeight = 1.5;
      line.strokeCap    = "ARROW_LINES";   // ← built-in arrow, both ends
      sec.appendChild(line);

      // Component name
      const nl = mkT(ann.name, 12, "Semi Bold", w80, LBL_W);
      nl.name = "__ann_name"; nl.x = endX + LBL_GAP; nl.y = ay - 15;
      sec.appendChild(nl);

      // Spec text
      const sl = mkT(ann.spec, 11, "Regular", w48, LBL_W);
      sl.name = "__ann_spec"; sl.x = endX + LBL_GAP; sl.y = ay + 2;
      sec.appendChild(sl);
    }
  }

  figma.viewport.scrollAndZoomIntoView(hp.children);
  figma.notify("✅ Arrow annotations applied — design untouched");
})();
```

### Annotation Overlap Rule
When two components share the same vertical centre (e.g. a row of cards), offset their `cy` values by **±18 px** so labels don't stack. Example:
```javascript
{ name: "Price Chart",         spec: "...", cy: 402 - 18 },  // 384
{ name: "Recent Transactions", spec: "...", cy: 402 + 18 },  // 420
```

### strokeCap Note
`strokeCap = "ARROW_LINES"` applies to **both** ends of a Figma LineNode — there is no `strokeStartCap`/`strokeEndCap` on LineNode. A double-headed arrow on an annotation leader line is intentional and looks professional. Do **not** attempt to build manual arrowheads from rotated lines.
