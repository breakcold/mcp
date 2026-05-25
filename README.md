# Breakcold MCP

Use Breakcold as a remote Model Context Protocol server from AI tools such as Claude, ChatGPT, Codex, Openclaw, Hermes, TypingMind, LM Studio, LibreChat, and Microsoft 365 Copilot.

Through the hosted MCP server, agents can drive your CRM across multiple channels — email, LinkedIn, meetings, WhatsApp, and Telegram — by reading and writing people, companies, deals, tasks, notes, custom fields, and pipeline/Kanban views. The server also exposes your full multichannel inbox, so agents can act on real conversations: triage stalled threads, move deals forward on inbox signals, run workspace-aware reports, attach prospect research to records, detect and create contacts from inbound messages, and even bootstrap a full CRM from a website URL.

This repository contains public setup guides, examples, and generated metadata for the hosted Breakcold MCP server. It does not contain a standalone MCP server. Breakcold hosts the server at `/mcp/v1`.

## MCP endpoints

- United States: `https://http.us.breakcold.com/mcp/v1`
- Europe: `https://http.eu.breakcold.com/mcp/v1`

Choose the region where your Breakcold workspace is hosted

## Authentication

Use OAuth whenever your MCP client supports sign-in. OAuth calls run as the signed-in Breakcold user and enforce the user's organization, workspace, and record permissions.

Use bearer-token access only for clients that cannot complete OAuth or for service-style agents that should not act as a signed-in user.

For user sign-in applications managed by Breakcold, send `X-Breakcold-Organization-Id` unless the user belongs to exactly one active Breakcold organization.

## Applications

- [Claude](./examples/claude.md)
- [ChatGPT](./examples/chatgpt.md)
- [Codex](./examples/codex.md)
- [Openclaw](./examples/openclaw.md)
- [Hermes](./examples/hermes.md)
- [TypingMind](./examples/typingmind.md)
- [LM Studio](./examples/lm-studio.md)
- [LibreChat](./examples/librechat.md)
- [Microsoft 365 Copilot](./examples/microsoft-365-copilot.md)

## Skill

This repository also ships the [Breakcold CRM skill](./skills/breakcold-crm/SKILL.md), an AgentSkills-compatible playbook for AI agents operating Breakcold through MCP. It exposes 50+ tools that let an agent control the CRM across emails, LinkedIn, meetings, WhatsApp, and Telegram — reading and writing people, companies, deals, tasks, notes, custom fields, and pipeline/Kanban views, and reading the full multichannel inbox so the agent acts on real conversations.

The skill ships with six built-in workflows: (1) multichannel auto-tasks for stalled conversations, (2) pipeline auto-movement on inbox signals (never backward), (3) workspace-aware reports that adapt to sales, agency, recruiting, or community use, (4) prospect research notes written directly to records, (5) inbox contact detection with a strict 95% confidence rule, and (6) full CRM setup from a website URL.

Install it by copying [skills/breakcold-crm](./skills/breakcold-crm) into your agent's skills directory, then configure the Breakcold MCP endpoint for your region. See [Skill setup](./docs/skill.md).

## Generated files

- [generated/tools.json](./generated/tools.json) contains the public MCP tool metadata generated from the Breakcold public API contract.
- [generated/manifest.json](./generated/manifest.json) contains region, application, and documentation metadata.

## Documentation

- [Breakcold MCP docs](https://docs.breakcold.com/api/mcp)
- [Application setup hub](https://docs.breakcold.com/api/mcp-applications)
- [Tool reference](./docs/tools.md)
- [Authentication](./docs/auth.md)
- [Rate limits](./docs/rate-limits.md)
- [Skill setup](./docs/skill.md)

## Source of truth

This repository is generated from the private Breakcold application repository. Open changes against this repo for documentation and example feedback; runtime MCP behavior changes are made in Breakcold itself.
