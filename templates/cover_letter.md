<!--
templates/cover_letter.md

Canonical cover letter source. This file holds only the per-job content: the
letterhead, rule, signature block, spacing, and one-page fitting all live in
templates/cover_letter.tex.

How it is used:
  - The build-application skill copies this file into
    outputs/<company-role>/<LastName>-Cover-Letter.md and fills the
    {{PLACEHOLDERS}}, including the YAML block at the top. <LastName> comes from
    data/profile.md and is the same on every application you send.
  - Render it in two steps, so the letter leaves an editable .tex beside its PDF
    exactly like the resume does:
        pandoc <LastName>-Cover-Letter.md -o <LastName>-Cover-Letter.tex \
          --template=templates/cover_letter.tex
        pdflatex <LastName>-Cover-Letter.tex
    No -V geometry / fontsize / colorlinks flags. The template sets all of it,
    and passing them does nothing.
  - Strip these HTML comments before rendering.

Note on the YAML placeholders below: they are quoted. Unquoted, {{DATE}} is
invalid YAML (a brace is a flow mapping), and pandoc refuses the file with
"Non-string keys are not supported". Keep the quotes until you replace them.

Formatting rules:
  - Everything above the salutation comes from the YAML block. Do not retype the
    letterhead, the date, or the recipient into the body.
  - The body is prose paragraphs only, separated by blank lines. No manual line
    breaks, no signature block: the template adds "Sincerely," and your name.
  - Three to four short body paragraphs. The template guarantees one page, but
    it does so by tightening spacing, so a letter that runs long still looks
    cramped. Write to length rather than relying on the fitter.

Writing rules (from CLAUDE.md, enforced here):
  - Never repeat resume bullets. Explain why this role, why this company, why
    these specific experiences matter.
  - No fabrication: every claim traces to data/ (profile, accomplishments).
  - Banned words: em dashes, passionate, results-driven, dynamic, cutting-edge,
    leveraged, utilized, synergy, "excited to apply", delve, fast-paced
    environment, go-getter, hardworking individual.
  - Sentences ~10-18 words. Active voice. Vary sentence openings and verbs.

YAML fields:
  date        e.g. June 29, 2026
  recipient   named person if known, else "Hiring Manager"
  company     company name as it should appear in the address block
  salutation  optional; defaults to "Dear <recipient>,"
  closing     optional; defaults to "Sincerely,"

Body placeholders to fill per job:
  {{ROLE}}         the posting's job title
  {{WHY_ROLE}}     1-2 sentences: what about this role fits your target
  {{WHY_COMPANY}}  1-2 sentences: specific product/industry/value, no flattery
  {{EVIDENCE_1}}   paragraph tying ONE accomplishment to a posting requirement
  {{EVIDENCE_2}}   paragraph tying a SECOND accomplishment to another requirement
  {{CLOSING_FIT}}  1 sentence: the through-line connecting the above to the role
-->
---
date: "{{DATE}}"
recipient: "{{HIRING_MANAGER}}"
company: "{{COMPANY}}"
---

I am applying for the {{ROLE}} position at {{COMPANY}}. {{WHY_ROLE}} {{WHY_COMPANY}}

{{EVIDENCE_1}}

{{EVIDENCE_2}}

{{CLOSING_FIT}} I would welcome the chance to discuss how my background fits your team. Thank you for your time and consideration.
