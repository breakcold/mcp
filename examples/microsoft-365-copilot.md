# Microsoft 365 Copilot

Connect Breakcold through Microsoft 365 Copilot custom connectors.

Website: https://www.microsoft.com/microsoft-365/copilot

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Ask a Microsoft 365 admin to open the admin center and go to Copilot > Connectors.
2. Create a custom connector and choose Connect to MCP server.
3. Use the MCP server URL as the connector base URL.
4. Complete the OAuth setup so users can connect with their own Breakcold access.

Use OAuth when Microsoft 365 Copilot asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when Microsoft 365 Copilot cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

## Note

Microsoft custom federated connectors currently focus on read-only MCP tools, so validate the Breakcold tool set before publishing it broadly.
