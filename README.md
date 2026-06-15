# Agumbe Codex Plugin

Build, deploy, and operate production-ready Agumbe full-stack applications from
Codex.

This repository is the public Git marketplace source for the Agumbe Codex
plugin. The plugin bundles focused workflow skills and connects Codex to the
OAuth-protected remote MCP server at `https://mcp.agumbe.dev/mcp`.

## Install

Add the Agumbe marketplace and install the plugin:

```bash
codex plugin marketplace add agumbe-ai/codex-plugin --ref main
codex plugin add agumbe@agumbe
```

Start a fresh Codex thread after installation. Codex will prompt you to
authorize Agumbe when a tool first needs access.

For a deterministic first-time setup:

```bash
codex mcp logout agumbe
codex mcp login agumbe --scopes workspaces:read,workspaces:write
codex mcp get agumbe --json
```

After authorization, exit the current Codex process and start a fresh one.
Plugin-provided MCP tools are loaded when a Codex session starts. If OAuth
fails during startup, the Agumbe skills remain visible but the Agumbe tools are
not exposed to that session.

Agumbe skills must never replace a failed Agumbe MCP operation by scaffolding a
local application.

## Try It

```text
Create a TypeScript and Next.js Agumbe workspace named support-desk for
triaging customer support tickets with assignment, SLA tracking, and
escalation. Poll provisioning until it succeeds or fails.
```

## Repository Layout

```text
.agents/plugins/marketplace.json  Git marketplace catalog
plugins/agumbe/                   Installable Agumbe plugin
plugins/agumbe/.mcp.json          Remote MCP connection
plugins/agumbe/skills/            Agumbe workflows for Codex
```

## Validate

Use Codex's bundled plugin validator:

```bash
python3 /path/to/plugin-creator/scripts/validate_plugin.py plugins/agumbe
```

The plugin contains no Kubernetes credentials, cloud credentials, GitHub
installation tokens, session cookies, or application access tokens.

## License

Apache-2.0
