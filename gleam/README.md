# Gleam Ecosystem Reviews

Snapshot-dated evaluations of the [Gleam](https://gleam.run/) library landscape — what's maintained, what's idiomatic, and what actually covers the feature you need. Each article scores repos against the same scaffold (stars, license, language-compat, maintenance, age, README maturity, idiomaticity) plus category-specific dimensions, with disregarded repos listed for auditability.

For the full scoring formalization see [../formalization.md](../formalization.md); the review process is documented in [../workflows/REVIEW_METHODOLOGY.md](../workflows/REVIEW_METHODOLOGY.md). Back to the top-level index: [../README.md](../README.md).

## Articles

### Web & HTTP

- [Web apps](web-and-http/web-apps.md) — full-stack, server frameworks, HTTP servers, frontend, dev tools, RPC. 15 repos reviewed.
- [HTTP clients](web-and-http/http-clients.md) — making outbound HTTP requests.
- [Hot reloading](web-and-http/hot-reloading.md) — BEAM module swap, browser live reload, JS dev servers, file watcher primitives.
- [UI development](web-and-http/ui-development.md) — component libraries, layout primitives, icon sets, drag-and-drop kits, animations, and form helpers above the Lustre / redraw frontend choice.
- [Lustre server components](web-and-http/lustre-server-components.md) — guided tour through the official Lustre server-component examples (init / update / view running on the BEAM, DOM patches over WebSocket).
- [Tailwind](web-and-http/tailwind.md) — Gleam-specific Tailwind integrations, standalone CLI wrappers, class utilities, and component kits.

### Testing

- [Testing libraries](testing/general-testing.md) — gleeunit, dream_test, property testing, snapshot, assertions. 17 repos reviewed.
- [Browser automation](testing/browser-automation.md) — CDP clients, E2E frameworks, integration test infra.

### Other

- [Parsers & generators](parsers-and-generators.md) — parser combinators, format parsers, code generators (SQL→Gleam, OpenAPI→Gleam, JSON Schema), and gap analysis for Gleam→OpenAPI.
- [Subprocesses](subprocesses.md) — shelling out, streaming stdio, process control.
- [Syntax highlighting](syntax-highlighting.md) — per-language lexers, multi-language grammars, tree-sitter NIFs. 6 repos reviewed.
- [Databases](databases.md) — PostgreSQL / SQLite / MySQL drivers, query builders, codegen, migrations, framework-bundled DB. 9 repos reviewed (+ 4 disregarded).
- [Logging](logging.md) — OTP `logger` adapters, structured dual-target loggers, specialist sinks. 11 repos reviewed.
- [Authentication](authentication.md) — password hashing, JOSE/JWT, OAuth2 clients, TOTP, WebAuthn, sessions, IDaaS. 20+ repos reviewed.
- [Email](email.md) — SMTP clients, transactional email vendor wrappers, HTML email composition, and the inbound / IMAP gap.
