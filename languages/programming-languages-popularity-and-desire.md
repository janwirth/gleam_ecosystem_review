# Programming language popularity & desire

**Snapshot 2026-04-23** — data from [Stack Overflow Developer Survey 2025](https://survey.stackoverflow.co/2025/technology).

Two numbers matter when you pick a language: how many people *use* it (how easy will it be to hire for, find libraries for, and get answers on Stack Overflow) and how many people who have used it *want to keep using it* (how likely is it that your team enjoys the tool a year in). The Stack Overflow Developer Survey publishes both once a year.

This article is a companion to the [security incidents review](../security/) — both inform the question "which tech do we pick and commit to". Nothing here is a recommendation to switch stacks; these are decision inputs.

**Terminology note.** Stack Overflow renamed the old "loved" question to **"admired"** in 2024. An *admired* language is one a respondent has used in the past year *and* wants to use again in the next year — retention, not hype. A *desired* language is one any respondent wants to use in the next year, whether or not they have used it — interest, including from non-users. Both are asked on the same survey screen.

> [!NOTE]
> The Stack Overflow survey page is a single-page app. The most-popular tables below were captured from the rendered chart directly. The admired/desired rankings are partially sourced from secondary writeups (cited per row) because the relevant SPA section is not crawlable as static HTML. Where a number could not be verified, the row is marked and no percentage is given.

---

## Most popular

What developers have done extensive work in over the past year. Source: [survey.stackoverflow.co/2025/technology](https://survey.stackoverflow.co/2025/technology#most-popular-technologies-language-language-prof-ai).

Stack Overflow publishes two splits — all respondents (includes students, hobbyists, learning-to-code) and professional developers (paid, full-time). Professionals skew higher on TypeScript, SQL, and C#; lower on Python (because the learning-to-code cohort leans Python-heavy).

| Rank | Language | All respondents | Professional devs |
| ---: | --- | ---: | ---: |
| 1 | JavaScript | 66.0% | 68.8% |
| 2 | HTML/CSS | 61.9% | 63.0% |
| 3 | SQL | 58.6% | 61.3% |
| 4 | Python | 57.9% | 54.8% |
| 5 | Bash/Shell | 48.7% | 48.8% |
| 6 | TypeScript | 43.6% | 48.8% |
| 7 | Java | 29.4% | 29.6% |
| 8 | C# | 27.8% | 29.9% |
| 9 | C++ | 23.5% | 21.8% |
| 10 | PowerShell | 23.2% | 23.1% |
| 11 | C | 22.0% | 19.1% |
| 12 | PHP | 18.9% | 19.1% |
| 13 | Go | 16.4% | 17.4% |
| 14 | Rust | 14.8% | 14.5% |
| 15 | Kotlin | 10.8% | 11.5% |
| 16 | Lua | 9.2% | 8.6% |
| 17 | Assembly | 7.1% | 5.7% |
| 18 | Ruby | 6.4% | 6.9% |
| 19 | Dart | 5.9% | 6.1% |
| 20 | Swift | 5.4% | 5.7% |
| 21 | R | 4.9% | — |

Source for both columns: [SO 2025 technology → programming languages](https://survey.stackoverflow.co/2025/technology#most-popular-technologies-language-language-prof-ai).

**Year-over-year movement (SO-reported deltas, 2024 → 2025):**

- Python +7 pp — the single biggest jump, attributed by SO to AI/data work ([SO blog recap](https://stackoverflow.blog/2025/12/29/developers-remain-willing-but-reluctant-to-use-ai-the-2025-developer-survey-results-are-here/)).
- Rust +2 pp, Go +2 pp — both growing on the back of infra and AI tooling.
- PHP −3 pp, Ruby −2.5 pp — continued slow decline.

---

## Most admired

Share of developers who have used the language *and* want to keep using it next year. Source: [SO 2025 → Admired and Desired](https://survey.stackoverflow.co/2025/technology#admired-and-desired). Supplementary percentages cross-checked against third-party writeups where noted.

| Rank | Language | Admired % | Source for % |
| ---: | --- | ---: | --- |
| 1 | Rust | 72.4% | [SO primary](https://survey.stackoverflow.co/2025/technology#admired-and-desired), [itequia.com](https://itequia.com/stack-overflow-2025-where-is-technological-development-heading/) |
| 2 | Gleam | 70.8% | [SO primary](https://survey.stackoverflow.co/2025/technology#admired-and-desired), [itequia.com](https://itequia.com/stack-overflow-2025-where-is-technological-development-heading/) |
| 3 | Elixir | 65.9% | [SO primary](https://survey.stackoverflow.co/2025/technology#admired-and-desired), [itequia.com](https://itequia.com/stack-overflow-2025-where-is-technological-development-heading/) |
| 4 | Zig | 64.2% | [SO primary](https://survey.stackoverflow.co/2025/technology#admired-and-desired), [itequia.com](https://itequia.com/stack-overflow-2025-where-is-technological-development-heading/) |
| — | Python | 56.4% | [frameworktraining.co.uk recap](https://www.frameworktraining.co.uk/news-insights/stackoverflow-developer-survey-results-2025-overview) |
| — | Lua | 46.9% | [frameworktraining.co.uk recap](https://www.frameworktraining.co.uk/news-insights/stackoverflow-developer-survey-results-2025-overview) |

Rust has held the #1 admired slot in the Stack Overflow survey every year since 2016 — ten years running as of this snapshot. Gleam appeared in the admired ranking for the first time in 2025 and debuted at #2 — an unusually strong debut for a language whose usage share is below the survey's reporting threshold.

> [!NOTE]
> Beyond the six entries above, percentages for the remaining admired top-20 could not be confirmed from any text-crawlable source at snapshot time. The interactive SPA chart on [survey.stackoverflow.co/2025/technology#admired-and-desired](https://survey.stackoverflow.co/2025/technology#admired-and-desired) does show the full list visually — readers who need positions 7–20 should consult it directly. This article will be refreshed when a scrape-accessible data export becomes available.

---

## Most desired

Share of *all* developers (users and non-users) who want to work with the language in the next year. Source: [SO 2025 → Admired and Desired](https://survey.stackoverflow.co/2025/technology#admired-and-desired).

| Rank | Language | Desired % | Source for % |
| ---: | --- | ---: | --- |
| 1 | Python | 39.3% | [frameworktraining.co.uk recap](https://www.frameworktraining.co.uk/news-insights/stackoverflow-developer-survey-results-2025-overview) |
| 2 | Rust | 29.2% | [frameworktraining.co.uk recap](https://www.frameworktraining.co.uk/news-insights/stackoverflow-developer-survey-results-2025-overview) |
| 3 | Go | 23.4% | [frameworktraining.co.uk recap](https://www.frameworktraining.co.uk/news-insights/stackoverflow-developer-survey-results-2025-overview) |
| — | TypeScript | — | SO primary names it in the top cluster ([SO 2025](https://survey.stackoverflow.co/2025/technology#admired-and-desired)), exact % not in cited recaps |

SO's own summary groups **Python, TypeScript, Rust, and Go** together as the top desired languages ([SO 2025 technology](https://survey.stackoverflow.co/2025/technology#admired-and-desired)). Independent forum discussion of the 2025 results ([Rust users forum](https://users.rust-lang.org/t/stack-overflow-survey-2025/131005), [Elixir forum](https://elixirforum.com/t/stackoverflow-survey-2025-results/71874)) corroborates Gleam and Elixir appearing high in the desired ranking as well — surprising given how small their user bases are.

> [!NOTE]
> As with the admired table, the full desired top-20 percentages are locked behind the SPA chart. The three numbers above were captured from a secondary source quoting them directly; other positions were not reproduced with percentages in any writeup found at snapshot time.

---

## The delta — popular vs desired

The interesting signal is the *gap* between what's used today and what developers want to use next. A language that's high on both is a safe bet. A language that's high on popular but absent from desired is the status-quo tax. A language that's high on desired but not yet popular is an emerging bet — high enthusiasm, small hiring pool.

| Language | Popular rank | Desired rank | Signal |
| --- | :---: | :---: | --- |
| **Python** | 4 | 1 | High-use + high-want. The safest pick in the survey. |
| **TypeScript** | 6 | top-4 cluster | High-use + high-want. Incumbent for typed frontend/backend. |
| **Rust** | 14 | 2 | Small user base, big desire. Classic "people who use it don't want to leave." Greenfield systems bet. |
| **Go** | 13 | 3 | Similar profile to Rust — smaller pool, strong want, especially for infra and cloud-native. |
| **JavaScript** | 1 | not top-3 | Ubiquitous but not a desire leader. Use because the platform requires it, not because devs pick it voluntarily. |
| **HTML/CSS** | 2 | — | Platform language; "desire" isn't really meaningful here. |
| **SQL** | 3 | — | Same — the pragmatic default, not a love story. |
| **Java** | 7 | not top-4 | Enterprise incumbent. Hiring pool is huge; desire among people already using it is modest. |
| **C#** | 8 | not top-4 | Microsoft-stack incumbent. Similar profile to Java. |
| **PHP** | 12 | declining | Usage shrinking −3 pp YoY. Legacy projects only. |
| **Ruby** | 18 | declining | Usage shrinking −2.5 pp YoY. Shopify/Rails keeps it alive. |
| **Gleam** | below threshold | top-tier admired | #2 admired (70.8%) despite being too small to appear in the usage chart. Earliest-stage bet; small but passionate community. |
| **Elixir** | below threshold | top-tier admired | #3 admired (65.9%). Mature runtime (BEAM), niche usage. |
| **Zig** | below threshold | top-tier admired | #4 admired (64.2%). Systems-programming alternative to Rust, pre-1.0. |

---

## Reading the signal

A few caveats before you act on any of this.

**Sample bias.** The 2025 survey had 49,000+ respondents, down from 65,000+ in 2024 and 90,000+ in 2023 ([Elixir forum discussion](https://elixirforum.com/t/stack-overflow-developer-survey-2025/71073)). Smaller samples amplify enthusiasm from tight-knit communities — this is likely how Gleam, a language with a fraction of Rust's users, landed at #2 admired. That doesn't mean the signal is noise (Gleam users genuinely do like it); it means the bar for reaching "top admired" is lower when the denominator shrinks.

**Self-selection.** People who fill out the Stack Overflow survey are people who read the Stack Overflow blog. That skews toward web, enterprise backend, and developer-tooling work. The survey systematically under-represents embedded, scientific computing (Fortran is invisible here despite running most climate and physics code), HPC, and game development.

**"Popular" ≠ "hiring demand."** Usage is self-reported from developers who happen to answer the survey. It's a proxy for hiring demand, not a direct measurement. For actual hiring-market data, pair this with job-posting aggregators.

**"Admired" rewards small communities.** A 100-person Gleam community that all answered "yes, I want to keep using it" gives Gleam 100% admiration. A million-user Java community with 30% dissatisfaction gives Java a mediocre admired score even though Java is paying more salaries than Gleam ever will. Admiration is a retention signal — useful — but it does not translate directly into strategic safety.

**"Desired" includes wishful thinking.** Someone who has never written a line of Rust can still check the "I want to use Rust next year" box. Desired is aspiration; admired is retention; popular is practice. All three are useful for different decisions.

**AI is moving the numbers.** SO itself attributes Python's +7 pp jump and Rust/Go's +2 pp each to AI and infrastructure work ([SO 2025 recap](https://stackoverflow.blog/2025/12/29/developers-remain-willing-but-reluctant-to-use-ai-the-2025-developer-survey-results-are-here/)). If your tech selection doesn't touch AI workloads, discount those YoY movements somewhat.

---

## Takeaways for tech selection

- **Python, JavaScript, TypeScript, and SQL are the safest bets** — they combine high usage (easy to hire) with top-cluster desire (developers don't resent them). If the project scope fits, pick from these four.
- **Rust scores in the top-3 on desire and has held #1 admired for ten years.** If you are starting a systems, infra, or performance-sensitive project and your team has appetite for a newer ecosystem, this is the survey-backed choice.
- **Go is the low-risk Rust alternative** — #3 desired, simpler learning curve, strong cloud-native/CLI-tool ecosystem, mature hiring pool.
- **Legacy popularity is not future-proofing.** PHP and Ruby are still widely used; they are also both shrinking year-over-year. Pick them for maintaining an existing app, not for starting a new one.
- **Admired-but-niche languages (Gleam, Elixir, Zig) are bets, not defaults.** A high admiration score means people who use them stick with them — a real signal — but the hiring pool is small and the library ecosystem is thin. Viable for small teams comfortable with ecosystem-building; risky for organisations that need to onboard a new engineer every few months.
- **JavaScript is a platform, not a preference.** It is #1 popular and not in the top desire cluster. Use it because the browser requires it, then consider TypeScript on top for the typed experience developers actually want.

---

*Data captured 2026-04-23 from the 2025 Stack Overflow Developer Survey. For the underlying question wording and full methodology, see [Stack Overflow's 2025 methodology page](https://survey.stackoverflow.co/2025/methodology/).*
