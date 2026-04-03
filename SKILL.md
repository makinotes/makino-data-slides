---
name: makino-data-slides
invocation: user
description: "Data Slides — Turn Excel/data analysis into investor-grade HTML slide cards with narrative. ECharts + static cards, Playwright screenshot."
version: "4.3"
last_updated: "2026-04-03"
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
│ hdr: padding 18px 36px 0                page│  ← ~50px
│ takeaway: margin 6px 36px 0                 │  ← ~32px
│                                              │
│ body: padding 8px 36px 0, explicit height    │  ← ~570-590px
│   charts (ECharts) / cards / tables / grids  │
│   justify-content: space-between             │
│                                              │
│ ft: padding 6px 36px 10px                    │  ← ~28px
└─────────────────────────────────────────────┘
```

Total: 1080×720px fixed.

### Spacing 铁律（从已验证的 charts_v4.html 提取）

```css
.slide .hdr       { padding: 18px 36px 0 }       /* 紧凑头部 */
.slide .takeaway  { margin: 6px 36px 0 }          /* 紧贴标题 */
.slide .body      { padding: 8px 36px 0 }         /* 最小顶部留白 */
.slide .ft        { padding: 6px 36px 10px }       /* 底部克制 */
```

### Body 填充策略（关键）

**禁止 `flex:1` + 固定 gap 组合** — 会导致内容堆在顶部、底部留白过多。

正确做法：
1. body 设显式高度（根据 hdr/takeaway/ft 实际高度计算）
2. 用 `justify-content: space-between` 或 `space-evenly` 让内容均匀撑满
3. chart 容器用 `flex:1` 吃掉剩余空间，其他元素 `flex-shrink:0`

```css
/* 典型 body 高度计算 */
.body { height: 570px; display: flex; flex-direction: column; justify-content: space-between; }
/* chart 吃剩余 */
#chart { flex: 1; width: 100%; }
/* 固定元素不缩 */
.stat-row { flex-shrink: 0; }
```

## Visual System

### Background & Shadow
```css
body { background: #1a1a2e; }                     /* 深色背景衬托白卡 */
.slide { box-shadow: 0 8px 32px rgba(0,0,0,.25); border-radius: 6px; }
```

### Colors
```js
const C = {
  blue:   '#1e40af',  // primary, deep layer
  blueL:  '#93c5fd',  // blue light (secondary bars)
  blueP:  '#dbeafe',  // blue pale (background)
  red:    '#dc2626',  // alert, negative
  green:  '#059669',  // positive, action layer
  purple: '#7c3aed',  // accent (open source etc)
  amber:  '#d97706',  // warning, interpretation layer
  gray:   '#9ca3af',  // neutral, news layer
  dark:   '#1e293b',  // text primary (body)
  mid:    '#475569',  // text secondary
  light:  '#64748b',  // text tertiary
  grid:   '#f1f5f9',  // chart grid lines
  axis:   '#e2e8f0',  // chart axis lines
};
```

### Typography

| 元素 | 字号 | 字重 | 颜色 | 备注 |
|------|------|------|------|------|
| Font family | — | — | — | `-apple-system, 'PingFang SC', 'Helvetica Neue', sans-serif` |
| Title (h2) | 21px | 800 | `#0f172a` | margin-bottom 2px |
| Subtitle (.sub) | 12px | 400 | `#64748b` | max-width 720px |
| Page number | 11px | 600 | `#94a3b8` | letter-spacing 1px, 格式 `01 / 08` |
| Takeaway | 13px | 600 | `#991b1b` | gradient bg, border-left 3px solid #dc2626, border-radius `0 6px 6px 0` |
| Body text | 11-12px | 400 | `#334155` | line-height 1.6 |
| Footer | 11px | 400 | `#64748b` | letter-spacing .3px, centered |
| Hero number (.n) | 32-40px | 800 | #fff | 40px for emphasis |
| Hero label (.l) | 10px | 400 | #fff | opacity .8 |
| Hero detail (.d) | 9px | 400 | #fff | opacity .5 |
| Stat value (.v) | 24px | 800 | per-color | `.v.blue` / `.v.red` etc |
| Stat key (.k) | 10px | 400 | `#64748b` | margin-top 2px |
| Insight number (.n) | 20px | 800 | per-color | |
| Insight label (.l) | 9px | 400 | `#64748b` | |
| Finding title | 14px | 700 | per-color | with page ref `→ P3` in 10px #94a3b8 |
| Finding text | 12px | 400 | `#334155` | line-height 1.6 |
| Section heading (in body) | 13px | 700 | `#334155` | text-align center, margin-bottom 6px |

### Takeaway 精确样式
```css
.slide .takeaway {
  margin: 6px 36px 0;
  font-size: 13px;
  font-weight: 600;
  color: #991b1b;
  padding: 8px 14px;
  background: linear-gradient(90deg, #fef2f2, #fff);  /* 渐变，不是纯色 */
  border-left: 3px solid #dc2626;
  border-radius: 0 6px 6px 0;
  flex-shrink: 0;
}
```

### Watermark
```css
.slide::before {
  content: '马奇诺  马奇诺  ...';  /* 40 repeats, double-space separated */
  position: absolute; top: 0; left: 0; right: 0; bottom: 0;
  z-index: 999; pointer-events: none;
  font-size: 16px; font-weight: 600;
  color: rgba(0,0,0,0.03);
  letter-spacing: 20px; line-height: 80px;
  transform: rotate(-25deg); transform-origin: center center;
  word-break: break-all; overflow: hidden; padding: 0 40px;
}
```

### Card Components

| Component | CSS | Key specs |
|-----------|-----|-----------|
| Hero card (.hc) | `background:linear-gradient(135deg,#1e3a5f,#1e40af)` | border-radius 10px, padding 16px 12px (or 24px 14px for emphasis) |
| Hero card variant | `background:linear-gradient(135deg,#059669,#047857)` | green variant for positive metrics |
| Stat box (.stat-box) | `background:#f8fafc; border:1px solid #e2e8f0` | border-radius 8px, padding 10px 14px, text-align center |
| Insight card (.ic) | `background:#f8fafc; border:1px solid #e2e8f0` | border-radius 8px, padding 10px 8px |
| Layer bar (.lbar) | `display:flex; overflow:hidden` | height 24px (or 32px), border-radius 5px, font 10px 700 white |
| Layer legend (.lleg) | `display:flex; gap:14px` | font-size 9px, color `#64748b`, colored square `8px` |
| Mini table (.mini-tbl) | `border-collapse:collapse` | th: `background:#1e3a5f` (深蓝), td: padding 4px 8px |
| Finding card (2×2 grid) | `grid-template-columns:1fr 1fr; gap:12px` | border-radius 8px, padding 14px 16px, border-left 3px |
| Finding badge (.fb) | `20px×20px; border-radius:4px` | background per-color, 10px 700 white |
| Progress bar | Pure HTML div | 10px height, border-radius 5px, bg `#e2e8f0` |
| Info callout | colored bg + border | border-radius 10px, padding 26px 20px |
| Summary card row | `display:flex; gap:6px` | border 1px, border-radius 6px, padding 5px 8px |

### Chart Types (ECharts)

Common axis config:
```js
const bA = {
  axisLine:  { lineStyle: { color: C.axis } },
  splitLine: { lineStyle: { color: C.grid } },
  axisLabel: { fontSize: 11, color: C.light }
};
```

| Chart | When to use | Key settings |
|-------|-------------|-------------|
| Scatter/Bubble | Distribution, 2D | symbolSize by volume, opacity .75, border white 1.5px, `labelLayout:{hideOverlap:true}` |
| Grouped Bar | Time comparison | barCategoryGap 25%, barGap 5%, borderRadius `[3,3,0,0]` |
| Stacked Bar | Composition | stack prop, label position 'right' shows total, borderRadius `[0,4,4,0]` on last segment |
| Horizontal Bar | Ranking | barWidth 18-20px, borderRadius `[0,5,5,0]`, label right bold |
| Progress Bar | Percentage | **Pure HTML**, no ECharts. 10px height, border-radius 5px |
| Proportion Bar | Part-of-whole | **Pure HTML** flex-based, colored segments |
| Zone Scatter | Quadrant/区域 | markArea with transparent fill + markLine dashed thresholds + graphic text zone labels |

ECharts grid defaults:
```js
grid: { left: 50, right: 30, top: 36, bottom: 20 }  // top 留 legend 空间
animation: false  // ALWAYS disable for screenshot
legend: { top: 4 }  // 统一放顶部，禁止 bottom（会和 bar label 重叠）
```

标注线/标注文字：
```js
// 正确：用 markLine（在 SVG 内渲染，不被 watermark z-index 遮挡）
markLine: {
  silent: true, symbol: 'none',
  lineStyle: { type: 'dashed', width: 1 },
  data: [
    { xAxis: '02-12', lineStyle: { color: '#dc2626' },
      label: { formatter: '2/12 事件', fontSize: 10, color: '#dc2626', fontWeight: 600, position: 'insideStartTop' }}
  ]
}
// 错误：graphic 硬编码像素 — 位置不准 + 被 watermark 遮挡
```

Scatter zone labels (graphic watermark style):
```js
graphic: [
  { type:'text', left:'45%', top:'12%',
    style: { text:'深度层', fontSize:28, fontWeight:900, fill:'rgba(30,64,175,0.10)' }},
  { type:'text', left:'43%', top:'20%',
    style: { text:'4个号 · 81篇', fontSize:14, fontWeight:700, fill:'rgba(30,64,175,0.08)' }}
]
```

## Execution

### Phase 1: Understand the data

1. Read the data source (JSON/CSV/markdown/user description)
2. Identify key metrics, comparisons, trends, distributions
3. Propose slide outline: how many slides, what each one covers
4. Get user approval

### Phase 2: Generate HTML

1. Create single HTML file with all slides
2. **ECharts 加载策略（双保险）**：
   - 首选：内联 ECharts。检查本地缓存 `/tmp/echarts.min.js`，存在则读取内容嵌入 `<script>` 标签
   - 如果本地缓存不存在，先下载：`curl -sL "https://cdn.jsdelivr.net/npm/echarts@5.5.0/dist/echarts.min.js" > /tmp/echarts.min.js`
   - 内联后 HTML 文件 ~1MB，但 file:// 直接打开也能渲染图表
   - 同时保留 CDN fallback：`<script src="https://cdn.jsdelivr.net/npm/echarts@5.5.0/dist/echarts.min.js"></script>`
   - 加载失败提示：如果 `typeof echarts === 'undefined'`，在图表容器内显示错误信息和 HTTP server 启动命令
3. Each slide is a `<div class="slide">` with the 5-layer structure
4. Charts render via `DOMContentLoaded` with 50ms delay
5. **每个 slide 的 body 必须显式计算高度**，用 space-between 撑满

### Phase 3: Review and iterate

1. **必须用 HTTP server 打开**（即使内联了 ECharts，部分浏览器仍可能限制 file://）：
   ```bash
   cd "$(dirname "$HTML_PATH")" && python3 -m http.server 8766 &
   open "http://localhost:8766/$(basename "$HTML_PATH")"
   ```
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

## CSS Reference Template

完整的基线 CSS（从 charts_v4.html 验证提取），每次生成直接复制：

```css
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:-apple-system,'PingFang SC','Helvetica Neue',sans-serif;background:#1a1a2e;padding:40px 0}
.slide{width:1080px;height:720px;margin:28px auto;background:#fff;border-radius:6px;overflow:hidden;box-shadow:0 8px 32px rgba(0,0,0,.25);display:flex;flex-direction:column;position:relative}
.slide::before{
  content:'马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺  马奇诺';
  position:absolute;top:0;left:0;right:0;bottom:0;z-index:999;pointer-events:none;
  font-size:16px;font-weight:600;color:rgba(0,0,0,0.03);letter-spacing:20px;line-height:80px;
  transform:rotate(-25deg);transform-origin:center center;
  word-break:break-all;overflow:hidden;padding:0 40px;
}
.slide .hdr{display:flex;justify-content:space-between;align-items:flex-start;padding:18px 36px 0;flex-shrink:0}
.slide .hdr-left h2{font-size:21px;font-weight:800;color:#0f172a;margin-bottom:2px}
.slide .hdr-left .sub{font-size:12px;color:#64748b;max-width:720px}
.slide .hdr-right{text-align:right;flex-shrink:0}
.slide .hdr-right .page{font-size:11px;color:#94a3b8;font-weight:600;letter-spacing:1px}
.slide .takeaway{margin:6px 36px 0;font-size:13px;font-weight:600;color:#991b1b;padding:8px 14px;background:linear-gradient(90deg,#fef2f2,#fff);border-left:3px solid #dc2626;border-radius:0 6px 6px 0;flex-shrink:0}
.slide .body{padding:8px 36px 0;flex:1;overflow:hidden}
.slide .ft{padding:6px 36px 10px;font-size:11px;color:#64748b;text-align:center;letter-spacing:.3px;flex-shrink:0}

/* Stat box */
.stat-box{background:#f8fafc;border:1px solid #e2e8f0;border-radius:8px;padding:10px 14px;text-align:center}
.stat-box .v{font-size:24px;font-weight:800;line-height:1}
.stat-box .v.blue{color:#1e40af} .stat-box .v.red{color:#dc2626} .stat-box .v.green{color:#059669} .stat-box .v.amber{color:#d97706}
.stat-box .k{font-size:10px;color:#64748b;margin-top:2px}

/* Mini table */
.mini-tbl{font-size:11px;border-collapse:collapse;width:100%}
.mini-tbl th{background:#1e3a5f;color:#fff;padding:5px 8px;text-align:left;font-weight:600;font-size:10px}
.mini-tbl td{padding:4px 8px;border-bottom:1px solid #f1f5f9;color:#334155}
.mini-tbl tr:nth-child(even) td{background:#f8fafc}

/* Hero cards */
.hcards{display:flex;gap:12px}
.hc{flex:1;background:linear-gradient(135deg,#1e3a5f,#1e40af);border-radius:10px;padding:16px 12px;text-align:center;color:#fff}
.hc .n{font-size:32px;font-weight:800;line-height:1}
.hc .l{font-size:10px;opacity:.8;margin-top:3px}
.hc .d{font-size:9px;opacity:.5;margin-top:1px}

/* Insight row */
.irow{display:flex;gap:10px;margin-top:10px}
.ic{flex:1;background:#f8fafc;border-radius:8px;padding:10px 8px;text-align:center;border:1px solid #e2e8f0}
.ic .n{font-size:20px;font-weight:800;line-height:1.1}
.ic .n.red{color:#dc2626} .ic .n.green{color:#059669} .ic .n.blue{color:#1e40af} .ic .n.amber{color:#d97706}
.ic .l{font-size:9px;color:#64748b;margin-top:2px}

/* Layer bar */
.lbar{display:flex;height:24px;border-radius:5px;overflow:hidden;font-size:10px;font-weight:700;color:#fff;margin:8px 0 4px}
.lbar>div{display:flex;align-items:center;justify-content:center}
.lleg{display:flex;gap:14px;font-size:9px;color:#64748b}
.lleg i{width:8px;height:8px;border-radius:2px;display:inline-block;margin-right:3px;vertical-align:middle}

/* Findings */
.flist{display:flex;flex-direction:column;gap:4px}
.fi{display:flex;align-items:center;gap:8px;font-size:12px;color:#334155;padding:5px 0;border-bottom:1px solid #f1f5f9}
.fi:last-child{border:none}
.fb{min-width:20px;height:20px;border-radius:4px;background:#1e40af;color:#fff;font-size:10px;font-weight:700;display:flex;align-items:center;justify-content:center;flex-shrink:0}
```

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
- Do NOT use `flex:1` + fixed `gap` on body — 会导致底部留白过多。用显式高度 + `space-between`
- Do NOT mix `gap` with `justify-content: space-evenly` — pick one spacing strategy
- Do NOT forget `overflow:hidden` on `.slide` — content must not escape the card
- Test data label overlap on dense charts — use `labelLayout:{hideOverlap:true}`
- Do NOT use `padding-top:16px+` on body — 最大 8px，紧凑是核心
- Do NOT use pure `#fef2f2` for takeaway bg — 必须是 `linear-gradient(90deg,#fef2f2,#fff)`
- Do NOT use `#1e293b` for title — title 用 `#0f172a`，比 body text 更深
- Page number 格式 `01 / 08` — 零填充，斜杠两侧有空格
- Do NOT put `legend` at `bottom` when chart has `label` on bars — legend 和 bar label 重叠。**legend 统一放 top**，grid.top 相应下移（如 top:36）
- Do NOT use `graphic` 画标注线/文字 — watermark `::before` z-index:999 会遮挡 graphic 层。**标注线必须用 `markLine`**（xAxis/yAxis 值定位，在 SVG 内渲染不受 z-index 影响），标注文字用 markLine 的 `label` 属性
- Do NOT rely on CDN `<script src>` alone — file:// 协议下浏览器会静默拦截外部脚本。**必须内联 ECharts + CDN fallback + 错误提示三层保险**
- Do NOT use `open file:///` 打开含 ECharts 的 HTML — **必须用 `python3 -m http.server` + `http://localhost`**
