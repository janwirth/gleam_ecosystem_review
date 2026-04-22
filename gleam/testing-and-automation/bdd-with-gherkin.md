# BDD with Gherkin across ecosystems

> Cross-ecosystem survey relevant to BEAM/Gleam developers picking a BDD path.

**Snapshot 2026-04-22** — metrics from live GitHub web UI only (no API, no clone). Star counts, last-commit dates, and open-issue counts reflect what a logged-out visitor sees today.

## Table of Contents

1. [What is Gherkin?](#what-is-gherkin)
2. [The BDD pipeline](#the-bdd-pipeline)
3. [Ecosystem reference implementations](#ecosystem-reference-implementations)
4. [The polyglot Gherkin parser](#the-polyglot-gherkin-parser)
5. [Status in the BEAM ecosystem](#status-in-the-beam-ecosystem)
6. [Status in Gleam specifically](#status-in-gleam-specifically)
7. [Recommendation for Gleam developers](#recommendation-for-gleam-developers)

## What is Gherkin?

**Gherkin** is a plain-text, line-oriented DSL for describing software behaviour in examples. A `.feature` file looks like this:

```gherkin
Feature: Shopping cart totals

  Scenario: Adding two items
    Given I have an empty cart
    When I add a "Widget" costing 10
    And I add a "Gadget" costing 5
    Then the total should be 15
```

Three properties make it interesting:

- **Business-readable.** Non-programmers can read (and often write) scenarios.
- **Executable.** Each `Given`/`When`/`Then` line is matched to a code block ("step definition") in a host language, which runs during the test suite.
- **Polyglot.** The same feature file can drive tests in Ruby, JS, Java, Python, .NET, Go, Rust, PHP, Elixir, etc. — because the parser and the runner are separate concerns.

Gherkin is the *specification language*. **Cucumber** is the family of runners that execute it. Together they implement **Behaviour-Driven Development (BDD)** — the practice of writing specs first, discussing them with stakeholders, and then using them as the source of truth for the test suite.

## The BDD pipeline

Every Cucumber-family tool has the same three layers:

```
┌─────────────────────────┐
│  .feature file          │  ← Gherkin spec (plain text, business-owned)
└───────────┬─────────────┘
            │ parsed by Gherkin parser
            ▼
┌─────────────────────────┐
│  AST / message stream   │  ← Language-neutral intermediate representation
└───────────┬─────────────┘
            │ matched against…
            ▼
┌─────────────────────────┐
│  Step definitions       │  ← Host-language glue code (Ruby/Python/JS/…)
│  (regex + lambda)       │
└───────────┬─────────────┘
            │ executed by…
            ▼
┌─────────────────────────┐
│  Test runner            │  ← Native test harness (RSpec/Jest/pytest/…)
└─────────────────────────┘
```

When you pick a BDD library for a language, you are really picking three things:

1. **A Gherkin parser** (usually the official `cucumber/gherkin` port for that language).
2. **A step-binding layer** (how regex/cucumber-expression matchers map to functions).
3. **A test runner** (standalone like `cucumber-js`, or integrated with an existing runner like `pytest-bdd`).

Some tools bundle all three (e.g. `cucumber-ruby`). Some only do parsing (e.g. `cucumber_gherkin` for Elixir). Some plug into an existing runner (e.g. `pytest-bdd`, `cabbage` → ExUnit, `dream_test` → its own + ExUnit-style).

## Ecosystem reference implementations

One row per ecosystem. This is the canonical or most-active BDD tool for each.

| Ecosystem | Tool | Stars | Last commit | Issues | License | Activity | README |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Ruby** | [cucumber/cucumber-ruby](https://github.com/cucumber/cucumber-ruby) | 5.2k · 🟩🟩 | 2026-04-22 · 🟩🟩 | 22 | MIT · 🟩 | 🟩🟩 | 🟩🟩 (reference impl) |
| **JavaScript / TS** | [cucumber/cucumber-js](https://github.com/cucumber/cucumber-js) | 5.3k · 🟩🟩 | 2026-04-19 · 🟩🟩 | 42 | MIT · 🟩 | 🟩🟩 | 🟩🟩 |
| **Java / JVM** | [cucumber/cucumber-jvm](https://github.com/cucumber/cucumber-jvm) | 2.8k · 🟩🟩 | 2026-04-20 · 🟩🟩 | 51 | MIT · 🟩 | 🟩🟩 | 🟩🟩 |
| **Python** (standalone) | [behave/behave](https://github.com/behave/behave) | 3.5k · 🟩🟩 | 2026-04-16 · 🟩🟩 | 51 | BSD-2-Clause · 🟩 | 🟩🟩 | 🟩🟩 |
| **Python** (pytest plugin) | [pytest-dev/pytest-bdd](https://github.com/pytest-dev/pytest-bdd) | 1.4k · 🟩🟩 | 2026-04-06 · 🟩🟩 | 61 | MIT · 🟩 | 🟩🟩 | 🟩🟩 |
| **.NET** | [reqnroll/Reqnroll](https://github.com/reqnroll/Reqnroll) | 742 · 🟩🟩 | 2026-03-23 · 🟩🟩 | 22 | BSD-3-Clause · 🟩 | 🟩🟩 | 🟩🟩 (migration guide, docs site) |
| **Go** | [cucumber/godog](https://github.com/cucumber/godog) | 2.6k · 🟩🟩 | 2026-04-14 · 🟩🟩 | 66 | MIT · 🟩 | 🟩🟩 | 🟩🟩 |
| **Rust** | [cucumber-rs/cucumber](https://github.com/cucumber-rs/cucumber) | 708 · 🟩🟩 | 2026-04-14 · 🟩🟩 | 22 | MIT + Apache-2.0 · 🟩 | 🟩🟩 | 🟩🟩 |
| **PHP** | [Behat/Behat](https://github.com/Behat/Behat) | 4.0k · 🟩🟩 | ~2026-04-19 (v3.31.0) · 🟩🟩 | 51 | MIT · 🟩 | 🟩🟩 | 🟩🟩 |
| **Elixir** (ExUnit) | [cabbage-ex/cabbage](https://github.com/cabbage-ex/cabbage) | 153 · 🟩 | 2025-03-14 · 🟩 | 18 | MIT · 🟩 | 🟩 | 🟩 |
| **Elixir** (standalone) | [meadery/white-bread](https://github.com/meadery/white-bread) | 229 · 🟩🟩 | 2020-11-04 · 🟥 | 16 | MIT · 🟩 | 🟥 (looking for maintainers) | 🟩 |
| **Gleam** | [TrustBound/dream_test](https://github.com/TrustBound/dream_test) | 5 · 🟥 | 2026-02-03 · 🟩 | 1 | MIT · 🟩 | 🟩 | 🟩🟩 |
| **Haskell** | [sol/cucumber-haskell](https://github.com/sol/cucumber-haskell) | 9 · 🟥 | 2014-03-02 · 🟥 | 0 | unclear · 🟥 | 🟥 | 🟥 (stub "effort") |
| **Erlang** | — | — | — | — | — | ⬜ | No current active native implementation; use via Elixir interop or Cucumber-JVM FFI |

**Legend:** 🟩🟩 strong / thriving · 🟩 healthy · 🟥 stale / minor · ⬜ unknown or absent

### Notes per ecosystem

- **Ruby, JS, JVM, Python, PHP, Go, Rust, .NET**: first-tier support. Active releases, daily-to-weekly commits, full docs, ecosystem plugins (reports, parallelism, IDE integration).
- **Reqnroll** is the successor to SpecFlow. SpecFlow's repos were *deleted* on 2025-01-01 after end-of-life; Reqnroll forked the codebase before shutdown and is now the canonical .NET BDD choice.
- **behave vs pytest-bdd**: Two Pythonic approaches. `behave` is a standalone runner faithful to Cucumber-Ruby; `pytest-bdd` is a plugin that reuses pytest fixtures, meaning you write fewer separate tools but give up some Gherkin features (e.g. tags are implemented as pytest markers). Both active.
- **cabbage** (Elixir) is the currently-active Elixir option. Compile-time translation of `.feature` files to ExUnit tests. Last commit 2025-03-14 (~13 months before snapshot) — healthy but not daily.
- **white-bread** (Elixir) is historically the best-known Elixir BDD tool but has been unmaintained since 2020-11-04 (5+ years). README notes it is "looking for maintainers." Do not pick for new projects.
- **dream_test** (Gleam) is the only Gleam-native BDD tool at snapshot. Covered in depth [below](#status-in-gleam-specifically).
- **Haskell**: `sol/cucumber-haskell` last touched 2014, 9 stars — effectively abandoned. `chuchu` and `abacate` on Hackage are similarly stale. Haskell BDD is thin — most Haskell teams use `hspec` + property tests instead.
- **Erlang**: no active Cucumber-ports found. Best path is Elixir interop (cabbage) from an Erlang project, or treat BDD as Elixir-layer tooling.

## The polyglot Gherkin parser

Separate from the runners, there is one **canonical Gherkin parser** that is ported to 12 languages and maintained in a single monorepo:

| Repo | [cucumber/gherkin](https://github.com/cucumber/gherkin) |
| --- | --- |
| Stars | 323 · 🟩🟩 |
| Last commit | 2026-04-22 · 🟩🟩 |
| Open issues | 42 |
| License | MIT · 🟩 |
| Target languages | .NET, Java, JavaScript, Ruby, Go, Python, C, C++, Objective-C, Perl, PHP, Dart |
| Not yet shipped | Rust (wishlisted), Elixir (shipped separately as [`cucumber_gherkin`](https://hex.pm/packages/cucumber_gherkin) v38, published by cukebot) |
| README maturity | 🟩🟩 (per-language install tables, i18n keyword list, contributor guide) |

All language ports are generated from a shared grammar definition and kept version-locked. That's why a `.feature` file behaves identically whether you run it through cucumber-ruby or cucumber-js.

Independent parsers exist but are niche:

- **[Ajwah/ex-gherkin](https://github.com/Ajwah/ex-gherkin)** — Elixir, 7★, "fully fledged" alternative to the cucumber org's Elixir port. Uses `yecc`. Niche.
- **[cabbage-ex/gherkin](https://github.com/cabbage-ex/gherkin)** — 16★, extracted from white-bread, used by the cabbage runner.
- **[stewartmatheson/gherkin](https://github.com/stewartmatheson/gherkin)** — Elixir, single-author pure parser.

If you only need to *parse* `.feature` files (e.g. to generate documentation, lint scenarios, or route to a custom runner), the official `cucumber/gherkin` port for your target language is the safe pick.

## Status in the BEAM ecosystem

BEAM languages (Erlang / Elixir / Gleam) share a VM but not tooling maturity for BDD.

| Sub-ecosystem | State at 2026-04-22 |
| --- | --- |
| **Erlang** | No currently-active native Cucumber port. Community typically reaches down to Elixir tooling or skips BDD. |
| **Elixir** | Two tools: **cabbage** (active, ExUnit-integrated, recommended) and **white-bread** (stale since 2020, avoid for new work). Plus the official `cucumber_gherkin` parser on Hex (published under the `cukebot` account, pointed at `cucumber/gherkin/elixir`, last release 2026-03-01) if you want parsing only. |
| **Gleam** | One tool: **dream_test** (see below). Ships a native Gleam Gherkin parser + step binder + runner. 5 stars, young, but solid README and zero community track record. |

**Can Gleam use the Elixir libraries via FFI?** In principle yes — cabbage is a compile-time macro over ExUnit, and Gleam can call Erlang code, but the macro mechanism is Elixir-specific and won't feel natural from Gleam. For a Gleam project, dream_test is the direct path.

## Status in Gleam specifically

### dream_test

| Criterion | [dream_test](https://github.com/TrustBound/dream_test) |
| --- | --- |
| Stars | 5★ · 🟥 |
| License | MIT · 🟩 |
| Target | ☎️ BEAM |
| Gleam compat | `gleam_stdlib >= 0.60.0 and < 1.0.0` · 🟩 |
| Deps | 4 (gleam_erlang, gleam_json, gleam_otp, gleam_regexp) |
| Maintenance | 🟩 (last commit 2026-02-03, ~11 weeks before snapshot) |
| Age | ~5 months (first PR merged 2025-12-01) · 🟨 |
| Open issues | 1 · 🟩🟩 (clean tracker) |
| README maturity | 🟩🟩 (feature list, Gherkin + unit examples, reporter docs) |
| Idiomaticity | 🟩 (explicit APIs, type-safe step context, no magic macros) |

**What it provides.** A single framework spanning both unit testing (describe/group/it) and Gherkin-style integration tests. Gherkin features can be authored as `.feature` files *or* inline in Gleam via a DSL. Modules exposed:

- `dream_test/gherkin/feature` — feature and scenario definitions
- `dream_test/gherkin/parser` — `.feature` file parsing
- `dream_test/gherkin/steps` — step registration and `StepContext`
- `dream_test/gherkin/world` — mutable shared state across steps
- `dream_test/gherkin/discover` — auto-discovery of feature files
- `dream_test/gherkin/step_trie`, `dream_test/gherkin/types` — internals

Also ships: parallel execution with configurable concurrency, multiple reporters (BDD, progress, JSON), snapshot testing, and pipe-first matchers (`should |> be_equal(...)`).

**Example (inline Gleam DSL).**

```gleam
import dream_test/gherkin/steps
import dream_test/gherkin/feature.{feature, scenario, given, when, then}
import dream_test/gherkin/world

pub fn shopping_cart_feature() {
  let my_steps =
    steps.new()
    |> steps.step("I have {int} items", fn(ctx) {
      world.put(ctx.world, "cart", get_int(ctx.captures, 0))
      Ok(steps.succeed())
    })
    |> steps.step("I add {int} more", fn(ctx) {
      let current = world.get_or(ctx.world, "cart", 0)
      world.put(ctx.world, "cart", current + get_int(ctx.captures, 0))
      Ok(steps.succeed())
    })
    |> steps.step("I should have {int} items", fn(ctx) {
      world.get_or(ctx.world, "cart", 0)
      |> should
      |> be_equal(get_int(ctx.captures, 0))
    })

  feature("Shopping Cart", my_steps, [
    scenario("Adding items", [
      given("I have 3 items"),
      when("I add 2 more"),
      then("I should have 5 items"),
    ]),
  ])
}
```

**Where it sits in the Gleam ecosystem.** Complementary to (not competing with) `gleeunit`, which is the default Gleam unit test runner. You would use dream_test when you want Gherkin scenarios or richer unit organization; you would stick with `gleeunit` for simple lib tests.

**Risks to be aware of.**

- 5 stars, 5 months old, single-org maintained. No track record yet.
- `< 1.0.0` stdlib upper bound means minor breakage risk when Gleam 1.x lands — though Gleam stdlib has been relatively stable.
- Only one active contributor org (TrustBound). If the org pauses, there is no second choice in the Gleam ecosystem.

### What a Gleam team could do instead

If you specifically need Gherkin today and dream_test feels too young:

1. **Skip BDD.** Use `gleeunit` (the ecosystem default) with descriptive test names. Most small Gleam projects do this.
2. **Write your own mini-runner.** The canonical parser (`cucumber/gherkin`) compiles to 12 languages; you could call the Erlang or JS port via FFI and bind steps in Gleam. Viable for a team that really wants Gherkin and is willing to maintain a thin adapter.
3. **Host-side BDD.** If your Gleam code is called from Elixir, you can write your Gherkin tests in Elixir using cabbage and have them exercise Gleam modules via Erlang FFI.

## Recommendation for Gleam developers

Most Gleam teams do not need Gherkin — `gleeunit` covers unit testing, and BDD's primary value is stakeholder-readable specs, which is rare in early-stage Gleam projects.

If you do need Gherkin in Gleam, **dream_test is the only option** at snapshot and is perfectly usable: strong docs, clean tracker, maintained. The risk is thin community, not broken code.

If you need Gherkin in Elixir to drive Gleam code, use **cabbage** — it's the active Elixir option, compile-time, ExUnit-native.

If you need Gherkin in Erlang, there is no first-class path. Consider moving that layer of the test stack to Elixir or JS.

---

### Appendix: ecosystems outside the table

Covered briefly for completeness — not BEAM-adjacent, so not ranked:

- **Swift / iOS**: `XCTest-Gherkin` (NATIVE by Sam Dean-style community port), plus `Cucumberish`. Lower traction than the top-tier ports.
- **Dart / Flutter**: `flutter_gherkin` (community, active). Part of the `cucumber/gherkin` polyglot parser's target list.
- **Scala**: wraps `cucumber-jvm` via `cucumber-scala`. No standalone tool worth tracking separately.
- **Clojure**: historically `lein-cucumber`; now largely abandoned. Most Clojure teams stick to `clojure.test` + generative testing.

---

*Snapshot: 2026-04-22. Data sources: cucumber.io docs, live GitHub web UI, live Hex.pm package listings, live packages.gleam.run searches. No API or git-clone access used.*
