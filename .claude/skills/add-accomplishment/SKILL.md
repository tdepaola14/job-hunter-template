---
name: add-accomplishment
description: Add a new accomplishment to the knowledge base by interviewing the user. Use when the user says "add an accomplishment", "I have a new project/job to add", "log this experience", "remember this for my resume", or "/add-accomplishment". Captures one experience as a STAR-format document in data/accomplishments/ using only real information, never invented metrics or technologies.
---

# Add Accomplishment

Capture one real experience as a structured knowledge-base document so it can be
scored and assembled into future resumes. Read `CLAUDE.md` first. The single
hard rule: nothing is invented. If a detail does not exist, omit it.

## Step 1: Identify the accomplishment

Ask what the experience is: job, internship, research role, class project, or
personal build. Pick a short kebab-case slug for
`data/accomplishments/<slug>.md` (for example `mars_rover_suspension`).

## Step 2: Interview

Work through these, one cluster at a time. Accept "none" or "not measured" as
valid answers and record them honestly.

- **Overview:** what it was, where, when, your role.
- **Technologies:** tools, software, languages, hardware actually used.
- **Skills:** what the work demonstrates.
- **STAR:** Situation, Task, Action (what *you* did), Result.
- **Resume bullet candidates:** 3-5 strong-verb bullets drawn only from the
  above. Follow the bullet rules in `CLAUDE.md`.
- **Supporting details:** context that helps later tailoring.
- **Measurable outcomes:** real numbers only. If none exist, say so plainly.
- **Related coursework:** matching courses from `data/education.md`.

Probe for specifics, but never supply a number, tool, or claim the user did not
state.

## Step 3: Write the document

Write `data/accomplishments/<slug>.md` matching the existing files' structure. If
this is the first accomplishment and there are no existing files to match, follow
`templates/EXAMPLE-accomplishment.md`, which is a fictional file kept outside
`data/` purely as a format reference:

```markdown
# <Title> - <Role>

## Overview

## Technologies

## Skills

## STAR Breakdown

- **Situation:**
- **Task:**
- **Action:**
- **Result:**

## Resume Bullet Candidates

## Measurable Outcomes

## Related Coursework
```

Match the voice of the existing accomplishment docs: concise, active, technically
accurate, no banned words, sentences roughly 10-18 words.

## Step 4: Reconcile the rest of the KB

- If the work demonstrates a skill not yet in `data/skills.md`, add it with this
  accomplishment as its evidence.
- If it includes a new honor, license, or award, update `data/profile.md`
  (and `data/education.md` if academic).
- Do not duplicate an existing accomplishment; extend the existing file instead.

## Step 5: Confirm

Show the user the new file and the exact bullet candidates. Confirm every detail
is accurate before considering it part of the source of truth.
