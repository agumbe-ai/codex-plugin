---
name: deploy-app
description: Deploy application changes to Agumbe. Use when the user asks to deploy, publish, release, or roll out an Agumbe application.
---

# Deploy An Agumbe App

1. Confirm the target workspace and environment from repository context or
   `agumbe_list_workspaces`.
2. Verify the requested changes are committed or identify the revision the user
   wants deployed.
3. Call `agumbe_deploy` with a newly generated idempotency key.
4. Poll `agumbe_get_operation`, then `agumbe_get_deployment_status`.
5. Do not call the deployment complete until all required components are
   healthy and synchronized.
6. Return the deployed revision, component status, and live URL.

Default the environment to `main` only when no safer context exists.
