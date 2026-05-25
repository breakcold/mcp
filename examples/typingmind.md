# TypingMind

Connect Breakcold to TypingMind as a remote MCP server.

Website: https://www.typingmind.com

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Open TypingMind and go to the MCP Store or MCP settings.
2. Choose to add a remote MCP server and paste the MCP server URL.
3. When TypingMind asks for a connector, use TypingMind Cloud or your private connector.
4. Authenticate with Breakcold or provide a bearer key if TypingMind asks for an API key.

Use OAuth when TypingMind asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when TypingMind cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

