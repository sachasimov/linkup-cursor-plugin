# Linkup plugin for Cursor

Give Cursor reliable, real-time access to the web through [Linkup](https://www.linkup.so) — a web search API built for agents. It searches the live web, reads pages, runs multi-source deep research, extracts structured records, and returns cited results.

This plugin ships three things:

1. **The Linkup MCP server** — adds `linkup-search` and `linkup-fetch` tools to the agent.
2. **Five skills** — concise, on-demand entry points that teach the agent *how* to use Linkup well.
3. **A bundled knowledge pack** (`reference/`) — the full query, endpoint, and workflow knowledge from [linkup-for-agents](https://github.com/LinkupPlatform/linkup-for-agents), plus 18 ready-to-adapt workflow recipes. The skills route into these files for exact rules and templates.

## Skills

| Skill | Use it for | Routes into |
|-------|-----------|-------------|
| `web-search` | Write one great search: pick depth, output type, filters; phrase the query as a retrieval plan | Query mental model + prompt optimizer |
| `fetch-url` | Read a known URL as clean Markdown | API reference (Fetch) |
| `deep-research` | Minutes-long, cross-checked, cited investigations (`/v1/research`) | Specialized endpoints (Research) |
| `bulk-extract` | Many structured rows from one listing page (`/v1/extract`) | Specialized endpoints (Extract) |
| `build-workflow` | Turn a business goal into a multi-step Linkup workflow | Workflow guide + optimizer + recipes |

## Setup

1. Get a Linkup API key at [app.linkup.so](https://app.linkup.so).
2. Set it in your environment so both the MCP server and the REST-based skills can read it:

```shell
export LINKUP_API_KEY="your-key"
```

The MCP server reads the key from `${env:LINKUP_API_KEY}` — no secret is stored in this repo. The `deep-research`, `bulk-extract`, and advanced `web-search` (structured output, domain/date filters) flows also use `LINKUP_API_KEY` for direct REST calls. Restart Cursor after setting the key.

## Install

Install from the Cursor Marketplace, or add the repository directly in Cursor from **Customize → Plugins**.

### Test locally

```shell
ln -s "$(pwd)" ~/.cursor/plugins/local/linkup
```

Restart Cursor (or run **Developer: Reload Window**) and confirm the `linkup` MCP server and the five skills appear under **Customize**.

## Usage examples

- "Find Anthropic's latest funding round amount and date." → `web-search`
- "Scrape https://www.datadoghq.com/pricing and extract each plan's name and price." → `fetch-url`
- "Research and compare 2024 cloud revenue growth for Microsoft, Amazon, and Google with sources." → `deep-research`
- "Extract every open role from this careers page into a table." → `bulk-extract`
- "I have a list of companies — how should I use Linkup to enrich and prioritize them?" → `build-workflow`

## What's in `reference/`

```
reference/
├── knowledge/
│   ├── LINKUP_API_REFERENCE.md              # endpoints, output types, depth, auth
│   ├── LINKUP_AGENT_QUERY_MENTAL_MODEL.md   # reason to the right request shape
│   ├── LINKUP_PROMPT_OPTIMIZER_KNOWLEDGE.md # exact depth rules, templates, bad patterns
│   ├── LINKUP_WORKFLOW_GUIDE.md             # 8 workflow patterns + vertical playbooks
│   ├── LINKUP_WORKFLOW_OPTIMIZER_KNOWLEDGE.md # assemble a goal into steps
│   └── LINKUP_SPECIALIZED_ENDPOINTS.md      # /research and /extract
└── workflows/                               # 18 recipes: sales, marketing, research
```

## Credits

Knowledge pack and workflow recipes from [LinkupPlatform/linkup-for-agents](https://github.com/LinkupPlatform/linkup-for-agents). Plugin format by Cursor.

## License

MIT — see [LICENSE](LICENSE).
