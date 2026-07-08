# Daily Job-Hunting Workflow

A practical guide to using this workspace day-to-day. Your profile, `.docx` CV pipeline, cover letters, and search queries are already set up — this is how to drive it.

---

## The loop

**Find → Triage → Apply → Track**, with occasional tuning. Four commands do the heavy lifting:

| Command | When | What it does |
|---------|------|--------------|
| `/scrape` | Finding jobs | Searches (LinkedIn, remote boards, Bdjobs) via your tuned queries; dedupes against what you've already seen/applied to |
| `/rank` | Too many results | Scores scraped jobs against your fit framework; returns a ranked shortlist |
| `/apply` | Applying | Evaluates fit, drafts your CV (`.docx`) + cover letter, reviews, renders PDFs |
| `/outcome` | After you hear back | Logs the result, archives materials, calibrates future fit-scoring |

---

## Daily routine

### 1. Find jobs

Let the system search, or bring one yourself:

```
/scrape                  # run all your search queries
/scrape remote React     # focus on a keyword
/scrape Germany          # focus on a country (adds visa-sponsorship filtering)
```

Returns new matches (deduped — won't re-show what's in your tracker), each with a quick fit signal.

**Or (very common):** you spot a job on LinkedIn or elsewhere → just copy the URL or the posting text. You don't need `/scrape` to apply.

### 2. Triage (only if `/scrape` returns a lot)

```
/rank
```

Scores every scraped posting across the 5 fit dimensions (parallel agents fetch each one), vets deal-breakers (an onsite-abroad role with no visa sponsorship gets vetoed), and returns a ranked shortlist with honest strengths/gaps per job. Tell it a number and it hands that job straight to `/apply`.

### 3. Apply — the main event

```
/apply https://the-job-posting-url.com
```

…or `/apply` followed by pasted posting text. Then:

1. It **evaluates fit** (skills / experience / behavior / location / career) and shows the score + verdict.
2. It **asks: "Should I proceed?"** — you say yes/no. *(Your wide-net setting means it proceeds from ~50% match up.)*
3. It drafts your **CV (`.docx`)** via `cv/tailor_docx.py` + a **cover letter (LaTeX)**, runs a reviewer pass, revises, and renders both to PDF.
4. It presents the **verification checklist** (1-page CV, ATS-clean, etc.).

**Your job:** open the generated `cv/main_<company>.docx` (and its PDF) — tweak anything personal, then submit. The cover letter PDF is ready as-is.

### 4. Track the outcome

After you submit — and especially when you hear back:

```
/outcome <company>
```

Logs the status (interview / offer / rejected / no response), **archives the CV + cover letter + posting** into `documents/applications/<company>_<role>/`, and updates `job_search_tracker.csv`. Over time this is gold: `/setup` re-reads these to calibrate what actually gets you interviews.

---

## Weekly / occasional

- **`/upskill`** — analyzes gaps between your profile and the jobs you've tracked (or `/upskill <url>` for one job); produces a prioritized learning plan with study resources. Run when you want direction on what to learn next.
- **`/setup --section search`** — reconfigure your search queries as priorities shift (e.g., add a target company or country).
- **`/expand`** — enriches your profile by scanning your GitHub/portfolio (run once after major profile changes).

---

## Things to remember about your setup

- **The 4 Danish scrapers don't apply to you** (broken + wrong market). Use **`/scrape`** (WebSearch-based, works globally) or the **LinkedIn scraper** directly — those are your real finders.
- **Keep your Google Docs master current.** When you update your resume in Google Docs, re-export to `cv/main_example.docx`, then re-run:
  ```
  python3 cv/tailor_docx.py set-contact cv/main_example.docx
  ```
  to re-apply the LinkedIn/GitHub icons + clickable links.
- **Wide net is on.** `/apply` proceeds on anything ~50%+; it only auto-skips onsite-abroad roles with no visa sponsorship.
- **Salary benchmarking is off** (no data file) — `/apply` silently skips it. Set it up later only if you want it.
- **The tracker (`job_search_tracker.csv`) is your source of truth** — `/scrape` and `/rank` dedupe against it, `/outcome` writes to it. Don't delete it.

---

## TL;DR cheat sheet

```
/scrape                    # find jobs
/rank                      # shortlist (optional)
/apply <url-or-text>       # evaluate + draft CV + cover letter → PDFs
/outcome <company>         # log result + archive
/upskill                   # what to learn next
/setup --section search    # retune searches
```

**The two highest-value habits:**
1. Every time you apply, run **`/apply`** (even for a job you found manually) — that's where the tailored CV + cover letter + ATS check happen.
2. Every time you hear back, run **`/outcome`** — so the system learns what works for you.
