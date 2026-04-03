# Style Presets — Data Slides

3 color themes. Default is `investor`. User picks in Phase 1.

## 1. investor (default)

Investor-report style. Dark background, white cards, blue/red data colors.

```css
body { background: #1a1a2e; }
.slide { background: #fff; box-shadow: 0 8px 32px rgba(0,0,0,.25); }
.slide .takeaway { color: #991b1b; background: linear-gradient(90deg,#fef2f2,#fff); border-left: 3px solid #dc2626; }
.mini-tbl th { background: #1e3a5f; }
.hc { background: linear-gradient(135deg,#1e3a5f,#1e40af); }
```

```js
const C = {
  blue: '#1e40af', blueL: '#93c5fd', blueP: '#dbeafe',
  red: '#dc2626', green: '#059669', purple: '#7c3aed',
  amber: '#d97706', gray: '#9ca3af',
  dark: '#1e293b', mid: '#475569', light: '#64748b',
  grid: '#f1f5f9', axis: '#e2e8f0'
};
```

**When to use**: Monthly reports, data journalism, public-facing analysis.

## 2. corporate

Clean, light background. Professional and neutral.

```css
body { background: #f3f4f6; }
.slide { background: #fff; box-shadow: 0 4px 16px rgba(0,0,0,.08); }
.slide .takeaway { color: #1e40af; background: linear-gradient(90deg,#eff6ff,#fff); border-left: 3px solid #2563eb; }
.mini-tbl th { background: #1f2937; }
.hc { background: linear-gradient(135deg,#1f2937,#374151); }
```

```js
const C = {
  blue: '#2563eb', blueL: '#93c5fd', blueP: '#dbeafe',
  red: '#dc2626', green: '#059669', purple: '#7c3aed',
  amber: '#d97706', gray: '#9ca3af',
  dark: '#111827', mid: '#4b5563', light: '#6b7280',
  grid: '#f3f4f6', axis: '#e5e7eb'
};
```

**When to use**: Internal reports, team reviews, stakeholder decks.

## 3. dark

High contrast dark theme. Tech/engineering vibe.

```css
body { background: #000; }
.slide { background: #0f172a; box-shadow: 0 8px 32px rgba(0,0,0,.5); color: #e2e8f0; }
.slide .hdr-left h2 { color: #f1f5f9; }
.slide .hdr-left .sub { color: #94a3b8; }
.slide .takeaway { color: #67e8f9; background: linear-gradient(90deg,rgba(6,182,212,0.1),transparent); border-left: 3px solid #06b6d4; }
.slide .body { color: #cbd5e1; }
.slide .ft { color: #64748b; }
.stat-box { background: #1e293b; border-color: #334155; }
.stat-box .k { color: #94a3b8; }
.mini-tbl th { background: #0ea5e9; }
.mini-tbl td { color: #e2e8f0; border-color: #334155; }
.mini-tbl tr:nth-child(even) td { background: #1e293b; }
.hc { background: linear-gradient(135deg,#0c4a6e,#0284c7); }
.ic { background: #1e293b; border-color: #334155; }
.ic .l { color: #94a3b8; }
.fi { color: #e2e8f0; border-color: #334155; }
```

```js
const C = {
  blue: '#0ea5e9', blueL: '#7dd3fc', blueP: '#0c4a6e',
  red: '#f87171', green: '#34d399', purple: '#a78bfa',
  amber: '#fbbf24', gray: '#94a3b8',
  dark: '#f1f5f9', mid: '#cbd5e1', light: '#94a3b8',
  grid: '#1e293b', axis: '#334155'
};
```

**When to use**: Tech demos, engineering reviews, dark-mode presentations.

## How to Apply

1. Read `base.css` for structural layout (shared across all presets)
2. Read this file for the chosen preset's color overrides
3. In generated HTML: embed base.css first, then preset overrides in `<style>` block
4. Use the preset's `const C = {...}` in `<script>` for ECharts colors
