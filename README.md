# Data Slides — Data tells the story

Turn structured data into investor-grade HTML slide cards.

Each slide is a 1080×720 static card with ECharts charts and typographic hierarchy — designed to be screenshot and embedded in articles, shared on social media, or displayed on dashboards.

Not a presentation tool. These are data cards that make numbers visual.

## What You Get

```
/makino-data-slides
```

Interactive mode: describe your data and narrative, get slide cards.

```
/makino-data-slides path/to/data.json
```

Generate slides from a structured data file.

## Output

Single self-contained HTML file. All CSS and JS inline. Only external dependency: ECharts CDN.

Each slide is screenshot-ready at 1080×720 via Playwright.

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

## Install

### Claude Code

```bash
cd ~/.claude-internal/skills/
git clone https://github.com/makinotes/makino-data-slides.git
```

Then type `/makino-data-slides` in Claude Code.

### OpenClaw

Copy the `data-slides` folder into your agent's skills directory, then invoke `/makino-data-slides`.

## Update

```bash
cd ~/.claude-internal/skills/makino-data-slides && git pull
```

The skill checks for updates automatically on each run.

## How It Works

```
Your data  -->  Slide outline (user approves)  -->  HTML generation  -->  Playwright screenshot
```

1. **Understand**: Read data, identify metrics/comparisons/trends
2. **Outline**: Propose slide count and content per slide
3. **Generate**: Single HTML with ECharts + CSS cards
4. **Review**: Open in browser, iterate on spacing/data/readability
5. **Screenshot**: Playwright captures each slide at 1080×720

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
