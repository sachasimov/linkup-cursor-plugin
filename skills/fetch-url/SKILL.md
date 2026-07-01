---
name: fetch-url
description: Use this skill when you already know the exact URL and need its content as clean Markdown — a pricing page, an article, a docs page, or a URL you found in a previous step. Uses the Linkup MCP `linkup-fetch` tool. Prefer this over web-search when the URL is known.
---

# Linkup Fetch

When you already have the exact URL, use `linkup-fetch` instead of search. It is faster, cheaper, and purpose-built for single-page extraction — it returns the page as clean Markdown.

## When to use fetch vs search

| Use `fetch-url` when... | Use `web-search` when... |
| --- | --- |
| You have a specific URL and want its content | You don't know which URL has the answer |
| Scraping a known page (pricing, article, docs) | You need results from multiple pages |
| A previous step found a URL you now need to read | You need Linkup to find and extract for you |

## How to call it

Pass the URL to `linkup-fetch`. **Default `renderJs` to true** — many sites load content via JavaScript, and the small latency cost is almost always worth the reliability. Only turn it off when speed matters more than completeness on a known-static page.

## After fetching

- The result is Markdown. Extract the specific fields the task needs rather than dumping the whole page back to the user.
- Keep the source URL so any claim you make can be verified.
- If the page is one of several you need, and you only have this one URL, fetch it here; if you need to discover *and* scrape multiple pages, switch to `web-search` with `deep`.
