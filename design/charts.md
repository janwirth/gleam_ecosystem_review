# Charts — Cross-Ecosystem Survey

> [!NOTE]
> **Status:** DRAFT · **Authoring:** AI-assisted, human-reviewed.

> **Explicitly not diagrams.** This article is about **charts** — visual encodings of *data* (bars, lines, points, geographic shapes, distributions) — not **diagrams** — visual encodings of *structure/relationships* (flowcharts, ER diagrams, sequence diagrams). See [`diagramming.md`](diagramming.md) for the latter. The line blurs at the edges: Vega-Lite can render a Sankey flow that reads as diagram-shaped, and [Kroki](diagramming.md#meta-aggregator-kroki) lists `vega-lite` as a rendering engine. Where a tool genuinely straddles both, this article cross-links rather than duplicating the diagramming review.

**Snapshot 2026-08-12** — metrics from the GitHub REST API (`stargazers_count`, `license.spdx_id`, `pushed_at`, `open_issues_count`) captured live on the snapshot date, cross-checked against README/LICENSE file contents where the API's license detection was ambiguous (`NOASSERTION`).

## Table of Contents

1. [Why programmatic charting](#why-programmatic-charting)
2. [The charting pipeline](#the-charting-pipeline)
3. [Aspects we score on](#aspects-we-score-on)
4. [JS/web charting](#jsweb-charting)
5. [The React-specific layer](#the-react-specific-layer)
6. [Python charting](#python-charting)
7. [The declarative grammar-of-graphics lineage](#the-declarative-grammar-of-graphics-lineage)
8. [Ecosystem & plugin breadth](#ecosystem--plugin-breadth)
9. [Comparison matrix — all tools](#comparison-matrix--all-tools)
10. [Decision guide by use case](#decision-guide-by-use-case)
11. [Leaderboards](#leaderboards)
12. [Disregarded](#disregarded)
13. [Discovery — search queries](#discovery--search-queries)

## Why programmatic charting

A chart is a function from data to a visual encoding. The design question every tool in this article answers differently is **where that function lives**:

- **Imperative canvas/SVG APIs** (Chart.js, D3 at its lowest level) — you write code that draws; the library gives you primitives, you own the composition.
- **Declarative component trees** (Recharts, Nivo, Plotly.js) — you describe *what* chart you want as a tree of pre-built components/config; the library owns layout and redraw.
- **Declarative grammar specs** (Vega-Lite, Altair, Observable Plot) — you describe data + encodings (`x`, `y`, `color`, `size`) as a portable spec (often literal JSON); a compiler decides the marks and layout. The spec is the artifact, not the code that produced it.

None of these approaches is strictly better — they trade code-you-write for expressiveness-you-give-up, in different amounts. This article is organized so the trade-off is visible at each level: JS/web tools first (the deepest and most fragmented layer), then the React-specific sub-ecosystem (because "own the DOM vs. let React own the DOM" is its own axis), then Python (a mostly separate world with its own foundation, its own interactive layer, and its own direct lineage back into the JS grammar-of-graphics tools via Altair).

## The charting pipeline

```
┌──────────────────────────┐
│  Data                    │  ← array/dataframe/JSON
└────────────┬─────────────┘
             │ mapped by …
             ▼
┌──────────────────────────┐
│  Encoding                │  ← x, y, color, size, shape (declarative spec or imperative calls)
└────────────┬─────────────┘
             │ computed by …
             ▼
┌──────────────────────────┐
│  Scales & layout          │  ← linear/log/time/ordinal scales, stacking, faceting
└────────────┬─────────────┘
             │ rendered to …
             ▼
┌──────────────────────────┐
│  Rendering target         │  ← SVG / Canvas / WebGL / static image (PNG/PDF)
└──────────────┬────────────┘
             │ optionally wired to …
             ▼
┌──────────────────────────┐
│  Interaction layer         │  ← tooltip, zoom/pan, brush, crossfilter, server push
└──────────────────────────┘
```

Picking a charting tool means picking a point on **three largely independent axes**: rendering target (SVG stays crisp and DOM-inspectable but chokes above ~10-50k points; Canvas is faster and lower-memory but opaque to CSS/accessibility tooling; WebGL scales to millions of points but is the least ergonomic to style), interactivity model (static image vs. client-side JS interactivity vs. a live server pushing updates), and API shape (imperative vs. declarative-component vs. declarative-spec, above).

## Aspects we score on

This article uses the [shared scoring rubric](../practices/formalization.md#scoring-dimensions-per-repo) (stars, license, maintenance, README maturity) with the same kind of domain-specific extensions [`diagramming.md`](diagramming.md#aspects-we-score-on) introduced for a rendering-tool article:

- **Stars** — 🟩🟩 ≥10k, 🟩 ≥1k, 🟨 ≥100, 🟥 <100.
- **License** — 🟩 permissive (MIT/BSD/Apache-2.0/ISC), 🟨 weak copyleft (LGPL), 🟥 viral (GPL/AGPL). **New marking for this article: 🟪 commercial / source-available** — the code is visible on GitHub but *use* is gated by revenue thresholds or a paid tier beyond a free allowance. Two entrants required this (Highcharts, and ApexCharts as of its July 2025 v5.0.0 relicense) — flagged with `[!CAUTION]` callouts since both were historically assumed MIT-equivalent by JS developers.
- **Maintenance** — latest commit vs. snapshot. 🟩🟩 same-day/weeks, 🟩 within a year, 🟥 >1 year stale.
- **README maturity** — 🟩🟩 guide + feature list + gallery, 🟩 tagline + install + basic usage, 🟥 minimal/scaffold.
- **Rendering target** — SVG / Canvas / WebGL / static-image / hybrid. Load-bearing for the perf and styling trade-offs above.
- **Interactivity model** — none (static) / tooltip-only / zoom-pan-brush / crossfilter / server-push. Qualitative, per tool.
- **Chart-type breadth** — count and notability of distinct chart types (or, for grammar tools, the mark vocabulary).
- **Ecosystem/plugin breadth** — themes, official plugin registries, extension packages, notebook/IDE distribution. The "ecosystem" axis this article was explicitly asked to cover — see its [own section](#ecosystem--plugin-breadth).

The first five are tabular; interactivity, breadth, and ecosystem are prose/qualitative per tool, consolidated in the [comparison matrix](#comparison-matrix--all-tools).

## JS/web charting

The foundation, then twelve libraries built at every altitude above it.

| Dimension | [D3.js](#d3js) | [Vega](#vega--vega-lite) | [Vega-Lite](#vega--vega-lite) | [Observable Plot](#observable-plot) | [Chart.js](#chartjs) | [ApexCharts](#apexcharts) | [ECharts](#echarts-apache-echarts) | [Highcharts](#highcharts) | [Plotly.js](#plotlyjs) | [uPlot](#uplot) | [Chartist](#chartist) | [Frappe Charts](#frappe-charts) | [Billboard.js](#billboardjs) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Stars | 113.4k · 🟩🟩 | 12.0k · 🟩🟩 | 5.4k · 🟩 | 5.3k · 🟩 | 67.6k · 🟩🟩 | 15.1k · 🟩🟩 | 67.1k · 🟩🟩 | 12.5k · 🟩🟩 | 18.3k · 🟩🟩 | 10.4k · 🟩🟩 | 13.4k · 🟩🟩 | 15.1k · 🟩🟩 | 6.0k · 🟩 |
| License | ISC · 🟩 | BSD-3 · 🟩 | BSD-3 · 🟩 | ISC · 🟩 | MIT · 🟩 | **Dual/commercial · 🟪** | Apache-2.0 · 🟩 | **Commercial · 🟪** | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 |
| Latest commit | 2026-05-28 · 🟩 | 2026-08-12 · 🟩🟩 | 2026-08-12 · 🟩🟩 | 2026-07-13 · 🟩🟩 | 2026-05-27 · 🟩 | 2026-08-12 · 🟩🟩 | 2026-08-04 · 🟩🟩 | 2026-08-12 · 🟩🟩 | 2026-08-09 · 🟩🟩 | 2026-04-22 · 🟩 | 2025-10-18 · 🟩 | 2024-12-12 · 🟥 | 2026-08-12 · 🟩🟩 |
| Open issues | 21 | 473 | 818 | 345 | 579 | 334 | 1,559 | 655 | 841 | 149 | 245 | 145 | 148 |
| README maturity | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩 |
| Rendering target | SVG/Canvas/HTML | Canvas/SVG (dataflow) | compiles→Vega | SVG | Canvas | SVG | Canvas/SVG/WebGL | SVG/Canvas | SVG/WebGL (`scattergl`) | Canvas | SVG | SVG | SVG/Canvas |

> [!CAUTION]
> **ApexCharts relicensed to a dual/commercial model in v5.0.0 (July 2025).** The free "Community License" now caps at **<$2M USD annual revenue**; above that, or if you're redistributing ApexCharts inside a product others configure (SDKs, embedded BI, no-code platforms), a paid Commercial or OEM license is required. Many existing integrations (and search results) still describe it as plain MIT — verify against [apexcharts.com/license](https://apexcharts.com/license) before adopting for a funded/revenue-generating product.
>
> **Highcharts has always been commercial** for anything beyond personal/non-profit/trial use — the GitHub repo is source-visible, not free-to-use. Longest commercial track record of any tool here (Highstock for financial charts, Highmaps for choropleths, sold as separate licensed modules).

### D3.js

**[d3/d3](https://github.com/d3/d3)** — *"Bring data to life with SVG, Canvas and HTML."*

**The foundation of the entire JS/web charting layer.** D3 (Data-Driven Documents) is not a chart library — it has no `<BarChart>` component and no fixed catalogue of chart types. It is a set of ~30 composable modules (`d3-scale`, `d3-shape`, `d3-array`, `d3-selection`, `d3-transition`, `d3-force`, `d3-hierarchy`, `d3-geo`, `d3-chord`, …) for turning data into DOM/SVG/Canvas attributes. **Vega, Plotly.js, Nivo, Recharts, Chartist, and Billboard.js all use D3 internally** for scales/shapes/layout math; visx wraps D3's math with React-owned rendering. Effective reach is far larger than direct-user count, exactly like Graphviz's role underneath the diagramming ecosystem.

- **113.4k★**, by a wide margin the most-starred tool in this article. **21 open issues** — remarkably low for a project this size, a strong maintenance signal (the team is aggressive about triage, not that nobody reports bugs).
- **No chart-type breadth** in the conventional sense — primitives instead: scales (linear/log/time/ordinal/quantize), shapes (line/area/arc/symbol/pie/stack), layouts (force-directed, hierarchy/treemap/pack/partition, chord, Voronoi), geo projections (dozens, via `d3-geo`), transitions/interpolation.
- **Distribution platform:** [Observable](https://observablehq.com) notebooks were originally built around D3 as the reactive-notebook charting substrate (see also [Observable Plot](#observable-plot) below, from the same team, as the higher-level alternative to hand-rolling D3).

**Pick when:** you need a chart shape nothing pre-built offers, or you're building a charting *library* rather than a chart. **Pass when:** you want a chart in an afternoon — every other tool in this section exists to save you from D3's learning curve.

### Vega / Vega-Lite

**[vega/vega](https://github.com/vega/vega)** — *"A visualization grammar."* · **[vega/vega-lite](https://github.com/vega/vega-lite)** — *"A concise grammar of interactive graphics, built on Vega."*

Two repos, one project, two altitudes. **Vega** is the low-level engine: a JSON "visualization grammar" describing data, scales, marks (rect/line/arc/symbol/path/text/image), and reactive signals — a complete dataflow + rendering runtime, powerful enough to build any of the higher-level tools on top of it. **Vega-Lite compiles to Vega** — a higher-level declarative grammar (closer to ggplot2's vocabulary: `mark` + `encoding` with `x`/`y`/`color`/`size`/`shape`/`facet` channels) that a Vega-Lite compiler expands into a full Vega spec. You almost never hand-write Vega directly; you write Vega-Lite and let it compile down, the same relationship as a high-level language compiling to an IR.

- **Vega:** 12.0k★, BSD-3-Clause, pushed **today** (2026-08-12), 473 open issues.
- **Vega-Lite:** 5.4k★, BSD-3-Clause, pushed **today**, 818 open issues.
- **The spec is the artifact.** A Vega-Lite JSON document is diffable, reviewable in a PR, and regenerable from a template — this is the property the [lineage section](#the-declarative-grammar-of-graphics-lineage) below builds on, and why Altair (Python) can wrap Vega-Lite directly rather than reimplementing it.
- **Ecosystem:** [`vega-embed`](https://github.com/vega/vega-embed) for drop-in web embedding, [`vega-themes`](https://github.com/vega/vega-themes) for a shared theme registry, [Voyager](https://github.com/vega/voyager)/Polestar for interactive spec-building GUIs, `ipyvega` for Jupyter. Also the rendering engine behind [Kroki's](diagramming.md#meta-aggregator-kroki) `vega-lite` support.

**Pick when:** you want the chart definition itself to be a portable, version-controlled artifact, not an API call sequence; you're already in a JSON/declarative pipeline. **Pass when:** you need pixel-level custom interaction Vega-Lite's grammar doesn't expose (drop to raw Vega, or to D3).

### Observable Plot

**[observablehq/plot](https://github.com/observablehq/plot)** — *"A concise API for exploratory data visualization implementing a layered grammar of graphics."*

From the D3/Observable team (Mike Bostock et al.), positioned explicitly as **"D3 for people who don't want to write D3."** Implements its own layered grammar-of-graphics API — **not** built on the Vega-Lite spec (a distinct lineage branch from Vega-Lite/Altair; see the [lineage section](#the-declarative-grammar-of-graphics-lineage)) — with a "marks" vocabulary (bar, line, area, dot, cell, rule, tick, text, arrow, link, image, geo, density, contour, hexbin) composed via a single `Plot.plot({...})` call.

- **5.3k★**, ISC, pushed 2026-07-13, 345 open issues.
- **Rendering:** SVG only, no Canvas/WebGL fallback — a deliberate simplicity trade-off, not a perf tool.
- **Distribution:** first-class inside [Observable](https://observablehq.com) notebooks (same team, same platform as D3's notebook heritage) as well as npm-installable standalone.

**Pick when:** you want ggplot2-shaped exploratory plotting in JS with a much smaller API surface than raw D3; notebook-first workflows. **Pass when:** you need the JSON-spec portability of Vega-Lite, or Canvas/WebGL-scale point counts.

### Chart.js

**[chartjs/Chart.js](https://github.com/chartjs/Chart.js)** — *"Simple HTML5 Charts using the `<canvas>` tag."*

The default recommendation for "I need a chart, no ceremony." Canvas-rendered (not D3-based), 8 core chart types (line, bar, radar, doughnut/pie, polarArea, bubble, scatter, plus mixed/combo charts), extended by an **official plugin registry** (`chartjs-plugin-datalabels`, `-zoom`, `-annotation`) and community plugins for treemap/matrix/sankey/wordcloud series.

- **67.6k★**, MIT, pushed 2026-05-27, 579 open issues.
- **Framework wrappers:** [`react-chartjs-2`](#react-chartjs-2) (reviewed below), `vue-chartjs`, `ng2-charts` (Angular) — the widest framework-wrapper coverage of any canvas library here.

**Pick when:** you want the safest, most-documented, MIT-licensed canvas chart with the least API surface to learn. **Pass when:** you need SVG-level CSS styling control, or chart types beyond the core 8 without a plugin.

### ApexCharts

**[apexcharts/apexcharts.js](https://github.com/apexcharts/apexcharts.js)** — *"Interactive JavaScript Charts built on SVG."*

SVG-rendered, broadest out-of-box catalogue in the SVG-JS category: line, area, bar/column, box plot, candlestick, bubble, scatter, heatmap, treemap, pie/donut, radialBar, radar, polarArea, funnel/pyramid, timeline/rangeBar, sparklines, and mixed/combo charts, all with built-in zoom/pan/brush and animated transitions with no extra config.

- **15.1k★**, pushed **today**, 334 open issues. README maturity 🟩🟩.
- **License — see the `[!CAUTION]` above.** Dual-licensed (Community <$2M revenue / paid Commercial / paid OEM) since v5.0.0, July 2025.
- **Framework bindings:** `vue-apexcharts`, `react-apexcharts`, Angular wrapper, `svelte-apexcharts` — strong multi-framework coverage.

**Pick when:** you want SVG interactivity (zoom/brush/animated transitions) out of the box with minimal config, **and** your org is under the revenue cap or willing to pay. **Pass when:** license certainty matters more than convenience — Chart.js (MIT) or ECharts (Apache-2.0) sidestep the question entirely.

### ECharts (Apache ECharts)

**[apache/echarts](https://github.com/apache/echarts)** — *"A powerful, interactive charting and data visualization library for browser."*

The **widest chart-type breadth of anything reviewed in this article**: line, bar, pie, scatter/effectScatter, geo/map, candlestick, boxplot, heatmap, graph/relationship (network), tree, treemap, sunburst, parallel-coordinates, sankey, funnel, gauge, pictorialBar, themeRiver, calendar, plus fully custom series. Apache Software Foundation governance (donated by Baidu 2018) gives it institutional longevity most JS charting projects don't have.

- **67.1k★**, Apache-2.0, pushed 2026-08-04, 1,559 open issues (highest issue count in this article — expected at this breadth/scale, not a red flag on its own given active triage).
- **Rendering:** Canvas by default, SVG mode available, **WebGL via the `echarts-gl` extension** for 3D and large-point-count scenes.
- **Ecosystem:** `echarts-gl` (3D/WebGL), `echarts-wordcloud`, `echarts-liquidfill`, `echarts-stat` — official extension packages, not just community plugins, backed by the same governance as the core.

**Pick when:** you need the broadest chart-type catalogue in a single permissively-licensed library, or geo/network/hierarchical chart types specifically. **Pass when:** bundle size is the binding constraint — ECharts is large; Chart.js/Frappe Charts/Chartist are far lighter.

### Highcharts

**[highcharts/highcharts](https://github.com/highcharts/highcharts)** — *"Highcharts JS, the JavaScript charting framework."*

Senior of the commercial tier (project since 2010's predecessor). Polished defaults, broad chart-type catalogue (line, area, bar/column, pie, scatter, bubble, gauge, heatmap, treemap, sunburst, sankey, dependency wheel, network graph, wordcloud, funnel, pyramid, boxplot, waterfall, polar, organization chart, venn), plus **separately-licensed modules**: Highstock (financial/OHLC/candlestick, an industry standard in trading UIs), Highmaps (choropleth/geo), Highcharts Gantt, and the newer Highcharts Dashboards product.

- **12.5k★**, pushed **today**, 655 open issues. README maturity 🟩🟩.
- **License — 🟪 commercial**, see the `[!CAUTION]` above. Free only for personal projects, non-profits, and trials.

**Pick when:** enterprise support/SLA and the widest domain-specific module catalogue (financial, geo, Gantt) matter more than license cost; budget exists. **Pass when:** the license cost isn't justified by the org's stage — ECharts covers most of the same chart-type ground for free.

### Plotly.js

**[plotly/plotly.js](https://github.com/plotly/plotly.js)** — *"Open-source JavaScript charting library behind Plotly and Dash."*

Deepest **scientific/statistical + 3D + geo** breadth of any JS library here: scatter/line, bar, pie, heatmap, contour, 3D surface/scatter/mesh, choropleth/scattergeo maps, candlestick/OHLC, box, violin, histogram, sankey, treemap, sunburst, parallel coordinates, ternary plots, polar. **WebGL-accelerated `scattergl` trace type** handles far larger point counts than the SVG-default traces.

- **18.3k★**, MIT, pushed 2026-08-09, 841 open issues.
- **Same engine as [Plotly (Python)](#plotly-python)** — figures built in Python (or R) render through this exact JS library; this is the single clearest example of a shared rendering engine across a JS/Python boundary in this article. Also the engine underneath [Dash](#app-shell-delivery-layers).

**Pick when:** you need scientific-grade chart types (3D, ternary, contour) or want figures that render identically whether authored in Python or JS. **Pass when:** you don't need the scientific breadth — the bundle is large relative to Chart.js/uPlot.

### uPlot

**[leeoniya/uPlot](https://github.com/leeoniya/uPlot)** — *"A small, fast chart for time series, lines, areas, OHLC & bars."*

Deliberately narrow: line, area, bar, and OHLC/candlestick for time-series data, nothing else. The narrowness **is** the feature — uPlot's entire design brief is minimum bundle size and maximum render speed for dense time-series (financial tick data, monitoring dashboards, telemetry). Canvas-rendered; independent benchmarks routinely show it rendering 10-100× faster than SVG-based libraries at high point counts (tens of thousands to millions of points).

- **10.4k★**, MIT, pushed 2026-04-22 (~3.5 months before snapshot — still 🟩, but the least recently active of the "core JS" tier), 149 open issues.

**Pick when:** the chart is time-series and the dataset is large enough that SVG libraries visibly lag (dashboards, monitoring, financial). **Pass when:** you need chart types beyond line/area/bar/OHLC.

### Chartist

**[chartist-js/chartist](https://github.com/chartist-js/chartist)** — *"Simple responsive charts."*

- **13.4k★**, MIT, last commit on `main` 2025-10-18 (~10 months before snapshot — GitHub's `pushed_at` shows a more recent date, but that reflects a push to a non-default branch, not new work on `main`), 245 open issues. README maturity 🟩🟩.
- SVG, zero dependencies, deliberately minimal chart-type set (line, bar, pie/donut).

> [!NOTE]
> **Supersession, not two competing projects.** The originally canonical repo, `gionkunz/chartist-js`, now literally describes itself as *"Legacy Chartist Repo for old gh-pages"* and last pushed 2024-05-06 — see [Disregarded](#disregarded). Development continues under the `chartist-js` GitHub org (`chartist-js/chartist`, still published to npm as `chartist`), an org-level continuation of the original project rather than a fork/rewrite. Link to the org repo, not the original author's, for anything current.

**Pick when:** you want the smallest reasonable SVG chart library with zero dependencies and don't need more than the core 3 chart types. **Pass when:** you need chart-type breadth — Frappe Charts and Chartist occupy the same minimalist niche; pick by aesthetic preference or check both READMEs for the closer default style.

### Frappe Charts

**[frappe/charts](https://github.com/frappe/charts)** — *"Simple, responsive, modern SVG Charts with zero dependencies."*

- **15.1k★**, MIT, **last commit 2024-12-12 · 🟥** (~20 months stale as of snapshot) — the only tool in the JS/web table with a maintenance flag. 145 open issues.
- Small chart set: line, bar, pie/donut, percentage, heatmap.
- From [Frappe](https://frappe.io) (the ERPNext company); used internally, which may explain the slower external-facing commit cadence despite the healthy star count.

**Pick when:** the minimalist zero-dependency SVG niche fits and the maintenance gap is acceptable for your use case. **Pass when:** active upstream maintenance is a hard requirement — Chartist covers the same niche with a current commit history.

### Billboard.js

**[naver/billboard.js](https://github.com/naver/billboard.js)** — *"Re-usable, easy interface JavaScript chart library based on D3.js, with SVG and Canvas rendering support."*

- **6.0k★**, MIT, pushed **today**, 148 open issues.
- D3-based (see the [D3 foundation](#d3js) note), covers line, spline, area, bar, scatter, pie, donut, gauge, radar, bubble, candlestick.
- From Naver (the Korean search/portal company); a spiritual successor to the older C3.js (also D3-based, no longer actively maintained), positioning itself explicitly as the "easy interface on top of D3" niche.

**Pick when:** you want a D3-powered chart with a friendlier declarative config layer than hand-rolled D3, and both SVG and Canvas rendering targets matter to you. **Pass when:** you want the largest community/ecosystem in this tier — Chart.js and ApexCharts have far more Stack Overflow/plugin coverage.

## The React-specific layer

"Own the DOM vs. let React own the DOM" is a distinct axis from anything above — every library below is React-first, not a generic JS library with a React wrapper bolted on (react-chartjs-2 is the one deliberate exception, included for contrast).

| Dimension | [visx](#visx) | [Recharts](#recharts) | [Nivo](#nivo) | [Victory](#victory) | [react-chartjs-2](#react-chartjs-2) |
| --- | --- | --- | --- | --- | --- |
| Stars | 21.0k · 🟩🟩 | 27.5k · 🟩🟩 | 14.1k · 🟩🟩 | 11.2k · 🟩🟩 | 6.9k · 🟩 |
| License | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 | MIT · 🟩 (API reports `NOASSERTION`; verified MIT via `LICENSE.txt`) | MIT · 🟩 |
| Latest commit | 2026-06-22 · 🟩 | 2026-08-11 · 🟩🟩 | 2026-07-21 · 🟩🟩 | 2025-12-19 · 🟩 (~8mo, approaching stale) | 2026-05-26 · 🟩 |
| Open issues | 146 | 442 | 49 | 90 | 107 |
| README maturity | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 (defers chart config to Chart.js docs) |
| Architecture | D3-math + React-DOM, **low-level primitives** | D3-math + React-DOM, **opinionated pre-built components** | D3-math + React-DOM, **opinionated pre-built components** | D3-math + React-DOM, **composable primitives** (visx-adjacent) | Thin React wrapper **around Chart.js** (not D3-based) |
| Rendering target | SVG | SVG | **SVG / HTML / Canvas** (per chart type) | SVG | Canvas (Chart.js) |

None of the five is a "thin D3-DOM wrapper" in the old sense (D3 directly manipulating the DOM, React just providing a ref) — that pattern has died out. The real split is: **visx** ships ~30 independently-installable scoped packages (`@visx/shape`, `@visx/scale`, `@visx/group`, …) with **zero pre-built chart components** — you compose bars/lines/axes yourself, D3 supplies the math, React owns rendering. **Recharts and Nivo** are the opposite: opinionated, pre-built `<LineChart>`/`<BarChart>` components you configure, not compose from primitives — D3 is an internal implementation detail for scales/shapes only. **Victory** sits closer to visx's composable-primitives philosophy (`VictoryChart` + `VictoryLine` + `VictoryBar` snap together) while still shipping more pre-built shape than visx's raw building blocks.

### visx

**[airbnb/visx](https://github.com/airbnb/visx)** — *"visx | visualization components."*

Airbnb's collection of **low-level, composable** visualization primitives: shapes, scales, axes, curves, annotations, grids, hierarchies, network graphs, and geo — no pre-built "chart" components by design. "Combines the power of d3 to generate your visualization with the benefits of react for updating the DOM" (README's own framing). TypeScript-native, ~30 scoped packages so you only install what you use.

**Pick when:** the design doesn't fit any pre-built chart shape, or brand/interaction requirements are unusual enough that composing from primitives is faster than fighting an opinionated library's config surface.

### Recharts

**[recharts/recharts](https://github.com/recharts/recharts)** — *"Redefined chart library built with React and D3."*

Most active of the five (commit **today**, highest star count). Pre-built, declarative component catalogue: Line, Bar, Area, Composed, Pie, Radar, RadialBar, Scatter, Funnel, Treemap, Sankey. React renders the SVG; D3 supplies scales/math only, never touching the DOM directly.

**Pick when:** you want the fastest time-to-shipped-dashboard with pre-built, well-documented components and don't need custom chart shapes.

### Nivo

**[plouc/nivo](https://github.com/plouc/nivo)** — *"nivo provides a rich set of dataviz components, built on top of the awesome d3 and React libraries."*

Same architectural family as Recharts (opinionated pre-built components, D3 for math only) but with the **widest chart-type catalogue of the React-native tier**: Bar, Line, Pie, Scatterplot, Heatmap, Radar, Sankey, Chord, Treemap, Sunburst, Calendar, Bump, Boxplot, Network/graph, Choropleth/geo. Distinctive **multi-renderer output** — SVG, HTML, or Canvas per chart type — and `react-spring`-powered animation instead of D3 transitions.

> [!NOTE]
> Nivo's latest **tagged release** (v0.99.0) is from 2025-05-23 — 14+ months behind the 2026-07-21 commit activity as of snapshot. Commit history is active; the version number on npm is not. Worth checking `main` vs. the last tag if you need a specific recent fix.

**Pick when:** you want pre-built components with the widest chart-type range in the React tier, or need Canvas/HTML rendering for perf reasons without leaving the opinionated-component model.

### Victory

**[FormidableLabs/victory](https://github.com/FormidableLabs/victory)** — *"A collection of composable React components for building interactive data visualizations."*

Composable in the visx sense (`VictoryChart` + `VictoryLine` + `VictoryBar` + `victory-core`) rather than Recharts/Nivo's fully-opinionated components. Chart types: Line, Bar, Area, Pie, Scatter, Candlestick, Boxplot, ErrorBar, Voronoi — plus a **first-class React Native port** (`victory-native`, and newer `victory-native-xl`), the only library in this tier with native-mobile reach.

> [!NOTE]
> Formidable Labs was acquired/rebranded under **Nearform** ("Nearform Commerce" on GitHub, homepage now `commerce.nearform.com`); Victory was **not** merged into Nivo (a plausible-sounding but false rumor worth naming and killing here) — it continues under Nearform's maintenance, including active `victory-native-xl` work into 2025-2026.

**Pick when:** you need React Native + web chart parity from one library, or prefer the composable-primitive model but want more pre-built shape than visx offers.

### react-chartjs-2

**[reactchartjs/react-chartjs-2](https://github.com/reactchartjs/react-chartjs-2)** — *"React components for Chart.js, the most popular charting library."*

The deliberate exception in this table: not D3-based at all, a thin React wrapper around [Chart.js](#chartjs) — mounts a `<canvas>`, instantiates/updates a Chart.js instance via `useEffect`/refs. Whatever Chart.js supports (line, bar, radar, doughnut/pie, polarArea, bubble, scatter), Canvas-rendered.

**Pick when:** the project already uses Chart.js elsewhere (non-React parts, existing configs, existing plugins) and you just need idiomatic React lifecycle wiring around it — don't reach for it as a first choice if starting fresh in React (Recharts/Nivo are more idiomatically React).

> [!NOTE]
> **Tremor** ([tremorlabs/tremor](https://github.com/tremorlabs/tremor), 3.6k★, Apache-2.0) is worth knowing about but isn't scored in the leaderboard above — it's a **dashboard UI kit** (KPI cards, tables, tabs, badges, plus Area/Bar/Line/Donut chart primitives built on Recharts under the hood), not a general-purpose charting library, the same "domain-shaped, different evaluation framework" distinction [`caching.md`](../gleam/caching.md) draws for domain-shaped caches. Vercel acquired Tremor in January 2025 and made it fully open-source; the current model is **copy-paste components** (shadcn/ui-style — package name is literally `tremor-raw`, private, unversioned) rather than an installable npm dependency. If you want a full dashboard shell with charts as one ingredient, this is the fastest path; if you want a general-purpose chart library, use Recharts/Nivo/visx directly.

## Python charting

A mostly separate ecosystem from the JS/web tier, with its own foundation (matplotlib), its own interactive layer (Plotly/Bokeh/Altair), and one direct lineage bridge back into JS (Altair compiles to Vega-Lite JSON — see the [lineage section](#the-declarative-grammar-of-graphics-lineage)).

| Dimension | [matplotlib](#matplotlib) | [Seaborn](#seaborn) | [Plotly](#plotly-python) | [Bokeh](#bokeh) | [Altair](#altair--vega-altair) | [HoloViews](#holoviews) | [Pygal](#pygal) | [Plotnine](#plotnine) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Stars | 23.1k · 🟩🟩 | 14.0k · 🟩🟩 | 18.7k · 🟩🟩 | 20.4k · 🟩🟩 | 10.5k · 🟩🟩 | 2.9k · 🟩 | 2.8k · 🟩 | 4.8k · 🟩 |
| License | Matplotlib License (BSD-style, permissive) · 🟩 | BSD-3 · 🟩 | MIT · 🟩 | BSD-3 · 🟩 | BSD-3 · 🟩 | BSD-3 · 🟩 | LGPL-3.0 · 🟨 | MIT · 🟩 |
| Latest commit | 2026-08-12 · 🟩🟩 | 2026-07-06 · 🟩🟩 | 2026-08-07 · 🟩🟩 | 2026-08-12 · 🟩🟩 | 2026-08-09 · 🟩🟩 | 2026-08-11 · 🟩🟩 | 2026-07-21 · 🟩🟩 | 2026-08-11 · 🟩🟩 |
| Open issues | 1,477 | 227 | 775 | 859 | 150 | 1,062 | 198 | 85 |
| README maturity | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩🟩 |
| Rendering target | Static (PNG/PDF/SVG), Agg/Cairo backends | Static (matplotlib backend) | Interactive HTML/JS (shares [Plotly.js](#plotlyjs)) | Interactive HTML/JS, **server-capable** | Interactive HTML/JS (compiles → Vega-Lite) | Delegates to Bokeh/matplotlib/Plotly | Static SVG (+ PNG via CairoSVG) | Static (matplotlib backend) |
| Interactivity | None | None | Client-side JS (zoom/pan/hover) | Client-side JS **or server-push callbacks** | Client-side JS (Vega-Lite's) | Whatever the chosen backend supports | None | None |

Every Python entry here is scored on the same rubric as the JS tier; **the split that matters most for the [decision guide](#decision-guide-by-use-case) is static-image-only (matplotlib, Seaborn, Pygal, Plotnine) vs. browser-interactive (Plotly, Bokeh, Altair, HoloViews)** — pick the first group for print/paper output, the second for anything that needs to live in a browser tab and respond to a mouse.

### Matplotlib

**[matplotlib/matplotlib](https://github.com/matplotlib/matplotlib)** — *"matplotlib: plotting with Python."*

**The foundation of Python static plotting**, the same structural role D3 plays for JS: pandas' `.plot()`, Seaborn, and Plotnine all render *through* matplotlib (Seaborn and Plotnine call matplotlib APIs directly rather than reimplementing rendering); most of the scientific-Python stack (scikit-learn's plotting utilities, astropy, geopandas) assumes a matplotlib `Axes` object as the interop point.

- **23.1k★**, pushed **today**, 1,477 open issues (expected at this scale — matplotlib underlies nearly the entire Python numerical-computing visualization surface).
- **License:** the "Matplotlib License" — a custom BSD-compatible, PSF-derived permissive license, not a standard SPDX identifier (hence GitHub's API reports `license: null`) but OSI-equivalent in practice. Scored 🟩.
- Nearly every static chart type exists somewhere in the API: line, bar, scatter, hist, pie, boxplot, violin, contour, 3D (via `mplot3d`), imshow/heatmaps, quiver, streamplot, polar.

**Pick when:** print/paper-quality static output is the deliverable, or you're writing a library that needs to interop with the rest of scientific Python. **Pass when:** you want interactivity — matplotlib's is limited to a few backend-specific widgets, not a first-class feature.

### Seaborn

**[mwaskom/seaborn](https://github.com/mwaskom/seaborn)** — *"Statistical data visualization in Python."*

A statistical layer **on top of** matplotlib (not a competing renderer): distribution plots (`histplot`/`kdeplot`/`ecdfplot`), relational (`scatterplot`/`lineplot`), categorical (`boxplot`/`violinplot`/`stripplot`/`swarmplot`/`barplot`), regression (`regplot`/`lmplot`), matrix (`heatmap`/`clustermap`), and multi-plot grids (`FacetGrid`/`PairGrid`/`JointGrid`). The value-add is statistically-sound defaults (confidence intervals, appropriate binning, colorblind-safe palettes) out of the box — matplotlib gives you the primitives, Seaborn gives you the judgment calls a statistician would make.

- **14.0k★**, BSD-3-Clause, pushed 2026-07-06, 227 open issues.

**Pick when:** the chart is for exploratory statistical analysis or a paper and you want research-grade defaults without hand-tuning matplotlib. **Pass when:** you need a chart type Seaborn doesn't wrap — drop to matplotlib directly (it's always available underneath).

### Plotly (Python)

**[plotly/plotly.py](https://github.com/plotly/plotly.py)** — *"The interactive graphing library for Python."*

Renders through the **exact same [Plotly.js](#plotlyjs) engine** reviewed in the JS section — the clearest shared-engine bridge across the JS/Python line in this article. Same breadth: scatter/line, bar, pie, 3D, maps, financial (candlestick/OHLC), statistical (box/violin/histogram), Sankey, treemap. The high-level `plotly.express` module gives a one-liner API (`px.scatter(df, x=..., y=..., color=...)`) comparable in spirit to Seaborn's ergonomics but backed by Plotly.js's interactive rendering instead of matplotlib's static one.

- **18.7k★**, MIT, pushed 2026-08-07, 775 open issues.
- Also the rendering engine underneath [Dash](#app-shell-delivery-layers).

**Pick when:** you want interactive (zoomable/hoverable) charts from Python with the broadest scientific chart-type catalogue, or need figures that are identical whether generated in Python, R, or JS. **Pass when:** bundle/page weight matters — Plotly.js is large relative to Altair/Bokeh's output.

### Bokeh

**[bokeh/bokeh](https://github.com/bokeh/bokeh)** — *"Interactive Data Visualization in the browser, from Python."*

The one Python tool in this tier with **genuine server-backed interactivity**: the Bokeh server can push live-streaming data updates and run Python-side callbacks in response to browser events (a slider dragged client-side triggers a Python function server-side) — not just client-side JS interactivity baked in at export time, which is what Plotly.js/Altair/Vega-Lite offer. Chart types: interactive line/bar/scatter/patch, hex tiles, images, maps (via GeoJSON/tile providers), graphs/networks.

- **20.4k★**, BSD-3-Clause, pushed **today**, 859 open issues.

**Pick when:** the interactivity needs to trigger Python-side computation live (not just client-side pan/zoom) — a genuinely different capability from every other tool in this article. **Pass when:** you don't need a running server — Altair/Plotly ship self-contained HTML with no backend process required.

### Altair — Vega-Altair

**[vega/altair](https://github.com/vega/altair)** — *"Vega-Altair is a declarative statistical visualization library for Python... built on top of the powerful Vega-Lite JSON specification."* (quoted verbatim from the README)

Confirmed via the project's own words: Altair (now branded "Vega-Altair," moved under the `vega` GitHub org) **compiles Python calls directly into Vega-Lite JSON specs** — the exact spec format reviewed in [Vega/Vega-Lite](#vega--vega-lite) above. This is the direct spec-sharing bridge in the [lineage section](#the-declarative-grammar-of-graphics-lineage): whatever mark/encoding vocabulary Vega-Lite supports, Altair exposes with a Pythonic API.

- **10.5k★**, BSD-3-Clause, pushed 2026-08-09, 150 open issues.
- Not affiliated with Altair Engineering, Inc. — the README explicitly disclaims this, worth knowing if you're searching and get confused results.

**Pick when:** you want a versionable, diffable chart *spec* (same rationale as choosing Vega-Lite directly in JS) but your pipeline is Python — the JSON Altair emits is the same portable artifact either way. **Pass when:** you need chart types outside Vega-Lite's mark vocabulary (3D, for instance) — Plotly or matplotlib cover more ground there.

### HoloViews

**[holoviz/holoviews](https://github.com/holoviz/holoviews)** — *"With Holoviews, your data visualizes itself."*

A meta-layer, not a renderer: you annotate data with visualization *intent* (this is a `Scatter`, this is a `HeatMap`) and HoloViews delegates actual drawing to a **pluggable backend** — Bokeh, matplotlib, or Plotly, chosen per-plot or globally. The pitch is backend-agnostic code: switch from static (matplotlib) to interactive (Bokeh) output by changing one setting, not rewriting the plotting calls.

- **2.9k★** (smallest in this Python tier, but backed by the same [HoloViz](https://holoviz.org) org as [Panel](#app-shell-delivery-layers)), BSD-3-Clause, pushed 2026-08-11, 1,062 open issues (high relative to star count — worth noting, though consistent with a project whose surface area spans three rendering backends).

**Pick when:** you're building analysis code where the render target (static vs. interactive) is a late decision you want to defer, or you're already in the HoloViz stack (Panel/Datashader). **Pass when:** you want a smaller dependency footprint — HoloViews pulls in whichever backend(s) you target.

### Pygal

**[Kozea/pygal](https://github.com/Kozea/pygal)** — *"PYthon svg GrAph plotting Library."*

Pure-SVG, notable for "pretty" defaults out of the box and built-in PNG export via CairoSVG. Chart types: line, bar, radar, pie/donut, gauge, funnel, treemap, box plot, dot, pyramid.

- **2.8k★**, **LGPL-3.0 · 🟨** — the only weak-copyleft license in the Python tier (everything else here is BSD/MIT). Pushed 2026-07-21, 198 open issues.
- README maturity 🟩 (tagline + usage, not a full guide-plus-gallery).

**Pick when:** a lightweight, dependency-light, aesthetically-decent-by-default SVG output is the goal and LGPL is acceptable for your distribution model. **Pass when:** you're shipping a proprietary product where LGPL's file-level copyleft is a concern — matplotlib/Seaborn/Plotnine are all permissively licensed.

### Plotnine

**[has2k1/plotnine](https://github.com/has2k1/plotnine)** — *"A Grammar of Graphics for Python."*

A **direct Python port of ggplot2's specific grammar and API** (not merely "inspired by," and notably *not* the same lineage as Vega-Lite/Altair — see the [lineage section](#the-declarative-grammar-of-graphics-lineage)): `geom_point()`, `geom_line()`, `geom_bar()`, `geom_boxplot()`, `geom_violin()`, `geom_histogram()`, `geom_density()`, `geom_smooth()`, `+ facet_wrap()`, `+ coord_flip()` — the exact `ggplot() + geom + facet` compositional syntax R users know, rendered through matplotlib underneath.

- **4.8k★**, MIT, pushed 2026-08-11, 85 open issues.

**Pick when:** the team already thinks in ggplot2's grammar (R data scientists moving to Python, or teams that standardized on ggplot2's mental model) and wants the exact same composition syntax. **Pass when:** nobody on the team knows ggplot2 — Seaborn's API is more idiomatically Pythonic for the same statistical-plot territory.

### App-shell delivery layers

Not charting libraries themselves — these wrap a chart (or several) in a full interactive **application**, with minimal glue code. Mentioned briefly because "Python charts" questions frequently turn out to really be "Python dashboard app" questions; no dedicated dashboard/app-framework article exists yet in this almanac to defer to.

| Tool | Stars | License | Relationship to charting |
| --- | --- | --- | --- |
| [Streamlit](https://github.com/streamlit/streamlit) | 45.5k · 🟩🟩 | Apache-2.0 | Script-to-app framework; renders Plotly/Altair/matplotlib/Bokeh figures with a one-line `st.plotly_chart()`-style call, handles the web server and widget state for you. |
| [Dash](https://github.com/plotly/dash) | 24.4k · 🟩🟩 | MIT | Plotly's own dashboard framework — "no JavaScript required" — built directly on Plotly.js/plotly.py plus a React component model under the hood. |
| [Panel](https://github.com/holoviz/panel) | 5.7k · 🟩 | BSD-3-Clause | The HoloViz app-shell layer, pairs naturally with [HoloViews](#holoviews)/Bokeh — same org, shared backend assumptions. |
| [Gradio](https://github.com/gradio-app/gradio) | 43.3k · 🟩🟩 | Apache-2.0 | ML-demo-first (not chart-first), but commonly used to wrap a matplotlib/Plotly figure as one input/output component in a quick shareable app. |

## The declarative grammar-of-graphics lineage

The term "grammar of graphics" originates from Leland Wilkinson's 1999 book of the same name — the idea that a chart is fully specified by composing **data + a mapping from variables to visual channels (encoding) + a mark type + a coordinate system**, rather than being an ad-hoc drawing. **ggplot2** (Hadley Wickham, R, 2005) was the first mainstream, widely-adopted implementation and is the reference point everything below is compared against, even though it isn't reviewed here (out of scope — R, not JS/Python).

Two genuinely separate lineages carry the idea into JS and Python, and conflating them is a common mistake worth flagging explicitly:

**Lineage 1 — the Vega-Lite spec-sharing chain:**

```
Wilkinson's "Grammar of Graphics" (1999, book)
        │
        ▼
Vega (JS) — low-level visualization grammar, JSON dataflow + rendering engine
        │  Vega-Lite compiles TO Vega
        ▼
Vega-Lite (JS) — higher-level declarative grammar, ggplot2-like mark+encoding vocabulary
        │  Altair compiles Python calls INTO Vega-Lite JSON — same spec format, different authoring language
        ▼
Vega-Altair (Python) — Pythonic API, emits literal Vega-Lite JSON
```

This is a **spec-sharing** lineage: a Vega-Lite JSON document and the JSON Altair emits are the *same artifact format*. A chart spec produced by Altair could, in principle, be handed to `vega-embed` in a JS app with no translation step.

**Lineage 2 — independent reimplementations of the same idea, not spec-compatible with Lineage 1:**

- **Observable Plot (JS)** — its own README says it implements *"a layered grammar of graphics"*, inspired by both ggplot2 and D3, but it is **not** built on Vega-Lite and does not share its spec format. A separate, independent grammar-of-graphics implementation in JS.
- **Plotnine (Python)** — a **direct port of ggplot2's own grammar and API** (`ggplot() + geom_point() + facet_wrap()`), not a reinterpretation and not Vega-Lite-based either. If Lineage 1 is "the same idea, reimplemented and spec-shared," Plotnine is "the literal R API, translated to Python."

**The practical takeaway:** if you need a chart spec that moves between a JS frontend and a Python backend without translation, Vega-Lite/Altair is the only pair in this article that actually shares a wire format. Observable Plot and Plotnine both *implement the same underlying idea* as ggplot2 but produce incompatible artifacts with Vega-Lite/Altair and with each other.

## Ecosystem & plugin breadth

The task that motivated this article specifically asked that ecosystem breadth be considered explicitly, not folded silently into "maintenance." Per tool:

- **D3** — ~30 independently-versioned `d3-*` modules; foundation for Vega, Plotly.js, Nivo, Recharts, Chartist, Billboard.js (see [D3.js](#d3js)); [Observable](https://observablehq.com) notebooks as a distribution platform built around it.
- **Vega/Vega-Lite** — [`vega-embed`](https://github.com/vega/vega-embed) (drop-in web embedding), [`vega-themes`](https://github.com/vega/vega-themes) (shared theme registry), Voyager/Polestar (spec-building GUIs), `ipyvega` (Jupyter), plus every consumer in [Lineage 1](#the-declarative-grammar-of-graphics-lineage) above; also a [Kroki](diagramming.md#meta-aggregator-kroki) rendering engine.
- **Observable Plot** — distributed as a first-class citizen inside Observable notebooks in addition to standalone npm.
- **Chart.js** — an **official plugin registry** (`chartjs-plugin-datalabels`/`-zoom`/`-annotation`) plus community plugins for treemap/matrix/sankey/wordcloud series it doesn't ship natively; the widest framework-wrapper coverage (React, Vue, Angular) of any canvas library reviewed.
- **ECharts** — official extension packages with the same governance as core: `echarts-gl` (3D/WebGL), `echarts-wordcloud`, `echarts-liquidfill`, `echarts-stat`.
- **ApexCharts** — fewer third-party plugins, more monolithic core; strong multi-framework binding coverage (Vue/React/Angular/Svelte) compensates.
- **Highcharts** — official paid-module ecosystem (Highstock, Highmaps, Highcharts Gantt, Highcharts Dashboards) rather than a community plugin registry — the commercial model funds first-party breadth instead.
- **Plotly.js / Plotly.py** — shared engine powers [Dash](#app-shell-delivery-layers) and Chart Studio (hosted spec editor); deep integration with pandas (`df.plot(backend="plotly")`) and the high-level `plotly.express` API.
- **Matplotlib** — the base rendering layer for pandas' `.plot()`, Seaborn, Plotnine, geopandas, astropy, and scikit-learn's plotting utilities; the closest Python analogue to D3's foundational role.
- **Altair/Vega-Altair** — inherits Vega-Lite's entire theme/embed ecosystem for free (same spec format); ships `vega-datasets` bundled for examples.

The pattern worth naming: **grammar-spec tools (Vega-Lite/Altair) and foundation libraries (D3/matplotlib) both earn outsized ecosystem leverage** — the former because other tools can consume their spec format directly, the latter because other tools build on top rather than re-solving scales/layout math. Opinionated pre-built-component libraries (Recharts, ApexCharts) earn ecosystem breadth through framework-binding coverage instead, since there's no spec/foundation for others to build on.

## Comparison matrix — all tools

Core dimensions only, for a global scan. Full per-tool detail is in the category sections above.

| Tool | Category | Stars | License | Maintenance | Rendering |
| --- | --- | --- | --- | --- | --- |
| D3.js | JS foundation | 113.4k · 🟩🟩 | ISC · 🟩 | 🟩 | SVG/Canvas/HTML |
| Vega | JS grammar | 12.0k · 🟩🟩 | BSD-3 · 🟩 | 🟩🟩 | Canvas/SVG |
| Vega-Lite | JS grammar | 5.4k · 🟩 | BSD-3 · 🟩 | 🟩🟩 | compiles→Vega |
| Observable Plot | JS grammar | 5.3k · 🟩 | ISC · 🟩 | 🟩🟩 | SVG |
| Chart.js | JS canvas | 67.6k · 🟩🟩 | MIT · 🟩 | 🟩 | Canvas |
| ApexCharts | JS SVG | 15.1k · 🟩🟩 | Dual/commercial · 🟪 | 🟩🟩 | SVG |
| ECharts | JS canvas/SVG/GL | 67.1k · 🟩🟩 | Apache-2.0 · 🟩 | 🟩🟩 | Canvas/SVG/WebGL |
| Highcharts | JS commercial | 12.5k · 🟩🟩 | Commercial · 🟪 | 🟩🟩 | SVG/Canvas |
| Plotly.js | JS scientific | 18.3k · 🟩🟩 | MIT · 🟩 | 🟩🟩 | SVG/WebGL |
| uPlot | JS perf | 10.4k · 🟩🟩 | MIT · 🟩 | 🟩 | Canvas |
| Chartist | JS minimal | 13.4k · 🟩🟩 | MIT · 🟩 | 🟩 | SVG |
| Frappe Charts | JS minimal | 15.1k · 🟩🟩 | MIT · 🟩 | 🟥 | SVG |
| Billboard.js | JS D3-based | 6.0k · 🟩 | MIT · 🟩 | 🟩🟩 | SVG/Canvas |
| visx | React primitives | 21.0k · 🟩🟩 | MIT · 🟩 | 🟩 | SVG |
| Recharts | React components | 27.5k · 🟩🟩 | MIT · 🟩 | 🟩🟩 | SVG |
| Nivo | React components | 14.1k · 🟩🟩 | MIT · 🟩 | 🟩🟩 | SVG/HTML/Canvas |
| Victory | React composable | 11.2k · 🟩🟩 | MIT · 🟩 | 🟩 | SVG |
| react-chartjs-2 | React wrapper | 6.9k · 🟩 | MIT · 🟩 | 🟩 | Canvas |
| Matplotlib | Python foundation | 23.1k · 🟩🟩 | Matplotlib License · 🟩 | 🟩🟩 | Static |
| Seaborn | Python statistical | 14.0k · 🟩🟩 | BSD-3 · 🟩 | 🟩🟩 | Static |
| Plotly (py) | Python interactive | 18.7k · 🟩🟩 | MIT · 🟩 | 🟩🟩 | Interactive HTML |
| Bokeh | Python interactive | 20.4k · 🟩🟩 | BSD-3 · 🟩 | 🟩🟩 | Interactive HTML + server |
| Altair | Python grammar | 10.5k · 🟩🟩 | BSD-3 · 🟩 | 🟩🟩 | compiles→Vega-Lite |
| HoloViews | Python meta-layer | 2.9k · 🟩 | BSD-3 · 🟩 | 🟩🟩 | Backend-delegated |
| Pygal | Python minimal | 2.8k · 🟩 | LGPL-3.0 · 🟨 | 🟩🟩 | Static SVG |
| Plotnine | Python grammar | 4.8k · 🟩 | MIT · 🟩 | 🟩🟩 | Static |

## Decision guide by use case

Charting tools are not substitutable across use cases — a static-report library can't do interactive brushing, and a Jupyter notebook tool can't ship inside a React SPA bundle. Pick by job, not by star count.

| Use case | Pick | Why |
| --- | --- | --- |
| **Static chart for a PDF/print report** | Matplotlib (Python) or D3 → static SVG export (JS) | No interactivity needed; matplotlib's PNG/PDF/SVG export is the default in nearly every academic/reporting pipeline. If the pipeline is already JS-side, render to static SVG rather than pulling in an interactive library's runtime. |
| **Interactive dashboard embedded in a React SPA** | Recharts (fast to ship) or visx (custom/branded shapes) | Both let React own the DOM — no fighting React's diffing with imperative D3 mutation. Recharts wins on speed with pre-built components; visx wins when the design doesn't fit any pre-built chart shape. |
| **Exploratory data analysis in a Jupyter notebook** | Matplotlib/Seaborn for the first quick look; Plotly or Bokeh once you need to zoom/hover/filter interactively; Altair when the spec itself should be reusable | EDA is iterative — start with the fastest one-liners, escalate to interactivity only once a specific plot needs interrogation. |
| **A declarative chart spec you want under version control, regenerated from data automatically** | Vega-Lite (JS/JSON) or Altair (Python, compiles to the same Vega-Lite JSON) | The entire point of a grammar spec is that it's a diffable, PR-reviewable artifact, not a script with side effects. Pick whichever matches the pipeline's language — the spec format is identical either way (see the [lineage section](#the-declarative-grammar-of-graphics-lineage)). |
| **Huge dataset — 100k+ points, real-time streaming, financial tick data** | uPlot (JS, Canvas, purpose-built) or Plotly.js `scattergl` (WebGL) | uPlot's entire design brief is "smallest/fastest time-series chart"; benchmarks routinely show 10-100× the render speed of SVG-based libraries at high point counts. `scattergl` is the fallback when Plotly's broader chart-type catalogue is also needed at scale. |
| **Publication-quality statistical chart (papers, dissertations)** | Seaborn (Python), or Plotnine if the team already thinks in ggplot2's grammar | Seaborn's defaults encode statistically sound choices (confidence intervals, appropriate binning) out of the box; Plotnine makes multi-variable faceting explicit and reviewable via the ggplot2 grammar. |
| **GIS / geographic data** | ECharts (built-in geo/map series), D3 (`d3-geo` projections, full control), or Vega-Lite's `geoshape` mark | None of the tools here are dedicated GIS tools — for serious mapping, reach for Leaflet, Mapbox GL, deck.gl, or Kepler.gl (out of scope for this article). For chart-adjacent choropleths/geo-encoded points, ECharts ships map series out of the box, D3 gives full projection control, and Vega-Lite keeps it declarative. |
| **Embedding inline on GitHub/Notion/a markdown viewer, zero build step** | None of the tools above — that's terminal-chart / diagram-engine territory | See [`diagramming.md`'s terminal-chart section](diagramming.md#terminal-charts) (YouPlot, plotext, asciichart) for text-rendered numeric plots, and [Kroki's `vega-lite` engine](diagramming.md#meta-aggregator-kroki) for server-rendered embeds. |
| **Minimal config, small bundle, no framework commitment** | Chart.js, or Frappe Charts/Chartist for the smallest footprint | Chart.js is the default safe MIT-licensed canvas choice; Frappe Charts and Chartist trade chart-type breadth for the smallest bundle when that's the binding constraint. |
| **Commercial product where enterprise support/SLA matters and budget exists** | Highcharts, or ApexCharts' commercial tier | Both sell support contracts; Highcharts has the longer commercial track record (Highstock is a financial-industry standard). ApexCharts converted to dual-licensing in 2025 specifically to fund paid support — see the `[!CAUTION]` in the [JS/web table](#jsweb-charting). |
| **Python app that needs to be a shareable interactive tool, not just a chart** | Streamlit (general-purpose) or Dash (Plotly-native) | Neither is a chart library — both are the app-shell layer that turns a chart into something a non-technical stakeholder can open in a browser. See [App-shell delivery layers](#app-shell-delivery-layers). |

> [!NOTE]
> **No Gleam-native charting library exists** as of this snapshot (checked `gleam/` for prior coverage; the only chart-adjacent mention is [`gleam/cli.md`'s TUI-widget gap note](../gleam/cli.md), which flags the *absence* of Ratatui-style chart/sparkline widgets on BEAM). A Gleam project that compiles to JS can pull in any tool from the [JS/web](#jsweb-charting) or [React](#the-react-specific-layer) sections directly as an npm dependency (Lustre + Vega-Lite or Chart.js is the natural pairing); a BEAM-side Gleam project has no native path and would need to shell out or serve pre-rendered images.

## Leaderboards

Charting tools aren't substitutable across categories any more than they are across use cases — ranked within category, not globally.

### JS/web, general-purpose

1. **🥇 [D3.js](#d3js)** — 113.4k★, ISC, the foundation everything else in this section is built on or inspired by. Pick directly only when nothing pre-built fits; otherwise it's what you're already using indirectly.
2. **🥈 [ECharts](#echarts-apache-echarts)** — 67.1k★, Apache-2.0, broadest chart-type catalogue of any single library reviewed, Apache Software Foundation governance.
3. **🥉 [Chart.js](#chartjs)** — 67.6k★, MIT, the safest default for "I need a chart, no ceremony."
4. **[Plotly.js](#plotlyjs)** — 18.3k★, MIT, deepest scientific/3D/geo breadth; shared engine with Python via [Plotly (py)](#plotly-python).
5. **[Vega-Lite](#vega--vega-lite)** — 5.4k★, BSD-3, the versionable-spec choice; pairs with [Vega](#vega--vega-lite) underneath.
6. **[ApexCharts](#apexcharts)** — 15.1k★, but 🟪 dual-licensed since v5.0.0 — verify the revenue cap before adopting.
7. **[Highcharts](#highcharts)** — 12.5k★, 🟪 commercial, widest domain-module catalogue (financial/geo/Gantt) but paid.
8. **[Observable Plot](#observable-plot)** — 5.3k★, ISC, modern grammar API with a smaller footprint than Vega-Lite.
9. **[uPlot](#uplot)** — 10.4k★, MIT, best-in-class perf niche (time-series only).
10. **[Billboard.js](#billboardjs)** / **[Chartist](#chartist)** / **[Frappe Charts](#frappe-charts)** — lightweight tier; Chartist/Frappe Charts tie on scope, Frappe Charts carries a 🟥 maintenance flag.

### React-specific layer

1. **🥇 [Recharts](#recharts)** — 27.5k★, most active (commit today), broadest pre-built catalogue, fastest to ship.
2. **🥈 [Nivo](#nivo)** — 14.1k★, widest chart-type breadth of the opinionated-component tier, multi-renderer (SVG/HTML/Canvas).
3. **🥉 [visx](#visx)** — 21.0k★, best for fully custom/branded design work via composable primitives.
4. **[Victory](#victory)** — 11.2k★, solid alternative, unique React Native parity via `victory-native-xl`.
5. **[react-chartjs-2](#react-chartjs-2)** — 6.9k★, use only when already committed to Chart.js elsewhere.

*Disregarded: [react-vis](#disregarded) (deprecated, see below). Not scored: [Tremor](#the-react-specific-layer) (dashboard UI kit, different evaluation framework).*

### Python

1. **🥇 [Matplotlib](#matplotlib)** — 23.1k★, the foundation; nearly everything else in this section renders through or is inspired by it.
2. **🥈 [Plotly (py)](#plotly-python)** — 18.7k★, best all-around interactive + broadest breadth + shared engine with the JS side.
3. **🥉 [Bokeh](#bokeh)** — 20.4k★, only tool here with genuine server-backed Python-callback interactivity.
4. **[Altair](#altair--vega-altair)** — 10.5k★, best for versionable declarative specs shared with JS via Vega-Lite.
5. **[Seaborn](#seaborn)** — 14.0k★, best statistical-EDA defaults.
6. **[HoloViews](#holoviews)** — 2.9k★, best for backend-agnostic power users already in the HoloViz stack.
7. **[Plotnine](#plotnine)** — 4.8k★, best for teams standardized on ggplot2's exact grammar.
8. **[Pygal](#pygal)** — 2.8k★, lightweight pure-SVG niche; only LGPL entry in this tier.

## Disregarded

- **[uber/react-vis](https://github.com/uber/react-vis)** — 8.8k★, MIT, last commit **2024-12-18** (>19 months stale as of snapshot), last tagged release **2019-04-19** (>7 years stale). Ships a `DEPRECATED.md` reading verbatim: *"Unfortunately, react-vis currently has no active maintainers. As such, we have decided to deprecate the library... won't receive any patches or new features... Anyone is welcome to fork this library."* Repo is not formally GitHub-archived but is functionally dead. Do not use for new projects — Recharts/Nivo/visx cover the same ground actively.
- **[gionkunz/chartist-js](https://github.com/gionkunz/chartist-js)** — the original Chartist repo, now self-described as *"Legacy Chartist Repo for old gh-pages,"* last pushed 2024-05-06. **Not a competing project** — a supersession, not a parallel-discovery case: development continues at [`chartist-js/chartist`](#chartist) (org-level continuation, same npm package name `chartist`). Link to the org repo, not the original author's fork, for anything current.
- **GIS/mapping tools (Leaflet, Mapbox GL, deck.gl, Kepler.gl)** — named in the [decision guide](#decision-guide-by-use-case)'s GIS row as the honest answer for serious mapping needs, but not reviewed here — out of scope for a *charting* article; they're mapping/geospatial engines, a different tool class even though ECharts/D3/Vega-Lite offer basic geo support.
- **Dedicated dashboard/BI platforms** (Tableau, Power BI, Metabase, Superset, Grafana) — out of scope. This article covers *libraries you write code against*, not hosted/GUI-first BI products. Would warrant a separate article if requested.

## Discovery — search queries

Queries used (Google, GitHub topic walks, live GitHub REST API via `gh api`) to assemble and verify this article's coverage against the task's named must-haves (D3, Vega, Vega-Lite, visx, Chart.js) plus broader sweep:

- `javascript charting library comparison 2026 D3 vega chart.js echarts`
- `best charting library react D3 visx recharts nivo victory comparison`
- `python interactive plotting library comparison plotly bokeh altair holoviews 2026`
- `grammar of graphics javascript vega-lite observable plot ggplot2`
- `declarative visualization spec JSON vega-lite altair python bindings`
- `canvas vs svg vs webgl chart rendering performance benchmark large dataset`
- `apexcharts license change 2025 dual license commercial`
- `highcharts license free commercial use`
- `chartist js maintained fork gionkunz vs chartist-js org`
- `react-vis deprecated uber maintained alternative`
- `victory formidable nearform acquisition maintained`
- `tremor react dashboard components vercel acquisition`
- `uplot fast time series chart performance`
- `frappe charts vs chartist svg zero dependency`
- `plotnine ggplot2 python grammar of graphics`
- `holoviews bokeh matplotlib plotly backend agnostic`
- GitHub REST API (`gh api repos/<org>/<repo>`) direct pulls for stars/license/pushed_at/open_issues on all ~27 core repos, cross-checked license files directly (`contents/LICENSE`) where `license.spdx_id` returned `NOASSERTION` (Victory, Highcharts, ApexCharts).

New repos added in this pass: **D3.js, Vega, Vega-Lite, Observable Plot, Chart.js, ApexCharts, ECharts, Highcharts, Plotly.js, uPlot, Chartist, Frappe Charts, Billboard.js, visx, Recharts, Nivo, Victory, react-chartjs-2, Matplotlib, Seaborn, Plotly (Python), Bokeh, Altair/Vega-Altair, HoloViews, Pygal, Plotnine, Streamlit, Dash, Panel, Gradio**. Noted in Disregarded/asides: **react-vis, gionkunz/chartist-js (legacy), Tremor** (mentioned, not leaderboard-scored), **GIS tools** (named, not reviewed).

---

**Snapshot 2026-08-12.** Submit issues for missing tools, repo updates, or corrections.
