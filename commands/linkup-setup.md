---
name: linkup-setup
description: Set up the Linkup plugin — configure the MCP server, the API key, and verify the tools load
---

# Linkup Plugin Setup

## Step 1 — Get an API key

Ask the user to get a Linkup API key at https://app.linkup.so if they don't have one.

## Step 2 — Make the key available

The bundled `mcp.json` reads the key from `${env:LINKUP_API_KEY}`. Cursor does **not** read a project `.env`, so the variable must be in Cursor's own environment. Two options:

- **Env var (recommended, no secret in files):** have the user set `LINKUP_API_KEY` and launch Cursor so it inherits it:

  ```bash
  export LINKUP_API_KEY="their-key"
  open -a Cursor   # macOS
  ```

- **Direct in config:** if they prefer, put the key straight into the server URL in `~/.cursor/mcp.json` instead of the env-var placeholder. Warn that this stores the secret in a file — never commit it.

Verify the key works before continuing:

```bash
curl -sS -o /dev/null -w "%{http_code}\n" -X POST "https://api.linkup.so/v1/search" \
  -H "Authorization: Bearer $LINKUP_API_KEY" -H "Content-Type: application/json" \
  -d '{"q":"test","depth":"standard","outputType":"searchResults"}'
```

`200` means the key is valid; `401`/`403` means it's missing or wrong.

## Step 3 — Confirm the MCP server config

The plugin ships this `mcp.json` at its root (auto-discovered). It runs Linkup's MCP server locally over stdio via `npx`, which requires Node.js v24+. If setting up manually without the plugin, add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "linkup": {
      "command": "npx",
      "args": ["-y", "linkup-mcp-server", "apiKey=${env:LINKUP_API_KEY}"]
    }
  }
}
```

This exposes the `linkup-search`, `linkup-fetch`, `linkup-research`, and `linkup-get-research` tools.

### Remote MCP (alternative)

Linkup's docs recommend the hosted endpoint (no Node/npx needed). The docs' preferred config for Cursor, using header auth:

```json
{
  "mcpServers": {
    "linkup": {
      "type": "http",
      "url": "https://mcp.linkup.so/mcp",
      "headers": { "Authorization": "Bearer ${env:LINKUP_API_KEY}" }
    }
  }
}
```

**Known incompatibility with Cursor (as of 2026-07):** this does not currently work in Cursor. Cursor's streamable-HTTP client opens a session `GET` SSE stream, and the hosted endpoint returns **HTTP 404** for it; after 5 such responses Cursor tombstones the connection. A spec-compliant server that has no server-initiated stream should return **405 Method Not Allowed** instead (for example, Exa's hosted endpoint returns 405 and works in Cursor). This affects both the `Authorization: Bearer` and `?apiKey=` auth forms identically, so it is not an auth problem. Until the hosted endpoint returns 405 (or serves the stream), use the local `npx` stdio config above.

## Step 4 — Fully restart Cursor

This is the step most people miss. Reloading the window or reopening while Cursor is still running does **not** pick up a newly installed local plugin or MCP server.

Ask the user to **quit Cursor completely** (`Cmd+Q` on macOS — closing the window is not enough), then reopen it.

## Step 5 — Verify

- Open **Cursor Settings → Tools & MCP** (or **Customize**), find **linkup**, and make sure it is enabled (green).
- Confirm `linkup-search` appears in the available tool list.

## Troubleshooting

If `linkup-search` is still not available:

- Do not fall back to answering from memory or other search tools — stop and fix setup first.
- Confirm the `linkup` MCP server shows as enabled (green) in **Settings → Tools & MCP**.
- Confirm `LINKUP_API_KEY` is actually set in the environment Cursor was launched from (not just in a project `.env`).
- Confirm Cursor was fully quit and reopened after install.
