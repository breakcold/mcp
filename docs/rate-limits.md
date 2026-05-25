# Rate Limits

MCP tool calls use the same protected Breakcold API behind the scenes. Each tool call counts separately.

Breakcold checks limits at several levels, including organization, workspace, actor, and route. A tool call is blocked when any one of those limits is full.

The default route limit exposed in the generated metadata is the per-tool per-organization limit. See [generated/tools.json](../generated/tools.json) for tool-level metadata.

When a request is rate limited, Breakcold returns HTTP `429`. MCP clients receive a JSON-RPC error with code `-32029` and message `Public API rate limit exceeded`. When possible, Breakcold also sends a `Retry-After` header in seconds.

Good client behavior:

- Wait before retrying a rate-limited tool call.
- Respect `Retry-After` when it is present.
- Avoid retrying many tool calls in parallel.
- Cache recent reads when it is safe to do so.
- Break large jobs into smaller batches.
