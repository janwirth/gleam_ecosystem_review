# Ecosystem Reviews

Systematic evaluations of software ecosystems — sampled by the author's project needs, with the goal of becoming a reliable reference for CTOs and coding agents making tooling decisions.

## Vision

Picking a library shouldn't mean a weekend of GitHub archaeology. Every review here asks the same questions — *is it maintained? is it idiomatic? does it actually cover the feature you need?* — and answers them with reproducible, snapshot-dated evidence.

The target audience:

- **CTOs & tech leads** — need to justify tooling choices with more than a star count.
- **Coding agents** — need machine-readable, consistently-scored signal to recommend the right library on behalf of a human.

Coverage starts where the author's own projects demand answers, then widens outward. Topic requests and contributions welcome.

## Articles

### Gleam

- [Tools for building web apps](gleam/tools-for-building-web-apps.md) — full-stack, server frameworks, HTTP servers, frontend, dev tools, RPC. 15 repos reviewed.
- [HTTP clients](gleam/http-clients.md) — making outbound HTTP requests.
- [OpenAPI → Gleam generators](gleam/openapi-to-gleam-generators.md) — client/server codegen from OpenAPI specs.
- [Gleam → OpenAPI generators](gleam/gleam-to-openapi-generators.md) — code-first spec generation from mist/wisp routes (gap: no tool exists yet).
- [Tools for spawning subprocesses](gleam/tools-for-spawning-subprocesses.md) — shelling out, streaming stdio, process control.
- [Hot reloading tools](gleam/hot-reloading-tools.md) — BEAM module swap, browser live reload, JS dev servers, file watcher primitives.
- [Syntax highlighting](gleam/syntax-highlighting.md) — per-language lexers, multi-language grammars, tree-sitter NIFs. 6 repos reviewed.
- [Tools for talking to databases](gleam/tools-for-talking-to-databases.md) — PostgreSQL drivers, SQLite bindings, query builders. 3 repos reviewed.

### OpenAPI

- [Postman → OpenAPI converters](openapi/postman-to-openapi-converters.md) — converting Postman collections to OpenAPI specs.

### Shell

- [Browser-based SSH terminals](shell/browser-ssh-terminals.md) — embeddable web SSH clients for iframe / Notion use.

### Upcoming

- Parsers & generators
- Linters

## Method

Every review uses the same scoring scaffold: **stars, license, language-compat, maintenance, age, README maturity, idiomaticity**, plus category-specific dimensions (feature completeness, ease of use, output quality) where they apply.

- Data comes from public sources — GitHub / GitLab / Hex / package registries — captured on a named snapshot date.
- Disregarded repos are listed with reasons, so the triage is auditable.
- Scores combine into a leaderboard so authors can sanity-check their own metrics at a glance.

The full scoring formalization lives in [formalization.md](formalization.md). The first worked example is in the [Gleam web apps review](gleam/tools-for-building-web-apps.md#research-method).

## Contributing

Requests for new ecosystems, categories, or re-snapshots are welcome. Open an issue.

## License

[MIT](LICENSE)
