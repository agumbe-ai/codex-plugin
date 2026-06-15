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
