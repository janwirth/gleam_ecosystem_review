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
   - [Query Builders](#query-builders) — [squirrel](#squirrel)
   - [Migration Tools](#migration-tools)
   - [ORMs & Higher-Level Abstractions](#orms--higher-level-abstractions)
   - [Related Work](#related-work)
4. [Leaderboard](#leaderboard)

## Summary

The Gleam database story is thin but growing. PostgreSQL has **pog** (driver), **sqlight** (SQLite bindings), and **squirrel** (query builder). Migration tooling and ORMs are gaps.

Snapshot: **2026-04-22**.

| Layer | BEAM | Status |
| --- | --- | --- |
| **[PostgreSQL Drivers](#postgresql-drivers)** | [pog](#pog) (driver with pooling) | 🟩🟩 active |
| **[SQLite Bindings](#sqlite-bindings)** | [sqlight](#sqlight) (low-level bindings) | 🟩 maintained |
| **[Query Builders](#query-builders)** | [squirrel](#squirrel) (composable builders) | 🟩 maintained |
| **[Migrations](#migration-tools)** | ⬜ No standard tool found | Gap |
| **[ORMs](#orms--higher-level-abstractions)** | ⬜ No ORM found | Gap |

> [!IMPORTANT]
> All database tools here target **BEAM only**. No cross-target (JS) database libraries exist yet.

## Research Method

### Scoring Dimensions

Same rubric as [tools-for-building-web-apps.md](./tools-for-building-web-apps.md#scoring-dimensions). Recap:

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
[repo](https://github.com/gleam-lang/pog) · [🥇](#leaderboard)

Official PostgreSQL driver for Gleam. Provides connection pooling, parameterized queries, and type-safe result decoding. No ORM — you write SQL directly and decode results with custom decoders.

| Criterion | [pog](https://github.com/gleam-lang/pog) |
| --- | --- |
| Stars | 81★ · 🟩 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM |
| Deps | 5 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 🟩🟩 (last commit 2026-04-18, active issue triaging) |
| Age | ~2 years (Apr 2024) · 🟩 |
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
import gleam/list
import gleam/option.{Some}

pub fn get_user(db: pog.Connection, id: Int) -> Result(User, pog.QueryError) {
  pog.query(
    "SELECT id, name, email FROM users WHERE id = $1",
  )
  |> pog.parameter(pog.int(id))
  |> pog.returning(decode_user)
  |> pog.execute(db)
  |> result.try(fn(rows) {
    case rows {
      [user] -> Ok(user)
      _ -> Error(pog.NoRowsReturned)
    }
  })
}

fn decode_user(row: List(Any)) -> Result(User, Nil) {
  case row {
    [id, name, email] -> {
      Ok(User(
        id: int.parse(dynamic.from(id)),
        name: string.parse(dynamic.from(name)),
        email: string.parse(dynamic.from(email)),
      ))
    }
    _ -> Error(Nil)
  }
}
```

### SQLite Bindings

#### sqlight
[repo](https://github.com/gleam-lang/sqlight) · [🥈](#leaderboard)

Official SQLite bindings for Gleam. Lower-level than pog — you manage connections directly and decode results. Good for embedding SQLite in Gleam applications or when PostgreSQL isn't needed.

| Criterion | [sqlight](https://github.com/gleam-lang/sqlight) |
| --- | --- |
| Stars | 63★ · 🟨 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM |
| Deps | 2 |
| Gleam compat | `>= 0.32 and < 2.0` · 🟩 |
| Maintenance | 🟩 (last commit 2026-03-05, ~6 weeks) |
| Age | ~3 years (Apr 2023) · 🟩🟩 |
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

### Query Builders

#### squirrel
[repo](https://github.com/gleam-lang/squirrel) · [🥉](#leaderboard)

Composable query builder for SQL. Works with any backend (PostgreSQL, SQLite, MySQL, etc.) by building SQL strings. Useful when you want to compose queries dynamically or avoid string concatenation.

| Criterion | [squirrel](https://github.com/gleam-lang/squirrel) |
| --- | --- |
| Stars | 23★ · 🟨 |
| License | Apache-2.0 · 🟩 |
| Target | ☎️ BEAM |
| Deps | 2 |
| Gleam compat | `>= 0.44 and < 2.0` · 🟩 |
| Maintenance | 🟩 (last commit 2025-11-02, ~6 months) |
| Age | ~1.5 years (Oct 2024) · 🟩 |
| README maturity | 🟩🟩 (comprehensive guide with SELECT, INSERT, UPDATE, DELETE examples) |
| Idiomaticity | 🟩 (builder pattern, typed) |

**Key features:**
- Builder API for SELECT, INSERT, UPDATE, DELETE
- Automatic parameterization
- Works database-agnostic (you choose the driver)
- Composable — chain filters, joins, ordering

```gleam
import squirrel as sq

pub fn find_active_users() -> String {
  sq.select([sq.col("id"), sq.col("name")])
  |> sq.from("users")
  |> sq.where(sq.col("active") |> sq.eq(sq.bool(True)))
  |> sq.order_by(sq.col("created_at"), sq.Desc)
  |> sq.to_string
}

// Generates: SELECT id, name FROM users WHERE active = true ORDER BY created_at DESC
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
- **Query builders + drivers** — Combine `squirrel` for query building + `pog`/`sqlight` for execution.

### Related Work

- **[gleam/http](./tools-for-building-web-apps.md#http)** — Core HTTP types, often paired with database tools for REST APIs.
- **[lustre](./tools-for-building-web-apps.md#lustre)** — Web framework; Lustre Server Components often interact with databases on the BEAM side.

## Leaderboard

| Position | Award | Repo | ★ | Lic | Compat | Maint | Age | README | Idiom | Score |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 🥇 | [gleam-lang/pog](https://github.com/gleam-lang/pog) | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩🟩 | 🟩 | **10** |
| 2 | 🥈 | [gleam-lang/sqlight](https://github.com/gleam-lang/sqlight) | 🟨 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | 🟩 | **8** |
| 3 | 🥉 | [gleam-lang/squirrel](https://github.com/gleam-lang/squirrel) | 🟨 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩🟩 | 🟩 | **9** |

**Summary:** pog leads with active maintenance and comprehensive docs. sqlight is the established SQLite choice. squirrel fills the query-building gap. No migrations or ORM yet.

[How scores are calculated →](#scoring-dimensions)
