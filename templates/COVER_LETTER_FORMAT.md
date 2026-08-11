# Cover Letter Format Spec

The layout implemented by `templates/cover_letter.tex`. Companion to
`RESUME_FORMAT.md`. The letter is deliberately a close relative of the resume,
not a copy: same font and rule treatment, letter margins and a left business
letterhead. Do not redesign; content changes only.

## Page

- US Letter, 8.5 x 11 in.
- Margins: 1 in on all four sides. A letter is not the 0.5 in resume.
- One page, guaranteed by the fitter (below).

## Typography

- Font: Helvetica throughout (`helvet`, the scalable Arial match the resume
  uses).
- Base body text: 11 pt.
- Name: 14 pt, bold, left-aligned.
- Letterhead address and contact lines: 11 pt, regular.
- Rule below the letterhead: 0.6 pt, full text width. Same weight as the
  resume's section rules, which is what ties the two documents together.
- Email: blue, a live `mailto:` hyperlink.
- Body: left-aligned block paragraphs, no first-line indent, blank line between
  paragraphs. No indentation anywhere.

## Layout Structure (top to bottom)

1. **Letterhead** - name (14 pt bold), street address, phone and email.
2. **Rule** - full width.
3. **Date**.
4. **Recipient block** - recipient on one line, company on the next.
5. **Salutation** - `Dear <recipient>,`.
6. **Body** - three to four paragraphs.
7. **Closing** - `Sincerely,` then vertical space, then your name, then the
   portfolio link on the line below it as a blue `https://` hyperlink. The
   portfolio link mirrors the resume's contact line, so it is drawn by the
   template rather than written per letter. Suppress it on a single letter with
   `hide-portfolio: true`; delete it from the template if you have no site.

Items 1, 2, 7 and the signature are drawn by the template and are identical on
every letter. Items 3 through 5 come from the markdown YAML block. Only item 6
is written per job.

Your street address is in the letterhead because that is the traditional
business-letter form. Dropping it for city and state alone is a normal choice
and costs nothing.

## Source File Fields

The per-job markdown file opens with a YAML block:

```yaml
---
date: August 4, 2026
recipient: Hiring Manager
company: Example Corp.
---
```

| Field            | Required | Default            |
|------------------|----------|--------------------|
| `date`           | yes      | -                  |
| `recipient`      | yes      | `Hiring Manager`   |
| `company`        | yes      | -                  |
| `salutation`     | no       | `Dear <recipient>,`|
| `closing`        | no       | `Sincerely,`       |
| `hide-portfolio` | no       | unset (link shown) |

Set `salutation` only when the default is wrong. Everything below the YAML block
is body prose: paragraphs separated by blank lines, no manual line breaks, no
letterhead, no signature.

While the template still carries its `{{PLACEHOLDERS}}`, quote them in YAML
(`date: "{{DATE}}"`). Unquoted, a brace opens a YAML flow mapping and pandoc
rejects the file with `Non-string keys are not supported`.

## Build

Two steps, so every letter leaves an editable `.tex` beside its PDF exactly like
the resume:

```
pandoc <LastName>-Cover-Letter.md -o <LastName>-Cover-Letter.tex \
  --template=templates/cover_letter.tex
pdflatex <LastName>-Cover-Letter.tex
```

The result is byte-identical to rendering straight to PDF in a single pandoc
call. The `.tex` is the file to hand-edit when one letter needs something the
markdown cannot express.

## One-Page Fitter

The template typesets the whole letter into a box, measures it against the page,
and rebuilds it one step smaller if it does not fit:

| Preset      | Size    | Leading | Paragraph gap |
|-------------|---------|---------|---------------|
| comfortable | 11 pt   | 13.6 pt | 11 pt         |
| tight       | 11 pt   | 13.0 pt | 8 pt          |
| compact     | 10.5 pt | 12.4 pt | 6 pt          |
| minimum     | 10 pt   | 11.8 pt | 5 pt          |

A three or four paragraph letter, roughly 350 to 480 words, stays on the
comfortable preset and never triggers any of this. Around 500 words the letter
starts stepping down. Past roughly 600 words it exhausts the ladder, and the
build raises a hard LaTeX error so **no PDF is written at all**. That is the
only reliable way to prevent a two-page cover letter from being submitted by
accident: shorten the prose instead of reaching for smaller type.

In the two-step build the failure lands on `pdflatex`, not pandoc. Pandoc does
no typesetting, so it still writes the `.tex` happily. A `.tex` with no `.pdf`
beside it means the letter is too long.

## Notes

- The fitter stepping down is a warning sign about length, not a feature to
  lean on. Write to three or four paragraphs.
- The template is a pandoc template, so `$` starts a field substitution even
  inside a LaTeX `%` comment. Never write a bare dollar-delimited field name in
  its prose; use `$$` for a literal dollar sign.
- Passing `-V geometry`, `-V fontsize`, `-V colorlinks`, or
  `--include-in-header` alongside `--template` does nothing. The template sets
  all of it.
- Never redirect a compile log to `<name>.out`. `hyperref` writes its own `.out`
  file under that name and reads yours back as bookmark data, which fails with a
  confusing `Missing { inserted` error.
