# /apply - Drafter-Reviewer Job Application Workflow

You are orchestrating a two-agent job application workflow. The job posting is provided below as `$ARGUMENTS` (either a URL or pasted text).

Follow these steps **exactly in order**. Do not skip steps.

**Token-efficiency rules for this workflow:**
- Never re-Read a file whose contents are already in your context from an earlier step. If you read it in Step 1, it is still available in Step 2.
- When dispatching the reviewer agent, pass draft content **inline in the agent prompt** rather than asking the agent to Read files you already have in memory.
- Run the full verification checklist exactly once, at the end (Step 6). The reviewer focuses on content critique, not verification.
- Step 5 (render and inspect PDFs) is mandatory and non-skippable — a `.docx`/`.tex` source that looks fine can render to a broken PDF (CV spilling to page 2, styling broken by a python-docx edit, cover letter to 2 pages, bullet fonts mismatching).

---

## Step 0: Parse Input

- If `$ARGUMENTS` looks like a URL, use `WebFetch` to retrieve the job posting content.
- If it is pasted text, use it directly.
- Extract: **company name**, **role title**, **department** (if mentioned), **location**, and **language** of the posting (Danish or English).
- Store these for use throughout the workflow.

---

## Step 1: DRAFTER - Evaluate Fit

Read the evaluation framework:
- `.claude/skills/job-application-assistant/04-job-evaluation.md`
- `.claude/skills/job-application-assistant/01-candidate-profile.md`

Using the framework from `04-job-evaluation.md`, evaluate the job posting against the candidate's profile. If the salary lookup tool is configured, run:

```bash
python salary_lookup.py "<Company Name>" --json
```

If the posting specifies a city, add `--city "<City>"` to narrow results. Parse the JSON output and include the salary benchmark in the evaluation. If the tool is not configured or returns an error, skip the salary benchmark.

Present the evaluation to the user with:

1. **Skills match** - which required/preferred skills match vs. gaps
2. **Experience match** - how work history maps to the role
3. **Behavioral/culture match** - how behavioral profile fits the role/company culture
4. **Salary benchmark** - salary index for the company (if available)
5. **Overall fit score** and recommendation (strong fit / moderate fit / weak fit)

After presenting the evaluation, ask the user:
> "Should I proceed with drafting the CV and cover letter for this role?"

**If the user says no, stop here.** If yes, continue to Step 2.

---

## Step 2: DRAFTER - Draft CV + Cover Letter

You already have `01-candidate-profile.md` and `04-job-evaluation.md` in context from Step 1. **Do not re-read them.**

Read only the reference files you do not yet have:
- `.claude/skills/job-application-assistant/03-writing-style.md`
- `.claude/skills/job-application-assistant/05-cv-templates.md`
- `.claude/skills/job-application-assistant/06-cover-letter-templates.md`

Also read the most recent existing CV and cover letter for concrete structural reference (one of each is enough):
- Inspect the master `cv/main_example.docx` (or any existing `cv/main_*.docx`) via `python3 cv/tailor_docx.py read cv/main_example.docx` to see its section structure
- Read any existing `cover_letters/cover_*.tex` or `cover_letters/Cover_*.tex` file as a LaTeX template reference

### CV (`cv/main_<company>.docx`)
- Always in **English**
- Build from the master `cv/main_example.docx` using the `cv/tailor_docx.py` helper (python-docx), which preserves the Google Docs styling. See `05-cv-templates.md` for the section-level operations (`copy_master`, `set_profile`, `set_experience_bullets`, `set_skills`).
- Tailor the profile statement and experience bullets to the specific role
- Reframe skills and achievements to match job requirements
- Keep to **1 page**

### Cover Letter (`cover_letters/cover_<company>_<role>.tex`)
- **Match the language of the job posting** (Danish posting -> Danish cover letter, English posting -> English cover letter)
- Follow the structure from `06-cover-letter-templates.md`
- Use the `cover.cls` template
- Tailor the opening paragraph to the specific role and company
- Address to a named person if available in the posting, otherwise "Dear Hiring Manager" (or equivalent in posting language)
- Keep to approximately one page
- Any mention of agentic coding or AI tooling must reference **Claude Code** by name

Write both files to disk. Keep the exact text of both drafts in working memory — you will pass them inline to the reviewer in Step 3 and revise them in Step 4 without re-reading.

---

## Step 3: REVIEWER - Research & Critique

Use the **Agent tool** to spawn a `general-purpose` reviewer agent. The reviewer gets a fresh context, so pass the drafts **inline in the prompt** below (do not make the reviewer Read them). Scope the reviewer's file reads to content-critique essentials only — the reviewer does not need the template files (`05`, `06`) to critique content, since those govern document structure the drafter already applied.

Replace `<COMPANY>`, `<ROLE>`, `<INSERT_JOB_POSTING_TEXT_HERE>`, `<INSERT_CV_DRAFT_HERE>`, and `<INSERT_COVER_LETTER_DRAFT_HERE>` with actual values before dispatching.

```
You are a hiring manager proxy reviewing a job application. Your job is to make the application as targeted and compelling as possible.

## Your Tasks

### 1. Research the Company
Use WebSearch and WebFetch to research:
- The company's website, mission, and recent news
- The specific department or team (if mentioned in the posting)
- Any recent projects, press releases, or strategic initiatives relevant to the role
- Company culture and values

### 2. Read Reference Materials (content-critique only)
Read these four files — and only these — to ground your critique:
- `.claude/skills/job-application-assistant/01-candidate-profile.md`
- `.claude/skills/job-application-assistant/02-behavioral-profile.md` — use this specifically to check whether the cover letter's voice matches the candidate's natural register. A "Collaborator" PI profile, for example, should not be given a combative, solo-hero tone; a "Persuader" profile should not be given over-hedged, apologetic phrasing.
- `.claude/skills/job-application-assistant/03-writing-style.md`
- `.claude/skills/job-application-assistant/04-job-evaluation.md`

Do NOT read `05-cv-templates.md` or `06-cover-letter-templates.md` — those govern document structure the drafter already applied and are not needed for content critique.

### 3. Drafts to Review
Both drafts are provided inline below. Do NOT use the Read tool on the draft files — use these exact texts.

<CV_DRAFT file="cv/main_<COMPANY>.docx">
<INSERT_CV_DRAFT_HERE>
</CV_DRAFT>

<COVER_LETTER_DRAFT file="cover_letters/cover_<COMPANY>_<ROLE>.tex">
<INSERT_COVER_LETTER_DRAFT_HERE>
</COVER_LETTER_DRAFT>

### 4. Job Posting
<JOB_POSTING>
<INSERT_JOB_POSTING_TEXT_HERE>
</JOB_POSTING>

### 5. Produce Feedback

Return your feedback in **two parts**:

**Part A — Structured edits (preferred format whenever possible):**
A JSON array of concrete edits the drafter can apply directly without re-reading the files. Each edit is an object:
```json
{
  "file": "cv/main_<COMPANY>.docx" | "cover_letters/cover_<COMPANY>_<ROLE>.tex",
  "old_string": "<exact text currently in the draft>",
  "new_string": "<replacement text>",
  "reason": "<one-line rationale: keyword match / company angle / reframing / style>"
}
```
Only use this format when you can quote the exact `old_string` from the drafts above. Make `old_string` unique — include enough surrounding context so it matches exactly once per file.

**Part B — Narrative suggestions (for judgment calls that are not mechanical edits):**
Prose suggestions grouped by category. Produce each category even if your finding is "no issues" — silence on a category can be mistaken for skipping it.
- **Missed keywords/requirements** — what to add and roughly where, if it cannot be expressed as a clean string replacement
- **Company/department-specific angles** — connections between experience and the company's strategic priorities, based on your research
- **Action-oriented reframing** — identify passive, generic, or low-energy statements and suggest action-oriented rewrites. Use this category especially for structural weakness that doesn't fit a single-sentence swap (e.g., "the whole opening paragraph reads as passive — restructure around your single strongest match to the posting").
- **Tone and style issues** — check against `03-writing-style.md` AND `02-behavioral-profile.md`. Flag any issues with tone, formality, or voice (cliches, hedging, over-humility, inconsistent register), and specifically flag any mismatch between the letter's voice and the candidate's natural register as described in the behavioral profile.

**CRITICAL RULE:** All suggestions must be grounded in actual profile data. Do NOT suggest fabricating skills, experience, or achievements. If a requirement is a gap, say so honestly and suggest how to frame adjacent experience instead.

Do **not** run a verification checklist — the drafter will do that in the final step. Focus on content critique.

Return Part A and Part B together as a single structured message.
```

---

## Step 4: DRAFTER - Revise Based on Feedback

Once the reviewer agent returns its feedback:

1. **Apply Part A (structured edits).** For the **cover letter** (`.tex`), apply each edit directly with the `Edit` tool — you already have the draft in context from Step 2, and the reviewer's `old_string` values were quoted from that text. For the **CV** (`.docx`), the `Edit` tool cannot touch a binary `.docx` — translate each string edit into the matching `tailor_docx.py` operation (profile-text swap → `set_profile`; a bullet reword → `set_experience_bullets`; skills rewrite → `set_skills`) and apply via the helper. Skip any edit whose rationale would require fabricating content.
2. **Apply Part B (narrative suggestions)** using judgment. These need interpretation, not mechanical replacement. Walk through every Part B category the reviewer returned and address it:
   - **Missed keywords/requirements:** add the keyword or capability where it fits naturally in the CV or cover letter. Prefer the experience bullets (concrete evidence) over the profile statement (abstract claim).
   - **Company/department-specific angles:** weave the reviewer's research into the cover letter opening or motivation paragraph. Verify every company claim via WebFetch/WebSearch before including it — do not trust reviewer research at face value.
   - **Action-oriented reframing:** rewrite passive or generic phrasing (CV profile statement, cover letter opening, bullet leads). Structural weakness that the reviewer flagged without a clean JSON edit lives here.
   - **Tone and style issues:** apply the writing-style-guide fixes (no em-dashes, no cliches, no apologetic hedging, consistent first-person active voice).
   For the cover letter use `Edit`; for the CV use the `tailor_docx.py` helper. Only re-read a file if an edit fails because the surrounding text has shifted.
3. Do NOT incorporate any suggestion that would fabricate skills or experience. If a posting requirement is a genuine gap, acknowledge it honestly and frame adjacent experience instead.

After all edits are applied, the two files on disk are the final drafts.

---

## Step 5: DRAFTER - Render & Inspect PDFs (MANDATORY)

**Never skip this step.** The `.docx`/`.tex` source looking fine is not sufficient — rendering can produce broken layouts (CV spilling to page 2, styling broken by a python-docx edit, cover letter to 2 pages, bullet fonts not matching body text). Render/compile both documents and visually verify the PDFs before presenting.

### 5a. Render / compile

```bash
# CV: .docx -> PDF via LibreOffice headless (no Word/LaTeX needed)
soffice --headless --convert-to pdf --outdir cv cv/main_<company>.docx
# Cover letter: LaTeX -> PDF via xelatex (cover.cls requires fontspec)
cd cover_letters && xelatex -interaction=nonstopmode cover_<company>_<role>.tex
```

- CV uses **LibreOffice** (`soffice --headless --convert-to pdf`) — renders the `.docx` (authored/edited via the `tailor_docx.py` helper / python-docx) to PDF.
- Cover letter uses **xelatex** — `cover.cls` requires fontspec.

If either step fails, fix the error and re-run until clean.

### 5b. Inspect layout

Read both PDFs via the Read tool and verify:

**CV (`cv/main_<company>.pdf`):**
- [ ] Exactly 1 page (nothing spilled to page 2)
- [ ] Google Docs styling preserved — fonts, sizes, colors, bullet styles, spacing match the master; no broken/blank paragraphs, no dropped formatting from the python-docx edits
- [ ] All sections present and in the intended order; no orphaned section headings
- [ ] No awkward whitespace gaps or layout shifts introduced by the edits

**Cover letter (`cover_letters/cover_<company>_<role>.pdf`):**
- [ ] Exactly 1 page
- [ ] Signature block visible, not cut off or pushed to a second page
- [ ] Bullet list font matches surrounding body text (both should be Raleway-Medium)

### 5c. Iterate until clean

If the layout has problems, fix and re-render. Common fixes (see `05-cv-templates.md` and `06-cover-letter-templates.md` for full details):

- **CV spills to page 2 (near-miss, 1-2 lines):** trim the profile statement by a line, or tighten the `.docx` margins slightly via the helper. Do NOT shrink fonts below ~10pt to squeeze.
- **CV spills to page 2 (a whole section):** cut content using **relevance-weighted cutting** (see `05-cv-templates.md` → "Relevance-weighted cutting"). Score each candidate line by (a) relevance to THIS posting's keywords and responsibilities, (b) uniqueness (is it duplicated elsewhere?), (c) narrative load (does the cover letter depend on it?). Cut the lowest-total-score line first, regardless of section. Re-apply the cut via the `tailor_docx.py` helper, then re-render.
- **CV styling broke (wrong font/size, broken bullet, blank paragraph):** a python-docx edit touched a run/paragraph it shouldn't have. Re-do that edit using the helper's section-level operations (which preserve styles) rather than raw text replacement.
- **Cover letter itemize breaks compile or uses wrong font:** close `\lettercontent{}` before the list, wrap the list in `{\raggedright\fontspec[Path = OpenFonts/fonts/raleway/]{Raleway-Medium}\fontsize{11pt}{13pt}\selectfont \begin{itemize}...\end{itemize}\par}`
- **Cover letter spills to 2 pages:** trim using the same relevance-weighted logic. First cut: sentences that restate what a bullet already said. Second cut: a bullet that does not hit posting keywords. Last resort: a bullet that does hit posting keywords. Never reduce geometry or line spacing.

Do not proceed to Step 6 until both PDFs pass inspection.

### 5d. ATS & keyword verification (CV)

An ATS parser reads the PDF's embedded **text layer**, not the rendered page — a CV that passed visual inspection can still extract as garbage (icon glyphs where the contact details should be, scrambled reading order in multi-column layouts). This step verifies what a parser actually sees. It applies to the **CV only**; cover letters rarely go through keyword screening.

**Availability check:** run `pdftotext -v`. `pdftotext` (poppler) is an optional dependency, not part of TeX distributions. If it is missing, print a one-line warning that the mechanical parse check is skipped, do the keyword-coverage check (item 3 below) against your visual Read of the PDF instead, and note the degraded mode in the Step 6 report. Same graceful-skip pattern as the salary lookup.

**1. Extract the text layer:**

```bash
cd cv && pdftotext -layout main_<company>.pdf main_<company>.txt
```

Read the `.txt` file.

**2. Parseability checks** on the extracted text:

- [ ] **Text extracted at all**, with no garbage runs: no `(cid:NNN)` markers, no `�` replacement characters, no stretches of missing text that are visible in the PDF
- [ ] **Email and phone survive as literal text.** In a `.docx` CV these are normally plain text (Google Docs doesn't use icon fonts), so they should extract cleanly. The failure mode to watch for is a contact detail carried only by a hyperlink target (e.g. the email as a mailto link with no visible text, or a LinkedIn URL behind link text) — invisible to an ATS. The email address must appear as printed text.
- [ ] **Reading order matches the visual order** — section headings appear in the same sequence as on the page, and lines from different sections are not interleaved. A single-column `.docx` is safe; a multi-column or text-box-heavy Google Docs layout is where this breaks — flag prominently if so.
- [ ] **Dates recognizable** — each role and degree has its years present in the extraction.

Failures here are document-level problems: fix them in the `.docx` via the `tailor_docx.py` helper (e.g. ensure the email is printed as text, not only a hyperlink), then re-render (5a) and re-extract. If the master `.docx`'s layout fundamentally scrambles extraction order, tell the user prominently — they may be trading ATS compatibility for looks.

**3. Keyword coverage.** Reuse the required/preferred keyword list you extracted in Step 1 — do not re-derive it. Match each keyword against the extracted text, **in the posting's language** (a Danish posting's keywords are matched in Danish even though the CV is in English — where the CV legitimately covers the concept in English, count it as synonym-only and note the language difference). Report a table:

| Keyword | Priority | Status | Note |
|---------|----------|--------|------|
| ... | required/preferred | covered / synonym-only / missing (have it) / missing (gap) | where it appears, or why absent |

- **covered** — the term appears (verbatim or trivial inflection).
- **synonym-only** — the concept is present under a different term. If the posting's exact term is truthfully applicable per the profile, prefer the posting's term (ATS keyword matches are often literal).
- **missing (have it)** — the profile shows the candidate genuinely has this skill but the CV never says it: add it where it fits naturally, preferring experience bullets (concrete evidence) over the profile statement, then re-run 5a–5c.
- **missing (gap)** — a genuine gap: leave it missing. **Never stuff keywords.** This is the same honesty rule the reviewer follows — a gap gets acknowledged in the cover letter's framing, not hidden in the CV.

**4. Clean up:** delete the extracted `.txt` file.

### 5e. Clean up build artifacts

After the final clean render/compile: the CV (`.docx`) produces no auxiliary files — keep `cv/main_<company>.docx` and `.pdf`. For the cover letter, delete the `.aux`, `.log`, `.out` files (keep the `.tex` and `.pdf`).

---

## Step 6: Present Final Output

Run the full verification checklist from `CLAUDE.md` now — this is the **only** verification pass in the workflow. Re-read both files once here to verify final state on disk matches your mental model after the Step 4 and Step 5 edits.

### Verification Checklist
Report pass/fail for each item in the CLAUDE.md verification checklist (factual accuracy, targeting, consistency, quality).

### Key Tailoring Decisions
Summarize 3-5 key decisions made to tailor the application:
- What was emphasized and why
- What company-specific angles were incorporated
- What the reviewer suggested that was most impactful
- Any gaps that were acknowledged or reframed

### Files Created
List the files written:
- `cv/main_<company>.docx` (and `cv/main_<company>.pdf`)
- `cover_letters/cover_<company>_<role>.tex` (and `.pdf`)

Tell the user: "Both files are ready for your review. Open them to check the final output before compiling."

Also mention: once they have actually submitted the application, `/outcome <company>` logs it in the tracker and starts the per-application record that `/setup` later uses to calibrate the fit framework.
