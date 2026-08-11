---
name: build-application
description: Assemble a tailored resume and cover letter for one job posting. Use after analyze-posting, or when the user says "build the application", "make a resume for this job", "tailor my resume", "generate the cover letter", or "/build-application". Scores accomplishments from data/ against the posting, selects the strongest one-page set, then produces <LastName>-Resume.tex/pdf, <LastName>-Cover-Letter.md/tex/pdf, Job-Description.md/pdf, and analysis.md in outputs/<company-role>/, and updates outputs/accomplishment_scores.xlsx.
---

# Build Application

Assemble a truthful, ATS-friendly, one-page resume and a tailored cover letter
for a single posting. Every line traces to `data/`. Read `CLAUDE.md` fully
before generating anything; the writing rules and banned-word list are strict.

## Precondition

`jobs/<company-role>/keywords.json` must exist. If it does not, run
`analyze-posting` first (or ask the user for the posting).

## Step 1: Load the knowledge base

Read `data/profile.md`, `data/education.md`, `data/skills.md`, and every file in
`data/accomplishments/`. Read `jobs/<company-role>/keywords.json` and
`parsed_posting.md`, plus `company_research.md` if it exists (produced by
`research-company`; it feeds the cover letter).

`data/` is the only source of truth. If an older resume, CV, or transcript is
lying around the repo and contradicts it, the knowledge base wins. Where
`data/profile.md` records a "to confirm" note on a detail (a GPA that differs
between documents, a graduation term), use the value it tells you to use and
never split the difference yourself.

## Step 2: Score the accomplishments

Score each accomplishment against the posting. Suggested criteria (weight to
taste): required skills matched, preferred skills matched, software overlap,
discipline overlap, leadership/communication relevance.

Record scores in `outputs/accomplishment_scores.xlsx` (one of only two loose
files allowed in `outputs/`; everything else goes in per-job subfolders). Keep
one row per accomplishment and a column group per job so scores accumulate
across postings. Use the xlsx skill if needed to read/write the workbook.

If that file does not exist yet, copy `templates/accomplishment_scores.xlsx`
into `outputs/` first. Copy it rather than building a new workbook: it carries
the sheet names and the layout convention documented on its Legend sheet.

## Step 3: Select and order

Pick the strongest set that still fits one page. Map them to the two experience
sections (Relevant Experience, Work Experience) and order by relevance to the
posting, not by date. Education stays fixed.

## Step 4: Generate the resume

Clone `templates/resume.tex` to `outputs/<company-role>/<LastName>-Resume.tex`. The
deliverable is always named `<LastName>-Resume` regardless of the job, where
`<LastName>` is the surname in `data/profile.md`. Employers receive a file named
after the applicant, not after their own job posting.

Change only the experience entries and their order; keep the layout, header,
Education block, and formatting exactly. The contact line is fixed at
`phone | email | portfolio` (the latter two clickable via `hyperref`), whatever
the user set during setup. Never drop a segment of it to buy space; cut a bullet
instead.

Use each selected accomplishment's "Resume Bullet Candidates". Rewrite bullets
only when needed to surface posting keywords, and keep them honest. Weave
keywords naturally; never keyword-stuff. Keep it one page.

Compile with the local LaTeX install. This produces `<LastName>-Resume.pdf`:

```
latexmk -pdf -interaction=nonstopmode -halt-on-error \
  -outdir=outputs/<company-role> \
  outputs/<company-role>/<LastName>-Resume.tex
```

Or with `pdflatex` directly:

```
pdflatex -interaction=nonstopmode -halt-on-error \
  -output-directory=outputs/<company-role> \
  outputs/<company-role>/<LastName>-Resume.tex
```

If a new terminal cannot find `pdflatex`, the LaTeX distribution's bin directory
is not on PATH. Add it, or call the binary by its full path. Common locations:

- **Windows (MiKTeX):** `%LOCALAPPDATA%\Programs\MiKTeX\miktex\bin\x64`
- **macOS (MacTeX):** `/Library/TeX/texbin`
- **Linux (TeX Live):** `/usr/local/texlive/<year>/bin/x86_64-linux`

On MiKTeX, leave auto-install enabled (`[MPM]AutoInstall=1`) so the first
compile fetches missing packages silently instead of prompting.

Confirm one page and check the `.log` for Overfull/Underfull boxes. Then delete
`.aux`, `.log`, `.out` build artifacts (`latexmk -c` also clears them). Do this
after Step 5, since the cover letter compile creates its own set.

## Step 5: Generate the cover letter

Clone `templates/cover_letter.md` to
`outputs/<company-role>/<LastName>-Cover-Letter.md` and fill every
`{{PLACEHOLDER}}`. The deliverable is always named `<LastName>-Cover-Letter`. Never
repeat resume bullets; explain why this role, why this company, and why two
specific experiences matter. If `jobs/<company-role>/company_research.md`
exists, use its Hooks for the "why this company" paragraph: reference a real
product, project, or site fact and connect it honestly to the user's experience.
No research file means no company specifics; write from the posting alone
rather than inventing any.

Fill the YAML block at the top (`date`, `recipient`, `company`) and write the
body as plain paragraphs. Do **not** retype the letterhead, date, recipient
block, or signature into the body: `templates/cover_letter.tex` draws all of
that. Strip the template's HTML comments, then render in **two steps**, so the
letter leaves an editable `.tex` source beside its PDF exactly like the resume:

```
pandoc outputs/<company-role>/<LastName>-Cover-Letter.md \
  -o outputs/<company-role>/<LastName>-Cover-Letter.tex \
  --template=templates/cover_letter.tex
```

```
pdflatex -interaction=nonstopmode -halt-on-error \
  -output-directory=outputs/<company-role> \
  outputs/<company-role>/<LastName>-Cover-Letter.tex
```

Step one expands the pandoc template into a standalone LaTeX document; step two
compiles it. The PDF is byte-identical to rendering straight to PDF in one
pandoc call, so nothing about the layout changes. Both files are deliverables:
`<LastName>-Cover-Letter.tex` is what you hand-edit when a letter needs a tweak the
markdown cannot express.

Notes that matter (learned the hard way):
- Pass **no** `-V geometry / fontsize / colorlinks` flags and no
  `--include-in-header`. The template owns margins (1in, not the 0.5in resume),
  11pt Helvetica, and the blue clickable email. Those flags are now dead weight.
- The template guarantees one page. It typesets the letter, measures it, and
  steps down through four spacing/size presets until it fits. A letter of the
  intended length never leaves the first preset, so shrinking is a signal the
  prose is long, not a licence to keep writing.
- If the letter overflows even the smallest preset, the **`pdflatex` step**
  fails with no PDF and prints `Cover letter does not fit on one page even at
  the smallest preset`. That is intentional. Pandoc still writes the `.tex`,
  since it does no typesetting, so the missing PDF is the signal. Cut the body
  back to three or four paragraphs; do not fight the template.
- Never redirect the compile log to `<name>.out`. `hyperref` writes its own
  `.out` file under that name and will read yours back as bookmark data, which
  fails with a confusing `Missing { inserted` error.
- Layout spec and field reference: `templates/COVER_LETTER_FORMAT.md`.
- `templates/pandoc-header.tex` is still used by the job description in Step 6.
  It makes the body font Helvetica (a scalable Arial match) so it matches the
  resume AND so microtype font expansion works. Without it, pandoc's default
  bitmap font triggers `pdfTeX error (font expansion): auto expansion is only
  possible with scalable fonts`.

## Step 6: Generate the human-readable job description

Write `outputs/<company-role>/Job-Description.md` so the posting sits next to
the documents you submitted, for interview prep. Source it from
`jobs/<company-role>/parsed_posting.md` and `keywords.json`.

- **Quote the posting directly.** Use the employer's own wording verbatim; do
  not paraphrase, summarize away detail, or invent requirements. If the source
  was a URL, include it at the top.
- Organize for fast review with these headings (omit any the posting lacks):
  `Company`, `Role`, `Location`, `Employment type`, `Source`, then
  `## Role Summary`, `## Key Responsibilities`, `## Required Qualifications`,
  `## Preferred Qualifications`, `## About the Company`,
  `## Compensation & Logistics`.
- Keep responsibilities and qualifications as the posting's bullet lists so they
  read cleanly alongside the resume.

Then render a matching PDF for side-by-side review:

```
pandoc outputs/<company-role>/Job-Description.md \
  -o outputs/<company-role>/Job-Description.pdf \
  -V geometry=margin=0.75in -V fontsize=11pt \
  --include-in-header=templates/pandoc-header.tex
```

## Step 7: Write analysis.md

Write `outputs/<company-role>/analysis.md` covering: extracted keywords,
selected accomplishments (and why), omitted accomplishments (and why), missing
skills the posting wants that the KB cannot back, an ATS match estimate, and
suggested improvements (including any honest gaps the user could close).

## Step 8: Update the application tracker

If `outputs/Application-Tracker.xlsx` does not exist yet, copy
`templates/Application-Tracker.xlsx` into `outputs/` first. Copy it rather than
creating one: it carries the column headers, the Status dropdown on column F,
and the Legend sheet.

Add a row for this job to `outputs/Application-Tracker.xlsx` (sheet
`Applications`) with openpyxl: Company, Role, Folder (slug), Location, Type,
Status `Built`, today's date as Date Built, the ATS estimate from `analysis.md`
(as text, for example `~80%`), and any eligibility flag or open question in
Next Step / Notes. If a row for the slug already exists (a rebuild), update it
instead of adding a duplicate. Write into the next pre-formatted blank row and
preserve the sheet's formatting, Status dropdown, and the Legend sheet. The
tracker and `accomplishment_scores.xlsx` are the only loose files in
`outputs/`.

## Step 9: Quality checklist

Before finishing, verify against `CLAUDE.md`: no fabrication, no repeated
bullets or phrases, no banned words or em dashes, active voice, correct
grammar/spelling, keywords incorporated naturally, formatting unchanged, and the
resume is one page. Confirm the job description quotes the posting accurately and
invents nothing.

## Output

`outputs/<company-role>/` should contain `<LastName>-Resume.tex`,
`<LastName>-Resume.pdf`, `<LastName>-Cover-Letter.md`, `<LastName>-Cover-Letter.tex`,
`<LastName>-Cover-Letter.pdf`, `Job-Description.md`, `Job-Description.pdf`, and
`analysis.md`. The job also
has a `Built` row in `outputs/Application-Tracker.xlsx` and scores recorded in
`outputs/accomplishment_scores.xlsx`.
