# Authentication

Breakcold MCP supports OAuth sign-in and bearer-token access. Use the method that matches how your MCP client runs.

## Choose an authentication method

Use OAuth when the MCP client supports sign-in and should act as the signed-in Breakcold user. This is the recommended option for user-facing clients such as Claude, ChatGPT, Codex, and Microsoft 365 Copilot.

Use a bearer token when the MCP client cannot complete OAuth, or when a service-style agent should use its own scoped access instead of acting as a signed-in user.

Both methods use the same MCP endpoint and the same Breakcold permission checks. Tool calls still enforce organization, workspace, scope, and record-level permissions.

## OAuth

OAuth calls run as the signed-in Breakcold user and enforce the same organization, workspace, and record permissions that user has in Breakcold.

Use the MCP endpoint for your workspace region:

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

Configure the `/mcp/v1` URL as a protected resource in your MCP client. Breakcold exposes OAuth metadata at the matching protected-resource URL:

~~~txt
/.well-known/oauth-protected-resource/mcp/v1
~~~

When the client starts sign-in, Breakcold sends the user through WorkOS AuthKit. After sign-in, MCP tool calls run as that Breakcold user.

## Bearer token

Bearer tokens are intended for MCP clients that ask for an API key, authorization token, bearer key, custom bearer token, or static header.

Create bearer-token access from Breakcold MCP settings or API key settings. Give the key only the scopes and workspaces the MCP client needs. Store the key in an environment variable or your MCP client's secret manager.

Send the key in the `Authorization` header:

~~~txt
Authorization: Bearer YOUR_API_KEY
~~~

Replace `YOUR_API_KEY` with the key copied from Breakcold. Keep the word `Bearer`, then add one space, then paste the key.

## Organization selection

For OAuth sign-in, Breakcold can select the organization automatically when the user belongs to exactly one active organization.

If the user belongs to more than one active Breakcold organization, MCP clients should send:

~~~txt
X-Breakcold-Organization-Id: YOUR_ORGANIZATION_ID
~~~

Bearer tokens are already tied to the organization and workspace limits chosen when the key was created.

## Examples

Generic header-based MCP client:

~~~txt
Authorization: Bearer YOUR_API_KEY
X-Breakcold-Organization-Id: YOUR_ORGANIZATION_ID
~~~

Codex with an environment variable:

~~~toml
[mcp_servers.breakcold]
url = "https://http.us.breakcold.com/mcp/v1"
bearer_token_env_var = "BREAKCOLD_MCP_TOKEN"
~~~

Direct Streamable HTTP request:

~~~sh
curl "https://http.us.breakcold.com/mcp/v1" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
~~~

Use the Europe URL instead if your Breakcold workspace is hosted in Europe.

## Common errors

`401 Unauthorized` usually means the token is missing, expired, revoked, copied incorrectly, or sent without the `Bearer ` prefix.

`403 Forbidden` usually means the token or signed-in user does not have the required scope, workspace access, organization membership, or MCP access is not enabled for the organization.

Wrong-region errors usually happen when the client uses the US endpoint for a Europe workspace, or the Europe endpoint for a US workspace.

Never commit bearer tokens to source control or paste them in public logs.
