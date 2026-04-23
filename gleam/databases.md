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
   - [SQL Code Generators](#sql-code-generators) — [squirrel](#squirrel)
   - [Migration Tools](#migration-tools)
   - [ORMs & Higher-Level Abstractions](#orms--higher-level-abstractions)
   - [Related Work](#related-work)
4. [Leaderboard](#leaderboard)

## Summary

Gleam database tools span drivers, bindings, and developer experience. **pog** (PostgreSQL driver), **sqlight** (SQLite bindings), and **squirrel** (SQL→Gleam codegen) cover query execution. Migration tooling and ORMs are gaps.

Snapshot: **2026-04-22**.

| Layer | BEAM | Status |
| --- | --- | --- |
| **[PostgreSQL Drivers](#postgresql-drivers)** | [pog](#pog) (driver with pooling) | 🟩🟩 active |
| **[SQLite Bindings](#sqlite-bindings)** | [sqlight](#sqlight) (low-level bindings) | 🟩 maintained |
| **[SQL Code Generators](#sql-code-generators)** | [squirrel](#squirrel) (SQL→Gleam codegen) | 🟩🟩 active |
| **[Migrations](#migration-tools)** | ⬜ No standard tool found | Gap |
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
- [sql](https://packages.gleam.run/?search=sql)
- [postgres](https://packages.gleam.run/?search=postgres)
- [sqlite](https://packages.gleam.run/?search=sqlite)
- [orm](https://packages.gleam.run/?search=orm)

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
| Maintenance | 🟩 (last commit 2026-03-09, ~6 weeks) |
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
| Maintenance | 🟩🟩 (last commit 2026-04-18, same day as snapshot) |
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
| Maintenance | 🟩 (last commit 2026-03-19, ~1 month) |
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

### Migration Tools

No standard Gleam migration tool found. Workarounds:
- **Manual SQL files** — Source control `.sql` files, run them manually or via shell script.
- **Gleam CLI** — Write migration logic in Gleam and invoke via `gleam run` (no framework support yet).
- **External tools** — Use Flyway, Migrate, or Alembic and call them from Gleam shell commands.

### ORMs & Higher-Level Abstractions

No ORM exists for Gleam yet. The philosophy is:
- **Minimal** — Gleam prefers explicit SQL + decoders to auto-magic ORMs.
- **Control** — Manage queries and result mapping yourself.
- **Performance** — No hidden N+1 queries or implicit joinloading.

For feature-rich abstractions, consider:
- **Nested decoders** — Build reusable decoder functions to map rows to typed structures.
- **SQL codegen + drivers** — Use `squirrel` to generate type-safe decoders from SQL files + `pog`/`sqlight` for execution.

### Related Work

- **[gleam/http](./web-and-http/web-apps.md#http)** — Core HTTP types, often paired with database tools for REST APIs.
- **[lustre](./web-and-http/web-apps.md#lustre)** — Web framework; Lustre Server Components often interact with databases on the BEAM side.

## Leaderboard

| Position | Award | Repo | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [lpil/pog](https://github.com/lpil/pog) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | **10** |
| 2 | 🥈 | [giacomocavalieri/squirrel](https://github.com/giacomocavalieri/squirrel) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **9** |
| 2 | 🥈 | [lpil/sqlight](https://github.com/lpil/sqlight) | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩🟩 | 🟩 | 🟩 | **9** |

**Summary:** pog leads with active maintenance and comprehensive docs. sqlight and squirrel are tied: sqlight for low-level SQLite control, squirrel for SQL-first development (code generation, zero decoder drift). No migrations or ORM standard yet.

[How scores are calculated →](#scoring-dimensions)
