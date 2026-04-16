# Gleam Web Servers and Frameworks Review

## Table of Contents

1. [TL;DR](#tldr)
2. [Approach](#approach)
   - [Evaluation Criteria](#evaluation-criteria)
   - [Repos Reviewed](#repos-reviewed)
   - [Disregarded Repos](#disregarded-repos)
3. [Full-Stack Frameworks](#full-stack-frameworks) — [glimr](#glimr)
4. [Server Frameworks](#server-frameworks) — [wisp](#wisp) · [glen](#glen)
5. [Dev Tools](#dev-tools) — [lustre_dev_tools](#lustre_dev_tools) · [gleam-radiate](#gleam-radiate) · [olive](#olive)
6. [Frontend Frameworks](#frontend-frameworks) — [lustre](#lustre) · [redraw](#redraw)
7. [Core HTTP Types](#core-http-types) — [http](#http)
8. [HTTP Servers & Adapters](#http-servers--adapters) — [mist](#mist) · [cowboy](#cowboy) · [glisten](#glisten)
9. [Notes](#notes)

## TL;DR

Gleam's web ecosystem already covers most use cases. This review evaluates every actively maintained web library so you can **pick the right tool instead of rebuilding what exists.**

- **Grouped by purpose:** full-stack, server, frontend, dev tools, HTTP infrastructure
- **Assessed for maturity:** activity, issues, README quality, community adoption
- **Production-ready picks identified** vs. learning material and abandoned experiments

**Don't rebuild what exists.** Find your use case below, click through to the detailed evaluation.

| Use case | BEAM | JS |
| --- | --- | --- |
| **Full-stack, batteries-included** | [glimr](#glimr) (165★) — routing, templates, DB schema + migrations, auth, hot reload | — |
| **Server framework** | [wisp](#wisp) (1.4k★) — handler+middleware, production-ready | [glen](#glen) (111★) — lightweight, promise-based, Deno/Node/Workers |
| **Slim & low-level** | [mist](#mist) (489★) + [glisten](#glisten) (197★) — raw HTTP/TCP primitives | — |
| **Core HTTP types** | [http](#http) (276★) — request/response types, used by nearly everything | [http](#http) (276★) — also targets JS |
| **Frontend (Elm-style)** | [lustre](#lustre) (2.2k★) — declarative UI, SSR, universal | [lustre](#lustre) (2.2k★) — also runs on JS target |
| **Frontend (React)** | — | [redraw](#redraw) (62★) — full React 19 bindings, type-checked |
| **Dev tooling** | [lustre_dev_tools](#lustre_dev_tools) (112★) — dev server, bundling, Tailwind, live reload | — |

### Ecosystem Overview

- **Full-stack**
  - [glimr](#glimr) — routing, templates, DB schema, auth, hot reload
    - Uses [mist](#mist) for HTTP
- **Server frameworks** (independent)
  - [wisp](#wisp) — practical, production-ready; uses [mist](#mist)
  - [glen](#glen) — lightweight, JS target
- **Dev tools**
  - [lustre_dev_tools](#lustre_dev_tools) — dev server, bundling, Tailwind
  - [olive](#olive) — hot-reload, restarts server
  - [gleam-radiate](#gleam-radiate) — hot-reload, reloads BEAM modules
- **Frontend** (independent)
  - [lustre](#lustre) — declarative, Elm-style, universal, SSR
  - [redraw](#redraw) — React 19 bindings, JS target
- **HTTP infrastructure**
  - [mist](#mist) — active, WebSocket, streaming; uses [glisten](#glisten)
    - Used by [glimr](#glimr) and [wisp](#wisp)
  - [cowboy](#cowboy) — Erlang adapter
- **Socket layer**
  - [glisten](#glisten) — TCP/TLS, supervised acceptors (used by [mist](#mist))
- **Core types**
  - [http](#http) (gleam_http) — request/response types, adapters

---

## Approach

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

> <details>
> <summary><strong>Scoring dimensions</strong></summary>
>
> - **Maintenance:** Latest commit date + commit frequency. 🟩🟩 = actively developed (recent + frequent), 🟩 = steady, 🟥 = dormant
> - **README maturity:** 🟩🟩 = guide-style + examples + full docs, 🟩 = clear, 🟥 = minimal/broken
> - **Community (stars):** Raw star count shown in tables
> - **Work (effort):** Commit count, history depth, pagination. 🟩🟩 = substantial, 🟩 = solid, 🟥 = sparse
> - **Issues:** Count + context. Zero = positive, high = possible problems (balance against activity)
> - **Gleam compat:** Version constraint from `gleam.toml`. 🟩🟩 = compatible with latest (v1.15.4). "No constraint" = no minimum specified.
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

<details>
<summary>Excluded from detailed review due to maintenance or scope issues</summary>

**Unmaintained:**
- **[gleam-lang/plug](https://github.com/gleam-lang/plug)** — HTTP adapter for Elixir Plug compatibility. Last commit 2020-08-27 (6+ years dormant). Activity 🟥.

**Superseded / Low Activity:**
- **[brettkolodny/react-gleam](https://github.com/brettkolodny/react-gleam)** — React bindings for Gleam. 69★, 27 commits (Nov 2022–Sep 2025). Superseded by redraw which has fuller React 19 coverage. Maintenance 🟥.
- **[samifouad/gild_frontend](https://github.com/samifouad/gild_frontend)** — React client-side framework for Gleam. 0★, 18 commits, "extremely alpha." Abandoned experiment. Work 🟥.
- **[TobiasBinnewies/novdom](https://gitlab.com/TobiasBinnewies/novdom)** — Frontend framework for Gleam. GitLab-hosted. 93 commits, last Sep 2024 (7+ months stale). v0.5.0. MIT. Maintenance 🟥.
- **[TobiasBinnewies/cosmos](https://gitlab.com/TobiasBinnewies/cosmos)** — UI library for novdom. 3 commits, v0.0.0. Early experiment, depends on inactive novdom. Work 🟥.

**Experiments & Stubs:**
- **[myzykyn/gleam_webserver](https://github.com/myzykyn/gleam_webserver)** — Stub/scaffold. Single commit, default TODO template. Not usable; abandoned experiment. Work 🟥.
- **[gleam-lang/example-echo-server](https://github.com/gleam-lang/example-echo-server)** — Reference implementation. Learning example only; not for production use.

</details>

</details>

---

## Full-Stack Frameworks

<details>
<summary>glimr</summary>

All-in-one solutions: routing, templates, middleware, auth, database, development tools.

| Criterion | glimr |
| --- | --- |
| Open issues | 0 |
| Stars | 165 |
| License | MIT |
| Target | BEAM (server) + JS (Vite frontend) |
| Deps | 16 |
| Gleam compat | No constraint specified · 🟩🟩 |
| Maintenance | 2026-04-11 · 🟩🟩 |
| Total work | Paginated `/commits` (35+ per page); history Mar 5 to Apr 11 · 🟩🟩 |
| README maturity | 🟩🟩 (batteries-included; full guide) |

### glimr

Batteries-included full-stack framework. Routing with annotations, compiled Loom templates, middleware, DB schema + migrations, Vite integration, hot reload. Built on Mist HTTP server. Zero open issues — strong quality signal.

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
| Open issues | 16 | 2 |
| Stars | 1400 | 111 |
| License | Apache-2.0 | MIT |
| Target | BEAM | JavaScript |
| Deps | 12 | 7 |
| Gleam compat | >= 1.11.0 · 🟩🟩 | No constraint · 🟩🟩 |
| Maintenance | 2026-03-28 · 🟩🟩 | 2025-06-30 · 🟩 |
| Total work | Paginated (35+); Oct 2025–Mar 2026 · 🟩 | Paginated; Feb 2024 back · 🟩 |
| README maturity | 🟩🟩 (practical, handler+middleware) | 🟩 (tagline + repo) |

### wisp

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

### glen

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

</details>

---

## Dev Tools

<details>
<summary>lustre_dev_tools · gleam-radiate · olive</summary>

Hot-reload, dev servers, bundling. Used during development, not shipped to production.

| Criterion | lustre_dev_tools | gleam-radiate | olive |
| --- | --- | --- | --- |
| Open issues | 19 | 3 | 1 |
| Stars | 112 | 66 | 8 |
| License | MIT | Apache-2.0 | Apache-2.0 |
| Target | BEAM | BEAM | BEAM |
| Deps | 20 | 5 | 14 |
| Gleam compat | No constraint · 🟩🟩 | No constraint · 🟩🟩 | No constraint · 🟩🟩 |
| Maintenance | 2026-04-02 · 🟩🟩 | 2025-09-18 · 🟩 | 2025-07-18 · 🟩 |
| Total work | Paginated; active 2025–Apr 2026 · 🟩🟩 | Oct 2023–2025 · 🟩 | Short (Feb–Jul 2025) · 🟩 |
| README maturity | 🟩 (features list, install guide, no code samples) | 🟩 (tagline) | 🟩 (clear + limits) |

### lustre_dev_tools

Lustre's CLI and development tooling. Zero-config dev server, bundling, TailwindCSS v4 auto-detection. Uses bun for bundling/file watching. CLI-driven (no Gleam API). Pairs with lustre frontend framework.

```sh
# Install as dev dependency
gleam add lustre_dev_tools --dev

# Start dev server with live reload
gleam run -m lustre/dev start
```

### gleam-radiate

Watches source files and reloads BEAM modules. Lightweight hot-reload. 5 deps, small footprint.

```gleam
let _ =
  radiate.new()
  |> radiate.add_dir("src")
  |> radiate.start()
```

### olive

Watches and restarts Gleam server processes. Similar to gleam-radiate but restarts whole process. 14 deps (pulls in wisp + mist).

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
| Open issues | 9 | 1 |
| Stars | 2213 | 62 |
| License | MIT | MIT |
| Target | Both (BEAM + JS) | JavaScript |
| Deps | 5 | 2 |
| Gleam compat | >= 1.13.0 · 🟩🟩 | >= 1.13.0 · 🟩🟩 |
| Maintenance | 2026-03-22 · 🟩🟩 | 2026-01-20 · 🟩 |
| Total work | Paginated `/commits` (34+ per page); history to Jan 2026 · 🟩🟩 | Paginated; 89 commits; Aug 2025–Jan 2026 visible · 🟩 |
| README maturity | 🟩🟩 (full guide) | 🟩🟩 (guide-style, examples, hooks, contexts) |

### lustre

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

### redraw

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

</details>

---

## Core HTTP Types

<details>
<summary>http</summary>

Shared type definitions used by all server and client adapters in the ecosystem.

| Criterion | http |
| --- | --- |
| Open issues | 1 |
| Stars | 276 |
| License | Apache-2.0 |
| Target | Both (BEAM + JS) |
| Deps | 1 |
| Gleam compat | >= 1.11.0 · 🟩🟩 |
| Maintenance | 2025-10-02 · 🟩 |
| Total work | Paginated; dense 2025 · 🟩🟩 |
| README maturity | 🟩 (adapter tables + ecosystem) |

### http

Types and functions for HTTP clients and servers. Links server adapters (Mist, Cowboy) and client adapters (Plug, fetch, etc.). Central to ecosystem — almost every web library depends on this.

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
| Open issues | 9 | ⬜ | ⬜ |
| Stars | 489 | 75 | 197 |
| License | Apache-2.0 | Apache-2.0 | Apache-2.0 |
| Target | BEAM | BEAM | BEAM |
| Deps | 9 | 5 | 5 |
| Gleam compat | >= 1.11.0 · 🟩🟩 | >= 1.7.0 · 🟩🟩 | >= 1.11.0 · 🟩🟩 |
| Maintenance | 2026-04-01 · 🟩🟩 | 2025-11-01 · 🟩 | 2026-01-11 · 🟩 |
| Total work | Paginated (master); active 2025–Apr 2026 · 🟩🟩 | Multi-year history · 🟩 | 231 commits; Jun 2025–Jan 2026 visible · 🟩 |
| README maturity | 🟩🟩 (builder, WebSocket, streaming) | 🟩 (adapter pattern) | 🟩 (TCP/TLS server) |

### mist

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

### cowboy

Adapter for Cowboy (Erlang HTTP server). Use when integrating Gleam with existing Cowboy infrastructure.

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

Pure Gleam TCP/TLS server with supervised socket acceptors. Low-level building block for custom protocol implementations.

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

---

## Notes

<details>
<summary>Expand</summary>

**Disregarded repos** — See [Approach > Disregarded Repos](#disregarded-repos) for unmaintained or experimental projects. Includes `gleam-lang/plug` (2020, unmaintained), `react-gleam` (superseded by redraw), `gild_frontend` (abandoned), `novdom`/`cosmos` (stale), `gleam_webserver` (stub), and `example-echo-server` (learning example).

**Known issues**: Cowboy and glisten GitHub issue tabs did not load reliably in session; issue counts marked ⬜.

**Key signals:**
- **glimr**: Zero issues. Strong maintenance signal. Built on Mist.
- **wisp**: 16 issues but actively maintained. High adoption (1.4k stars).
- **mist**: Active development (latest Apr 2026). Used by glimr and wisp.

</details>

---

<details>
<summary><strong>Comparison Section</strong></summary>

None of these READMEs include head-to-head framework comparison. **gleam-lang/http** includes **adapter comparison tables** (Mist, Cowboy, Plug, fetch, etc.) — ecosystem context, not tool comparison.

</details>

---

<details>
<summary><strong>Open Questions</strong></summary>

> Added during review pass (2026-04-14). Items below need investigation or decision before this article can be considered reliable.

### Blind Spots
1. **Hex docs / external documentation.** README quality is assessed but published hex.pm docs (common in Gleam ecosystem) aren't checked.

</details>
