# Tools for building web applications with Gleam

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Evaluation Criteria](#evaluation-criteria)
   - [Repos Reviewed](#repos-reviewed)
   - [Disregarded Repos](#disregarded-repos)
3. [Full-Stack Frameworks](#full-stack-frameworks) — [glimr](#glimr)
4. [Server Frameworks](#server-frameworks) — [wisp](#wisp) · [glen](#glen)
5. [Dev Tools](#dev-tools) — [lustre_dev_tools](#lustre_dev_tools) · [gleam-radiate](#gleam-radiate) · [olive](#olive)
6. [Frontend Frameworks](#frontend-frameworks) — [lustre](#lustre) · [redraw](#redraw)
7. [Core HTTP Types](#core-http-types) — [http](#http)
8. [HTTP Servers & Adapters](#http-servers--adapters) — [mist](#mist) · [cowboy](#cowboy) · [glisten](#glisten)

## Summary



Gleam's ecosystem is growing fast, and keeping track of what's available isn't easy.
I put this together so you don't have to spend hours on research, or worse, build something that already exists.
Gleam's ecosystem for building web applications already covers most of what you need to build on the web. Here's what's out there.


| Category | BEAM | JS |
| --- | --- | --- |
| **[Full-Stack Frameworks](#full-stack-frameworks)** | [glimr](#glimr) ([repo](https://github.com/glimr-org/glimr), 165★) — *routing, templates, DB schema + migrations, auth, hot reload* | — |
| **[Server Frameworks](#server-frameworks)** | [wisp](#wisp) ([repo](https://github.com/gleam-wisp/wisp), 1.4k★) — *handler+middleware, production-ready* | [glen](#glen) ([repo](https://github.com/MystPi/glen), 111★) — *lightweight, promise-based, Deno/Node/Workers* |
| **[Frontend Frameworks](#frontend-frameworks)** (Elm-style) | [lustre](#lustre) ([repo](https://github.com/lustre-labs/lustre), 2.2k★) — *declarative UI, SSR, universal* | [lustre](#lustre) ([repo](https://github.com/lustre-labs/lustre), 2.2k★) — *also runs on JS target* |
| **[Frontend Frameworks](#frontend-frameworks)** (React) | — | [redraw](#redraw) ([repo](https://github.com/ghivert/redraw), 62★) — *full React 19 bindings, type-checked* |
| **[Dev Tools](#dev-tools)** | [lustre_dev_tools](#lustre_dev_tools) ([repo](https://github.com/lustre-labs/dev-tools), 112★) — *dev server, bundling, Tailwind* / [gleam-radiate](#gleam-radiate) ([repo](https://github.com/pta2002/gleam-radiate), 66★) — *BEAM module reload* / [olive](#olive) ([repo](https://github.com/fravan/olive), 8★) — *process restart* | — |
| **[HTTP Servers & Adapters](#http-servers--adapters)** | [mist](#mist) ([repo](https://github.com/rawhat/mist), 489★) — *HTTP server, WebSocket, streaming* / [cowboy](#cowboy) ([repo](https://github.com/gleam-lang/cowboy), 75★) — *Erlang adapter* / [glisten](#glisten) ([repo](https://github.com/lpil/glisten), 197★) — *TCP/TLS primitives* | — |
| **[Core HTTP Types](#core-http-types)** | [http](#http) ([repo](https://github.com/gleam-lang/http), 276★) — *request/response types, used by nearly everything* | [http](#http) ([repo](https://github.com/gleam-lang/http), 276★) — *also targets JS* |

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
> **Frontend Frameworks:**
> - [lustre](#lustre), [redraw](#redraw) — standalone, integrate with any backend
>
> **Dev Tools:**
> - [lustre_dev_tools](#lustre_dev_tools) — pairs with [lustre](#lustre)
> - [olive](#olive), [gleam-radiate](#gleam-radiate) — framework-agnostic
>
> **HTTP Servers & Adapters:**
> - [mist](#mist) → [glisten](#glisten) (TCP/TLS) → [http](#http) (core types)
> - [cowboy](#cowboy) → [http](#http) (core types)
>
> **Core HTTP Types:**
> - [http](#http) — no upstream deps; depended on by nearly everything above
>
> </details>

---

## Research Method

<details>
<summary>Expand</summary>

**Snapshot 2026-04-13** — data from **public GitHub web pages and raw README URLs only** (no clone, no GitHub API).

**Legend:** 🟩🟩 strong · 🟩 OK · ⬜ unknown / not shown / not applicable · 🟥 negative signal.

### Evaluation Criteria

Each repo evaluated using public GitHub pages only (no API, no clone):

1. **Fetch repo homepage** — Extract star count, description
2. **Read README** — Assess maturity (guide-style / clear / minimal), examples, design intent
3. **Commit history** — Latest commit date (ISO), pagination/depth, visible history span, commit density
4. **Gleam.toml** — Check Gleam version requirement, dependencies
5. **Issues tab** — Count open issues, assess health

**Note on code samples:** All code examples are taken from project READMEs or docs, not independently verified or tested.

> <details>
> <summary><strong>Scoring dimensions</strong></summary>
>
> - **Maintenance:** Latest commit date + commit frequency **only**. Does not factor in issues. 🟩🟩 = actively developed (recent + frequent), 🟩 = steady, 🟥 = dormant
> - **Stars / Issues:** Combined row. Stars as raw count (x.xk if ≥1000). Issue health scored: 🟩🟩 = 0 issues, 🟩 = 1–5, 🟨 = 6–15 (watch), 🟥 = 16+ (weigh against activity), ⬜ = unknown/couldn't load
> - **License:** 🟩 = permissive OSS (MIT, Apache-2.0, BSD). 🟥 = viral (GPL, AGPL) or no license
> - **README maturity:** 🟩🟩 = guide-style + examples + full docs, 🟩 = clear, 🟥 = minimal/broken
> - **Work (effort):** Commit count, history depth, pagination. 🟩🟩 = substantial, 🟩 = solid, 🟥 = sparse
> - **Gleam compat:** Version constraint from `gleam.toml`. 🟩🟩 = compatible with latest (v1.15.4). "No constraint" = no minimum specified.
>
> **Why maintenance and issues are separate:** A repo can be actively committed to but have a growing issue backlog (feature requests, unfixed bugs), or have zero issues but be dormant. Conflating them hides useful signal. Check both rows.
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
- **[fravan/olive](https://github.com/fravan/olive)** — Hot-reload dev tool. Watches and restarts Gleam server processes.
- **[pta2002/gleam-radiate](https://github.com/pta2002/gleam-radiate)** — Hot-reload dev tool. Watches source files and reloads BEAM modules.

**Frontend Frameworks:**
- **[lustre-labs/lustre](https://github.com/lustre-labs/lustre)** — Declarative HTML, Elm-style state, universal components, SSR, CLI tooling.
- **[ghivert/redraw](https://github.com/ghivert/redraw)** — React bindings for Gleam. Full React 19 support, type-checked, JS target.

**Core HTTP Types:**
- **[gleam-lang/http](https://github.com/gleam-lang/http)** — HTTP types and functions. Core layer linking server/client adapters (Mist, Cowboy, Plug, fetch).

**HTTP Servers & Adapters:**
- **[rawhat/mist](https://github.com/rawhat/mist)** — HTTP server. Builder pattern, WebSocket, streaming, flexible routing. Powers glimr and wisp.
- **[gleam-lang/cowboy](https://github.com/gleam-lang/cowboy)** — HTTP adapter. Integration layer for Cowboy web server.
- **[lpil/glisten](https://github.com/lpil/glisten)** — TCP/TLS server. Pure Gleam, supervised socket acceptors, TLS support. Low-level building block.

### Disregarded Repos

> <details>
> <summary>Excluded from detailed review due to maintenance or scope issues</summary>
>
> **Unmaintained:**
> - **[gleam-lang/plug](https://github.com/gleam-lang/plug)** — HTTP adapter for Elixir Plug compatibility. Last commit 2020-08-27 (6+ years dormant). Activity 🟥.
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

---

## Full-Stack Frameworks

<details>
<summary>glimr</summary>

All-in-one solutions: routing, templates, middleware, auth, database, development tools.

| Criterion | glimr |
| --- | --- |
| Stars / Issues | 165★ · 0 issues 🟩🟩 |
| License | MIT · 🟩 |
| Target | BEAM (server) + JS (Vite frontend) |
| Deps | 16 |
| Gleam compat | No constraint specified · 🟩🟩 |
| Maintenance | 2026-04-11 · 🟩🟩 |
| Total work | Paginated `/commits` (35+ per page); history Mar 5 to Apr 11 · 🟩🟩 |
| README maturity | 🟩🟩 (batteries-included; full guide) |

### glimr

[repo](https://github.com/glimr-org/glimr) · Batteries-included full-stack framework. Routing with annotations, compiled Loom templates, middleware, DB schema + migrations, Vite integration, hot reload. Built on Mist HTTP server. Zero open issues — strong quality signal.

```gleam
import compiled/loom/welcome
import glimr/http/http.{type Response}
import glimr/response/response

/// @get "/welcome"
pub fn show() -> Response {
  welcome.render() |> response.string_tree(200)
}
```

</details>

---

## Server Frameworks

<details>
<summary>wisp · glen</summary>

Routing, handlers, middleware. All are independent (not full-stack).

| Criterion | wisp | glen |
| --- | --- | --- |
| Stars / Issues | 1.4k★ · 16 issues 🟥 | 111★ · 2 issues 🟩 |
| License | Apache-2.0 · 🟩 | MIT · 🟩 |
| Target | BEAM | JavaScript |
| Deps | 12 | 7 |
| Gleam compat | >= 1.11.0 · 🟩🟩 | No constraint · 🟩🟩 |
| Maintenance | 2026-03-28 · 🟩🟩 | 2025-06-30 · 🟩 |
| Total work | Paginated (35+); Oct 2025–Mar 2026 · 🟩 | Paginated; Feb 2024 back · 🟩 |
| README maturity | 🟩🟩 (practical, handler+middleware) | 🟩 (tagline + repo) |

### wisp

[repo](https://github.com/gleam-wisp/wisp) · "A practical web framework for Gleam" (backend). Mature (1.4k stars), handler + context pattern. Built-in logging, static files. Production-ready.

```gleam
import wisp.{type Request, type Response}

pub type Context {
  Context(secret: String)
}

pub fn handle_request(request: Request, context: Context) -> Response {
  wisp.ok()
}
```

### glen

[repo](https://github.com/MystPi/glen) · "A peaceful web framework for Gleam that targets JS." Lightweight, promise-based, runs on Deno/Node/Cloudflare Workers.

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

</details>

---

## Dev Tools

<details>
<summary>lustre_dev_tools · gleam-radiate · olive</summary>

Hot-reload, dev servers, bundling. Used during development, not shipped to production.

| Criterion | lustre_dev_tools | gleam-radiate | olive |
| --- | --- | --- | --- |
| Stars / Issues | 112★ · 19 issues 🟥 | 66★ · 3 issues 🟩 | 8★ · 1 issue 🟩 |
| License | MIT · 🟩 | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 |
| Target | BEAM | BEAM | BEAM |
| Deps | 20 | 5 | 14 |
| Gleam compat | No constraint · 🟩🟩 | No constraint · 🟩🟩 | No constraint · 🟩🟩 |
| Maintenance | 2026-04-02 · 🟩🟩 | 2025-09-18 · 🟩 | 2025-07-18 · 🟩 |
| Total work | Paginated; active 2025–Apr 2026 · 🟩🟩 | Oct 2023–2025 · 🟩 | Short (Feb–Jul 2025) · 🟩 |
| README maturity | 🟩 (features list, install guide, no code samples) | 🟩 (tagline) | 🟩 (clear + limits) |

### lustre_dev_tools

[repo](https://github.com/lustre-labs/dev-tools) · Lustre's CLI and development tooling. Zero-config dev server, bundling, TailwindCSS v4 auto-detection. Uses bun for bundling/file watching. CLI-driven (no Gleam API). Pairs with lustre frontend framework.

```sh
# Install as dev dependency
gleam add lustre_dev_tools --dev

# Start dev server with live reload
gleam run -m lustre/dev start
```

### gleam-radiate

[repo](https://github.com/pta2002/gleam-radiate) · Watches source files and reloads BEAM modules. Lightweight hot-reload. 5 deps, small footprint.

```gleam
let _ =
  radiate.new()
  |> radiate.add_dir("src")
  |> radiate.start()
```

### olive

[repo](https://github.com/fravan/olive) · Watches and restarts Gleam server processes. Similar to gleam-radiate but restarts whole process. 14 deps (pulls in wisp + mist).

```gleam
pub fn handle_request(_req: Request) -> Response {
  response.new(200)
  |> response.set_body(mist.Bytes(bytes_tree.from_string("Hello world")))
}
```

</details>

---

## Frontend Frameworks

<details>
<summary>lustre · redraw</summary>

Client-side / UI layer. Can be integrated with any backend (including glimr).

| Criterion | lustre | redraw |
| --- | --- | --- |
| Stars / Issues | 2.2k★ · 9 issues 🟨 | 62★ · 1 issue 🟩 |
| License | MIT · 🟩 | MIT · 🟩 |
| Target | Both (BEAM + JS) | JavaScript |
| Deps | 5 | 2 |
| Gleam compat | >= 1.13.0 · 🟩🟩 | >= 1.13.0 · 🟩🟩 |
| Maintenance | 2026-03-22 · 🟩🟩 | 2026-01-20 · 🟩 |
| Total work | Paginated `/commits` (34+ per page); history to Jan 2026 · 🟩🟩 | Paginated; 89 commits; Aug 2025–Jan 2026 visible · 🟩 |
| README maturity | 🟩🟩 (full guide) | 🟩🟩 (guide-style, examples, hooks, contexts) |

### lustre

[repo](https://github.com/lustre-labs/lustre) · Declarative HTML, Elm-style state (`init`/`update`/`view`), universal components, SSR support, CLI tooling. Most popular Gleam frontend framework (2.2k stars).

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

### redraw

[repo](https://github.com/ghivert/redraw) · React 19 bindings for Gleam. Components, props, hooks, contexts, with full Gleam type-checking. JS target only.

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

</details>

---

## Core HTTP Types

<details>
<summary>http</summary>

Shared type definitions used by all server and client adapters in the ecosystem.

| Criterion | http |
| --- | --- |
| Stars / Issues | 276★ · 1 issue 🟩 |
| License | Apache-2.0 · 🟩 |
| Target | Both (BEAM + JS) |
| Deps | 1 |
| Gleam compat | >= 1.11.0 · 🟩🟩 |
| Maintenance | 2025-10-02 · 🟩 |
| Total work | Paginated; dense 2025 · 🟩🟩 |
| README maturity | 🟩 (adapter tables + ecosystem) |

### http

[repo](https://github.com/gleam-lang/http) · Types and functions for HTTP clients and servers. Links server adapters (Mist, Cowboy) and client adapters (Plug, fetch, etc.). Central to ecosystem — almost every web library depends on this.

```gleam
// Core types provided by this package:
pub type Request(body)    // HTTP request with typed body
pub type Response(body)   // HTTP response with typed body
pub type Header           // Header key-value pair
pub type Method           // Get, Post, Put, Delete, etc.
pub type Scheme           // Http, Https
```

</details>

---

## HTTP Servers & Adapters

<details>
<summary>mist · cowboy · glisten</summary>

Low-level server implementations and adapter layers. Use with frameworks or custom routing.

| Criterion | mist | cowboy | glisten |
| --- | --- | --- | --- |
| Stars / Issues | 489★ · 9 issues 🟨 | 75★ · 1 issue 🟩 | 197★ · 0 issues 🟩🟩 |
| License | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 | Apache-2.0 · 🟩 |
| Target | BEAM | BEAM | BEAM |
| Deps | 9 | 5 | 5 |
| Gleam compat | >= 1.11.0 · 🟩🟩 | >= 1.7.0 · 🟩🟩 | >= 1.11.0 · 🟩🟩 |
| Maintenance | 2026-04-01 · 🟩🟩 | 2025-11-01 · 🟩 | 2026-01-11 · 🟩 |
| Total work | Paginated (master); active 2025–Apr 2026 · 🟩🟩 | Multi-year history · 🟩 | 231 commits; Jun 2025–Jan 2026 visible · 🟩 |
| README maturity | 🟩🟩 (builder, WebSocket, streaming) | 🟩 (adapter pattern) | 🟩 (TCP/TLS server) |

### mist

[repo](https://github.com/rawhat/mist) · "A glistening Gleam web server." Builder pattern, WebSocket support, streaming. Powers glimr and wisp. Active development (latest Apr 2026). Note: commit history on `master` branch, not `main`.

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

### cowboy

[repo](https://github.com/gleam-lang/cowboy) · Adapter for Cowboy (Erlang HTTP server). Use when integrating Gleam with existing Cowboy infrastructure.

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

### glisten

[repo](https://github.com/lpil/glisten) · Pure Gleam TCP/TLS server with supervised socket acceptors. Low-level building block for custom protocol implementations.

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

</details>

