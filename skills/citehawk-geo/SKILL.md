---
name: citehawk-geo
description: >-
  Close AI-visibility (GEO) gaps in a website's own repo using live CiteHawk
  data. Use when the user wants to improve how AI assistants (ChatGPT, Claude,
  Gemini, Perplexity) mention and cite their brand, mentions "AI visibility",
  "GEO", "generative engine optimization", "get cited by AI", "AI search", or
  "CiteHawk", or asks why competitors show up in AI answers and they do not.
  This skill reads the user's real visibility gaps and recommendations from the
  CiteHawk MCP, then edits their site (schema.org/JSON-LD, comparison and FAQ
  pages, titles/meta, content structure) to close them. Requires the CiteHawk
  MCP to be connected.
---

# CiteHawk Agentic GEO

You are closing **AI-visibility gaps** for the user's brand: the places where AI
assistants answer questions in the user's category but fail to mention or cite
them. CiteHawk measures this every Monday by asking real prompts across 8 AI
engines. Your job is the **find → fix → measure** loop:

1. **Find** — read the user's real gaps and recommendations from the CiteHawk MCP.
2. **Fix** — act on the user's OWN website repo to close them.
3. **Measure** — commit, then tell the user next Monday's CiteHawk collection
   will quantify the lift.

You are not guessing at generic SEO. Every fix must trace back to a specific gap
or recommendation returned by the MCP.

## Precondition: connect the CiteHawk MCP

The data comes from the CiteHawk MCP server. If it is not already connected,
have the user add it (an API key is self-served from CiteHawk Settings):

```bash
claude mcp add --transport http citehawk https://www.citehawk.com/api/mcp \
  --header "Authorization: Bearer <API_KEY>"
```

Confirm the connection by calling `get_visibility_summary` once. If it returns
`available: false`, the workspace has no collection yet — tell the user to run a
collection in CiteHawk first, then stop. Do not fabricate data.

## Step 1 — Find the gaps (read from the MCP)

Pull the picture in this order. Each tool is scoped to the user's workspace by
their API key; you never pass a workspace id.

- **`get_visibility_summary`** — the headline. Visibility + Brand Health scores,
  sub-scores (presence, authority, sentiment, accuracy), KPIs (share of voice,
  mention rate, citation rate), how many of the AI engines currently cite the
  brand, and the last collection date. Start here to know how bad it is.
- **`list_recommendations`** — CiteHawk's own prioritised recommendations, each
  with `category`, `priority`, `status`, `impact`, `effort`. Treat high-priority,
  low-effort items as your first targets.
- **`get_source_gaps`** — the external sources (directories, review sites,
  listicles) AI assistants cite in this category, and whether the brand is
  confirmed listed, confirmed missing, or how many competitors rank ahead where
  AI already cites the source. This tells you which off-site places to get
  listed and which comparison/alternative pages to write.
- **`get_rankings`** — where the brand sits versus competitors on a chosen metric
  (`sov`, `mentionRate`, `citationRate`, `avgSentiment`). Identifies the specific
  competitors beating them, so your comparison pages name real rivals.
- **`get_prompt_performance`** — the exact prompts the brand wins and loses on.
  Losing prompts are literal content briefs: write pages that answer them.
- **`get_evidence`** — for any domain, the real AI answers (provider, prompt,
  excerpt, exact cited URL, date) that cite it. Use this to confirm a gap is real
  and to see the phrasing/format AI actually quotes before you write.

Summarise for the user: current scores, the 3-5 highest-leverage gaps, and which
of them you can fix in *their repo* (versus off-site actions like getting listed
on a third-party directory, which you can only draft outreach/content for).

## Step 2 — Fix in the user's repo

First, understand the site: find the framework (Next.js, Astro, Hugo, plain
HTML, WordPress theme, etc.), where pages/templates live, and how metadata and
structured data are currently set. Then apply the playbooks below that match the
gaps. Make real, minimal, correct edits — no placeholder content.

### Playbook A — schema.org / JSON-LD structured data

AI assistants and their retrieval layers lean on structured data to understand
and cite entities. Missing or thin schema is a common presence gap.

- **Organization** (site-wide, in the root layout/head) — establishes the brand
  as an entity:

  ```html
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "Acme Analytics",
    "url": "https://acme.com",
    "logo": "https://acme.com/logo.png",
    "description": "Product analytics for B2B SaaS teams.",
    "sameAs": [
      "https://www.linkedin.com/company/acme",
      "https://twitter.com/acme",
      "https://g2.com/products/acme"
    ]
  }
  </script>
  ```

- **Product / SoftwareApplication** (on product pages) — with `name`,
  `description`, `offers` (pricing), and `aggregateRating` if you have real
  review data. Never invent ratings.
- **FAQPage** (on any page with Q&A content) — each `Question` + `acceptedAnswer`
  becomes a directly quotable unit for AI answers.
- **BreadcrumbList** and **Article** for content/blog pages.

In Next.js App Router, prefer rendering the JSON-LD via a `<script>` tag in the
page/layout, or `generateMetadata`. Validate the JSON is well-formed and the
`@type` values are real schema.org types.

### Playbook B — AI-citable comparison / alternatives pages

When `get_source_gaps` or `get_rankings` shows competitors winning, the highest-
leverage new content is a comparison page. AI assistants quote these directly for
"best X" and "X vs Y" and "X alternatives" prompts.

Structure that gets cited:

1. **Clear H1** naming the comparison: "Acme vs Rival: Analytics Compared (2026)".
2. **A summary sentence up top** that directly answers the likely prompt — AI
   often lifts the first authoritative sentence verbatim. Make it factual and
   self-contained ("Acme is a product-analytics tool for B2B SaaS; Rival focuses
   on marketing attribution.").
3. **A comparison table** — feature rows, both products in columns. Tables are
   easy for models to parse and quote.
4. **Honest, specific differentiators** — real capabilities, not marketing fluff.
   AI systems downrank and rarely cite obvious puffery.
5. **An FAQ block** (with FAQPage schema) answering the sub-questions.
6. **Cite your own sources** — link to docs, pricing, changelog. Verifiable pages
   get cited more.

Name the *real* competitors surfaced by `get_rankings`, and target the *real*
losing prompts from `get_prompt_performance`.

### Playbook C — titles, meta descriptions, and content structure

- **Titles** — front-load the entity and the job ("Acme Analytics — Product
  Analytics for B2B SaaS"), not vague slogans.
- **Meta descriptions** — one factual sentence that stands alone as an answer.
- **Heading structure** — one H1, meaningful H2/H3 that read as questions or
  clear topics. Models chunk by heading.
- **Answer-first paragraphs** — lead each section with the direct answer, then
  elaborate. This is the single biggest lever for being quoted.
- **Definitions and specifics** — concrete numbers, dates, capabilities, and a
  crisp "What is X?" definition near the top of key pages.
- **Fix accuracy gaps** — if `get_visibility_summary` shows a low accuracy score
  or `list_recommendations` flags wrong facts AI is repeating, correct the
  canonical facts (pricing, positioning, feature list) on-site so future
  collections pick up the truth.

### What to hand off rather than edit

Off-site gaps (getting listed on a directory or review site from
`get_source_gaps`, earning third-party mentions) can't be fixed by a repo edit.
For those, draft the submission/outreach content or a listing-worthy page, and
tell the user the off-site action to take.

## Step 3 — Commit and set up measurement

- Make focused commits (conventional-commit style), e.g.
  `feat(geo): add Organization + FAQPage JSON-LD to close CiteHawk presence gap`.
- Branch rather than committing to the default branch unless the user's workflow
  says otherwise.
- Tell the user plainly: these changes take effect once deployed and re-crawled;
  **CiteHawk's next weekly collection (Mondays) will re-measure** presence,
  citation rate, and the affected prompts, so the lift shows up in their
  visibility scores then. Suggest they re-run this skill after the next
  collection to confirm the gap closed and pick the next target.

## Guardrails

- Never invent metrics, ratings, reviews, or facts. Every claim on-site must be
  true; every fix must map to a real MCP gap.
- If the MCP returns `available: false` or empty, say so and stop — do not
  proceed on assumptions.
- Keep edits minimal and reversible; match the site's existing conventions.
