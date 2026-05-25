# Claude

Connect Breakcold to Claude as a remote MCP server with OAuth.

Website: https://claude.ai

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Open Claude settings and go to Customize > Connectors.
2. Click +, choose Add custom connector, and name it Breakcold.
3. Paste the MCP server URL, add the connector, then connect or sign in when Claude asks.
4. In a chat, click + > Connectors and enable Breakcold for the conversation.

Use OAuth when Claude asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when Claude cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

