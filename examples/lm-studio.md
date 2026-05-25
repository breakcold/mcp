# LM Studio

Use Breakcold tools from local models in LM Studio chat.

Website: https://lmstudio.ai

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

1. Open LM Studio and switch to the Program tab.
2. Click Install > Edit mcp.json.
3. Add Breakcold as a remote MCP server using the MCP server URL.
4. If prompted for headers, set Authorization to Bearer followed by a Breakcold bearer key.

Use OAuth when LM Studio asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when LM Studio cannot complete OAuth or when an agent should not act as a signed-in user.

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

