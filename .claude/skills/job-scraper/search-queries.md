# Search Queries for Job Scraper

<!-- SETUP: Customized for Muhammad Rafiqul Islam - Senior Frontend Engineer, global remote + Bangladesh -->

## Candidate Context
- **Profile:** Senior Frontend Engineer (React, Next.js, TypeScript), growing into full-stack (NestJS, Prisma, GraphQL)
- **Work mode:** Open to **remote (any country)**, **onsite abroad with visa sponsorship**, and **all Bangladesh roles** (onsite/hybrid/remote)
- **Strategy:** Wide net - apply to roles from ~50% skill match upward

## Search Sites

Primary (global + Bangladesh - the built-in Danish CLIs are NOT relevant for this candidate):
- **linkedin.com/jobs** - global and Bangladesh listings (use the `linkedin-search` CLI: country-agnostic)
- **weworkremotely.com** - remote-only roles
- **remoteok.com** - remote-only roles
- **wellfound.com** (AngelList) - startups, many remote + visa
- **himalayas.app** - remote jobs with good filtering
- **bdjobs.com** - Bangladesh's largest job board (onsite/hybrid BD roles)
- **remotive.io** - remote tech jobs

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for target companies and "career" / "jobs" pages

## Visa-Sponsorship Filtering

For onsite/hybrid roles outside Bangladesh, prioritize and flag postings that mention:
- "visa sponsorship", "relocation assistance", "relocation provided", "we sponsor", "open to international candidates"
- Skip / deprioritize onsite-abroad roles that explicitly offer **no** sponsorship or relocation (deal-breaker for this candidate).

## Query Categories

Queries are grouped by priority. Combine with location/work-mode terms (`remote`, `Bangladesh`, `Dhaka`, or a target country + `visa sponsorship`).

### Priority 1: Senior Frontend / Frontend Engineer

These match the strongest and most desired career direction.

```
site:linkedin.com/jobs "Senior Frontend Engineer" (remote OR Bangladesh)
site:linkedin.com/jobs "Frontend Engineer" React Next.js (remote OR Bangladesh)
"Senior Frontend Engineer" remote visa sponsorship
site:weworkremotely.com "Frontend Engineer" React
site:remoteok.com "Frontend Developer" React TypeScript
"React Developer" remote (worldwide OR global)
site:bdjobs.com "Frontend Developer" Dhaka
```

### Priority 2: Full-Stack / JavaScript (domain growth direction)

These match the candidate's active growth into full-stack (NestJS, Prisma, GraphQL).

```
site:linkedin.com/jobs "Full Stack Developer" React NestJS (remote OR Bangladesh)
site:linkedin.com/jobs "Full Stack Engineer" React TypeScript (remote OR Bangladesh)
"Full Stack Developer" React Node remote visa sponsorship
site:weworkremotely.com "Full Stack" React
"JavaScript Developer" React remote
site:bdjobs.com "Full Stack Developer" Dhaka
```

### Priority 3: Frontend Lead / Tech Lead (adjacent / leadership)

Adjacent roles the candidate can pivot into given frontend-lead experience.

```
site:linkedin.com/jobs "Frontend Lead" (remote OR Bangladesh)
site:linkedin.com/jobs "Tech Lead" frontend React (remote OR Bangladesh)
"Frontend Lead" remote visa sponsorship
"Lead Frontend Engineer" React TypeScript remote
site:bdjobs.com "Software Engineer" React Dhaka
```

### Priority 4: Broader net (wider catch)

Wider net for general frontend/web engineering roles (supports the ~50% match wide-net strategy).

```
site:linkedin.com/jobs "Web Developer" React (remote OR Bangladesh)
"Next.js Developer" remote worldwide
site:wellfound.com "Frontend Engineer" React remote
site:himalayas.app "Frontend" React remote
"UI Engineer" React TypeScript remote
site:bdjobs.com "Web Developer" JavaScript Dhaka
```

## Location Filter

The candidate is based in Mirpur, Dhaka, Bangladesh. Apply this location logic when filtering results:

- **Ideal (auto-include):**
  - Fully **remote** roles (any country / worldwide)
  - **Bangladesh** roles - Dhaka, Chittagong, Narayanganj, or elsewhere (onsite, hybrid, or remote)
- **Acceptable (include, flag as needs-visa):**
  - **Onsite/hybrid abroad WITH visa sponsorship or relocation** support
- **Borderline (investigate):**
  - Onsite/hybrid abroad where visa/sponsorship status is **unclear** - check the posting before applying
- **Too far / skip (deal-breaker):**
  - Onsite/hybrid abroad with **no visa sponsorship and no relocation** support

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
- If the user names a target country (e.g., "/scrape Germany"), add country-specific queries with "visa sponsorship" / "relocation".

## Note on the Danish portal CLIs

The four Danish scrapers in `.agents/skills/` (jobindex, jobnet, jobbank, jobdanmark) are **not relevant** for this candidate. Use the `linkedin-search` CLI (country-agnostic) and the WebSearch/WebFetch queries above.
