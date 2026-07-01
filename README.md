# Linkup plugin for Cursor

Give Cursor real-time access to the web through [Linkup](https://linkup.so) — an agentic search API that finds, reads, and researches live web pages and returns accurate, sourced results.

This plugin bundles:

- The **Linkup MCP server**, which adds two tools to the agent:
  - `linkup-search` — agentic web search with adjustable depth
  - `linkup-fetch` — fetch any URL as clean Markdown
- Three **skills** that teach the agent how to use Linkup well:

| Skill | What it does |
|-------|--------------|
| `web-search` | How to construct queries, pick search depth, and choose output type |
| `fetch-url` | When and how to scrape a known URL as Markdown |
| `deep-research` | Run multi-source, sourced investigations via Linkup's async Research endpoint |

## Setup

1. Get a Linkup API key at [app.linkup.so](https://app.linkup.so).
2. Set it in your environment so Cursor can read it:

```shell
export LINKUP_API_KEY="your-key"
```

The MCP server reads the key from `${env:LINKUP_API_KEY}` — no secret is stored in this repo. Restart Cursor after setting it so the server picks it up.

> The `deep-research` skill also uses `LINKUP_API_KEY` for direct REST calls to the Research endpoint.

## Install

Install from the Cursor Marketplace, or add the repository directly in Cursor from **Customize → Plugins**.

### Test locally

Symlink this repo into Cursor's local plugin folder, then reload the window:

```shell
ln -s "$(pwd)" ~/.cursor/plugins/local/linkup
```

Restart Cursor (or run **Developer: Reload Window**) and confirm the `linkup` MCP server and the three skills appear under **Customize**.

## Usage examples

- "Find Anthropic's latest funding round amount and date."
- "Scrape https://www.datadoghq.com/pricing and extract each plan's name and price."
- "Research and compare 2024 cloud revenue growth for Microsoft, Amazon, and Google with sources."

## Directory structure

```
linkup-cursor-plugin/
├── .cursor-plugin/
│   └── plugin.json        # Plugin manifest
├── mcp.json               # Linkup MCP server (search + fetch tools)
├── skills/
│   ├── web-search/
│   ├── fetch-url/
│   └── deep-research/
└── README.md
```

## License

MIT
