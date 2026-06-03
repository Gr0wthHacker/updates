# updates

Public log of shipped work. High-level synthesis of things Jack Gierlich built,
launched, learned, or wrapped — surfaced on [jackgierlich.com](https://jackgierlich.com).

Source code stays private. The synthesis is public.

## Why this exists

Most things I ship are private repos (client work, internal tools, advisory).
A few become posts or papers. Most don't. This repo is a single source of truth
for the in-between: a paragraph that says "yes, this happened, here's the
flavor" without exposing the code.

Built so the public site can fetch a clean feed at build time without me
hand-editing JSON every Friday.

## Format

One markdown file per update at `updates/<YYYY-MM-DD>-<slug>.md`.
Frontmatter is the structured payload; the body is optional commentary.

```yaml
---
title: "Shipped a marketing AI system for lifecycle ops"
date: 2026-06-02
category: system
venture: leal-health
tags: [ai, marketing-ops, lifecycle]
status: shipped
link: null
visibility: public
---

Optional 1–3 paragraphs of context. Markdown supported. Keep it skim-friendly —
the audience is someone deciding whether to read more.
```

### Frontmatter fields

| Field | Type | Required | Notes |
|---|---|---|---|
| `title` | string | yes | Public-safe headline. Imperative or descriptive — both fine. |
| `date` | YYYY-MM-DD | yes | ISO date of completion. |
| `category` | enum | yes | See [Categories](#categories). |
| `venture` | enum | no | Which business. See [Ventures](#ventures). |
| `tags` | string[] | no | Free-form. Used for filtering only. |
| `status` | enum | no | Default `shipped`. Also: `in-progress`, `sunset`. |
| `link` | URL or null | no | Public destination (blog post, public app, talk video). |
| `slug` | string | no | Override filename-derived slug. |
| `visibility` | enum | no | Default `public`. Set to `draft` to exclude from feed. |

### Categories

- `system` — built a system / tool / automation
- `launch` — shipped something publicly live
- `research` — published research, paper, study
- `partnership` — closed a strategic deal
- `talk` — presented / spoke at a conference
- `essay` — wrote a long-form piece
- `acquisition` — exit / M&A event
- `milestone` — generic numerical milestone (10K users, $1M ARR, etc.)

Add a new category by using it. The consumer renders unknown categories with a
neutral label.

### Ventures

- `leal-health`
- `indexthread`
- `actually-health`
- `haba`
- `multistatemd`
- `whitecoat-alliance`
- `personal` — anything not tied to a venture

## Editorial rules

Five rules. Break them with intent, not accident.

1. **Public-safe by default.** No client names that haven't been published
   elsewhere. No internal numbers that aren't already in a deck I've shown
   externally.
2. **Sentence-first headline.** Lead with what got done, not what kind of
   thing it is. `"Shipped a referral engine"` beats `"New project: referral
   engine"`.
3. **No dates in the headline.** The date field carries them.
4. **One claim per entry.** If two things shipped, two files.
5. **The body explains the operator angle.** Why it matters, what the system
   does differently, what compounded. Skip the "here's how I built it"
   tutorial.

## Adding a new update

```bash
# 1. Drop a file at updates/<YYYY-MM-DD>-<slug>.md following the format above.
# 2. Commit + push to main. That's it.
# 3. The site picks it up on next deploy.
```

Filename slugs should match the title's keyword shape — lowercase, hyphenated,
trim filler. Examples:

- `2026-06-02-marketing-ai-lifecycle-system.md`
- `2026-05-14-asco-poster-on-patient-acquisition.md`
- `2026-04-30-shipped-csv-merger-tool.md`

## How the site consumes this

The jackgierlich.com build pulls the file list via GitHub's REST API, fetches
each `.md`, parses frontmatter, and bakes the feed into the bundle.

No runtime fetch — the site's CSP keeps `connect-src 'self'`.

See [`schema/update.schema.json`](schema/update.schema.json) for a machine
contract if you want to validate locally before pushing.

## License

[MIT](LICENSE). The text in this repo is mine; quote it freely.
