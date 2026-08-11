---
name: interview-prep
description: Prepare for an interview for a job that has a built application. Use when the user says "I got an interview", "prep me for the <company> interview", "interview questions for this job", or "/interview-prep". Reads the job's posting, analysis, company research, and the STAR breakdowns of the selected accomplishments, then writes Interview-Prep.md/pdf to outputs/<company-role>/ with likely questions, honest answers mapped to real experiences, and questions to ask. Also updates the application tracker.
---

# Interview Prep

Turn an application's existing materials into a practical interview prep
document. Everything in it must trace to `data/` or to sourced company
research; the goal is to help the user tell true stories well, never to coach
claims the knowledge base cannot back.

Read `CLAUDE.md` first. Accuracy and writing-style rules apply.

## Precondition

`outputs/<company-role>/` must exist with `Job-Description.md` and
`analysis.md`. If not, run `analyze-posting` and `build-application` first.

## Step 1: Load the materials

Read, for the job:

- `outputs/<company-role>/Job-Description.md` - the employer's own wording.
- `outputs/<company-role>/analysis.md` - selected accomplishments, omitted
  ones, and the honest gaps.
- `outputs/<company-role>/<LastName>-Resume.tex` and `<LastName>-Cover-Letter.md` -
  what the interviewer has in front of them. Never contradict these.
- `jobs/<company-role>/keywords.json` and `parsed_posting.md`.
- `jobs/<company-role>/company_research.md` if present. If missing, offer to
  run `research-company` first; company questions are much stronger with it.
- The full `data/accomplishments/` doc for every accomplishment on the resume,
  especially the STAR breakdowns and supporting details.

## Step 2: Write Interview-Prep.md

Save to `outputs/<company-role>/Interview-Prep.md`:

```
# Interview Prep - <Company> <Role>
Prepared: <today's date>

## The role in one paragraph
## Your positioning
## Behavioral questions
## Technical questions
## Gap questions
## Questions to ask them
## Logistics and flags
```

Section guidance:

- **The role in one paragraph** - what the job is, in plain language, from the
  posting.
- **Your positioning** - the 2-3 sentence true story of why the user fits this
  role, built from the analysis's selection rationale. This is the answer
  skeleton for "tell me about yourself" and "why this role".
- **Behavioral questions** - likely questions derived from the posting's soft
  skills and responsibilities ("tell me about a time you..."). For each, name
  the accomplishment to answer with and sketch the STAR answer from that doc's
  actual Situation/Task/Action/Result. One story per question; do not reuse the
  same accomplishment for every answer.
- **Technical questions** - plausible questions from the posting's required
  skills, software, and disciplines. For each, point to the real experience
  that supports the answer, including honest depth ("used daily for a full
  internship" vs "one class project"). Where the knowledge base has real numbers or design
  details, surface them; interviewers probe for specifics.
- **Gap questions** - take the "missing skills" straight from `analysis.md` and
  draft an honest response to each: acknowledge the gap, name the closest real
  experience, and state a genuine willingness or plan to learn. Never script a
  claim of experience that does not exist.
- **Questions to ask them** - 4-6 questions, the more specific the better.
  Draw from company research hooks and the posting itself; a question that
  proves the user read about the actual site or product beats a generic one.
- **Logistics and flags** - interview format if known, and any open issues from
  the analysis or tracker (for example an unresolved eligibility question) that
  the user should be ready to address.

## Step 3: Render the PDF

```
pandoc outputs/<company-role>/Interview-Prep.md \
  -o outputs/<company-role>/Interview-Prep.pdf \
  -V geometry=margin=0.75in -V fontsize=11pt \
  --include-in-header=templates/pandoc-header.tex
```

## Step 4: Update the tracker

In `outputs/Application-Tracker.xlsx`, set the job's row Status to "Interview
Scheduled" and record the Interview Date if the user gave one. Use openpyxl;
edit only that row's cells, preserving all formatting and the Status dropdown.
If the user is prepping speculatively (no interview booked), leave the tracker
unchanged.

## Step 5: Report

Give the positioning paragraph, the three most likely questions, and the one
gap question that needs the most practice. Offer a mock-interview round in
chat if the user wants to rehearse answers out loud.
