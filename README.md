# CiteHawk Skills

Agent skills for [CiteHawk](https://www.citehawk.com) — AI-visibility (GEO)
monitoring for brands. These skills let Claude Code (and other agents supported
by the `skills` CLI) read your live CiteHawk data and act on your own website
repo to improve how AI assistants — ChatGPT, Claude, Gemini, Perplexity,
DeepSeek, Grok, Copilot, Google AI Overviews — mention and cite you.

## Skills

### `citehawk-geo` — Agentic GEO

Runs the **find → fix → measure** loop:

1. **Find** — reads your real visibility gaps and recommendations from the
   CiteHawk MCP (`get_visibility_summary`, `list_recommendations`,
   `get_source_gaps`, `get_rankings`, `get_prompt_performance`, `get_evidence`).
2. **Fix** — edits your website repo to close them: schema.org / JSON-LD
   markup, AI-citable comparison and FAQ pages, titles/meta descriptions, and
   answer-first content structure so AI answers can quote you.
3. **Measure** — commits the changes; CiteHawk's next weekly collection
   quantifies the lift in your visibility scores.

Every fix traces back to a specific, live gap in your workspace — not generic
SEO advice.

## Install

Prerequisite: a CiteHawk account and workspace API key (self-served from
CiteHawk Settings — [citehawk.com](https://www.citehawk.com)).

**1. Connect the CiteHawk MCP** (the skill's data source):

```bash
claude mcp add --transport http citehawk https://www.citehawk.com/api/mcp \
  --header "Authorization: Bearer <API_KEY>"
```

**2. Add the skill:**

```bash
npx skills add richyparr/citehawk-skills
```

The MCP is scoped to the single workspace that owns the API key, so the skill
can never read across tenants and never needs a workspace id.

## Also available: REST API + OpenAPI

For non-MCP agents (ChatGPT custom GPT Actions, any framework), CiteHawk
exposes a read-only REST API (`/api/v1/brands`, `/api/v1/metrics`,
`/api/v1/responses`) described by an OpenAPI 3.1 spec at
[`https://www.citehawk.com/openapi.json`](https://www.citehawk.com/openapi.json),
using the same Bearer API key.

Docs: [citehawk.com/docs](https://www.citehawk.com/docs) ·
[/docs/mcp](https://www.citehawk.com/docs/mcp) ·
[/docs/claude-code](https://www.citehawk.com/docs/claude-code) ·
[/docs/chatgpt](https://www.citehawk.com/docs/chatgpt)

## License

MIT © RP & SE Technology Pty Ltd
