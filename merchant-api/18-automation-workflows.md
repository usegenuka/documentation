---
title: "Automation — Workflows"
description: "Workflows are automated multi-step processes that run in response to business events (triggers) or manual invocations. Each workflow has a mutable **draft graph"
sidebarTitle: "Automation & Workflows"
---

Workflows are automated multi-step processes that run in response to business events (triggers) or manual invocations. Each workflow has a mutable **draft graph** and one or more immutable **versions** that are actually executed. Runs are created when a workflow is triggered and execute each node (action / logic / trigger) in sequence; run history is preserved indefinitely for auditing and replay.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/workflows` | List workflows |
| POST | `/2023-11/admin/workflows` | Create a workflow |
| POST | `/2023-11/admin/workflows/http/test` | Test an outbound HTTP request |
| GET | `/2023-11/admin/workflows/node-types` | List all available node types |
| POST | `/2023-11/admin/workflows/nodes/simulate` | Simulate a single workflow node |
| GET | `/2023-11/admin/workflows/runs` | List workflow runs |
| POST | `/2023-11/admin/workflows/runs` | Manually trigger a workflow run |
| GET | `/2023-11/admin/workflows/runs/{run}` | Get a single workflow run |
| POST | `/2023-11/admin/workflows/runs/{run}/cancel` | Cancel a queued or running workflow run |
| POST | `/2023-11/admin/workflows/runs/{run}/replay` | Replay a finished workflow run |
| GET | `/2023-11/admin/workflows/samples` | Fetch a sample record for a model |
| GET | `/2023-11/admin/workflows/stats` | Get workflow and run statistics |
| POST | `/2023-11/admin/workflows/whatsapp/simulate` | Simulate an incoming WhatsApp message |
| GET | `/2023-11/admin/workflows/{workflow}` | Get a single workflow |
| PUT | `/2023-11/admin/workflows/{workflow}` | Update a workflow |
| DELETE | `/2023-11/admin/workflows/{workflow}` | Delete a workflow |
| POST | `/2023-11/admin/workflows/{workflow}/publish` | Publish a new workflow version |
| PATCH | `/2023-11/admin/workflows/{workflow}/toggle` | Toggle a workflow active/inactive |
| GET | `/2023-11/admin/workflows/{workflow}/versions` | List versions of a workflow |
| GET | `/2023-11/admin/workflows/{workflow}/versions/{version}` | Get a single workflow version |

---

## Workflows

### `GET` `/2023-11/admin/workflows`

List all workflows for the authenticated company. Supports filtering and search.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | no | 1 | Page number |
| `per_page` | integer | no | 15 | Results per page |
| `filter[id]` | string | no | — | Exact match on workflow ID |
| `filter[name]` | string | no | — | Partial match on workflow name |
| `filter[search]` | string | no | — | Permissive full-text search on `name` |

```http
GET /2023-11/admin/workflows?filter[search]=onboarding&per_page=10 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Customer Onboarding",
      "description": "Sends a welcome email after a customer registers.",
      "draft_graph_json": {
        "nodes": [],
        "edges": []
      },
      "is_active": true,
      "active_version": {
        "id": "01hqydxwtxdj3kmzp3bz7jk800",
        "revision": 3
      },
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-04-01T08:00:00.000000Z"
    }
  ],
  "links": {
    "first": "https://api.genuka.com/2023-11/admin/workflows?page=1",
    "last": "https://api.genuka.com/2023-11/admin/workflows?page=2",
    "prev": null,
    "next": "https://api.genuka.com/2023-11/admin/workflows?page=2"
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 2,
    "per_page": 15,
    "to": 15,
    "total": 22
  }
}
```

**Error responses**
- `401` — missing or invalid bearer token.
- `403` — authenticated user has no access to this company.

---

### `POST` `/2023-11/admin/workflows`

Create a new workflow with an optional draft graph.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | yes | max:255 | Human-readable workflow name |
| `description` | string | no | — | Optional longer description |
| `draft_graph_json` | object | no | array | Initial draft graph (`{ nodes: [], edges: [] }`) |

```http
POST /2023-11/admin/workflows HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Customer Onboarding",
  "description": "Sends a welcome email after a customer registers.",
  "draft_graph_json": {
    "nodes": [],
    "edges": []
  }
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Customer Onboarding",
  "description": "Sends a welcome email after a customer registers.",
  "draft_graph_json": {
    "nodes": [],
    "edges": []
  },
  "is_active": false,
  "active_version": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `401` — unauthenticated.
- `422` — validation failed (e.g. `name` missing).

---

### `GET` `/2023-11/admin/workflows/{workflow}`

Retrieve a single workflow including its active version summary.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |

```http
GET /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Customer Onboarding",
  "description": "Sends a welcome email after a customer registers.",
  "draft_graph_json": {
    "nodes": [
      { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} }
    ],
    "edges": []
  },
  "is_active": true,
  "active_version": {
    "id": "01hqydxwtxdj3kmzp3bz7jk800",
    "revision": 3
  },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-04-01T08:00:00.000000Z"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found or belongs to a different company.

---

### `PUT` `/2023-11/admin/workflows/{workflow}`

Update a workflow's name, description, or draft graph. All fields are optional (partial update).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | no | max:255 | New workflow name |
| `description` | string | no | — | New description (pass `null` to clear) |
| `draft_graph_json` | object | no | array | Replaced draft graph |

```http
PUT /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "draft_graph_json": {
    "nodes": [
      { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} },
      { "id": "node_2", "type": "action", "handler": "email.send", "params": { "template": "welcome" } }
    ],
    "edges": [
      { "from": "node_1", "to": "node_2" }
    ]
  }
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Customer Onboarding",
  "description": "Sends a welcome email after a customer registers.",
  "draft_graph_json": {
    "nodes": [
      { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} },
      { "id": "node_2", "type": "action", "handler": "email.send", "params": { "template": "welcome" } }
    ],
    "edges": [
      { "from": "node_1", "to": "node_2" }
    ]
  },
  "is_active": true,
  "active_version": {
    "id": "01hqydxwtxdj3kmzp3bz7jk800",
    "revision": 3
  },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-04-10T09:15:00.000000Z"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found.
- `422` — validation failed.

---

### `DELETE` `/2023-11/admin/workflows/{workflow}`

Soft-delete a workflow and all its associated data.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |

```http
DELETE /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`204 No Content`)

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found.

---

### `PATCH` `/2023-11/admin/workflows/{workflow}/toggle`

Toggle the `is_active` flag of a workflow. Inactive workflows will not fire on events.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |

**Request body:** None — no body required for this action endpoint.

```http
PATCH /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g/toggle HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Customer Onboarding",
  "description": "Sends a welcome email after a customer registers.",
  "draft_graph_json": {
    "nodes": [
      { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} },
      { "id": "node_2", "type": "action", "handler": "email.send", "params": { "template": "welcome" } }
    ],
    "edges": [
      { "from": "node_1", "to": "node_2" }
    ]
  },
  "is_active": false,
  "active_version": {
    "id": "01hqydxwtxdj3kmzp3bz7jk800",
    "revision": 3
  },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-04-10T09:15:00.000000Z"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found.

---

## Workflow Versions

### `GET` `/2023-11/admin/workflows/{workflow}/versions`

List all published versions of a workflow in descending revision order.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | no | 1 | Page number |
| `per_page` | integer | no | 20 | Results per page |

```http
GET /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g/versions HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk800",
      "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "revision": 3,
      "is_active": true,
      "message": "Added email confirmation step",
      "graph": {
        "nodes": [
          { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} }
        ],
        "edges": []
      },
      "created_at": "2024-04-01T08:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 20, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found.

---

### `POST` `/2023-11/admin/workflows/{workflow}/publish`

Snapshot the current draft as a new immutable version. By default the new version is immediately activated (becomes the version used for future runs).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `graph` | object | yes | array | The graph to publish (`{ nodes: [], edges: [] }`) |
| `message` | string | no | max:255 | Human-readable changelog message |
| `activate` | boolean | no | `true` | Whether to set this version as the active one |

```http
POST /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g/publish HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "graph": {
    "nodes": [
      { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} },
      { "id": "node_2", "type": "action", "handler": "email.send", "params": { "template": "welcome" } }
    ],
    "edges": [
      { "from": "node_1", "to": "node_2" }
    ]
  },
  "message": "Added email confirmation step",
  "activate": true
}
```

**Success response** (`200 OK`) — returns the parent workflow resource with the newly activated version:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Customer Onboarding",
  "description": "Sends a welcome email after a customer registers.",
  "draft_graph_json": { "nodes": [], "edges": [] },
  "is_active": true,
  "active_version": {
    "id": "01hqydxwtxdj3kmzp3bz7jk801",
    "revision": 4
  },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-04-10T09:15:00.000000Z"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found.
- `422` — `graph` missing.

---

### `GET` `/2023-11/admin/workflows/{workflow}/versions/{version}`

Retrieve a single version including the full immutable graph.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `workflow` | ULID | The workflow ID |
| `version` | ULID | The version ID |

```http
GET /2023-11/admin/workflows/01hqydxwtxdj3kmzp3bz7jk73g/versions/01hqydxwtxdj3kmzp3bz7jk800 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk800",
  "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "revision": 3,
  "is_active": true,
  "message": "Added email confirmation step",
  "graph": {
    "nodes": [
      { "id": "node_1", "type": "trigger", "handler": "order.created", "params": {} },
      { "id": "node_2", "type": "action", "handler": "email.send", "params": { "template": "welcome" } }
    ],
    "edges": [
      { "from": "node_1", "to": "node_2" }
    ]
  },
  "created_at": "2024-04-01T08:00:00.000000Z"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow or version not found, or version does not belong to the workflow.

---

## Workflow Runs

### `GET` `/2023-11/admin/workflows/runs`

List all workflow runs for the authenticated company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `workflow_id` | ULID | no | — | Filter runs to a specific workflow |
| `page` | integer | no | 1 | Page number |
| `per_page` | integer | no | 20 | Results per page |

```http
GET /2023-11/admin/workflows/runs?workflow_id=01hqydxwtxdj3kmzp3bz7jk73g&per_page=20 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk900",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "workflow_version_id": "01hqydxwtxdj3kmzp3bz7jk800",
      "status": "success",
      "trigger_type": "order.created",
      "context_json": { "order_id": "01hqydxwtxdj3kmzp3bz7jk111" },
      "started_by": null,
      "started_at": "2024-04-10T09:00:00.000000Z",
      "finished_at": "2024-04-10T09:00:03.000000Z",
      "queue": "default",
      "workflow": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Customer Onboarding" }
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 20, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — unauthenticated.

---

### `POST` `/2023-11/admin/workflows/runs`

Manually trigger a workflow run. The workflow must be active and have an active version.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `workflow_id` | ULID | yes | exists:workflows,id | The workflow to run |
| `workflow_version_id` | ULID | no | exists:workflow_versions,id | Override which version to run; defaults to the active version |
| `trigger_type` | string | no | — | Label for the trigger source; defaults to `"manual"` |
| `context` | object | no | array | Arbitrary key-value context passed to the workflow execution |
| `queue` | string | no | — | Override the queue name; defaults to the application's default queue |

```http
POST /2023-11/admin/workflows/runs HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "trigger_type": "manual",
  "context": {
    "order_id": "01hqydxwtxdj3kmzp3bz7jk111"
  }
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk901",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "workflow_version_id": "01hqydxwtxdj3kmzp3bz7jk800",
  "status": "queued",
  "trigger_type": "manual",
  "context_json": { "order_id": "01hqydxwtxdj3kmzp3bz7jk111" },
  "started_by": "01hqydxwtxdj3kmzp3bz7jkusr",
  "started_at": null,
  "finished_at": null,
  "queue": "default"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — workflow not found.
- `422` — workflow is inactive (`"This workflow is not active."`), or no active version exists (`"No active workflow version to run."`), or validation failed.

---

### `GET` `/2023-11/admin/workflows/runs/{run}`

Retrieve full details of a single workflow run, including per-node execution records.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `run` | ULID | The run ID |

```http
GET /2023-11/admin/workflows/runs/01hqydxwtxdj3kmzp3bz7jk901 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk901",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "workflow_version_id": "01hqydxwtxdj3kmzp3bz7jk800",
  "status": "success",
  "trigger_type": "manual",
  "context_json": { "order_id": "01hqydxwtxdj3kmzp3bz7jk111" },
  "started_by": "01hqydxwtxdj3kmzp3bz7jkusr",
  "started_at": "2024-04-10T09:00:00.000000Z",
  "finished_at": "2024-04-10T09:00:03.000000Z",
  "queue": "default",
  "workflow": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Customer Onboarding" },
  "version": { "id": "01hqydxwtxdj3kmzp3bz7jk800", "revision": 3 },
  "nodes": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jkn01",
      "workflow_run_id": "01hqydxwtxdj3kmzp3bz7jk901",
      "node_id": "node_1",
      "type": "trigger",
      "handler": "order.created",
      "status": "success",
      "input_json": {},
      "output_json": { "order_id": "01hqydxwtxdj3kmzp3bz7jk111" },
      "logs_text": null,
      "started_at": "2024-04-10T09:00:00.000000Z",
      "finished_at": "2024-04-10T09:00:01.000000Z",
      "attempts": 1
    }
  ]
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — run not found or belongs to a different company.

---

### `POST` `/2023-11/admin/workflows/runs/{run}/cancel`

Cancel a run that is in `queued` or `running` status. Also marks any queued/running nodes as `canceled`.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `run` | ULID | The run ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/workflows/runs/01hqydxwtxdj3kmzp3bz7jk901/cancel HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`204 No Content`)

**Error responses**
- `401` — unauthenticated.
- `404` — run not found or belongs to a different company.
- `422` — run is already finished (`{ "message": "Cannot cancel" }`).

---

### `POST` `/2023-11/admin/workflows/runs/{run}/replay`

Create a new run that re-uses the context of a finished run. The workflow must still be active. The new run uses the workflow's current active version.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `run` | ULID | The completed run ID to replay |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/workflows/runs/01hqydxwtxdj3kmzp3bz7jk901/replay HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk902",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "workflow_version_id": "01hqydxwtxdj3kmzp3bz7jk800",
  "status": "queued",
  "trigger_type": "manual",
  "context_json": { "order_id": "01hqydxwtxdj3kmzp3bz7jk111" },
  "started_by": "01hqydxwtxdj3kmzp3bz7jkusr",
  "started_at": null,
  "finished_at": null,
  "queue": "default"
}
```

**Error responses**
- `401` — unauthenticated.
- `404` — run not found or belongs to a different company.
- `422` — run is not yet finished (status must be `success`, `failed`, or `canceled`); or the parent workflow is inactive; or no active version exists.

---

## Node Types

### `GET` `/2023-11/admin/workflows/node-types`

Return all registered node type definitions. Used by the workflow editor to populate the node palette. This endpoint does not require `X-Company` in practice but the standard auth headers are still expected.

```http
GET /2023-11/admin/workflows/node-types HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "handler": "order.created",
    "label": "Order Created",
    "type": "trigger",
    "paramsSchema": {},
    "inputs": [],
    "outputs": ["next"],
    "icon": "shopping-cart"
  },
  {
    "handler": "email.send",
    "label": "Send Email",
    "type": "action",
    "paramsSchema": {
      "template": { "type": "string", "label": "Template" }
    },
    "inputs": ["in"],
    "outputs": ["success", "error"],
    "icon": "mail"
  },
  {
    "handler": "if",
    "label": "Condition",
    "type": "logic",
    "paramsSchema": {
      "condition": { "type": "string", "label": "Expression" }
    },
    "inputs": ["in"],
    "outputs": ["true", "false"],
    "icon": "git-branch"
  }
]
```

**Error responses**
- `401` — unauthenticated.

---

## Utilities

### `GET` `/2023-11/admin/workflows/stats`

Return aggregate statistics for workflows and runs belonging to the authenticated company.

```http
GET /2023-11/admin/workflows/stats HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "workflows": {
    "total": 12
  },
  "runs": {
    "total": 340,
    "by_status": {
      "success": 298,
      "failed": 30,
      "canceled": 7,
      "running": 5
    },
    "by_trigger": {
      "order.created": 210,
      "manual": 85,
      "whatsapp.text_received": 45
    },
    "recent": [
      {
        "id": "01hqydxwtxdj3kmzp3bz7jk901",
        "workflow_id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "workflow_version_id": "01hqydxwtxdj3kmzp3bz7jk800",
        "status": "success",
        "trigger_type": "order.created",
        "started_at": "2024-04-10T09:00:00.000000Z",
        "finished_at": "2024-04-10T09:00:03.000000Z",
        "workflow": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Customer Onboarding" },
        "version": { "id": "01hqydxwtxdj3kmzp3bz7jk800", "revision": 3 }
      }
    ]
  }
}
```

**Error responses**
- `401` — unauthenticated.

---

### `GET` `/2023-11/admin/workflows/samples`

Fetch a sample record for a supported automation model. Used in the workflow editor to preview live data for context binding. If an `id` is supplied and not found, falls back to the most recent record of that model type.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `model` | string | yes | — | Model key (e.g. `order`, `customer`, `product`) — must be a key in `config('automation.models')` |
| `id` | ULID | no | — | Specific record ID to fetch; latest record returned if not found |

```http
GET /2023-11/admin/workflows/samples?model=order&id=01hqydxwtxdj3kmzp3bz7jk111 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "model": "order",
  "id": "01hqydxwtxdj3kmzp3bz7jk111",
  "sample": {
    "id": "01hqydxwtxdj3kmzp3bz7jk111",
    "status": "pending",
    "total": 25000,
    "created_at": "2024-04-01T08:00:00.000000Z"
  }
}
```

If `model` is not in the allowed list, returns an empty array `[]`.

**Error responses**
- `401` — unauthenticated.

---

### `POST` `/2023-11/admin/workflows/http/test`

Proxy an outbound HTTP request from the server, returning the response details. Used in the workflow editor to test HTTP action nodes before publishing.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `url` | string | yes | — | Target URL |
| `method` | string | yes | GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS | HTTP method |
| `timeout` | integer | no | 30 (seconds) | Request timeout in milliseconds (max 60000) |
| `headers` | array | no | — | Array of `{ key, value }` header pairs |
| `query` | array | no | — | Array of `{ key, value }` query parameter pairs |
| `body.mode` | string | no | `none` | Body mode: `none`, `json`, `form`, `multipart`, `raw` |
| `body.json` | string | no | — | JSON string body (used when `body.mode` is `json`) |
| `body.raw` | string | no | — | Raw body string (used when `body.mode` is `raw`) |
| `body.rawContentType` | string | no | `text/plain` | Content-Type for raw body |
| `body.form` | array | no | — | Array of `{ key, value }` form fields (used when `body.mode` is `form`) |
| `body.multipart` | array | no | — | Array of `{ name, value }` multipart parts (used when `body.mode` is `multipart`) |
| `authentication.type` | string | no | `none` | Auth type: `none`, `basic`, `bearer`, `apiKey` |
| `authentication.basic.username` | string | no | — | Basic auth username |
| `authentication.basic.password` | string | no | — | Basic auth password |
| `authentication.bearer.token` | string | no | — | Bearer token |
| `authentication.apiKey.placement` | string | no | `header` | Where to place the API key: `header` or `query` |
| `authentication.apiKey.name` | string | no | — | API key header/query parameter name |
| `authentication.apiKey.value` | string | no | — | API key value |

```http
POST /2023-11/admin/workflows/http/test HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "url": "https://httpbin.org/post",
  "method": "POST",
  "headers": [
    { "key": "X-Custom-Header", "value": "my-value" }
  ],
  "body": {
    "mode": "json",
    "json": "{\"hello\": \"world\"}"
  },
  "authentication": {
    "type": "bearer",
    "bearer": { "token": "my-api-token" }
  }
}
```

**Success response** (`200 OK`):
```json
{
  "status": 200,
  "statusText": "OK",
  "headers": {
    "Content-Type": ["application/json"]
  },
  "body": {
    "json": { "hello": "world" },
    "url": "https://httpbin.org/post"
  },
  "duration": 342
}
```

If the request itself fails to connect, a `400` is returned:
```json
{
  "status": 0,
  "statusText": "Request failed",
  "message": "cURL error 6: Could not resolve host: invalid.host"
}
```

**Error responses**
- `401` — unauthenticated.
- `400` — network error or connection failure (see `message` field).
- `422` — validation failed (e.g. invalid `method` value).

---

### `POST` `/2023-11/admin/workflows/nodes/simulate`

Execute a single workflow node handler in isolation and return its output. Useful for testing node configuration before running the full workflow.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `handler` | string | yes | — | Handler identifier (e.g. `"Customer.read"`, `"email.send"`) — must be registered in the node registry |
| `params` | object | no | array | Node configuration parameters (matches the node's `paramsSchema`) |
| `inputs` | object | no | array | Input data passed to the node |
| `context` | object | no | array | Execution context (e.g. `company_id` is injected automatically) |

```http
POST /2023-11/admin/workflows/nodes/simulate HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "handler": "Customer.read",
  "params": { "model": "Customer", "id": "01hqydxwtxdj3kmzp3bz7jk999" },
  "inputs": {},
  "context": {}
}
```

**Success response** (`200 OK`):
```json
{
  "success": true,
  "handler": "Customer.read",
  "output": {
    "id": "01hqydxwtxdj3kmzp3bz7jk999",
    "first_name": "Jean",
    "last_name": "Dupont",
    "email": "jean.dupont@example.com"
  }
}
```

**Error responses**
- `401` — unauthenticated.
- `422` — unknown handler: `{ "error": "Unknown handler: BadHandler.name" }`, or no company found for user.
- `400` — handler executed but raised an exception: `{ "success": false, "handler": "...", "error": "..." }`.

---

### `POST` `/2023-11/admin/workflows/whatsapp/simulate`

Fire simulated WhatsApp webhook events to test WhatsApp-triggered workflows without a real device. Dispatches `whatsapp.message_received` and a type-specific event (e.g. `whatsapp.text_received`) into the workflow trigger service.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `type` | string | yes | `text`, `image`, `video`, `audio`, `document`, `location`, `sticker`, `contacts`, `interactive`, `button`, `order`, `reaction` | Simulated message type |
| `from` | string | no | — | Sender phone number; defaults to `"237690000000"` |
| `body` | string | no | — | Text body of the message |
| `contact_name` | string | no | — | Simulated contact display name; defaults to `"Simulated User"` |
| `interactive_type` | string | no | `button_reply`, `list_reply`, `nfm_reply` | For `type=interactive` only — the interactive subtype |
| `emoji` | string | no | — | For `type=reaction` — the reaction emoji; defaults to `"👍"` |
| `with_referral` | boolean | no | — | Include a simulated ad referral object and fire `whatsapp.referral_received` |
| `with_context` | boolean | no | — | Include a quoted-message context object |

```http
POST /2023-11/admin/workflows/whatsapp/simulate HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "type": "text",
  "from": "237690123456",
  "body": "Hello, I need help with my order",
  "contact_name": "Test Customer"
}
```

**Success response** (`200 OK`):
```json
{
  "success": true,
  "events_fired": [
    "whatsapp.message_received",
    "whatsapp.text_received"
  ],
  "context": {
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "phone_number_id": "SIMULATED_PHONE_NUMBER_ID",
    "display_phone_number": "+237600000000",
    "from": "237690123456",
    "message_id": "wamid.SIMULATED_1712743200",
    "timestamp": "1712743200",
    "type": "text",
    "body": {
      "text": "Hello, I need help with my order",
      "preview_url": false
    },
    "message": {
      "from": "237690123456",
      "id": "wamid.SIMULATED_1712743200",
      "timestamp": "1712743200",
      "type": "text",
      "text": { "body": "Hello, I need help with my order" }
    },
    "contact": {
      "profile": { "name": "Test Customer" },
      "wa_id": "237690123456"
    },
    "context": null,
    "referral": null
  }
}
```

**Error responses**
- `401` — unauthenticated.
- `422` — invalid `type` value or no company found for user.
