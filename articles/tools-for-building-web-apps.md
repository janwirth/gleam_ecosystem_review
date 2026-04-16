# Tools for building web applications with Gleam

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Evaluation Criteria](#evaluation-criteria)
   - [Repos Reviewed](#repos-reviewed)
   - [Disregarded Repos](#disregarded-repos)
3. [Categories](#categories)
   - [Full-Stack Frameworks](#full-stack-frameworks) — [glimr](#glimr)
   - [Server Frameworks](#server-frameworks) — [wisp](#wisp) · [glen](#glen)
   - [Dev Tools](#dev-tools) — [lustre_dev_tools](#lustre_dev_tools) · [gleam-radiate](#gleam-radiate)
   - [RPC Layer](#rpc-layer) — [libero](#libero)
   - [Frontend Frameworks](#frontend-frameworks) — [lustre](#lustre) · [redraw](#redraw)
   - [Core HTTP Types](#core-http-types) — [http](#http)
   - [HTTP Servers & Adapters](#http-servers--adapters) — [mist](#mist) · [cowboy](#cowboy) · [glisten](#glisten)

## Summary



Gleam's ecosystem is growing fast, and keeping track of what's available isn't easy.
I put this together so you don't have to spend hours on research, or worse, build something that already exists.
Gleam's ecosystem for building web applications already covers most of what you need to build on the web - while the ☎️ BEAM target is ahead of 📜 JS.
Here's what's out there.


| Category | ☎️ BEAM | 📜 JS |
| --- | --- | --- |
| **[Full-Stack Frameworks](#full-stack-frameworks)** | · 🥉 [glimr](#glimr) ([repo](https://github.com/glimr-org/glimr), 165★) — *young; routing, templates, DB schema + migrations, auth, hot reload* | — |
| **[Server Frameworks](#server-frameworks)** | · 🥈 [wisp](#wisp) ([repo](https://github.com/gleam-wisp/wisp), 1.4k★) — *handler+middleware, production-ready* | · [glen](#glen) ([repo](https://github.com/MystPi/glen), 111★) — *lightweight, promise-based, Deno/Node/Workers* |
| **[Frontend Frameworks](#frontend-frameworks)** (Elm-style) | · 🥇 [lustre](#lustre) ([repo](https://github.com/lustre-labs/lustre), 2.2k★) — *declarative UI, SSR, universal* | · 🥇 [lustre](#lustre) ([repo](https://github.com/lustre-labs/lustre), 2.2k★) — *also runs on JS target* |
| **[Frontend Frameworks](#frontend-frameworks)** (React) | — | · [redraw](#redraw) ([repo](https://github.com/ghivert/redraw), 62★) — *full React 19 bindings, type-checked* |
| **[RPC Layer](#rpc-layer)** | · [libero](#libero) ([repo](https://github.com/pairshaped/libero), 3★) — *young; typed RPC for Lustre+server, WebSocket, codegen* | · [libero](#libero) — *also generates JS client stubs* |
| **[Dev Tools](#dev-tools)** | · 🥉 [lustre_dev_tools](#lustre_dev_tools) ([repo](https://github.com/lustre-labs/dev-tools), 112★) — *dev server, bundling, Tailwind*<br>· [gleam-radiate](#gleam-radiate) ([repo](https://github.com/pta2002/gleam-radiate), 66★) — *BEAM module reload* | — |
| **[HTTP Servers & Adapters](#http-servers--adapters)** | · 🥇 [mist](#mist) ([repo](https://github.com/rawhat/mist), 489★) — *HTTP server, WebSocket, streaming*<br>· [cowboy](#cowboy) ([repo](https://github.com/gleam-lang/cowboy), 75★) — *adapter for existing Erlang/Elixir Cowboy setups*<br>· [glisten](#glisten) ([repo](https://github.com/rawhat/glisten), 197★) — *TCP/TLS infra, powers mist* | — |
| **[Core HTTP Types](#core-http-types)** | · 🥉 [http](#http) ([repo](https://github.com/gleam-lang/http), 276★) — *request/response types, used by nearly everything* | · 🥉 [http](#http) ([repo](https://github.com/gleam-lang/http), 276★) — *also targets JS* |

> <details>
> <summary><strong>Dependency Graph</strong></summary>
>
> Arrows read "uses."
>
> **Full-Stack Frameworks:**
> - [glimr](#glimr) → [mist](#mist) (HTTP server)
>
> **Server Frameworks:**
> - [wisp](#wisp) → [mist](#mist) (HTTP server)
> - [glen](#glen) → [http](#http) (core types) — *JS target, independent stack*
>
> **RPC Layer:**
> - [libero](#libero) → [lustre](#lustre) (client stubs) — works with any server
>
> **Frontend Frameworks:**
> - [lustre](#lustre), [redraw](#redraw) — standalone, integrate with any backend
>
> **Dev Tools:**
> - [lustre_dev_tools](#lustre_dev_tools) — pairs with [lustre](#lustre)
> - [gleam-radiate](#gleam-radiate) — framework-agnostic
>
> **HTTP Servers & Adapters:**
> - [mist](#mist) → [glisten](#glisten) (TCP/TLS) → [http](#http) (core types)
> - [cowboy](#cowboy) → [http](#http) (core types)
>
> **Core HTTP Types:**
> - [http](#http) — no upstream deps; depended on by nearly everything above
>
> </details>


## Research Method

<details>
<summary>Expand</summary>

**Snapshot 2026-04-13** — data from **public GitHub web pages and raw README URLs only** (no clone, no GitHub API).

**Legend:** 🟩🟩 strong · 🟩 OK · ⬜ unknown / not shown / not applicable · 🟥 negative signal.

### Evaluation Criteria

Each repo evaluated using public GitHub pages only (no API, no clone):

1. **Fetch repo homepage** — Extract star count, description
2. **Fork check** — Verify the repo is not a fork; if it is, use the upstream original instead
3. **Read README** — Assess maturity (guide-style / clear / minimal), examples, design intent
4. **Commit history** — Latest commit date (ISO), pagination/depth, visible history span, commit density
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
> - **Total work:** Volume of effort based on commit count, history depth, and whether `/commits` paginates. 🟩🟩 = paginated + dense history (substantial), 🟩 = solid history, 🟥 = sparse / few commits. *Example: "Paginated; active 2025–Apr 2026" → 🟩🟩.*
> - **Age:** Time from first visible commit to snapshot date. Older projects have more time to stabilize. 🟩🟩 = ≥3 years, 🟩 = ≥1 year, 🟨 = ≥3 months, 🟥 = <3 months. *Example: history starts Mar 2026, snapshot Apr 2026 → ~1 month → 🟥.*
> - **README maturity:** Quality of documentation in the repo README. 🟩🟩 = guide-style with examples, full feature docs, and getting-started instructions. 🟩 = clear description and basic usage. 🟥 = minimal, broken, or missing. *Example: batteries-included framework with full guide → 🟩🟩; tagline-only → 🟩.*
>
> **Why maintenance and issues are separate:** A repo can be actively committed to but have a growing issue backlog (feature requests, unfixed bugs), or have zero issues but be dormant. Conflating them hides useful signal. Check both rows.
>
> **Leaderboard scoring:** 🟥 = −1, 🟨 = 0, 🟩 = 1, 🟩🟩 = 2. Sum of all 7 dimensions. Max possible = 14.
>
> </details>

### Repos Reviewed

Discovered via [Gleam packages registry](https://packages.gleam.run/) searches: [server](https://packages.gleam.run/?search=server), [backend](https://packages.gleam.run/?search=backend), [frontend](https://packages.gleam.run/?search=frontend), [react](https://packages.gleam.run/?search=react). Relevant results picked from each; duplicates and non-web packages ignored. Listed by category.

**Full-Stack Frameworks:**
- **[glimr-org/glimr](https://github.com/glimr-org/glimr)** — Batteries-included: routing, templates, middleware, DB schema + migrations, caching, auth, hot reload. Built on Mist.

**Server Frameworks:**
- **[gleam-wisp/wisp](https://github.com/gleam-wisp/wisp)** — Practical web framework. Handler + middleware pattern, pragmatic, production-ready.
- **[MystPi/glen](https://github.com/MystPi/glen)** — Lightweight web framework. "A peaceful web framework for Gleam that targets JS."

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
> </details>

</details>


## Categories

### Full-Stack Frameworks

All-in-one solutions: routing, templates, middleware, auth, database, development tools.

| Criterion | glimr |
| --- | --- |
| Stars | 165★ · 🟩 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM (server) + 📜 JS (Vite frontend) |
| Deps | 16 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 2026-04-11 · 🟩🟩 |
| Total work | Paginated `/commits` (35+ per page); history Mar 5 to Apr 11 · 🟩🟩 |
| Age | ~4.5 months (Nov 2025) · 🟨 |
| README maturity | 🟩🟩 (batteries-included; full guide) |

#### 🥉 glimr · [repo](https://github.com/glimr-org/glimr)

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

| Criterion | wisp | glen |
| --- | --- | --- |
| Stars | 1.4k★ · 🟩🟩 | 111★ · 🟩 |
| License | Apache-2.0 · 🟩 | MIT · 🟩 |
| Target | ☎️ BEAM | 📜 JavaScript |
| Deps | 12 | 7 |
| Gleam compat | `>= 0.50 and < 2.0` · 🟩 | `~> 0.34 or ~> 1.0` · 🟥 |
| Maintenance | 2026-03-28 · 🟩🟩 | 2025-06-30 · 🟨 |
| Total work | Paginated (35+); Oct 2025–Mar 2026 · 🟩 | Paginated; Feb 2024 back · 🟩 |
| Age | ~2.7 years (Aug 2023) · 🟩 | ~2.2 years (Jan 2024) · 🟩 |
| README maturity | 🟩🟩 (practical, handler+middleware) | 🟩 (tagline + repo) |

#### 🥈 wisp · [repo](https://github.com/gleam-wisp/wisp)

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

#### glen · [repo](https://github.com/MystPi/glen)

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


### Dev Tools

Hot-reload, dev servers, bundling. Used during development, not shipped to production.

| Criterion | lustre_dev_tools | gleam-radiate |
| --- | --- | --- |
| Stars | 112★ · 🟩 | 66★ · 🟨 |
| License | MIT · 🟩 | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM |
| Deps | 20 | 5 |
| Gleam compat | `>= 0.60 and < 2.0` · 🟩 | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 2026-04-02 · 🟩🟩 | 2025-09-18 · 🟨 |
| Total work | Paginated; active 2025–Apr 2026 · 🟩🟩 | Oct 2023–2025 · 🟩 |
| Age | ~2 years (Mar 2024) · 🟩 | ~2.5 years (Oct 2023) · 🟩 |
| README maturity | 🟩 (features list, install guide, no code samples) | 🟩 (tagline) |

#### 🥉 lustre_dev_tools · [repo](https://github.com/lustre-labs/dev-tools)

Lustre's CLI and development tooling. Zero-config dev server, bundling, TailwindCSS v4 auto-detection. Uses bun for bundling/file watching. CLI-driven (no Gleam API). Pairs with lustre frontend framework.

```sh
# Install as dev dependency
gleam add lustre_dev_tools --dev

# Start dev server with live reload
gleam run -m lustre/dev start
```

#### gleam-radiate · [repo](https://github.com/pta2002/gleam-radiate)

Watches source files and reloads BEAM modules. Lightweight hot-reload. 5 deps, small footprint.

```gleam
let _ =
  radiate.new()
  |> radiate.add_dir("src")
  |> radiate.start()
```


### RPC Layer

Typed remote procedure calls between server and client. Replaces REST boilerplate with codegen.

| Criterion | libero |
| --- | --- |
| Stars | 3★ · 🟥 |
| License | MIT · 🟩 |
| Target | ☎️📜 Both (BEAM server + JS client) |
| Deps | 6 |
| Gleam compat | `>= 0.69 and < 1.0` · 🟩 |
| Maintenance | 2026-04-13 · 🟩🟩 |
| Total work | 62 commits in 3 days; paginated · 🟩 |
| Age | 2 days (Apr 2026) · 🟥 |
| README maturity | 🟩🟩 (full guide, annotated examples, error docs, CLI reference) |

#### libero · [repo](https://github.com/pairshaped/libero)

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

| Criterion | lustre | redraw |
| --- | --- | --- |
| Stars | 2.2k★ · 🟩🟩 | 62★ · 🟨 |
| License | MIT · 🟩 | MIT · 🟩 |
| Target | ☎️📜 Both (BEAM + JS) | 📜 JavaScript |
| Deps | 5 | 2 |
| Gleam compat | `>= 0.60 and < 2.0` · 🟩 | `>= 0.60 and < 2.0` · 🟩 |
| Maintenance | 2026-03-22 · 🟩🟩 | 2026-01-20 · 🟩 |
| Total work | Paginated `/commits` (34+ per page); history to Jan 2026 · 🟩🟩 | Paginated; 89 commits; Aug 2025–Jan 2026 visible · 🟩 |
| Age | ~4.2 years (Feb 2022) · 🟩🟩 | ~1.7 years (Jul 2024) · 🟩 |
| README maturity | 🟩🟩 (full guide) | 🟩🟩 (guide-style, examples, hooks, contexts) |

#### 🥇 lustre · [repo](https://github.com/lustre-labs/lustre)

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

#### redraw · [repo](https://github.com/ghivert/redraw)

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

| Criterion | http |
| --- | --- |
| Stars | 276★ · 🟩🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️📜 Both (BEAM + JS) |
| Deps | 1 |
| Gleam compat | `>= 0.45 and < 2.0` · 🟩 |
| Maintenance | 2025-10-02 · 🟨 |
| Total work | Paginated; dense 2025 · 🟩🟩 |
| Age | ~6.8 years (Jun 2019) · 🟩🟩 |
| README maturity | 🟩 (adapter tables + ecosystem) |

#### 🥉 http · [repo](https://github.com/gleam-lang/http)

Types and functions for HTTP clients and servers. Links server adapters (Mist, Cowboy) and client adapters (Plug, fetch, etc.). Central to ecosystem — almost every web library depends on this.

```gleam
// Core types provided by this package:
pub type Request(body)    // HTTP request with typed body
pub type Response(body)   // HTTP response with typed body
pub type Header           // Header key-value pair
pub type Method           // Get, Post, Put, Delete, etc.
pub type Scheme           // Http, Https
```


### HTTP Servers & Adapters

Low-level server implementations and adapter layers. Use with frameworks or custom routing.

| Criterion | mist | cowboy | glisten |
| --- | --- | --- | --- |
| Stars | 489★ · 🟩🟩 | 75★ · 🟨 | 197★ · 🟩 |
| License | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM | ☎️ BEAM | ☎️ BEAM |
| Deps | 9 | 5 | 5 |
| Gleam compat | `>= 0.50 and < 1.0` · 🟩 | `>= 0.45 and < 2.0` · 🟩 | `>= 0.59 and < 1.0` · 🟩 |
| Maintenance | 2026-04-01 · 🟩🟩 | 2025-11-01 · 🟨 | 2026-01-11 · 🟩 |
| Total work | Paginated (master); active 2025–Apr 2026 · 🟩🟩 | Multi-year history · 🟩 | 231 commits; Jun 2025–Jan 2026 visible · 🟩 |
| Age | ~4 years (Apr 2022) · 🟩🟩 | ~5.7 years (Aug 2020) · 🟩🟩 | ~4 years (Apr 2022) · 🟩🟩 |
| README maturity | 🟩🟩 (builder, WebSocket, streaming) | 🟩 (adapter pattern) | 🟩 (TCP/TLS server) |

#### 🥇 mist · [repo](https://github.com/rawhat/mist)

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

#### cowboy · [repo](https://github.com/gleam-lang/cowboy)

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

#### glisten · [repo](https://github.com/rawhat/glisten)

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


## Leaderboard

This one is for fun, don't take it too seriously!
Everyone's a winner! 🎉
Better luck next time.


| Position | Award | Repo | ★ | Lic | Compat | Maint | Work | Age | README | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | · [lustre-labs/lustre](https://github.com/lustre-labs/lustre)<br>· [rawhat/mist](https://github.com/rawhat/mist) | 🟩🟩<br>🟩🟩 | 🟩<br>🟩 | 🟩<br>🟩 | 🟩🟩<br>🟩🟩 | 🟩🟩<br>🟩🟩 | 🟩🟩<br>🟩🟩 | 🟩🟩<br>🟩🟩 | **12** |
| 2 | 🥈 | [gleam-wisp/wisp](https://github.com/gleam-wisp/wisp) | 🟩🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩 | 🟩🟩 | **10** |
| 3 | 🥉 | · [glimr-org/glimr](https://github.com/glimr-org/glimr)<br>· [gleam-lang/http](https://github.com/gleam-lang/http)<br>· [lustre-labs/dev-tools](https://github.com/lustre-labs/dev-tools) | 🟩<br>🟩🟩<br>🟩 | 🟩<br>🟩<br>🟩 | 🟩<br>🟩<br>🟩 | 🟩🟩<br>🟨<br>🟩🟩 | 🟩🟩<br>🟩🟩<br>🟩🟩 | 🟨<br>🟩🟩<br>🟩 | 🟩🟩<br>🟩<br>🟩 | **9** |
| 4 | | [rawhat/glisten](https://github.com/rawhat/glisten) | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **8** |
| 5 | | [ghivert/redraw](https://github.com/ghivert/redraw) | 🟨 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | **7** |
| 6 | | [gleam-lang/cowboy](https://github.com/gleam-lang/cowboy) | 🟨 | 🟩 | 🟩 | 🟨 | 🟩 | 🟩🟩 | 🟩 | **6** |
| 7 | | · [pta2002/gleam-radiate](https://github.com/pta2002/gleam-radiate)<br>· [pairshaped/libero](https://github.com/pairshaped/libero) | 🟨<br>🟥 | 🟩<br>🟩 | 🟩<br>🟩 | 🟨<br>🟩🟩 | 🟩<br>🟩 | 🟩<br>🟥 | 🟩<br>🟩🟩 | **5** |
| 8 | | [MystPi/glen](https://github.com/MystPi/glen) | 🟩 | 🟩 | 🟥 | 🟨 | 🟩 | 🟩 | 🟩 | **4** |

**🥇 Gold** — Lustre ([hayleigh-dot-dev](https://github.com/hayleigh-dot-dev) 703 commits, [yoshi-monster](https://github.com/yoshi-monster) 79) defines how Gleam UIs are built and mist ([rawhat](https://github.com/rawhat) 363 commits) is the HTTP server under nearly every backend framework, making them the two load-bearing pillars of the web ecosystem.

**🥈 Silver** — Wisp ([lpil](https://github.com/lpil) 278 commits, [renatillas](https://github.com/renatillas) 16) is the default choice for production backends, bridging the gap between mist's raw server and application-level concerns like routing and middleware.

**🥉 Bronze** — http ([lpil](https://github.com/lpil) 158 commits, [CrowdHailer](https://github.com/CrowdHailer) 29, [giacomocavalieri](https://github.com/giacomocavalieri) 28) provides the shared request/response types that glue the entire ecosystem together, lustre_dev_tools ([hayleigh-dot-dev](https://github.com/hayleigh-dot-dev) 215 commits, [giacomocavalieri](https://github.com/giacomocavalieri) 13, [yoshi-monster](https://github.com/yoshi-monster) 10) enables the standard dev workflow, and glimr ([miguejarias](https://github.com/miguejarias) 125 commits) is the first attempt at a Rails-style "just build" experience for Gleam.

