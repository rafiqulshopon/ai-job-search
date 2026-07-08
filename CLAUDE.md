# Job Application Assistant for Muhammad Rafiqul Islam

<!-- SETUP: This file is populated by running /setup -->
<!-- After running /setup, all [PLACEHOLDER] tokens will be replaced with your actual information -->

## Role
This repo is a job application workspace. Claude acts as a career advisor and application assistant for Muhammad Rafiqul Islam, helping with:
1. **Job fit evaluation** - Assess job postings against your profile (skills, experience, behavioral traits)
2. **CV tailoring** - Tailor the Google Docs (.docx) CV to target specific roles (via `cv/tailor_docx.py`)
3. **Cover letter writing** - Draft targeted cover letters using existing templates (LaTeX)
4. **Interview preparation** - Prepare answers, questions, and talking points for interviews
5. **Career strategy** - Advise on positioning and personal branding

## Candidate Profile

<!-- This section is auto-populated by /setup. You can also fill it in manually. -->

### Identity
- **Name:** Muhammad Rafiqul Islam
- **Location:** Mirpur, Dhaka, Bangladesh
- **Languages:** Bengali (native), English (professional)
- **Status:** Employed - Senior Frontend Engineer at Blaze Digital Solutions (Feb 2024-present); finishing M.Sc. in parallel. **Open to onsite AND remote roles anywhere in the world if visa sponsorship is provided, and to all Bangladesh roles (onsite and remote).** Wide-net strategy: applies to roles from ~50% match upward.
- **LinkedIn headline:** "Senior Frontend Engineer | React, Next.js, TypeScript | Building EdTech & SaaS products"

### Education
<!-- List your degrees, most recent first -->
- **M.Sc. in Computer Science & Engineering** (May 2024-Sep 2026, in progress) - Jahangirnagar University, Savar, BD
- **B.Sc. in Computer Science & Engineering** (Jan 2017-Jun 2021) - R. P. Shaha University, Narayanganj, BD

### Professional Experience
<!-- List your roles, most recent first -->
- **Senior Frontend Engineer** (Feb 2024 - Present) - **Blaze Digital Solutions** (Chittagong, BD)
  - Led frontend for an EdTech platform from scratch, architecting a micro-frontend structure across 5 modules
  - Designed the GraphQL data layer (Apollo Client) cutting redundant API calls by 20%; tuned Webpack for load times
  - Set up Jest unit testing and GitHub Actions CI/CD; implemented WCAG accessibility (ARIA, keyboard nav)
- **JavaScript Developer** (Jul 2022 - Jan 2024) - **Gain Solutions Ltd** (Dhaka, BD)
  - Built features for a Norwegian property platform serving 10,000+ partners (lease mgmt, real-time chat via Pusher)
  - Co-led frontend architecture for Payrun (SaaS HRM, 50+ clients); sole frontend lead for a support-ticket system (team of 5)
  - Employee of the Month x2
- **Frontend Developer** (Jul 2021 - Jun 2022) - **Bitwise Lab** (Narayanganj, BD)
  - Delivered 5 client projects (e-commerce, healthcare) in an agency team

### Technical Skills
- **Primary:** JavaScript, TypeScript, React.js, Next.js, Redux Toolkit, Zustand, React Query, Apollo GraphQL, Tailwind CSS, frontend architecture (micro-frontends, state management, data-layer design), accessibility (WCAG/ARIA), Jest
- **Secondary:** NestJS, Prisma, TypeORM, RESTful & GraphQL APIs, PostgreSQL, MongoDB, Docker, Webpack, CI/CD (GitHub Actions)
- **Domain:** SaaS/HRM platforms, EdTech, property/real-estate management, real-time (chat) systems
- **Software:** Git, Docker, Jest, GitHub Actions, Webpack, Agile/Scrum

### Certifications
<!-- List relevant certifications with dates -->
- **Fundamentals of Backend Engineering**
- **10 Days of JS**
- **Frontend Developer (React)**
- **Next.js From Scratch**
- **JavaScript Data Structures & Algorithms + LeetCode Exercises**

### Publications
<!-- List peer-reviewed publications, if any -->
- *(None)*

### Awards
<!-- List relevant awards, hackathons, competitions -->
- Employee of the Month x2 - Gain Solutions Ltd (2022-2024)

### Behavioral Profile
<!-- Your behavioral assessment results (PI, DISC, Myers-Briggs, or self-assessment) -->
- *(Inferred from LinkedIn About - no formal assessment on file)*
- **End-to-end ownership** - prefers owning a problem fully rather than staying in one lane
- **Cross-stack curiosity** - frontend-strong, actively growing into backend (NestJS, Prisma, GraphQL)
- **Initiative / leadership** - repeatedly steps into frontend-lead roles
- **Strengths:** architecture ownership, measurable performance work, mentoring, bridging frontend and backend
- **Growth areas:** deeper backend/system-design expertise (frame as active growth)
- **Thrives in:** autonomous, ownership-driven roles; international/remote collaboration

### What Excites You
<!-- What motivates you professionally -->
- Owning frontend architecture and building products from scratch
- Bridging frontend into backend and going deeper on the full stack
- Measurable performance and accessibility work
- Mentoring engineers and leading through code review
- International and remote opportunities on global products

### Target Sectors
<!-- Industries and companies you're targeting -->
- EdTech, SaaS/HRM, property/real-estate tech, real-time/messaging platforms
- Remote-first product companies (global); any sector with React/Next.js/TypeScript stacks

### Deal-breakers
<!-- Hard constraints on job search -->
- Onsite roles abroad with **no visa sponsorship and no relocation support** (cannot accept)
- *(Otherwise running a wide net - open to ~50% match and above, onsite or remote, globally with visa or within Bangladesh)*

## Repo Structure
- `cv/` - .docx CV (Google Docs master `main_example.docx`, tailored per role via `tailor_docx.py`)
- `cover_letters/` - LaTeX cover letters (custom cover.cls template)
- `.claude/skills/` - AI skill definitions for the application workflow
- `.agents/skills/` - Job search CLI tools

## Workflow for New Job Applications
1. User provides a job posting (URL or text)
2. **Always evaluate fit first**: skills match, experience match, behavioral/culture match. Present this assessment to the user before proceeding.
3. If good fit: create targeted CV (`cv/main_<company>.docx`) and cover letter (`cover_letters/cover_<company>_<role>.tex`)
4. **Verify both documents** (see Verification Checklist below)
5. Prepare interview talking points based on the role requirements and your strengths

**Important:** When mentioning agentic coding or AI tooling in CVs/cover letters, explicitly reference **Claude Code** by name.

## Verification Checklist
After creating or updating a CV or cover letter, re-read the generated file and verify **all** of the following before presenting to the user. Report the results as a pass/fail checklist.

### Factual accuracy
- [ ] All claims match actual profile (CLAUDE.md / candidate profile) - no fabricated skills, experience, or achievements
- [ ] Job titles, dates, company names, and locations are correct
- [ ] Contact details are correct
- [ ] All company-specific claims (partnerships, products, technology, expansions) have been independently verified via WebFetch/WebSearch - do not trust reviewer agent research without verification

### Targeting
- [ ] Profile statement / opening paragraph is tailored to the specific role (not generic)
- [ ] Skills and experience bullets are reframed to match the job requirements
- [ ] Key job requirements are addressed (with gaps acknowledged where relevant)
- [ ] Nice-to-have requirements are highlighted where there is a match

### Consistency
- [ ] CV follows the standard 1-page Google Docs (.docx) format, styling preserved
- [ ] Cover letter uses cover.cls template and established structure
- [ ] Tone is consistent across CV and cover letter
- [ ] No contradictions between CV and cover letter content

### Quality
- [ ] No document errors (CV: .docx styling intact, no broken/blank paragraphs from tailor_docx.py edits; cover letter: balanced LaTeX braces, correct commands)
- [ ] No spelling or grammar errors
- [ ] Agentic coding / AI tooling references mention **Claude Code** by name
- [ ] Cover letter is addressed to the correct person (or "Dear Hiring Manager" if unknown)
- [ ] Cover letter fits approximately one page

### Rendered PDF verification (MANDATORY - never skip)
Both documents MUST be rendered to PDF and visually inspected via the Read tool. "Looks fine in the source" is not acceptable - the CV (.docx via LibreOffice) and cover letter (.tex via xelatex) can both render to broken layouts. Iterate until these all pass:
- [ ] CV rendered with **LibreOffice** (`soffice --headless --convert-to pdf`) from the tailored `.docx` (no Word/LaTeX needed). Cover letter compiled with **xelatex** (cover.cls requires fontspec).
- [ ] **CV is exactly 1 page** - not 2
- [ ] **CV is a single page** - nothing spills to page 2. The CV is a .docx; there are no LaTeX page-break commands. Fix overflow by trimming content, tightening the .docx page margins via `tailor_docx.py`, or cutting a low-relevance bullet (see 05-cv-templates.md "Fixing overflow"). Do NOT shrink fonts below ~10pt.
- [ ] **Cover letter is exactly 1 page** - signature block must fit with the body, never overflow
- [ ] **Cover letter bullet font matches body font** - `\lettercontent{}` must not wrap `\begin{itemize}...\end{itemize}` (the command's trailing `\\` errors on `\end{itemize}`, and moving itemize outside loses the Raleway font). Standard pattern: close `\lettercontent{}`, then wrap the list in `{\raggedright\fontspec[Path = OpenFonts/fonts/raleway/]{Raleway-Medium}\fontsize{11pt}{13pt}\selectfont \begin{itemize}...\end{itemize}\par}`

### ATS & keyword verification (CV)
ATS parsers read the PDF's embedded text layer, not the rendered page. Extract it with `pdftotext -layout` and verify what a parser sees. `pdftotext` (poppler) is optional - if missing, skip the parseability items with a warning and check keyword coverage from the visual PDF read instead.
- [ ] CV text layer extracts cleanly - no `(cid:*)` markers, `�` replacement characters, or text visible in the PDF but absent from the extraction
- [ ] Email and phone appear as **literal text** in the extraction (a .docx uses plain text, so these usually extract cleanly; the failure mode is a contact detail carried only by a hyperlink target - invisible to ATS)
- [ ] Reading order of the extracted text matches the visual order (single-column .docx is safe; multi-column or text-box-heavy Google Docs layouts are where this breaks)
- [ ] Posting keywords covered or honestly absent - synonym-only matches tightened to the posting's exact term where truthfully applicable, keywords the profile genuinely supports added to experience bullets, genuine gaps left visible and **never stuffed**
