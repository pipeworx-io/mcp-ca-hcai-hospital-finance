# @pipeworx/ca-hcai-hospital-finance

California hospital quarterly financial and utilization data: revenue, expense, derived operating margin, balance-sheet and volume lines for every licensed hospital, from the state's Department of Health Care Access and Information (HCAI, formerly OSHPD).

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2043+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `ca_hospital_finance(hospital?, facility_no?, county?, year?, quarter?, limit?)` — one hospital's (or a county's) revenue and expense lines for a quarter; answers "2025 Q1 operating margin for Adventist Health Rideout".
- `ca_hospital_periods(limit?)` — which quarterly reports are published, newest first.

## Auth

Keyless.

## Data sources

- California Department of Health Care Access and Information (HCAI), Hospital Quarterly Financial and Utilization Report — each licensed hospital's quarterly filing, published as a trailing four-quarter total per report quarter.

Each report covers the FOUR quarters ending in the report quarter (a "2025 Quarter 1" row spans 2024-04-01 to 2025-03-31), so the figures are annualised, not single-quarter. Net patient revenue equals gross inpatient plus gross outpatient revenue, less deductions, plus capitation (verified on a real row). Operating revenue, operating income and operating margin are DERIVED here (net patient revenue plus other operating revenue, minus operating expense) and carry a `_derived` suffix; HCAI does not publish them as columns in this table. Peer-group ratios are not computed.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "ca-hcai-hospital-finance": {
      "url": "https://gateway.pipeworx.io/ca-hcai-hospital-finance/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/ca-hcai-hospital-finance/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 2043+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ca_hospital_finance \
  -H 'Content-Type: application/json' \
  -d '{"hospital":"Rideout","year":2025,"quarter":1}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ca_hospital_finance`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-ca-hcai-hospital-finance` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/ca-hcai-hospital-finance/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "ca-hcai-hospital-finance": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-ca-hcai-hospital-finance"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Ca Hcai Hospital Finance data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
