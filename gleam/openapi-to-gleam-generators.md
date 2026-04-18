# OpenAPI to Gleam generators

You have an OpenAPI spec.
You want typed Gleam code without hand-rolling 200 records and decoders.
Start here.

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Scoring Dimensions](#scoring-dimensions)
   - [Discovery](#discovery)
3. [Categories](#categories)
   - [OpenAPI Generators](#openapi-generators) — [oaspec](#oaspec) · [gilly](#gilly)
4. [Leaderboard](#leaderboard)

## Summary

Two Gleam packages take an OpenAPI 3.x spec and emit typed Gleam — both landed in the last two weeks of the snapshot window. Different scope: `oaspec` generates **both** server stubs and client SDKs from one spec; `gilly` focuses on **client** SDKs only. Both run as CLIs, both produce code that uses standard `gleam/http` types and works with any compatible HTTP client.

Snapshot date: 2026-04-18.

| Tool | Scope | Maturity | Notes |
| --- | --- | --- | --- |
| **[oaspec](#oaspec)** ([repo](https://github.com/nao1215/oaspec), 3★) | Client + server | ~11 days old, v0.12.0 | Wide OpenAPI 3.x coverage, capability boundaries documented, CI `--check` mode |
| **[gilly](#gilly)** ([repo](https://github.com/cyclimse/gilly), 1★) | Client only | ~6 days old, v0.3.0 | Builder-pattern requests, snapshot-tested, real-world Scaleway example |

> [!IMPORTANT]
> Both projects are days old. They move fast — every PR in both repos was merged within hours of opening. Neither has a community track record yet. Pin a version.

> [!NOTE]
> Generated code is plain Gleam built on `gleam/http` types, so it composes with the rest of the ecosystem (e.g. [server frameworks](tools-for-building-web-apps.md#server-frameworks), [HTTP clients](http-clients.md)). The generators themselves run on BEAM.

## Research Method

### Scoring Dimensions

- **Stars:** Community adoption signal based on star count. 🟩🟩 = ≥200★, 🟩 = ≥100★, 🟨 = ≥10★, 🟥 = <10★. *Example: 3★ → 🟥.*
- **License:** Whether the license allows unrestricted commercial use. 🟩 = permissive OSS or public-domain dedication (MIT, Apache-2.0, BSD, Unlicense). 🟥 = viral (GPL, AGPL) or no license.
- **Gleam compat:** Checks `gleam_stdlib` version constraint format in `gleam.toml`. 🟩 = range constraint (`>= X and < Y`), 🟥 = non-range constraint.
- **Maintenance:** `max(recency, responsiveness)`. **Recency:** 🟩🟩 <1 month, 🟩 <6 months, 🟨 <1 year, 🟥 >1 year. **Responsiveness:** 🟩🟩 <2 days to owner reply, 🟩 <1 week, 🟨 <1 month, 🟥 >1 month or ignored.
- **Age:** First commit to snapshot. 🟩🟩 ≥3 years, 🟩 ≥1 year, 🟨 ≥3 months, 🟥 <3 months.
- **README maturity:** 🟩🟩 = guide-style with examples, full feature docs, getting-started, capability boundaries. 🟩 = clear description, basic usage, flag/option reference. 🟥 = minimal or missing.
- **Idiomaticity:** Typed, sound, explicit, no magic. 🟩 = explicit codegen step producing readable Gleam (developer can see and understand the generated code). 🟥 = magic directives or implicit template extraction. Both tools here are explicit codegen — the generated `.gleam` files are checked into your repo.
- **Feature completeness:** Coverage of the OpenAPI 3.x surface and codegen feature matrix in the [category table](#openapi-generators) (14 rows: client, server, spec formats, `$ref`, composition keywords, param styles, form bodies, security, validation, `validate`/`--check`, library API, snapshot tests). 🟩🟩 ≥90% full support, 🟩 ≥60%, 🟨 ≥30%, 🟥 <30%. Partial support counts as half.

**Leaderboard scoring:** 🟥 = −1, 🟨 = 0, 🟩 = 1, 🟩🟩 = 2. Sum of all 8 dimensions. Max = 15.

### Discovery

2 repos found via [Gleam packages registry](https://packages.gleam.run/?search=openapi) — **both included**.

- [openapi](https://packages.gleam.run/?search=openapi)

## Categories

### OpenAPI Generators

Build-time tools that read an OpenAPI 3.x spec and emit typed Gleam modules. Run from the CLI; commit the generated code; no runtime spec parsing.

| Criterion | [oaspec](https://github.com/nao1215/oaspec) | [gilly](https://github.com/cyclimse/gilly) |
| --- | --- | --- |
| Stars | 3★ · 🟥 | 1★ · 🟥 |
| License | MIT · 🟩 | Unlicense (public domain) · 🟩 |
| Target | ☎️ BEAM (codegen + generated code) | ☎️ BEAM (codegen + generated code) |
| Deps | 7 | 7 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 🟩🟩 (last commit 2026-04-18; PRs merged within minutes) | 🟩🟩 (last commit 2026-04-14; PRs merged within minutes) |
| Age | ~11 days (Apr 7, 2026) · 🟥 | ~6 days (Apr 12, 2026) · 🟥 |
| README maturity | 🟩🟩 (full guide, CLI ref, library API, capability boundaries) | 🟩 (description, usage, flag table, examples link) |
| Idiomaticity | 🟩 (explicit codegen) | 🟩 (explicit codegen) |
| Feature completeness | 🟩🟩 (13/14 full) | 🟨 (≈5/14 full, 3 partial) |
| | | |
| **Features** | | |
| Generates client SDK | ✅ | ✅ |
| Generates server stubs (handlers + router) | ✅ | — |
| Spec format | YAML + JSON | JSON |
| `$ref` resolution | ✅ (incl. circular detection) | partial |
| `oneOf` / `anyOf` / `allOf` | ✅ | partial |
| Path / query / header / cookie params | ✅ | path, query, header |
| `deepObject` / `pipeDelimited` / `spaceDelimited` | ✅ | — |
| Form bodies (`x-www-form-urlencoded`, `multipart`) | ✅ | — |
| Security schemes (apiKey, HTTP, OAuth2, OIDC) | ✅ (bearer attach for OAuth2/OIDC) | manual via request middleware |
| Validation guards from schema constraints | ✅ | — |
| `validate` subcommand (no codegen) | ✅ | — |
| CI drift check (`--check`) | ✅ | — |
| Library API (use as Gleam dep, not just CLI) | ✅ | ✅ |
| Snapshot tests on generated output | — | ✅ (birdie) |

#### oaspec
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

#### gilly
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

## Leaderboard

Every contribution is invaluable and appreciated.

Both projects appeared within ~2 weeks of the snapshot. Stars, age, and traction scores are all hammered by youth — the substantive scores (compat, maintenance, README, idiomaticity) are what separate them right now.

**Honorable mentions**
- **oaspec** ([nao1215](https://github.com/nao1215)) — Wide OpenAPI coverage out of the gate, capability boundaries documented, server *and* client. Test suite already substantial.
- **gilly** ([cyclimse](https://github.com/cyclimse)) — Tight scope, builder-pattern API, snapshot-tested, real-world Scaleway example proving it works on a non-trivial spec.

| Position | Repo | ★ | Lic | Compat | Maint | Age | README | Idiom | Features | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | [nao1215/oaspec](https://github.com/nao1215/oaspec) | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | 🟩🟩 | **7** |
| 2 | [cyclimse/gilly](https://github.com/cyclimse/gilly) | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩 | 🟩 | 🟨 | **4** |

**By target:** ☎️ BEAM **11** (2 repos).

[How scores are calculated →](#scoring-dimensions)
