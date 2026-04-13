# Library Review Methodology

Generic workflow for systematic review of any library/tool ecosystem.

## Core Principle

Use **public web UI only** (no API, no clone). Visitor-level access. Check: README, commit history, issues, stars. Look for maintenance, maturity, community signal.

## Data Sources

1. **Repo homepage** — star count, description
2. **Rendered README** — tagline, docs, examples, design intent
3. **Commits page** — date of newest commit, commit density, history span
4. **Issues tab** — open issue count (first page or total shown)
5. Commit count in repo header (if shown)

## Scoring Dimensions

| Dimension | Legend | Signal |
| --- | --- | --- |
| Activity (recency) | 🟩🟩 recent · 🟩 months old · 🟥 years old | Latest commit date; gap since last change |
| Maintenance | 🟩🟩 actively · 🟩 steady · 🟥 dormant | Commit recency + frequency across history |
| README quality | 🟩🟩 guide-style · 🟩 clear · 🟥 minimal/broken | Docs maturity; examples; intent clarity |
| Community | 🟩🟩 strong · 🟩 moderate · 🟥 none | Star count; relative adoption signal |
| Work (effort) | 🟩🟩 substantial · 🟩 solid · 🟥 minimal | Commit count, history depth, pagination |
| Issues | counts + emoji | Open issue count; health signal |

## Review Structure

1. **Snapshot header** — date + "public web UI only"
2. **Legend line** — define emoji scale
3. **Table** — one column per repo, rows = dimensions above
4. **Notes section** — call out edge cases, ⬜ reasons, error notes
5. **Comparison check** — any "head-to-head" docs? Yes/no + brief note
6. **Ranking** — numbered 1–N, best fit first
   - Bold repo name + link
   - README lead: 1–2 sentences from README/tagline
   - Scores: emoji dimensions + raw metrics (issues, maintenance date, work volume)

## Scoring Rules

- Use only what public pages show (no inference from prior knowledge)
- ⬜ = unknown, not shown, not applicable, or error occurred
- 🟩🟩 = strong signal, 🟩 = OK, 🟥 = negative signal
- Maintenance date: ISO style + emoji vs today (🟩🟩 same day, 🟩 months, 🟥 years)
- Work volume: describe pagination or visible span + emoji (🟩🟩 large, 🟩 solid, 🟥 sparse)
- Ranking: tie-break by maintenance + evidence strength, not just stars

## Running a Review

1. **List repos** to evaluate (semantic group: e.g., "web servers", "logging", "auth")
2. **Visit each repo** in sequence: home, README, /commits, Issues tab
3. **Capture findings** in table (date, star count, newest commit date, issue count, pagination/history note, README maturity)
4. **Note exceptions** (Issues UI error, no commit history, stub repo, etc.)
5. **Check README** for ecosystem/comparison content
6. **Rank repos** by fit for stated goal (adjust criteria if needed)
7. **Write snapshot** with all findings

## Reusing This Template

- **Goal statement:** "Ranking goal: [e.g., 'library choice for X', 'example quality', 'ecosystem maturity']"
- **Criteria:** Core dimensions above; add domain-specific rows if needed (e.g., "TypeScript support", "WebAssembly binding")
- **Legend:** Reuse 🟩🟩 / 🟩 / ⬜ / 🟥 unless domain-specific scoring makes sense
- **Ranking tie-break:** Maintenance date + evidence quality + stated goal
