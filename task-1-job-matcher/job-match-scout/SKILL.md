---
name: job-match-scout
description: Search LinkedIn, Indeed, Glassdoor and other job boards for roles matching the user's resume, then produce a per-job fit report scoring strong matches, partial matches, and gaps in the resume against each job's stated requirements. Use this skill whenever the user asks to find jobs, look for openings, check what roles they qualify for, match their resume or CV against job descriptions, assess fit for a specific posting, identify skill gaps for a target role, or tailor their resume to a job ad — even if they don't name a specific job board. Also use it when the user pastes a job description and asks "am I a good fit?" or "what am I missing?"
---

# Job Match Scout

Find relevant openings and tell the user the truth about their fit — including the parts that hurt.

The value of this skill is not the list of links. It's the honest requirement-by-requirement comparison against the user's actual resume, so they know which applications are worth their time and what to fix before they hit send.

## What you can and cannot access

Be upfront about this the first time it matters, then move on — don't re-litigate it in every response.

- **LinkedIn, Indeed, Glassdoor** actively block automated fetching (bot detection, login walls, no open API). Their listing pages usually cannot be fetched directly.
- **What works instead:** `web_search` surfaces their public job pages with enough detail (title, company, location, often a requirements snippet) to work with. Search engines index these pages even when direct fetching fails.
- **What fetches cleanly:** company career pages (Greenhouse, Lever, Workable, Ashby, BambooHR, SmartRecruiters), and regional/remote boards like Rozee.pk, Mustakbil, WeWorkRemotely, RemoteOK, Wellfound, Otta, Y Combinator's Work at a Startup, Stack Overflow Jobs successors, and Hacker News "Who is hiring" threads.
- If a fetch fails, don't fake it. Work from the search snippet, mark the listing's requirement detail as `low confidence`, and say so in the report.

Never invent a job, a company, a salary, or a requirement. A short honest list beats a long fabricated one.

## Workflow

### 1. Load the candidate profile

Read `references/candidate-profile.md` — it holds the user's skills, experience, and constraints distilled from their resume.

If the user has uploaded a new or updated resume, re-derive the profile from it and offer to update that file. If neither exists, ask for the resume before searching. Searching without a profile produces generic results and wastes the user's time.

### 2. Confirm the search parameters

Only ask about what you genuinely don't know. Check the profile and the conversation first. The things that actually change the result set:

- **Location / work model** — onsite in a specific city, remote-local, remote-international, or open to relocation and visa sponsorship. This is usually the single biggest filter.
- **Seniority band** — the profile's years of experience implies this; confirm rather than ask open-ended.
- **Role focus** — e.g. backend vs. full-stack vs. desktop/systems, and which languages the user wants to keep working in versus move away from.

Use the elicitation tool with tappable options rather than a wall of prose questions.

### 3. Search broadly, then narrow

Run searches per source and per role variant — a single combined query returns shallow results for everything. A reasonable spread is 8–15 searches. Vary the phrasing across:

```
site:linkedin.com/jobs backend engineer Java <location>
site:indeed.com <role> <location> <year>
site:glassdoor.com <role> jobs <location>
<role> jobs <location> remote <year>
"<specific technology>" developer hiring <location>
```

Include the current year in queries so stale postings sort lower. Then `web_fetch` any listing that looks promising and belongs to a fetchable domain, to get the full requirements list — snippet-level matching is guesswork.

Discard: postings older than ~60 days where a date is visible, duplicates across boards (keep the source with the most requirement detail), and roles clearly outside the confirmed parameters. Aim to present 5–10 genuinely plausible roles rather than 30 marginal ones.

### 4. Match each role against the profile

For every job, extract its stated requirements — must-haves, nice-to-haves, years of experience, domain, education, location/visa terms — and classify each one:

| Verdict | Meaning |
|---|---|
| **Strong** | Direct, evidenced experience in the resume. Point to the specific bullet or project that proves it. |
| **Partial** | Adjacent or transferable, but not a literal match — e.g. the job wants Spring Boot and the resume has Django REST plus deep Java. Say explicitly what the transfer argument is, since the user will need to make that argument in a cover letter. |
| **Gap** | Not present in the resume. Note whether it's a hard blocker (visa, a required degree, a licence, 8 years for a 3-year candidate) or a soft one that could be closed with a weekend project or a rewritten bullet. |

Weight requirements by whether the posting lists them as required or preferred. Ten satisfied nice-to-haves don't compensate for a missed must-have, and a report that implies otherwise is misleading.

Then assign an overall fit band, and be willing to use the low ones:
- **Strong fit (apply now)** — all must-haves met or near-met.
- **Stretch (worth applying)** — one or two must-haves partial; the transferable case is arguable.
- **Reach (only if highly motivated)** — a real must-have gap, or a large seniority jump.
- **Not a fit** — hard blocker. Say so plainly and briefly rather than padding it into a maybe.

A calibrated report is more useful than an encouraging one. If everything comes back "strong fit," the scoring is broken.

### 5. Write the report

Follow `references/report-template.md` exactly. Deliver as a markdown file in `/mnt/user-data/outputs/` and present it, since the user will want to keep it while applying.

After the per-job sections, always include the cross-cutting analysis — the patterns across all roles found are usually more valuable than any single listing:

- **Recurring gaps** — a technology demanded by most postings that the resume never mentions is the highest-value thing the user can fix. Rank these by how often they appeared.
- **Underused strengths** — real experience buried in the resume that these postings explicitly ask for. This is the cheapest fix: rewording, not learning.
- **Resume fixes** — concrete, per-bullet. "Add a bullet quantifying the REST API's throughput" beats "add more metrics." If the resume names a specific target employer in its summary (common in tailored resumes), flag that it must be changed per application.

## Handling a single pasted job description

If the user pastes one job ad and asks about fit, skip searching entirely. Go straight to step 4, and use the single-job section of the template. Include a short "how to position yourself" note covering which resume bullets to lead with and how to phrase the partial matches.

## Tone

Warm but not flattering. The user is making decisions about where to spend limited effort, and a report that oversells fit costs them interviews they won't get and hides fixes they could make in an afternoon. State gaps directly, then say what closes them.
