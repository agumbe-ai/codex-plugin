---
name: environments
description: Create and inspect isolated Agumbe environments. Use when the user asks for preview, test, development, staging, or other non-main environments.
---

# Manage Agumbe Environments

1. Resolve the target workspace.
2. Use a short lowercase environment name.
3. Call `agumbe_create_environment` with a newly generated idempotency key.
4. Poll `agumbe_get_operation` until the environment is ready or failed.
5. Return the environment name, namespace abstraction, source branch, and live
   URL.

Do not expose raw Kubernetes namespace operations to the user.
