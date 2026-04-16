# Tools for building web applications with Gleam

You want to ~~become a pokemon trainer~~ build a web app in gleam?
Don't know where to start?
Start here.

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Evaluation Criteria](#evaluation-criteria)
   - [Repos Reviewed](#repos-reviewed)
   - [Disregarded Repos](#disregarded-repos)
3. [Categories](#categories)
   - [Full-Stack Frameworks](#full-stack-frameworks) — [glimr](#glimr)
   - [Server Frameworks](#server-frameworks) — [wisp](#wisp) · [dream](#dream) · [glen](#glen)
   - [Static Site Generators](#static-site-generators) — [arctic](#arctic)
   - [Dev Tools](#dev-tools) — [lustre_dev_tools](#lustre_dev_tools) · [gleam-radiate](#gleam-radiate)
   - [RPC Layer](#rpc-layer) — [libero](#libero)
   - [Frontend Frameworks](#frontend-frameworks) — [lustre](#lustre) · [redraw](#redraw)
   - [Core HTTP Types](#core-http-types) — [http](#http)
   - [HTTP Servers](#http-servers) — [mist](#mist) · [ewe](#ewe)
   - [Network Infrastructure](#network-infrastructure) — [glisten](#glisten) · [cowboy](#cowboy)
4. [Leaderboard](#leaderboard)

## Summary



Gleam's ecosystem is growing fast, and keeping track of what's available isn't easy.

I put this together so you don't have to spend hours on research, or worse, build something that already exists. 34 repos evaluated — **15 included**, 19 disregarded.

The essentials to build something great are there.


| Category | ☎️ BEAM | 📜 JS |
| --- | --- | --- |
| **[Full-Stack Frameworks](#full-stack-frameworks)** | · [glimr](#glimr) ([repo](https://github.com/glimr-org/glimr), 165★) — *young; routing, templates, DB schema + migrations, auth, hot reload* | — |
| **[Server Frameworks](#server-frameworks)** | · [🥈](#leaderboard) [wisp](#wisp) ([repo](https://github.com/gleam-wisp/wisp), 1.4k★) — *handler+middleware, production-ready*<br>· [dream](#dream) ([repo](https://github.com/TrustBound/dream), 39★) — *composable, no magic, batteries-included* | · [glen](#glen) ([repo](https://github.com/MystPi/glen), 111★) — *lightweight, promise-based, Deno/Node/Workers* |
| **[Static Site Generators](#static-site-generators)** | · [arctic](#arctic) ([repo](https://github.com/RyanBrewer317/arctic), 26★) — *content collections, parsers, SSG via lustre_ssg* | — |
| **[Frontend Frameworks](#frontend-frameworks)** (Elm-style) | · [🥇](#leaderboard) [lustre](#lustre) ([repo](https://github.com/lustre-labs/lustre), 2.2k★) — *declarative UI, SSR, universal* | · [🥇](#leaderboard) [lustre](#lustre) ([repo](https://github.com/lustre-labs/lustre), 2.2k★) — *also runs on JS target* |
| **[Frontend Frameworks](#frontend-frameworks)** (React) | — | · [redraw](#redraw) ([repo](https://github.com/ghivert/redraw), 62★) — *full React 19 bindings, type-checked* |
| **[RPC Layer](#rpc-layer)** | · [libero](#libero) ([repo](https://github.com/pairshaped/libero), 3★) — *young; typed RPC for Lustre+server, WebSocket, codegen* | · [libero](#libero) — *also generates JS client stubs* |
| **[Dev Tools](#dev-tools)** | · [🥉](#leaderboard) [lustre_dev_tools](#lustre_dev_tools) ([repo](https://github.com/lustre-labs/dev-tools), 112★) — *dev server, bundling, Tailwind*<br>· [gleam-radiate](#gleam-radiate) ([repo](https://github.com/pta2002/gleam-radiate), 66★) — *BEAM module reload* | — |
| **[HTTP Servers](#http-servers)** | · [🥇](#leaderboard) [mist](#mist) ([repo](https://github.com/rawhat/mist), 489★) — *HTTP server, WebSocket, streaming*<br>· [🥉](#leaderboard) [ewe](#ewe) ([repo](https://github.com/vshakitskiy/ewe), 106★) — *HTTP server, TLS, WebSocket, compression* | — |
| **[Network Infrastructure](#network-infrastructure)** | · [🥉](#leaderboard) [glisten](#glisten) ([repo](https://github.com/rawhat/glisten), 197★) — *TCP/TLS layer, powers mist*<br>· [cowboy](#cowboy) ([repo](https://github.com/gleam-lang/cowboy), 75★) — *adapter for existing Erlang/Elixir Cowboy setups* | — |
| **[Core HTTP Types](#core-http-types)** | · [🥉](#leaderboard) [http](#http) ([repo](https://github.com/gleam-lang/http), 276★) — *request/response types, used by nearly everything* | · [🥉](#leaderboard) [http](#http) ([repo](https://github.com/gleam-lang/http), 276★) — *also targets JS* |

> [!IMPORTANT]  
> Crucial information necessary for users to succeed.


> <details>
> <summary><strong>Dependency Graph</strong></summary>
>
> Arrows read "uses."
>
> **[Full-Stack Frameworks](#full-stack-frameworks):**
> - [glimr](#glimr) → [mist](#mist) (HTTP server)
>
> **[Server Frameworks](#server-frameworks):**
> - [wisp](#wisp) → [mist](#mist) (HTTP server)
> - [dream](#dream) → [mist](#mist) (HTTP server)
> - [glen](#glen) → [http](#http) (core types) — *JS target, independent stack*
>
> **[Static Site Generators](#static-site-generators):**
> - [arctic](#arctic) → [lustre](#lustre) + lustre_ssg (rendering)
>
> **[RPC Layer](#rpc-layer):**
> - [libero](#libero) → [lustre](#lustre) (client stubs) — works with any server
>
> **[Frontend Frameworks](#frontend-frameworks):**
> - [lustre](#lustre), [redraw](#redraw) — standalone, integrate with any backend
>
> **[Dev Tools](#dev-tools):**
> - [lustre_dev_tools](#lustre_dev_tools) — pairs with [lustre](#lustre)
> - [gleam-radiate](#gleam-radiate) — framework-agnostic
>
> **[HTTP Servers](#http-servers):**
> - [mist](#mist) → [glisten](#glisten) (TCP/TLS) → [http](#http) (core types)
> - [ewe](#ewe) → [glisten](#glisten) (TCP/TLS) → [http](#http) (core types)
>
> **[Network Infrastructure](#network-infrastructure):**
> - [glisten](#glisten) → [http](#http) (core types) — *TCP/TLS layer, powers mist and ewe*
> - [cowboy](#cowboy) → [http](#http) (core types) — *adapter for Erlang's Cowboy*
>
> **[Core HTTP Types](#core-http-types):**
> - [http](#http) — no upstream deps; depended on by nearly everything above
>
> </details>


## Research Method

<details>
<summary>Expand</summary>

**Snapshot 2026-04-13** — data from **public GitHub web pages and raw README URLs only** (no clone, no GitHub API). 34 repos evaluated: 15 included in detailed review, 19 disregarded.

**Legend:** 🟩🟩 strong · 🟩 OK · ⬜ unknown / not shown / not applicable · 🟥 negative signal.

### Evaluation Criteria

Each repo evaluated using public GitHub pages only (no API, no clone):

1. **Fetch repo homepage** — Extract star count, description
2. **Fork check** — Verify the repo is not a fork; if it is, use the upstream original instead
3. **Read README** — Assess maturity (guide-style / clear / minimal), examples, design intent
4. **Commit history** — Latest commit date (ISO), visible history span
5. **Gleam.toml** — Check Gleam version requirement, dependencies
6. **Issues tab** — Count open issues, assess health

**Note on code samples:** All code examples are taken from project READMEs or docs, not independently verified or tested.

> <details>
> <summary><strong>Scoring dimensions</strong></summary>
>
> - **Stars:** Community adoption signal based on star count. 🟩🟩 = ≥200★, 🟩 = ≥100★, 🟨 = ≥10★, 🟥 = <10★. *Example: 165★ → 🟩; 3★ → 🟥.*
> - **License:** Whether the license allows unrestricted commercial use. 🟩 = permissive OSS (MIT, Apache-2.0, BSD). 🟥 = viral (GPL, AGPL) or no license. Only one tier — permissive or not.
> - **Gleam compat:** Checks `gleam_stdlib` version constraint format in `gleam.toml` `[dependencies]`. 🟩 = range constraint (`>= X and < Y`), 🟥 = non-range constraint (`~>` style) which can cause resolver conflicts during install. *Example: `"~> 0.34 or ~> 1.0"` → 🟥; `">= 0.44.0 and < 2.0.0"` → 🟩.*
> - **Maintenance:** Latest commit date relative to snapshot (2026-04-13), combined with commit frequency. Does **not** factor in issues. 🟩🟩 = recent + frequent (within ~2 months), 🟩 = within 5 months, 🟨 = >5 months old (watch), 🟥 = >1 year (dormant). *Example: last commit 2025-09-18 = ~7 months → 🟨.*
> - **Age:** Time from first visible commit to snapshot date. Older projects have more time to stabilize. 🟩🟩 = ≥3 years, 🟩 = ≥1 year, 🟨 = ≥3 months, 🟥 = <3 months. *Example: history starts Mar 2026, snapshot Apr 2026 → ~1 month → 🟥.*
> - **README maturity:** Quality of documentation in the repo README. 🟩🟩 = guide-style with examples, full feature docs, and getting-started instructions. 🟩 = clear description and basic usage. 🟥 = minimal, broken, or missing. *Example: batteries-included framework with full guide → 🟩🟩; tagline-only → 🟩.*
> - **Idiomaticity:** Whether the project follows Gleam's principles: typed, sound, explicit, no magic. 🟩 = idiomatic (explicit APIs, no codegen, no annotation magic). 🟥 = relies on codegen, annotations, or implicit behavior that breaks Gleam's explicit style. *Example: `/// @get "/path"` annotation routing → 🟥; builder pattern → 🟩.* Note: Gleam gives more freedom than e.g. Elm, but non-idiomatic patterns are still discouraged by the community. Projects that use them are included and graded rather than excluded — significant bodies of work deserve coverage regardless of style.
>
> **Why maintenance and issues are separate:** A repo can be actively committed to but have a growing issue backlog (feature requests, unfixed bugs), or have zero issues but be dormant. Conflating them hides useful signal. Check both rows.
>
> **Leaderboard scoring:** 🟥 = −1, 🟨 = 0, 🟩 = 1, 🟩🟩 = 2. Sum of all 7 dimensions. Max possible = 13.
>
> </details>

### Repos Reviewed

Discovered via [Gleam packages registry](https://packages.gleam.run/) searches. Relevant results picked from each; duplicates and non-web packages ignored. Listed by category.

- [server](https://packages.gleam.run/?search=server)
- [backend](https://packages.gleam.run/?search=backend)
- [frontend](https://packages.gleam.run/?search=frontend)
- [react](https://packages.gleam.run/?search=react)
- [web](https://packages.gleam.run/?search=web)

**Full-Stack Frameworks:**
- **[glimr-org/glimr](https://github.com/glimr-org/glimr)** — Batteries-included: routing, templates, middleware, DB schema + migrations, caching, auth, hot reload. Built on Mist.

**Server Frameworks:**
- **[gleam-wisp/wisp](https://github.com/gleam-wisp/wisp)** — Practical web framework. Handler + middleware pattern, pragmatic, production-ready.
- **[TrustBound/dream](https://github.com/TrustBound/dream)** — Composable web framework. "Clean, composable web development for Gleam. No magic." Router, middleware, JSON support.
- **[MystPi/glen](https://github.com/MystPi/glen)** — Lightweight web framework. "A peaceful web framework for Gleam that targets JS."

**Static Site Generators:**
- **[RyanBrewer317/arctic](https://github.com/RyanBrewer317/arctic)** — Content-driven SSG. Collections, custom parsers, builds via lustre_ssg. Serverless-friendly.

**Dev Tools:**
- **[lustre-labs/dev-tools](https://github.com/lustre-labs/dev-tools)** — Lustre CLI + dev server. Zero-config dev server, bundling, TailwindCSS v4, live reload.
- **[pta2002/gleam-radiate](https://github.com/pta2002/gleam-radiate)** — Hot-reload dev tool. Watches source files and reloads BEAM modules.

**RPC Layer:**
- **[pairshaped/libero](https://github.com/pairshaped/libero)** — Typed RPC for Lustre+Gleam. `@rpc` annotations generate client stubs, routes, encoders/decoders. WebSocket transport, compile-time type safety.

**Frontend Frameworks:**
- **[lustre-labs/lustre](https://github.com/lustre-labs/lustre)** — Declarative HTML, Elm-style state, universal components, SSR, CLI tooling.
- **[ghivert/redraw](https://github.com/ghivert/redraw)** — React bindings for Gleam. Full React 19 support, type-checked, JS target.

**Core HTTP Types:**
- **[gleam-lang/http](https://github.com/gleam-lang/http)** — HTTP types and functions. Core layer linking server/client adapters (Mist, Cowboy, Plug, fetch).

**HTTP Servers & Adapters:**
- **[rawhat/mist](https://github.com/rawhat/mist)** — HTTP server. Builder pattern, WebSocket, streaming, flexible routing. Powers glimr and wisp.
- **[vshakitskiy/ewe](https://github.com/vshakitskiy/ewe)** — HTTP server. TLS, WebSocket, compression. Independent alternative to mist.
- **[gleam-lang/cowboy](https://github.com/gleam-lang/cowboy)** — HTTP adapter. Integration layer for Cowboy web server.
- **[rawhat/glisten](https://github.com/rawhat/glisten)** — TCP/TLS server. Pure Gleam, supervised socket acceptors, TLS support. Low-level building block.

### Disregarded Repos

> <details>
> <summary>Excluded from detailed review due to maintenance or scope issues</summary>
>
> **Unmaintained:**
> - **[gleam-lang/plug](https://github.com/gleam-lang/plug)** — HTTP adapter for Elixir Plug compatibility. Last commit 2020-08-27 (6+ years dormant). Activity 🟥.
>
> **No Longer Maintained:**
> - **[fravan/olive](https://github.com/fravan/olive)** — Hot-reload dev tool. 8★, short history (Feb–Jul 2025). README states "no longer in active development" and warns of rough edges. Maintenance 🟥.
>
> **Superseded / Low Activity:**
> - **[brettkolodny/react-gleam](https://github.com/brettkolodny/react-gleam)** — React bindings for Gleam. 69★, 27 commits (Nov 2022–Sep 2025). Superseded by redraw which has fuller React 19 coverage. Maintenance 🟥.
> - **[samifouad/gild_frontend](https://github.com/samifouad/gild_frontend)** — React client-side framework for Gleam. 0★, 18 commits, "extremely alpha." Abandoned experiment. Work 🟥.
> - **[TobiasBinnewies/novdom](https://gitlab.com/TobiasBinnewies/novdom)** — Frontend framework for Gleam. GitLab-hosted. 93 commits, last Sep 2024 (7+ months stale). v0.5.0. MIT. Maintenance 🟥.
> - **[TobiasBinnewies/cosmos](https://gitlab.com/TobiasBinnewies/cosmos)** — UI library for novdom. 3 commits, v0.0.0. Early experiment, depends on inactive novdom. Work 🟥.
>
> **Experiments & Stubs:**
> - **[myzykyn/gleam_webserver](https://github.com/myzykyn/gleam_webserver)** — Stub/scaffold. Single commit, default TODO template. Not usable; abandoned experiment. Work 🟥.
> - **[gleam-lang/example-echo-server](https://github.com/gleam-lang/example-echo-server)** — Reference implementation. Learning example only; not for production use.
>
> **Discovered via [web](https://packages.gleam.run/?search=web) search — not reviewed:**
>
> | Package | Repo | ★ | Last update | Recommendation | Reasoning |
> | --- | --- | --- | --- | --- | --- |
> | dew | [rawhat/dew](https://github.com/rawhat/dew) | 490 | 2026-04-01 | **Exclude** | Same repo as mist (identical GitHub repo ID) — old package name. |
> | ewe | [vshakitskiy/ewe](https://github.com/vshakitskiy/ewe) | 106 | 2026-04-01 | **Included** | HTTP Servers & Adapters. Independent mist alternative built on glisten. |
> | tiramisu | [renatillas/tiramisu](https://github.com/renatillas/tiramisu) | 101 | 2026-03-29 | **Exclude** | 3D game engine, not web app tooling — out of scope. |
> | dream | [TrustBound/dream](https://github.com/TrustBound/dream) | 39 | 2026-03-28 | **Included** | Server Frameworks. Composable alternative to wisp with more built-ins. |
> | glare | [Endercheif/glare](https://github.com/Endercheif/glare) | 35 | 2024-05-18 | **Exclude** | SolidJS-based framework, dormant ~2 years. Maintenance 🟥. |
> | arctic | [RyanBrewer317/arctic](https://github.com/RyanBrewer317/arctic) | 26 | 2025-07-16 | **Included** | Static Site Generators (new category). Content-driven SSG via lustre_ssg. |
> | howdy | [mikeyjones/howdy](https://github.com/mikeyjones/howdy) | 12 | 2022-06-19 | **Exclude** | API wrapper on Mist, dormant ~4 years. Maintenance 🟥. |
> | gliew | [arnarg/gliew](https://github.com/arnarg/gliew) | 9 | 2023-06-12 | **Exclude** | SSR framework, dormant ~3 years. Maintenance 🟥. |
> | meadow | [JoelVerm/meadow](https://github.com/JoelVerm/meadow) | 8 | 2024-05-12 | **Exclude** | Server side for glare — dormant, depends on dormant glare. |
> | bliss | [sporto/bliss](https://github.com/sporto/bliss) | 7 | 2022-05-12 | **Exclude** | Micro web framework experiment, dormant ~4 years. Maintenance 🟥. |
> | mist_reload | [crowdhailer/mist_reload](https://github.com/crowdhailer/mist_reload) | 5 | 2026-03-28 | **Exclude** | Dev tool overlapping with gleam-radiate, 5★, too niche. |
> | mysig | [crowdhailer/mysig](https://github.com/crowdhailer/mysig) | 4 | 2025-12-05 | **Exclude** | Web toolkit by CrowdHailer, 4★, low traction. |
> | refrakt | [raskell-io/refrakt](https://github.com/raskell-io/refrakt) | 1 | 2026-04-11 | **Exclude** | Phoenix-inspired framework, 1★ — too early, no traction yet. |
> | lily | [haleywhazel/lily](https://github.com/haleywhazel/lily) | 0 | 2026-04-14 | **Exclude** | Reactive framework, 0★ — just started, no traction. |
>
> </details>

</details>


## Categories

### Full-Stack Frameworks

All-in-one solutions: routing, templates, middleware, auth, database, development tools.

| Criterion | [glimr](https://github.com/glimr-org/glimr) |
| --- | --- |
| Stars | 165★ · 🟩 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM (server) + 📜 JS (Vite frontend) |
| Deps | 16 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 2026-04-11 · 🟩🟩 |
| Age | ~4.5 months (Nov 2025) · 🟨 |
| README maturity | 🟩🟩 (batteries-included; full guide) |
| Idiomaticity | 🟥 (annotation routing, codegen) |

#### glimr
[repo](https://github.com/glimr-org/glimr)

Young, batteries-included full-stack framework (~5 weeks of history). Routing with annotations, compiled Loom templates, middleware, DB schema + migrations, Vite integration, hot reload. Built on Mist HTTP server. Zero open issues — but also no community track record yet. Ambitious scope for its age; worth watching, not yet battle-tested.

```gleam
import compiled/loom/welcome
import glimr/http/http.{type Response}
import glimr/response/response

/// @get "/welcome"
pub fn show() -> Response {
  welcome.render() |> response.string_tree(200)
}
```


### Server Frameworks

Routing, handlers, middleware. All are independent (not full-stack).

| Criterion | [wisp](https://github.com/gleam-wisp/wisp) [🥈](#leaderboard) | [dream](https://github.com/TrustBound/dream) | [glen](https://github.com/MystPi/glen) |
| --- | --- | --- | --- |
| Stars | 1.4k★ · 🟩🟩 | 39★ · 🟨 | 111★ · 🟩 |
| License | Apache-2.0 · 🟩 | MIT · 🟩 | MIT · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM | 📜 JavaScript |
| Deps | 12 | 10 | 7 |
| Gleam compat | `>= 0.50 and < 2.0` · 🟩 | `>= 0.44 and < 2.0` · 🟩 | `~> 0.34 or ~> 1.0` · 🟥 |
| Maintenance | 2026-03-28 · 🟩🟩 | 2026-03-17 · 🟩🟩 | 2025-06-30 · 🟨 |
| Age | ~2.7 years (Aug 2023) · 🟩 | ~5 months (Nov 2025) · 🟨 | ~2.2 years (Jan 2024) · 🟩 |
| README maturity | 🟩🟩 (practical, handler+middleware) | 🟩🟩 (guide-style, quickstart, examples) | 🟩 (tagline + repo) |
| Idiomaticity | 🟩 | 🟩 | 🟩 |
| | | | |
| **Features** | | | |
| Routing DSL | — (`case` on path segments) | ✅ (route/method/path) | — (`case` on path segments) |
| Middleware | ✅ | ✅ | ✅ |
| JSON | ✅ | ✅ | — |
| Static files | ✅ | — | ✅ |
| Logging | ✅ | — | — |
| WebSocket | — | ✅ | ✅ |
| Sessions / cookies | ✅ | — | — |
| Form parsing | ✅ | — | — |
| Testing helpers | ✅ (`wisp/simulate`) | — | — |

#### wisp
[repo](https://github.com/gleam-wisp/wisp) · [🥈](#leaderboard)

"A practical web framework for Gleam" (backend). Mature (1.4k stars), handler + context pattern. Built-in logging, static files. Production-ready.

```gleam
import wisp.{type Request, type Response}

pub type Context {
  Context(secret: String)
}

pub fn handle_request(request: Request, context: Context) -> Response {
  wisp.ok()
}
```

#### dream
[repo](https://github.com/TrustBound/dream)

"Clean, composable web development for Gleam. No magic." Young (~5 months), batteries-included server framework built on mist. Router, middleware, JSON support, structured logging. 39 stars, 3 open issues. Positions itself as an alternative to wisp with more built-in conveniences.

```gleam
import dream/server
import dream/router.{Get, route}

pub fn main() {
  router()
  |> route(method: Get, path: "/", controller: index, middleware: [])
  |> server.new()
  |> server.bind("localhost")
  |> server.listen(3000)
}
```

#### glen
[repo](https://github.com/MystPi/glen)

"A peaceful web framework for Gleam that targets JS." Lightweight, promise-based, runs on Deno/Node/Cloudflare Workers.

```gleam
import gleam/javascript/promise.{type Promise}
import glen
import glen/status

pub fn main() {
  glen.serve(8000, handle_req)
}

fn handle_req(req: glen.Request) -> Promise(glen.Response) {
  "<h1>Welcome to my webpage!</h1>"
  |> glen.html(status.ok)
  |> promise.resolve
}
```


### Static Site Generators

Build-time content pipelines that output static HTML. No runtime server needed.

| Criterion | [arctic](https://github.com/RyanBrewer317/arctic) |
| --- | --- |
| Stars | 26★ · 🟨 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM (build-time) |
| Deps | 10 |
| Gleam compat | `>= 0.40 and < 2.0` · 🟩 |
| Maintenance | 2025-07-16 · 🟨 |
| Age | ~1.7 years (Aug 2024) · 🟩 |
| README maturity | 🟩 (conceptual guide, external quickstart link) |
| Idiomaticity | 🟩 |

#### arctic
[repo](https://github.com/RyanBrewer317/arctic)

Content-driven static site generator built on lustre and lustre_ssg. Define collections (e.g. blog posts) with custom parsers, renderers, and index pages. Outputs static HTML — designed for fast response times and serverless/lightweight backends. 26 stars, 1 open issue, 9 months since last commit.

```gleam
let posts = collection.new("posts")
  |> collection.with_parser(post_parser)
  |> collection.with_index(app.post_index)
  |> collection.with_renderer(app.post_renderer)

let config = config.new()
  |> config.home_renderer(app.render_homepage)
  |> config.add_collection(posts)

build.build(config)
```


### Dev Tools

Hot-reload, dev servers, bundling. Used during development, not shipped to production.

| Criterion | [lustre_dev_tools](https://github.com/lustre-labs/dev-tools) [🥉](#leaderboard) | [gleam-radiate](https://github.com/pta2002/gleam-radiate) |
| --- | --- | --- |
| Stars | 112★ · 🟩 | 66★ · 🟨 |
| License | MIT · 🟩 | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM |
| Deps | 20 | 5 |
| Gleam compat | `>= 0.60 and < 2.0` · 🟩 | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 2026-04-02 · 🟩🟩 | 2025-09-18 · 🟨 |
| Age | ~2 years (Mar 2024) · 🟩 | ~2.5 years (Oct 2023) · 🟩 |
| README maturity | 🟩 (features list, install guide, no code samples) | 🟩 (tagline) |
| Idiomaticity | 🟩 | 🟩 |

#### lustre_dev_tools
[repo](https://github.com/lustre-labs/dev-tools) · [🥉](#leaderboard)

Lustre's CLI and development tooling. Zero-config dev server, bundling, TailwindCSS v4 auto-detection. Uses bun for bundling/file watching. CLI-driven (no Gleam API). Pairs with lustre frontend framework.

```sh
# Install as dev dependency
gleam add lustre_dev_tools --dev

# Start dev server with live reload
gleam run -m lustre/dev start
```

#### gleam-radiate
[repo](https://github.com/pta2002/gleam-radiate)

Watches source files and reloads BEAM modules. Lightweight hot-reload. 5 deps, small footprint.

```gleam
let _ =
  radiate.new()
  |> radiate.add_dir("src")
  |> radiate.start()
```


### RPC Layer

Typed remote procedure calls between server and client. Replaces REST boilerplate with codegen.

| Criterion | [libero](https://github.com/pairshaped/libero) |
| --- | --- |
| Stars | 3★ · 🟥 |
| License | MIT · 🟩 |
| Target | ☎️📜 Both (BEAM server + JS client) |
| Deps | 6 |
| Gleam compat | `>= 0.69 and < 1.0` · 🟩 |
| Maintenance | 2026-04-13 · 🟩🟩 |
| Age | 2 days (Apr 2026) · 🟥 |
| README maturity | 🟩🟩 (full guide, annotated examples, error docs, CLI reference) |
| Idiomaticity | 🟥 (`/// @rpc` annotations, codegen) |

#### libero
[repo](https://github.com/pairshaped/libero)

Young (2 days old at snapshot), typed RPC layer for Lustre + any Gleam server. Annotate server functions with `/// @rpc`, run the code generator, and call them from the client as if local. WebSocket transport, compile-time type safety across the wire, panic recovery with trace IDs. Works with any server (wisp, mist, glimr). Impressive scope and docs for its age — zero community track record yet.

```gleam
// Server: annotate a function
/// @rpc
pub fn greet(name: String) -> String {
  "Hello, " <> name <> "!"
}

// Client: generated stub, call it like a local function
libero.call(rpc.greet("World"), fn(result) {
  case result {
    Ok(greeting) -> io.println(greeting)
    Error(_) -> io.println("RPC failed")
  }
})
```


### Frontend Frameworks

Client-side / UI layer. Can be integrated with any backend (including glimr).

| Criterion | [lustre](https://github.com/lustre-labs/lustre) [🥇](#leaderboard) | [redraw](https://github.com/ghivert/redraw) |
| --- | --- | --- |
| Stars | 2.2k★ · 🟩🟩 | 62★ · 🟨 |
| License | MIT · 🟩 | MIT · 🟩 |
| Target | ☎️📜 Both (BEAM + JS) | 📜 JavaScript |
| Deps | 5 | 2 |
| Gleam compat | `>= 0.60 and < 2.0` · 🟩 | `>= 0.60 and < 2.0` · 🟩 |
| Maintenance | 2026-03-22 · 🟩🟩 | 2026-01-20 · 🟩 |
| Age | ~4.2 years (Feb 2022) · 🟩🟩 | ~1.7 years (Jul 2024) · 🟩 |
| README maturity | 🟩🟩 (full guide) | 🟩🟩 (guide-style, examples, hooks, contexts) |
| Idiomaticity | 🟩 | 🟩 |

#### lustre
[repo](https://github.com/lustre-labs/lustre) · [🥇](#leaderboard)

Declarative HTML, Elm-style state (`init`/`update`/`view`), universal components, SSR support, CLI tooling. Most popular Gleam frontend framework (2.2k stars).

```gleam
import lustre
import lustre/element/html
import lustre/event

pub fn main() {
  let app = lustre.simple(init, update, view)
  let assert Ok(_) = lustre.start(app, "#app", Nil)
}

fn view(model) {
  html.div([], [
    html.button([event.on_click(Decr)], [html.text("-")]),
    html.p([], [html.text(int.to_string(model))]),
    html.button([event.on_click(Incr)], [html.text("+")]),
  ])
}
```

#### redraw
[repo](https://github.com/ghivert/redraw)

React 19 bindings for Gleam. Components, props, hooks, contexts, with full Gleam type-checking. JS target only.

```gleam
import redraw
import redraw/dom/attribute
import redraw/dom/html

pub fn gleam_is_awesome() {
  use Nil <- redraw.component_("GleamIsAwesome")
  html.div([attribute.class("oh-yeah")], [
    html.text("Yeah, for sure")
  ])
}
```


### Core HTTP Types

Shared type definitions used by all server and client adapters in the ecosystem.

| Criterion | [http](https://github.com/gleam-lang/http) [🥉](#leaderboard) |
| --- | --- |
| Stars | 276★ · 🟩🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️📜 Both (BEAM + JS) |
| Deps | 1 |
| Gleam compat | `>= 0.45 and < 2.0` · 🟩 |
| Maintenance | 2025-10-02 · 🟨 |
| Age | ~6.8 years (Jun 2019) · 🟩🟩 |
| README maturity | 🟩 (adapter tables + ecosystem) |
| Idiomaticity | 🟩 |

#### http
[repo](https://github.com/gleam-lang/http) · [🥉](#leaderboard)

Types and functions for HTTP clients and servers. Links server adapters (Mist, Cowboy) and client adapters (Plug, fetch, etc.). Central to ecosystem — almost every web library depends on this.

```gleam
// Core types provided by this package:
pub type Request(body)    // HTTP request with typed body
pub type Response(body)   // HTTP response with typed body
pub type Header           // Header key-value pair
pub type Method           // Get, Post, Put, Delete, etc.
pub type Scheme           // Http, Https
```


### HTTP Servers

Full HTTP server implementations. Use with frameworks or custom routing.

| Criterion | [mist](https://github.com/rawhat/mist) [🥇](#leaderboard) | [ewe](https://github.com/vshakitskiy/ewe) [🥉](#leaderboard) |
| --- | --- | --- |
| Stars | 489★ · 🟩🟩 | 106★ · 🟩 |
| License | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM |
| Deps | 9 | 9 |
| Gleam compat | `>= 0.50 and < 1.0` · 🟩 | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 2026-04-01 · 🟩🟩 | 2026-04-01 · 🟩🟩 |
| Age | ~4 years (Apr 2022) · 🟩🟩 | ~8 months (Aug 2025) · 🟨 |
| README maturity | 🟩🟩 (builder, WebSocket, streaming) | 🟩🟩 (guide, TLS, routing examples) |
| Idiomaticity | 🟩 | 🟩 |
| | | |
| **Features** | | |
| HTTP/1.1 | ✅ | ✅ |
| WebSocket | ✅ | ✅ |
| TLS / HTTPS | ✅ (via glisten) | ✅ |
| Streaming responses | ✅ (chunked) | ✅ (chunked) |
| Server-Sent Events | — | ✅ |
| File serving | ✅ (`send_file`) | — |
| Compression | — | — |
| HTTP/2 | — | — |

#### mist
[repo](https://github.com/rawhat/mist) · [🥇](#leaderboard)

"A glistening Gleam web server." Builder pattern, WebSocket support, streaming. Powers glimr and wisp. Active development (latest Apr 2026). Note: commit history on `master` branch, not `main`.

```gleam
import gleam/http/response
import gleam/bytes_tree
import mist

pub fn main() {
  fn(req) {
    response.new(200)
    |> response.set_body(mist.Bytes(bytes_tree.from_string("Hello, Mist!")))
  }
  |> mist.new
  |> mist.port(4000)
  |> mist.start
}
```

#### ewe
[repo](https://github.com/vshakitskiy/ewe)

"A fluffy Gleam web server." Independent HTTP server built on glisten (same foundation as mist). TLS support, WebSocket, compression, routing by path segments. 106 stars, single-author project, actively maintained. Younger and smaller community than mist but growing.

```gleam
import ewe

fn handler(req) {
  ewe.response(200, "Hello from ewe!")
}

pub fn main() {
  ewe.new(handler)
  |> ewe.bind("0.0.0.0")
  |> ewe.listening(port: 8080)
  |> ewe.start
}
```

### Network Infrastructure

TCP/TLS layer and legacy adapter. Not HTTP servers themselves — foundational pieces the servers above are built on.

| Criterion | [glisten](https://github.com/rawhat/glisten) [🥉](#leaderboard) | [cowboy](https://github.com/gleam-lang/cowboy) |
| --- | --- | --- |
| Stars | 197★ · 🟩 | 75★ · 🟨 |
| License | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM |
| Deps | 5 | 5 |
| Gleam compat | `>= 0.59 and < 1.0` · 🟩 | `>= 0.45 and < 2.0` · 🟩 |
| Maintenance | 2026-01-11 · 🟩 | 2025-11-01 · 🟨 |
| Age | ~4 years (Apr 2022) · 🟩🟩 | ~5.7 years (Aug 2020) · 🟩🟩 |
| README maturity | 🟩 (TCP/TLS server) | 🟩 (adapter pattern) |
| Idiomaticity | 🟩 | 🟩 |

#### glisten
[repo](https://github.com/rawhat/glisten) · [🥉](#leaderboard)

Pure Gleam TCP/TLS server with supervised socket acceptors. Not a web framework — this is the networking infrastructure that mist is built on. Included here because it's a key dependency in the stack, not because you'd use it directly for web apps.

```gleam
import glisten/socket/options.{ActiveMode, Passive}
import glisten/tcp

pub fn main() {
  let assert Ok(listener) = tcp.listen(8000, [ActiveMode(Passive)])
  let assert Ok(socket) = tcp.accept(listener)
  let assert Ok(msg) = tcp.receive(socket, 0)
  echo #("got a msg", msg)
}
```

#### cowboy
[repo](https://github.com/gleam-lang/cowboy)

Thin adapter for [Cowboy](https://github.com/ninenines/cowboy), the battle-tested Erlang HTTP server (~12+ years old, powers Phoenix/Plug). Translates between Gleam's `gleam/http` types and Cowboy's Erlang interface. Most new Gleam projects use mist instead — this adapter is for integrating Gleam handlers into an existing Erlang/Elixir codebase already running Cowboy.

```gleam
import gleam/bytes_tree
import gleam/http/response
import gleam/erlang/process

pub fn my_service(request) {
  let body = bytes_tree.from_string("Hello, world!")
  response.new(200)
  |> response.prepend_header("made-with", "Gleam")
  |> response.set_body(body)
}

pub fn main() {
  cowboy.start(my_service, on_port: 3000)
  process.sleep_forever()
}
```


## Leaderboard

This one is for fun, don't take it too seriously!
Everyone's a winner! 🎉
Better luck next time.


| Position | Award | Repo | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | · [lustre-labs/lustre](https://github.com/lustre-labs/lustre)<br>· [rawhat/mist](https://github.com/rawhat/mist) | 🟩🟩<br>🟩🟩 | 🟩<br>🟩 | 🟩<br>🟩 | 🟩🟩<br>🟩🟩 | 🟩🟩<br>🟩🟩 | 🟩🟩<br>🟩🟩 | 🟩<br>🟩 | **11** |
| 2 | 🥈 | [gleam-wisp/wisp](https://github.com/gleam-wisp/wisp) | 🟩🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩🟩 | 🟩 | **10** |
| 3 | 🥉 | · [gleam-lang/http](https://github.com/gleam-lang/http)<br>· [lustre-labs/dev-tools](https://github.com/lustre-labs/dev-tools)<br>· [rawhat/glisten](https://github.com/rawhat/glisten)<br>· [vshakitskiy/ewe](https://github.com/vshakitskiy/ewe) | 🟩🟩<br>🟩<br>🟩<br>🟩 | 🟩<br>🟩<br>🟩<br>🟩 | 🟩<br>🟩<br>🟩<br>🟩 | 🟨<br>🟩🟩<br>🟩<br>🟩🟩 | 🟩🟩<br>🟩<br>🟩🟩<br>🟨 | 🟩<br>🟩<br>🟩<br>🟩🟩 | 🟩<br>🟩<br>🟩<br>🟩 | **8** |
| 4 | | · [ghivert/redraw](https://github.com/ghivert/redraw)<br>· [TrustBound/dream](https://github.com/TrustBound/dream) | 🟨<br>🟨 | 🟩<br>🟩 | 🟩<br>🟩 | 🟩<br>🟩🟩 | 🟩<br>🟨 | 🟩🟩<br>🟩🟩 | 🟩<br>🟩 | **7** |
| 5 | | · [glimr-org/glimr](https://github.com/glimr-org/glimr)<br>· [gleam-lang/cowboy](https://github.com/gleam-lang/cowboy) | 🟩<br>🟨 | 🟩<br>🟩 | 🟩<br>🟩 | 🟩🟩<br>🟨 | 🟨<br>🟩🟩 | 🟩🟩<br>🟩 | 🟥<br>🟩 | **6** |
| 6 | | · [pta2002/gleam-radiate](https://github.com/pta2002/gleam-radiate)<br>· [RyanBrewer317/arctic](https://github.com/RyanBrewer317/arctic) | 🟨<br>🟨 | 🟩<br>🟩 | 🟩<br>🟩 | 🟨<br>🟨 | 🟩<br>🟩 | 🟩<br>🟩 | 🟩<br>🟩 | **5** |
| 7 | | [MystPi/glen](https://github.com/MystPi/glen) | 🟩 | 🟩 | 🟥 | 🟨 | 🟩 | 🟩 | 🟩 | **4** |
| 8 | | [pairshaped/libero](https://github.com/pairshaped/libero) | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟥 | **3** |

**🥇 Gold** — The two load-bearing pillars of the web ecosystem.
- **lustre** ([hayleigh-dot-dev](https://github.com/hayleigh-dot-dev) 703 commits, [yoshi-monster](https://github.com/yoshi-monster) 79) — Defines how Gleam UIs are built.
- **mist** ([rawhat](https://github.com/rawhat) 363 commits) — The HTTP server under nearly every backend framework.

**🥈 Silver** — The default choice for production backends.
- **wisp** ([lpil](https://github.com/lpil) 278 commits, [renatillas](https://github.com/renatillas) 16) — Bridges the gap between mist's raw server and application-level concerns like routing and middleware.

**🥉 Bronze** — Foundational infrastructure and tooling.
- **http** ([lpil](https://github.com/lpil) 158 commits, [CrowdHailer](https://github.com/CrowdHailer) 29, [giacomocavalieri](https://github.com/giacomocavalieri) 28) — Shared request/response types that glue the entire ecosystem together.
- **lustre_dev_tools** ([hayleigh-dot-dev](https://github.com/hayleigh-dot-dev) 215 commits, [giacomocavalieri](https://github.com/giacomocavalieri) 13, [yoshi-monster](https://github.com/yoshi-monster) 10) — Enables the standard dev workflow.
- **glisten** ([rawhat](https://github.com/rawhat) 217 commits) — TCP/TLS foundation that mist is built on.
- **ewe** ([vshakitskiy](https://github.com/vshakitskiy) 185 commits) — Independent HTTP server alternative to mist, built on glisten.

