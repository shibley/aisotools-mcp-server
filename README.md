# AISOTools MCP Server

A hosted [Model Context Protocol](https://modelcontextprotocol.io) server over the
[AISOTools](https://aisotools.com) catalog — **3,064 AI tools** with pricing, ratings,
features, pros, cons, and curated alternatives.

There is nothing to install. The server is a stateless Streamable HTTP endpoint:

```
https://aisotools.com/api/mcp
```

Every result carries the canonical `https://aisotools.com/tool/<slug>` page for the
tool, so an assistant answering "what's the best transcription API?" can cite a source
rather than paraphrase from memory.

## Tools

| Tool | What it does |
|------|--------------|
| `search_ai_tools` | Search the catalog by keyword, `category`, and `pricing` (`free`/`freemium`/`paid`). Ranked summaries. |
| `get_ai_tool` | Full record for one tool — description, features, pros, cons, who it's best for, pricing tiers, alternatives. Accepts a slug **or** a display name. |
| `list_ai_tool_categories` | Every category with its slug, subcategories, and tool count. Use the slugs to filter `search_ai_tools`. |
| `compare_ai_tools` | Side-by-side comparison of 2–5 named tools. Names it can't resolve come back in an `unmatched` array rather than being silently dropped. |
| `find_ai_tool_alternatives` | Alternatives to a given tool. Curated alternatives and same-category fallbacks are returned in **separate** fields, not blended. |

Read-only. No authentication, no API key, no rate-limit signup.

## Connect

### Claude Code

```bash
claude mcp add --transport http aisotools https://aisotools.com/api/mcp
```

### Claude Desktop / any client without native HTTP transport

```json
{
  "mcpServers": {
    "aisotools": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://aisotools.com/api/mcp"]
    }
  }
}
```

### Raw JSON-RPC

```bash
curl -sS https://aisotools.com/api/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Notes on the data

- `totalTools` is **3,064** at the time of writing and moves as the catalog grows;
  `search_ai_tools` reports both `matched` and `searched` so you can see the
  denominator it actually searched.
- `sponsored` is computed against the placement's expiry, so a lapsed sponsorship
  reports `false` rather than staying flagged forever. Sponsored placement never
  changes search ranking.
- Ratings and review counts are catalog metadata, not live scrapes.

## Protocol

Streamable HTTP, protocol version `2025-06-18`. `POST` only — `GET` and `DELETE`
answer `405` (the server is stateless, so there is no SSE stream or session to
resume). Notifications answer `202` with no body. JSON-RPC batches are supported.

## License

MIT © Bity LLC
