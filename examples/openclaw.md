# OpenClaw

Connect Breakcold to OpenClaw as a remote MCP server.

Website: https://openclaw.ai

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Add Breakcold to OpenClaw's MCP server registry with openclaw mcp set.
2. Use the MCP server URL for your Breakcold workspace region.
3. Set transport to streamable-http.
4. Use an Authorization bearer header with a Breakcold bearer key.

Use OAuth when OpenClaw asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when OpenClaw cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

## Note

OpenClaw stores MCP server definitions for runtimes that consume MCP servers, so keep the Breakcold bearer key scoped to the minimum tools the agent needs.
