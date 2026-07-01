---
name: deep-research
description: Use this skill for multi-source investigations that need a thorough, sourced answer or report and can run for minutes — competitive analyses, market landscapes, due diligence, "compare X across Y" questions. Uses Linkup's async /v1/research REST endpoint (not the MCP tools). Requires LINKUP_API_KEY in the environment.
---

# Linkup Deep Research

For questions too broad for a single search — comparisons across many entities, market landscapes, due diligence, sourced reports — use Linkup's Research endpoint. It runs asynchronously (seconds to minutes) and returns a well-sourced answer.

This uses the REST API directly, not the MCP tools, so it needs `LINKUP_API_KEY` in the environment. Confirm it is set before calling:

```shell
test -n "$LINKUP_API_KEY" || echo "Missing LINKUP_API_KEY"
```

## When to use research vs search

Reach for research only when a single `deep` search can't cover it: many entities, many facets, or a report-style deliverable. For a single fact or one page, use `web-search` or `fetch-url` — they are cheaper and faster.

## Settings

- `mode`: `answer` (direct), `investigate` (balanced, most common), or `research` (most thorough).
- `reasoningDepth`: `S`, `M`, `L`, or `XL` — more depth means more time and cost.
- `outputType`: `sourcedAnswer` or `structured`.

## Submit, then poll

`POST /v1/research` returns a task envelope immediately. Poll `GET /v1/research/{id}` every 5–10 seconds until `status` is `completed` or `failed`. Never poll faster than once per second.

```shell
# Submit
curl -sS -X POST "https://api.linkup.so/v1/research" \
  -H "Authorization: Bearer $LINKUP_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "Compare 2024 cloud revenue growth for Microsoft, Amazon, and Google using primary sources.",
    "outputType": "sourcedAnswer",
    "mode": "investigate",
    "reasoningDepth": "L"
  }'

# Poll (repeat every 5-10s until completed/failed)
curl -sS "https://api.linkup.so/v1/research/RESEARCH_ID" \
  -H "Authorization: Bearer $LINKUP_API_KEY"
```

## Guidance

- Phrase `q` as a clear question with an explicit scope: timeframe, geography, entities, and the specific sub-topics to cover.
- Because this call costs more and runs for minutes, tell the user what you're about to run and why before starting.
- Keep the `sources` Linkup returns so the answer can be verified. Ground every claim in them.
- Retry only transient failures (`429`, `5xx`) with exponential backoff. Never retry a malformed `400` without changing the request.
- When done, summarize in plain English: what was researched, the answer, key sources, and roughly how long it took.
