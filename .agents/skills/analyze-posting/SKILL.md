---
name: analyze-posting
description: Analyze a job or internship posting before writing any application. Use whenever the user shares a posting (PDF path, URL, or pasted text) or says things like "analyze this posting", "parse this job", "what does this role want", or "/analyze-posting". Extracts required and preferred skills, software, languages, disciplines, certifications, soft skills, action verbs, and recurring keywords into structured JSON under jobs/<company-role>/. This is the first step before build-application.
---

# Analyze Posting

Turn a raw job posting into structured, reusable analysis. Output lands in
`jobs/<company-role>/` and feeds `build-application`. Do not write resume or
cover letter content here; this step is pure analysis.

Read `CLAUDE.md` first. Accuracy and honesty rules apply to every later step
that consumes this output.

## Step 1: Get the posting

Accept any of:

- A PDF path (the user points to a saved posting).
- A URL (fetch the page text).
- Pasted text.

If none was given, ask for one rather than guessing.

## Step 2: Choose the slug

Form a kebab-case `<company-role>` slug, for example `boeing-mechanical-intern`
or `tesla-manufacturing-coop`. Keep it short and stable; both `jobs/` and
`outputs/` will use this exact slug.

## Step 3: Create the job folder

Create `jobs/<company-role>/`. If the user gave a PDF, copy it in as
`posting.pdf`. If the source was a URL, record the URL in `parsed_posting.md`.

## Step 4: Write parsed_posting.md

Save a clean, readable copy of the posting to
`jobs/<company-role>/parsed_posting.md`. Start with a short header block:

```
# <Role> - <Company>
Location: ...
Employment type: internship | co-op | ...
Source: <url or "PDF provided">
Date analyzed: <today>
```

Then the cleaned full text of the posting (strip navigation, ads, boilerplate).

## Step 5: Extract keywords.json

Write `jobs/<company-role>/keywords.json` with this schema. Separate required
from preferred. Use the posting's own wording for keywords so later matching is
honest.

```json
{
  "company": "",
  "role": "",
  "location": "",
  "employment_type": "",
  "source": "",
  "required_skills": [],
  "preferred_skills": [],
  "software": [],
  "programming_languages": [],
  "disciplines": [],
  "certifications": [],
  "soft_skills": [],
  "action_verbs": [],
  "recurring_keywords": []
}
```

Guidance:

- "Required" = listed under requirements/qualifications or stated as must-have.
- "Preferred" = nice-to-have, "a plus", "preferred".
- `recurring_keywords` = terms that appear several times; they signal what the
  team values and should surface naturally in the resume later.

## Step 6: Report

Summarize for the user: company, role, the top required skills, and any
notable required software or certifications. Note gaps you already see against
the knowledge base so `build-application` can address them. Do not invent fit.
