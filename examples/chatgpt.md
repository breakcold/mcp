# ChatGPT

Connect Breakcold to ChatGPT apps or custom MCP apps.

Website: https://chatgpt.com

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Open ChatGPT settings and go to Apps.
2. Add or enable the Breakcold app for your workspace.
3. Use the MCP server URL when ChatGPT asks for the remote MCP endpoint.
4. Connect the app, then call Breakcold from the app picker or with an @ mention.

Use OAuth when ChatGPT asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when ChatGPT cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

## Note

Workspace admins may need to enable custom apps before members can connect Breakcold.
