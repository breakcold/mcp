# Deployment

The Breakcold MCP skill is **15 plain markdown files** in a folder, following the [AgentSkills](https://agentskills.io) format. Any agent that can read text can use it. **What differs per platform is how the skill gets loaded into the agent's context.**

Three loading patterns cover every platform:

1. **Native skill install** — the platform recognizes `.skill` files (or AgentSkills folders) as a first-class concept. Drop or install once, and the agent triggers the skill automatically when relevant. Currently: Claude apps, Claude Code, OpenClaw, Hermes Agent, Codex.
2. **Filesystem + rules** — the agent reads files from a project folder when its config / rules file tells it to. Most IDE-style agents.
3. **System prompt + knowledge files** — paste `SKILL.md` into the agent's instructions; upload reference files as knowledge / attached docs. Most chat-style platforms.

Below: one short section per platform, sorted by maturity of MCP / skill support.

---

## Tier 1 — Native AgentSkills support

These platforms understand `SKILL.md` + `references/` natively. **Easiest install. No glue prompts needed.**

### Claude (web, desktop, iOS, Android)

1. Open Claude.
2. Go to Settings → Skills → Add skill.
3. Upload `breakcold-crm.skill`.
4. Make sure the Breakcold MCP is connected (Settings → Connectors → Breakcold).
5. Done. The skill triggers automatically whenever you mention Breakcold or CRM-related work.

### Claude Code (CLI)

1. Place the `breakcold-crm/` folder in **one** of:
   - `~/.claude/skills/breakcold-crm/` (available everywhere)
   - `<project-root>/.claude/skills/breakcold-crm/` (project-local)
2. Configure the Breakcold MCP via `claude mcp add` or in your Claude Code settings.
3. Restart your session. The skill is now discoverable.

### OpenClaw

OpenClaw uses the AgentSkills format directly — the skill folder works as-is. **Three install paths**, pick the easiest one for you:

**A. From your local folder (recommended for first install):**

```bash
openclaw skills install ./path/to/breakcold-crm --as breakcold-crm
```

Or for all local agents on the machine:

```bash
openclaw skills install ./path/to/breakcold-crm --as breakcold-crm --global
```

**B. From a Git repo** (once you put the skill in version control):

```bash
openclaw skills install git:your-org/breakcold-crm-skill@main
```

**C. From ClawHub** (once published — see [ClawHub](https://clawhub.ai)):

```bash
openclaw skills install breakcold-crm
```

**D. Manual drop** — place the folder in any of the following (highest precedence first):

- `<workspace>/skills/breakcold-crm/`
- `<workspace>/.agents/skills/breakcold-crm/`
- `~/.agents/skills/breakcold-crm/`
- `~/.openclaw/skills/breakcold-crm/`

Then set up the Breakcold MCP in OpenClaw's configuration. The skills watcher auto-detects new skills; restart the session if it doesn't pick up immediately.

### Hermes Agent (Nous Research)

Hermes also uses the AgentSkills format and loads everything in `~/.hermes/skills/`. Three install paths:

**A. From your local folder:**

```bash
hermes skills install ./path/to/breakcold-crm
```

**B. From a Git repo or direct URL:**

```bash
hermes skills install https://your-host/breakcold-crm/SKILL.md --name breakcold-crm
```

**C. Manual drop:**

```bash
cp -r ./breakcold-crm ~/.hermes/skills/
```

The skill appears automatically — no registration needed. Configure the Breakcold MCP in `~/.hermes/config.yaml`:

```yaml
mcp_servers:
  breakcold:
    url: https://http.us.breakcold.com/mcp/v1   # or .eu. for EU workspaces
    # plus OAuth or bearer token per your auth setup
```

External skill directories (for shared team libraries) are supported via the `external_dirs` setting in `config.yaml`.

### Codex (OpenAI)

Codex has a native skills directory at `$CODEX_HOME/skills` (default: `~/.codex/skills/`) and supports the AgentSkills format. Two-step install:

**1. Drop the skill folder:**

```bash
cp -r ./breakcold-crm ~/.codex/skills/
```

If `$CODEX_HOME` is set to a non-default location, use that instead.

**2. Configure the Breakcold MCP server.** Per Breakcold's official Codex guide, the easiest path is the Codex CLI:

```bash
# US workspace
codex mcp add breakcold --url https://http.us.breakcold.com/mcp/v1

# OR Europe workspace
codex mcp add breakcold --url https://http.eu.breakcold.com/mcp/v1
```

If your Breakcold server uses OAuth, authorize Codex:

```bash
codex mcp login breakcold
```

Verify the server is registered:

```bash
codex mcp list
```

For bearer-token access instead of OAuth, configure the server in `~/.codex/config.toml`:

```toml
[mcp_servers.breakcold]
url = "https://http.us.breakcold.com/mcp/v1"
bearer_token_env_var = "BREAKCOLD_MCP_TOKEN"
```

Then set `BREAKCOLD_MCP_TOKEN` in your environment. Restart the Codex session — the skill triggers automatically when relevant.

---

## Tier 2 — IDE-style agents (filesystem + rules)

### Cursor

1. Place the `breakcold-crm/` folder anywhere inside your project (e.g., `.cursor/skills/breakcold-crm/`).
2. Add to `.cursorrules` (or `.cursor/rules.md`):

   ```
   When the user asks about Breakcold, their CRM, deals, pipeline,
   or any Breakcold-related workflow, read .cursor/skills/breakcold-crm/SKILL.md
   and follow its routing table to the right reference file.
   ```

3. Configure the Breakcold MCP in Cursor's MCP settings.
4. Restart Cursor.

### Windsurf

Same pattern as Cursor:

1. Place the folder under `.windsurf/skills/breakcold-crm/`.
2. Add a Windsurf rule pointing to `SKILL.md`.
3. Configure MCP in Windsurf settings.

### Cline (VSCode extension)

1. Place the folder in your workspace (e.g., `<workspace>/skills/breakcold-crm/`).
2. In Cline settings, configure the Breakcold MCP server URL.
3. Add a `.clinerules` file at workspace root:

   ```
   For any Breakcold-related task, first read skills/breakcold-crm/SKILL.md
   and follow its routing instructions before acting.
   ```

### Continue (VSCode / JetBrains)

1. Place the folder in your project.
2. In `~/.continue/config.json`, add an MCP server entry for Breakcold and reference the skill folder in your custom instructions.
3. Add a system message: "When working with Breakcold, read `skills/breakcold-crm/SKILL.md` first."

### Goose (Block)

1. Place the `breakcold-crm/` folder somewhere accessible (e.g., `~/.goose/skills/breakcold-crm/`).
2. Add the Breakcold MCP server in `~/.config/goose/config.yaml`.
3. In Goose's system prompt or recipe: "Reference `~/.goose/skills/breakcold-crm/SKILL.md` for any Breakcold work."

### OpenHands (formerly OpenDevin)

1. Mount the `breakcold-crm/` folder into the agent's workspace.
2. Configure MCP for Breakcold in `config.toml`.
3. Add to the agent profile: "For Breakcold tasks, follow `breakcold-crm/SKILL.md`."

---

## Tier 3 — Chat-style platforms (system prompt + knowledge)

These platforms don't natively load file folders. The pattern is: **paste `SKILL.md` into the system prompt; upload reference files as knowledge.**

### ChatGPT (Custom GPT)

1. Create a Custom GPT (chatgpt.com → Explore GPTs → Create).
2. In **Instructions**: paste the contents of `SKILL.md` in full. Add at the top:

   ```
   When you would read a file like "references/action-tasks.md", look up the
   matching file in your Knowledge (e.g., "action-tasks.md") and use its content.
   ```

3. In **Knowledge**: upload all files from `references/` (9 files).
4. Under **Capabilities**: enable Web Browsing (needed for Action 4 — prospect research).
5. Configure the Breakcold MCP via ChatGPT's MCP connector setup.

### Microsoft 365 Copilot

1. In Copilot Studio, create a new agent.
2. Add the Breakcold MCP server via the MCP connector interface.
3. Paste `SKILL.md` content into the agent's instructions.
4. Upload `references/` files as knowledge documents.

### TypingMind

1. Create a new Custom Agent / Chatbot.
2. Paste `SKILL.md` into the system prompt.
3. Add MCP connection for Breakcold (TypingMind supports MCP servers directly).
4. Attach reference files as knowledge documents or include them inline.

### LibreChat

1. In `librechat.yaml`, add the Breakcold MCP server under `mcpServers`.
2. Create a new preset / agent and paste `SKILL.md` into its system prompt.
3. For references: either inline them into the system prompt (longer prompt, fewer round-trips) or use LibreChat's RAG / file feature.

### LM Studio

1. In LM Studio's MCP settings, add the Breakcold MCP server.
2. Create a system prompt that includes `SKILL.md` content.
3. LM Studio doesn't have a built-in knowledge-file system — concatenate references into the system prompt, or use a wrapper agent that handles file access.

### Open WebUI (formerly Ollama WebUI)

1. In Open WebUI Settings → Tools / MCP, add the Breakcold MCP server.
2. Create a new Model or Modelfile with a system prompt containing `SKILL.md`.
3. For references: Open WebUI's "Documents" / RAG feature can index the `references/` folder so the agent retrieves them on demand. Alternatively, inline them.

---

## Tier 4 — Plain API integrations

If you're building a custom agent on top of an LLM API (Anthropic, OpenAI, Google, open-source), you have full control over context.

**Pattern A — load everything up front.**
Concatenate `SKILL.md` + all 9 reference files into the system prompt. Heaviest context, simplest implementation. Works for any model with a 200k+ context window.

**Pattern B — retrieve on demand (recommended).**
1. Put `SKILL.md` in the system prompt only.
2. When the agent's routing table determines which reference to use (e.g., "user asked about follow-up tasks → `action-tasks.md`"), have your orchestration layer inject that file's content into the next message's context.
3. The agent reads it and proceeds.

This keeps every conversation lean and matches how Claude's, OpenClaw's, and Hermes's native skill loaders behave.

**MCP wiring.** Whichever pattern you use, your agent loop needs to be able to call Breakcold MCP tools. The endpoint is:

- US workspaces: `https://http.us.breakcold.com/mcp/v1`
- EU workspaces: `https://http.eu.breakcold.com/mcp/v1`

Use OAuth or a Breakcold API key per `references/fundamentals.md § 2`.

---

## Other platforms

If your platform isn't listed and it supports MCP, one of the three patterns above applies. Decision tree:

- **Does it have a "skill" or "agent profile" concept that can load a folder?** → Tier 1 / Tier 2 pattern.
- **Does it have a "knowledge files" or "documents" feature alongside a system prompt?** → Tier 3 pattern.
- **Are you building it yourself?** → Tier 4 pattern.

If unsure, default to pasting `SKILL.md` into the system prompt and inlining the most relevant `references/*.md` for the workflow at hand. That works on virtually anything.

---

## Sanity checks after installing

After installing on any platform, verify with this prompt:

> What can you help me with in Breakcold?

A correctly loaded skill responds with the six headline workflows (auto follow-up tasks, pipeline auto-movement, sales reports, prospect research, inbox → CRM detection, CRM setup) in the user's language. If the agent says "I don't have information about Breakcold" or doesn't mention the six workflows, the skill isn't loaded correctly — re-check the install steps for your platform.

Deeper check: ask it to set up a follow-up task routine. A correctly loaded skill will reference `action-tasks.md` behavior — asking about momentum threshold, assignee, frequency, and recap channel in **one** message.

---

## A note on skill marketplaces

For wider distribution, you can publish this skill to:

- **ClawHub** ([clawhub.ai](https://clawhub.ai)) — OpenClaw's public registry. Use `clawhub sync --all` from the skill folder once you have ClawHub credentials.
- **Hermes Skills Hub** ([hermes-agent.nousresearch.com/docs/skills](https://hermes-agent.nousresearch.com/docs/skills/)) — Hermes's public registry.
- **Anthropic's Claude skill marketplace** — distribution path through Claude's skill ecosystem.

Same `breakcold-crm/` folder; different upload mechanisms. Keep the source of truth in one Git repo and publish to each.
