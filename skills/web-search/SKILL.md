---
name: web-search
description: Use this skill whenever you need real-time information from the web via Linkup — company research, news, pricing, data enrichment, fact-checking, or anything not in the codebase. Teaches how to construct queries, pick search depth, and choose the right output type using the Linkup MCP `linkup-search` tool.
---

# Linkup Web Search

Linkup is an agentic web search API. It interprets a natural-language instruction and runs the retrieval steps needed to return accurate, real-time web data. This plugin exposes it through the `linkup-search` tool (and `linkup-fetch`; see the `fetch-url` skill).

Do the reasoning and synthesis yourself. Tell Linkup **what to find and where to look** — not what conclusion to reach.

## 1. Reason before you query

Answer three questions in order. Each answer constrains the next.

**What inputs do I already have?**

| I have... | Then... |
| --- | --- |
| A specific URL | Scrape it directly with `fetch-url` — don't waste a search finding it |
| A company name, topic, or question (no URL) | You need to search |
| Both a URL and a broader question | Combine: scrape the known URL + search for the rest |

**Where does the data live?**

| The data is... | Example | Then... |
| --- | --- | --- |
| In search snippets (titles, short facts) | A funding amount, a launch date, a job title | `standard` is enough |
| On full pages (tables, specs, long-form) | A pricing table, an article body | You must **scrape** the page → likely `deep` |
| Not sure | — | Default to `deep` |

**Do I need to chain steps?**

| Scenario | Sequential? | Depth |
| --- | --- | --- |
| Everything can be gathered in parallel searches | No | `standard` |
| I have one URL and just need to scrape it | No | `standard` (or `fetch-url`) |
| I must find URLs first, then scrape them | Yes | `deep` |
| I must scrape a page, then search again on what I found | Yes | `deep` |
| I must scrape multiple known URLs | Yes | `deep` |

## 2. Choose depth

- **`standard`** — runs multiple parallel searches; can scrape **one** URL provided in the prompt. Cannot scrape multiple URLs or follow URLs discovered mid-search.
- **`deep`** — up to ~10 iterative passes; can scrape multiple URLs and chain search → scrape.

When uncertain, default to `deep`.

Cost tip: 3–5 focused `standard` calls in parallel are often faster and cheaper than one `deep` call. Reserve `deep` for scraping multiple URLs or chaining search → scrape.

## 3. Choose output type

| Output | Returns | Use when |
| --- | --- | --- |
| `searchResults` | `{name, url, content}` sources | You will filter/synthesize the results yourself |
| `sourcedAnswer` | Natural-language answer + sources | The answer is shown directly to the user |
| `structured` | JSON matching a schema you provide | Results feed an automated pipeline |

Default to `searchResults` when you'll process results yourself.

## 4. Write effective queries

**Be specific.** Add dates ("Q4 2025"), locations ("French company Total"), versions ("since React 19"), domains ("on sec.gov").

| Bad | Good |
| --- | --- |
| "Tell me about the company" | "Find {company}'s annual revenue and employee count" |
| "Microsoft revenue" | "Microsoft fiscal year 2024 total revenue" |
| "AI news" | "OpenAI product announcements January 2026" |

**Keyword-style** for simple facts: `"NVIDIA Q4 2024 revenue"`.

**Instruction-style** for extraction: `"Find Datadog's pricing page. Extract plan names, per-host prices, and included features per tier."`

**Ask for breadth** (works even in `standard`): `"Find recent news about OpenAI. Run several searches with adjacent keywords: 'OpenAI funding', 'OpenAI product launch', 'OpenAI partnerships'."`

**Chain sequentially** (deep only): `"First find Snowflake's LinkedIn company page. Then scrape it and extract employee count, HQ, industry, and description."`

## 5. Filters — only when asked

Apply `includeDomains` / `excludeDomains` and `fromDate` / `toDate` only when the user or task implies that constraint. Don't add them speculatively.

## Quick reference

```
STANDARD  parallel searches ✓  scrape one provided URL ✓  scrape many ✗  chain search→scrape ✗
DEEP      iterative passes ✓   scrape many URLs ✓         chain search→scrape ✓
UNCERTAIN default to deep
OUTPUT    searchResults (raw) | sourcedAnswer (user-facing) | structured (JSON schema)
```
