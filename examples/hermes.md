# Hermes

Connect Breakcold to Hermes as a remote MCP server.

Website: [https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/docs)

## Endpoint

Use the MCP endpoint for the region where your Breakcold workspace is hosted.

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

## OAuth setup

Add Breakcold as an MCP server in your Hermes config:

```yaml
mcp_servers:
  breakcold:
    url: "https://http.us.breakcold.com/mcp/v1"
    auth: oauth
    tools:
      resources: false
      prompts: false
```

Use the Europe URL instead if your Breakcold workspace is hosted in Europe.

Restart Hermes or reload MCP:

```txt
/reload-mcp
```

Hermes opens an OAuth authorization flow on first connect and stores MCP tokens for future sessions.

Use OAuth when Hermes asks you to connect or sign in.

## Bearer-token fallback

Use bearer-token access only when Hermes cannot complete OAuth or when an agent should not act as a signed-in user.

Replace `auth: oauth` with an authorization header in your Hermes config:

```yaml
headers:
  Authorization: "Bearer <your Breakcold bearer key>"
```

Default bearer-token scopes for this setup:

- `records:read`

Store the key in an environment variable such as `BREAKCOLD_MCP_TOKEN` and configure your client to send it as an Authorization bearer token.

For sensitive CRM access, start with narrow permissions and only expose the Breakcold tools the Hermes agent needs.
