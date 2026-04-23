# Gleam → OpenAPI generators

You have a [mist](./web-and-http/web-apps.md#mist) or [wisp](./web-and-http/web-apps.md#wisp) app.
You want an OpenAPI spec emitted from the routes and handler types you already wrote.
Short answer: no such tool exists in Gleam yet.

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Discovery](#discovery)
3. [Adjacent Tooling](#adjacent-tooling)
   - [crowdhailer/oas](#crowdhaileroas--spec-parser-not-a-generator)
   - [Hand-written spec](#hand-written-spec--source-of-truth-outside-gleam)
   - [Spec-first with oaspec / gilly](#spec-first-with-oaspec--gilly)
4. [The Gap](#the-gap)
5. [Leaderboard](#leaderboard)

## Summary

Snapshot: **2026-04-21**.

The [OpenAPI → Gleam generators](./openapi-to-gleam-generators.md) review covers the direction that **does** work today: `oaspec` and `gilly` consume a spec and emit typed Gleam. This article covers the reverse — **code → spec** — and the finding is negative: no Gleam package introspects or annotates HTTP handlers and emits OpenAPI 3.x.

| Direction | Status | Tools |
| --- | --- | --- |
| Spec → Gleam (server stubs + client SDK) | ✅ covered | [oaspec](./openapi-to-gleam-generators.md#oaspec), [gilly](./openapi-to-gleam-generators.md#gilly) |
| **Gleam → Spec (code-first)** | ❌ **none** | — |
| Spec parsing (as a library) | ✅ covered | [crowdhailer/oas](https://github.com/crowdhailer/oas) |

> [!IMPORTANT]
> If you want code-first OpenAPI in the style of [FastAPI](https://fastapi.tiangolo.com/), [Ktor](https://ktor.io/docs/openapi-spec-generation.html), [tsoa](https://tsoa-community.github.io/docs/), or [utoipa](https://github.com/juhaku/utoipa), **the Gleam ecosystem cannot give it to you today**. You will either hand-write the spec, generate it outside Gleam, or adopt the spec-first workflow with [oaspec](./openapi-to-gleam-generators.md#oaspec)/[gilly](./openapi-to-gleam-generators.md#gilly).

> [!NOTE]
> The structural reason: Gleam has no runtime type reflection and no macros. A code-first generator would have to walk the compiler AST or require developers to write a parallel description of each route (defeating the point). Neither approach exists as a published package.

## Research Method

Same rubric as [web-apps.md](./web-and-http/web-apps.md#scoring-dimensions) — but there are no repos to score.

### Discovery

Queries run on **2026-04-21**:

| Query | Source | Result |
| --- | --- | --- |
| `openapi` | [Gleam packages registry](https://packages.gleam.run/?search=openapi) | 2 hits — both **spec → code** ([oaspec](./openapi-to-gleam-generators.md#oaspec), [gilly](./openapi-to-gleam-generators.md#gilly)) |
| `swagger` | [Gleam packages registry](https://packages.gleam.run/?search=swagger) | 0 hits |
| `mist` | [Gleam packages registry](https://packages.gleam.run/?search=mist) | [mist](./web-and-http/web-apps.md#mist), [mist_reload](./web-and-http/hot-reloading.md#mist_reload), [sprocket_mist](./web-and-http/web-apps.md#sprocket), [howdy](./web-and-http/web-apps.md#howdy) — none emit specs |
| `wisp` | [Gleam packages registry](https://packages.gleam.run/?search=wisp) | [wisp](./web-and-http/web-apps.md#wisp) + adapters — none emit specs |
| `gleam openapi mist` | [GitHub repo search](https://github.com/search?q=gleam+openapi+mist&type=repositories) | 0 hits |
| `gleam code-first openapi` / `gleam openapi generator` / `gleam swagger` | Web search | Only reverse-direction results + the `oas` parser |

**0 included.** No Gleam package produces OpenAPI output from Gleam source.

## Adjacent Tooling

Not generators, but useful if you need to bridge the gap yourself.

### [crowdhailer/oas](https://github.com/crowdhailer/oas) — spec parser, not a generator

`oas` decodes an OpenAPI 3.x document into Gleam types (paths, components, operations). It is the **input side** of the problem — a library that could plausibly be used in reverse to *build* a spec with Gleam constructors and serialise to JSON/YAML, but nothing in the package advertises or automates that direction. You would be writing the builder yourself.

- License: Apache-2.0
- Intended use: read specs (validation, client generation, documentation tooling)
- As of 2026-04-21: no `encode` / `to_json` entry points in the public API

### Hand-written spec — source of truth outside Gleam

The common fallback. Keep `openapi.yaml` in the repo, feed it to [oaspec](./openapi-to-gleam-generators.md#oaspec) with `--check` in CI to detect drift between spec and generated server stubs. This inverts the code-first model: the **spec** is authoritative, your Gleam server is derived.

### Spec-first with `oaspec` / `gilly`

If your reason for wanting code-first is "I don't want to maintain a YAML file by hand," spec-first tooling now covers enough of the OpenAPI surface that the YAML can be short and mostly auto-scaffoldable from an initial sketch. See the [OpenAPI → Gleam generators](./openapi-to-gleam-generators.md) review for coverage.

## The Gap

What a hypothetical `mist_openapi` or `wisp_openapi` would need to do:

1. **Enumerate routes.** Both mist and wisp dispatch via ordinary Gleam pattern-match on the request — there is no declarative route registry to walk. An annotation layer or a routing DSL would need to exist first.
2. **Extract parameter and body types.** Gleam functions carry static types, but those types are erased on BEAM and absent from JS. Without macros or reflection, the generator would have to parse Gleam source (the compiler AST) at build time.
3. **Emit JSON Schema.** A type → schema mapper for Gleam records, custom types (sum types → `oneOf`), `Option` (nullable), `List` (array), `Dict` (`additionalProperties`), and generics.
4. **Assemble the document.** Compose into a full OpenAPI 3.x document (security schemes, servers, tags, responses) and serialise.

**What exists that could be reused:**

- [crowdhailer/oas](https://github.com/crowdhailer/oas) — data model for the output document (could be the encode side if builders + a JSON encoder were added).
- [gleam_json](https://hexdocs.pm/gleam_json/) — JSON serialisation.
- Gleam compiler's published package metadata — type information exists in `.cache_meta` / HexDocs JSON, in principle reachable.

**What is missing:**

- Any published Gleam-level route registry abstraction (the mist/wisp idiom is an opaque pattern match).
- Any type-introspection mechanism. Gleam has [no macros](https://github.com/gleam-lang/gleam/issues/1767) and no runtime reflection by design, so the path is source-level codegen against the compiler AST — a non-trivial dependency on unstable compiler internals.

## Leaderboard

No entries at snapshot. This article will be updated when a Gleam → OpenAPI tool appears — if you ship one, [open an issue](https://github.com/janwirth/ecosystem_review/issues) or PR here.

**Related reviews**
- [OpenAPI → Gleam generators](./openapi-to-gleam-generators.md) — the direction that *is* covered.
- [Web apps](./web-and-http/web-apps.md) — mist, wisp, howdy, and adapter packages referenced above.
