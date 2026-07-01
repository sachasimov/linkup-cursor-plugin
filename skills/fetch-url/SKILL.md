---
name: fetch-url
description: Use when you already know the exact URL and need its content as clean Markdown — a pricing page, article, docs page, PDF, or a URL found in a previous step. Uses the Linkup `linkup-fetch` MCP tool. Prefer this over web-search when the URL is known; prefer bulk-extract when you need many structured rows from one listing page.
---

# Linkup Fetch

When you already have the exact URL, use the **`linkup-fetch` MCP tool** instead of search. It is faster and cheaper, and returns the page as clean Markdown (with JavaScript rendering, PDF support, and optional image extraction).

## When to use fetch vs the alternatives

| Use `fetch-url` when... | Use instead... |
| --- | --- |
| You have one URL and want its content as Markdown | — |
| You don't know which URL has the answer | `web-search` |
| You need many structured records from one listing page (team, catalog, jobs) | `bulk-extract` (`/v1/extract`) |
| You need to discover URLs and then scrape them | `web-search` with `depth: deep` |

## How to call it

Pass the URL to `linkup-fetch`. **Default JavaScript rendering to on** — many sites load content client-side, and the small latency cost is almost always worth the reliability. Turn it off only when speed matters more on a known-static page.

Important: fetch reads page *content*; it cannot produce structured JSON from a page. If you need structured fields from a known page, use `bulk-extract` (`/v1/extract`) or the Search API with `outputType: structured`.

## After fetching

- Extract only the fields the task needs; don't dump the whole page back to the user.
- Keep the source URL so every claim can be verified.

For the full endpoint reference, read `reference/knowledge/LINKUP_API_REFERENCE.md` (Fetch API section) at the plugin root.
