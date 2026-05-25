# Breakcold MCP

Use Breakcold as a remote Model Context Protocol server from AI tools such as Claude, ChatGPT, Codex, TypingMind, LM Studio, LibreChat, and Microsoft 365 Copilot.

This repository contains public setup guides, examples, and generated metadata for the hosted Breakcold MCP server. It does not contain a standalone MCP server. Breakcold hosts the server at `/mcp/v1`.

## MCP endpoints

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

Choose the region where your Breakcold workspace is hosted.

## Authentication

Use OAuth whenever your MCP client supports sign-in. OAuth calls run as the signed-in Breakcold user and enforce the user's organization, workspace, and record permissions.

Use bearer-token access only for clients that cannot complete OAuth or for service-style agents that should not act as a signed-in user.

For user sign-in applications managed by Breakcold, send `X-Breakcold-Organization-Id` unless the user belongs to exactly one active Breakcold organization.

## Applications

- [Claude](./examples/claude.md)
- [ChatGPT](./examples/chatgpt.md)
- [Codex](./examples/codex.md)
- [TypingMind](./examples/typingmind.md)
- [LM Studio](./examples/lm-studio.md)
- [LibreChat](./examples/librechat.md)
- [Microsoft 365 Copilot](./examples/microsoft-365-copilot.md)

## Generated files

- [generated/tools.json](./generated/tools.json) contains the public MCP tool metadata generated from the Breakcold public API contract.
- [generated/manifest.json](./generated/manifest.json) contains region, application, and documentation metadata.

## Documentation

- [Breakcold MCP docs](https://docs.breakcold.com/api/mcp)
- [Application setup hub](https://docs.breakcold.com/api/mcp-applications)
- [Tool reference](./docs/tools.md)
- [Authentication](./docs/auth.md)
- [Rate limits](./docs/rate-limits.md)

## Source of truth

This repository is generated from the private Breakcold application repository. Open changes against this repo for documentation and example feedback; runtime MCP behavior changes are made in Breakcold itself.
