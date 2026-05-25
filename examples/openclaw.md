# OpenClaw

Connect Breakcold to OpenClaw as a remote MCP server.

Website: [https://openclaw.ai](https://openclaw.ai)

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## Setup

Add Breakcold to OpenClaw's MCP server registry:

```bash
openclaw mcp set breakcold '{"url":"https://http.us.breakcold.com/mcp/v1","transport":"streamable-http","headers":{"Authorization":"Bearer <your Breakcold bearer key>"}}'
```

Verify the server is saved:

```bash
openclaw mcp show breakcold --json
```

Use the Europe URL instead if your Breakcold workspace is hosted in Europe.

OpenClaw's MCP registry stores server definitions for runtimes that consume MCP servers, and Breakcold is exposed as a Streamable HTTP MCP server.

## Bearer-token access

OpenClaw connects to Breakcold using a bearer key rather than an interactive OAuth flow. Use bearer-token access when an agent should not act as a signed-in user, or when running OpenClaw in unattended/service mode.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and reference it from the OpenClaw registry entry instead of hard-coding the value.

For sensitive CRM access, keep the bearer key scoped to the minimum Breakcold tools your OpenClaw agent needs.
