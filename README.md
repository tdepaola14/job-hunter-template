# Job Hunter

A personal career assistant that generates truthful, ATS-friendly resumes and
cover letters tailored to a specific job posting. It runs on **Claude Code
skills** working off a knowledge base you build from your own experience.
Nothing is invented: every line in a generated document traces back to `data/`.

You never edit LaTeX by hand. You talk to the skills; they read your knowledge
base and produce the documents.

For the full ruleset (writing style, banned words, accuracy-first priority), see
[`CLAUDE.md`](CLAUDE.md).

---

## Quick start

```bash
git clone <your-fork-url> job-hunter && cd job-hunter
```

Open the folder in Claude Code, then:

```
/setup
```

The setup skill interviews you, fills in your details, adapts the templates, and
walks you through your first accomplishments. Budget twenty minutes. If you have
an existing resume, point the skill at it and setup goes much faster.

Then, for any job you want to apply to:

```
/analyze-posting
```
```
/build-application
```

---

## How it works

1. Your real experience lives in `data/` as a knowledge base: profile,
   education, skills, and one STAR-format document per accomplishment.
2. For each job, a skill analyzes the posting into structured keywords.
3. Another skill scores your accomplishments against those keywords, picks the
   strongest one-page set, and assembles the resume, cover letter, and an
   analysis file, compiling everything to PDF.

The knowledge base is the whole trick. Time spent there pays off on every
application you ever send, because tailoring is a selection problem once the
material exists.

---

## Prerequisites

- **Claude Code**, opened in this repository. Project skills only load here.
- **A LaTeX distribution**, with its binaries on PATH. Verify in a new terminal
  with `pdflatex --version`.
  - Windows: [MiKTeX](https://miktex.org/). Leave auto-install enabled so the
    first compile fetches missing packages silently instead of prompting.
  - macOS: [MacTeX](https://tug.org/mactex/).
  - Linux: TeX Live (`sudo apt install texlive-latex-recommended texlive-fonts-recommended`).
- **pandoc**, for rendering cover letters and job descriptions. Verify with
  `pandoc --version`. It uses your LaTeX install as its PDF engine.
- **Python with openpyxl** (`pip install openpyxl`), for the tracking workbooks.

Want to check the toolchain before entering any real data? This compiles the
placeholder template as shipped:

```bash
pdflatex -interaction=nonstopmode templates/resume.tex
```

---

## The skills

All skills live in `.claude/skills/<name>/SKILL.md` and are auto-discovered by
Claude Code. Trigger one either by describing the task in plain language or by
typing its name as a slash command.

| Skill | Use it to | Trigger |
|-------|-----------|---------|
| `setup` | First-run setup: your details, templates, toolchain | `/setup`, or "I just cloned this" |
| `add-accomplishment` | Add a real experience to the knowledge base | `/add-accomplishment`, or "add an accomplishment" |
| `analyze-posting` | Parse a job posting into structured keywords | `/analyze-posting`, or "analyze this posting: \<path/url/text\>" |
| `research-company` | Research the employer (products, focus, news) with sources | `/research-company`, or "research \<company\>" |
| `build-application` | Assemble the tailored resume + cover letter | `/build-application`, or "build the application for \<job\>" |
| `master-resume` | Build the general-purpose resume, no posting attached | `/master-resume`, or "make my career fair resume" |
| `interview-prep` | Generate an interview prep doc from a built application | `/interview-prep`, or "I got an interview with \<company\>" |

---

## Typical workflow

### 1. Analyze the posting

Give the skill a PDF path, a URL, or pasted text:

```
/analyze-posting
```
> Analyze this posting: ~/Downloads/acme-intern.pdf

It creates `jobs/<company-role>/` containing:
- `posting.pdf` (if you provided one)
- `parsed_posting.md` (cleaned text + summary header)
- `keywords.json` (required vs preferred skills, software, languages,
  disciplines, certifications, soft skills, action verbs, recurring keywords)

### 2. Research the company (optional but recommended)

```
/research-company
```
> Research Acme for the acme-mechanical-intern job

It writes `jobs/<company-role>/company_research.md`: products, industry,
technical focus, values, recent news, and 2-4 "hooks", all with sources. The
cover letter and interview prep use it for real company specifics. Skip it and
the cover letter is written from the posting alone.

### 3. Build the application

```
/build-application
```
> Build the application for the acme-mechanical-intern job

It scores every accomplishment against the posting, selects the strongest
one-page set, and writes `outputs/<company-role>/`:
- `<LastName>-Resume.tex` and `<LastName>-Resume.pdf`
- `<LastName>-Cover-Letter.md`, `.tex`, and `.pdf` (both documents ship editable
  LaTeX next to the PDF, so you can hand-tune one letter without regenerating)
- `Job-Description.md` and `.pdf`, a clean readable copy of the posting quoting
  the employer directly, for reviewing requirements next to what you submitted
- `analysis.md` (keywords, selected vs omitted accomplishments, missing skills,
  ATS match estimate, suggested improvements)

Documents are named after **you**, not the job, since those are the files
employers receive.

It also updates `outputs/accomplishment_scores.xlsx`, the running scoreboard of
how each accomplishment rates across jobs, and adds a `Built` row to
`outputs/Application-Tracker.xlsx`.

### 4. Review

Open the PDFs in `outputs/<company-role>/`. Read `analysis.md` for the rationale
and any honest gaps worth closing. Ask for revisions in plain language ("swap the
drone project for the staple-gun FEA"), and the documents regenerate.

### 5. Track the application

`outputs/Application-Tracker.xlsx` holds one row per application: status, dates,
ATS estimate, next step, and notes (status meanings are on its Legend sheet).
Update it by hand or in plain language: "mark Acme as submitted today", "Acme
rejected me". Statuses move through Built, Submitted, Interview Scheduled,
Interviewed, and Offer / Rejected / Withdrawn / On Hold.

### 6. Prep for interviews

```
/interview-prep
```
> I got an interview with Acme

It reads the job description, analysis, company research, and the full STAR
documents behind the resume, then writes `Interview-Prep.md`/`.pdf` to
`outputs/<company-role>/`: your positioning, likely behavioral and technical
questions mapped to real experiences, honest answers for the gaps, and questions
to ask them. It also marks the tracker row `Interview Scheduled`.

---

## When there is no posting

```
/master-resume
```
> Build my master resume for the career fair

Career fairs, recruiters who ask you to send something over, and portals with no
job attached all need a resume you can hand out as-is. This skill builds one from
the knowledge base alone, choosing experiences for breadth across your target
areas instead of fit to a single job. It writes `<LastName>-Resume.(tex|pdf)` and
`selection.md` to `outputs/.master-resume/`, same format and one-page limit as
every tailored resume.

No cover letter comes with it, by design: a letter that could go to anyone reads
like it went to no one. Rerun the skill after adding an accomplishment so the
printed copy stays current.

---

## Growing the knowledge base

The assistant can only use what is in `data/`. When you finish a new project,
job, or research role, add it:

```
/add-accomplishment
```

The skill interviews you and writes a STAR-format document to
`data/accomplishments/`. It records real metrics only; if a number does not
exist, it says so rather than inventing one. New skills it surfaces are added to
`data/skills.md` with that accomplishment as their evidence.

Two or three accomplishments is enough for a first resume. Six or more is where
tailoring gets genuinely good, because the scoring step starts having real
choices to make.

---

## Your data and privacy

- `data/` **is tracked by git.** That is deliberate: your knowledge base is the
  thing worth versioning and backing up.
- `jobs/` and `outputs/` are **gitignored**, so postings and generated documents
  (which carry your address and phone number on every cover letter) stay on your
  machine.
- If you push this repo anywhere, **your knowledge base goes with it.** Make the
  remote private. Your profile holds your phone number, email, and mailing
  address.
- Sharing your setup with a friend? Send them the upstream template, not your
  own repo.

---

## Where things live

```
data/                 the knowledge base (single source of truth, tracked)
  profile.md          identity, contact, target roles, honors
  education.md        degree, GPA, coursework, licenses
  skills.md           skills, each tied to evidence
  accomplishments/    one STAR-format document per experience

templates/
  resume.tex                  canonical one-page resume layout
  cover_letter.md             per-job letter content, with placeholders
  cover_letter.tex            letterhead, one-page fitter, signature
  RESUME_FORMAT.md            layout spec the resume implements
  COVER_LETTER_FORMAT.md      layout spec the letter implements
  EXAMPLE-accomplishment.md   fictional format reference, never source data
  *.xlsx                      blank workbooks, copied to outputs/ on first use

jobs/<company-role>/  per-posting analysis (gitignored)

outputs/              generated documents (gitignored)
  Application-Tracker.xlsx     living status tracker, one row per application
  accomplishment_scores.xlsx   running relevance scoreboard
  .master-resume/              general-purpose resume + selection.md
  <company-role>/              per job: resume, cover letter, job description,
                               analysis.md, Interview-Prep once prepped
```

---

## Ground rules (enforced by the skills)

- **Accuracy first.** No fabricated accomplishments, metrics, technologies,
  coursework, or awards. Missing detail is omitted, not invented.
- **One page.** Resumes stay to a single page, and the cover letter template
  refuses to produce a PDF at all if the letter runs past one.
- **Natural keywords.** ATS terms are woven in, never stuffed.
- **Writing style.** Concise, active voice, no AI cliches or em dashes. The full
  banned-word list and style rules are in [`CLAUDE.md`](CLAUDE.md).

---

## Making it yours

The default resume layout is a conservative, ATS-safe one-pager. If you already
have a resume you like better, tell `setup` and it will reverse-engineer your
layout into `templates/RESUME_FORMAT.md` and reshape the template to match.
Everything downstream works the same either way.

Changing the layout later is a template edit, not a per-job one. Skills are
instructed never to redesign the template while building an application, which
is what keeps every document you send looking like a set.
