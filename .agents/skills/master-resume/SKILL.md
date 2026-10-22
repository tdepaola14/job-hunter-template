---
name: master-resume
description: Build the general-purpose master resume from the knowledge base, with no job posting attached. Use when the user says "make my master resume", "build a general resume", "career fair resume", "I need a resume with no posting", "update my base resume", or "/master-resume". Selects accomplishments for breadth across the user's target areas instead of fit to one job, then writes <LastName>-Resume.tex/pdf and selection.md to outputs/.master-resume/.
---

# Master Resume

Build one general-purpose resume for situations with no posting to tailor to: a
career fair handout, a recruiter who says "send me your resume", or a portal
with no job attached. Every line still traces to `data/`. Read `CLAUDE.md` fully
first; the writing rules and banned-word list apply exactly as they do to a
tailored resume.

This is `build-application` minus the posting. Format, honesty rules, and the
one-page limit are identical. Only the selection logic changes: breadth instead
of fit.

## Precondition

None. The knowledge base is the only input. There is no `jobs/` folder, no
`keywords.json`, and nothing to score against.

## Step 1: Load the knowledge base

Read `data/profile.md`, `data/education.md`, `data/skills.md`, and every file in
`data/accomplishments/`. `data/` is the only source of truth: if an older resume
or CV in the repo contradicts it, the knowledge base wins. Honor any "to confirm"
note in `data/profile.md` rather than picking a value yourself.

Do not read `jobs/` or any per-job folder in `outputs/`. A master resume built
off the last posting is a tailored resume in disguise.

## Step 2: Select for breadth

There is no posting to score against, so rank on coverage instead:

1. **Cover the target areas.** Read the target roles and industries out of
   `data/profile.md`. Aim for one strong entry per area rather than three from
   the same one. A recruiter should be able to place the user in their industry
   within a few seconds of scanning. If the profile names a single narrow target,
   this criterion matters less and depth matters more.
2. **Named employers and formal titles first.** A real title at a named employer
   ("Manufacturing Intern, Acme Tooling") and a formal academic role
   ("Undergraduate Research Assistant") read as verified experience in a stack of
   resumes. Independent projects support them; they do not replace them.
3. **Depth over count.** One entry with three specific bullets beats two entries
   with one thin bullet each.
4. **Recency.** Prefer ongoing and recent work. An older entry earns its spot by
   showing something no other entry shows.
5. **One page, always.** Typically four to five entries at two or three bullets
   each. Cut the weakest entry before touching the format.

Order Relevant Experience by strength: most substantial and most transferable
first. Work Experience holds employment that shows reliability and working with
people. Education stays fixed. Independent projects use the existing pattern:
descriptive title, `Independent Project` as the organization line, real dates.

Record the reasoning in Step 5, not in a scores workbook.

## Step 3: Write for a general reader

- Start from each accomplishment's "Resume Bullet Candidates" and use them as
  written wherever they work.
- Rewrite only to strip job-specific slant or to make narrow jargon legible. At
  a career fair the first reader is often a recruiter, not a specialist. Lead each
  entry with its most broadly readable bullet and put specialist detail after it.
- Do not weave keywords toward any posting. The right keywords here are the ones
  that are true and common across roles in the field: the software, languages,
  and methods the user actually uses.
- Honesty rules are unchanged. Calculated figures stay labeled as design
  calculations, never measurements.
- The Technical Skills line in the Education block should carry the broadest
  true set, ordered by strength, drawn from `data/skills.md`. Only list a skill
  an accomplishment backs. With no posting to filter it, keep it to tools the user
  could defend in a hallway conversation.

## Step 4: Generate the resume

Create `outputs/.master-resume/` if it does not exist. The leading dot sorts it
away from the per-job folders and marks it as not an application.

Clone `templates/resume.tex` to `outputs/.master-resume/<LastName>-Resume.tex`. The
deliverable is named `<LastName>-Resume` like every other resume in the repo,
where `<LastName>` is the surname in `data/profile.md`. Change only the
experience entries and their order; keep the layout, header, Education block,
and formatting exactly. The contact line is fixed at `phone | email | portfolio`
(the latter two clickable via `hyperref`), whatever the user set during setup.
Never drop a segment of it to buy space; cut a bullet instead.

Compile with the local LaTeX install:

```
latexmk -pdf -interaction=nonstopmode -halt-on-error \
  -outdir=outputs/.master-resume \
  outputs/.master-resume/<LastName>-Resume.tex
```

Or with `pdflatex` directly:

```
pdflatex -interaction=nonstopmode -halt-on-error \
  -output-directory=outputs/.master-resume \
  outputs/.master-resume/<LastName>-Resume.tex
```

If a new terminal cannot find `pdflatex`, the LaTeX distribution's bin directory
is not on PATH. See the PATH notes in the `build-application` skill.

Confirm one page and check the `.log` for Overfull/Underfull boxes. Then delete
`.aux`, `.log`, `.out` build artifacts (`latexmk -c` also clears them).

## Step 5: Write selection.md

Write `outputs/.master-resume/selection.md` so the next rebuild is consistent:

- Build date.
- Accomplishments on the resume, and what each one is there to prove.
- Accomplishments left out, and why (usually covered by a stronger entry, or too
  narrow for a general audience).
- Gaps in the knowledge base worth filling, so `add-accomplishment` has a target.

## Step 6: What this skill does not do

- **No cover letter.** A generic one would break the cover letter philosophy in
  `CLAUDE.md`: a letter should read like it was written for one employer. When
  the user has a specific company, run `analyze-posting` then `build-application`.
- **No Job-Description file.** There is no posting.
- **No row in `outputs/Application-Tracker.xlsx`.** This is not an application.
- **No column in `outputs/accomplishment_scores.xlsx`.** Scores are per posting.
- Skills that iterate over `outputs/<company-role>/` should skip dot-prefixed
  folders, since this one is not a job.

## Step 7: Quality checklist

Verify against `CLAUDE.md`: no fabrication, no repeated bullets or phrases, no
banned words or em dashes, active voice, correct grammar and spelling,
formatting unchanged from the template, and one page. Then read it once as a
recruiter with thirty seconds: is it obvious what the user studies, when they
graduate, what they can build, and how to reach them?

## Keeping it current

Rerun this skill after `add-accomplishment`, when a role's dates or scope
change, or before a career fair. The PDF in `outputs/.master-resume/` should
always be current enough to print and hand out without edits.

## Output

`outputs/.master-resume/` should contain `<LastName>-Resume.tex`,
`<LastName>-Resume.pdf`, and `selection.md`. Nothing else in the repo changes.
