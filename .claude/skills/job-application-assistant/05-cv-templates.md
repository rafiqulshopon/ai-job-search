# CV Templates and Tailoring Guide

<!-- SETUP: The CV is a Google Docs (.docx) document. Tailored per application via cv/tailor_docx.py. -->

## Template: Google Docs (.docx)

The CV is authored and maintained in Google Docs, exported to `.docx`, and tailored per application by a Python helper (`cv/tailor_docx.py`, built on `python-docx`). This preserves the candidate's exact Google Docs styling. The CV is rendered to PDF with LibreOffice (no Word or LaTeX required).

**Master file:** `cv/main_example.docx` (the candidate's comprehensive resume export — source of truth for all tailoring).
**Tailored output:** `cv/main_<company>.docx`
**Render to PDF:** LibreOffice headless (`soffice`).
**Tailoring helper:** `cv/tailor_docx.py`.

### Render command

```bash
soffice --headless --convert-to pdf --outdir cv cv/main_<company>.docx
```

Expected output: `cv/main_<company>.pdf` at **exactly 1 page**. Any page count other than 1 is a failure that must be fixed before presenting to the user.

### Tailoring helper (`cv/tailor_docx.py`)

The `.docx` is a binary format — the `Edit` tool cannot touch it. All CV edits go through the helper, which operates at the **section level** (preserving styles by editing runs in-place and reusing the document's named styles for new bullets). Commands:

- `python3 cv/tailor_docx.py read <path>` — dump the document's sections + bullets (run this first to see the structure before editing)
- `python3 cv/tailor_docx.py copy <dst> [--from cv/main_example.docx]` — copy the master to `cv/main_<company>.docx`
- `python3 cv/tailor_docx.py set-profile <path> --text "..."` — replace the profile/summary paragraph (keeps its style)
- `python3 cv/tailor_docx.py set-bullets <path> --role "<role title>" --bullets "bullet one|bullet two|..."` — replace a role's experience bullets (supports variable counts; reuses the doc's list style)
- `python3 cv/tailor_docx.py set-skills <path> --items "Frontend: ...|Architecture: ...|..."` — rewrite the skills block
- `python3 cv/tailor_docx.py tighten-margins <path> [--by 0.4]` — reduce top/bottom margins to recover a blank trailing page

`copy` automatically applies **single-page cleanup**: it neutralizes a trailing Google-Docs "next page" section break, strips trailing empty paragraphs, and trims top/bottom margins by ~0.4cm — so tailored CVs render to exactly 1 page without a blank trailing page (a common Google-Docs-vs-LibreOffice rendering artifact). If a tailored CV still spills or shows a blank page 2, run `tighten-margins` again.

When the helper cannot cleanly express a needed edit, fall back to reading the structure (`read`), reasoning about the paragraph index, and a targeted script — but prefer the section-level operations; they are what keep styling intact.

## Preserving Google Docs Styling (important)

The whole point of the .docx workflow is to keep the candidate's Google Docs look. Rules:

- **Edit runs in-place, do not rebuild paragraphs from scratch.** Replacing `paragraph.runs[0].text` preserves the run's font/size/color/bold. Deleting a paragraph and adding a new one with raw text loses styling.
- **Reuse the document's named styles for new content.** When adding a bullet, use the same list style (numbering definition) as neighboring bullets, not a freshly-created list. The helper handles this.
- **Do not touch the theme/fonts/margins unless intentionally tightening for 1-page fit.** A stray margin or font change is immediately visible in the rendered PDF.
- **Beware Google Docs' direct formatting.** Google Docs often applies formatting directly (not via named styles). The helper's `read` output shows the real paragraph/run structure so you can target the right element.

## Section-by-Section Tailoring

### Profile Statement / Elevator Pitch (Best Practice)
This is the most important section to customize. It appears at the top of the CV.

Write 2-3 lines that function as an "elevator pitch": a concise, compelling introduction explaining why you're qualified for *this specific role*. Focus on what the employer gains from hiring you. Keep it tight — a 1-page CV has no room for a long opener.

**Profile statement templates for this candidate's main role types:**

**For Senior Frontend Engineer / Frontend Engineer roles:**
> Senior Frontend Engineer with 5 years building production React and Next.js applications for SaaS, EdTech, and real-estate platforms serving 10,000+ users. Drives frontend architecture end to end - from micro-frontend structure and state management to the GraphQL data layer - and ships quality through testing, CI/CD, and WCAG accessibility. Tailor the domain (SaaS/HRM, EdTech, property, real-time) to the posting.

**For Full-Stack Developer / Engineer roles:**
> Frontend-led full-stack engineer with 5 years on React and Next.js and growing production backend experience with NestJS, Prisma, and GraphQL. Comfortable owning a problem end to end across the stack - has architected frontends from scratch and co-designed backend data layers. Tailor the backend depth (NestJS/Prisma/PostgreSQL) to the posting's stack.

### Core Competencies / Skills Section (Best Practice)
Reorder and emphasize based on the role. Use bold category labels.

List **3-5 key competencies** in bullet format, tailored to the specific job. For each competency, briefly explain how it adds value to the position.

### Education
- Always include your highest degrees
- For senior roles, keep education brief (dates and titles only)

### Professional Experience
- Rewrite bullet points to emphasize aspects most relevant to the target role
- Use 3-4 bullets for most recent role, 2-3 for previous, 1-2 for older (a 1-page CV cannot afford 5-6 bullets per role)
- **Emphasize measurable results** where possible: "cut redundant API calls by 20%", "onboarded 50+ clients"

### Handling Employment Gaps (Best Practice)
If there is a gap in your employment history:
- The gap should be explained matter-of-factly if needed
- Describe how professional development continued during the gap
- Frame as deliberate skill-building and career repositioning

## Render-and-Inspect Loop (MANDATORY)

After tailoring the `.docx` and before presenting to the user, always render to PDF and visually inspect. Iterate until the layout is clean. Workflow:

1. Tailor via `tailor_docx.py` → `cv/main_<company>.docx`
2. Render: `soffice --headless --convert-to pdf --outdir cv cv/main_<company>.docx`
3. Check the output page count: must be exactly 1
4. Read the PDF via the Read tool and visually inspect: styling intact (fonts/colors/bullets match master), no broken or blank paragraphs, nothing spilled to page 2

### Fixing overflow (content spills to page 2)

A 1-page CV has no second page:

- **Near-miss (1-2 lines over):** trim the profile statement by a line, or tighten the `.docx` page margins slightly via the helper (`tighten-margins`). Down to ~1.0cm margins the layout still looks professional.
- **Blank trailing page (page 2 exists but empty):** a Google-Docs-vs-LibreOffice rendering artifact. `copy` already auto-applies single-page cleanup; if it recurs, run `python3 cv/tailor_docx.py tighten-margins <path>` and re-render.
- **Significant overflow (a whole section on page 2):** cut content using relevance-weighted cutting below. Re-apply via the helper, then re-render.
- **Do NOT shrink fonts below ~10pt** to force-fit — a cramped 1-pager reads worse than a slightly fuller one.
- **Styling broke (wrong font/size, blank paragraph):** a python-docx edit hit the wrong run/paragraph. Re-do it via a section-level helper operation instead of raw text replacement.
- **Content finishes high on the page (feels thin):** restore the highest-relevance item that was previously cut. A 1-page CV ending below ~60% of the page looks incomplete.

## ATS Parseability

Most employers run CVs through an ATS before a human sees them, and the ATS reads the PDF's embedded **text layer**, not the rendered page. After the layout passes the render-and-inspect loop, verify the text layer:

```bash
cd cv && pdftotext -layout main_<company>.pdf main_<company>.txt
```

`pdftotext` comes from [poppler](https://poppler.freedesktop.org/) (already installed on this machine). What to check in the extraction:

- **Contact details as literal text.** Google Docs `.docx` files use plain text (not icon fonts), so email and phone usually extract cleanly. The failure mode is a detail carried only by a hyperlink target (e.g. email as a mailto link with no visible text, or a LinkedIn URL behind link text) — invisible to an ATS. The email must appear as printed text.
- **No garbled output.** `?` replacement characters mean a font is embedded without a Unicode mapping — rare with Google Docs exports but possible with unusual fonts.
- **Reading order matches the visual order.** A single-column `.docx` is safe; a multi-column or text-box-heavy Google Docs layout can interleave unrelated lines in the extraction — flag prominently if so.
- **Keyword coverage.** Match the posting's required/preferred terms against the extracted text, in the posting's language. Prefer the posting's exact term over a synonym when truthfully applicable. Never add a keyword the profile does not support.

## Page Budget - Hard 1-Page Limit

The CV **must** fit on exactly 1 page when rendered. Use these content limits as a guide (tighter than a 2-page CV):

| Section | Max budget |
|---------|-----------|
| Profile statement | 2-3 lines |
| Skills | 3-5 items, each 1 line |
| Most recent role | 3-4 bullets |
| Previous role | 2-3 bullets |
| Older roles | 1 bullet (1 line) |
| Education | 2 entries, 1 line each |
| Languages | 1 line (merge with Certifications if needed) |
| References | Omit (implied on a 1-page CV) |

**If in doubt, cut rather than squeeze.** Shrinking fonts below ~10pt or removing all spacing to force-fit content makes the CV look cramped — prefer cutting a low-relevance bullet.

## Relevance-weighted cutting (the right way to shrink a CV)

**Cut by signal, not by section.** Static priority lists ("remove oldest education first, then shorten the earliest role...") are wrong when a relevant "lower-priority" item is competing with an irrelevant "higher-priority" item. An older-role bullet that speaks directly to the posting is worth more than a recent-role bullet that does not.

For every candidate line, score three things:

1. **Relevance to THIS posting** — does the line hit a named tool, keyword, or stated responsibility in the job ad?
2. **Uniqueness** — is it the only place this claim appears, or is it duplicated elsewhere in the CV?
3. **Narrative load** — does the cover letter depend on it? If cutting the line would force you to rewrite a cover-letter paragraph, it is load-bearing.

Cut the lowest-total-score line first, regardless of which section it sits in.

### Practical order of cuts (easiest → last resort)

1. **Redundancy.** If an achievement appears in both Skills AND a role bullet, the Skills version is usually the cleaner cut (the experience bullet is more concrete evidence).
2. **Profile-statement fluff.** A sentence that just restates what Skills or Experience will show.
3. **Low-relevance experience bullets.** A bullet about work that does not touch posting keywords, wherever it sits.
4. **Low-relevance supporting content.** An older-role bullet that does not speak to the target role. A certification that does not touch the posting's stack.
5. **Last-resort structural cuts.** Oldest education entry, tightening an older role to 1 bullet. These only happen if the relevance-weighted cuts above are exhausted.

### Pitfalls to avoid

- Do not mechanically cut from the bottom of a static section list without checking relevance.
- Do not cut the one concrete example the cover letter leans on.
- Do not over-cut for a near-miss (1-2 lines over 1 page). Prefer tightening margins over dropping a high-relevance bullet; reserve content cuts for genuine overflow (a full section on page 2).

## Recommended Section Order

The section order varies by role type:

**For technical / engineering roles:**
1. Profile statement / elevator pitch
2. Core competencies / Skills
3. Professional Experience (reverse chronological)
4. Education (reverse chronological)
5. Languages (& Certifications)

**For domain-specific / specialist roles:**
1. Profile statement / elevator pitch
2. Core competencies / Skills
3. Education (reverse chronological) - credentials are a key qualifier
4. Professional Experience (reverse chronological)
