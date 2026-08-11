# Resume Format Spec

The layout implemented by `templates/resume.tex`. This is a clean, conservative,
ATS-safe one-page layout that works for most students and early-career
applicants. Replicate it; do not redesign per job.

**Already have a resume you like?** Tell the `setup` skill. It can reshape this
spec and the template to match your existing layout, so the generated documents
look like yours rather than like this default. Everything downstream works the
same either way.

## Page

- US Letter, 8.5 x 11 in.
- Margins: 0.5 in on all four sides.
- One page.

## Typography

- Font: Arial throughout (LaTeX uses Helvetica via `helvet`, the closest
  metric match; a system Arial via `fontspec` also works under XeLaTeX).
- Base body text: 11 pt.
- Name: 18 pt, bold, centered.
- Contact line: 11 pt, centered. Email and portfolio are live blue hyperlinks;
  the phone number stays plain black text.
- Section headers: 12 pt, bold, with a bottom border rule spanning the page
  width.
- Entry title lines: 11 pt, bold.
- Entry sub-line (org/department + date): 11 pt, regular.
- Bullets: 11 pt, regular, standard round bullet.

## Layout Structure (top to bottom)

1. **Name** - bold, centered, 18 pt.
2. **Contact line** - centered, 11 pt. Format: `phone  |  email  |  portfolio`.
   Email and portfolio are clickable (`\href`, blue via `hyperref`), so the link
   text stays readable as a bare domain for ATS parsing while the annotation
   points at the full `https://` URL.
   - No portfolio site? Drop that segment and its separator, or put LinkedIn
     there instead. Pick one and keep it on every resume you send.
   - If you do keep a portfolio link, treat it as fixed: never trade it for
     space when an entry runs long. Cut a bullet instead.
3. **Section: Education** - bold 12 pt header with bottom rule.
4. **Section: Relevant Experience** - bold 12 pt header with bottom rule.
5. **Section: Work Experience** - bold 12 pt header with bottom rule.

## Entry Pattern (Experience / Work)

- Line 1 (bold): `Position Title - Organization or Lab`
- Line 2 (regular): organization/department on the left, dates right-aligned to
  the margin via a right tab stop. Example layout:
  `School of Engineering and Applied Sciences            Spring 2026 - Present`
- Following lines: bulleted achievements, 11 pt.

Independent and personal projects use the same pattern, with a descriptive title
and `Independent Project` as the organization line.

## Education Block

- Line 1 (bold): your school, with the graduation term right-aligned via a tab
  stop.
- Line 2: your degree.
- Line 3: honors line (GPA, Dean's List, awards). Drop any you do not have. GPA
  is conventionally omitted below about 3.0.
- Line: licenses or certifications, with the date earned. Drop if none.
- Line: `Activities: ...`. Drop if none.
- Line: `Technical Skills: ...`.

Only the school, degree, and graduation term are mandatory. Delete the rest of
the lines rather than padding them.

## Notes

- Section order is Education, Relevant Experience, Work Experience. Per the
  resume philosophy in `CLAUDE.md`, entry selection and ordering *within* the
  experience sections is chosen per job posting; the visual format stays fixed.
- Once you have a few years of full-time work behind you, moving Education below
  the experience sections is the normal convention. Until then, it leads.
- Avoid em dashes in generated content; use commas or hyphens. This matches the
  project writing rules.
