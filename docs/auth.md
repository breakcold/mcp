# Authentication

Breakcold MCP supports OAuth and bearer-token access.

## OAuth

OAuth is the recommended option when the client supports MCP sign-in. OAuth calls run as the signed-in Breakcold user and enforce the same organization, workspace, and record permissions that user has in Breakcold.

Use the MCP endpoint for your workspace region:

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

If the user belongs to more than one active Breakcold organization, clients should send `X-Breakcold-Organization-Id`.

## Bearer token

Bearer tokens are intended for MCP clients that cannot complete OAuth, or for service-style agents that should not act as a signed-in user.

Create bearer-token access from Breakcold MCP settings. Store the key in an environment variable and reference it from your MCP client configuration.

Never commit bearer tokens to source control.
