# Agumbe Codex Plugin

Build, deploy, and operate Agumbe full-stack applications from Codex.

## Product Boundary

The plugin is a thin agent experience over the public Agumbe control plane. It
must not contain Kubernetes credentials, cloud credentials, GitHub installation
tokens, or provisioning business logic.

The plugin connects only to the remote Streamable HTTP MCP server at
`https://mcp.agumbe.dev/mcp`. The server wraps the existing Agumbe workload
APIs and preserves the intended public tool contract.

For MCP workspace creation, the server automatically creates and approves a
codegen blueprint before calling the workload API. The landing application
continues to use its explicit blueprint review and approval flow.

Authenticate through the MCP OAuth flow:

```bash
codex mcp logout agumbe
codex mcp login agumbe --scopes workspaces:read,workspaces:write
```

Start a fresh Codex process or thread after authorization. Existing sessions do
not dynamically acquire MCP tools that failed to initialize during startup.

Never paste an Agumbe session cookie or application token into this plugin.
Codex securely stores and refreshes the OAuth credentials used by the remote
MCP server.

If an Agumbe skill loads but its MCP tool is unavailable, do not let Codex
create a local substitute application. Reauthorize and restart Codex:

```bash
codex mcp logout agumbe
codex mcp login agumbe --scopes workspaces:read,workspaces:write
codex mcp get agumbe --json
```

## Included Skills

- `create-app`: provision a GitHub-backed Agumbe workspace
- `deploy-app`: deploy changes and watch the rollout
- `deployment-status`: inspect provisioning and deployment state
- `logs`: investigate runtime failures
- `environments`: create and inspect isolated environments
- `workspaces`: list and inspect tenant workspaces

## Available Tools

The remote MCP server exposes:

- `agumbe_create_workspace`
- `agumbe_list_workspaces`
- `agumbe_get_workspace`
- `agumbe_get_operation`

`agumbe_get_operation` currently synthesizes operation progress from the
existing namespace provisioning status API. OAuth-backed MCP authentication is
live.

See [references/control-plane-contract.md](references/control-plane-contract.md)
for the required MCP and HTTP contracts.
