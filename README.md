# mcp-nansen

Nansen Smart Money MCP — on-chain smart-money labels & flows (nansen.ai)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `nansen_smart_money_netflow` | What is smart money buying or selling (net flow) on chain X — returns tokens ranked by net USD flow from Nansen "Smart Money" wallets (top traders + funds) over a time window. Positive = accumulation, negative = distribution. Example: nansen_smart_money_netflow({ chain: "ethereum", timeframe: "1d", _apiKey: "your-nansen-key" }) |
| `nansen_smart_money_holdings` | What does smart money hold on chain X — returns tokens currently held by Nansen "Smart Money" wallets on a chain, ranked by USD value held, with holder counts and 24h balance change. Example: nansen_smart_money_holdings({ chain: "ethereum", _apiKey: "your-nansen-key" }) |
| `nansen_wallet_profiler` | Labels & profile for wallet X — returns Nansen labels (e.g. "Smart Money", "Fund", exchange/entity tags) for a wallet address, plus a best-effort USD balance summary. Example: nansen_wallet_profiler({ address: "0x28c6c06298d514db089934071355e5743bf21d60", chain: "ethereum", _apiKey: "your-nansen-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "nansen": {
      "url": "https://gateway.pipeworx.io/nansen/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/nansen/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/nansen_smart_money_netflow`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "nansen": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-nansen"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-nansen
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Nansen data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
