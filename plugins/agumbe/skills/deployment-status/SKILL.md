---
name: deployment-status
description: Inspect Agumbe provisioning and deployment status. Use when the user asks whether an app is ready, live, healthy, provisioning, or failing.
---

# Inspect Agumbe Status

1. Resolve the workspace with `agumbe_get_workspace` or
   `agumbe_list_workspaces`.
2. Check active durable operations with `agumbe_get_operation` when an
   operation ID is available.
3. Call `agumbe_get_deployment_status` for the target environment.
4. Summarize provisioning, sync, health, revision, and live URL separately.
5. If a component is unhealthy, identify the failed component and recommend
   checking its logs.

Do not describe a deployment as ready based only on repository creation or a
successful build.
