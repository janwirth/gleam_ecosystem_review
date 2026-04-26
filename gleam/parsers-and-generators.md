# Parsers and code generators in Gleam

You have text or data and you want a typed Gleam value.
Or you have a schema and you want typed Gleam code.
This article maps both directions, plus the cross-language cases.

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Scoring Dimensions](#scoring-dimensions)
   - [Discovery](#discovery)
3. [Categories](#categories)
   - [Gleam Parsers](#gleam-parsers) — parser combinators, lexers, and format-specific parsers written in Gleam
   - [Other-Language Parsers](#other-language-parsers) — Gleam libraries that parse the source of other languages
   - [Gleam Generators](#gleam-generators) — Gleam libraries that emit Gleam code or other artifacts
   - [X to Gleam Generators](#x-to-gleam-generators) — codegen tools that take an external input and emit Gleam
4. [Gaps](#gaps)
5. [Leaderboard](#leaderboard)

## Summary

**Snapshot 2026-04-26.** The Gleam ecosystem covers a lot of parsing ground in pure Gleam: parser combinator toolkits ([nibble](#nibble), [chomp](#chomp), [atto](#atto), [party](#party)), data-format parsers ([tom](#tom), [jot](#jot), [gsv](#gsv), [gleam_xmlm](#gleam_xmlm)), and HTML helpers ([presentable_soup](#presentable_soup), [htmgrrrl](#htmgrrrl), [javascript_dom_parser](#javascript_dom_parser)). For other-language parsing, [glance](#glance) parses Gleam source itself, and [crowdhailer/oas](#oas) reads OpenAPI specs. Code generation is split across Gleam-emitting tools ([gleamgen](#gleamgen), [glue](#glue), [derived](#derived), [gserde](#gserde)) and X→Gleam pipelines (SQL via [squirrel](databases.md#squirrel-)/[parrot](#parrot)/[marmot](#marmot)/[sqlode](databases.md#sqlode-); OpenAPI via [oaspec](#oaspec) and [gilly](#gilly); JSON Schema via [json_typedef](#json_typedef)).

The single missing direction is **Gleam → spec** — no published package introspects mist/wisp routes and emits OpenAPI 3.x. See [Gaps](#gaps) for the structural reason.

| Category | Highlights |
| --- | --- |
| [Gleam parsers](#gleam-parsers) | Combinators: [nibble](#nibble), [chomp](#chomp), [atto](#atto), [party](#party) · Formats: [tom](#tom), [jot](#jot), [gsv](#gsv), [gleam_xmlm](#gleam_xmlm), [kirala_markdown](#kirala_markdown) · HTML: [presentable_soup](#presentable_soup), [htmgrrrl](#htmgrrrl), [javascript_dom_parser](#javascript_dom_parser) · DSL: [cel-gleam](#cel-gleam) · Strings: [splitter](#splitter) |
| [Other-language parsers](#other-language-parsers) | [glance](#glance) (Gleam source) · [oas](#oas) (OpenAPI 3.x) · syntax-highlighting lexers (cross-link → [syntax-highlighting.md](syntax-highlighting.md)) |
| [Gleam generators](#gleam-generators) | [gleamgen](#gleamgen), [glue](#glue), [derived](#derived), [gserde](#gserde), [json_typedef](#json_typedef) |
| [X → Gleam generators](#x-to-gleam-generators) | SQL: [squirrel](databases.md#squirrel-) 🐘, [parrot](#parrot) 🐘🪶🐬, [marmot](#marmot) 🪶, [sqlode](databases.md#sqlode-) 🐘🪶🐬 (cross-link → [databases.md](databases.md#sql-code-generators)) · OpenAPI: [oaspec](#oaspec), [gilly](#gilly), [oas_generator](#oas_generator) |

> [!IMPORTANT]
> **Gleam → OpenAPI (code-first) is a gap.** No package introspects routes and emits a spec. See [Gaps](#gaps) for the spec-first workaround.

> [!NOTE]
> Where parser libraries overlap with [syntax-highlighting.md](syntax-highlighting.md) (e.g., per-language lexers used as highlighter input), this article focuses on the parsing/AST role. Read both articles together if you need both signals.

## Research Method

### Scoring Dimensions

Same rubric as [databases.md](./databases.md#scoring-dimensions) and [web-apps.md](./web-and-http/web-apps.md#scoring-dimensions). Recap:

- **Stars:** 🟩🟩 ≥200★, 🟩 ≥100★, 🟨 ≥10★, 🟥 <10★. ⬜ = not shown by host.
- **License:** 🟩 permissive (MIT, Apache-2.0, BSD, Unlicense). 🟥 viral or missing.
- **Gleam compat:** `gleam_stdlib` constraint format. 🟩 range (`>= X and < Y`), 🟥 non-range or missing.
- **Maintenance:** `max(recency, responsiveness)`. 🟩🟩 <1 month / clean tracker, 🟩 <6 months, 🟨 <1 year, 🟥 >1 year or ignored.
- **Age:** First commit to snapshot. 🟩🟩 ≥3 years, 🟩 ≥1 year, 🟨 ≥3 months, 🟥 <3 months.
- **README maturity:** 🟩🟩 guide-style with examples + feature docs, 🟩 clear tagline + usage, 🟥 minimal.
- **Idiomaticity:** 🟩 typed, explicit, no magic. 🟥 magic directives or template extraction.

**Leaderboard scoring:** 🟥 = −1, 🟨/⬜ = 0, 🟩 = 1, 🟩🟩 = 2. Sum of all 7 dimensions. Max = 13.

### Discovery

Searches run on **2026-04-26** against [packages.gleam.run](https://packages.gleam.run/):

| Query | URL |
| --- | --- |
| `parser` | [packages.gleam.run/?search=parser](https://packages.gleam.run/?search=parser) |
| `combinator` | [packages.gleam.run/?search=combinator](https://packages.gleam.run/?search=combinator) |
| `lexer` | [packages.gleam.run/?search=lexer](https://packages.gleam.run/?search=lexer) |
| `parse` | [packages.gleam.run/?search=parse](https://packages.gleam.run/?search=parse) |
| `codegen` | [packages.gleam.run/?search=codegen](https://packages.gleam.run/?search=codegen) |
| `generator` | [packages.gleam.run/?search=generator](https://packages.gleam.run/?search=generator) |
| `serialize` | [packages.gleam.run/?search=serialize](https://packages.gleam.run/?search=serialize) |
| `openapi` | [packages.gleam.run/?search=openapi](https://packages.gleam.run/?search=openapi) |
| `swagger` | [packages.gleam.run/?search=swagger](https://packages.gleam.run/?search=swagger) |
| `json` | [packages.gleam.run/?search=json](https://packages.gleam.run/?search=json) |
| `regex` | [packages.gleam.run/?search=regex](https://packages.gleam.run/?search=regex) |
| `html` | [packages.gleam.run/?search=html](https://packages.gleam.run/?search=html) |
| `sql` | [packages.gleam.run/?search=sql](https://packages.gleam.run/?search=sql) |
| `markdown` | [packages.gleam.run/?search=markdown](https://packages.gleam.run/?search=markdown) |
| `tree-sitter` | [packages.gleam.run/?search=tree-sitter](https://packages.gleam.run/?search=tree-sitter) (0 hits) |
| `ast` | [packages.gleam.run/?search=ast](https://packages.gleam.run/?search=ast) |

**Out of scope** (covered in dedicated articles, not duplicated here):

- **Per-language syntax-highlighter lexers** (contour, just, pearl, tear, smalto, glimra) → [syntax-highlighting.md](syntax-highlighting.md)
- **JSON encoder/decoder libraries** (gleam_json, jackson, juno, simplejson) — these are runtime decoders, not codegen; covered as dependencies elsewhere
- **CLI argument parsers** (glint, clip, hoist, glap, sheen) — domain-specific, covered when relevant in CLI tooling
- **UUID / NanoID / identicon generators** — value generators, not code generators
- **HTML/CSS/PDF/Typst document generators** (nakai, htmz, paddlefish, glypst, sketch_css) — output-document generators, not Gleam-code generators

## Categories

### Gleam Parsers

Libraries that parse text or data **inside Gleam** at runtime. Three sub-shapes:

1. **Parser combinator toolkits** — primitives (`many`, `sep`, `between`, etc.) for hand-rolling a parser.
2. **Format-specific parsers** — TOML, JSON, CSV, XML, Markdown, HTML, etc.
3. **String-slicing primitives** — building blocks below combinator level.

#### Parser combinator toolkits

| Criterion | [nibble](#nibble) | [chomp](#chomp) | [atto](#atto) | [party](#party) |
| --- | --- | --- | --- | --- |
| Stars | 82★ · 🟥 | 9★ · 🟥 | 33★ · 🟨 | 18★ · 🟨 |
| License | MIT · 🟩 | Apache-2.0 · 🟩 | MIT · 🟩 | MPL-2.0 · 🟥 |
| Target | ☎️📜 Both | ☎️📜 Both | ☎️📜 Both | ☎️📜 Both |
| Maintenance | 🟩 (last commit 2025-07-29) | 🟨 (last commit 2024-12-31) | 🟩 (last commit 2025-08-17) | 🟩🟩 (last commit 2026-04-21) |
| Age | ~3 years (Mar 2023) · 🟩🟩 | ~1.5 years (late 2024) · 🟩 | ~2.5 years · 🟩 | ~3 years · 🟩🟩 |
| README maturity | 🟩🟩 (guide, examples, lexer + parser) | 🟩 (clear: "fork of nibble that does some things differently") | 🟩🟩 (combinator reference, error-message focus) | 🟩 (tagline + usage) |
| Idiomaticity | 🟩 (typed, explicit) | 🟩 | 🟩 | 🟩 |
| Issues | 1 open | 1 open | 0 open | 1 open |

Pick: **nibble** for the most-used choice (82★, used by [chomp](#chomp) as its base). **chomp** if you want better error messages and don't mind a fork. **atto** for robust combinators with custom-stream support. **party** for a small surface and recent activity.

##### nibble
[repo](https://github.com/hayleigh-dot-dev/gleam-nibble)

"A lexer and parser combinator library inspired by `elm/parser`." Two-stage design: a traditional lexer produces tokens, then combinators consume them. Pure Gleam, dual-target. By Hayleigh Thompson (creator of [Lustre](https://github.com/lustre-labs/lustre)).

```gleam
import nibble
import nibble/lexer

pub fn main() {
  let tokens = lexer.run("3 + 4", my_lexer)
  let assert Ok(ast) = nibble.run(tokens, my_parser)
  echo ast
}
```

##### chomp
[repo](https://github.com/MystPi/chomp)

"A fork of nibble that does some things differently." Removes "error dead ends" and adds custom error types. Same lexer + combinator shape. Stale-ish (last commit Dec 2024) but stable.

##### atto
[repo](https://github.com/ieeemma/atto)

"Robust and extensible parser-combinators for Gleam." Combinator inventory (`many`, `sep`, `between`, choice with backtracking), beautiful error messages, custom-stream support, and contextual grammar capabilities. Active in 2025.

##### party
[repo](https://github.com/RyanBrewer317/party)

"A little parser combinator library in Gleam." Small surface, recent stdlib upgrade (Apr 2026). README explicitly recommends [atto](#atto) for more advanced needs. **Note:** MPL-2.0 license is weak copyleft at the file level — flag for shops standardising on MIT/Apache.

#### Data-format parsers

Pure-Gleam parsers for specific formats — useful as drop-in replacements for hand-rolling.

| Tool | Format | Stars | Latest commit | Notes |
| --- | --- | --- | --- | --- |
| [tom](#tom) | TOML | 38★ · 🟨 | recent | Pure-Gleam TOML parser by Louis Pilfold |
| [jot](#jot) | Djot (Markdown-like) | 60★ · 🟥 | 2026-04-07 | Parser + AST + HTML rendering |
| [gsv](#gsv) | CSV | 18★ · 🟨 | 2026-04-18 | `to_lists` / `to_dicts`, custom delimiters |
| [gleam_xmlm](#gleam_xmlm) | XML (pull-based) | 11★ · 🟨 | 2026-01-05 | Inspired by OCaml `xmlm` |
| [kirala_markdown](#kirala_markdown) | Markdown | 5★ · 🟥 | 2024-01-09 | Markdown → HTML, BEAM + JS |
| [mork](https://git.liten.app/krig/mork) | Markdown | ⬜ (self-hosted) | — | Markdown parser; repo on liten.app (gated by Anubis, no public metrics) |
| [cel-gleam](#cel-gleam) | Common Expression Language | 5★ · 🟥 | 2026-04-24 | Lex + parse + evaluate; interpreter |

##### tom
[repo](https://github.com/lpil/tom)

"A pure Gleam TOML parser!" By Louis Pilfold. Provides `tom.parse()` plus typed getter helpers. 3 open issues at snapshot (notably TOML v1.1.0 support and dynamic decoding) — usable but not actively-developed.

```gleam
import tom

pub fn main() {
  let assert Ok(parsed) = tom.parse("name = \"alice\"\nage = 30\n")
  let assert Ok("alice") = tom.get_string(parsed, ["name"])
}
```

##### jot
[repo](https://github.com/lpil/jot)

"A parser for Djot, a markdown-like language." Two outputs: AST (`jot.parse`) and HTML (`jot.to_html`). Covers headings, lists, links, code blocks, math, and text formatting. Active (last commit 2026-04-07, v11.0.0).

##### gsv
[repo](https://github.com/giacomocavalieri/gsv)

"A simple csv parser and serialiser for Gleam." `to_lists()` for nested-list output, `to_dicts()` for header-keyed records. Custom delimiter support. Active (last commit 2026-04-18, v5.1.0).

##### gleam_xmlm
[repo](https://github.com/mooreryan/gleam_xmlm)

"A pull-based XML parser for the Gleam programming language." Inspired by OCaml's `xmlm`. Includes XML conformance tests, benchmarking, and Erlang profiling tooling. Dual-licensed Apache-2.0 / MIT.

##### kirala_markdown
[repo](https://github.com/Yasuo-Higano/kirala_markdown)

Markdown parser + HTML renderer. Both Erlang and JavaScript targets. Stale (last commit 2024-01-09, "for gleam ver 0.33") — usable but unlikely to track recent stdlib changes.

##### cel-gleam
[repo](https://github.com/JonasHedEng/cel-gleam)

A Gleam implementation of [Common Expression Language](https://github.com/google/cel-spec) — Google's "non-Turing complete language designed for simplicity, speed, safety, and portability." Provides lexer, parser, AST, and expression evaluator. Useful for embedding rule/policy DSLs in Gleam apps. Active (last commit 2026-04-24).

#### HTML parsers

Three approaches to reading HTML in Gleam, none of them overlapping:

| Tool | Approach | Target | Stars | Notes |
| --- | --- | --- | --- | --- |
| [presentable_soup](#presentable_soup) | High-level: query, scrape, snapshot-test | ☎️ BEAM (htmerl) | 23★ · 🟨 | "Good for snapshot testing" |
| [htmgrrrl](#htmgrrrl) | Low-level SAX events | ☎️ BEAM (htmerl) | 14★ · 🟨 | Stale (last commit 2023-12-08) |
| [javascript_dom_parser](#javascript_dom_parser) | Bindings to browser `DOMParser` | 📜 JS only | 8★ · 🟥 | Stale (last commit 2024-04-07) |

##### presentable_soup
[repo](https://github.com/lpil/presentable-soup)

"Efficient querying, scraping, and parsing of HTML. Good for snapshot testing too!" Built on the Erlang `htmerl` parser. Highest-level of the three: query elements, scrape multiple, integrate with [birdie](https://github.com/giacomocavalieri/birdie) snapshots. Active (last commit 2026-02-05, v2.0.0).

##### htmgrrrl
[repo](https://github.com/lpil/htmgrrrl)

"Gleam bindings to htmerl, the fast and memory efficient Erlang HTML SAX parser." Event-based — receive `start_element`, `end_element`, `characters`. Lower-level than presentable_soup; useful when you don't want a tree in memory. Last commit 2023-12-08 — unmaintained but stable bindings.

##### javascript_dom_parser
[repo](https://github.com/lpil/javascript-dom-parser)

"Bindings to the JavaScript `DOMParser` API." JS target only — for Lustre/SSR or browser-side parsing. Last commit 2024-04-07.

#### String-slicing primitives

##### splitter
[repo](https://github.com/lpil/splitter)

"Efficiently slice prefixes from strings. Good for parsers!" Below the combinator level — used as a dependency by several syntax-highlighter lexers (just, pearl, tear). 18★, last commit 2025-11-18, 0 open issues.

### Other-Language Parsers

Gleam libraries that parse the source/structure of **other** languages.

| Tool | Parses | Stars | Latest commit | Notes |
| --- | --- | --- | --- | --- |
| [glance](#glance) | Gleam source code | 80★ · 🟥 | 2025-10-21 | Permissive parser, accepts code the compiler rejects |
| [oas](#oas) | OpenAPI 3.x specs (+ JSON Schema) | 15★ · 🟨 | 2026-04-16 | Used as input for [oas_generator](#oas_generator) |
| [glance_printer](#glance_printer) | Re-emits glance AST as Gleam | ⬜ | — | Pretty printer for the glance AST |

> [!NOTE]
> **Per-language syntax-highlighting lexers** (Gleam, Erlang, Elixir, JavaScript) live in [syntax-highlighting.md](syntax-highlighting.md). They parse other languages too — the role-difference is "extract token stream for colourisation" vs "extract AST for analysis/codegen." If you want to highlight Gleam code, use [contour](syntax-highlighting.md#contour); if you want to analyse Gleam code, use [glance](#glance).

#### glance
[repo](https://github.com/lpil/glance)

"A Gleam source code parser, in Gleam!" Permissive — accepts some code the official compiler rejects, useful for refactoring tools and analysers. By Louis Pilfold. Active (last commit 2025-10-21, v6.0.0).

```gleam
import glance

pub fn main() {
  let code = "
    pub type Cardinal {
      North
      East
      South
      West
    }
  "
  let assert Ok(parsed) = glance.module(code)
  echo parsed.custom_types
}
```

Used as a dependency by [crowdhailer/oas_generator](#oas_generator), [dusty-phillips/derived](#derived), and others doing source-level codegen.

#### glance_printer
[repo](https://github.com/bcpeinhardt/glance_printer)

"A pretty printer for the glance AST." Companion to [glance](#glance) — pairs the parser with a re-emitter, closing the source → AST → source loop required for refactor tools.

#### oas
[repo](https://github.com/crowdhailer/oas)

"Work with Open API Specs (previously swagger) and JSON Schema." Decodes OpenAPI 3.x documents into Gleam types (paths, components, operations). Used as input by [oas_generator](#oas_generator). Apache-2.0. 0 open issues at snapshot. Note: gleam.toml has `target = "javascript"` — JS-first, but parsing is platform-agnostic.

### Gleam Generators

Libraries that **emit Gleam code** from Gleam — typically called by build scripts or other codegen tools.

| Tool | Input | Output | Stars | Latest commit | Notes |
| --- | --- | --- | --- | --- | --- |
| [gleamgen](#gleamgen) | Gleam DSL (phantom-typed) | Formatted .gleam files | 27★ · 🟨 | — | Type-safe Gleam-emitting Gleam |
| [glue](#glue) | Gleam source (via glance) | Gleam functions in same module | 9★ · 🟥 | 2024-12-08 | `compare`, `list_variants` derivers |
| [derived](#derived) | Gleam source w/ `!derived()` markers | Gleam functions inserted in-place | 2★ · 🟥 | 2025-08-03 | "Code generator's code generator" |
| [gserde](#gserde) | Gleam types | JSON encode/decode functions | 32★ · 🟨 | 2025-12-13 | Alpha quality (per README) |
| [json_typedef](#json_typedef) | RFC 8927 JSON Type Definition | Gleam encoders/decoders | 51★ · 🟥 | 2025-04-27 | Schema-first JSON codegen |

#### gleamgen
[repo](https://github.com/Brewingweasel/gleamgen)

"A package for generating clean, type-checked, and formatted Gleam code!" Phantom-typed builder API — the type system enforces that the generated code typechecks. Removes unused imports, inlines functions, simplifies blocks. Foundational dependency for several other generators in the ecosystem.

#### glue
[repo](https://github.com/lpil/glue)

"A package for generating functions from your Gleam code!" Built-in derivers: `compare` (orderings for custom types) and `list_variants` (list-of-all-variants for sum types). Reads existing Gleam source via [glance](#glance), inserts generated functions. By Louis Pilfold. Last commit 2024-12-08 — usable but not actively-developed.

#### derived
[repo](https://github.com/dusty-phillips/derived)

"The gleam code generator's code generator." Marker-based: write `!derived(...)` directives in your Gleam source, run `derived`, and the tool inserts generated code at protected markers (round-trip safe). For building custom serialisation, validation, or documentation derivers. Last commit 2025-08-03.

#### gserde
[repo](https://github.com/cdaringe/gserde)

"gleam codegen for serialization and deserialization." Generates JSON encode/decode functions for custom types. README declares **alpha** quality. Last commit 2025-12-13. Useful for prototyping; for production, prefer hand-written `gleam_json` decoders or the schema-first [json_typedef](#json_typedef).

#### json_typedef
[repo](https://github.com/lpil/json-typedef)

"Work with JSON using a schema! RFC8927." JSON Type Definition (RFC 8927) — a JSON schema standard simpler than JSON Schema itself. Decodes a TypeDef document into Gleam, then generates encoders/decoders. By Louis Pilfold. 51★, last commit 2025-04-27, v1.1.3.

> [!NOTE]
> **Reverse direction — Gleam → spec** (e.g. mist/wisp routes → OpenAPI document) is **not** covered in this category. See [Gaps](#gaps).

### X to Gleam Generators

Codegen tools that take an **external input** (SQL, OpenAPI, JSON Schema, etc.) and **emit Gleam**. The biggest, most-actively-developed slice of the parser/generator space.

#### SQL → Gleam

Cross-link: [databases.md → SQL Code Generators](databases.md#sql-code-generators) covers these in depth, with a comparison table for **squirrel vs sqlode**. Quick map:

| Tool | Dialects | Style | Stars | Notes |
| --- | --- | --- | --- | --- |
| [squirrel](databases.md#squirrel-) 🐘 | PostgreSQL 16+ | `.sql` files → typed Gleam decoders | 633★ | Established, FOSDEM-talked, tight pog integration |
| [parrot](#parrot) 🐘🪶🐬 | PostgreSQL · SQLite · MySQL | sqlc plugin → typed Gleam | 206★ | sqlc-backed (renamed from `sqlc-gen-gleam`) |
| [sqlode](databases.md#sqlode-) 🐘🪶🐬 | PostgreSQL · SQLite · MySQL 8 | sqlc-style schema + queries → typed Gleam | 3★ | Brand new (Apr 2026), driver-agnostic at codegen |
| [marmot](#marmot) 🪶 | SQLite | `.sql` files → typed functions | 2★ | Inspired by squirrel, SQLite-only |

##### parrot
[repo](https://github.com/daniellionel01/parrot) (renamed from `sqlc-gen-gleam`)

"🦜 type-safe SQL in gleam." A sqlc plugin: leverages sqlc's mature SQL parser/codegen pipeline and emits Gleam. Supports SQLite, PostgreSQL, and MySQL. Auto-downloads the sqlc binary; processes `src/sql/` files; outputs a unified `sql.gleam` module. README explicitly credits sqlc: *"Most of the heavy lifting features are provided by / built into sqlc, I do not aim to take credit for them."* Different from squirrel (which has its own PostgreSQL parser) and from [sqlode](databases.md#sqlode-) (which writes its own multi-dialect codegen).

| Criterion | [parrot](https://github.com/daniellionel01/parrot) |
| --- | --- |
| Stars | 206★ · 🟩🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM (codegen + generated code) |
| Gleam compat | `gleam_stdlib >= 0.34.0 and < 2.0.0` · 🟩 |
| Maintenance | 🟩 (last commit 2026-02-03) |
| Age | ~1 year · 🟩 |
| README maturity | 🟩🟩 (multi-DB walkthrough, sqlc integration, examples) |
| Idiomaticity | 🟩 (explicit codegen, sqlc-backed) |
| Issues | 3 open |

##### marmot
[repo](https://github.com/pairshaped/marmot)

"Type-safe SQL for SQLite in Gleam. Write `.sql` files, generate typed functions." Inspired by squirrel, focused on SQLite. Brand new — small star count, but fills the SQLite-codegen gap left by squirrel (which is PostgreSQL-only). Last commit 2026-04-21.

| Criterion | [marmot](https://github.com/pairshaped/marmot) |
| --- | --- |
| Stars | 2★ · 🟥 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM (SQLite via [sqlight](databases.md#sqlight-)) |
| Maintenance | 🟩🟩 (last commit 2026-04-21, ~5 days vs snapshot) |
| Age | ~1 month · 🟥 |
| README maturity | 🟩🟩 (squirrel-style guide, examples) |
| Idiomaticity | 🟩 |
| Issues | 0 open |

#### OpenAPI → Gleam

Three tools cover this corner. Both [oaspec](#oaspec) and [gilly](#gilly) are very young (Apr 2026) but ship usable code today; [oas_generator](#oas_generator) is older, built on [oas](#oas).

##### Comparison

| Criterion | [oaspec](https://github.com/nao1215/oaspec) | [gilly](https://github.com/cyclimse/gilly) | [oas_generator](https://github.com/crowdhailer/oas_generator) |
| --- | --- | --- | --- |
| Stars | 4★ · 🟥 | 1★ · 🟥 | 25★ · 🟨 |
| License | MIT · 🟩 | Unlicense (public domain) · 🟩 | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM | ☎️📜 Both (uses gleam/httpc + gleam/fetch) |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 | `>= 0.44 and < 2.0` · 🟩 | unspecified (no gleam constraint) · 🟨 |
| Maintenance | 🟩🟩 (last commit 2026-04-26, snapshot day) | 🟩🟩 (last commit 2026-04-19, 1 wk) | 🟩🟩 (last commit 2026-04-16, ~10 days) |
| Age | ~3 weeks (Apr 7, 2026) · 🟥 | ~2 weeks (Apr 12, 2026) · 🟥 | ~older · 🟩 |
| README maturity | 🟩🟩 (full guide, CLI ref, library API, capability boundaries) | 🟩 (description, usage, flag table, examples link) | 🟩 (description + usage) |
| Idiomaticity | 🟩 (explicit codegen) | 🟩 (explicit codegen) | 🟩 (explicit codegen) |
| Issues | 5 open | 3 open | ⬜ |
| | | | |
| **Features** | | | |
| Generates client SDK | ✅ | ✅ | ✅ |
| Generates server stubs (handlers + router) | ✅ | — | — |
| Spec format | YAML + JSON | JSON | OpenAPI 3.0+ |
| `$ref` resolution | ✅ (incl. circular detection) | partial | via `oas` |
| `oneOf` / `anyOf` / `allOf` | ✅ | partial | via `oas` |
| Path / query / header / cookie params | ✅ | path, query, header | typical |
| `deepObject` / `pipeDelimited` / `spaceDelimited` | ✅ | — | — |
| Form bodies (`x-www-form-urlencoded`, `multipart`) | ✅ | — | — |
| Security schemes (apiKey, HTTP, OAuth2, OIDC) | ✅ (bearer attach for OAuth2/OIDC) | manual via request middleware | basic |
| Validation guards from schema constraints | ✅ | — | — |
| `validate` subcommand (no codegen) | ✅ | — | — |
| CI drift check (`--check`) | ✅ | — | — |
| Library API (use as Gleam dep, not just CLI) | ✅ | ✅ | ✅ |
| Snapshot tests on generated output | — | ✅ ([birdie](https://github.com/giacomocavalieri/birdie)) | — |

> [!IMPORTANT]
> Both **oaspec** and **gilly** are days-to-weeks old at snapshot. They move fast — every PR in both repos was merged within hours of opening. Neither has a community track record yet. Pin a version.

> [!NOTE]
> Generated code is plain Gleam built on `gleam/http` types, so it composes with the rest of the ecosystem (e.g. [server frameworks](web-and-http/web-apps.md#server-frameworks), [HTTP clients](web-and-http/http-clients.md)). The generators themselves run on BEAM (oaspec/gilly) or are dual-target (oas_generator).

##### oaspec
[repo](https://github.com/nao1215/oaspec)

"Generate strongly typed Gleam server stubs and client SDK from OpenAPI 3.x specifications." Wide coverage of the OpenAPI features that show up in real specs: `$ref`, `allOf`/`oneOf`/`anyOf`, `deepObject` query params, form and multipart bodies, all four major security scheme families. Capability boundaries are listed in the README and enforced at validation time — unsupported keywords (`prefixItems`, `not`, `unevaluatedProperties`, etc.) fail fast instead of producing broken code. Backed by 624 unit tests, 40 integration compile tests, 182 fixtures (94 OSS-derived). Distributed as an Erlang escript binary; also usable as a Gleam library with a pure pipeline (`parse → normalize → resolve → capability check → hoist → dedup → validate → codegen`).

CLI:
```sh
oaspec init
# edit oaspec.yaml: input, package, mode (server | client | both)
oaspec generate --config=oaspec.yaml
```

Output layout:
```text
gen/my_api/        types, decode, encode, request_types, response_types,
                   middleware, guards, handlers, router
gen_client/my_api/ types, decode, encode, request_types, response_types,
                   middleware, guards, client
```

Generated client signature:
```gleam
pub type Pet {
  Pet(id: Int, name: String, status: PetStatus, tag: Option(String))
}

pub type PetStatus {
  PetStatusAvailable
  PetStatusPending
  PetStatusSold
}

pub fn create_pet(config: ClientConfig, body: types.CreatePetRequest)
  -> Result(response_types.CreatePetResponse, ClientError)
```

CI drift check:
```sh
oaspec generate --config=oaspec.yaml --check --fail-on-warnings
```

##### gilly
[repo](https://github.com/cyclimse/gilly)

"Generate Gleam SDKs from OpenAPI specifications." Client-only scope, inspired by [squirrel](https://github.com/giacomocavalieri/squirrel) (Postgres codegen) and [oapi-codegen](https://github.com/oapi-codegen/oapi-codegen) (Go). Builder-pattern request constructors, snapshot-tested with [birdie](https://github.com/giacomocavalieri/birdie). README is upfront about scope: "many features from the OpenAPI specification are not yet supported"; generated code may break between releases. Ships a real-world Scaleway Containers example (~80 lines) that creates a serverless namespace, polls it to ready, and deploys an nginx container — useful as a reference for how the generated builder API composes with `gleam/httpc`.

CLI:
```sh
gleam add gilly --dev
gleam run -m gilly -- openapi.json --output src/my_api.gleam
```

Flags worth knowing:
- `--optionality {RequiredOnly | NullableOnly | RequiredAndNullable}` — how to decide which fields are `Option(_)` (default `RequiredOnly`)
- `--optional-query-params` — force all query params optional regardless of spec
- `--indent N` — spaces per indent level (default 2)

Generated client usage (Scaleway example, abridged):
```gleam
let api_client =
  client.new(send_fn, region: region, project_id: project_id)

let assert Ok(namespace) =
  client.new_create_namespace_request(
    name: "gilly-example",
    activate_vpc_integration: True,
    secret_environment_variables: [],
    tags: ["example", "gilly"],
  )
  |> client.create_namespace(api_client)
```

The `send_fn` is yours — gilly stays out of HTTP transport. Plug in `gleam/httpc`, `gleam/hackney`, or anything that round-trips `gleam/http` types.

##### oas_generator
[repo](https://github.com/crowdhailer/oas_generator)

"Generate HTTP clients from Open API specs." Older entrant in the OpenAPI corner, built on top of the [oas](#oas) parser library. Supports both backend (`gleam/httpc`) and frontend (`gleam/fetch`) — making it the dual-target choice when oaspec/gilly's BEAM-only output won't fit. 25★, last commit 2026-04-16. Same author as `oas`.

#### JSON Schema → Gleam

Cross-reference: [json_typedef](#json_typedef) is the schema-first JSON codegen entry, listed under [Gleam generators](#gleam-generators) above (because it both parses the schema and emits the Gleam — making it both an "other-language parser" and a "Gleam generator" in one package). Listed here for completeness.

#### Other → Gleam

Adjacent codegen patterns worth knowing about (not surveyed in detail here):

- **GraphQL → Gleam** — [squall](https://github.com/bigmoves/squall) ("Type-safe GraphQL client generator"). Out of scope for this snapshot; warrants a dedicated review.
- **gRPC → Gleam** — no published package as of 2026-04-26.
- **Protobuf → Gleam** — no published package as of 2026-04-26.
- **MCP tools → Gleam** — [aide_generator](https://github.com/crowdhailer/aide_generator) ("Generate encoders and decoders for MCP tools"). Same author as `oas`/`oas_generator`.
- **Static assets → Gleam** — [embeds](https://github.com/arkandos/gleam-embeds) ("Generate Gleam modules based on static assets") — bake files into compiled modules.

## Gaps

What the Gleam ecosystem does **not** have, as of 2026-04-26.

### Gleam → OpenAPI (code-first spec generation)

If you have a [mist](./web-and-http/web-apps.md#mist) or [wisp](./web-and-http/web-apps.md#wisp) app and want an OpenAPI spec emitted from your routes and handler types — **no such tool exists in Gleam yet**.

| Direction | Status | Tools |
| --- | --- | --- |
| Spec → Gleam (server stubs + client SDK) | ✅ covered | [oaspec](#oaspec), [gilly](#gilly), [oas_generator](#oas_generator) |
| **Gleam → Spec (code-first)** | ❌ **none** | — |
| Spec parsing (as a library) | ✅ covered | [oas](#oas) |

> [!IMPORTANT]
> If you want code-first OpenAPI in the style of [FastAPI](https://fastapi.tiangolo.com/), [Ktor](https://ktor.io/docs/openapi-spec-generation.html), [tsoa](https://tsoa-community.github.io/docs/), or [utoipa](https://github.com/juhaku/utoipa), **the Gleam ecosystem cannot give it to you today**. You will either hand-write the spec, generate it outside Gleam, or adopt the spec-first workflow with [oaspec](#oaspec)/[gilly](#gilly)/[oas_generator](#oas_generator).

#### Why the gap exists

The structural reason: **Gleam has no runtime type reflection and no macros.** A code-first generator would have to walk the compiler AST or require developers to write a parallel description of each route (defeating the point). Neither approach exists as a published package.

What a hypothetical `mist_openapi` or `wisp_openapi` would need to do:

1. **Enumerate routes.** Both mist and wisp dispatch via ordinary Gleam pattern-match on the request — there is no declarative route registry to walk. An annotation layer or a routing DSL would need to exist first.
2. **Extract parameter and body types.** Gleam functions carry static types, but those types are erased on BEAM and absent from JS. Without macros or reflection, the generator would have to parse Gleam source (the compiler AST) at build time.
3. **Emit JSON Schema.** A type → schema mapper for Gleam records, custom types (sum types → `oneOf`), `Option` (nullable), `List` (array), `Dict` (`additionalProperties`), and generics.
4. **Assemble the document.** Compose into a full OpenAPI 3.x document (security schemes, servers, tags, responses) and serialise.

#### What exists that could be reused

- [crowdhailer/oas](#oas) — data model for the output document (could be the encode side if builders + a JSON encoder were added).
- [gleam_json](https://hexdocs.pm/gleam_json/) — JSON serialisation.
- [glance](#glance) — Gleam source parser, the most plausible bridge to "extract types from handler functions."
- [gleamgen](#gleamgen) / [derived](#derived) — code-emit infrastructure.
- Gleam compiler's published package metadata — type information exists in `.cache_meta` / HexDocs JSON, in principle reachable.

#### What is missing

- Any published Gleam-level route registry abstraction (the mist/wisp idiom is an opaque pattern match).
- Any type-introspection mechanism. Gleam has [no macros](https://github.com/gleam-lang/gleam/issues/1767) and no runtime reflection by design, so the path is source-level codegen against the compiler AST — a non-trivial dependency on unstable compiler internals.

#### Workarounds

- **Hand-written spec** — keep `openapi.yaml` in the repo, feed it to [oaspec](#oaspec) with `--check` in CI to detect drift between spec and generated server stubs. This inverts the code-first model: the **spec** is authoritative, your Gleam server is derived.
- **Spec-first as default** — if your reason for wanting code-first is "I don't want to maintain a YAML file by hand," spec-first tooling now covers enough of the OpenAPI surface that the YAML can be short and mostly auto-scaffoldable from an initial sketch.

### Tree-sitter wrappers (general parser NIFs)

[glimra](syntax-highlighting.md#glimra) wraps tree-sitter for syntax-highlighting only. There is no general-purpose Gleam tree-sitter binding for parsing other languages into ASTs. If you want to walk a Python or Rust file from Gleam, you'd write your own NIF.

### gRPC / Protobuf codegen

No published Gleam package emits Gleam code from `.proto` definitions, and no Gleam gRPC server framework exists. Workaround: generate Erlang stubs and call them from Gleam.

## Leaderboard

Every contribution is invaluable and appreciated.

This article spans many sub-categories — best-fit picks per category are highlighted in the relevant section above. The leaderboard below is **per category** rather than overall, since "the best parser combinator" and "the best SQL→Gleam generator" aren't comparable.

### Parser combinators

| Position | Award | Repo | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [hayleigh-dot-dev/gleam-nibble](https://github.com/hayleigh-dot-dev/gleam-nibble) | 🟥 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **7** |
| 2 | 🥈 | [ieeemma/atto](https://github.com/ieeemma/atto) | 🟨 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **7** |
| 3 | 🥉 | [RyanBrewer317/party](https://github.com/RyanBrewer317/party) | 🟨 | 🟥 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩 | **6** |
| 4 | — | [MystPi/chomp](https://github.com/MystPi/chomp) | 🟥 | 🟩 | 🟩 | 🟨 | 🟩 | 🟩 | 🟩 | **3** |

### Format-specific parsers (Gleam-side)

| Position | Award | Repo | Format | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [giacomocavalieri/gsv](https://github.com/giacomocavalieri/gsv) | CSV | 🟨 | 🟨 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **9** |
| 2 | 🥈 | [lpil/jot](https://github.com/lpil/jot) | Djot | 🟥 | 🟨 | 🟩 | 🟩🟩 | 🟩 | 🟩🟩 | 🟩 | **7** |
| 2 | 🥈 | [lpil/tom](https://github.com/lpil/tom) | TOML | 🟨 | 🟨 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩 | **7** |
| 4 | 🥉 | [JonasHedEng/cel-gleam](https://github.com/JonasHedEng/cel-gleam) | CEL | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩 | 🟩 | **6** |
| 5 | — | [mooreryan/gleam_xmlm](https://github.com/mooreryan/gleam_xmlm) | XML | 🟨 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | **7** |

(Note: gsv, jot, tom licenses not surfaced via README/sidebar inspection — recorded as 🟨 unknown rather than asserting; check repos directly.)

### HTML parsers

| Position | Award | Repo | ★ | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [lpil/presentable-soup](https://github.com/lpil/presentable-soup) | 🟨 | 🟩🟩 | 🟩 | 🟩 | 🟩 | **6** |
| 2 | 🥈 | [lpil/htmgrrrl](https://github.com/lpil/htmgrrrl) | 🟨 | 🟥 | 🟩🟩 | 🟩 | 🟩 | **4** |
| 3 | 🥉 | [lpil/javascript-dom-parser](https://github.com/lpil/javascript-dom-parser) | 🟥 | 🟥 | 🟩🟩 | 🟩 | 🟩 | **2** |

### Other-language parsers

| Position | Award | Repo | Parses | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [lpil/glance](https://github.com/lpil/glance) | Gleam source | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **9** |
| 2 | 🥈 | [crowdhailer/oas](https://github.com/crowdhailer/oas) | OpenAPI 3.x | 🟨 | 🟩 | 🟨 | 🟩🟩 | 🟩 | 🟩 | 🟩 | **7** |

### Gleam generators

| Position | Award | Repo | Output | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [Brewingweasel/gleamgen](https://github.com/Brewingweasel/gleamgen) | Gleam (DSL) | 🟨 | 🟨 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **6** |
| 2 | 🥈 | [lpil/json-typedef](https://github.com/lpil/json-typedef) | Gleam (RFC 8927) | 🟥 | 🟨 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩 | **5** |
| 3 | 🥉 | [cdaringe/gserde](https://github.com/cdaringe/gserde) | JSON encoders/decoders | 🟨 | 🟨 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | **6** |
| 4 | — | [dusty-phillips/derived](https://github.com/dusty-phillips/derived) | Custom (markers) | 🟥 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | **5** |
| 5 | — | [lpil/glue](https://github.com/lpil/glue) | Gleam (`compare`, etc.) | 🟥 | 🟨 | 🟩 | 🟨 | 🟩 | 🟩 | 🟩 | **3** |

### X → Gleam generators (combined)

Includes SQL, OpenAPI, JSON Schema. Cross-reference: [databases.md leaderboard](databases.md#leaderboard) covers SQL codegen alongside drivers and migrations.

| Position | Award | Repo | Input | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [giacomocavalieri/squirrel](https://github.com/giacomocavalieri/squirrel) | SQL (PostgreSQL) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **9** |
| 2 | 🥈 | [daniellionel01/parrot](https://github.com/daniellionel01/parrot) | SQL (sqlc; multi-DB) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **9** |
| 3 | 🥉 | [nao1215/oaspec](https://github.com/nao1215/oaspec) | OpenAPI 3.x | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **6** |
| 4 | — | [crowdhailer/oas_generator](https://github.com/crowdhailer/oas_generator) | OpenAPI 3.x | 🟨 | 🟩 | 🟨 | 🟩🟩 | 🟩 | 🟩 | 🟩 | **6** |
| 5 | — | [pairshaped/marmot](https://github.com/pairshaped/marmot) | SQL (SQLite) | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **5** |
| 6 | — | [nao1215/sqlode](https://github.com/nao1215/sqlode) | SQL (multi-DB) | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **5** |
| 7 | — | [cyclimse/gilly](https://github.com/cyclimse/gilly) | OpenAPI 3.x | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩 | 🟩 | **4** |

### Honorable mentions

- **[squirrel](https://github.com/giacomocavalieri/squirrel)** — the established SQL→Gleam codegen, FOSDEM-talked, 633★. The reference implementation other tools cite.
- **[parrot](https://github.com/daniellionel01/parrot)** — sqlc-backed, multi-DB, 206★. Renamed from `sqlc-gen-gleam`.
- **[oaspec](https://github.com/nao1215/oaspec)** — wide OpenAPI coverage out of the gate, capability boundaries documented, server *and* client.
- **[gilly](https://github.com/cyclimse/gilly)** — tight scope, builder-pattern API, snapshot-tested, real-world Scaleway example.
- **[glance](https://github.com/lpil/glance)** — the Gleam-source parser that makes most Gleam-level codegen possible.
- **[nibble](https://github.com/hayleigh-dot-dev/gleam-nibble)** — the most-used parser combinator library in the ecosystem.

### Related reviews

- **[databases.md](databases.md)** — SQL→Gleam codegen in depth (squirrel, sqlode comparison table) plus drivers, query builders, migrations.
- **[syntax-highlighting.md](syntax-highlighting.md)** — per-language lexers (contour, just, pearl, tear, smalto) and tree-sitter NIFs (glimra). Overlaps with "other-language parsers" but role-different.
- **[web-apps.md](web-and-http/web-apps.md)** — mist, wisp, server frameworks; relevant context for the [Gleam → OpenAPI](#gleam--openapi-code-first-spec-generation) gap.
- **[http-clients.md](web-and-http/http-clients.md)** — HTTP transports the OpenAPI-generated SDKs plug into.

[How scores are calculated →](#scoring-dimensions)
