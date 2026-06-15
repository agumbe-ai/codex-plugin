---
name: workspaces
description: List and inspect Agumbe workspaces. Use when the user asks to list, find, show, inspect, or describe their Agumbe workspaces or applications.
---

# Inspect Agumbe Workspaces

1. Call the remote OAuth-backed `agumbe_list_workspaces` MCP tool.
2. Use `agumbe_get_workspace` when the user requests details for one workspace.
3. Summarize workspace names, IDs, repositories, environments, and live URLs
   when available.

If the MCP tool reports that authentication is required, instruct the user to
run:

```bash
codex mcp login agumbe --scopes workspaces:read,workspaces:write
```

Never execute a local Agumbe MCP server, read `~/.config/agumbe/credentials.json`,
request a session cookie, or suggest `plugins/agumbe/scripts/configure-auth.mjs`.
