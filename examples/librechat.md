# LibreChat

Connect Breakcold to LibreChat workspaces that have MCP enabled.

Website: https://www.librechat.ai

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Open the MCP settings panel from LibreChat.
2. Click + to add a server, name it Breakcold, and choose Streamable HTTP.
3. Paste the MCP server URL.
4. Use API key authentication with Bearer format if LibreChat asks for credentials.

Use OAuth when LibreChat asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when LibreChat cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

