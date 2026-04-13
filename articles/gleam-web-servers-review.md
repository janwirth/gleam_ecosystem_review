# Gleam Web Frameworks and Servers Review

**Snapshot 2026-04-13** — data from **public GitHub web pages and raw README URLs only** (no clone, no GitHub API).

**Legend:** 🟩🟩 strong · 🟩 OK · ⬜ unknown / not shown / not applicable · 🟥 negative signal.

## Repos Reviewed

- lustre-labs/lustre
- MystPi/glen
- gleam-lang/http
- gleam-lang/example-echo-server
- myzykyn/gleam_webserver
- fravan/olive
- pta2002/gleam-radiate
- gleam-lang/cowboy
- gleam-lang/plug
- glimr-org/glimr

## Evaluation Table

| Criterion | lustre | glen | http | example-echo-server | gleam_webserver | olive | gleam-radiate | cowboy | plug | glimr |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Open issues | 9 | 2 | 1 | 2 | 0 | 1 | 3 | ⬜ | ⬜ | 0 |
| Stars | 2213 | 111 | 276 | 83 | 0 | 8 | 66 | 75 | 36 | 165 |
| Recently maintained | 2026-03-22 · 🟩🟩 | 2025-06-30 · 🟩 | 2025-10-02 · 🟩 | 2025-04-13 · 🟩 | 2025-08-28 · 🟩 | 2025-07-18 · 🟩 | 2025-09-18 · 🟩 | 2025-11-01 · 🟩 | 2020-08-27 · 🟥 | 2026-04-11 · 🟩🟩 |
| Total work | Paginated `/commits` (34+ per page); history to Jan 2026 · 🟩🟩 | Paginated; Feb 2024 back · 🟩 | Paginated; dense 2025 · 🟩🟩 | Long tail to 2020 · 🟩 | Single commit · 🟥 | Short history (Feb–Jul 2025) · 🟩 | Oct 2023 initial through 2025 · 🟩 | Multi-year history · 🟩 | Handful of 2020 commits · 🟥 | Paginated `/commits` (35+ per page); history Mar 5 to Apr 11 · 🟩🟩 |
| Activity (recency) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟩 | 🟥 | 🟩🟩 |
| README maturity | 🟩🟩 (full guide) | 🟩 (tagline + repo) | 🟩 (adapter tables + ecosystem) | 🟩 (example; template text) | 🟥 (TODO scaffold) | 🟩 (clear tagline; calls out limits) | 🟩 (tagline; full README not shown) | 🟩 (adapter pattern) | 🟩 (usage + old import style) | 🟩🟩 (batteries-included; full guide) |
| Community (stars) | 🟩🟩 | 🟩 | 🟩 | 🟩 | 🟥 | 🟥 | 🟩 | 🟩 | 🟥 | 🟩 |

## Notes

**myzykyn/gleam_webserver**: One commit (2025-08-28); project is stub. README is default TODO. **gleam-lang/plug**: Newest commit 2020-08-27; maintenance 🟥. **Cowboy/plug Issues**: Pages did not load reliably; cells marked ⬜. **glimr-org/glimr**: Zero open issues; robust, current framework with strong maintenance.

## Comparison Section

None of these READMEs include head-to-head framework comparison. **gleam-lang/http** includes **adapter comparison tables** (Mist, Cowboy, Plug, fetch, etc.) — ecosystem context, not tool comparison.

## Ranking (readme + scoring)

Goal: centrality to Gleam HTTP/web stack and maintenance evidence.

1. **[gleam-lang/http](https://github.com/gleam-lang/http)**
   - **README (lead):** Types and functions for HTTP clients and servers, with tables linking server and client adapters (Mist, Cowboy, Plug, fetch, etc.).
   - **Scores:** activity 🟩 · README 🟩 · stars 🟩 · issues **1** · maintenance **2025-10-02** · work **paginated, substantial history**

2. **[glimr-org/glimr](https://github.com/glimr-org/glimr)**
   - **README (lead):** Batteries-included web framework for Gleam emphasizing type safety and functional programming; includes routing, templates, middleware, ORM-like features, caching, auth scaffolding, hot reload, Vite integration.
   - **Scores:** activity 🟩🟩 · README 🟩🟩 · stars 🟩 · issues **0** · maintenance **2026-04-11** · work **paginated, substantial recent history**

3. **[lustre-labs/lustre](https://github.com/lustre-labs/lustre)**
   - **README (lead):** "Make your frontend shine" — declarative HTML in Gleam, Elm/OTP-style state, universal components, CLI, SSR; points to Hex, quickstart, examples.
   - **Scores:** activity 🟩🟩 · README 🟩🟩 · stars 🟩🟩 · issues **9** · maintenance **2026-03-22** · work **large paginated history**

4. **[gleam-lang/cowboy](https://github.com/gleam-lang/cowboy)**
   - **README (lead):** Gleam HTTP service adapter for the Cowboy web server.
   - **Scores:** activity 🟩 · README 🟩 · stars 🟩 · issues **unknown** · maintenance **2025-11-01** · work **multi-year commit list**

5. **[MystPi/glen](https://github.com/MystPi/glen)**
   - **README (lead):** "A peaceful web framework for Gleam that targets JS."
   - **Scores:** activity 🟩 · README 🟩 · stars 🟩 · issues **2** · maintenance **2025-06-30** · work **moderate history**
