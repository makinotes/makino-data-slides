---
name: makino-data-slides
invocation: user
description: "Data Slides — Turn Excel/data analysis into investor-grade HTML slide cards with narrative. ECharts + static cards, Playwright screenshot."
version: "1.1"
last_updated: "2026-04-02"
---

# Data Slides — Data tells the story

You turn data analysis results into investor-grade HTML slide cards.
Each slide is a 1080×720 card combining a **data visualization** with a **narrative takeaway** — designed to be screenshot and embedded in articles or shared standalone.

NOT a chart tool. NOT a presentation tool. This is: **Excel data → analysis conclusions → visual cards with opinions.**

## What This Is (and Isn't)

| Need | Use this? | Use instead |
|------|-----------|-------------|
| Single chart (line/bar/pie) | No | `/chart` |
| Article → navigable slide deck | No | `/article-to-slides` |
| Cover image for article | No | `/cover-gen` |
| **Data analysis → multi-slide cards with narrative, embedded in articles** | **Yes** | — |

The key difference from `/chart`: every card here has a **takeaway** (an opinion/conclusion), not just a visualization. It's "chart + judgment" as a unit.

## Input

Primary input: **Excel/CSV with structured data** (scores, rankings, time series, category breakdowns).

Also accepts:
- JSON data files
- Markdown with inline data points (tables, bullet lists with numbers)
- Verbal description of data + conclusions (least ideal, most manual)

The skill reads the data, proposes analysis angles, and generates cards. User provides or confirms the narrative direction.

## Commands

| Command | What it does |
|---------|-------------|
| `/makino-data-slides` | Interactive: ask for data + narrative, generate slides |
| `/makino-data-slides <path>` | Generate slides from a data file (Excel/CSV/JSON/markdown) |

## Slide Anatomy (5-layer structure)

Every slide follows this exact structure, top to bottom:

```
┌─────────────────────────────────────────────┐
│ hdr: title (h2, 21px 800) + sub (12px)  page│  ← 66px
│ takeaway: red left-border, 13px bold 600     │  ← 34px
│                                              │
│ body: flex column, all content here          │  ← flex:1
│   charts (ECharts) / cards / tables / grids  │
│                                              │
│ ft: 11px #64748b, centered                   │  ← 32px
└─────────────────────────────────────────────┘
```

Total height: 720px fixed. Width: 1080px fixed.
Body available = 720 - 132 = 588px. Calculate content + gaps precisely.

## Visual System

### Colors
```
blue:   #1e40af (primary, deep layer)
red:    #dc2626 (alert, negative)
green:  #059669 (positive, action layer)
amber:  #d97706 (warning, interpretation layer)
gray:   #9ca3af (neutral, news layer)
dark:   #1e293b (text primary)
mid:    #475569 (text secondary)
light:  #64748b (text tertiary)
grid:   #f1f5f9 (background lines)
axis:   #e2e8f0 (chart axes)
```

### Typography
- Font: `-apple-system, 'PingFang SC', 'Helvetica Neue', sans-serif`
- Title: 21px, weight 800
- Subtitle: 12px, #64748b
- Takeaway: 13px, weight 600, #991b1b, red left-border
- Body text: 11-13px range
- Footer: 11px, #64748b, centered
- Hero numbers: 32-40px, weight 800

### Watermark
Every slide has a `::before` pseudo-element watermark:
- Content: brand name repeated 40 times
- Diagonal rotation: -25deg
- Color: rgba(0,0,0,0.03) — barely visible
- z-index: 999, pointer-events: none

### Card Components

| Component | Use for | CSS class |
|-----------|---------|-----------|
| Hero card (hc) | Big numbers with label | gradient bg, white text, 32-40px number |
| Stat box | Key metrics row | #f8fafc bg, border, centered |
| Insight card (ic) | Small data callouts | 20-24px number + 9px label |
| Layer bar (lbar) | Proportion visualization | flex-based horizontal segments |
| Mini table | Ranked data | 11px, striped rows, blue header |
| Finding card | Text insight with border | colored left-border, bg tint |

### Chart Types (ECharts)

| Chart | When to use | Key settings |
|-------|-------------|-------------|
| Scatter/Bubble | Distribution, 2D positioning | symbolSize by volume, color by category |
| Grouped Bar | Time comparison, category comparison | barCategoryGap 25%, rounded tops |
| Stacked Bar | Composition breakdown | stack property, right-side total label |
| Progress Bar | Percentage completion | Pure HTML div, no ECharts needed |
| Proportion Bar | Part-of-whole | flex-based, colored segments |

ECharts config defaults:
```js
grid: { left: 50, right: 30, top: 14, bottom: 50 }
animation: false  // always disable for screenshot
axisLabel: { fontSize: 11, color: '#64748b' }
splitLine: { lineStyle: { color: '#f1f5f9' } }
```

## Execution

### Phase 1: Understand the data

1. Read the data source (JSON/CSV/markdown/user description)
2. Identify key metrics, comparisons, trends, distributions
3. Propose slide outline: how many slides, what each one covers
4. Get user approval

### Phase 2: Generate HTML

1. Create single HTML file with all slides
2. Include ECharts CDN: `https://cdn.jsdelivr.net/npm/echarts@5.5.0/dist/echarts.min.js`
3. Each slide is a `<div class="slide">` with the 5-layer structure
4. Charts render via `DOMContentLoaded` with 50ms delay
5. Calculate body content height precisely: measure each element, compute gaps

### Phase 3: Review and iterate

1. Open in browser (start local server if file:// blocked)
2. Screenshot each slide for user review
3. Iterate on feedback: spacing, data accuracy, readability

### Phase 4: Screenshot

1. Use Playwright to screenshot each slide at native 1080×720
2. Save to user-specified directory
3. Naming convention: `slide_{N}_{slug}.png`

```javascript
// Playwright screenshot pattern
const slide = page.locator('.slide').nth(i);
await slide.scrollIntoViewIfNeeded();
await page.waitForTimeout(500);
await slide.screenshot({ path: outputPath, type: 'png' });
```

## Layout Math (critical)

Every layout decision must be calculated, not guessed.

Slide = 720px total:
- hdr: ~66px (title size dependent)
- takeaway: ~34px
- body: remaining (flex:1)
- ft: ~32px

Body content calculation:
```
available = 720 - hdr - takeaway - ft
content = sum(card heights) + sum(gaps)
remaining = available - content
```

If remaining > 60px: use `justify-content: space-evenly` to distribute.
If remaining < 20px: reduce card padding or font sizes.
Never leave unbalanced whitespace (top heavy or bottom heavy).

## Data Accuracy Rules

- Every number on a slide must trace back to the source data
- Cross-check totals: if parts shown, verify they sum correctly
- If a number appears on multiple slides, it must be identical everywhere
- Footer can provide context but must not introduce new data claims
- When data is approximate, say so (e.g. "~40%" not "40%")

## File Structure

Output: single self-contained HTML file.
- All CSS inline in `<style>` block
- All JS inline in `<script>` block
- Only external dependency: ECharts CDN
- No images, no external CSS, no build step

## Error Warnings

- Do NOT use `animation: true` in ECharts — screenshots capture mid-animation
- Do NOT set fixed `height` on body unless content fills it — use `flex:1`
- Do NOT mix `gap` with `justify-content: space-evenly` — pick one spacing strategy
- Do NOT forget `overflow:hidden` on `.slide` — content must not escape the card
- Test data label overlap on dense charts — use `labelLayout` or hide low-value labels
