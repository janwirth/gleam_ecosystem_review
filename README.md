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

**Web & HTTP**

- [Web apps](gleam/web-and-http/web-apps.md) — full-stack, server frameworks, HTTP servers, frontend, dev tools, RPC. 15 repos reviewed.
- [HTTP clients](gleam/web-and-http/http-clients.md) — making outbound HTTP requests.
- [Hot reloading](gleam/web-and-http/hot-reloading.md) — BEAM module swap, browser live reload, JS dev servers, file watcher primitives.

**Testing**

- [Testing libraries](gleam/testing/general-testing.md) — gleeunit, dream_test, property testing, snapshot, assertions. 17 repos reviewed.
- [Browser automation](gleam/testing/browser-automation.md) — CDP clients, E2E frameworks, integration test infra.

**Other**

- [Parsers & generators](gleam/parsers-and-generators.md) — parser combinators, format parsers, code generators (SQL→Gleam, OpenAPI→Gleam, JSON Schema), and gap analysis for Gleam→OpenAPI.
- [Subprocesses](gleam/subprocesses.md) — shelling out, streaming stdio, process control.
- [Syntax highlighting](gleam/syntax-highlighting.md) — per-language lexers, multi-language grammars, tree-sitter NIFs. 6 repos reviewed.
- [Databases](gleam/databases.md) — PostgreSQL / SQLite / MySQL drivers, query builders, codegen, migrations, framework-bundled DB. 9 repos reviewed (+ 4 disregarded).
- [Logging](gleam/logging.md) — OTP `logger` adapters, structured dual-target loggers, specialist sinks. 11 repos reviewed.
- [Authentication](gleam/authentication.md) — password hashing, JOSE/JWT, OAuth2 clients, TOTP, WebAuthn, sessions, IDaaS. 20+ repos reviewed.

### Testing

- [BDD with Gherkin](testing/bdd-with-gherkin.md) — cross-ecosystem Cucumber-family survey across Ruby, JS, JVM, Python, .NET, Go, Rust, PHP, Elixir, Gleam, Haskell.

### OpenAPI

- [Postman → OpenAPI converters](openapi/postman-to-openapi-converters.md) — converting Postman collections to OpenAPI specs.

### Shell

- [Browser-based SSH terminals](shell/browser-ssh-terminals.md) — embeddable web SSH clients for iframe / Notion use.

### Security

- [Recent incidents in major technologies](security/recent-incidents-in-major-technologies.md) — 2024-2026 CVE and supply-chain highlights across Next.js, WordPress, Python, npm, and others.

### Languages

- [Programming language popularity & desire](languages/programming-languages-popularity-and-desire.md) — Stack Overflow 2025 rankings: what's used vs what's loved.

### Upcoming

- Linters

## Method

Every review uses the same scoring scaffold: **stars, license, language-compat, maintenance, age, README maturity, idiomaticity**, plus category-specific dimensions (feature completeness, ease of use, output quality) where they apply.

- Data comes from public sources — GitHub / GitLab / Hex / package registries — captured on a named snapshot date.
- Disregarded repos are listed with reasons, so the triage is auditable.
- Scores combine into a leaderboard so authors can sanity-check their own metrics at a glance.

The full scoring formalization lives in [formalization.md](formalization.md). The first worked example is in the [Gleam web apps review](gleam/web-and-http/web-apps.md#research-method).

## Contributing

Requests for new ecosystems, categories, or re-snapshots are welcome. Open an issue.

## License

[MIT](LICENSE)
