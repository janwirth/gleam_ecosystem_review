# Tools for talking to databases in Gleam

Need to query a database, run migrations, or pool connections from Gleam?
This article maps out what's available.

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Scoring Dimensions](#scoring-dimensions)
   - [Discovery](#discovery)
3. [Categories](#categories)
   - [PostgreSQL Drivers](#postgresql-drivers) — [pog](#pog)
   - [SQLite Bindings](#sqlite-bindings) — [sqlight](#sqlight)
   - [SQL Query Builders](#sql-query-builders) — [cake](#cake)
   - [SQL Code Generators](#sql-code-generators) — [squirrel](#squirrel), [sqlode](#sqlode)
   - [Migration Tools](#migration-tools) — [cigogne](#cigogne), [gorrion](#gorrion), [migrant](#migrant), [storch](#storch), [akaridb](#akaridb)
   - [ORMs & Higher-Level Abstractions](#orms--higher-level-abstractions)
   - [Related Work](#related-work)
4. [Leaderboard](#leaderboard)

## Summary

Gleam database tools span drivers, bindings, query builders, code generators, and migrations. **pog** (PostgreSQL driver), **sqlight** (SQLite bindings), **cake** (multi-dialect SQL builder), **squirrel** and **sqlode** (SQL→Gleam codegen) cover query construction and execution. Migration tooling has matured into multiple options (**cigogne**, **gorrion** for PostgreSQL; **migrant**, **storch** for SQLite; **akaridb** as a closed-source outlier). ORMs remain a gap.

Snapshot: **2026-04-26**.

| Layer | BEAM | Status |
| --- | --- | --- |
| **[PostgreSQL Drivers](#postgresql-drivers)** | [pog](#pog) (driver with pooling) | 🟩🟩 active |
| **[SQLite Bindings](#sqlite-bindings)** | [sqlight](#sqlight) (low-level bindings) | 🟩🟩 active |
| **[SQL Query Builders](#sql-query-builders)** | [cake](#cake) (multi-dialect composer) | 🟩🟩 active |
| **[SQL Code Generators](#sql-code-generators)** | [squirrel](#squirrel) (PostgreSQL), [sqlode](#sqlode) (multi-dialect) | 🟩🟩 active |
| **[Migrations](#migration-tools)** | [cigogne](#cigogne) (pg), [gorrion](#gorrion) (pg, ecto-like), [migrant](#migrant) (sqlite), [storch](#storch) (sqlite, **superseded**), [akaridb](#akaridb) (closed-source) | 🟩 active, fragmented |
| **[ORMs](#orms--higher-level-abstractions)** | ⬜ No ORM found | Gap |

> [!IMPORTANT]
> All database tools here target **BEAM only**. No cross-target (JS) database libraries exist yet.

## Research Method

### Scoring Dimensions

Same rubric as [web-apps.md](./web-and-http/web-apps.md#scoring-dimensions). Recap:

- **Stars:** 🟩🟩 ≥200★, 🟩 ≥100★, 🟨 ≥10★, 🟥 <10★.
- **License:** 🟩 permissive (MIT/Apache/BSD), 🟥 viral or missing.
- **Gleam compat:** `gleam_stdlib` constraint format. 🟩 range (`>= X and < Y`), 🟥 `~>` pin.
- **Maintenance:** max(recency, responsiveness). 🟩🟩 <1 mo / <2 days / clean tracker, 🟩 <6 mo / <1 wk, 🟨 <1 yr / <1 mo, 🟥 older / ignored.
- **Age:** 🟩🟩 ≥3 yrs, 🟩 ≥1 yr, 🟨 ≥3 mo, 🟥 <3 mo.
- **README maturity:** 🟩🟩 full guide + examples, 🟩 clear tagline + usage, 🟥 minimal.
- **Idiomaticity:** 🟩 typed/explicit, 🟥 magic directives or template extraction.

**Leaderboard:** 🟥 = −1, 🟨 = 0, 🟩 = 1, 🟩🟩 = 2. Sum of 7 dims, max 13.

### Discovery

Repos identified via [packages.gleam.run](https://packages.gleam.run/) searches:
- [database](https://packages.gleam.run/?search=database)
- [db](https://packages.gleam.run/?search=db)
- [sql](https://packages.gleam.run/?search=sql)
- [postgres](https://packages.gleam.run/?search=postgres)
- [sqlite](https://packages.gleam.run/?search=sqlite)
- [migration](https://packages.gleam.run/?search=migration)
- [orm](https://packages.gleam.run/?search=orm)
- [ecto](https://packages.gleam.run/?search=ecto)

## Categories

### PostgreSQL Drivers

#### pog
[repo](https://github.com/lpil/pog) · [🥇](#leaderboard)

PostgreSQL driver for Gleam. Provides connection pooling, parameterized queries, and type-safe result decoding. No ORM — write SQL directly and decode results with custom decoders.

| Criterion | [pog](https://github.com/lpil/pog) |
| --- | --- |
| Stars | 248★ · 🟩🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM |
| Deps | 5 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 🟩 (last commit 2026-03-09, ~7 weeks vs 2026-04-26) |
| Age | ~6 years (Nov 2019) · 🟩🟩 |
| README maturity | 🟩🟩 (comprehensive guide with pooling example, SQL API, error handling) |
| Idiomaticity | 🟩 (typed queries, explicit decoders) |

**Key features:**
- Connection pooling via `pog.Postgrex`
- Parameterized queries (prevents SQL injection)
- Custom decoders for result rows
- Supports transactions
- Async-safe (works with Gleam processes)

```gleam
import pog
import gleam/dynamic/decode

pub fn get_user(db: pog.Connection, id: Int) -> Result(pog.Returned(User), pog.QueryError) {
  let decoder = {
    use id <- decode.field(0, decode.int)
    use name <- decode.field(1, decode.string)
    use email <- decode.field(2, decode.string)
    decode.success(User(id:, name:, email:))
  }
  
  "SELECT id, name, email FROM users WHERE id = $1"
  |> pog.query()
  |> pog.parameter(pog.int(id))
  |> pog.returning(decoder)
  |> pog.execute(db)
}
```

### SQLite Bindings

#### sqlight
[repo](https://github.com/lpil/sqlight) · [🥉](#leaderboard)

SQLite bindings for Gleam. Lower-level than pog — you manage connections directly and decode results. Good for embedding SQLite in Gleam applications or when PostgreSQL isn't needed.

| Criterion | [sqlight](https://github.com/lpil/sqlight) |
| --- | --- |
| Stars | 147★ · 🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM |
| Deps | 2 |
| Gleam compat | `>= 0.32 and < 2.0` · 🟩 |
| Maintenance | 🟩🟩 (last commit 2026-04-18, ~8 days vs 2026-04-26) |
| Age | ~3.5 years (Dec 2022) · 🟩🟩 |
| README maturity | 🟩 (tagline + basic example + list of functions) |
| Idiomaticity | 🟩 (typed, explicit) |

**Key features:**
- Direct SQLite C bindings
- No connection pooling (manage connections yourself)
- Parameterized queries
- Synchronous API (blocks until query completes)

```gleam
import sqlight
import gleam/list

pub fn insert_user(db: sqlight.Connection, name: String, email: String) -> Result(Nil, sqlight.Error) {
  sqlight.exec(
    db,
    "INSERT INTO users (name, email) VALUES (?, ?)",
    [sqlight.text(name), sqlight.text(email)],
  )
}

pub fn list_users(db: sqlight.Connection) -> Result(List(User), sqlight.Error) {
  sqlight.query(
    db,
    "SELECT id, name, email FROM users",
    [],
    decode_user,
  )
}

fn decode_user(row: sqlight.Row) -> Result(User, Nil) {
  User(
    id: sqlight.column(row, 0),
    name: sqlight.column(row, 1),
    email: sqlight.column(row, 2),
  )
  |> Ok
}
```

### SQL Query Builders

#### cake
[repo](https://github.com/inoas/gleam-cake) · [🥇](#leaderboard)

Multi-dialect SQL composer. Builds prepared-statement-safe SQL fragments for PostgreSQL, SQLite, MariaDB, and MySQL. Composes queries — does **not** execute. Pairs with execution adapters (`pog`, `sqlight`, `shork`, `gmysql`).

| Criterion | [cake](https://github.com/inoas/gleam-cake) |
| --- | --- |
| Stars | 124★ · 🟩 |
| License | MPL-2.0 · 🟥 (weak copyleft, file-level — flag for MIT/Apache shops) |
| Target | ☎️ BEAM + JS |
| Gleam compat | unknown (gleam.toml fetch failed) · 🟨 |
| Maintenance | 🟩🟩 (last commit 2026-04-25, 1 day before snapshot) |
| Age | ~2 years (Apr 2024) · 🟩🟩 |
| README maturity | 🟩🟩 (full guide: install, worked examples, design goals/non-goals, dialect adapter list) |
| Idiomaticity | 🟩 (typed fragment composition, prepared-statement-safe) |
| Issues | 2 open |

**Key features:**
- Four SQL dialects from one API: PostgreSQL, SQLite, MariaDB, MySQL
- Erlang + JavaScript targets
- Read + write query construction (SELECT / INSERT / UPDATE / DELETE)
- Composition-only — execute via separate adapter (pog/sqlight/shork/gmysql)
- Prepared-statement-safe parameter binding

### SQL Code Generators

#### squirrel
[repo](https://github.com/giacomocavalieri/squirrel) · [🥇](#leaderboard)

SQL-first code generator: write `.sql` files, squirrel generates type-safe Gleam functions with decoders. Embraces SQL + editor support (syntax highlighting, formatting) without ORM magic. Requires PostgreSQL 16+.

| Criterion | [squirrel](https://github.com/giacomocavalieri/squirrel) |
| --- | --- |
| Stars | 630★ · 🟩🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM (Postgres 16+ required) |
| Deps | 2 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 🟩 (last commit 2026-03-19, ~5 weeks vs 2026-04-26) |
| Age | ~1.5 years (Aug 2024) · 🟩 |
| README maturity | 🟩🟩 (FOSDEM talk, detailed guides, real examples) |
| Idiomaticity | 🟩 (generated code matches hand-written idiomatic Gleam) |

**Key features:**
- Write queries in `.sql` files (syntax highlighting, formatting, `explain`)
- Auto-generate type-safe decoders from SQL result types
- Convention-based: `src/**/sql/` → `src/**/sql.gleam`
- Works with `pog` driver for execution
- Prevent decoder/query drift automatically

```sql
-- src/app/sql/find_user.sql
-- Find user by name with owned items count.
select
  u.id,
  u.name,
  count(i.id) as items_count
from
  users u
left join items i on u.id = i.user_id
where
  u.name = $1
group by u.id, u.name
```

Generated Gleam (automatic):
```gleam
import pog
import app/sql

pub fn main(db: pog.Connection) {
  let assert Ok(pog.Returned(_count, rows)) = 
    sql.find_user(db, "alice")
  
  let assert [sql.FindUserRow(id: 1, name: "alice", items_count: 5)] = rows
}
```

#### sqlode
[repo](https://github.com/nao1215/sqlode) · [leaderboard ↓](#leaderboard)

Multi-dialect sqlc-style code generator. Reads SQL schema + query files, emits typed Gleam code. Supports PostgreSQL (via pog), MySQL 8 (via shork), and SQLite (via sqlight). Two output modes: **raw** (returns SQL string + params) and **native** (full adapter binding/decoding). Brand new — created 2026-04-12.

| Criterion | [sqlode](https://github.com/nao1215/sqlode) |
| --- | --- |
| Stars | 3★ · 🟥 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM (driver-agnostic at codegen time) |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 🟩🟩 (last commit 2026-04-26, snapshot day) |
| Age | ~2 weeks (Apr 2026) · 🟥 |
| README maturity | 🟩🟩 (comprehensive: tutorials, examples, migration instructions, Docker notes) |
| Idiomaticity | 🟩 (generated code uses standard adapter APIs) |
| Issues | 0 open (positive — but very new) |

**Key features:**
- sqlc-style typed code generation from SQL schema + query files
- Three dialects: PostgreSQL, MySQL 8, SQLite
- Two runtime modes: raw (`SQL + params`) and native (full driver binding + decoding)
- Query macros: `sqlode.arg`, `sqlode.narg`, `sqlode.slice`, `sqlode.embed`
- `sqlode verify` static-check command
- Latest version: **0.9.0**

### Migration Tools

Migration tooling has matured rapidly in 2026. Five Hex-published tools cover both PostgreSQL and SQLite, with Ecto-style and CLI-driven workflows.

#### cigogne
[repo](https://github.com/Billuc/cigogne) · [leaderboard ↓](#leaderboard)

PostgreSQL migration tool (via pog). SQL up/down migrations with hash verification (tamper detection), CLI for apply/rollback/status, and library-bundled migration support (deps can ship their own migrations).

| Criterion | [cigogne](https://github.com/Billuc/cigogne) |
| --- | --- |
| Stars | 43★ · 🟨 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM (PostgreSQL via pog) |
| Gleam compat | `>= 0.60.0 and < 2.0.0` · 🟩 |
| Maintenance | 🟩🟩 (last commit 2026-04-23, 3 days before snapshot) |
| Age | ~17 months (Nov 2024) · 🟩 |
| README maturity | 🟩🟩 (full guide: install, usage, `cigogne.toml` config, advanced options) |
| Idiomaticity | 🟩 |
| Latest version | **5.0.6** (Hex) |
| Issues | 4 open |

**Key features:**
- SQL up/down migrations with hash verification
- CLI: apply / rollback / status
- Library-bundled migrations (deps can ship migrations)
- Configurable transaction control via `cigogne.toml`

#### gorrion
[repo](https://github.com/davecaos/gorrion) · [leaderboard ↓](#leaderboard)

Ecto-inspired PostgreSQL migration library. Reads `.sql` migration files from a directory, tracks state in `_schema_migrations` table, supports `migrate()`, `rollback()`, `rollback_to()`, `status()`. Brand new (Mar 2026), comprehensive README, but only 1 star.

| Criterion | [gorrion](https://github.com/davecaos/gorrion) |
| --- | --- |
| Stars | 1★ · 🟥 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM (PostgreSQL via pog) |
| Gleam compat | `>= 0.51.0` (no upper bound — risky) · 🟨 |
| Maintenance | 🟩🟩 (last commit 2026-04-19, 1 week before snapshot) |
| Age | ~1 month (Mar 2026) · 🟥 |
| README maturity | 🟩🟩 (full guide: conventions, examples, API docs, implementation details) |
| Idiomaticity | 🟩 (Ecto-style API: `migrate`/`rollback`/`status`) |
| Latest version | **0.2.0-rc1** |
| Issues | 0 open |

**Key features:**
- Ecto-style API: `migrate()`, `rollback()`, `rollback_to()`, `status()`
- File-based `.sql` migrations in a directory
- State table: `_schema_migrations`
- Optional down migrations

#### migrant
[repo](https://github.com/aosasona/migrant) · [leaderboard ↓](#leaderboard)

SQLite-only migration tool (via sqlight). Oldest of the migration libs. Works on Erlang + JS targets.

| Criterion | [migrant](https://github.com/aosasona/migrant) |
| --- | --- |
| Stars | 19★ · 🟨 |
| License | MIT (repo) / Apache-2.0 (gleam.toml) — inconsistent · 🟩 |
| Target | ☎️ BEAM + JS (SQLite via sqlight) |
| Gleam compat | `>= 0.53.0 and < 1.0.0` · 🟥 (capped below stdlib 1.0 — restrictive) |
| Maintenance | 🟩 (last commit 2025-11-30, ~5 months vs snapshot) |
| Age | ~2.5 years (Oct 2023) · 🟩🟩 |
| README maturity | 🟩 (basic install + usage) |
| Idiomaticity | 🟩 |
| Latest version | **1.0.1** (Hex) — Notion list says 1.0.0 |
| Issues | 5 open |

**Key features:**
- SQLite migrations via sqlight
- Erlang + JS targets (Erlang primary)
- Local dependency install supported

> [!WARNING]
> `migrant`'s gleam_stdlib constraint (`< 1.0.0`) is restrictive — will block compatibility with newer stdlib versions. Also has a license inconsistency (repo: MIT; gleam.toml: Apache-2.0).

#### storch
[repo](https://github.com/VioletBuse/storch) · [leaderboard ↓](#leaderboard)

> [!WARNING]
> **Superseded.** Repo README explicitly states storch is "superceded by the feather package, which offers a superset of storch's functionality." Do not pick up for new projects.

SQLite migration runner (via sqlight). CLI generates migration files; on apply, also writes a schema file.

| Criterion | [storch](https://github.com/VioletBuse/storch) |
| --- | --- |
| Stars | 1★ · 🟥 |
| License | Apache-2.0 (gleam.toml; GitHub API reports none specified) · 🟩 |
| Target | ☎️ BEAM (SQLite via sqlight) |
| Gleam compat | `>= 0.34.0 and < 2.0.0` · 🟩 |
| Maintenance | 🟥 (last commit 2024-06-28, ~22 months stale) |
| Age | ~2 years (May 2024) · 🟩🟩 |
| README maturity | 🟩 (basic setup + code example) |
| Idiomaticity | 🟩 |
| Latest version | **2.1.0** |
| Issues | 1 open |

#### akaridb
[hex](https://hex.pm/packages/akaridb) · [docs](https://hexdocs.pm/akaridb) · [leaderboard ↓](#leaderboard)

> [!IMPORTANT]
> **No public source.** akaridb is published on Hex with no GitHub/GitLab repository link in package metadata, hexdocs sidebar, or footer. Source is not inspectable; you cannot audit, fork, or contribute.

| Criterion | [akaridb](https://hex.pm/packages/akaridb) |
| --- | --- |
| Stars | ⬜ (no public repo) |
| License | Apache-2.0 · 🟩 |
| Target | ⬜ (no inspectable gleam.toml) |
| Gleam compat | ⬜ (no inspectable gleam.toml) |
| Maintenance | 🟥 (last release 2024-08-11, ~20 months stale) |
| Age | ~20 months · 🟩 |
| README maturity | 🟥 (hexdocs shows minimal README + single `akaridb.create_database()` function) |
| Idiomaticity | ⬜ (cannot inspect) |
| Latest version | **1.0.0** |
| Downloads | 16,111 all-time (1,832 last week, 266 last day — surprisingly active for an unverifiable package) |
| Issues | ⬜ (no public tracker) |

**Notes:** Despite high download counts, the absence of a public repository makes this package unsuitable for projects that require source-level audit, vendoring, or community contribution. Treat as a closed-source dependency.

### ORMs & Higher-Level Abstractions

No ORM exists for Gleam yet. The philosophy is:
- **Minimal** — Gleam prefers explicit SQL + decoders to auto-magic ORMs.
- **Control** — Manage queries and result mapping yourself.
- **Performance** — No hidden N+1 queries or implicit joinloading.

For feature-rich abstractions, consider:
- **Nested decoders** — Build reusable decoder functions to map rows to typed structures.
- **SQL codegen + drivers** — Use `squirrel` (PostgreSQL) or `sqlode` (multi-dialect) to generate type-safe decoders from SQL files + `pog`/`sqlight`/`shork` for execution.
- **Query builders** — Use `cake` to compose dialect-portable SQL fragments at runtime, then execute via your driver of choice.

### Related Work

- **[gleam/http](./web-and-http/web-apps.md#http)** — Core HTTP types, often paired with database tools for REST APIs.
- **[lustre](./web-and-http/web-apps.md#lustre)** — Web framework; Lustre Server Components often interact with databases on the BEAM side.

## Leaderboard

| Position | Award | Repo | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [lpil/pog](https://github.com/lpil/pog) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **10** |
| 2 | 🥈 | [giacomocavalieri/squirrel](https://github.com/giacomocavalieri/squirrel) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **9** |
| 2 | 🥈 | [lpil/sqlight](https://github.com/lpil/sqlight) | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩 | **9** |
| 4 | 🥉 | [Billuc/cigogne](https://github.com/Billuc/cigogne) | 🟨 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩🟩 | 🟩 | **8** |
| 5 | — | [inoas/gleam-cake](https://github.com/inoas/gleam-cake) | 🟩 | 🟥 | 🟨 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **7** |
| 6 | — | [nao1215/sqlode](https://github.com/nao1215/sqlode) | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **5** |
| 6 | — | [aosasona/migrant](https://github.com/aosasona/migrant) | 🟨 | 🟩 | 🟥 | 🟩 | 🟩🟩 | 🟩 | 🟩 | **5** |
| 8 | — | [davecaos/gorrion](https://github.com/davecaos/gorrion) | 🟥 | 🟩 | 🟨 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **4** |
| 8 | — | [VioletBuse/storch](https://github.com/VioletBuse/storch) ⚠ superseded | 🟥 | 🟩 | 🟩 | 🟥 | 🟩🟩 | 🟩 | 🟩 | **4** |
| — | ⬜ | [akaridb](https://hex.pm/packages/akaridb) (no public source) | ⬜ | 🟩 | ⬜ | 🟥 | 🟩 | 🟥 | ⬜ | **n/a** |

**Summary:**
- **Drivers & bindings:** **pog** (PostgreSQL) leads the article overall with active maintenance, mature pooling, and comprehensive docs. **sqlight** is the SQLite low-level binding of choice and just received a same-day commit at snapshot time.
- **Codegen:** **squirrel** is the established SQL→Gleam code generator (PostgreSQL only, 630★, FOSDEM-talked). **sqlode** is a brand-new (Apr 2026) multi-dialect challenger with a comprehensive README — promising but unproven.
- **Query builder:** **cake** is the only multi-dialect composer (PG/SQLite/MariaDB/MySQL, BEAM + JS) with active development. MPL-2.0 license is the main caveat for shops standardising on permissive licenses.
- **Migrations:** **cigogne** leads PostgreSQL migrations (mature, 5.0.6, hash-verified migrations, library-bundled migration support). **gorrion** is an Ecto-style alternative — well-documented but only one month old. **migrant** is the established SQLite option but its `< 1.0.0` stdlib cap is a flag. **storch** is explicitly **superseded by `feather`** — avoid for new projects. **akaridb** has no public source — treat as closed-source.
- **Notable patterns:** Three of nine actively-tracked repos (cigogne, sqlode, cake) had a commit within 3 days of snapshot — heavy churn in the Gleam DB space right now.

[How scores are calculated →](#scoring-dimensions)
