# Typography — Vertical Rhythm, Type Scales, Fluid Type, and Trimming the Leading

> [!NOTE]
> **Status:** DRAFT · **Authoring:** AI-assisted, human-reviewed.

> **Explicitly the half that [`ux.md`](ux.md) excludes.** The UX article draws a line at *"typography, colour systems, brand identity, illustration, motion"* and reviews only the flow side. This article picks up the first item on that list — but only the **typesetting mechanics** a front-end engineer has to get right: how line boxes are built, how spacing stays consistent down a page, how sizes scale across viewports, and how to trim the space a font puts above and below its letters. Typeface *selection* (which font) and pairing are out of scope. So is print.

**Snapshot 2026-09-07** — GitHub metrics from the REST API (`stargazers_count`, `license.spdx_id`, `pushed_at`, `created_at`, `open_issues_count`) via `gh api`, captured 2026-09-06/07. npm weekly downloads from `api.npmjs.org` for the week 2026-08-31 → 2026-09-06. Browser versions from caniuse.com, MDN and `mdn/browser-compat-data`. Tool outputs marked *reproduced* were generated in a fresh `npm init` project on the snapshot date (`@capsizecss/core 4.1.3`, `utopia-core 1.6.0`, Node 22.22.0).

## Table of Contents

1. [TL;DR — what to do](#tldr--what-to-do)
2. [Vocabulary](#vocabulary)
3. [Background: how a browser builds a line](#background-how-a-browser-builds-a-line)
4. [Vertical rhythm — the practice and the debate](#vertical-rhythm--the-practice-and-the-debate)
5. [Type scales — ratios, and what real design systems actually ship](#type-scales--ratios-and-what-real-design-systems-actually-ship)
6. [Fluid typography](#fluid-typography)
7. [Trimming the leading — Capsize and `text-box-trim`](#trimming-the-leading--capsize-and-text-box-trim)
8. [Metric-matched fallback fonts](#metric-matched-fallback-fonts)
9. ["Vertical rhythm reset" — three things the phrase can mean](#vertical-rhythm-reset--three-things-the-phrase-can-mean)
10. [Accessibility floors and reading research](#accessibility-floors-and-reading-research)
11. [Native platforms — the same problem, different knobs](#native-platforms--the-same-problem-different-knobs)
12. [Browser support](#browser-support)
13. [Aspects we score on](#aspects-we-score-on)
14. [Tools — rhythm & scale libraries](#tools--rhythm--scale-libraries)
15. [Tools — fluid type](#tools--fluid-type)
16. [Tools — leading trim, font metrics, fallbacks](#tools--leading-trim-font-metrics-fallbacks)
17. [Tools — calculators and overlays](#tools--calculators-and-overlays)
18. [When to use what](#when-to-use-what)
19. [Leaderboards](#leaderboards)
20. [Disregarded](#disregarded)
21. [Discovery — search queries](#discovery--search-queries)
22. [Not verified](#not-verified)

## TL;DR — what to do

If you only read one table:

| You want… | Do this | Detail |
| --- | --- | --- |
| Body text that reads well and passes WCAG | `font-size: 1rem` or up; `line-height: 1.5`; measure `max-width: 60–75ch`; `text-wrap: pretty` on paragraphs | [Accessibility floors](#accessibility-floors-and-reading-research) |
| Consistent vertical spacing without a Sass toolchain | Pick one unit = body line-height; space blocks with `margin-block-start: 1lh` (or `1rlh`) via a `.flow > * + *` rule; keep all line-heights integer multiples of the base at the sizes you actually use | [Vertical rhythm](#vertical-rhythm--the-practice-and-the-debate) |
| Headings that scale between phone and desktop with no breakpoints | `clamp()` from [Utopia](https://utopia.fyi) or `utopia-core`; keep a `rem` term in the middle; run the WCAG 1.4.4 check | [Fluid typography](#fluid-typography) |
| Text that lines up with icons/images and buttons whose padding is what you typed | `text-box: trim-both cap alphabetic` (Chrome 133 / Safari 18.2 / Firefox 154), with [Capsize](#capsize) as the fallback for older browsers or CSS-in-JS | [Trimming the leading](#trimming-the-leading--capsize-and-text-box-trim) |
| No layout shift when the web font arrives | `fontaine` (Vite/Nuxt/Astro), `next/font` (Next.js), or Capsize's `createFontStack` — all emit `size-adjust` + `ascent-override` `@font-face` rules | [Metric-matched fallbacks](#metric-matched-fallback-fonts) |
| A print-style baseline grid where every glyph baseline sits on a line | Accept that CSS has no primitive for it; use `plumber` (Sass, dormant) or per-font `text-box-trim` + `1rlh` spacing; snap images with `round(up, …, 1rlh)` (Chromium) or a `ResizeObserver` | [Background](#background-how-a-browser-builds-a-line), [gotchas](#gotchas-that-break-the-grid) |

> [!IMPORTANT]
> **The tools in this article are not substitutable.** A type-scale calculator, a fluid-`clamp()` generator, a leading-trim library, and a metric-matched-fallback generator solve four different problems, and a modern stack usually uses one from each. Leaderboards at the bottom rank *within* a category only. Pick by job using [When to use what](#when-to-use-what).

## Vocabulary

| Term | Meaning | Canonical source |
| --- | --- | --- |
| **Leading / line-height** | Space from one baseline to the next. Historically strips of lead between lines of metal type. In CSS, `line-height` is the height of the *line box*; the extra beyond the font's ascent + descent is split in half above and below the glyphs ("half-leading"). | [CSS Inline Layout 3 §5.3](https://www.w3.org/TR/css-inline-3/#inline-height) |
| **Vertical rhythm** | Every vertical dimension on the page (font sizes × line-heights, margins, paddings, borders) is a multiple of one base unit, usually the body line-height. Rutter: *"Space in typography is like time in music. It is infinitely divisible, but a few proportional intervals can be much more useful than a limitless choice of arbitrary quantities."* | [Rutter, 24ways 2006](https://24ways.org/2006/compose-to-a-vertical-rhythm/); Bringhurst §2.2.2 via [webtypography.net](http://webtypography.net/2.2.2) |
| **Baseline grid** | The print version: an invisible ruled grid, and the *glyph baseline* of every line of text sits on a rule — across columns and pages. Alignment target is the baseline, not the box. Brunborg: *"Our eyes are not trained to follow the x-axis center of characters when scanning lines of text — they're trained to follow the baseline."* | [Brunborg, Smashing 2012](https://www.smashingmagazine.com/2012/12/css-baseline-the-good-the-bad-and-the-ugly/); Müller-Brockmann, *Grid Systems* (1981) |
| **Measure** | Characters per line including spaces. Bringhurst/Rutter: *"Anything from 45 to 75 characters is widely regarded as a satisfactory length of line"*; 66 *"widely regarded as ideal"*. On the web, set it in `ch` so it survives font-size changes. | [webtypography.net 2.1.2](http://webtypography.net/2.1.2); [Butterick](https://practicaltypography.com/line-length.html) (45–90) |
| **Modular scale / type scale** | Sizes derived as base × ratioⁿ. Named ratios (typescale.com): minor second 1.067, major second 1.125, minor third 1.200, major third 1.250, perfect fourth 1.333, augmented fourth 1.414, perfect fifth 1.500, golden 1.618. Brown quoting Bringhurst: *"A modular scale, like a musical scale, is a prearranged set of harmonious proportions."* | [Brown, A List Apart 2011](https://alistapart.com/article/more-meaningful-typography/); [typescale.com](https://typescale.com/); [modularscale.com](https://www.modularscale.com/) |
| **Fluid type** | A `font-size` that interpolates linearly between a minimum at a small viewport and a maximum at a large one, with no breakpoints: `clamp(min, intercept + slope·vw, max)`. | [Riethmuller 2015](https://www.madebymike.com.au/writing/precise-control-responsive-typography/); [Brown, "CSS Locks" 2016](https://blog.typekit.com/2016/08/17/flexible-typography-with-css-locks/); [Utopia](https://utopia.fyi) |
| **Cap height / x-height / ascent / descent** | Font-file metrics in units-per-em (`unitsPerEm`, usually 1000 or 2048). Cap height = height of `H`; x-height = height of `x`; ascent/descent = the extents the browser uses for the *content area*. | [De Oliveira, 2017](https://iamvdo.me/en/blog/css-font-metrics-line-height-and-vertical-align) |
| **Leading trim** | Removing the half-leading (and the gap between cap height and ascender) at the top of the first line and the bottom of the last, so a text box's edges are the cap line and the baseline. Spec'd as `text-box-trim` / `text-box-edge` (originally `leading-trim` / `text-edge`). | [MDN `text-box-trim`](https://developer.mozilla.org/en-US/docs/Web/CSS/text-box-trim); [Wang, Microsoft Design 2020](https://medium.com/microsoft-design/leading-trim-the-future-of-digital-typesetting-d082d84b202) |
| **`lh` / `rlh`** | CSS length units equal to the element's (`lh`) or root element's (`rlh`) computed line-height. The modern way to write "one line" as a length. | [MDN length](https://developer.mozilla.org/en-US/docs/Web/CSS/length); [Burzo 2023](https://danburzo.ro/line-height-lh/) |
| **`cap` / `ex` / `ic`** | Length units equal to the font's cap height, x-height, and the advance of the CJK water ideograph 水. `cap` sizes an icon to the capitals next to it. | [MDN length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) |

## Background: how a browser builds a line

Most tools reviewed below are thin wrappers around one mechanism, so it is worth stating it once. The model is Vincent De Oliveira's [*Deep dive CSS: font metrics, line-height and vertical-align*](https://iamvdo.me/en/blog/css-font-metrics-line-height-and-vertical-align) (2017), still the clearest single source.

1. **The font defines an em-square** (`unitsPerEm`, typically 1000 or 2048) and, inside it, an ascender and descender. Inter, for example, ships `ascent 1984`, `descent −494`, `capHeight 1490`, `xHeight 1118` on a 2048 em (*reproduced* from `@capsizecss/metrics/inter`).
2. **The content area** of an inline box is `(ascent + descent) × font-size / unitsPerEm`. For Inter at 16px that is (1984 + 494) / 2048 × 16 ≈ **19.36px** — already taller than the font-size, before any line-height is applied. De Oliveira measured `line-height: normal` across 1,117 installed fonts and found computed values from **0.618 to 3.378**, not the "about 1.2" folklore.
3. **Half-leading.** CSS Inline 3 §5.3: *"Half the leading (its half-leading) is added above A of the first available font, and the other half below D of the first available font."* So with `line-height: 24px` on 16px Inter, (24 − 19.36) / 2 ≈ 2.32px goes above the ascender and 2.32px below the descender. The glyphs are vertically *centred in the line box* — where the baseline lands depends on the font's ascent:descent ratio.
4. **Consequence: baselines don't align across fonts or sizes.** Two fonts sharing a 24px line box put their baselines at different offsets; the same font at 16px and 32px in 24px/48px boxes does too. The spec says so directly (§5.2): *"vertical rhythm can be broken any time there is a change in font metrics or vertical alignment."* Every "baseline grid in CSS" technique — Basekick's transforms, Plumber's per-font padding, Capsize's negative pseudo-element margins, and now `text-box-trim` — exists to cancel this centring.
5. **Which metrics?** A font file carries three candidate ascent/descent sets (`hhea`, `OS/2 typo`, `OS/2 win`), and browsers/OSes disagree on which wins unless the `USE_TYPO_METRICS` flag is set. This is why "the same font" renders taller on Windows than macOS, why Noto CJK line boxes on Linux/Android come out ~45% taller than Windows CJK fonts ([Mozilla bug 1733291](https://bugzilla.mozilla.org/show_bug.cgi?id=1733291), UNCONFIRMED since 2021), and why Capsize's metrics package exists at all.
6. **Unitless vs unit-ed line-height.** `line-height: 1.5` inherits the *factor* (each descendant recomputes against its own font-size). `1.5em` / `150%` / `24px` inherit the *computed length*. MDN's worked example: parent 15px × `1.1em` = 16.5px; a 30px child still inherits 16.5px. A rhythm system may *want* the fixed-length behaviour (`line-height: 1.5rem` on `html` = every line box is 24px, period); a readability-first stylesheet wants the factor. Pick one policy per project and write it down.

## Vertical rhythm — the practice and the debate

### The minimum viable rhythm (2026 edition)

Everything the 2006–2016 Sass libraries did can now be written in a dozen lines of plain CSS:

```css
html {
  font-size: 100%;          /* respect the user's browser setting */
  line-height: 1.5;         /* 16px × 1.5 = 24px = the rhythm unit */
}

/* One unit of space between siblings, and nowhere else.  */
.flow > * + * {
  margin-block-start: var(--flow-space, 1rlh);
}

/* Headings sized on a scale, line-heights snapped to whole units. */
h1 { font-size: 2rem;    line-height: 2rlh; }   /* 32px in a 48px box */
h2 { font-size: 1.5rem;  line-height: 2rlh; }   /* 24px in a 48px box — or 1rlh if you accept tight leading */
h3 { font-size: 1.25rem; line-height: 1rlh; }   /* 20px in a 24px box */

/* Debug overlay — Burzo's ruled-paper trick. */
html.debug-grid {
  background: repeating-linear-gradient(to bottom, transparent 0 calc(1lh - 1px), rgb(255 0 0 / .25) 0 1lh);
}
```

The moving parts, and where each comes from:

- **Line-height as the base unit.** Harry Roberts' 2012 [*Single-direction margin declarations*](https://csswizardry.com/2012/06/single-direction-margin-declarations/) called 1.5 the *"Magic Number"* and put `margin-bottom: 1.5rem` on every block element — margins in one direction only, so the rhythm never double-counts. Rutter's 2006 article did the same with 12px × 1.5em = 18px.
- **The owl selector.** Heydon Pickering and Andy Bell's [Every Layout *Stack*](https://every-layout.dev/layouts/stack/) — `* + *` — puts space *between* siblings, so the first child has no leading margin and nested containers don't accumulate. Their rule: *"vertical spacing of your design should be based on your standard line-height because text dominates most pages' layout."* Bell's later [`.flow`](https://piccalil.li/blog/my-favourite-3-lines-of-css/) (2023) adds the `--flow-space` custom property so a heading can opt into 2 units without a new class.
- **`lh` / `rlh`** (Baseline *widely available* since 2026-05-21, see [Browser support](#browser-support)) replace the Sass `rhythm()` function: "one line" is now a CSS length. Dan Burzo's [cookbook](https://danburzo.ro/line-height-lh/) covers icon sizing (`height: 1lh`), the debug gradient above, and the trap that inside `font-size` / `line-height` declarations `1lh` resolves against the *parent*.
- **`margin-trim: block`** would make the owl selector unnecessary by trimming the first/last child margins at a container's edge — but it is Safari-only (16.4+, experimental), so `.flow` stays.

### Gotchas that break the grid

| # | Problem | What to do |
| --- | --- | --- |
| 1 | **Any element whose height isn't n × unit** shifts everything after it: borders, `<hr>`, images, embeds, form controls. Rutter 2006 named borders as the classic offender. | Subtract borders from padding (`padding-block: calc(1rlh - 1px)` + `border-block: 1px`) — Compass encoded this as `rhythm-borders`. |
| 2 | **Inline elements taller than the line** (`<sup>`, `<code>` in a different font, inline-blocks, emoji fallback fonts) inflate one line box. | `sup, sub { line-height: 0 }`; give `code` the same line-height; test with an emoji in body copy. |
| 3 | **Images** have unknown height until layout. | Pure CSS, Chromium-only today: `height: round(up, calc(var(--rendered-w) / var(--ar)), 1rlh); object-fit: cover` with `--ar` read from `attr(width type(<number>))` ([Coudeville](https://dev.to/simoncoudeville/aligning-images-to-a-baseline-grid-with-modern-css-5fi4)). Cross-browser: a `ResizeObserver` that pads `height mod unit` ([Bernat, 2026](https://vincent.bernat.ch/en/blog/2026-css-vertical-rhythm)). Spec-level answer, `block-step-size: 1rlh` from [CSS Rhythmic Sizing](https://www.w3.org/TR/css-rhythm-1/), ships nowhere. |
| 4 | **Subpixel rounding.** Firefox lays out in 1/60px, Chrome/Safari in 1/64px; Safari floors fractional line-heights ([WebKit bug 225695](https://bugs.webkit.org/show_bug.cgi?id=225695), open). 17px × 1.4 = 23.8px compounds into visible drift after ~10 lines. | Choose sizes so `font-size × line-height` is an integer: 16 × 1.5 = 24, 18 × 1.5 = 27, 20 × 1.4 = 28. Zell Liew tested baseline overlays in 2016 and found Firefox *"the only browser I had that didn't get affected by subpixel rounding errors"* ([source](https://zellwk.com/blog/web-typography-broken/)). |
| 5 | **Nested `em` compounding.** `span { font-size: 1.6em }` nested twice = 40.96px. Every `em`-based rhythm system (Rutter 2006, `typesettings`) has to recompute line-height at every nesting level. | Use `rem` for sizes and `rlh` for space. MDN: `rem` *"were invented in order to sidestep the compounding problem."* |
| 6 | **`line-height-step`** — the property that would round every line box up to a multiple of the step — is behind a Chrome flag since M60, was pulled from BCD because nothing ships it, and Mozilla was *"skeptical about real-world demand"* ([Intent thread](https://groups.google.com/a/chromium.org/d/topic/blink-dev/z0RI8seNuCs)). | Don't build on it. |
| 7 | **CJK and Arabic** faces carry tall vertical metrics; `line-height: normal` produces boxes nothing else on the page matches. | Always set an explicit numeric line-height (Chinese practice: 1.5–2.0); check descender clipping below ~1.4. |

### The debate, and where it lands

The sceptical case is not that rhythm is bad, it is that **baseline grids are a print artefact** and the web version is cheaper than people think.

- Zell Liew, 2016, after auditing real sites: *"Following it to the pixel isn't"* important; what works is *"repetition breeds familiarity"* — sites succeed by repeating a small set of spacing values, not by snapping baselines ([*Is Web Typography Completely Broken?*](https://zellwk.com/blog/web-typography-broken/)). His practical rule in the [companion post](https://zellwk.com/blog/why-vertical-rhythms/): *"Set the vertical white space between elements to a multiple of 24px"* and *"Set the line-height of all text elements to a multiple of 24px."*
- Nathan Curtis, 2016, [*Space in Design Systems*](https://nathanacurtis.substack.com/p/space-in-design-systems-188bcbae0d62): design systems standardise on a **spacing scale** (Inset / Stack / Inline / Squish / Stretch, base 16 because *"It's a factor of all screen resolutions (320, 768, 1024)"*) and treat the line-height collision as a mixin problem, not a page-grid problem.
- Brunborg, 2012: the honest statement of the gap — vertical rhythm is *structural spacing of stacked elements*; a baseline grid is *characters aligning to predetermined horizontal lines*; CSS gives you the first for free and the second only with per-font offsets.
- The pro camp (Rutter, Brown, Bringhurst-via-webtypography.net, Bernat 2026) does not disagree with any of that. Bernat's 2026 article is a full `rlh` system *including* images and tables, and still concedes that true baseline alignment needs `block-step`, which doesn't exist.

**What it converges on:** (a) a strict baseline grid is not worth chasing in CSS — half-leading, images and mixed fonts fight you at every turn; (b) *line-height-multiple spacing* ("rhythm lite") is cheap, and `lh`/`rlh` + `.flow` make it a dozen lines; (c) for product UI, a **4/8px spacing scale with line-heights on the same grid** — what Material, Tailwind, Fluent and Apple all ship — gives most of the visual consistency with none of the fragility. The [design-system table](#type-scales--ratios-and-what-real-design-systems-actually-ship) below shows how few systems actually tie spacing to line-height.

## Type scales — ratios, and what real design systems actually ship

A modular scale is a ratio applied repeatedly to a base. Tim Brown's 2011 [*More Meaningful Typography*](https://alistapart.com/article/more-meaningful-typography/) popularised it for the web (and "double-stranded" scales: two bases, one ratio); Jeremy Church's [typescale.com](https://typescale.com/) (2013, formerly type-scale.com) made the named ratios a checkbox. The ratio you pick is a *design* decision (1.2 reads calm, 1.333+ reads editorial); what matters for engineering is whether line-heights and spacing land on a shared grid.

The table below is what the systems actually ship, read from their token sources on the snapshot date. **Rhythm column:** does the *spacing* scale derive from the *line-height* (true vertical rhythm), or is it an independent 4/8px scale?

| System | Base | Sizes (px) | Ratio | Line-height strategy | Space unit | Rhythm tied to type? | Fluid? | Trim? | Source |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Tailwind CSS v4** | 16 | 12 · 14 · 16 · 18 · 20 · 24 · 30 · 36 · 48 · 60 · 72 · 96 · 128 | hand-tuned | paired `--text-*--line-height`: 16 · 20 · 24 · 28 · 28 · 32 · 36 · 40, then `1` | 4px | No — line-heights are 4px multiples, not 24px multiples | No | No | [docs](https://tailwindcss.com/docs/font-size) |
| **`@tailwindcss/typography`** (`prose`) | 16 | 14 (`prose-sm`) … 24 (`prose-2xl`) | — | per-element `em` ratios (e.g. `lineHeight: 28/18`, margins `em(24, 18)`) | em | Partially — margins are px-equivalents of 16/24/32 expressed in em, so they hold at each `prose-*` size | No | No | [styles.js](https://github.com/tailwindlabs/tailwindcss-typography/blob/main/src/styles.js) (`max-width: 65ch` at line 1414) |
| **Bootstrap 5** | 16 (`$font-size-base: 1rem`) | h1 `× 2.5` … h6 `× 1`; display 1–6 | hand-tuned | `$line-height-base: 1.5`, `$headings-line-height: 1.2` | 16 (`$spacer: 1rem`) | **No** — `$paragraph-margin-bottom: 1rem` ≠ 1.5rem line; headings `margin-bottom: .5rem` | Yes via RFS (opt-in) | No | [`_variables.scss`](https://github.com/twbs/bootstrap/blob/main/scss/_variables.scss) |
| **Material 3** | 16 | display 57/45/36 · headline 32/28/24 · title 22/16/14 · body 16/14/12 · label 14/12/11 | hand-tuned | fixed per role: 64/52/44 · 40/36/32 · 28/24/20 · 24/20/16 · 20/16/16 — **every line-height is a multiple of 4** | 4dp / 8dp | Yes, to a 4dp grid: M2 specified a 4dp baseline grid for type with line-heights divisible by 4 (*secondary* — m2.material.io returned no body to the fetcher; every M3 line-height above is a 4-multiple, consistent with it) | No | No | [Flutter `typography.dart` `englishLike2021`](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/typography.dart) (sizes × `height` reproduce the sp table) |
| **MUI (Material 2 lineage)** | 14 on 16 html | h1 96 · h2 60 · h3 48 · h4 34 · h5 24 · h6 20 · body1 16 · body2 14 · caption 12 | hand-tuned | per-variant unitless: 1.167 · 1.2 · 1.167 · 1.235 · 1.334 · 1.6 · 1.5 · 1.43 · 1.66 | 8px | No | No | No | [`createTypography.js`](https://unpkg.com/@mui/material/styles/createTypography.js) |
| **Apple HIG (iOS, Large/default)** | 17 | Large Title 34 · Title 1 28 · Title 2 22 · Title 3 20 · Headline 17 · Body 17 · Callout 16 · Subhead 15 · Footnote 13 · Caption 1 12 · Caption 2 11 | hand-tuned | fixed "leading" per style: 41 · 34 · 28 · 25 · 22 · 22 · 21 · 20 · 18 · 16 · 13 | 8pt | No (leadings are not 4-multiples) — Dynamic Type re-issues the whole table per size class (xSmall: Large Title 31/38, Body 14/19; xLarge: Large Title 36/43, Title 1 30/37) | No — user-scaled, not viewport-scaled | No | [HIG Typography](https://developer.apple.com/design/human-interface-guidelines/typography) |
| **IBM Carbon** | 16 | 12 · 14 · 16 · 18 · 20 · 24 · 28 · 32 · 36 · 42 · 48 · 54 · 60 · 68 · 76 · 84 · 92 · 102 · 112 · 122 · 132 · 144 · 156 | **formula**: `Yₙ = Yₙ₋₁ + (⌊(n−2)/4⌋ + 1) × 2` — increments grow by 2px every 4 steps | per-token | 8px "mini unit" | No | Some tokens are fluid at breakpoints (docs; ⬜ not re-verified) | No | [`scale.ts`](https://github.com/carbon-design-system/carbon/blob/main/packages/type/src/scale.ts) |
| **Microsoft Fluent 2 (web)** | 14 (`Body1`) | 10 · 12 · 14 · 16 · 20 · 24 · 28 · 32 · 40 · 68 | hand-tuned | fixed: 14 · 16 · 20 · 22 · 28 · 32 · 36 · 40 · 52 · 92 | 4px | No | No | **No** — the team that proposed `leading-trim` ([Wang, 2020](https://medium.com/microsoft-design/leading-trim-the-future-of-digital-typesetting-d082d84b202)) does not use it in its own web tokens as of the snapshot | [`fonts.ts`](https://github.com/microsoft/fluentui/blob/master/packages/tokens/src/global/fonts.ts) |
| **GOV.UK Design System** | 19 | 16 · 19 · 24 · 27 · 36 · 48 · 80 (tablet+); 16 · 19 · 21 · 21 · 27 · 32 · 53 (mobile) | hand-tuned | fixed px per size *per breakpoint*: 20/25 · 25 · 30 · 30 · 40 · 50 · 80 | 5px-ish | No | **No — stepped at one breakpoint, deliberately not fluid** | No | [`_typography-responsive.scss`](https://github.com/alphagov/govuk-frontend/blob/main/packages/govuk-frontend/src/govuk/settings/_typography-responsive.scss) |
| **USWDS** | 16 | 10 · 12 · 13 · 14 · 15 · 16 · 17 · 18 · 20 · 22 · 24 · 28 · 32 · 36 · 40 · 48 · 56 · 64 · 80 · 120 · 140 (21 steps) | hand-tuned | 6 unitless tokens: 1 · 1.2 · 1.35 · 1.5 · 1.62 · 1.75 | 8px | No | No | No | [`type-scale.scss`](https://github.com/uswds/uswds/blob/develop/packages/uswds-core/src/styles/tokens/font/type-scale.scss) |
| **Adobe Spectrum** | 14 desktop / 17 mobile | desktop 10 · 11 · 12 · 14 · 16 · 18 · 20 · 22 · 25 · 28 · 32 · 36 · 40 · 45 · 51 · 58 · 65 · 73 | ≈1.125 per step | paired px per size (14→18, 16→20, 18→22 …); global multipliers `line-height-100: 1.3`, `-200: 1.5`; CJK 1.5 / 1.7 | 8px | No | No — two fixed platform scales | No | [`typography.json`](https://github.com/adobe/spectrum-design-data/blob/main/packages/tokens/src/typography.json) |
| **Shopify Polaris** | 13/14 | 11 · 12 · 13 · 14 · 16 · 18 · 20 · 22 · 24 · 30 · 32 · 36 · 40 | hand-tuned | line-height tokens 12 · 16 · 20 · 24 · 28 · 32 · 40 · 48 (all 4-multiples) | 4px | No | No | No | [`font.ts`](https://github.com/Shopify/polaris/blob/main/polaris-tokens/src/themes/base/font.ts) |
| **GitHub Primer** | 14 (*"Default body text size for UI"*) | 12 · 14 · 16 · 20 · 32 · 40 | hand-tuned | 5 unitless: 1.25 · 1.375 · 1.5 · 1.625 · 1.75 | 4/8px | No | No | No | [`typography.json5`](https://github.com/primer/primitives/blob/main/src/tokens/base/typography/typography.json5) |
| **Radix Themes** | 16 | 12 · 14 · 16 · 18 · 20 · 24 · 28 · 35 · 60 | hand-tuned | paired px: 16 · 20 · 24 · 26 · 28 · 30 · 36 · 40 · 60 | 4px | No — steps 1–3 on the 4px grid, 4+ off it | No | No | [docs](https://www.radix-ui.com/themes/docs/theme/typography) |
| **Chakra UI v3** | 16 | 10 · 12 · 14 · 16 · 18 · 20 · 24 · 30 · 36 · 48 · 60 · 72 · 96 · 128 | hand-tuned | named unitless: `shorter` 1.25 · `short` 1.375 · `moderate` 1.5 · `tall` 1.625 · `taller` 2 | 4px | No | No | No | [docs](https://chakra-ui.com/docs/theming/typography) |
| **Open Props** | 16 | `.5 · .75 · 1 · 1.1 · 1.25 · 1.5 · 2 · 2.5 · 3 · 3.5rem` | hand-tuned | `--font-lineheight-00..5`: .95 · 1.1 · 1.25 · 1.375 · 1.5 · 1.75 · 2 | rem | No | **Yes** — `--font-size-fluid-0: clamp(.75rem, 2vw, 1rem)` … `-3: clamp(2rem, 9vw, 3.5rem)` (no `rem` term in the middle — see [accessibility](#accessibility-of-fluid-type)) | No | [`props.fonts.css`](https://github.com/argyleink/open-props/blob/main/src/props.fonts.css) |
| **Every Layout** | 16 | `--s-1 … --s5` = base × 1.5ⁿ via `calc()` | **1.5** (same number as the line-height, on purpose) | 1.5 | the scale itself | **Yes** — the one system in this table where space *is* line-height multiples | Optional (`clamp()` on `--s*` in their fluid recipe) | No | [Stack](https://every-layout.dev/layouts/stack/) |
| **SEEK Braid** | per theme | per theme | per theme | computed by Capsize from font metrics | grid tokens | **Yes** — sizes are cap-heights on a grid: `fontSizeToCapHeight(tokens.grid, …)` and `precomputeValues({ fontMetrics })` | No | **Yes — Capsize** (the design system Capsize was built for) | [`makeVanillaTheme.ts`](https://github.com/seek-oss/braid-design-system/blob/master/packages/braid-design-system/src/lib/themes/makeVanillaTheme.ts) |

Three findings from that table:

1. **Almost nobody ships a vertical rhythm.** Of 18 systems, only Every Layout and Braid derive spacing from line-height. Everyone else runs a 4/8px spacing scale and a *separate* type scale whose line-heights happen to be 4px multiples (Material, Fluent, Polaris, Tailwind) — or don't even do that (Bootstrap, MUI, Apple, USWDS, Primer). If you are told "our design system has vertical rhythm," check the tokens.
2. **Line-heights get *tighter* as sizes grow, everywhere.** Material 3 goes 1.5 at 16px → 1.12 at 57px; Apple 1.29 → 1.21; Fluent 1.43 → 1.35. A single unitless `line-height: 1.5` inherited into headings is the most common self-inflicted typography bug in this corner; Bell's and Comeau's resets both special-case headings to ~1.1 (see [resets](#vertical-rhythm-reset--three-things-the-phrase-can-mean)).
3. **Fluid type is still rare in shipped design systems.** GOV.UK explicitly steps at one breakpoint; Apple and Android scale with the *user's* setting, not the viewport; only Open Props ships `clamp()` sizes, and those omit the `rem` term. Fluid type lives in marketing/editorial sites and in the tools below, not in product design systems.

## Fluid typography

### The formula

Given two points — size *y₁* at viewport *x₁*, size *y₂* at viewport *x₂* — the preferred value of `clamp()` is the straight line through them:

```
slope     = (y₂ − y₁) / (x₂ − x₁)
intercept = y₁ − slope × x₁            ← in rem, so it scales with the user's font-size setting
font-size: clamp(y₁, intercept + (slope × 100)vw, y₂)
```

Worked example, 16px at 320px → 24px at 1240px (*reproduced* with `utopia-core` 1.6.0 `calculateClamp`):

```css
font-size: clamp(1rem, 0.8261rem + 0.8696vw, 1.5rem);
/* slope = 8/920 = 0.008696 → 0.8696vw; intercept = 16 − 0.008696 × 320 = 13.22px = 0.8261rem */
```

This is Mike Riethmuller's 2015 `calc()` formula — `calc([min] + ([max] − [min]) × ((100vw − [minvw]) / ([maxvw] − [minvw])))` — with `clamp()` doing the min/max that used to need two media queries. Tim Brown named the same construction a [*CSS lock*](https://blog.typekit.com/2016/08/17/flexible-typography-with-css-locks/) in 2016: *"a way of dynamically calculating any value between two extremes, relative to another set of extreme values — independent from media query breakpoints"*. Adrian Bece's 2022 [Smashing article](https://www.smashingmagazine.com/2022/01/modern-fluid-typography-css-clamp/) gives the same two equations in closed form (`v = 100·(y₂−y₁)/(x₂−x₁)`, `r = (x₁y₂ − x₂y₁)/(x₁ − x₂)`).

Why the `rem` in the middle matters: `vw` never changes when the user raises their browser font size or zooms; `rem` does. A preferred value of pure `vw` is what fails WCAG (next section). Open Props' `--font-size-fluid-*` tokens use pure `vw` in the middle term and rely on the `rem` bounds alone.

### Fluid *scales* — Utopia

[Utopia](https://utopia.fyi) (James Gilyead + Trys Mudford, 2020) is the current reference. Instead of one clamp per size, it interpolates between **two modular scales** — e.g. ratio 1.2 at 320px and 1.25 at 1240px — so headings grow faster than body copy as the viewport widens. *Reproduced* with `calculateTypeScale` (base 16→20px, ratios 1.2→1.25, steps −1..3):

```css
--step--1: clamp(0.8333rem, 0.7754rem + 0.2899vw, 1rem);
--step-0:  clamp(1rem,      0.913rem  + 0.4348vw, 1.25rem);
--step-1:  clamp(1.2rem,    1.0739rem + 0.6304vw, 1.5625rem);
--step-2:  clamp(1.44rem,   1.2615rem + 0.8924vw, 1.9531rem);
--step-3:  clamp(1.728rem,  1.4799rem + 1.2407vw, 2.4414rem);
```

Utopia's other calculators produce a **fluid space scale** (the same interpolation on spacing tokens, plus "space pairs" like `--space-s-l` that go from S at the small viewport to L at the large one), a fluid grid, and a single-clamp calculator. `utopia-core` (TS) and `utopia-core-scss` expose the maths for build pipelines; `relativeTo: 'container'` swaps `vw` for `cqi` so a card can scale its own type by its own width. The [`fluid-tailwind`](#fluid-tailwind) plugin brings the same idea to Tailwind utilities (`~text-base/4xl`), and Bootstrap's [RFS](#rfs) predates all of it with a Sass mixin that rescales any length.

### Accessibility of fluid type

> [!WARNING]
> **Pure viewport-unit type fails WCAG 1.4.4.** SC 1.4.4 Resize Text (AA): *"Except for captions and images of text, text can be resized without assistive technology up to 200 percent without loss of content or functionality."* Failure technique [F94](https://www.w3.org/WAI/WCAG22/Techniques/failures/F94) is literally *"Failure of Success Criterion 1.4.4 due to incorrect use of viewport units to resize text."* Zooming shrinks the CSS viewport, which shrinks `vw`, which shrinks the text you just tried to enlarge. Adrian Roselli's [test](https://adrianroselli.com/2019/12/responsive-type-and-zoom.html) shows `vw` text at 200% zoom that grew 6.25%.

Roselli's rules, adopted by every serious tool since: keep a `rem` term in the preferred value; **if you cap with `clamp()`, make the max at least 2× the min**; and test by zooming across browsers and viewports. Two tools automate the check:

- **`utopia-core` `checkWCAG()`** returns the viewport range in which 500% zoom text would be *smaller than* 2× the 100% text. *Reproduced:* 16→24px over 320–1240px returns `[]` (passes); 16→48px over the same range returns `[1010, 2060]` — i.e. between 1010px and 2060px viewports, a user zooming to 500% cannot reach 200% text. The comment in the source credits *"Maxwell Barvian, creator of fluid.style for this calculation"*.
- **`fluid-tailwind`** ships the same maths as `checkSC144` (default `true`): *"By default, the plugin will not generate fluid type that would fail WCAG Success Criterion 1.4.4"* — a failing pair emits an empty rule with a comment, error code `'fails-sc-144'` in [`errors.ts`](https://github.com/barvian/fluid-tailwind/blob/main/packages/fluid-tailwind/src/util/errors.ts).

### Fluid-type gotchas

- **Ultrawide.** Without the max, a 2vw heading is 77px on a 3840px monitor. Always clamp.
- **Line-height stays unitless.** `line-height: 1.2` follows a fluid size for free; `line-height: 2rlh` does not (it is root-relative), and `calc()` locks on line-height have the font-size-vs-root mismatch Brown's own commenters caught.
- **Pair fluid type with fluid space** or the rhythm drifts as type grows and margins don't — Utopia's space calculator exists for this reason.
- **Component-level fluidity:** `cqi` (Chrome 105 / Firefox 110 / Safari 16) gives a card its own scale; `utopia-core`'s `relativeTo: 'container'` emits it.
- **Snapping fluid values to a grid:** `round(nearest, <fluid>, 0.25rem)` (Chrome 125 / Firefox 118 / Safari 15.4) keeps a fluid line-height on the 4px grid.
- **Print:** `100vw` is the paper width; give print styles fixed sizes.

## Trimming the leading — Capsize and `text-box-trim`

### The problem, in one paragraph

Designers measure from cap height to baseline. Browsers measure content-area edge to content-area edge, plus half-leading. So a button with `padding: 12px` renders with visibly *more* than 12px above the text (half-leading + the gap between ascender and cap line) and a different amount below (half-leading + descender), and two adjacent labels in different fonts don't share a baseline. Ethan Wang's 2020 Microsoft Design post [*Leading-Trim: The Future of Digital Typesetting*](https://medium.com/microsoft-design/leading-trim-the-future-of-digital-typesetting-d082d84b202) put it to the CSSWG; Microsoft sponsored the spec work, it landed in [CSS Inline Layout Level 3](https://www.w3.org/TR/css-inline-3/) as `leading-trim` / `text-edge`, and was renamed `text-box-trim` / `text-box-edge` (shorthand `text-box`) before shipping.

### `text-box-trim` — the platform answer

```css
h1, .button-label {
  text-box: trim-both cap alphabetic;   /* top edge = cap line, bottom edge = baseline */
}
h2 {
  text-box: trim-start cap;             /* only the top, e.g. to align with an image's top edge */
}
```

- **Longhands:** `text-box-trim: none | trim-start | trim-end | trim-both`; `text-box-edge: <over> <under>` with over ∈ `text | cap | ex | ideographic | ideographic-ink` and under ∈ `text | alphabetic | ideographic | ideographic-ink`. Applies to *"block containers and inline boxes"* (MDN).
- **Shipping:** Safari **18.2** (2024-12-09, *"the first browser to ship Text Box"* — [WebKit blog](https://webkit.org/blog/16301/webkit-features-in-safari-18-2/)), Chrome **133** ([Chrome blog, 2025-01-14](https://developer.chrome.com/blog/css-text-box-trim)), Edge 132, Firefox **154**. caniuse: 85.3% global on the snapshot date; MDN marks it **Baseline 2026 — newly available since August 2026**. Not yet *widely available* (that needs 30 months), so ship it as a progressive enhancement — untrimmed text is the graceful fallback.
- **What it fixes, per Chrome's post:** half-leading control (*"names for each half: over and under. Plus, the ability to trim it off"*), symmetric button padding, icon/image alignment (*"With `text-box`, the image can perfectly align with the text content"*), and rhythm where *"something like `gap` can be used between contents."*
- **What it doesn't do:** it trims the *first and last* line only; it does not snap interior baselines to a grid, does not change `line-height`, and inline-element support is still partial (Firefox 154, Safari 26.5 partial, Chrome none per BCD).

### Capsize

**[seek-oss/capsize](https://github.com/seek-oss/capsize)** — *"Flipping how we define typography in CSS."* 1,717★ · MIT · created 2020-06-03 · pushed 2026-08-12 · 9 open issues · latest release `@capsizecss/metrics@4.2.0` (2026-07-20) · npm **106,156/wk** (`@capsizecss/core`), **248,896/wk** (`@capsizecss/metrics`).

Capsize is the userland leading-trim: given a font's metrics it emits CSS that (a) sizes text by **cap height** instead of font-size if you want, and (b) trims the space above the cap line and below the baseline using `::before`/`::after` pseudo-elements with negative margins. *Reproduced*, Inter at 16px with 24px leading:

```css
.capsized {
  font-size: 16px;
  line-height: 24px;
}
.capsized::before { content: ""; margin-bottom: -0.3862em; display: table; }
.capsized::after  { content: ""; margin-top:  -0.3862em; display: table; }
```

And the "design-tool" form — 12px cap height with a 12px gap between lines — resolves to `font-size: 16.494px; line-height: 24px` with `−0.3638em` trims. Points worth knowing:

- **Two sizing models, two leading models.** `capHeight` *or* `fontSize`; `lineGap` (gap between baseline and next cap line) *or* `leading` (baseline-to-baseline). The README: *"This aligns the web with how typography is treated in design tools."*
- **Metrics come from three places:** `@capsizecss/metrics` (pre-computed for Google Fonts + system fonts, import by name), `@capsizecss/unpack` (read a font file at build time — needed for self-hosted or user-uploaded fonts), or the [website](https://seek-oss.github.io/capsize/).
- **`createFontStack`** is the CLS half — see [Metric-matched fallbacks](#metric-matched-fallback-fonts).
- **Integrations:** `@capsizecss/react`, `@capsizecss/vanilla-extract`; the README warns *"It is not recommended to apply further layout-related styles to the same element … Instead consider using a nested element."*
- **Lineage:** Michael Taranto (178 of the repo's commits) wrote **Basekick** in 2015 (158★, Less/Sass, a `transform`-based baseline shift) to solve the same problem for SEEK; Capsize is its metrics-driven successor and the engine under [Braid](https://github.com/seek-oss/braid-design-system) (`precomputeValues`, `fontSizeToCapHeight`).
- **Relationship to `text-box-trim`:** the Capsize README does not mention the CSS property at all as of the snapshot (grep of the README: zero hits). It is not marketed as a polyfill, and it is not one — the pseudo-element trick trims by *metrics you supply*, the property trims by *metrics the browser reads*. In practice they converge on the same pixels for the same font.

### Capsize vs `text-box-trim`

| | Capsize | `text-box-trim` |
| --- | --- | --- |
| Where it runs | Build time / CSS-in-JS | Browser |
| Needs font metrics? | Yes — shipped package, `unpack`, or the website | No — browser reads the font |
| Works for fonts you don't control (user uploads, CMS themes) | Only via runtime `unpack` | Yes |
| Browser support | Everything (it's margins) | Chrome 133 / Safari 18.2 / Firefox 154 |
| Size by cap height | Yes (`capHeight` option) | No — combine with the `cap` unit yourself |
| Metric-matched fallback fonts | Yes (`createFontStack`) | No — separate concern |
| Trims interior lines / snaps baselines | No | No |
| Fallback-font mismatch | Trim is computed for the *primary* font; a fallback with different metrics is mis-trimmed until the web font loads | Browser trims whatever font is rendering |
| Extra DOM/CSS | Two pseudo-elements per trimmed element | One declaration |

**Recommendation on the snapshot date:** ship `text-box: trim-both cap alphabetic` behind `@supports (text-box-trim: trim-both)`; keep Capsize where you already have it (Braid-style design systems, vanilla-extract, anything that needs `capHeight` sizing or `createFontStack`). Do not add Capsize to a new project *only* for trimming.

## Metric-matched fallback fonts

The same font metrics solve a second problem: layout shift when the web font replaces the fallback. Four `@font-face` descriptors — `size-adjust`, `ascent-override`, `descent-override`, `line-gap-override` — let you reshape a local font (Arial, Roboto, system-ui) so its glyph box matches the web font's, and the line count and line boxes don't change on swap. Support: `size-adjust` Chrome 92 / Firefox 92 / Safari 17; the three overrides Chrome 87 / Firefox 89 / Safari 17 ([web.dev](https://web.dev/articles/css-size-adjust)).

*Reproduced* with Capsize's `createFontStack([inter, arial])`:

```css
@font-face {
  font-family: "Inter Fallback";
  src: local('Arial'), local('ArialMT');
  ascent-override: 90.4365%;
  descent-override: 22.518%;
  line-gap-override: 0%;
  size-adjust: 107.1194%;
}
```

| Tool | Shape | ★ · license · pushed | npm/wk | Notes |
| --- | --- | --- | --- | --- |
| **[unjs/fontaine](https://github.com/unjs/fontaine)** | Vite/Nuxt/Astro/webpack transform — scans `@font-face`, emits fallback faces automatically | 1,986 · MIT · 2026-09-07 | **584,600** | Zero runtime. README's playground numbers: CLS `0.24 → 0.054`. Built into Nuxt's font module. |
| **`next/font`** (Vercel) | Framework built-in; computes `size-adjust` + overrides for Google and local fonts at build time | (part of `vercel/next.js`) | — | Uses `@capsizecss/metrics` under the hood (dependency confirmed in the Next.js repo). |
| **Capsize `createFontStack`** | Library call; returns `fontFamily` + `fontFaces` | see [Capsize](#capsize) | 106,156 | Also templated into CSS-in-JS objects. |
| **[pixel-point/fontpie](https://github.com/pixel-point/fontpie)** | CLI: `npx fontpie ./font.woff2` → CSS snippet | 413 · MIT · 2023-02-02 | 112 | One-shot; dormant. |
| **Fallback Font Generator** ([screenspan.net/fallback](https://screenspan.net/fallback)) | Web UI with visual overlay tuning | — | — | Manual, good for one-off marketing pages. |
| **`font-size-adjust`** | CSS property, not a tool: `font-size-adjust: ex-height from-font` makes any fallback match the primary's x-height | Chrome 127 / Firefox 92 (two-value) / Safari 17 | — | Newly Baseline 2024-07-25. Fixes *x-height* only, not ascent/descent, so it complements rather than replaces the overrides. |

## "Vertical rhythm reset" — three things the phrase can mean

The phrase is not a standard term. Three readings, ranked by how likely a search will land on each:

1. **A specific package — [`jhildenbiddle/vertical-rhythm-reset`](https://github.com/jhildenbiddle/vertical-rhythm-reset).** 79★ · MIT · created 2016-03-16 · last commit 2024-02-07 · 1 open issue · npm 23/wk. *"A Sass/SCSS library for responsive vertical rhythm grids, modular scale typography, and CSS normalization."* It bundles three things that were separate in 2016: a Normalize.css-derived reset, a modular-scale function (credits modularscale.com), and per-breakpoint rhythm settings with utility classes. Dart Sass only. It is the most literal match for the phrase, and it is a **dormant 2016-era Sass library** — everything it does is reproducible with `rlh`, `.flow`, and a modern reset today. Score 🟨 (see [leaderboard](#rhythm--scale-libraries)); not recommended for new work.
2. **The typography rules inside modern CSS resets.** Every widely-used reset now *establishes* a rhythm baseline, which is what most people asking for a "reset" actually want:
   - Andy Bell, [*A (more) modern CSS reset*](https://piccalil.li/blog/a-more-modern-css-reset/) (2023-09-18): `body { line-height: 1.5 }`, `h1, h2, h3, h4, button, input, label { line-height: 1.1 }`, `h1–h4 { text-wrap: balance }`.
   - Josh Comeau, [*My Custom CSS Reset*](https://www.joshwcomeau.com/css/custom-css-reset/) (2021-11-23, updated 2026-06-03): `line-height: 1.5` (*"line-heights around 1.5 are friendlier, for body text"*) with an optional `line-height: calc(1em + 0.5rem)` — a line-height that is *proportional at body size but relatively tighter at heading sizes*, which is exactly the shape the [design-system table](#type-scales--ratios-and-what-real-design-systems-actually-ship) shows every system hand-tuning; plus `p { text-wrap: pretty }`, `h1–h6 { text-wrap: balance }`.
   - Tailwind Preflight sets `line-height: 1.5` on `html` and collapses heading margins to 0 so `prose` or your `.flow` rule owns spacing.
   The pattern common to all three: **body 1.5, headings ≈1.1, zero default margins, then one spacing mechanism.** That *is* a vertical-rhythm reset in 2026.
3. **The leading-trim family as a "reset" of half-leading.** Basekick (2015) → Capsize (2020) → `text-box-trim` (2024–26) reset the text box to cap-line/baseline so spacing measures what the designer measured. If the person saying "vertical rhythm reset" is a designer handing over Figma specs, this is the reading — see [Trimming the leading](#trimming-the-leading--capsize-and-text-box-trim).

## Accessibility floors and reading research

The numbers below are the *floors* a stylesheet should clear before any aesthetic decision:

| Rule | Value | Source |
| --- | --- | --- |
| Text resizes to 200% without loss | required, AA | [WCAG 2.2 SC 1.4.4](https://www.w3.org/WAI/WCAG22/Understanding/resize-text.html); F94 for `vw` type |
| User-applied text spacing must not break layout | line-height ≥ **1.5×** font size; paragraph spacing ≥ **2×**; letter-spacing ≥ **0.12em**; word-spacing ≥ **0.16em** — AA | [SC 1.4.12 Text Spacing](https://www.w3.org/WAI/WCAG22/Understanding/text-spacing.html) (values from the McLeish study via Dr. Dick) |
| Measure ≤ 80 characters (40 CJK); leading ≥ 1.5; paragraph spacing ≥ 1.5× leading; no justification; 200% without horizontal scroll | AAA | [SC 1.4.8 Visual Presentation](https://www.w3.org/WAI/WCAG22/Understanding/visual-presentation.html) |
| Measure for reading | 45–75 characters, 66 ideal (Bringhurst); 50–75 (Baymard, citing Ruder's 50–60 and WCAG's 80) | [webtypography.net 2.1.2](http://webtypography.net/2.1.2); [Baymard 2022](https://baymard.com/blog/line-length-readability) |
| Body line spacing | *"between 120% and 145% of the point size"* (Butterick, print-leaning); 1.5 (WCAG, Bell, Comeau) | [Practical Typography](https://practicaltypography.com/line-spacing.html) |
| Body size on screen | Butterick 15–25px; most systems in the table above sit at 14–17px | [Practical Typography](https://practicaltypography.com/point-size.html) |

Practical translation: `line-height: 1.5` on body copy is both the WCAG floor *and* the number every reset uses, so the "rhythm unit = 1.5 × base" convention from 2006 is now also the accessibility default. Tighter leading belongs on headings only. And SC 1.4.12 is the one that bites rhythm systems: a user stylesheet that forces `line-height: 1.5` and `margin-bottom: 2em` on paragraphs will break any layout that assumed fixed 24px line boxes — test with the [Text Spacing bookmarklet](https://www.html5accessibility.com/tests/tsbookmarklet.html).

## Native platforms — the same problem, different knobs

This article sits next to [`../application-types/mobile.md`](../application-types/mobile.md); the half-leading problem is not web-specific, and each platform exposes a different fix.

| Platform | Scale mechanism | Leading control | Trim / half-leading control | Source |
| --- | --- | --- | --- | --- |
| **iOS / iPadOS** | Dynamic Type: 11 text styles × 12 user size classes; the whole size/leading table is re-issued per class (Body 17/22 at Large, 14/19 at xSmall; Large Title 34/41 at Large, 36/43 at xLarge) | fixed "leading" per style | none exposed; `UIFont` metrics (`capHeight`, `ascender`) available for manual trims | [HIG Typography](https://developer.apple.com/design/human-interface-guidelines/typography) |
| **Android (Views)** | `sp` units scale with the user setting; **Android 14 made scaling non-linear** up to 200% — *"large text doesn't scale at the same rate as smaller text"* | `lineSpacingExtra` / `lineSpacingMultiplier` | `includeFontPadding="false"` removes the extra ascender/descender padding | [Android 14 features](https://developer.android.com/about/versions/14/features#non-linear-font-scaling) |
| **Jetpack Compose** | same `sp` | `lineHeight` + `LineHeightStyle.Alignment` (Top / Center / Bottom / Proportional) | **`LineHeightStyle.Trim`**: `None`, `Both`, `FirstLineTop`, `LastLineBottom` — the native equivalent of `text-box-trim`; only works with `includeFontPadding = false` | [Compose paragraph styling](https://developer.android.com/develop/ui/compose/text/style-paragraph) |
| **Flutter** | logical px; `textScaler` | `TextStyle.height` (multiplier) | **`TextLeadingDistribution.even`** (CSS half-leading) vs **`.proportional`** (split by the font's ascent/descent ratio); `TextHeightBehavior.applyHeightToFirstAscent` / `applyHeightToLastDescent` trim the first/last line | [`TextLeadingDistribution`](https://api.flutter.dev/flutter/dart-ui/TextLeadingDistribution.html) |
| **React Native** | `fontSize` in dp; `allowFontScaling` | `lineHeight` (*"the distance between the baselines of consecutive lines"*) | Android-only `includeFontPadding: false` + `textAlignVertical: 'center'` (*"With some fonts, this padding can make text look slightly misaligned when centered vertically"*); nothing on iOS | [Text style props](https://reactnative.dev/docs/text-style-props) |

Two things transfer: Android's non-linear scaling is the platform equivalent of "make the clamp max ≥ 2× the min" (keep hierarchy, stop headings exploding), and Compose's `Trim` shows the `text-box-trim` model — trim first-top and last-bottom only — is the industry consensus, not a web quirk.

## Browser support

Snapshot 2026-09-07, from caniuse.com / MDN / `mdn/browser-compat-data`. "Baseline" = the web-features availability label.

| Feature | Chrome/Edge | Firefox | Safari | Baseline | Note |
| --- | --- | --- | --- | --- | --- |
| `clamp()` / `min()` / `max()` | 79 | 75 | 13.1 | widely | 96.25% global |
| `lh` / `rlh` units | 109 / 111 | 120 | 16.4 | **widely available 2026-05-21** | 94.5% |
| `cap` unit | 118 | 97 | 17.2 | widely available 2026-06-11 | 92.8% |
| Container query units (`cqi`, `cqw`) | 105 | 110 | 16.0 | widely | 94.9% |
| `round()` | 125 | 118 | 15.4 | newly | 90.7% |
| `font-size-adjust` (incl. two-value) | 127 | 92 | 17 | newly 2024-07-25 | Firefox had one-value since 3 |
| `@font-face` `size-adjust` | 92 | 92 | 17 | widely | |
| `ascent-override` / `descent-override` / `line-gap-override` | 87 | 89 | 17 | widely | |
| `text-wrap: balance` | 114 (full 130) | 121 | 17.5 | newly 2024-05-13 | ≤6 lines Chromium, ≤10 Firefox |
| `text-wrap: pretty` | 117 | **not shipped** ([bug 1960910](https://bugzilla.mozilla.org/show_bug.cgi?id=1960910)) | 26.0 | not Baseline | |
| **`text-box-trim` / `text-box-edge` / `text-box`** | **133** / 132 | **154** | **18.2** | **newly, August 2026** | 85.3%; inline boxes partial |
| `hanging-punctuation` | not shipped | not shipped | 10 (full 26.5) | not Baseline | 15.7% |
| `margin-trim` | not shipped | not shipped | 16.4 (experimental) | not Baseline | |
| `line-height-step` | flag only (M60+) | never | never | none | removed from BCD; don't use |

## Aspects we score on

This article uses the [shared scoring rubric](../practices/formalization.md#scoring-dimensions-per-repo) with thresholds re-based for a niche where the *biggest* library has 6.7k★ and most have under 500:

- **Stars** — 🟩🟩 ≥1k, 🟩 ≥200, 🟨 ≥50, 🟥 <50. Hosted calculators with no repo are ⬜.
- **License** — 🟩 permissive (MIT/BSD/Apache/ISC), 🟥 GPL or none declared. "None in the API but MIT in the README" (sassline) scores 🟩 with a note.
- **Maintenance** — last commit on the default branch vs. snapshot: 🟩🟩 <6 months, 🟩 <2 years, 🟨 2–5 years, 🟥 >5 years or archived/deprecated by the author. `pushed_at` alone lies for repos with bot branches (modularscale-sass, basehold.it) — the table uses the default-branch date.
- **README maturity** — 🟩🟩 guide + API + examples, 🟩 tagline + usage, 🟥 minimal.
- **Binding** — Sass / PostCSS / JS / CSS-in-JS / Tailwind plugin / build transform / CLI / hosted. Not scored; load-bearing for whether it fits your stack.
- **Adoption** — npm weekly downloads where a package exists. Reported, not scored, because a hosted calculator's adoption is invisible.

## Tools — rhythm & scale libraries

The 2006–2019 generation. Every one of these is a wrapper around "base line-height × n" and a modular-scale function, in whatever preprocessor was current. Most are dormant because the browser absorbed the job (`lh`, `rlh`, `calc()`, custom properties).

| Tool | Binding | ★ | License | Last commit | README | npm/wk | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [modularscale/modularscale-sass](https://github.com/modularscale/modularscale-sass) | Sass | 1,959 · 🟩🟩 | MIT · 🟩 | 2019-09-22 · 🟨 | 🟩🟩 | 6,857 | The reference `ms(n)` function; multi-strand scales. `pushed_at` 2025-11-29 is a non-default branch. |
| [KyleAMathews/typography.js](https://github.com/KyleAMathews/typography.js) | JS (React/Gatsby) | 3,855 · 🟩🟩 | MIT · 🟩 | 2023-07-08 · 🟩 | 🟩🟩 | 7,445 | `baseFontSize` + `baseLineHeight` → rhythm CSS for every element; 30 themes. Gatsby's original type layer; 96 open issues. |
| [jakegiltsoff/sassline](https://github.com/jakegiltsoff/sassline) | Sass | 1,373 · 🟩🟩 | MIT (README) · 🟩 | 2023-12-30 · 🟩 | 🟩🟩 | 25 | Root font-size = ½ line-height so the grid has half-line steps. **Author-deprecated:** *"this code is p old now and is no longer actively maintained … you can do this all in pure CSS now."* |
| [jamonserrano/plumber](https://github.com/jamonserrano/plumber) (`plumber-sass`) | Sass | 254 · 🟩 | MIT · 🟩 | 2017-01-26 · 🟥 | 🟩🟩 | 112 | **The one Sass tool that snaps the glyph baseline**, via a per-font "baseline ratio" (e.g. Roboto 0.158203) fed into padding. PostCSS port `postcss-plumber` (11★). |
| [csswizardry/typecsset](https://github.com/csswizardry/typecsset) | Sass | 704 · 🟩 | NOASSERTION · 🟥 | 2014-01-07 · 🟥 | 🟩 | — | Roberts' `$typecsset-magic-number` lib; a three-day project that codified the 2012 article. |
| [2metres/typographic](https://github.com/2metres/typographic) | SCSS/Stylus | 680 · 🟩 | MIT · 🟩 | 2016-10-09 · 🟥 | 🟩 | — | Rhythm + modular scale + font stacks. |
| [ianrose/typesettings](https://github.com/ianrose/typesettings) | Sass/Stylus | 385 · 🟩 | MIT · 🟩 | 2020-12-06 · 🟨 | 🟩 | — | Em-based; responsive ratio headlines. |
| [kyleshevlin/shevyjs](https://github.com/kyleshevlin/shevyjs) | CSS-in-JS | 319 · 🟩 | MIT · 🟩 | 2021-08-30 · 🟨 | 🟩🟩 | — | `createShevy({ baseFontSize, baseLineHeight, fontScale, proximity })` for emotion/styled-components. Sass sibling `shevy` (180★, 2017). |
| [hiulit/Sassy-Gridlover](https://github.com/hiulit/Sassy-Gridlover) | Sass | 221 · 🟩 | MIT · 🟩 | 2019-04-01 · 🟨 | 🟩 | — | Mixin port of the Gridlover app. |
| [michaeltaranto/basekick](https://github.com/michaeltaranto/basekick) | Less/Sass/JS | 158 · 🟨 | none · 🟥 | 2019-10-14 · 🟨 | 🟩 | — | The Capsize precursor: `transform` shifts text onto the baseline given a descender ratio. Historic interest only. |
| [jonschlinkert/vertical-rhythm](https://github.com/jonschlinkert/vertical-rhythm) | Less/Stylus/SCSS | 89 · 🟨 | MIT · 🟩 | 2013-09-11 · 🟥 | 🟩 | — | Early Compass-rhythm port. |
| [oleq/syncope](https://github.com/oleq/syncope) | web app | 82 · 🟨 | **GPL-3.0 · 🟥** | 2017-04-30 · 🟥 | 🟩 | — | Rhythm tool for designers; GPL. |
| [ceteio/styled-components-rhythm](https://github.com/ceteio/styled-components-rhythm) | CSS-in-JS | 81 · 🟨 | none · 🟥 | 2021-11-10 · 🟨 | 🟩 | — | Rhythm *and* font-baseline offsets for styled-components. |
| [jhildenbiddle/vertical-rhythm-reset](https://github.com/jhildenbiddle/vertical-rhythm-reset) | Sass | 79 · 🟨 | MIT · 🟩 | 2024-02-07 · 🟩 | 🟩 (docs site) | 23 | Reset + modular scale + per-breakpoint rhythm. See [the phrase](#vertical-rhythm-reset--three-things-the-phrase-can-mean). |
| [markgoodyear/postcss-vertical-rhythm](https://github.com/markgoodyear/postcss-vertical-rhythm) | PostCSS | 73 · 🟨 | MIT · 🟩 | 2015-11-18 · 🟥 | 🟩 | — | Adds a `vr` unit. |
| [Pushplaybang/knife](https://github.com/Pushplaybang/knife) | Sass | 67 · 🟨 | MIT · 🟩 | 2018-03-27 · 🟥 | 🟩 | — | Rhythm + scale + rem helpers. |
| [pyrsmk/vertical-rhythmic](https://github.com/pyrsmk/vertical-rhythmic) | Sass | 62 · 🟨 | MIT · 🟩 | 2019-05-27 · 🟨 | 🟩 | — | Fluid type *and* rhythm — an early attempt to combine both. |
| [jeromev/baselinegrid.scss](https://github.com/jeromev/baselinegrid.scss) | SCSS | 28 · 🟥 | ⬜ | 2026-08-04 · 🟩🟩 | ⬜ | — | The only baseline-grid Sass repo with a 2026 commit; too small to score on README (not fetched). |
| [jmlweb/storybook-vrhythm](https://github.com/jmlweb/storybook-vrhythm) | Storybook decorator | 22 · 🟥 | MIT · 🟩 | 2026-05-29 · 🟩🟩 | ⬜ | — | Baseline overlay inside Storybook — a debugging tool, listed here because it is one of two live repos in the search. |
| [juliekoubova/tailwind-vertical-rhythm](https://github.com/juliekoubova/tailwind-vertical-rhythm) | Tailwind plugin | 17 · 🟥 | none · 🟥 | 2023-01-06 · 🟨 | ⬜ | — | *"Beautifully aligned type with tailwind.css."* |
| Compass `vertical_rhythm` ([Compass/compass](https://github.com/Compass/compass)) | Ruby Sass | 6,657 · 🟩🟩 | NOASSERTION | deprecated · 🟥 | historic | — | *"Compass is no longer actively maintained."* Listed because its vocabulary (`establish-baseline`, `adjust-font-size-to`, `rhythm()`, `leader/trailer`, `rhythm-borders`) is what every tool above copied. |
| [argyleink/open-props](https://github.com/argyleink/open-props) | CSS custom props | 5,510 · 🟩🟩 | MIT · 🟩 | 2026-08-11 · 🟩🟩 | 🟩🟩 | — | Not a rhythm library, but the live, modern token set: `--font-size-0..8`, `--font-lineheight-00..5`, `--size-content-1..3` (20/45/60ch measures), fluid sizes. |

## Tools — fluid type

| Tool | Binding | ★ | License | Last commit | README | npm/wk | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **[Utopia](https://utopia.fyi)** + [trys/utopia-core](https://github.com/trys/utopia-core) | hosted calculators + TS | 138 · 🟨 | none in API · ⬜ | 2024-09-19 · 🟩 | 🟩 | 5,590 | The reference fluid-scale tool. `calculateClamp`, `calculateTypeScale`, `calculateSpaceScale`, `checkWCAG`; `relativeTo: 'container'` for `cqi`. SCSS port [`utopia-core-scss`](https://github.com/trys/utopia-core-scss) (97★, pushed 2025-12-19). The site is the product; the repo's license field is empty — check before vendoring. |
| **[barvian/fluid-tailwind](https://github.com/barvian/fluid-tailwind)** <a id="fluid-tailwind"></a> | Tailwind plugin | 1,780 · 🟩🟩 | MIT · 🟩 | 2025-03-17 · 🟩 | 🟩🟩 (docs at fluid.tw) | 16,796 | `~text-base/4xl`, `~md/lg:~px-4/8`, container variant `~@md/lg:`. Ships the WCAG 1.4.4 check (`checkSC144`). Created 2024-02-12, so the 18-month gap since the last commit is worth watching; Tailwind v4 compatibility not stated in the docs. |
| **[twbs/rfs](https://github.com/twbs/rfs)** <a id="rfs"></a> | Sass/Less/Stylus/PostCSS | 3,366 · 🟩🟩 | MIT · 🟩 | 2026-09-03 · 🟩🟩 | 🟩🟩 | 16,139 | Bootstrap's *Responsive Font Sizes* engine (2017); `@include font-size(4rem)` emits a `calc()` that rescales below a breakpoint. Now rescales any length. The only fluid tool here with a 2026 commit and a large consumer (Bootstrap). |
| [davidhellmann/tailwindcss-fluid-type](https://github.com/davidhellmann/tailwindcss-fluid-type) | Tailwind plugin | 368 · 🟩 | MIT · 🟩 | 2024-11-29 · 🟩 | 🟩🟩 | 4,114 | Replaces Tailwind's `text-*` scale with clamp()s; predates `fluid-tailwind`. |
| [AleksandrHovhannisyan/fluid-type-scale-calculator](https://github.com/AleksandrHovhannisyan/fluid-type-scale-calculator) ([fluid-type-scale.com](https://www.fluid-type-scale.com/)) | hosted (Svelte) | 320 · 🟩 | MIT · 🟩 | 2025-10-15 · 🟩 | 🟩 | — | Utopia-like single-ratio calculator with copyable custom properties. |
| [codeAdrian/modern-fluid-typography-editor](https://github.com/codeAdrian/modern-fluid-typography-editor) ([modern-fluid-typography.vercel.app](https://modern-fluid-typography.vercel.app/)) | hosted | 353 · 🟩 | MIT · 🟩 | 2021-12-09 · 🟨 | 🟩 | — | Adrian Bece's visual clamp editor from the Smashing article; the graph of size-vs-viewport is the best teaching aid in the category. |
| [seaneking/postcss-responsive-type](https://github.com/seaneking/postcss-responsive-type) | PostCSS | 368 · 🟩 | none · 🟥 | 2023-07-11 · 🟩 | 🟩 | — | `font-size: responsive` + `font-range` — the 2015 pre-`clamp()` shape. |
| [boriskirov/fluiditype](https://github.com/boriskirov/fluiditype) | CSS | 233 · 🟩 | ⬜ | 2021-09-30 · 🟨 | ⬜ | — | Drop-in reading-experience CSS. |
| [jakobsen/fluid-typography](https://github.com/jakobsen/fluid-typography) | hosted | 92 · 🟨 | ⬜ | 2024-11-11 · 🟩 | ⬜ | — | Single-heading clamp finder. |
| [Chris Burnell — clamp() calculator](https://chrisburnell.com/clamp-calculator/) | hosted | ⬜ | — | — | — | — | Minimal single-value calculator. |
| [davatron5000/FitText.js](https://github.com/davatron5000/FitText.js) | jQuery | 6,712 · 🟩🟩 | none · 🟥 | 2020-12-02 · 🟨 | 🟩 | — | 2011: JS that resizes a headline to its container's width. Historic — the problem it solved is now `clamp()` + `cqi`. |

## Tools — leading trim, font metrics, fallbacks

| Tool | Binding | ★ | License | Last commit | README | npm/wk | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **[seek-oss/capsize](https://github.com/seek-oss/capsize)** | JS / CSS-in-JS / vanilla-extract | 1,717 · 🟩🟩 | MIT · 🟩 | 2026-08-12 · 🟩🟩 | 🟩🟩 | 106,156 core · 248,896 metrics | See [Capsize](#capsize). The metrics package outpulls core 2.3:1 because `next/font` depends on it. |
| **`text-box-trim`** (platform) | CSS | — | — | — | — | — | See [above](#text-box-trim--the-platform-answer). [jantimon/text-box-trim-examples](https://github.com/jantimon/text-box-trim-examples) (402★, no license, pushed 2026-01-08) is the playground. |
| **[unjs/fontaine](https://github.com/unjs/fontaine)** | build transform | 1,986 · 🟩🟩 | MIT · 🟩 | 2026-09-07 · 🟩🟩 | 🟩🟩 | 584,600 | See [fallbacks](#metric-matched-fallback-fonts). |
| [pixel-point/fontpie](https://github.com/pixel-point/fontpie) | CLI | 413 · 🟩 | MIT · 🟩 | 2023-02-02 · 🟨 | 🟩 | 112 | One-shot fallback CSS generator. |
| [opentypejs/opentype.js](https://github.com/opentypejs/opentype.js) | JS font parser | 5,011 · 🟩🟩 | MIT · 🟩 | 2026-08-08 · 🟩🟩 | 🟩🟩 | — | Read `hhea`/`OS/2` tables yourself when `@capsizecss/unpack` doesn't fit. |
| [foliojs/fontkit](https://github.com/foliojs/fontkit) | JS font engine | 1,670 · 🟩🟩 | none in API · ⬜ | 2024-08-14 · 🟩 | 🟩🟩 | — | What `@capsizecss/unpack` is built on; 165 open issues. |

## Tools — calculators and overlays

Hosted, no repo to score; listed for discoverability.

| Tool | Author | Does | Status |
| --- | --- | --- | --- |
| [typescale.com](https://typescale.com/) | Jeremy Church | Base × named ratio → preview + CSS. `type-scale.com` 301s here. | Live; freemium ("Go Pro", golden ratio behind Pro; price ⬜). |
| [modularscale.com](https://www.modularscale.com/) | Scott Kellum + Tim Brown | 17 ratios, multi-base scales; FAQ warns *"too many strands can dilute a scale."* | Live, free; site repo last pushed 2017. |
| [Gridlover](https://gridlover.net/) | Tuomas Jokela + Sakari Maaranen | Sliders → full rhythm CSS for html/body/h1–h6/p; cites Rutter 2006 + Brown 2011. | Live, *"always free"*. |
| [Golden Ratio Typography calculator](https://grtcalculator.com/) | Chris Pearson | Line-height from font size and measure via φ; later x-height corrected. | Live; article 2011, updated 2022. |
| [Basehold.it](https://basehold.it/) ([daneden/basehold.it](https://github.com/daneden/basehold.it), 551★) | Daniel Eden | `<link rel="stylesheet" href="https://basehold.it/24">` draws a 24px baseline overlay. | Service live; code last touched 2022-06-15 (2026 commits are dependabot). |
| Baseliner ([jpedroribeiro/baseliner](https://github.com/jpedroribeiro/baseliner), 47★, MIT, pushed 2026-09-06) | J. Pedro Ribeiro | Chrome extension overlay. | Live. |
| [jkeyes/baseline](https://github.com/jkeyes/baseline) (289★) | John Keyes | The original "Baseliner" bookmarklet + examples. | 2015. |
| `repeating-linear-gradient(… 1lh)` | — | Zero-dependency overlay (see the [snippet](#the-minimum-viable-rhythm-2026-edition)). | Works wherever `lh` does. |
| Figma: [Typescales](https://www.figma.com/community/plugin/739825414752646970/typescales) (111,846 installs per a mirror), [Typescale](https://www.figma.com/community/plugin/967802396210455992/typescale), [Baseline (Beta)](https://www.figma.com/community/plugin/1588106368687418194/baseline-beta), [Vertical Rhythm Scaler](https://www.figma.com/community/plugin/1603795188427131014/vertical-rhythm-scaler) | various | Scale generation and baseline overlays in design files. Figma's own [guidance](https://www.figma.com/best-practices/typography-systems-in-figma/): *"making line height values multiples of 4 makes it easier to establish vertical rhythm."* | Install counts ⬜ (figma.com 403s automated fetches). |
| Chrome DevTools | — | **No baseline/line-height overlay exists** — only the Grid/Flex overlays. | — |

## When to use what

| Job | Pick | Why not the others |
| --- | --- | --- |
| Marketing / editorial site, headings must feel right on phone and 4K | **Utopia** (type + space calculators) or `utopia-core` in the build; `text-wrap: balance` on headings | Fixed breakpoints produce jumps; hand-written clamps drift from each other. Run `checkWCAG`. |
| Tailwind project that wants fluid utilities | **`fluid-tailwind`** — but pin the version and check v4 compatibility first | `tailwindcss-fluid-type` only covers `text-*`; Tailwind core has no fluid scale. |
| Bootstrap project | **RFS** — it's already in `node_modules` | Anything else double-implements what `$enable-rfs` gives you. |
| Product UI with a design system (buttons, labels, cards) | 4/8px spacing scale + hand-tuned line-heights on the 4px grid (Material/Fluent/Polaris pattern) + **`text-box: trim-both cap alphabetic`** behind `@supports` | A page-level rhythm fights component padding; trimming makes 12px mean 12px. |
| Design handoff in cap-height units, or CSS-in-JS/vanilla-extract | **Capsize** (`capHeight` + `lineGap`) | `text-box-trim` has no cap-height *sizing*; Capsize is how Braid does it. |
| Long-form reading (docs, blog, book) | `.flow` + `1rlh` spacing, `line-height: 1.5`, `max-width: 65–70ch`, `text-wrap: pretty` on `p`; `@tailwindcss/typography` if you're on Tailwind (it encodes the same ratios, 18.8M npm downloads/wk) | This is the one place "rhythm lite" pays off; nothing needs a library. |
| Print-faithful baseline grid (magazine-style web layout, a typography portfolio) | **`plumber`** (Sass, dormant but correct) or per-font `text-box-trim` + `1rlh` everything + `round(up, …, 1rlh)` on images | Nothing else snaps glyph baselines; know you're buying maintenance. |
| Web font causes layout shift | **`fontaine`** (Vite/Nuxt/Astro), **`next/font`** (Next.js), or `createFontStack` | Hand-tuning `size-adjust` is a one-off job for `fontpie` or the screenspan generator, not a process. |
| "Our headings look loose" | Reset headings to `line-height: 1.1–1.2` (Bell/Comeau resets) or `calc(1em + 0.5rem)` | Not a rhythm problem; it's the inherited 1.5. |
| CJK / mixed-script UI | Explicit numeric `line-height` per script (Spectrum ships 1.5/1.7 CJK multipliers); test `line-height: normal` on Linux/Android | `normal` is font-driven and Noto CJK is ~45% taller than Windows CJK. |
| Check the rhythm you think you have | `html.debug-grid` gradient, Basehold.it, or `storybook-vrhythm` | DevTools has no overlay. |

> [!IMPORTANT]
> **There is no CSS baseline grid.** `line-height-step` and `block-step` are specs without implementations; `text-box-trim` trims only the first and last line. Everything that promises glyph baselines on a grid is doing per-font arithmetic (Plumber, Capsize, Basekick) that breaks the moment a fallback font renders. Decide up front whether you want *box rhythm* (cheap, robust) or *baseline alignment* (expensive, brittle) — the tools are different.

## Leaderboards

Ranked within category. Score = sum over Stars / License / Maintenance / README (🟥 −1, 🟨 0, 🟩 +1, 🟩🟩 +2; max 8). Hosted-only tools are unscored.

### Rhythm & scale libraries

1. **🥇 [open-props](https://github.com/argyleink/open-props)** — 8 (🟩🟩/🟩/🟩🟩/🟩🟩). The only live, permissive, documented token set; not a "rhythm library" but where a 2026 project should start.
2. **🥈 [typography.js](https://github.com/KyleAMathews/typography.js)** — 6 (🟩🟩/🟩/🟩/🟩🟩). Still the most complete rhythm-CSS generator; React-era API.
3. **🥈 [sassline](https://github.com/jakegiltsoff/sassline)** — 6 (🟩🟩/🟩/🟩/🟩🟩), but author-deprecated — read it for the half-line idea, don't install it.
4. **[modularscale-sass](https://github.com/modularscale/modularscale-sass)** — 5 (🟩🟩/🟩/🟨/🟩🟩). Dormant since 2019 yet 6.9k downloads/wk: the Sass world still uses `ms()`.
5. **[shevyjs](https://github.com/kyleshevlin/shevyjs)** — 4. Best CSS-in-JS option if you need one.
6. **[plumber](https://github.com/jamonserrano/plumber)** — 3 (🟩/🟩/🟥/🟩🟩). Lowest maintenance score, highest capability — the only true baseline-snapper.
7. **[vertical-rhythm-reset](https://github.com/jhildenbiddle/vertical-rhythm-reset)** — 3 (🟨/🟩/🟩/🟩). Alive-ish (2024), tiny, obsolete by design.
8. **[Sassy-Gridlover](https://github.com/hiulit/Sassy-Gridlover)** / **[typesettings](https://github.com/ianrose/typesettings)** — 3 / 3.
9. **[typecsset](https://github.com/csswizardry/typecsset)** / **[typographic](https://github.com/2metres/typographic)** — 0 / 2. Historic.
10. **[basekick](https://github.com/michaeltaranto/basekick)** — 0. Superseded by Capsize from the same author.

### Fluid type

1. **🥇 [RFS](https://github.com/twbs/rfs)** — 8 (🟩🟩/🟩/🟩🟩/🟩🟩). Boring, maintained, in Bootstrap.
2. **🥈 [fluid-tailwind](https://github.com/barvian/fluid-tailwind)** — 7 (🟩🟩/🟩/🟩/🟩🟩). The best-designed API in the category and the WCAG check; watch the commit gap.
3. **🥉 [tailwindcss-fluid-type](https://github.com/davidhellmann/tailwindcss-fluid-type)** — 5.
4. **[fluid-type-scale-calculator](https://github.com/AleksandrHovhannisyan/fluid-type-scale-calculator)** — 4; **[modern-fluid-typography-editor](https://github.com/codeAdrian/modern-fluid-typography-editor)** — 3.
5. **[utopia-core](https://github.com/trys/utopia-core)** — 2 on the rubric (🟨/⬜/🟩/🟩) — and the **tool this article actually recommends**. The score is an artefact of a 138★ helper repo behind a hosted product; Utopia the *method* has no competitor.
6. **[postcss-responsive-type](https://github.com/seaneking/postcss-responsive-type)** — 1; **[FitText.js](https://github.com/davatron5000/FitText.js)** — 2, historic.

### Leading trim, metrics, fallbacks

1. **🥇 [fontaine](https://github.com/unjs/fontaine)** — 8. Pushed on the snapshot date; 585k downloads/wk.
2. **🥈 [Capsize](https://github.com/seek-oss/capsize)** — 8 on the rubric, second by usage (106k core). Tied on score; fontaine wins the *default* slot because it needs no metrics import, Capsize wins wherever you need `capHeight` sizing.
3. **🥉 [opentype.js](https://github.com/opentypejs/opentype.js)** — 8; a parser, not a typography tool, but the escape hatch when both above fall short.
4. **[fontkit](https://github.com/foliojs/fontkit)** — 5 (license ⬜).
5. **[fontpie](https://github.com/pixel-point/fontpie)** — 4.

## Disregarded

- **[Compass/compass](https://github.com/Compass/compass)** `vertical_rhythm` — deprecated (*"Compass is no longer actively maintained"*), Ruby-Sass only. Named in the tables for vocabulary, not use.
- **[jakegiltsoff/sassline](https://github.com/jakegiltsoff/sassline)** — kept in the leaderboard because it still scores well on the rubric, but the author's own README deprecates it; treat as reading material.
- **[michaeltaranto/basekick](https://github.com/michaeltaranto/basekick)** — superseded by Capsize, same author, no license.
- **[oleq/syncope](https://github.com/oleq/syncope)** — GPL-3.0 in a category where every alternative is MIT; 2017.
- **The 2012–2016 long tail** surfaced by `gh search repos "vertical rhythm"` — `cssrecipes/vertical-rhythm` (31★), `gavinmcfarland/gridlover-mixin` (29★), `betsol/baseline-element` (24★), `MattWilcox/jQuery-Baseline-Align` (20★), `mpalpha/sass-vertical-rhythm` (20★), `Gaya/jQuery--Keep-the-Rhythm` (19★), `zellwk/vertical-rhythms-without-compass` (18★), `KyleAMathews/compass-vertical-rhythm` (18★), `mattbaker/Arrhythmia` (12★), `sfcgeorge/Grid-Bookmarklet` (11★), `Financial-Times/o-typography` (10★, FT-brand-specific), `thedayhascome/Fluid-Baseline-Grid` (245★, a 2012 HTML boilerplate, not a library), `jpsilvashy/basic-column-layout` (101★, XHTML-era), `kristoferjoseph/postcss-modular-scale` (53★, 2016), `mgsisk/postcss-modular-rhythm` (3★, archived), `melrosesolutions/postcss-baseline-vertical-rhythm` (2★; pushed 2026-09-03 but 2★ and unread), `andrasna/postcss-baseline-grid-overlay` (8★), `sakamies/postcss-gridlover` (13★), `tol-is/styled-baseline` (15★), `tyssen/Less-Baseline-Grid-Generator` (21★). All either under 50★, pre-2017, or both; none adds a capability the scored tools lack.
- **`line-height-step` / CSS Rhythmic Sizing** — a spec, not a tool; no shipping implementation. Covered in [gotchas](#gotchas-that-break-the-grid).
- **Typeface selection, pairing, variable-font axes (`opsz`, `wght`), OpenType features (`tnum`, `lnum`)** — real typography concerns, out of this article's typesetting-mechanics scope. `font-optical-sizing: auto` is on by default and does the right thing for `opsz` fonts ([MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/font-optical-sizing)); `font-variant-numeric: tabular-nums` is the one feature that affects rhythm (table columns), noted here so the search finds it.
- **Tremor-style "typography components"** in UI kits, Medium/Substack/iA reading-mode CSS — not reviewable as tools; their choices are reflected in the design-system table where token sources were public.
- **Atlassian Design System** typography tokens — docs are JS-rendered and returned nothing to automated fetches; ⬜ rather than guessed.

## Discovery — search queries

- `gh search repos "vertical rhythm" --sort stars --limit 25`; `"baseline grid"`, `"fluid typography"`, `"text-box-trim"`, `"leading-trim"`.
- `gh api repos/<owner>/<repo>` for every scored repo; `gh api repos/<o>/<r>/readme` for README reads; `gh api repos/seek-oss/capsize/releases/latest`; `gh api search/code?q=capsizecss+repo:seek-oss/braid-design-system`.
- npm: `api.npmjs.org/downloads/point/last-week/<pkg>` for `@capsizecss/core`, `@capsizecss/metrics`, `fontaine`, `fontpie`, `utopia-core`, `fluid-tailwind`, `tailwindcss-fluid-type`, `@tailwindcss/typography`, `rfs`, `typography`, `vertical-rhythm-reset`, `sassline`, `plumber-sass`, `modularscale-sass`.
- Token sources read from GitHub raw: `flutter/flutter` `typography.dart` (Material 3), `microsoft/fluentui` `fonts.ts`, `alphagov/govuk-frontend` `_typography-responsive.scss`, `uswds/uswds` `type-scale.scss` + `line-height.scss`, `adobe/spectrum-design-data` `typography.json` (the repo formerly named `spectrum-tokens`), `Shopify/polaris` `font.ts` + `size.ts`, `primer/primitives` `typography.json5`, `carbon-design-system/carbon` `scale.ts`, `seek-oss/braid-design-system` `makeVanillaTheme.ts`, `tailwindlabs/tailwindcss-typography` `styles.js`, `twbs/bootstrap` `_variables.scss`, `argyleink/open-props` `props.fonts.css`; Apple HIG typography page JSON for the Dynamic Type tables.
- Web: `vertical rhythm css 2026 lh rlh`, `text-box-trim browser support`, `leading-trim Ethan Wang Microsoft`, `capsize seek css`, `fluid typography clamp WCAG 1.4.4 zoom`, `utopia fluid type scale`, `fluid tailwind checkSC144`, `line-height-step chrome intent to ship`, `Material Design 4dp baseline grid type`, `Android 14 non-linear font scaling`, `Compose LineHeightStyle Trim`, `Flutter TextLeadingDistribution`, `React Native includeFontPadding`.
- Browser support: caniuse.com pages for `css-text-box-trim`, `css-math-functions`, `css-container-query-units`, `mdn-css_types_round`, `mdn-css_types_length_lh`, `font-size-adjust`, `css-text-wrap-balance`, `css-hanging-punctuation`, `mdn-css_properties_margin-trim`; MDN `text-box-trim`; `mdn/browser-compat-data` main.
- Reproduction: `npm init -y && npm i @capsizecss/core @capsizecss/metrics utopia-core` in `/tmp`, then `createStyleString`, `createStyleObject`, `createFontStack`, `calculateClamp`, `checkWCAG`, `calculateTypeScale` as quoted.

## Not verified

Marked ⬜ in the body; listed here so the next pass knows where to look:

- Figma plugin install counts other than *Typescales* (figma.com returns 403 to automated fetches; one mirror worked for one plugin).
- typescale.com Pro price (not in served HTML).
- `trys/utopia-core` and `foliojs/fontkit` licence — the GitHub API returns no SPDX id; check the repos' `package.json` before vendoring.
- `boriskirov/fluiditype`, `jakobsen/fluid-typography`, `jeromev/baselinegrid.scss` licences and READMEs (not fetched; sub-threshold).
- Atlassian Design System typography tokens (JS-rendered docs).
- Carbon's fluid-type-at-breakpoints claim (docs page returned no body to the fetcher; the scale formula is from source).
- Exact web-features Baseline *date* for `text-box-trim` (MDN says "since August 2026"; the feature file carries no date line).
- Ethan Wang's Medium post could not be fetched directly (Cloudflare block); quotes about it come from the CSS-Tricks and bram.us relays of 2020-08-21/22, and the syntax from the spec.

---

**Snapshot 2026-09-07.** Submit issues for missing tools, repo updates, or corrections.
