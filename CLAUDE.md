# CLAUDE.md

# AI Resume & Cover Letter Generator

## Purpose

This repository is a personal career assistant that generates high-quality,
ATS-friendly resumes and cover letters tailored to specific job postings.

The repository is **not** intended to invent accomplishments or fabricate
experience. Every statement included in a generated document must originate from
the knowledge base stored within this repository.

The primary objective is to maximize relevance to a specific job posting while
producing writing that sounds natural and human.

---

# Who This Is For

Read `data/profile.md` before doing anything else. It states who the user is,
what they are applying for, and any constraints on the search. This file makes
no assumptions about the user's field, degree, or career stage; the profile is
the authority on all of it.

If `data/profile.md` still contains `{{PLACEHOLDERS}}`, the repo has not been
set up yet. Run the `setup` skill before anything else.

---

# How This Works (Tooling & Workflow)

The workflow is implemented as **Claude Code skills**, not standalone automation
scripts. Each repeatable step is a skill invoked per job (for example, a skill to
add an accomplishment, a skill to analyze a posting, and a skill to assemble an
application).

* Invocable skills live in `.claude/skills/<name>/SKILL.md` so Claude Code can
  discover and run them.
* The repo's top-level `skills/` directory holds supporting reference material
  for those skills, not the skills themselves.

**Knowledge base from real data.** The knowledge base is built from the user's
actual history, never invented. If the user has an existing resume, that is the
starting source of truth; the assistant interviews them to fill gaps.

**PDF generation via LaTeX.** Resumes compile from `.tex` with
`pdflatex`/`latexmk`. Cover letters are authored in markdown, expanded to `.tex`
by pandoc using `templates/cover_letter.tex`, then compiled, so both documents
ship editable LaTeX beside their PDF and share one visual identity.

**Template fidelity.** `templates/resume.tex` defines the layout. Match its
layout, fonts, spacing, and section order exactly. Do not redesign the template
per job. If the user wants a different look, change the template once (via
`setup`) rather than varying it per application.

---

# Core Principles

## Accuracy Above Everything

Never fabricate:

* accomplishments
* metrics
* technologies
* coursework
* awards

If supporting information cannot be found in the knowledge base, omit it rather
than inventing it.

---

## Personal Writing Style

The writing should sound like the user: an ambitious applicant at the career
stage `data/profile.md` describes.

The tone should be:

* professional
* concise
* confident
* technically accurate
* conversational without becoming informal

Avoid sounding like marketing copy or an annoying young intern.

Avoid unnecessary adjectives.

Avoid exaggerated claims.

---

## AI Writing Rules

Never use:

* em dashes
* "passionate"
* "results-driven"
* "dynamic"
* "cutting-edge"
* "leveraged"
* "utilized"
* "synergy"
* "excited to apply"
* "delve"
* "fast-paced environment"
* "go-getter"
* "hardworking individual"

Prefer words such as:

* built
* designed
* created
* developed
* tested
* implemented
* analyzed
* optimized
* manufactured
* programmed
* modeled
* validated
* improved

Sentences should generally remain between 10 and 18 words.

Avoid repetitive sentence structure.

Avoid repeated verbs whenever possible.

---

# Repository Structure

```
.claude/
    skills/                  invocable Claude Code skills (the workflow)

data/                        the knowledge base (tracked in git)
    profile.md
    education.md
    skills.md
    accomplishments/

templates/
    resume.tex                   canonical resume layout
    cover_letter.md              per-job content, with placeholders
    cover_letter.tex             pandoc template: letterhead, fitter, signature
    RESUME_FORMAT.md             layout spec the resume template implements
    COVER_LETTER_FORMAT.md       layout spec the letter template implements
    pandoc-header.tex            shared font header for plain markdown renders
    EXAMPLE-accomplishment.md    fictional format reference, never source data
    Application-Tracker.xlsx     blank tracker, copied to outputs/ on first use
    accomplishment_scores.xlsx   blank scoreboard, copied to outputs/ on first use

jobs/                        gitignored
    <company-role>/          one subfolder per posting, mirrors outputs/
        posting.pdf
        parsed_posting.md
        keywords.json
        company_research.md  optional, written by the research-company skill

outputs/                     gitignored
    accomplishment_scores.xlsx   loose file: relevance scoreboard
    Application-Tracker.xlsx     loose file: living status tracker, one row per application
    .master-resume/              general-purpose resume, no posting attached; written by
                                 the master-resume skill. Not a job: skills that iterate
                                 over <company-role>/ folders skip dot-prefixed ones.
    <company-role>/              generated documents per job (plus Interview-Prep.md/pdf
                                 once the interview-prep skill runs)

skills/                      supporting reference material for the skills
                             (create as needed; not present in a fresh clone)

scripts/                     optional helpers only; not the primary workflow
                             (create as needed; not present in a fresh clone)
```

`jobs/` and `outputs/` are gitignored, so postings and generated documents stay
local. `data/` is tracked, because the knowledge base is the thing worth keeping
and versioning. If the user pushes this repo to a remote, their knowledge base
goes with it: that remote should be private.

Every directory has a specific responsibility.

---

# Knowledge Base

The repository contains the user's complete career history.

Every accomplishment should exist as an independent document in
`data/accomplishments/`, named with a kebab-case slug.

Each accomplishment contains:

* overview
* technologies
* skills
* STAR breakdown
* resume bullet candidates
* supporting details
* measurable outcomes
* related coursework

`templates/EXAMPLE-accomplishment.md` shows the exact shape. It is fictional and
lives outside `data/` so that it can never be read as real experience.

This knowledge base is the single source of truth. Where the user's own
documents disagree with each other, `data/profile.md` carries a "Source Notes /
To Confirm" section; honor what it says rather than choosing a value yourself.

---

# Resume Philosophy

The goal is **not** to rewrite an existing resume.

Instead:

1. Understand the job posting.
2. Identify the required skills.
3. Rank accomplishments by relevance.
4. Assemble the strongest combination of experiences.
5. Rewrite bullets only when necessary.
6. Produce a one-page resume.

---

# Resume Bullet Rules

Each bullet should:

* begin with a strong action verb
* emphasize impact
* include technical details
* remain concise
* avoid unnecessary filler

When metrics exist:

Good:

> Reduced processing time by 35% through...

When metrics do not exist:

Do not invent numbers.

Instead describe the technical accomplishment, using action words and STAR
format where appropriate.

---

# Job Posting Analysis

Every job posting should be analyzed before writing.

Extract:

* required skills
* preferred skills
* software
* programming languages
* disciplines
* certifications
* recurring keywords
* soft skills
* action verbs

Store this information as structured JSON.

---

# Accomplishment Selection

Each accomplishment should receive a relevance score.

Example criteria:

* required skills matched
* preferred skills matched
* software overlap
* discipline overlap
* leadership relevance

Store the scores in `outputs/accomplishment_scores.xlsx`. The only loose files in
`outputs/` are that scores workbook and `Application-Tracker.xlsx`; all other
outputted documents should be organized into subfolders under `outputs/` by
job/company.

---

# Application Tracking

`outputs/Application-Tracker.xlsx` is the living record of every application:
one row per job with status, dates, ATS estimate, next step, and notes.

* `build-application` adds (or updates) the job's row with status `Built`.
* `interview-prep` sets the status to `Interview Scheduled` when an interview is
  booked.
* When the user reports a change ("submitted the Acme app", "Acme rejected me"),
  update that row's status, date, and notes.

Edit only the affected row's cells with openpyxl; preserve the sheet's
formatting, the Status dropdown, and the Legend sheet. Valid statuses are listed
on the Legend sheet.

---

# ATS Optimization

Resumes should naturally incorporate important keywords.

Never keyword-stuff.

Never repeat the same keyword unnaturally.

Keyword density should always feel organic.

---

# Cover Letter Philosophy

Cover letters should never repeat the resume.

Instead they should explain:

* why this role
* why this company
* why these experiences matter

The letter should read like it was written specifically for one employer.

---

# Formatting

Never modify template formatting unless requested.

Preserve:

* margins
* fonts
* spacing
* section order
* heading style

Only populate content.

---

# Company Research

When available:

* identify company products
* identify industry
* identify technologies
* identify technical focus
* identify company values

Incorporate this knowledge naturally.

Avoid flattery.

Avoid generic statements.

---

# Quality Checklist

Before finalizing any document, verify:

* no fabricated information
* no repeated bullet points
* no repeated phrases
* no AI clichés
* no passive voice where active voice is better
* grammar is correct
* spelling is correct
* ATS keywords are naturally incorporated
* formatting matches template
* document remains one page unless instructed otherwise

---

# Output Requirements

For every generated application produce, where `<LastName>` is the surname
recorded in `data/profile.md`:

```
<LastName>-Resume.tex

<LastName>-Resume.pdf

<LastName>-Cover-Letter.md

<LastName>-Cover-Letter.tex

<LastName>-Cover-Letter.pdf

Job-Description.md

Job-Description.pdf

analysis.md
```

The resume and cover letter are always named after the applicant, regardless of
the job, since those are the files submitted to employers.

The cover letter keeps all three forms: the markdown is what the skill writes,
the `.tex` is the expanded LaTeX source (pandoc plus `templates/cover_letter.tex`),
and the PDF is what gets submitted. Both documents therefore ship editable
LaTeX next to their PDF.

`Job-Description.md` / `.pdf` is a clean, human-readable copy of the posting,
quoting the employer directly, placed alongside the application so the posting
can be reviewed next to what was submitted (useful for interview prep). Never
paraphrase requirements into it or invent content.

These land in `outputs/<company-role>/`. Produce the PDFs by compiling with
LaTeX: the resume via `pdflatex`/`latexmk`; the cover letter expanded from
markdown to `.tex` by pandoc with `templates/cover_letter.tex`, then compiled by
`pdflatex`; and the job description rendered from markdown straight to PDF by
pandoc, so all three match the resume styling. After compiling, clean up LaTeX
build artifacts (`.aux`, `.log`, `.out`).

The analysis file should summarize:

* extracted keywords
* selected accomplishments
* omitted accomplishments
* missing skills
* ATS match estimate
* suggested improvements

---

# Decision Priority

When making decisions, prioritize:

1. Truthfulness
2. Relevance to the job posting
3. Technical accuracy
4. Readability
5. ATS optimization
6. Writing style

Never sacrifice accuracy for optimization.

---

# General Behavior

Think before writing.

Use the knowledge base extensively.

Reuse verified information whenever possible.

Do not guess.

Do not embellish.

Do not invent.

Produce documents that a hiring manager would believe were written by an
experienced applicant rather than an AI.
