# Breakcold CRM Skill

The public repo includes an AgentSkills-compatible Breakcold CRM skill at [skills/breakcold-crm](../skills/breakcold-crm).

The skill contains:

- [SKILL.md](../skills/breakcold-crm/SKILL.md), the agent entry point.
- [references](../skills/breakcold-crm/references), workflow playbooks loaded only when needed.
- [assets](../skills/breakcold-crm/assets), Breakcold brand assets and the report template used by the reporting workflow.

## Install in Codex

Copy the skill folder into your Codex skills directory:

~~~bash
cp -R skills/breakcold-crm ~/.codex/skills/
~~~

Then configure the Breakcold MCP server:

~~~bash
codex mcp add breakcold --url https://http.us.breakcold.com/mcp/v1
codex mcp login breakcold
~~~

Use the Europe endpoint instead if your workspace is hosted in Europe:

~~~bash
codex mcp add breakcold --url https://http.eu.breakcold.com/mcp/v1
~~~

For bearer-token access, configure the server in `~/.codex/config.toml` and point it to an environment variable:

~~~toml
[mcp_servers.breakcold]
url = "https://http.us.breakcold.com/mcp/v1"
bearer_token_env_var = "BREAKCOLD_MCP_TOKEN"
~~~

## Install in other agents

Use the same folder for any agent that supports AgentSkills or filesystem-loaded instructions:

- Native skill loaders: upload or copy `skills/breakcold-crm`.
- IDE-style agents: point project rules at `skills/breakcold-crm/SKILL.md`.
- Chat-style agents: paste `SKILL.md` as the main instruction and attach the relevant reference files.

Always connect the Breakcold MCP endpoint for the user's workspace region before running the skill.
