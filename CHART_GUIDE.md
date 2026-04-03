# Chart Selection Guide — Match Analysis to Chart

Pick charts by **what you're analyzing**, not by what data looks like.

## Decision Table

| Analysis Goal | Chart Type | Typical Scenario | WRONG Choice |
|---------------|-----------|------------------|--------------|
| **Compare across periods** | Grouped Bar | Same metric, 3 time periods (before/during/after) | Pie chart, line chart for <5 points |
| **Rank / Top N** | Horizontal Bar | Top 10 sources by score | Vertical bar (hard to read labels), line chart |
| **Trend over time** | Line Chart | Daily DAU for 25+ days | Bar chart (too many bars), table |
| **Composition / Parts** | Stacked Bar or Proportion Bar (HTML) | Category breakdown, 4-8 segments | Pie chart (>6 slices unreadable) |
| **Distribution / Clustering** | Scatter / Bubble | 2D distribution with group coloring | Table of numbers, single-axis chart |
| **Progress / KPI** | Progress Bar (HTML) | 76% coverage, 5/5 verified | ECharts (overkill) |
| **Head vs Tail** | Side-by-side Cards | Best vs worst comparison with detail | Single chart (can't show qualitative detail) |
| **A vs B Detail** | HTML Table | Detailed multi-row comparison | Chart (information too dense) |

## Chart Templates (ECharts)

### Grouped Bar — Period Comparison

```js
{
  animation: false,
  grid: { left: 60, right: 30, top: 36, bottom: 28 },
  legend: { top: 4, textStyle: { fontSize: 11 } },
  xAxis: { type: 'category', data: ['Period A', 'Period B', 'Period C'],
    axisLabel: { fontSize: 11 }, axisLine: { lineStyle: { color: '#e2e8f0' } } },
  yAxis: { type: 'value', splitLine: { lineStyle: { color: '#f1f5f9' } },
    axisLabel: { fontSize: 11, formatter: v => (v/10000).toFixed(0)+'万' } },
  series: [
    { name: 'Series A', type: 'bar', data: [100, 60, 80],
      itemStyle: { color: '#1e40af', borderRadius: [3,3,0,0] } },
    { name: 'Series B', type: 'bar', data: [50, 12, 15],
      itemStyle: { color: '#dc2626', borderRadius: [3,3,0,0] } }
  ],
  barCategoryGap: '25%', barGap: '5%'
}
```
**Best for**: 2-4 periods, 2-3 series. Over 4 series → switch to table.
**Sizing**: Chart container height 300-380px. Y-axis label formatter for large numbers (万/亿).

### Horizontal Bar — Ranking

```js
{
  animation: false,
  grid: { left: 120, right: 60, top: 10, bottom: 10 },
  xAxis: { type: 'value', show: false },
  yAxis: { type: 'category', data: ['Item 5','Item 4','Item 3','Item 2','Item 1'],
    axisLine: { show: false }, axisTick: { show: false },
    axisLabel: { fontSize: 11, color: '#475569' } },
  series: [{
    type: 'bar', data: [30, 45, 60, 75, 90], barWidth: 18,
    itemStyle: { borderRadius: [0,5,5,0],
      color: (p) => p.dataIndex >= 3 ? '#1e40af' : '#93c5fd' },
    label: { show: true, position: 'right', fontSize: 11, fontWeight: 700 }
  }]
}
```
**Best for**: 5-15 items ranked. Labels on left, values on right.
**Sizing**: 18-22px barWidth. Left margin 100-140px depending on label length.

### Line Chart — Time Series

```js
{
  animation: false,
  grid: { left: 50, right: 50, top: 36, bottom: 28 },
  legend: { top: 4 },
  xAxis: { type: 'category', data: ['02-03','02-04',...],
    axisLabel: { fontSize: 10, interval: 2 } },
  yAxis: [
    { type: 'value', name: 'Series A (万)', position: 'left',
      axisLabel: { fontSize: 10, formatter: v => (v/10000).toFixed(0) } },
    { type: 'value', name: 'Series B (万)', position: 'right',
      axisLabel: { fontSize: 10, formatter: v => (v/10000).toFixed(0) } }
  ],
  series: [
    { name: 'Series A', type: 'line', data: [...], yAxisIndex: 0,
      lineStyle: { width: 2.5 }, itemStyle: { color: '#1e40af' }, symbol: 'none' },
    { name: 'Series B', type: 'line', data: [...], yAxisIndex: 1,
      lineStyle: { width: 2.5 }, itemStyle: { color: '#dc2626' }, symbol: 'none' }
  ]
}
```
**Best for**: 10+ time points. Use dual Y-axis when series have different scales.
**Sizing**: Full width. Show every 2-3 labels on x-axis to avoid overlap.
**Annotations**: Use markLine for events, markArea for periods.

### Scatter / Bubble — Distribution

```js
{
  animation: false,
  grid: { left: 50, right: 30, top: 36, bottom: 40 },
  xAxis: { name: 'Dimension X →', nameLocation: 'center', nameGap: 28,
    axisLine: { lineStyle: { color: '#e2e8f0' } }, splitLine: { lineStyle: { color: '#f1f5f9' } } },
  yAxis: { name: 'Dimension Y →', nameLocation: 'center', nameGap: 36,
    axisLine: { lineStyle: { color: '#e2e8f0' } }, splitLine: { lineStyle: { color: '#f1f5f9' } } },
  series: [{
    type: 'scatter', data: [[x,y,size,label,group], ...],
    symbolSize: (d) => Math.sqrt(d[2]) * 3,
    itemStyle: { opacity: 0.75, borderColor: '#fff', borderWidth: 1.5 },
    label: { show: true, formatter: (p) => p.data[3],
      fontSize: 10, position: 'right' },
    labelLayout: { hideOverlap: true }
  }]
}
```
**Best for**: 2D distribution with 10-80 data points. Color by group.
**Sizing**: Square-ish aspect ratio. Use markLine for threshold lines, graphic for zone labels.

### Proportion Bar — Part of Whole (HTML, not ECharts)

```html
<div class="lbar" style="height:32px;">
  <div style="flex:14;background:#1e40af;">14%</div>
  <div style="flex:50;background:#3b82f6;">50%</div>
  <div style="flex:29;background:#93c5fd;color:#1e293b;">29%</div>
  <div style="flex:7;background:#dbeafe;color:#1e293b;">7%</div>
</div>
<div class="lleg">
  <span><i style="background:#1e40af"></i>Category A 303篇</span>
  <span><i style="background:#3b82f6"></i>Category B 1066篇</span>
  ...
</div>
```
**Best for**: 2-6 segments showing composition. Pure CSS, no ECharts needed.
**Rule**: Segments <5% → merge into "Other" or skip label.

### Progress Bar — Completion Rate (HTML, not ECharts)

```html
<div style="background:#e2e8f0;border-radius:5px;height:10px;overflow:hidden;">
  <div style="width:76%;background:#059669;height:100%;border-radius:5px;"></div>
</div>
```
**Best for**: Single KPI percentage. Use green for >70%, amber for 40-70%, red for <40%.

## Common Mistakes

| Mistake | Why it's wrong | Fix |
|---------|---------------|-----|
| Pie chart for >6 categories | Slices too thin to read | Use horizontal bar or proportion bar |
| Bar chart for 25+ time points | Too many bars, unreadable | Use line chart |
| Line chart for 3 categories | Lines with 3 points look weird | Use grouped bar |
| ECharts for simple percentage | Overkill, slow to render | Use HTML progress bar |
| Table for ranking | No visual hierarchy | Use horizontal bar |
| Single chart for head-vs-tail | Can't show qualitative detail | Use side-by-side cards |
| Vertical bar with long Chinese labels | Labels overlap or rotate badly | Use horizontal bar (labels on left) |

## ECharts Global Defaults

Always apply these to every chart:

```js
animation: false                    // MANDATORY for screenshot
grid: { left: 50, right: 30, top: 36, bottom: 20 }
legend: { top: 4 }                  // NEVER bottom (overlaps bar labels)
axisLine: { lineStyle: { color: '#e2e8f0' } }
splitLine: { lineStyle: { color: '#f1f5f9' } }
axisLabel: { fontSize: 11, color: '#64748b' }
```
