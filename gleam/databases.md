# Tools for talking to databases in Gleam

Need to query a database, run migrations, or pool connections from Gleam?
This article maps out what's available.

## Table of Contents

1. [Summary](#summary)
2. [Research Method](#research-method)
   - [Scoring Dimensions](#scoring-dimensions)
   - [Discovery](#discovery)
3. [Categories](#categories)
   - [PostgreSQL Drivers](#postgresql-drivers) — [pog](#pog-)
   - [SQLite Bindings](#sqlite-bindings) — [sqlight](#sqlight-)
   - [MySQL Drivers](#mysql-drivers) — [shork](#shork-)
   - [SQL Query Builders](#sql-query-builders) — [cake](#cake-)
   - [SQL Code Generators](#sql-code-generators) — [squirrel](#squirrel-), [sqlode](#sqlode-)
   - [Migration Tools](#migration-tools) — [cigogne](#cigogne-), [gorrion](#gorrion-), [Disregarded](#disregarded-migration-tools)
   - [ORMs & Higher-Level Abstractions](#orms--higher-level-abstractions) — [glimr db](#glimr-db-)
   - [Related Work](#related-work)
4. [Leaderboard](#leaderboard)

Dialect legend: 🐘 PostgreSQL · 🪶 SQLite · 🐬 MySQL.

## Summary

Gleam database tools span drivers, bindings, query builders, code generators, migrations, and (newly) framework-bundled abstractions. **pog** 🐘 (PostgreSQL driver), **sqlight** 🪶 (SQLite bindings), **shork** 🐬 (MySQL/MariaDB driver), **cake** 🐘🪶🐬 (multi-dialect SQL builder), **squirrel** 🐘 and **sqlode** 🐘🪶🐬 (SQL→Gleam codegen) cover query construction and execution. PostgreSQL migration tooling has two standalone options (**cigogne**, **gorrion**); SQLite migration remains a gap — every standalone candidate is either superseded, stale, or has an outdated `gleam_stdlib` cap. The first batteries-included approximation of an ORM ships inside the **glimr** framework's [db module](#glimr-db-) — schema-diff migrations + typed query repositories — but it's not a standalone library.

Snapshot: **2026-04-26**.

| Layer | BEAM | Status |
| --- | --- | --- |
| **[PostgreSQL Drivers](#postgresql-drivers)** | [pog](#pog-) 🐘 (driver with pooling) | 🟩🟩 active |
| **[SQLite Bindings](#sqlite-bindings)** | [sqlight](#sqlight-) 🪶 (low-level bindings) | 🟩🟩 active |
| **[MySQL Drivers](#mysql-drivers)** | [shork](#shork-) 🐬 (mysql-otp wrapper, pog-style API) | 🟨 stale, GitHub mirror archived ([moved](#shork-) to Codeberg) |
| **[SQL Query Builders](#sql-query-builders)** | [cake](#cake-) 🐘🪶🐬 (multi-dialect composer) | 🟩🟩 active |
| **[SQL Code Generators](#sql-code-generators)** | [squirrel](#squirrel-) 🐘, [sqlode](#sqlode-) 🐘🪶🐬 | 🟩🟩 active |
| **[Migrations](#migration-tools)** | [cigogne](#cigogne-) 🐘, [gorrion](#gorrion-) 🐘 (ecto-like) | 🟩 active (PG only) |
| **SQLite migrations (standalone)** | None recommended ([disregarded](#disregarded-migration-tools)) | 🟥 Gap |
| **[Framework-bundled DB](#orms--higher-level-abstractions)** | [glimr db](#glimr-db-) 🐘🪶 (schema-diff migrations + typed query repos) | 🟩🟩 active (framework-only) |
| **Standalone ORM** | ⬜ None found | Gap |

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
- [mysql](https://packages.gleam.run/?search=mysql)
- [migration](https://packages.gleam.run/?search=migration)
- [orm](https://packages.gleam.run/?search=orm)
- [ecto](https://packages.gleam.run/?search=ecto)

Framework-bundled DB modules were surfaced from the [Web apps review](./web-and-http/web-apps.md) — currently only [glimr](#glimr-db-) ships a substantive DB layer.

## Categories

### PostgreSQL Drivers

#### pog 🐘
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

#### sqlight 🪶
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

### MySQL Drivers

#### shork 🐬
[repo](https://github.com/ninanomenon/shork) · [leaderboard ↓](#leaderboard)

MySQL / MariaDB driver for Gleam, built on top of Erlang's [mysql-otp](https://github.com/mysql-otp/mysql-otp). API is heavily inspired by [pog](#pog-) — same builder pattern (`shork.query` → `shork.parameter` → `shork.returning` → `shork.execute`), same row-decoder style. Used as the MySQL execution backend by [cake](#cake-) and [sqlode](#sqlode-).

> [!CAUTION]
> The README announces the project has **moved to Codeberg** ([codeberg.org/ninanonemon/shork](https://codeberg.org/ninanonemon/shork)); the GitHub repo is now a frozen mirror. Last GitHub commit was 2025-10-12, ~6.5 months before snapshot. Hex is still publishing as `shork` (latest 1.4.0), but future releases will originate from Codeberg.

| Criterion | [shork](https://github.com/ninanomenon/shork) |
| --- | --- |
| Stars | 9★ · 🟥 |
| License | LGPL-3.0 · 🟥 (weak copyleft — flag for permissive-only shops) |
| Target | ☎️ BEAM (via mysql-otp NIF) |
| Deps | 4 (`gleam_erlang`, `gleam_otp`, `gleam_time`, `mysql`) |
| Gleam compat | `>= 0.65.0 and < 1.0.0` · 🟥 (capped below current stdlib — risky) |
| Maintenance | 🟨 (last GitHub commit 2025-10-12, ~6.5 months vs 2026-04-26; mirror frozen) |
| Age | ~16 months (Dec 2024) · 🟩 |
| README maturity | 🟩 (clear tagline + worked example + dev/docker setup; relegates further docs to hexdocs) |
| Idiomaticity | 🟩 (pog-style typed builder + decoder) |
| Latest version | **1.4.0** (Hex) |
| Issues | ⬜ (Issues tab disabled on GitHub mirror; see Codeberg) |

**Key features:**
- MySQL & MariaDB support via Erlang's `mysql-otp`
- Builder API mirrors `pog`: parameterized queries, typed `returning` decoders
- Connection lifecycle: `default_config → user → password → database → connect`
- Synchronous execute path; pooling delegated to OTP supervisor design

```gleam
import shork
import gleam/dynamic/decode

pub fn main() {
  let connection =
    shork.default_config()
    |> shork.user("root")
    |> shork.password("strong_password")
    |> shork.database("poke")
    |> shork.connect

  let row_decoder = {
    use name <- decode.field(0, decode.string)
    use hp   <- decode.field(1, decode.int)
    decode.success(#(name, hp))
  }

  let assert Ok(response) =
    shork.query("select name, hp from pokemon where id = ?")
    |> shork.parameter(shork.int(1))
    |> shork.returning(row_decoder)
    |> shork.execute(connection)
}
```

> [!NOTE]
> Two other MySQL packages surfaced in the [`mysql`](https://packages.gleam.run/?search=mysql) search but were skipped:
> - **[gmysql](https://github.com/VioletBuse/gmysql)** — last release ~1 year before snapshot; superseded in active use by shork (which is what cake/sqlode wire to).
> - **[parrot](https://github.com/daniellionel01/parrot)** — codegen tool (typed SQL across SQLite/PG/MySQL), not a driver. Worth a follow-up review under [SQL Code Generators](#sql-code-generators).

### SQL Query Builders

#### cake 🐘🪶🐬
[repo](https://github.com/inoas/gleam-cake) · [🥇](#leaderboard)

Multi-dialect SQL composer. Builds prepared-statement-safe SQL fragments for PostgreSQL, SQLite, MariaDB, and MySQL. Composes queries — does **not** execute. Pairs with execution adapters (`pog`, `sqlight`, `shork`, `gmysql`).

| Criterion | [cake](https://github.com/inoas/gleam-cake) |
| --- | --- |
| Stars | 124★ · 🟩 |
| License | MPL-2.0 · 🟥 (weak copyleft, file-level — flag for MIT/Apache shops) |
| Target | ☎️ BEAM + JS · 🐘🪶🐬 (PG / SQLite / MariaDB & MySQL via shork) |
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

#### Comparison

| Tool | Dialects | Style | Driver coupling | Output modes | Stars | Latest | Maint |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [squirrel](#squirrel-) | 🐘 PostgreSQL 16+ | `.sql` files → typed Gleam decoders | tight (pog) | single (typed decoders) | 630★ | — | 🟩 (5 wk) |
| [sqlode](#sqlode-) | 🐘 PostgreSQL · 🪶 SQLite · 🐬 MySQL 8 | sqlc-style schema + queries → typed Gleam | driver-agnostic at codegen (pog/sqlight/shork) | raw (SQL+params) + native (full binding) | 3★ | 0.9.0 | 🟩🟩 (snapshot day) |

**Pick:** **squirrel** for PostgreSQL — established (FOSDEM-talked, 630★), proven, tight pog integration. **sqlode** if you need SQLite or MySQL codegen, or want raw-mode output for custom adapters; brand new (Apr 2026), unproven but comprehensive.

#### squirrel 🐘
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

#### sqlode 🐘🪶🐬
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

Two PostgreSQL migration tools are recommendable. SQLite migrations are a current gap — every published candidate is disregarded for cause (see [Disregarded](#disregarded-migration-tools) below).

#### Comparison

| Tool | Dialect | Style | Hash check | Library-bundled | Down migrations | CLI | Stars | Latest | Maint |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [cigogne](#cigogne-) | 🐘 PostgreSQL | SQL up/down | ✅ | ✅ | ✅ | ✅ | 43★ | 5.0.6 | 🟩🟩 (3 days) |
| [gorrion](#gorrion-) | 🐘 PostgreSQL | Ecto-style API | ❌ | ❌ | optional | ❌ | 1★ | 0.2.0-rc1 | 🟩🟩 (1 wk) |

**Pick:** **cigogne** for production PostgreSQL — mature, hash-verified, library-bundled migrations, real CLI. **gorrion** if you want an Ecto-style programmatic API and don't need a CLI; brand new (Mar 2026), one-month-old project, RC release.

#### cigogne 🐘
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

#### gorrion 🐘
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

#### Disregarded migration tools

The following migration packages turned up in the search but are **not recommended**. They are listed only so future reviewers know why they were skipped.

| Package | Dialect | Reason disregarded |
| --- | --- | --- |
| [migrant](https://github.com/aosasona/migrant) | 🪶 SQLite | `gleam_stdlib` constraint capped at `< 1.0.0` — incompatible with current stdlib. Last commit 2025-11-30 (~5 months stale). License inconsistency (repo MIT, gleam.toml Apache-2.0). |
| [storch](https://github.com/VioletBuse/storch) | 🪶 SQLite | Repo README states it is **superseded by `feather`**. Last commit 2024-06-28 (~22 months stale). |
| [feather](https://hex.pm/packages/feather) | 🪶 SQLite | Storch's named successor — but itself stale: last release 2024-08-14 (~20 months), no commits since. Same author as storch. |
| [akaridb](https://hex.pm/packages/akaridb) | ❓ unspecified | **No public source repository.** Hex package has no repo link in metadata, hexdocs, or footer. Cannot audit, fork, or contribute. Last release 2024-08-11. |

> [!WARNING]
> **SQLite migration is currently a gap in the Gleam ecosystem.** Every published SQLite migration package is either superseded, stale, or has an outdated `gleam_stdlib` cap. For new SQLite projects today, run migrations from outside Gleam (sqlite shell scripts, Make targets) or roll your own thin runner on top of [sqlight](#sqlight-).

### ORMs & Higher-Level Abstractions

No **standalone** ORM exists for Gleam. The standalone library philosophy is:
- **Minimal** — Gleam prefers explicit SQL + decoders to auto-magic ORMs.
- **Control** — Manage queries and result mapping yourself.
- **Performance** — No hidden N+1 queries or implicit joinloading.

For feature-rich abstractions, consider:
- **Nested decoders** — Build reusable decoder functions to map rows to typed structures.
- **SQL codegen + drivers** — Use `squirrel` 🐘 (PostgreSQL) or `sqlode` 🐘🪶🐬 (multi-dialect) to generate type-safe decoders from SQL files + `pog`/`sqlight`/`shork` for execution.
- **Query builders** — Use `cake` 🐘🪶🐬 to compose dialect-portable SQL fragments at runtime, then execute via your driver of choice.
- **Framework-bundled DB layer** — Use [glimr db](#glimr-db-) 🐘🪶 if you're already building on the [glimr](#glimr-db-) framework — closest thing to an ORM in the ecosystem (schema-diff migrations + typed query repository codegen). Not consumable as a standalone library.

#### glimr db 🐘🪶
[code](https://github.com/glimr-org/framework/tree/main/src/glimr/db) · [migrations docs](https://github.com/glimr-org/glimr#migrations) · [leaderboard ↓](#leaderboard)

The database module shipped inside the [glimr](https://github.com/glimr-org/glimr) batteries-included web framework. Provides a unified `db.DbPool` type backed by either PostgreSQL (via [`pog`](#pog-) through `glimr_postgres`) or SQLite (via [`sqlight`](#sqlight-) through `glimr_sqlite`), schema-diff migrations, and a CLI-driven typed query repository generator. The closest thing to an "ORM" the Gleam ecosystem currently has — but only consumable inside a glimr application, not as an à la carte library.

> [!IMPORTANT]
> **Framework-only, not a standalone library.** Code lives at `glimr-org/framework/src/glimr/db` and is published on Hex as part of `glimr`, but the schemas/migrations/queries pipeline (`./glimr make_model`, `./glimr db_gen`, `./glimr setup_database`) is wired through the framework's CLI and project layout. You can't `gleam add glimr` from a non-glimr project and use just the db module. The leaderboard scores it as a framework component, not a drop-in library.

| Criterion | [glimr db](https://github.com/glimr-org/framework/tree/main/src/glimr/db) |
| --- | --- |
| Stars | 84★ (framework repo) · 🟨 (template repo `glimr-org/glimr` has 169★) |
| License | MIT (framework repo) · 🟩 |
| Target | ☎️ BEAM (PG via pog, SQLite via sqlight) · 🐘🪶 |
| Gleam compat | `>= 0.44.0 and < 2.0.0` (framework `gleam.toml`) · 🟩 |
| Maintenance | 🟩🟩 (framework last commit 2026-04-23, 3 days vs 2026-04-26; template same day) |
| Age | ~5 months (Dec 2025) · 🟨 |
| README maturity | 🟩🟩 (full guide: schema DSL, column-type table, modifiers, migrations diffing, query naming convention, repository codegen, connection pooling, multi-database setup) |
| Idiomaticity | 🟩 (typed `DbPool`, generated typed query functions, `_or_fail` continuation variants for HTTP handlers) |
| Latest version | **1.3.3** (framework on Hex), **1.0.0** (template) |
| Issues | 1 open (framework), 1 open (template) |

**Key features:**
- **Schema DSL** — `glimr/db/schema` defines tables, columns (with PG/SQLite-aware types), modifiers (`nullable`, `default_*`, `array`, `on_delete`/`on_update`), enums (generates a Gleam custom type with `*_to_string`/`*_from_string`), indexes.
- **Automatic migrations** — diffs current schema definitions against a stored snapshot and emits driver-specific SQL. No hand-written `up`/`down` files.
- **Query repository codegen** — `./glimr make_model user` generates `create.sql` / `find.sql` / `list.sql` / `update.sql` / `delete.sql` from the schema; `./glimr db_gen` compiles all `.sql` files into typed Gleam functions. Each query yields **four** variants: `find` (returns `Result`), `find_wc` (in-transaction, takes `Connection`), `find_or_fail` (continuation-style for HTTP handlers), `find_or_fail_wc` (in-transaction continuation).
- **Naming convention** — file prefix `list` / `list_*` returns `List(T)`; everything else returns a single row.
- **Multi-database support** — multiple named connections in `config/database.toml`, can mix PG and SQLite in one app.
- **Driver-agnostic API** — application code uses `DbPool`; swap `glimr_postgres` ↔ `glimr_sqlite` without changing query call sites.
- **Connection pooling, transactions** with automatic deadlock retry.

```gleam
// src/database/main/models/user/user_schema.gleam
import glimr/db/schema

pub const table_name = "users"

pub fn definition() {
  schema.table(table_name, [
    schema.id(),
    schema.foreign("organization_id", "organizations")
      |> schema.nullable()
      |> schema.on_delete(schema.Cascade),
    schema.string("email"),
    schema.enum("role", ["admin", "editor", "viewer"]),
    schema.unix_timestamps(),
  ])
  |> schema.indexes([
    schema.unique(["email"]),
  ])
}
```

```gleam
// In a controller — _or_fail short-circuits to a 404/503/500 Response on error
import database/models/user/gen/user
import glimr/http/response.{type Response}

pub fn show(ctx: Context(App), id: Int) -> Response {
  use user <- user.find_or_fail(ctx.app.db, id)

  user_show.render(user: user)
  |> response.string_tree(200)
}
```

### Related Work

- **[gleam/http](./web-and-http/web-apps.md#http)** — Core HTTP types, often paired with database tools for REST APIs.
- **[lustre](./web-and-http/web-apps.md#lustre)** — Web framework; Lustre Server Components often interact with databases on the BEAM side.

## Leaderboard

Disregarded packages (akaridb, storch, migrant, feather) are excluded — see [Disregarded migration tools](#disregarded-migration-tools) for reasons. **glimr db** is included for reference but flagged as framework-bundled (not a standalone library).

| Position | Award | Repo | Dialect | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [lpil/pog](https://github.com/lpil/pog) | 🐘 | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **10** |
| 2 | 🥈 | [giacomocavalieri/squirrel](https://github.com/giacomocavalieri/squirrel) | 🐘 | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **9** |
| 2 | 🥈 | [lpil/sqlight](https://github.com/lpil/sqlight) | 🪶 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩 | **9** |
| 4 | 🥉 | [Billuc/cigogne](https://github.com/Billuc/cigogne) | 🐘 | 🟨 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩🟩 | 🟩 | **8** |
| 5 | — | [glimr-org/framework (db)](https://github.com/glimr-org/framework/tree/main/src/glimr/db) † | 🐘🪶 | 🟨 | 🟩 | 🟩 | 🟩🟩 | 🟨 | 🟩🟩 | 🟩 | **7** |
| 5 | — | [inoas/gleam-cake](https://github.com/inoas/gleam-cake) | 🐘🪶🐬 | 🟩 | 🟥 | 🟨 | 🟩🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **7** |
| 7 | — | [nao1215/sqlode](https://github.com/nao1215/sqlode) | 🐘🪶🐬 | 🟥 | 🟩 | 🟩 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **5** |
| 8 | — | [davecaos/gorrion](https://github.com/davecaos/gorrion) | 🐘 | 🟥 | 🟩 | 🟨 | 🟩🟩 | 🟥 | 🟩🟩 | 🟩 | **4** |
| 9 | — | [ninanomenon/shork](https://github.com/ninanomenon/shork) ‡ | 🐬 | 🟥 | 🟥 | 🟥 | 🟨 | 🟩 | 🟩 | 🟩 | **0** |

† **glimr db** is bundled inside the glimr web framework, not consumable as a standalone library. Score is informational; treat it as a framework feature, not a library to compare with the others. See the [glimr db](#glimr-db-) entry.

‡ **shork** GitHub mirror is frozen — repo has [moved to Codeberg](https://codeberg.org/ninanonemon/shork). Hex package (`shork`, 1.4.0) is still the recommended MySQL/MariaDB driver in practice (used by cake and sqlode), but the GitHub-visible activity score does not reflect ongoing Codeberg development.

**Summary:**
- **Drivers & bindings:** **pog** 🐘 leads overall — active maintenance, mature pooling, comprehensive docs. **sqlight** 🪶 is the SQLite low-level binding of choice and received a same-day commit at snapshot time. **shork** 🐬 fills the MySQL/MariaDB slot but the GitHub mirror has been frozen since Oct 2025 in favour of Codeberg, the gleam_stdlib cap is `< 1.0.0`, and the LGPL-3.0 license is a caveat for permissive-only shops — usable today (cake and sqlode wire to it) but watch for releases on Codeberg, not GitHub.
- **Codegen:** **squirrel** 🐘 is the established SQL→Gleam code generator (PostgreSQL only, 630★, FOSDEM-talked). **sqlode** 🐘🪶🐬 is a brand-new (Apr 2026) multi-dialect challenger with a comprehensive README — promising but unproven.
- **Query builder:** **cake** 🐘🪶🐬 is the only standalone multi-dialect composer (PG/SQLite/MariaDB/MySQL, BEAM + JS) with active development. MPL-2.0 license is the main caveat for shops standardising on permissive licenses.
- **Migrations:** **cigogne** 🐘 leads PostgreSQL migrations (mature, 5.0.6, hash-verified, library-bundled). **gorrion** 🐘 is an Ecto-style alternative — well-documented but one month old. **SQLite migration is a standalone gap**: every standalone candidate (migrant, storch, feather, akaridb) is disregarded — see the [Disregarded](#disregarded-migration-tools) table. **glimr db** 🐘🪶 covers SQLite migrations *if* you adopt the glimr framework.
- **Framework-bundled:** **glimr db** 🐘🪶 ships the Gleam ecosystem's first batteries-included DB layer (schema-diff migrations + typed query repository codegen) — the closest thing to an ORM, but framework-only.
- **Notable patterns:** Four of nine repos (cigogne, sqlode, cake, glimr) had a commit within 3 days of snapshot — heavy churn in the Gleam DB space right now. Zero open issues on **gorrion** and **sqlode** is a positive signal balanced by their newness; **shork** disables the GitHub Issues tab entirely (issue tracking is on Codeberg).

[How scores are calculated →](#scoring-dimensions)
