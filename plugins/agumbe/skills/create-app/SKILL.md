---
name: create-app
description: Provision a new Agumbe full-stack application workspace. Use when the user asks to create, scaffold, provision, or start a new application on Agumbe.
---

# Create An Agumbe App

1. Inspect the current repository when it contains useful product or stack
   context.
2. Derive a short application/display name, description, product requirement,
   backend language, and frontend framework from the user's request. The
   product requirement should capture the users, core workflows, and essential
   entities. The control plane owns the canonical `customer-*` infrastructure
   slug.
3. Default to `typescript` and `nextjs` when the user provides no stack choice.
4. Call `agumbe_create_workspace` with `product_requirement` and a newly
   generated idempotency key. MCP automatically creates and approves the
   blueprint before provisioning.
5. Poll `agumbe_get_operation` until it succeeds or fails.
6. On success, report the workspace ID, repository URLs, and live URL when
   available.
7. On failure, report the failing operation step and a concrete remediation.

Never request Kubernetes credentials or cloud-provider credentials from the
user.
