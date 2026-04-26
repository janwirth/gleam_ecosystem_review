# Workflow & Quality Standards

## Article Update Standards

When manually editing articles (e.g., adding repos, refreshing data), update workflow files to reflect discovered nuances:

### Data Quality

- **Snapshot dates**: Update article snapshot date when repos are re-evaluated. Use ISO format (YYYY-MM-DD).
- **Exact metrics**: Stars, issue counts, commit dates must come from live GitHub web UI, not inference.
- **Date precision**: Maintenance date uses ISO format + emoji showing recency vs snapshot date (🟩🟩 same day/recent, 🟩 weeks to months, 🟥 years).
- **Activity emoji**: Compare latest commit date to snapshot date. Recent = 🟩🟩, months old = 🟩, years old = 🟥.

### README Assessment

- **Batteries-included frameworks**: Full guide + clear feature list = 🟩🟩 (not just 🟩).
- **Tagline-only**: Just description = 🟩 for clarity, 🟥 if no substance.
- **Adapter patterns**: Lightweight READMEs for single-purpose repos (e.g., "HTTP server for Cowboy") = 🟩 if clear.
- **Example repos**: GitHub-generated templates = 🟩 (shows intent, not execution).

### Issue Tracking

- **Zero issues**: Positive signal. Note explicitly in table and notes section.
- **Unknown issues** (⬜): Page load error or access denied. Document in notes.

### Table Updates

When adding repos:
1. Add to "Repos Reviewed" list (maintain original order or sort by category).
2. Add column to table (right-aligned new repos for easy review).
3. Recalculate all rows (don't assume old emoji rankings still hold relative to new entrant).
4. Update Notes section with new repo's edge cases or highlights.

## Workflow File Updates Trigger

Update `/workflows/` files when articles reveal:

- **New scoring dimension**: E.g., if "zero issues" becomes a strong signal across multiple reviews, add explicit callout in AGENTIC_REVIEW_AGENT.md.
- **New edge case**: E.g., "batteries-included framework README" wasn't differentiated before glimr. Document in REVIEW_METHODOLOGY.md under README assessment.
- **New checklist item**: If manual review surfaced a missed step, add to REVIEW_CHECKLIST.md.

### Example: Glimr Addition (2026-04-13)

Added `glimr-org/glimr` to Gleam Web Frameworks article. Insights:

- **README 🟩🟩 signal**: "Batteries-included framework" with comprehensive guide → refined README maturity assessment in workflows.
- **Zero issues signal**: Noted explicitly; considered positive maintenance/quality signal beyond just activity date.
- **Snapshot date bump**: Moved from 2026-03-22 to 2026-04-13. Workflow reminder: snapshot date is not "creation date" but "evaluation date".

### Example: shork + glimr db Addition (2026-04-26)

Added `ninanomenon/shork` (MySQL driver) and `glimr-org/framework/src/glimr/db` (framework-bundled DB layer) to Gleam Databases article. Insights:

- **Frozen-mirror edge case**: shork's README announces the project moved to Codeberg; the GitHub repo is now a stale mirror. Decision: score the GitHub repo on its frozen state, add a `[!CAUTION]` callout, and footnote the leaderboard row. Codified in REVIEW_METHODOLOGY.md.
- **Framework-bundled modules**: glimr's `db` module is the closest thing to an ORM in Gleam, but only consumable inside a glimr application. Decision: include in leaderboard with a `†` footnote flagging it as framework-bundled (not standalone), retain framework metadata for scoring. Codified in REVIEW_METHODOLOGY.md.
- **New dialect icon**: Adding a MySQL driver triggered extending the dialect legend (🐘 / 🪶 / 🐬). Updated cake (now 🐘🪶🐬) and sqlode (now 🐘🪶🐬) inline tables to reflect the third dialect they already support.
- **Disabled Issues tab**: shork's GitHub Issues UI is disabled (issue tracking happens on Codeberg). Marked as ⬜ in the issues row with explanatory note — distinct from "0 open" (positive signal).
- **Capped gleam_stdlib**: shork's `< 1.0.0` cap mirrors the migrant disregard reason. Did not disregard shork because (a) user asked to add it, (b) cake/sqlode wire to it as the canonical MySQL backend; instead flagged the cap as 🟥 in compat row and called it out in the leaderboard summary.

### Example: Parsers & Generators Article (2026-04-26)

Created `gleam/parsers-and-generators.md` covering issue #3, merging two prior OpenAPI articles into it. Insights:

- **Article merge pattern**: When sub-todos explicitly authorise it, merge narrowly-scoped articles into a broader one rather than keeping separate stubs. Preserve **all** substantive content (feature tables, code examples, gap analyses) — fold them into appropriately-scoped sub-sections of the new article. Delete the old files and update every cross-link in the codebase.
- **Multi-category leaderboard**: When an article spans heterogeneous tool classes (e.g., parser combinators vs SQL→Gleam codegen), produce **per-category leaderboards** rather than one ranking. The 7-dim score is comparable within a class, not across (a SQL codegen with 600★ shouldn't dominate the parser-combinator winner with 80★ purely on community size).
- **Cross-reference instead of duplicate**: For repos already deeply covered elsewhere (e.g., squirrel/sqlode in `databases.md#sql-code-generators`), link to the existing entry rather than re-listing the full table. Use the cross-link pattern `[squirrel](databases.md#squirrel-)` (note the trailing `-` because the source heading is `#### squirrel 🐘`).
- **Cross-link role disambiguation**: When the same repo class fits two articles (e.g., per-language lexers fit both `syntax-highlighting.md` and a parsers article), pick the **role-defining** article and have the other cross-link with a one-sentence note explaining the role-difference ("parsing for AST/analysis" vs "tokenising for colourisation"). Don't duplicate.
- **Renamed/redirected repos**: `daniellionel01/sqlc-gen-gleam` redirects to `daniellionel01/parrot` (renamed). Use the **current** repo URL in the article, mention the prior name in prose (`"renamed from sqlc-gen-gleam"`), so readers find it from either name.
- **Self-hosted Git hosts gated by Anubis** (e.g., liten.app/krig/mork): WebFetch returns the access-denied page. Mark stars/license/dates as ⬜ with a note. Don't disregard — the package still exists on Hex.
- **Unknown licenses**: When repo sidebar/LICENSE doesn't render via WebFetch but the project is real, score as 🟨 unknown (not 🟥) and add a footnote inviting the reader to check directly. Don't pretend to know.

## Future Article Updates

When adding to existing articles or creating new ones:
1. Use current date as snapshot (not article creation date).
2. Follow AGENTIC_REVIEW_AGENT.md process exactly.
3. Capture exact metrics from live GitHub UI.
4. If new repo type or pattern emerges, note in workflows immediately.
5. Re-rank all repos when adding new ones (don't append to old ranking).
6. Update Notes section with summary of any new repos or edge cases.
