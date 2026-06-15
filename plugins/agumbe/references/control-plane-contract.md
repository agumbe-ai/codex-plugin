# Agumbe Public Control-Plane Contract

## Principles

- Agents use product concepts, not Kubernetes resources.
- Every mutation is asynchronous and idempotent.
- Every resource and operation is tenant-scoped.
- The MCP server is a thin adapter over a versioned HTTP API.
- Codex and Claude use the same MCP tools.
- Secrets are referenced or uploaded through dedicated secret flows; they are
  never returned by read APIs or tool results.

## Authentication

The remote MCP server must support OAuth authorization-code flow with PKCE.
Tokens must contain an Agumbe tenant, user identity, scopes, expiry, and token
identifier.

Initial scopes:

```text
workspaces:read
workspaces:write
deployments:read
deployments:write
logs:read
secrets:write
```

## MCP Tools

### `agumbe_create_workspace`

Creates a GitHub-backed workspace and its main environment.

Input:

```json
{
  "name": "expense-tracker",
  "description": "Track personal expenses and budgets",
  "backend_language": "go",
  "frontend_framework": "nextjs",
  "repository_mode": "agumbe_managed",
  "idempotency_key": "client-generated-uuid"
}
```

`name` is an application/display name. The control plane must generate or
preserve a canonical `customer-*` slug before deriving workspace, repository,
namespace, DNS, and Argo resource names. It must not use an arbitrary
application name as the repository prefix.

Output:

```json
{
  "workspace_id": "ws_123",
  "operation_id": "op_123",
  "status": "queued"
}
```

### `agumbe_list_workspaces`

Lists workspaces available to the authenticated tenant.

### `agumbe_get_workspace`

Returns workspace metadata, repositories, environments, and live URLs.

### `agumbe_deploy`

Requests deployment of a repository revision to an environment.

Input:

```json
{
  "workspace_id": "ws_123",
  "environment": "main",
  "revision": "git-sha-or-branch",
  "idempotency_key": "client-generated-uuid"
}
```

### `agumbe_get_operation`

Returns durable progress for provisioning, deployment, environment creation,
and deletion operations.

### `agumbe_get_deployment_status`

Returns component-level rollout, synchronization, health, revision, and live
URL information.

### `agumbe_get_logs`

Returns bounded, redacted logs for a workspace component and environment.

### `agumbe_create_environment`

Creates an isolated environment backed by the workspace manifests repository.

### `agumbe_set_secret_reference`

Creates or updates a named secret reference without returning secret values.

### `agumbe_delete_workspace`

Starts durable teardown and returns an operation ID.

## HTTP API

The MCP server should map tools onto a public API under:

```text
https://api.agumbe.dev/v1
```

Initial endpoints:

```text
POST   /workspaces
GET    /workspaces
GET    /workspaces/{workspace_id}
DELETE /workspaces/{workspace_id}
POST   /workspaces/{workspace_id}/environments
POST   /workspaces/{workspace_id}/deployments
GET    /workspaces/{workspace_id}/deployments/{deployment_id}
GET    /workspaces/{workspace_id}/logs
PUT    /workspaces/{workspace_id}/secret-references/{name}
GET    /operations/{operation_id}
```

All mutation endpoints must accept `Idempotency-Key`.

## Durable Operation Shape

```json
{
  "id": "op_123",
  "kind": "workspace.provision",
  "status": "queued",
  "resource_type": "workspace",
  "resource_id": "ws_123",
  "steps": [
    {
      "name": "github.repositories",
      "status": "pending"
    }
  ],
  "created_at": "2026-06-14T00:00:00Z",
  "updated_at": "2026-06-14T00:00:00Z"
}
```

Allowed operation states:

```text
queued
running
succeeded
failed
cancelling
cancelled
```

## Required Platform Changes

1. Add OAuth and scoped access-token validation at the public API boundary.
2. Move workspace provisioning into a durable orchestration operation.
3. Replace create-only Kubernetes writes with server-side apply.
4. Add structured conditions and outputs to `XNamespace.status`.
5. Restrict Argo projects to the tenant repository and namespace.
6. Add idempotency storage and unique resource constraints.
7. Publish an OpenAPI document and generate the MCP adapter from it where
   practical.
