# Review Checklist

Quick reference for running a systematic review.

## Pre-Review

- [ ] Define category/goal
- [ ] List all repos to review
- [ ] Note snapshot date (today's date)
- [ ] Confirm: "public web UI only" (no clone, no API)

## Per-Repo Steps

For each repo in list:

### Homepage
- [ ] Record star count from header
- [ ] Read tagline/description

### README (rendered)
- [ ] Read full or key sections
- [ ] Note maturity: guide-style / clear / minimal
- [ ] Scan for examples, docs, API clarity
- [ ] Check for ecosystem/comparison tables

### /commits
- [ ] Find newest commit date (ISO: YYYY-MM-DD)
- [ ] Note pagination (paginated vs single page)
- [ ] Describe history span visible (e.g., "Jan 2024 to Mar 2026")
- [ ] Estimate commit volume from pagination

### Issues Tab
- [ ] Count open issues on first load
- [ ] Note if tab failed to load (= ⬜)

### Scoring
- [ ] Record open issue count
- [ ] Record star count
- [ ] Calculate maintenance emoji (🟩🟩 = today/recent, 🟩 = months, 🟥 = years)
- [ ] Assign activity emoji based on newest commit
- [ ] Assign README maturity emoji
- [ ] Assign community emoji (based on star count relative to peers)
- [ ] Assign work emoji based on pagination/history span

## Table Construction

- [ ] Create table: repos as columns, dimensions as rows
- [ ] Fill all cells from captured data
- [ ] Mark ⬜ for unknown/error/not applicable
- [ ] Double-check emoji consistency with legend

## Notes & Exceptions

- [ ] List any ⬜ cells with reason
- [ ] Call out stub repos (README = TODO, no commits, etc.)
- [ ] Note any API/page load errors
- [ ] Flag edge cases (very old commits, very new, no history, etc.)

## Comparison Check

- [ ] Search READMEs for "comparison", "vs", "alternatives" sections
- [ ] Note if found (which repo, what it compares)
- [ ] Distinguish: ecosystem context vs tool head-to-head

## Ranking

- [ ] Rank 1–N by fit for stated goal
- [ ] For each, write README lead (1–2 sentences)
- [ ] For each, list scores: emoji dimensions + metrics
- [ ] Verify tie-break logic (maintenance date + evidence quality)

## Output Validation

- [ ] Snapshot date present and correct
- [ ] Legend clearly defined
- [ ] Table complete (no blank cells unless ⬜)
- [ ] All ⬜ explained in notes
- [ ] Ranking is 1–N (no ties)
- [ ] Each ranked repo has README lead + scores
- [ ] Emoji usage matches legend throughout
- [ ] No API calls or private data mentioned
- [ ] No inference from prior knowledge or memory
