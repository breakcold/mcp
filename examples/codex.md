# Codex

Connect Breakcold to Codex for agent workflows that need CRM context.

Website: https://developers.openai.com/codex

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Add the Breakcold MCP server with `codex mcp add breakcold --url https://http.us.breakcold.com/mcp/v1`.
2. Authorize Codex with Breakcold using `codex mcp login breakcold` when OAuth is available.
3. Run `codex mcp list` to verify the server is configured.
4. Use the Europe MCP endpoint instead if your Breakcold workspace is hosted in Europe.

Use OAuth when Codex asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when Codex cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

## Note

For bearer-token access, configure `bearer_token_env_var = "BREAKCOLD_MCP_TOKEN"` in the Breakcold MCP server entry in your Codex config.
