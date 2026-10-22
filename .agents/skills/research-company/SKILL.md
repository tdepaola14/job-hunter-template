---
name: research-company
description: Research an employer before writing the cover letter or prepping for an interview. Use when the user says "research this company", "look up <company>", "what does <company> do", or "/research-company", and as an optional step between analyze-posting and build-application. Gathers products, industry, technical focus, technologies, values, and recent news from the web into jobs/<company-role>/company_research.md with sources, so cover letters and interview prep can cite real specifics.
---

# Research Company

Gather verifiable facts about one employer so the cover letter and interview
prep can reference real specifics instead of generic praise. Output lands in
`jobs/<company-role>/company_research.md` and is consumed by
`build-application` and `interview-prep`.

Read `CLAUDE.md` first. The Company Research and accuracy rules apply: no
flattery, no generic statements, no invented facts.

## Precondition

`jobs/<company-role>/` should exist (run `analyze-posting` first). If the user
names a company with no job folder yet, ask whether to analyze the posting
first or research standalone; standalone research goes in the same file once
the folder exists.

## Step 1: Establish what to research

From `keywords.json` and `parsed_posting.md`, note the company name, the
specific division or subsidiary (for example GE Aerospace's Innoveering), and
the site location. Research the entity actually hiring, not just the parent
brand; a division's work often differs from the parent's public image.

## Step 2: Search the web

Use web search and page fetches. Prioritize primary sources: the company's own
site (products, careers, engineering or technical pages), the specific site/division page,
recent press releases, and reputable trade or local news. Look for:

- **Products and services** - what the company actually makes or does.
- **Industry and customers** - who buys it and why it matters.
- **Technical focus** - disciplines, technologies, and tools they talk about.
- **The specific site** - what the location in the posting works on, if known.
- **Company values / culture** - stated values, in their own words.
- **Recent news** - expansions, new products, contracts, awards (with dates).
- **Scale** - size, locations, ownership (public, private, subsidiary).

## Step 3: Write company_research.md

Save to `jobs/<company-role>/company_research.md`:

```
# Company Research - <Company>
Researched: <today's date>
Role context: <role> at <location>

## What they do
## Industry and customers
## Technical focus and technologies
## The <location> site (if known)
## Values and culture (their wording)
## Recent news
## Hooks
## Sources
```

Rules:

- **Cite every fact.** Each section draws on the Sources list (URLs with access
  dates). If a claim cannot be sourced, leave it out.
- **Separate fact from inference.** If you infer something (for example, likely
  tools from a job family), label it "inference" explicitly.
- **Hooks** are the 2-4 most usable specifics: concrete products, projects, or
  site facts that a cover letter could reference naturally, each with a note on
  which of the user's real experiences it connects to. A hook must connect to the
  knowledge base honestly or it does not belong.
- **Date everything.** Research goes stale; the header date lets a later
  session decide whether to refresh.

## Step 4: Report

Summarize the top findings and the hooks. Flag anything that changes the
application's angle (for example, the division does defense work requiring
citizenship, or the site focuses on a different product line than the posting
implies). Do not rewrite any application documents here; `build-application`
and `interview-prep` consume this file.
