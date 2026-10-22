---
name: setup
description: First-run setup for a fresh clone of this repo. Use when the user says "set up the repo", "I just cloned this", "get me started", "help me fill in my info", or "/setup", and whenever data/ or the templates still contain {{PLACEHOLDERS}}. Interviews the user for identity, contact details, education, and target roles, fills the resume and cover letter templates, checks the LaTeX toolchain, and seeds the knowledge base.
---

# Setup

Turn a fresh clone into this person's own career assistant. Nothing in the repo
is filled in yet: `data/` holds empty scaffolding and both templates carry
`{{PLACEHOLDERS}}`. This skill collects the real details once, writes them
everywhere they belong, and hands off to `add-accomplishment`.

Read `CLAUDE.md` first. The accuracy rule governs this skill too: record what the
user says, never a tidier version of it.

## Before starting

Check whether setup has already run. If `data/profile.md` has no placeholders
left, say so and ask whether the user wants to update specific fields instead of
starting over. Never overwrite a populated knowledge base without a clear yes.

## Step 1: Offer the fast path

Ask whether they already have a resume, CV, or LinkedIn export. If they do, ask
for the file path and read it. It is by far the quickest way to fill everything
below, and it is real data rather than recalled data.

Extract what it gives you, then confirm each item with the user before writing.
A resume is a starting point, not a source of truth: it may be out of date, and
it definitely lacks the STAR detail the accomplishment docs need.

If they have no resume, just run the interview.

## Step 2: Identity and contact

Collect, one small cluster at a time:

- Full name, and the surname to use in filenames. Every document this repo
  produces is named `<LastName>-Resume` and `<LastName>-Cover-Letter`, so
  confirm the exact spelling and capitalization.
- City and state (for the profile).
- Mailing address, for the cover letter letterhead. Tell them this is optional:
  a business letterhead traditionally carries a street address, but city and
  state alone is a normal modern choice. Respect whichever they pick.
- Phone number, formatted as they want it printed.
- Email to put on documents. If they have a school address and a personal one,
  ask which belongs on applications and record both.
- Portfolio or personal site, if any. Ask whether they want it in the contact
  line. If they have no site, ask whether LinkedIn should take that slot or
  whether the contact line should just be phone and email.

## Step 3: Education

- School, degree, and graduation term (month and year, or "Anticipated <term>").
- GPA, if they want it shown. Do not push. Below roughly 3.0 it is conventional
  to leave it off, and it is their call either way.
- Honors and awards, with dates.
- Licenses and certifications, with the date earned.
- Activities, clubs, and memberships.
- Relevant coursework. Ask for course codes and titles as their transcript lists
  them, and grades only if they want coursework grades tracked. This list is what
  `add-accomplishment` later cross-references.

Where two sources disagree (a resume says one GPA, the transcript another),
record both in a "Source Notes / To Confirm" section in `data/profile.md` and
state which one documents should use. Later skills are instructed to honor that
note rather than choose for themselves.

## Step 4: Target roles

- What they are applying for: internship, co-op, new grad, full-time.
- Fields or industries they are targeting, or explicitly that they are keeping
  options open.
- Any hard constraints: work authorization, relocation, remote-only, earliest
  start date, graduation timing that limits which terms they can accept.

`master-resume` reads this section to decide what breadth means for this person,
so record it plainly rather than aspirationally.

## Step 5: Write the knowledge base

Write `data/profile.md` and `data/education.md`, replacing the scaffolding.
Match the structure the scaffolding shows: it documents the sections later
skills expect to find.

Leave `data/skills.md` mostly empty for now. Skills are supposed to be earned:
each one gets added with the accomplishment that proves it. Seed it only with
tools the user has already named and can point to real work behind.

## Step 6: Fill the templates

Replace every `{{PLACEHOLDER}}` in `templates/resume.tex` and
`templates/cover_letter.tex`:

| Placeholder            | Source                                    |
|------------------------|-------------------------------------------|
| `{{FULL-NAME}}`        | Step 2                                    |
| `{{PHONE}}`            | Step 2                                    |
| `{{EMAIL}}`            | Step 2, the application-facing address    |
| `{{PORTFOLIO}}`        | Step 2, bare domain (no `https://`)       |
| `{{STREET-ADDRESS}}`   | Step 2, cover letter only                 |
| `{{CITY-STATE-ZIP}}`   | Step 2, cover letter only                 |
| `{{SCHOOL}}`           | Step 3                                    |
| `{{DEGREE}}`           | Step 3                                    |
| `{{GRADUATION-TERM}}`  | Step 3                                    |
| `{{HONORS-LINE}}`      | Step 3, GPA and awards on one line        |
| `{{CREDENTIALS-LINE}}` | Step 3, licenses and certifications       |
| `{{ACTIVITIES}}`       | Step 3                                    |
| `{{TECHNICAL-SKILLS}}` | Step 5, the tools they can defend         |

Handle the opt-outs properly rather than leaving an empty placeholder:

- **No portfolio:** delete the third contact segment in `resume.tex` *and* the
  ` \quad\textbar\quad ` before it, plus the portfolio line under the signature
  in `cover_letter.tex`.
- **No street address:** delete that line and its trailing `\\` from the
  letterhead.
- **No honors, credentials, or activities:** delete the whole line, including
  its `\\`. Do not print an empty label.

Then delete the two example `\entry` blocks in `resume.tex`. They exist to show
the pattern and are not the user's content. `build-application` writes real
entries per job.

Also strip the `FIRST RUN` comment block from the top of each template once the
placeholders are gone. It has served its purpose.

## Step 7: Adapt the layout, if they want

Ask whether they like the default layout or want their existing resume's look.
If they have their own and prefer it, reverse-engineer it into
`templates/RESUME_FORMAT.md` (page size, margins, fonts, sizes, section order,
entry pattern), then reshape `templates/resume.tex` to match the updated spec.

Keep the mechanics intact while you do it: one page, `\sectionheader` and
`\entry` helpers, `hyperref` links, and the same section names the skills expect
(Education, Relevant Experience, Work Experience). Downstream skills key off
those names.

## Step 8: Check the toolchain

Verify both tools are installed and reachable:

```
pdflatex --version
```

```
pandoc --version
```

Then test-compile the now-filled template:

```
pdflatex -interaction=nonstopmode -halt-on-error templates/resume.tex
```

If `pdflatex` is missing, point them at a distribution for their platform:
MiKTeX on Windows, MacTeX on macOS, TeX Live on Linux. If it is installed but
not found, its bin directory is not on PATH; the `build-application` skill lists
the usual locations. Clean up the `.aux`, `.log`, `.out`, and `.pdf` this test
leaves behind.

## Step 9: Create the output workbooks

`outputs/` is gitignored and does not exist in a fresh clone. Create it, then
copy the two blank workbooks so the tracking skills have something to write to:

- `templates/Application-Tracker.xlsx` to `outputs/Application-Tracker.xlsx`
- `templates/accomplishment_scores.xlsx` to `outputs/accomplishment_scores.xlsx`

Copy them; do not rebuild them. They carry the Status dropdown, the Legend
sheet, and the formatting the other skills are told to preserve.

## Step 10: Seed the knowledge base

The repo cannot write a resume from an empty `data/accomplishments/`. Explain
that plainly, then run `add-accomplishment` for their strongest experience,
right now, in this session. Two or three accomplishments is enough to build a
first real resume; more makes tailoring better.

If Step 1 turned up a resume, walk its entries one at a time. Each existing
bullet is a prompt for the STAR detail the doc actually needs, not a finished
accomplishment doc.

## Step 11: Report

Tell them what is now true and what to do next:

- What was written (`data/profile.md`, `data/education.md`, both templates).
- How many accomplishments are in the knowledge base, and that more is better.
- That `data/` is committed but `outputs/` and `jobs/` are gitignored, so
  generated documents and postings stay local. If they plan to push this repo
  anywhere, their knowledge base goes with it: make the remote private, or keep
  the repo local.
- The next command: `/analyze-posting` with a real posting, then
  `/build-application`. Or `/master-resume` if they just want a general resume
  to hand out.
