# makino-data-slides — Data tells the story

Excel 数据分析 → 投研风 HTML 数据卡片，带观点、带叙事。
不是画图表，是把数据结论变成可以直接嵌入文章的视觉卡片。

## Install

### Claude Code

```bash
cd ~/.claude-internal/skills/
git clone https://github.com/makinotes/makino-data-slides.git
```

Then type `/makino-data-slides` in Claude Code.

### OpenClaw

Copy the `makino-data-slides` folder into your agent's skills directory, then invoke `/makino-data-slides`.

## Usage

```
/makino-data-slides
```

Interactive mode: provide your data file + narrative direction, get slide cards.

```
/makino-data-slides path/to/data.xlsx
```

Generate slides from a data file (Excel/CSV/JSON/markdown).

## Features

- Each card 1080x720, fixed 5-layer structure (header → takeaway → chart → footer)
- Playwright auto-screenshot, ready to embed in articles
- Investor-report visual language: hero numbers, stat boxes, ECharts charts, insight callouts
- Not a chart tool (use `/chart` for that) — this is data analysis → opinionated visual cards

## What Problem Does This Solve

You finished a data analysis, conclusions scattered across notebooks, and you want article-grade illustrations. Screenshots are ugly, redoing in Figma is slow. This skill turns Excel data directly into publishable visual cards with narrative and opinions — no design tool needed.

## When to Use This

| Need | Use this? | Use instead |
|------|-----------|-------------|
| Single chart (line/bar/pie) | No | `/chart` |
| Article → navigable slide deck | No | `/article-to-slides` |
| Cover image for article | No | `/cover-gen` |
| **Data analysis → multi-slide cards with narrative** | **Yes** | — |

## Input

Primary: **Excel/CSV with structured data** (scores, rankings, time series, category breakdowns).

Also works with JSON, markdown tables, or verbal description of data + conclusions.

## Output

Single self-contained HTML file. All CSS and JS inline. Only external dependency: ECharts CDN.

Each slide is screenshot-ready at 1080x720 via Playwright.

## Example

The 3月 AI 公众号生态月报 slides were built with this skill:

- 8 slides covering executive summary, distribution analysis, scatter plots, grouped bars, progress indicators, ranked lists, head-to-tail comparisons, and multi-signal dashboards
- ECharts for scatter/bar/stacked charts, pure HTML/CSS for cards and progress bars
- Investor-report visual language: hero numbers, stat boxes, color-coded layers, watermark

## Design System

**5-layer slide structure**: header → takeaway → body → footer (fixed 720px height)

**Color palette**: blue (#1e40af) · red (#dc2626) · green (#059669) · amber (#d97706) · gray (#9ca3af)

**Components**: hero cards, stat boxes, insight callouts, layer bars, mini tables, finding cards, progress bars

**Charts**: scatter/bubble, grouped bar, stacked bar, proportion bar (ECharts, animation disabled for screenshot)

**Typography**: PingFang SC / system font stack, hero numbers 32-40px, body 11-13px

## How It Works

```
Your data  -->  Slide outline (user approves)  -->  HTML generation  -->  Playwright screenshot
```

1. **Understand**: Read data, identify metrics/comparisons/trends
2. **Outline**: Propose slide count and content per slide
3. **Generate**: Single HTML with ECharts + CSS cards
4. **Review**: Open in browser, iterate on spacing/data/readability
5. **Screenshot**: Playwright captures each slide at 1080x720

## Update

```bash
cd ~/.claude-internal/skills/makino-data-slides && git pull
```

The skill checks for updates automatically on each run.

## FAQ

**Q: What data formats are supported?**
JSON, CSV, markdown tables, or just describe your data in natural language. The skill extracts structure and proposes a slide outline.

**Q: Can I customize colors?**
The default palette is investor-report style (blue/red/green/amber). You can override per-slide, but the palette is designed to work as a system.

**Q: How is this different from /chart?**
`/chart` generates individual charts (line, bar, pie, etc.). `/makino-data-slides` generates complete slide cards with multiple visual elements, typography, and narrative structure — designed as article illustrations.

**Q: How is this different from /article-to-slides?**
`/article-to-slides` converts finished articles into navigable presentations. `/makino-data-slides` creates static data cards from raw data — for embedding in articles, not replacing them.

**Q: Can I use this for non-Chinese content?**
Yes. The typography and layout work with English and Chinese. Just provide your data in any language.

**Q: The charts look blank in the screenshot.**
ECharts needs a moment to render. The Playwright screenshot script waits 500ms per slide. If charts are complex, increase the wait.

## License

MIT

## Author

[makino](https://github.com/makinotes)
