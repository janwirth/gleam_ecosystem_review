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

## Future Article Updates

When adding to existing articles or creating new ones:
1. Use current date as snapshot (not article creation date).
2. Follow AGENTIC_REVIEW_AGENT.md process exactly.
3. Capture exact metrics from live GitHub UI.
4. If new repo type or pattern emerges, note in workflows immediately.
5. Re-rank all repos when adding new ones (don't append to old ranking).
6. Update Notes section with summary of any new repos or edge cases.
