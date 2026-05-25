# Hermes

Connect Breakcold to Hermes as a remote MCP server.

Website: https://hermes-agent.nousresearch.com/docs

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Open your Hermes MCP config.
2. Add Breakcold under mcp_servers with the MCP server URL for your workspace region.
3. Set auth to oauth for user sign-in.
4. Restart Hermes or run /reload-mcp after saving the config.

Use OAuth when Hermes asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when Hermes cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

## Note

For bearer-token fallback, set an Authorization bearer header with a narrow Breakcold bearer key and only expose the tools the Hermes agent needs.
