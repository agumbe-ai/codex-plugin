# Agumbe Codex Plugin Delivery Roadmap

## Goal

Allow a user to provision, build, deploy, inspect, and operate an Agumbe
full-stack application from Codex, then reuse the same control-plane integration
from Claude.

## Product Decision

The first release is provision-first and GitHub-backed:

1. An agent requests a new Agumbe workspace.
2. Agumbe provisions frontend, backend, and manifests repositories.
3. The agent edits those repositories.
4. Agumbe deploys the requested revision through the existing GitOps path.

Importing and deploying an arbitrary local repository is a later release. It
needs a separate source-ingestion and build-contract design.

## Architecture

```text
Codex plugin ----\
                  >---- Remote Agumbe MCP ---- Public Agumbe API
Claude plugin ---/                              |
                                                 v
                                      Durable operations service
                                                 |
                                   Workload API / XNamespace operator
                                                 |
                                       GitHub, Argo CD, Kubernetes
```

The plugins contain workflow guidance and presentation metadata. Authentication,
authorization, tenancy, provisioning, and deployment logic remain server-side.

## Milestone 1: Public Control API

Deliver:

- `/v1` API boundary at `api.agumbe.dev`
- OAuth authorization-code flow with PKCE
- scoped access tokens and revocation
- durable operation model
- `Idempotency-Key` support for mutations
- tenant-scoped workspace, environment, deployment, log, and deletion APIs
- OpenAPI document and contract tests

Exit criteria:

- A non-browser client can authenticate and create a workspace safely.
- Repeating the same create request cannot create duplicate resources.
- Every provisioning failure is visible through an operation record.

## Milestone 2: Provisioning Reliability

Deliver:

- move `CreateWorkspace` orchestration into a durable operation
- compensate or retry partial failures
- use server-side apply/create-or-patch for Kubernetes and Argo resources
- add structured `XNamespace.status.conditions`
- persist repository URLs, live URLs, revision, and failure reasons
- enforce unique workspace slug and tenant constraints
- make teardown observable and retryable

Exit criteria:

- Workspace creation, retry, and deletion pass repeatable end-to-end tests.
- No success response is returned while required resources failed silently.

## Milestone 3: Remote MCP Server

Deliver:

- Streamable HTTP MCP endpoint at `mcp.agumbe.dev/mcp`
- OAuth support
- tools defined in the plugin control-plane contract
- redacted structured logs
- audit trail for all tool calls
- rate limits and per-tenant quotas

Exit criteria:

- MCP conformance tests pass.
- An authenticated client can create, inspect, deploy, troubleshoot, and delete
  a workspace using only MCP tools.

## Milestone 4: Codex Plugin Beta

Deliver:

- validate and install the repo-scoped Agumbe plugin
- end-to-end beta test with disposable tenants
- onboarding and troubleshooting documentation
- plugin telemetry that records tool outcomes without source code or secrets
- submission package for wider Codex distribution

Exit criteria:

- A new user can install, authenticate, create an app, edit it, deploy it, and
  receive a live URL without visiting the Agumbe builder.

## Milestone 5: Claude Plugin

Deliver:

- Claude plugin manifest and marketplace packaging
- reuse the same skills and remote MCP endpoint
- Claude-specific installation and authentication testing

Exit criteria:

- Codex and Claude produce equivalent control-plane operations from the same
  user intent.

## Security Gates Before Public Launch

- Restrict Argo projects to tenant namespaces and repositories.
- Apply namespace resource quotas, limit ranges, network policies, and pod
  security controls.
- Never place long-lived cloud credentials in generated repositories.
- Sandbox builds and enforce image/source scanning.
- Add deployment, workspace, log, and spend quotas.
- Redact secrets from operation errors, notifications, and logs.
- Record tenant, actor, tool, request ID, and result for every mutation.

## Immediate Engineering Backlog

1. Define the durable operation collection and API handlers in workload.
2. Add idempotency middleware and unique Mongo indexes.
3. Add public bearer-token authentication alongside browser-cookie auth.
4. Refactor workspace creation into an operation runner.
5. Expand `XNamespace.status` to conditions and outputs.
6. Implement the remote MCP adapter against the public API.
7. Run the included Codex plugin against a staging MCP endpoint.
