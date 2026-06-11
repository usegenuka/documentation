---
title: "Inbox & Messaging"
description: "The Inbox domain provides a full omni-channel messaging stack. The central resource is a **Conversation**, which holds an ordered list of **Messages** exchanged"
sidebarTitle: "Inbox & Messaging"
---

The Inbox domain provides a full omni-channel messaging stack. The central resource is a **Conversation**, which holds an ordered list of **Messages** exchanged between customers and agents across channels (WhatsApp, webchat, Instagram, etc.). Supporting resources include **Labels** (conversation tags), **Reply Templates** (saved canned responses), **Sticker Packs**, **Inbox Agents** (API-driven automation bots with webhook delivery), and **Assignment Rules** (automatic agent routing).

Two distinct authentication surfaces exist: the standard `Authorization: Bearer <token>` flow used by human agents in the dashboard, and a dedicated **Inbox API Token** flow (`Authorization: InboxToken <token>`) used by external integrations and automation agents.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

> **Pagination note:** All list endpoints in this domain use **cursor-based pagination** instead of the standard offset pagination. The response wrapper replaces the `pagination` object with a `meta` / `links` cursor block from Laravel's cursor paginator. The `per_page` query parameter is honoured (default values vary per endpoint, commonly 25–50).

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/admin/inbox/agents` | List automation agents |
| `POST` | `/2023-11/admin/inbox/agents` | Create automation agent |
| `GET` | `/2023-11/admin/inbox/agents/available-events` | List subscribable webhook events |
| `GET` | `/2023-11/admin/inbox/agents/{agent}` | Get automation agent |
| `PATCH` | `/2023-11/admin/inbox/agents/{agent}` | Update automation agent |
| `DELETE` | `/2023-11/admin/inbox/agents/{agent}` | Delete automation agent |
| `GET` | `/2023-11/admin/inbox/agents/{agent}/logs` | List agent webhook/API logs |
| `GET` | `/2023-11/admin/inbox/agents/{agent}/logs/{log}` | Get single agent log entry |
| `POST` | `/2023-11/admin/inbox/agents/{agent}/regenerate-api-token` | Regenerate agent API token |
| `POST` | `/2023-11/admin/inbox/agents/{agent}/regenerate-secret` | Regenerate agent webhook secret |
| `GET` | `/2023-11/admin/inbox/agents/{agent}/stats` | Aggregated agent activity stats |
| `POST` | `/2023-11/admin/inbox/agents/{agent}/test-webhook` | Fire a test webhook |
| `GET` | `/2023-11/admin/inbox/assignment-rules` | List assignment rules |
| `POST` | `/2023-11/admin/inbox/assignment-rules` | Create assignment rule |
| `GET` | `/2023-11/admin/inbox/assignment-rules/{rule}` | Get assignment rule |
| `PATCH` | `/2023-11/admin/inbox/assignment-rules/{rule}` | Update assignment rule |
| `DELETE` | `/2023-11/admin/inbox/assignment-rules/{rule}` | Delete assignment rule |
| `GET` | `/2023-11/admin/inbox/company-settings` | Get inbox company settings |
| `PATCH` | `/2023-11/admin/inbox/company-settings` | Update inbox company settings |
| `GET` | `/2023-11/admin/inbox/conversations` | List conversations |
| `POST` | `/2023-11/admin/inbox/conversations` | Create conversation |
| `GET` | `/2023-11/admin/inbox/conversations/search` | Search conversations and messages |
| `GET` | `/2023-11/admin/inbox/conversations/unread-count` | Get unread conversation count |
| `GET` | `/2023-11/admin/inbox/conversations/{conversation}` | Get conversation |
| `PATCH` | `/2023-11/admin/inbox/conversations/{conversation}` | Update conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/archive` | Archive conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/assign` | Assign conversation to user/agent |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/labels` | Attach labels to conversation |
| `DELETE` | `/2023-11/admin/inbox/conversations/{conversation}/labels/{label}` | Detach label from conversation |
| `GET` | `/2023-11/admin/inbox/conversations/{conversation}/messages` | List messages in conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/messages` | Send message in conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/pin` | Pin conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/read` | Mark conversation as read |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/snooze` | Snooze conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/spam` | Mark conversation as spam |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/typing` | Send agent typing indicator |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/unarchive` | Unarchive conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/unpin` | Unpin conversation |
| `POST` | `/2023-11/admin/inbox/conversations/{conversation}/unsnooze` | Unsnooze conversation |
| `GET` | `/2023-11/admin/inbox/giphy/search` | Search GIFs via GIPHY |
| `GET` | `/2023-11/admin/inbox/giphy/trending` | Get trending GIFs via GIPHY |
| `GET` | `/2023-11/admin/inbox/labels` | List conversation labels |
| `POST` | `/2023-11/admin/inbox/labels` | Create label |
| `PATCH` | `/2023-11/admin/inbox/labels/{label}` | Update label |
| `DELETE` | `/2023-11/admin/inbox/labels/{label}` | Delete label |
| `PATCH` | `/2023-11/admin/inbox/messages/{message}` | Edit message |
| `DELETE` | `/2023-11/admin/inbox/messages/{message}` | Delete (revoke) message |
| `POST` | `/2023-11/admin/inbox/messages/{message}/reactions` | Add reaction to message |
| `DELETE` | `/2023-11/admin/inbox/messages/{message}/reactions` | Remove reaction from message |
| `POST` | `/2023-11/admin/inbox/messages/{message}/retry` | Retry failed message |
| `GET` | `/2023-11/admin/inbox/reply-templates` | List reply templates |
| `POST` | `/2023-11/admin/inbox/reply-templates` | Create reply template |
| `PATCH` | `/2023-11/admin/inbox/reply-templates/{template}` | Update reply template |
| `DELETE` | `/2023-11/admin/inbox/reply-templates/{template}` | Delete reply template |
| `GET` | `/2023-11/admin/inbox/sticker-packs` | List sticker packs |
| `POST` | `/2023-11/admin/inbox/sticker-packs` | Create sticker pack |
| `GET` | `/2023-11/admin/inbox/sticker-packs/{pack}` | Get sticker pack |
| `PATCH` | `/2023-11/admin/inbox/sticker-packs/{pack}` | Update sticker pack |
| `DELETE` | `/2023-11/admin/inbox/sticker-packs/{pack}` | Delete sticker pack |
| `POST` | `/2023-11/admin/inbox/sticker-packs/{pack}/stickers` | Add stickers to pack |
| `DELETE` | `/2023-11/admin/inbox/sticker-packs/{pack}/stickers/{media}` | Remove sticker from pack |
| `GET` | `/2023-11/inbox/chat/{conversation}/messages` | Public: list webchat messages |
| `POST` | `/2023-11/inbox/chat/{conversation}/messages` | Public: send webchat message |
| `POST` | `/2023-11/inbox/chat/{conversation}/read` | Public: mark conversation read |
| `POST` | `/2023-11/inbox/chat/{conversation}/request-unlock` | Public: request WhatsApp unlock link |
| `POST` | `/2023-11/inbox/chat/{conversation}/typing` | Public: customer typing indicator |
| `POST` | `/2023-11/inbox/conversations/{conversation}/messages` | API token: send message into existing conversation |
| `GET` | `/2023-11/inbox/me` | API token: get token info |
| `POST` | `/2023-11/inbox/messages` | API token: send message to recipient |

---

## Automation Agents

Inbox Agents are automation bots that can receive webhook events and send messages via a scoped API token. When an agent is created, both the `webhook_secret` and `api_token` (plain-text) are returned once in the response.

---

### `GET` `/2023-11/admin/inbox/agents`

List automation agents for the authenticated company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[is_active]` | boolean | No | — | Filter by active status |
| `filter[is_autonomous]` | boolean | No | — | Filter by autonomous mode |
| `filter[search]` | string | No | — | Search by name or description |
| `sort` | string | No | `-created_at` | Allowed: `name`, `created_at`, `updated_at`, `last_triggered_at`. Prefix with `-` for descending |
| `per_page` | integer | No | 50 | Results per page (cursor-based) |

**Request example**

```http
GET /2023-11/admin/inbox/agents?filter[is_active]=1&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Order Bot",
      "description": "Handles order confirmation messages",
      "logo_url": "https://cdn.genuka.com/agents/logo.png",
      "logo_thumb_url": "https://cdn.genuka.com/agents/logo_thumb.png",
      "webhook_url": "https://myapp.example.com/webhook",
      "webhook_events": ["message.received", "conversation.assigned"],
      "allowed_channels": ["whatsapp"],
      "allowed_connectables": [
        { "type": "WhatsappConnection", "id": "01hqydxwtxdj3kmzp3bz7jk73g" }
      ],
      "is_autonomous": false,
      "is_active": true,
      "failure_count": 0,
      "last_triggered_at": "2024-03-15T10:23:45.000000Z",
      "metadata": null,
      "api_tokens": [
        {
          "id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "name": "Order Bot (agent token)",
          "token_prefix": "gia_abc",
          "scopes": ["send_text", "send_template", "send_media", "upload_media"],
          "is_active": true,
          "last_used_at": null,
          "created_at": "2024-03-15T10:00:00.000000Z"
        }
      ],
      "created_at": "2024-03-15T10:00:00.000000Z",
      "updated_at": "2024-03-15T10:00:00.000000Z"
    }
  ]
}
```

**Error responses**

- `400` — Missing `X-Company` header (`company_id_required`)
- `401` — Invalid or expired token

---

### `POST` `/2023-11/admin/inbox/agents`

Create a new automation agent. The response includes `webhook_secret` and `api_token` in plain text — **these values are shown only once**. Store them securely.

- **Request body:** `multipart/form-data` or `application/json`

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:120 | Agent display name |
| `description` | string | No | max:2000 | Description |
| `logo` | file (image) | No | max:5 MB | Agent logo image |
| `webhook_url` | string (URL) | No | max:2048 | Endpoint to receive webhook events |
| `webhook_events` | array | No | items: see available events | Events to subscribe to. Empty = all events |
| `allowed_channels` | array | No | items: string max:30 | Channel IDs this agent may operate on. Empty = all channels |
| `allowed_connectables` | array | No | items: `{type, id}` | Specific connectable resources (e.g. WhatsApp connections). Empty = all |
| `allowed_connectables[].type` | string | Conditional | max:120 | Required when `id` present |
| `allowed_connectables[].id` | string | Conditional | max:26 | Required when `type` present |
| `is_autonomous` | boolean | No | — | Whether the agent operates without human oversight |
| `is_active` | boolean | No | default: true | Enable/disable the agent |
| `metadata` | object | No | — | Arbitrary key-value metadata |

**Request example**

```http
POST /2023-11/admin/inbox/agents HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "Order Bot",
  "description": "Handles order confirmation messages",
  "webhook_url": "https://myapp.example.com/webhook",
  "webhook_events": ["message.received", "conversation.assigned"],
  "allowed_channels": ["whatsapp"],
  "allowed_connectables": [
    { "type": "WhatsappConnection", "id": "01hqydxwtxdj3kmzp3bz7jk73g" }
  ],
  "is_autonomous": false,
  "is_active": true
}
```

**Success response — `200 OK`**

Same shape as the list item above, plus:

```json
{
  "data": {
    "...": "...",
    "webhook_secret": "abcdef1234567890abcdef1234567890abcdef12",
    "api_token": "gia_plainTextTokenShownOnce"
  }
}
```

**Error responses**

- `422` — Validation failure (e.g. invalid `webhook_events` value)

```json
{
  "message": "The webhook_events.0 field is invalid.",
  "errors": { "webhook_events.0": ["The selected webhook_events.0 is invalid."] },
  "code": 422
}
```

---

### `GET` `/2023-11/admin/inbox/agents/available-events`

Return the list of event strings an agent may subscribe to via `webhook_events`.

**Request example**

```http
GET /2023-11/admin/inbox/agents/available-events HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    "message.received",
    "message.status_updated",
    "conversation.created",
    "conversation.assigned",
    "conversation.updated"
  ]
}
```

---

### `GET` `/2023-11/admin/inbox/agents/{agent}`

Get a single automation agent.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

**Request example**

```http
GET /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Order Bot",
    "description": "Handles order confirmation messages",
    "logo_url": "https://cdn.genuka.com/agents/logo.png",
    "logo_thumb_url": "https://cdn.genuka.com/agents/logo_thumb.png",
    "webhook_url": "https://myapp.example.com/webhook",
    "webhook_events": ["message.received", "conversation.assigned"],
    "allowed_channels": ["whatsapp"],
    "allowed_connectables": [
      { "type": "WhatsappConnection", "id": "01hqydxwtxdj3kmzp3bz7jk73g" }
    ],
    "is_autonomous": false,
    "is_active": true,
    "failure_count": 0,
    "last_triggered_at": "2024-03-15T10:23:45.000000Z",
    "metadata": null,
    "api_tokens": [
      {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Order Bot (agent token)",
        "token_prefix": "gia_abc",
        "scopes": ["send_text", "send_template", "send_media", "upload_media"],
        "is_active": true,
        "last_used_at": null,
        "created_at": "2024-03-15T10:00:00.000000Z"
      }
    ],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-15T10:00:00.000000Z"
  }
}
```

**Error responses**

- `404` — Agent not found or does not belong to the company

---

### `PATCH` `/2023-11/admin/inbox/agents/{agent}`

Update an automation agent. After updating `allowed_channels` or `allowed_connectables`, any conversations where the agent is assigned but no longer has access are automatically unassigned.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

- **Request body:** `multipart/form-data` or `application/json` — all fields optional

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:120 | Agent display name |
| `description` | string | No | max:2000 | Description |
| `logo` | file (image) | No | max:5 MB | Agent logo image |
| `webhook_url` | string (URL) | No | max:2048 | Endpoint to receive webhook events |
| `webhook_events` | array | No | items: see available events | Events to subscribe to. Empty = all events |
| `allowed_channels` | array | No | items: string max:30 | Channel IDs this agent may operate on. Empty = all channels |
| `allowed_connectables` | array | No | items: `{type, id}` | Specific connectable resources (e.g. WhatsApp connections). Empty = all |
| `allowed_connectables[].type` | string | No | max:120 | Connectable type (e.g. `WhatsappConnection`) |
| `allowed_connectables[].id` | string | No | max:26 | Connectable ULID |
| `is_autonomous` | boolean | No | — | Whether the agent operates without human oversight |
| `is_active` | boolean | No | — | Enable/disable the agent |
| `metadata` | object | No | — | Arbitrary key-value metadata |

**Request example**

```http
PATCH /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "is_active": false
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Order Bot",
    "description": "Handles order confirmation messages",
    "logo_url": "https://cdn.genuka.com/agents/logo.png",
    "logo_thumb_url": "https://cdn.genuka.com/agents/logo_thumb.png",
    "webhook_url": "https://myapp.example.com/webhook",
    "webhook_events": ["message.received", "conversation.assigned"],
    "allowed_channels": ["whatsapp"],
    "allowed_connectables": [
      { "type": "WhatsappConnection", "id": "01hqydxwtxdj3kmzp3bz7jk73g" }
    ],
    "is_autonomous": false,
    "is_active": false,
    "failure_count": 0,
    "last_triggered_at": "2024-03-15T10:23:45.000000Z",
    "metadata": null,
    "api_tokens": [],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T08:00:00.000000Z"
  }
}
```

**Error responses**

- `404` — Agent not found
- `422` — Validation failure

---

### `DELETE` `/2023-11/admin/inbox/agents/{agent}`

Soft-delete an automation agent.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

**Request example**

```http
DELETE /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{ "success": true }
```

**Error responses**

- `404` — Agent not found

---

### `GET` `/2023-11/admin/inbox/agents/{agent}/logs`

List webhook delivery and inbound API call logs for an agent. Supports cursor-based pagination.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[direction]` | string | No | — | `inbound_api` or `outbound_webhook` |
| `filter[event_type]` | string | No | — | e.g. `message.received` |
| `filter[success]` | integer | No | — | `1` for successful, `0` for failed |
| `filter[status_code]` | integer | No | — | HTTP status code |
| `filter[conversation_id]` | ULID | No | — | Filter by conversation |
| `filter[since]` | ISO 8601 | No | — | Logs created at or after this timestamp |
| `filter[until]` | ISO 8601 | No | — | Logs created at or before this timestamp |
| `sort` | string | No | `-created_at` | Allowed: `created_at`, `duration_ms`, `status_code` |
| `per_page` | integer | No | 50 | Results per cursor page |

**Request example**

```http
GET /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g/logs?filter[success]=0&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "agent_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "direction": "outbound_webhook",
      "event_type": "message.received",
      "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "message_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "status_code": 200,
      "duration_ms": 142,
      "success": true,
      "error_message": null,
      "request_payload": { "event": "message.received" },
      "response_payload": null,
      "created_at": "2024-03-15T10:23:45.000000Z"
    }
  ]
}
```

---

### `GET` `/2023-11/admin/inbox/agents/{agent}/logs/{log}`

Get a single log entry including full request and response payloads.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |
| `log` | ULID | Log entry ID |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "agent_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "direction": "outbound_webhook",
    "event_type": "message.received",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "message_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "status_code": 200,
    "duration_ms": 142,
    "success": true,
    "error_message": null,
    "request_payload": { "event": "message.received" },
    "response_payload": null,
    "created_at": "2024-03-15T10:23:45.000000Z"
  }
}
```

---

### `POST` `/2023-11/admin/inbox/agents/{agent}/regenerate-api-token`

Revoke all existing API tokens for the agent and issue a new one. The plain token is returned **once** in `api_token`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

**Request example**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g/regenerate-api-token HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Order Bot",
    "description": "Handles order confirmation messages",
    "logo_url": "https://cdn.genuka.com/agents/logo.png",
    "logo_thumb_url": "https://cdn.genuka.com/agents/logo_thumb.png",
    "webhook_url": "https://myapp.example.com/webhook",
    "webhook_events": ["message.received", "conversation.assigned"],
    "allowed_channels": ["whatsapp"],
    "allowed_connectables": [
      { "type": "WhatsappConnection", "id": "01hqydxwtxdj3kmzp3bz7jk73g" }
    ],
    "is_autonomous": false,
    "is_active": true,
    "failure_count": 0,
    "last_triggered_at": "2024-03-15T10:23:45.000000Z",
    "metadata": null,
    "api_tokens": [
      {
        "id": "01jq2xwtxdj3kmzp3bz7jk99ab",
        "name": "Order Bot (agent token)",
        "token_prefix": "gia_xyz",
        "scopes": ["send_text", "send_template", "send_media", "upload_media"],
        "is_active": true,
        "last_used_at": null,
        "created_at": "2024-03-16T09:00:00.000000Z"
      }
    ],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z",
    "api_token": "gia_newPlainTextTokenShownOnce"
  }
}
```

---

### `POST` `/2023-11/admin/inbox/agents/{agent}/regenerate-secret`

Regenerate the webhook signing secret for an agent and reset the failure counter. The new secret is returned in `webhook_secret` **once**.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Order Bot",
    "description": "Handles order confirmation messages",
    "logo_url": "https://cdn.genuka.com/agents/logo.png",
    "logo_thumb_url": "https://cdn.genuka.com/agents/logo_thumb.png",
    "webhook_url": "https://myapp.example.com/webhook",
    "webhook_events": ["message.received", "conversation.assigned"],
    "allowed_channels": ["whatsapp"],
    "allowed_connectables": [
      { "type": "WhatsappConnection", "id": "01hqydxwtxdj3kmzp3bz7jk73g" }
    ],
    "is_autonomous": false,
    "is_active": true,
    "failure_count": 0,
    "last_triggered_at": "2024-03-15T10:23:45.000000Z",
    "metadata": null,
    "api_tokens": [],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T09:05:00.000000Z",
    "webhook_secret": "newRandomSecretStringOf40HexCharsHere00"
  }
}
```

---

### `GET` `/2023-11/admin/inbox/agents/{agent}/stats`

Aggregated activity statistics for an agent over a time period.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `period` | string | No | `7d` | `24h`, `7d`, or `30d` |

**Request example**

```http
GET /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g/stats?period=7d HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "period": "7d",
    "since": "2024-03-08T10:00:00.000000Z",
    "until": "2024-03-15T10:00:00.000000Z",
    "totals": {
      "total": 120,
      "successful": 115,
      "failed": 5,
      "success_rate": 95.83,
      "avg_duration_ms": 98,
      "max_duration_ms": 2340
    },
    "by_direction": { "outbound_webhook": 80, "inbound_api": 40 },
    "by_event": [
      { "event_type": "message.received", "count": 80 }
    ],
    "by_status_code": [
      { "status_code": 200, "count": 115 },
      { "status_code": 500, "count": 5 }
    ],
    "timeseries": [
      { "bucket": "2024-03-08", "count": 18, "successful": 18, "failed": 0 }
    ]
  }
}
```

---

### `POST` `/2023-11/admin/inbox/agents/{agent}/test-webhook`

Send a test payload to the agent's `webhook_url` to verify connectivity.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `agent` | ULID | Agent ID |

**Request body:** None — the test payload is fully server-generated. The server sends the following shape to the agent's `webhook_url`:

```json
{
  "event": "test",
  "agent_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "message": "This is a test webhook from Genuka Inbox Agents.",
  "timestamp": "2024-03-15T10:23:45.000000Z"
}
```

**Request example**

```http
POST /2023-11/admin/inbox/agents/01hqydxwtxdj3kmzp3bz7jk73g/test-webhook HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{ "message": "Webhook test sent successfully" }
```

**Error responses**

- `422` — Agent has no `webhook_url` configured

---

## Assignment Rules

Assignment rules automatically route incoming conversations to specific agents based on channel, connectable, customer tags, or conversation labels.

---

### `GET` `/2023-11/admin/inbox/assignment-rules`

List all assignment rules for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[is_active]` | boolean | No | — | Filter by active status |
| `filter[search]` | string | No | — | Search by name |
| `sort` | string | No | `-created_at` | Allowed: `name`, `created_at`, `updated_at` |
| `per_page` | integer | No | 50 | Results per cursor page |

**Request example**

```http
GET /2023-11/admin/inbox/assignment-rules HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "WhatsApp Orders Rule",
      "conditions": {
        "channel": "whatsapp",
        "customer_tags": ["VIP"],
        "conversation_labels": ["urgent"]
      },
      "agent_ids": ["01hqydxwtxdj3kmzp3bz7jk73g"],
      "is_active": true,
      "created_at": "2024-03-15T10:00:00.000000Z",
      "updated_at": "2024-03-15T10:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/inbox/assignment-rules`

Create an assignment rule. All referenced `agent_ids` must belong to the authenticated company.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:120 | Rule display name |
| `is_active` | boolean | No | — | Default: true |
| `conditions` | object | No | — | Routing conditions (see below) |
| `conditions.channel` | string | No | max:30 | Channel type to match (e.g. `whatsapp`) |
| `conditions.connectable` | object | No | — | Specific connectable to match |
| `conditions.connectable.type` | string | No | max:120 | e.g. `WhatsappConnection` |
| `conditions.connectable.id` | string | No | max:26 | Connectable ULID |
| `conditions.customer_tags` | array | No | items: string max:80 | Match conversations whose customer has these tags |
| `conditions.conversation_labels` | array | No | items: string max:80 | Match conversations with these label names |
| `agent_ids` | array | Yes | min:1, items: existing agent IDs | Agents to assign when rule matches |

**Request example**

```http
POST /2023-11/admin/inbox/assignment-rules HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "VIP WhatsApp Rule",
  "conditions": {
    "channel": "whatsapp",
    "customer_tags": ["VIP"]
  },
  "agent_ids": ["01hqydxwtxdj3kmzp3bz7jk73g"],
  "is_active": true
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq1xwtxdj3kmzp3bz7jk44qr",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "VIP WhatsApp Rule",
    "conditions": {
      "channel": "whatsapp",
      "customer_tags": ["VIP"],
      "conversation_labels": []
    },
    "agent_ids": ["01hqydxwtxdj3kmzp3bz7jk73g"],
    "is_active": true,
    "created_at": "2024-03-16T09:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

**Error responses**

- `422` — Validation failure or referenced agents do not belong to the company

---

### `GET` `/2023-11/admin/inbox/assignment-rules/{rule}`

Get a single assignment rule.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `rule` | ULID | Assignment rule ID |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "WhatsApp Orders Rule",
    "conditions": {
      "channel": "whatsapp",
      "customer_tags": ["VIP"],
      "conversation_labels": ["urgent"]
    },
    "agent_ids": ["01hqydxwtxdj3kmzp3bz7jk73g"],
    "is_active": true,
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-15T10:00:00.000000Z"
  }
}
```

---

### `PATCH` `/2023-11/admin/inbox/assignment-rules/{rule}`

Update an assignment rule. All fields are optional (`sometimes`).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `rule` | ULID | Assignment rule ID |

- **Request body:** same fields as create, all optional.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:120 | Rule display name |
| `is_active` | boolean | No | — | Enable/disable the rule |
| `conditions` | object | No | — | Routing conditions |
| `conditions.channel` | string | No | max:30 | Channel type to match |
| `conditions.connectable` | object | No | — | Specific connectable to match |
| `conditions.connectable.type` | string | No | max:120 | e.g. `WhatsappConnection` |
| `conditions.connectable.id` | string | No | max:26 | Connectable ULID |
| `conditions.customer_tags` | array | No | items: string max:80 | Customer tags to match |
| `conditions.conversation_labels` | array | No | items: string max:80 | Conversation label names to match |
| `agent_ids` | array | No | items: existing agent IDs | Agents to assign when rule matches |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "VIP WhatsApp Rule",
    "conditions": {
      "channel": "whatsapp",
      "customer_tags": ["VIP"],
      "conversation_labels": []
    },
    "agent_ids": ["01hqydxwtxdj3kmzp3bz7jk73g"],
    "is_active": true,
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T08:00:00.000000Z"
  }
}
```

---

### `DELETE` `/2023-11/admin/inbox/assignment-rules/{rule}`

Delete an assignment rule.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `rule` | ULID | Assignment rule ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

---

## Company Settings

---

### `GET` `/2023-11/admin/inbox/company-settings`

Get the inbox settings for the authenticated company.

**Request example**

```http
GET /2023-11/admin/inbox/company-settings HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "auto_unassign_agent_on_human_reply": false,
    "auto_assign_on_inactivity_enabled": false,
    "auto_assign_on_inactivity_minutes": 60,
    "updated_at": "2024-03-15T10:00:00.000000Z"
  }
}
```

---

### `PATCH` `/2023-11/admin/inbox/company-settings`

Update inbox company settings. All fields are optional.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `auto_unassign_agent_on_human_reply` | boolean | No | — | Unassign automation agent when a human agent replies |
| `auto_assign_on_inactivity_enabled` | boolean | No | — | Enable auto-assign after inactivity |
| `auto_assign_on_inactivity_minutes` | integer | No | min:1 max:1440 | Minutes of inactivity before auto-assign triggers |

**Request example**

```http
PATCH /2023-11/admin/inbox/company-settings HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "auto_unassign_agent_on_human_reply": true,
  "auto_assign_on_inactivity_enabled": true,
  "auto_assign_on_inactivity_minutes": 30
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "auto_unassign_agent_on_human_reply": true,
    "auto_assign_on_inactivity_enabled": true,
    "auto_assign_on_inactivity_minutes": 30,
    "updated_at": "2024-03-16T08:00:00.000000Z"
  }
}
```

---

## Conversations

---

### `GET` `/2023-11/admin/inbox/conversations`

List conversations for the company. By default excludes archived, spam, and snoozed conversations. Results are sorted by pinned status then most recent message.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[is_archived]` | boolean | No | `false` | Include archived conversations |
| `filter[is_pinned]` | boolean | No | — | Filter by pinned status |
| `filter[is_spam]` | boolean | No | `false` | Include spam conversations |
| `filter[customer_id]` | ULID | No | — | Filter by customer |
| `filter[channel]` | string | No | — | Filter by primary channel (e.g. `whatsapp`) |
| `filter[assigned]` | string | No | — | `me`, `unassigned`, or a user ULID |
| `filter[label]` | string | No | — | Filter by label name |
| `filter[label_id]` | ULID | No | — | Filter by label ID |
| `filter[snoozed]` | boolean | No | `false` | Include snoozed conversations |
| `filter[search]` | string | No | — | Search by subject, customer name, email, or phone |
| `filter[since]` | ISO 8601 | No | — | Incremental sync: conversations updated after this timestamp |
| `filter[ids]` | string/array | No | — | Comma-separated or array of conversation IDs |
| `filter[whatsapp_connection_id]` | ULID | No | — | Filter by WhatsApp connection |
| `filter[mentioned]` | string | No | — | `me` or a user ULID — conversations with an internal note mentioning this user |
| `filter[participated]` | string | No | — | `me` or a user ULID — conversations where this user sent a message |
| `filter[unread]` | boolean | No | — | Only conversations with unread customer messages for the current user |
| `sort` | string | No | `-is_pinned,-last_message_at` | Allowed: `last_message_at`, `created_at`, `updated_at`, `is_pinned`, `priority` |
| `include` | string | No | — | Comma-separated: `customer`, `assignedUser`, `assignedAgent`, `labels`, `channels`, `lastMessage` |
| `per_page` | integer | No | 25 | Results per cursor page |

**Request example**

```http
GET /2023-11/admin/inbox/conversations?filter[assigned]=me&filter[channel]=whatsapp&include=customer,labels,lastMessage HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shop_id": null,
      "customer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "assigned_user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "assigned_agent_id": null,
      "primary_channel": "whatsapp",
      "is_pinned": false,
      "is_archived": false,
      "is_spam": false,
      "snoozed_until": null,
      "subject": null,
      "last_message_at": "2024-03-15T10:23:45.000000Z",
      "last_message_preview": "Hello, how can I help?",
      "last_message": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "type": "text",
        "content": { "body": "Hello, how can I help?" },
        "sender_type": "user",
        "sender_id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "source_channel": "web",
        "status": "sent",
        "created_at": "2024-03-15T10:23:45.000000Z"
      },
      "metadata": null,
      "created_at": "2024-03-15T08:00:00.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "unread_count": 2,
      "customer": { "...": "(customer object, if included)" },
      "assigned_user": null,
      "assigned_agent": null,
      "labels": [],
      "channels": [
        {
          "channel_type": "whatsapp",
          "external_id": "+237612345678",
          "is_active": true,
          "metadata": {
            "connection_id": "01hqydxwtxdj3kmzp3bz7jk73g",
            "phone_number_id": "123456789",
            "display_phone_number": "+237699000000",
            "waba_id": "123456789",
            "custom_name": "Sales Line"
          }
        }
      ]
    }
  ]
}
```

---

### `POST` `/2023-11/admin/inbox/conversations`

Create a new agent-initiated conversation. For WhatsApp conversations, `whatsapp_connection_id` is required and a channel record is automatically created.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `customer_id` | ULID | Yes | exists:customers | Customer to open the conversation with |
| `primary_channel` | string | Yes | one of the InboxChannelType values | Channel to use (`whatsapp`, `web`, `instagram`, etc.) |
| `shop_id` | ULID | No | exists:shops | Scope to a specific shop |
| `subject` | string | No | max:255 | Conversation subject |
| `assigned_user_id` | ULID | No | exists:users | Assign to user; defaults to the authenticated user |
| `metadata` | object | No | — | Arbitrary metadata |
| `whatsapp_connection_id` | ULID | Conditional | required if `primary_channel=whatsapp` | WhatsApp connection to send from |

**Request example**

```http
POST /2023-11/admin/inbox/conversations HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "customer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "primary_channel": "whatsapp",
  "whatsapp_connection_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "subject": "Follow-up on order #1234"
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq3xwtxdj3kmzp3bz7jk55cd",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "shop_id": null,
    "customer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_agent_id": null,
    "primary_channel": "whatsapp",
    "is_pinned": false,
    "is_archived": false,
    "is_spam": false,
    "snoozed_until": null,
    "subject": "Follow-up on order #1234",
    "last_message_at": null,
    "last_message_preview": null,
    "last_message": null,
    "metadata": null,
    "created_at": "2024-03-16T09:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z",
    "unread_count": 0,
    "customer": null,
    "assigned_user": null,
    "assigned_agent": null,
    "labels": [],
    "channels": [
      {
        "channel_type": "whatsapp",
        "external_id": "+237612345678",
        "is_active": true,
        "metadata": {
          "connection_id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "phone_number_id": "123456789",
          "display_phone_number": "+237699000000",
          "waba_id": "123456789",
          "custom_name": "Sales Line"
        }
      }
    ]
  }
}
```

**Error responses**

- `422` — `customer_id` not found, invalid `primary_channel`, missing `whatsapp_connection_id` for WhatsApp

---

### `GET` `/2023-11/admin/inbox/conversations/search`

Full-text search across conversations (subject, customer info) and message content. Message body content is decrypted server-side.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `q` | string | Yes | — | Search query (minimum 2 characters) |
| `limit` | integer | No | 10 | Max results per category (max 25) |

**Request example**

```http
GET /2023-11/admin/inbox/conversations/search?q=order+1234&limit=5 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "conversations": [
    { "...": "(ConversationResource objects)" }
  ],
  "messages": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "type": "text",
      "content": { "body": "Your order 1234 has shipped." },
      "sender_type": "user",
      "sender_name": "Jane Agent",
      "created_at": "2024-03-15T10:23:45.000000Z",
      "conversation": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "subject": null,
        "primary_channel": "whatsapp",
        "customer": {
          "id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "first_name": "John",
          "last_name": "Doe",
          "phone": "+237612345678",
          "email": "john@example.com"
        }
      }
    }
  ]
}
```

---

### `GET` `/2023-11/admin/inbox/conversations/unread-count`

Return the total number of conversations with unread customer messages for the authenticated user.

**Request example**

```http
GET /2023-11/admin/inbox/conversations/unread-count HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{ "count": 7 }
```

---

### `GET` `/2023-11/admin/inbox/conversations/{conversation}`

Get a single conversation. Customer tags and related relations are eagerly loaded.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "shop_id": null,
    "customer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_agent_id": null,
    "primary_channel": "whatsapp",
    "is_pinned": false,
    "is_archived": false,
    "is_spam": false,
    "snoozed_until": null,
    "subject": null,
    "last_message_at": "2024-03-15T10:23:45.000000Z",
    "last_message_preview": "Hello, how can I help?",
    "metadata": null,
    "created_at": "2024-03-15T08:00:00.000000Z",
    "updated_at": "2024-03-15T10:23:45.000000Z",
    "unread_count": 0,
    "customer": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "first_name": "John",
      "last_name": "Doe",
      "email": "john@example.com",
      "phone": "+237612345678",
      "tags": ["VIP"]
    },
    "assigned_user": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Jane Agent",
      "avatar": "https://cdn.genuka.com/users/jane.jpg"
    },
    "assigned_agent": null,
    "labels": [
      {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "VIP",
        "color": "#F59E0B"
      }
    ],
    "channels": [
      {
        "channel_type": "whatsapp",
        "external_id": "+237612345678",
        "is_active": true,
        "metadata": {
          "connection_id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "phone_number_id": "123456789",
          "display_phone_number": "+237699000000",
          "waba_id": "123456789",
          "custom_name": "Sales Line"
        }
      }
    ],
    "last_message": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "type": "text",
      "content": { "body": "Hello, how can I help?" },
      "sender_type": "user",
      "sender_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "source_channel": "web",
      "status": "sent",
      "created_at": "2024-03-15T10:23:45.000000Z"
    }
  }
}
```

**Error responses**

- `404` — Conversation not found or does not belong to the company

---

### `PATCH` `/2023-11/admin/inbox/conversations/{conversation}`

Update conversation metadata. Broadcasts `InboxConversationUpdated` to other connected agents.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `subject` | string | No | max:255 | Conversation subject |
| `assigned_user_id` | ULID | No | exists:users | Assign to a user |
| `is_pinned` | boolean | No | — | Pin/unpin |
| `is_archived` | boolean | No | — | Archive/unarchive |
| `is_spam` | boolean | No | — | Mark/unmark as spam |
| `metadata` | object | No | — | Arbitrary metadata |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "shop_id": null,
    "customer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_agent_id": null,
    "primary_channel": "whatsapp",
    "is_pinned": true,
    "is_archived": false,
    "is_spam": false,
    "snoozed_until": null,
    "subject": "Updated subject",
    "last_message_at": "2024-03-15T10:23:45.000000Z",
    "last_message_preview": "Hello, how can I help?",
    "metadata": null,
    "created_at": "2024-03-15T08:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z",
    "unread_count": 0
  }
}
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/archive`

Archive a conversation.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/unarchive`

Unarchive a conversation.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/pin`

Pin a conversation.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/unpin`

Unpin a conversation.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/spam`

Mark a conversation as spam.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/snooze`

Snooze a conversation until a future date/time.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `until` | ISO 8601 | Yes | after:now | Snooze expiry datetime |

**Request example**

```http
POST /2023-11/admin/inbox/conversations/01hqydxwtxdj3kmzp3bz7jk73g/snooze HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "until": "2024-03-16T09:00:00Z"
}
```

**Success response — `200 OK`**

```json
{ "success": true }
```

**Error responses**

- `422` — `until` is not a future date

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/unsnooze`

Remove the snooze from a conversation.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/assign`

Assign a conversation to a human user and/or an automation agent. Sends an in-app notification to the newly assigned user and dispatches a `conversation.assigned` webhook to the newly assigned agent.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `user_id` | ULID | No | exists:users, must belong to company | Assign to this user (`null` to unassign) |
| `agent_id` | ULID | No | exists:inbox_agents, must belong to company | Assign to this agent (`null` to unassign) |

**Request example**

```http
POST /2023-11/admin/inbox/conversations/01hqydxwtxdj3kmzp3bz7jk73g/assign HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "shop_id": null,
    "customer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "assigned_agent_id": null,
    "primary_channel": "whatsapp",
    "is_pinned": false,
    "is_archived": false,
    "is_spam": false,
    "snoozed_until": null,
    "subject": null,
    "last_message_at": "2024-03-15T10:23:45.000000Z",
    "last_message_preview": "Hello, how can I help?",
    "metadata": null,
    "created_at": "2024-03-15T08:00:00.000000Z",
    "updated_at": "2024-03-16T09:05:00.000000Z",
    "unread_count": 0,
    "customer": null,
    "assigned_user": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Jane Agent",
      "avatar": "https://cdn.genuka.com/users/jane.jpg"
    },
    "assigned_agent": null,
    "labels": [],
    "channels": []
  }
}
```

**Error responses**

- `422` — Referenced user or agent does not belong to the company

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/labels`

Attach one or more labels to a conversation (additive, does not detach existing labels).

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `label_ids` | array | Yes | items: existing label IDs belonging to the company | Labels to attach |

**Request example**

```http
POST /2023-11/admin/inbox/conversations/01hqydxwtxdj3kmzp3bz7jk73g/labels HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "label_ids": ["01hqydxwtxdj3kmzp3bz7jk73g"]
}
```

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `DELETE` `/2023-11/admin/inbox/conversations/{conversation}/labels/{label}`

Detach a label from a conversation.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |
| `label` | ULID | Label ID to detach |

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/read`

Mark a conversation as read by the authenticated agent. Updates the per-user read cursor and dispatches WhatsApp read receipts asynchronously (blue checkmarks for the customer).

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/typing`

Broadcast an agent-is-typing indicator to the conversation channel (expires after 10 seconds). Also sends the typing event to the public webchat channel so the customer sees "typing…".

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

## Messages

---

### `GET` `/2023-11/admin/inbox/conversations/{conversation}/messages`

List messages in a conversation (cursor-based, oldest first by default). Internal notes are included for agents.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[type]` | string | No | — | Message type (e.g. `text`, `image`, `internal`) |
| `filter[sender_type]` | string | No | — | `user`, `customer`, `agent`, `system` |
| `filter[sender_id]` | ULID | No | — | Specific sender |
| `filter[source_channel]` | string | No | — | Channel the message arrived on |
| `filter[status]` | string | No | — | `pending`, `sent`, `delivered`, `read`, `failed` |
| `filter[since]` | ISO 8601 | No | — | Incremental sync: messages updated after this timestamp |
| `sort` | string | No | `created_at` | Allowed: `created_at`, `updated_at` |
| `include` | string | No | — | Comma-separated: `reactions`, `replyTo`, `sender`, `media` |
| `per_page` | integer | No | 50 | Results per cursor page |

**Request example**

```http
GET /2023-11/admin/inbox/conversations/01hqydxwtxdj3kmzp3bz7jk73g/messages?sort=-created_at&per_page=25 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "sender_type": "user",
      "sender_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "sender_name": "Jane Agent",
      "type": "text",
      "content": { "body": "Hello! How can I help you today?" },
      "source_channel": "web",
      "display_channel": null,
      "status": "sent",
      "reply_to_id": null,
      "external_id": "wamid.ABCD1234",
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "delivered_at": "2024-03-15T10:23:46.000000Z",
      "read_at": "2024-03-15T10:24:00.000000Z",
      "edited_at": null,
      "deleted_at": null,
      "media": [],
      "reactions": [
        {
          "emoji": "👍",
          "reactor_type": "customer",
          "reactor_id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "reactor_name": "John Doe"
        }
      ],
      "reply_to": null,
      "sender": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Jane Agent",
        "type": "user"
      },
      "read_by": [
        {
          "id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "name": "Bob Agent",
          "avatar": "https://cdn.genuka.com/users/bob.jpg"
        }
      ]
    }
  ]
}
```

---

### `POST` `/2023-11/admin/inbox/conversations/{conversation}/messages`

Send a message from the authenticated agent in a conversation. Supports text, internal notes, media uploads, templates, location, and more. Uses a 5-second deduplication window to prevent duplicate sends on rapid clicks.

This endpoint accepts `multipart/form-data` when uploading files, or `application/json` for text/template/structured messages. The `content` field may be sent as a JSON string in multipart requests.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `type` | string | Yes | one of InboxMessageType values | `text`, `internal`, `image`, `video`, `audio`, `voice_note`, `file`, `sticker`, `gif`, `template`, `location`, `contact`, `reaction`, `order`, `product`, `invoice`, `payment_request`, `button_message`, `list_message`, `poll` |
| `content` | object | Yes | — | Message content (shape varies by type — see below) |
| `reply_to_id` | ULID | No | exists:inbox_messages | ID of message being replied to |
| `caption` | string | No | max:1000 | Caption for media uploads |
| `attachments[]` | file | No | max:500 MB each | Image/video/file attachments (multipart) |
| `voice` | file | No | max:50 MB, mimes: ogg/mp3/wav/webm/opus | Voice note (multipart) |
| `documents[]` | file | No | max:500 MB each | Document attachments (multipart) |

**Content shapes by type:**

| Type | Required content fields | Optional content fields |
|---|---|---|
| `text` / `internal` | `body` (string, max:10000), `mentions[]` (user IDs for internal) | — |
| `image` / `video` / `audio` / `file` | — (upload via `attachments[]`) | `caption`, `filename`, `mime_type` |
| `sticker` | — (upload via `attachments[]`) | `url` |
| `gif` | `giphy_id` | `url`, `mp4_url` |
| `template` | `template_id` | `template_variables` (key-value map) |
| `location` | `latitude`, `longitude` | `name`, `address` |
| `contact` | `phone` | — |
| `reaction` | `emoji`, `target_message_id` | — |
| `order` | `order_id` | `order_number`, `total`, `currency`, `items[]` |
| `product` | `product_id` | `product_name`, `price`, `image_url` |
| `invoice` | `invoice_id` | `invoice_number` |
| `payment_request` | `amount` | `payment_url` |
| `button_message` | `body`, `buttons[]` (`id`, `text` max:20, max 3 buttons) | — |
| `list_message` | `body`, `button_text` (max:20), `sections[]` | — |
| `poll` | `question` (max:255), `options[]` (2–12 items, max:100 each) | `allow_multiple` |

**Request example (text message)**

```http
POST /2023-11/admin/inbox/conversations/01hqydxwtxdj3kmzp3bz7jk73g/messages HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "type": "text",
  "content": {
    "body": "Your order has shipped! Track it here: https://track.example.com"
  }
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq4xwtxdj3kmzp3bz7jk77ef",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_type": "user",
    "sender_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_name": "Jane Agent",
    "type": "text",
    "content": { "body": "Your order has shipped! Track it here: https://track.example.com" },
    "source_channel": "web",
    "display_channel": null,
    "status": "sent",
    "reply_to_id": null,
    "external_id": "wamid.ABCD1234",
    "created_at": "2024-03-15T10:30:00.000000Z",
    "updated_at": "2024-03-15T10:30:00.000000Z",
    "delivered_at": null,
    "read_at": null,
    "edited_at": null,
    "deleted_at": null,
    "media": [],
    "reactions": [],
    "reply_to": null,
    "sender": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Jane Agent",
      "type": "user"
    },
    "read_by": []
  }
}
```

**Error responses**

- `404` — Conversation not found or not in this company
- `422` — Validation failure (e.g. unapproved template, missing required fields)
- `500` — Media upload failure

---

### `PATCH` `/2023-11/admin/inbox/messages/{message}`

Edit the body of a text message. Only the message's sender may edit it. Also propagates the edit to WhatsApp if the conversation uses WhatsApp.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `message` | ULID | Message ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `content.body` | string | Yes | max:4096 | New message body |

**Request example**

```http
PATCH /2023-11/admin/inbox/messages/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "content": { "body": "Updated: Your order has shipped!" }
}
```

**Success response — `200 OK`**

```json
{
  "success": true,
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_type": "user",
    "sender_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_name": "Jane Agent",
    "type": "text",
    "content": { "body": "Updated: Your order has shipped!" },
    "source_channel": "web",
    "display_channel": null,
    "status": "sent",
    "reply_to_id": null,
    "external_id": "wamid.ABCD1234",
    "created_at": "2024-03-15T10:23:45.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z",
    "delivered_at": "2024-03-15T10:23:46.000000Z",
    "read_at": "2024-03-15T10:24:00.000000Z",
    "edited_at": "2024-03-16T09:00:00.000000Z",
    "deleted_at": null,
    "media": [],
    "reactions": [],
    "reply_to": null,
    "sender": null,
    "read_by": []
  }
}
```

**Error responses**

- `403` — Caller is not the message sender
- `422` — Message is not of type `text`

---

### `DELETE` `/2023-11/admin/inbox/messages/{message}`

Soft-delete (revoke) a message. Caller must be the sender or the message must be an internal note. Also propagates revocation to WhatsApp if applicable.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `message` | ULID | Message ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

**Error responses**

- `403` — Caller is not the sender and the message is not an internal note

---

### `POST` `/2023-11/admin/inbox/messages/{message}/reactions`

Add or update an emoji reaction from the authenticated agent. Also dispatches the reaction to WhatsApp.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `message` | ULID | Message ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `emoji` | string | Yes | max:10 | Emoji character(s) |

**Request example**

```http
POST /2023-11/admin/inbox/messages/01hqydxwtxdj3kmzp3bz7jk73g/reactions HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "emoji": "👍" }
```

**Success response — `200 OK`**

```json
{
  "success": true,
  "reaction": {
    "emoji": "👍",
    "reactor_type": "user",
    "reactor_id": "01hqydxwtxdj3kmzp3bz7jk73g"
  }
}
```

---

### `DELETE` `/2023-11/admin/inbox/messages/{message}/reactions`

Remove the authenticated agent's reaction from a message. Sends an empty emoji to WhatsApp to clear the reaction.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `message` | ULID | Message ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/messages/{message}/retry`

Retry sending a message that has `status = failed`. Resets delivery records and re-dispatches to the channel.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `message` | ULID | Message ID |

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_type": "user",
    "sender_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_name": "Jane Agent",
    "type": "text",
    "content": { "body": "Your order has shipped!" },
    "source_channel": "web",
    "display_channel": null,
    "status": "pending",
    "reply_to_id": null,
    "external_id": null,
    "created_at": "2024-03-15T10:23:45.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z",
    "delivered_at": null,
    "read_at": null,
    "edited_at": null,
    "deleted_at": null,
    "media": [],
    "reactions": [],
    "reply_to": null,
    "sender": null,
    "read_by": []
  }
}
```

**Error responses**

- `404` — Message not found, not in this company, or not in `failed` status

---

## Labels

Labels are coloured tags applied to conversations for categorisation and filtering.

---

### `GET` `/2023-11/admin/inbox/labels`

List all conversation labels for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Filter by name (partial match) |
| `filter[color]` | string | No | — | Filter by exact hex color |
| `filter[since]` | ISO 8601 | No | — | Incremental sync: labels updated after this timestamp |
| `filter[ids]` | string/array | No | — | Comma-separated or array of label IDs |
| `sort` | string | No | `sort_order,name` | Allowed: `sort_order`, `name`, `created_at`, `updated_at` |
| `per_page` | integer | No | 50 | Results per cursor page |

**Request example**

```http
GET /2023-11/admin/inbox/labels?sort=sort_order HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "VIP",
      "color": "#F59E0B",
      "sort_order": 0,
      "created_at": "2024-03-15T10:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/inbox/labels`

Create a label.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:50 | Label display name |
| `color` | string | No | regex `#RRGGBB`, default `#6B7280` | Hex colour |
| `sort_order` | integer | No | min:0, default 0 | Display order |

**Request example**

```http
POST /2023-11/admin/inbox/labels HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "name": "Urgent", "color": "#EF4444", "sort_order": 1 }
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq5xwtxdj3kmzp3bz7jk88gh",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Urgent",
    "color": "#EF4444",
    "sort_order": 1,
    "created_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

---

### `PATCH` `/2023-11/admin/inbox/labels/{label}`

Update a label. Uses the same form request as create (all fields optional when updating).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `label` | ULID | Label ID |

- **Request body:** all fields optional

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:50 | Label display name |
| `color` | string | No | regex `#RRGGBB` | Hex colour (e.g. `#EF4444`). Default `#6B7280` on create |
| `sort_order` | integer | No | min:0 | Display order |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Urgent",
    "color": "#EF4444",
    "sort_order": 2,
    "created_at": "2024-03-15T10:00:00.000000Z"
  }
}
```

---

### `DELETE` `/2023-11/admin/inbox/labels/{label}`

Delete a label.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `label` | ULID | Label ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

---

## Reply Templates

Reply templates are pre-written responses (with optional attachments) that agents can quickly insert. Templates may be shared company-wide or private to their creator.

---

### `GET` `/2023-11/admin/inbox/reply-templates`

List reply templates visible to the authenticated agent (shared templates + own private templates).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[category]` | string | No | — | Filter by category name |
| `filter[is_shared]` | boolean | No | — | Filter shared/private |
| `filter[created_by]` | ULID | No | — | Filter by creator |
| `filter[search]` | string | No | — | Search by title or shortcut |
| `filter[since]` | ISO 8601 | No | — | Incremental sync |
| `sort` | string | No | `sort_order,title` | Allowed: `sort_order`, `title`, `created_at`, `updated_at` |
| `include` | string | No | — | `creator` |
| `per_page` | integer | No | 50 | Results per cursor page |

**Request example**

```http
GET /2023-11/admin/inbox/reply-templates?filter[search]=hello&include=creator HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "title": "Welcome Message",
      "shortcut": "/welcome",
      "content": {
        "body": "Hello! Welcome to our support. How can we help you today?",
        "attachments": []
      },
      "category": "greeting",
      "is_shared": true,
      "sort_order": 0,
      "created_by": "01hqydxwtxdj3kmzp3bz7jk73g",
      "creator": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Jane Agent" },
      "created_at": "2024-03-15T10:00:00.000000Z",
      "updated_at": "2024-03-15T10:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/inbox/reply-templates`

Create a reply template.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | max:100 | Template title |
| `content` | object | Yes | — | Template content |
| `content.body` | string | Yes | max:4096 | Message body text |
| `content.attachments` | array | No | max:10 items | Pre-linked media attachments |
| `content.attachments[].type` | string | Conditional | `image`, `video`, `audio`, `document` | — |
| `content.attachments[].url` | string (URL) | Conditional | max:2048 | Media URL |
| `content.attachments[].filename` | string | No | max:255 | — |
| `content.attachments[].mime_type` | string | No | max:100 | — |
| `shortcut` | string | No | regex `/[a-z0-9_-]+` max:50 | Slash command shortcut e.g. `/welcome` |
| `category` | string | No | max:50 | Category name |
| `is_shared` | boolean | No | default: true | Visible to all agents in the company |
| `sort_order` | integer | No | min:0, default 0 | Display order |

**Request example**

```http
POST /2023-11/admin/inbox/reply-templates HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "title": "Shipping Confirmation",
  "shortcut": "/shipped",
  "content": {
    "body": "Your order has shipped and will arrive in 3-5 business days."
  },
  "category": "orders",
  "is_shared": true
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq6xwtxdj3kmzp3bz7jk99ij",
    "title": "Shipping Confirmation",
    "shortcut": "/shipped",
    "content": {
      "body": "Your order has shipped and will arrive in 3-5 business days.",
      "attachments": []
    },
    "category": "orders",
    "is_shared": true,
    "sort_order": 0,
    "created_by": "01hqydxwtxdj3kmzp3bz7jk73g",
    "creator": null,
    "created_at": "2024-03-16T09:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

---

### `PATCH` `/2023-11/admin/inbox/reply-templates/{template}`

Update a reply template. Only the creator or shared templates may be updated.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `template` | ULID | Reply template ID |

- **Request body:** all fields optional (same fields as create)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | max:100 | Template title |
| `content` | object | No | — | Template content object |
| `content.body` | string | Conditional | required if `content` present, max:4096 | Message body text |
| `content.attachments` | array | No | max:10 items | Pre-linked media attachments |
| `content.attachments[].type` | string | Conditional | `image`, `video`, `audio`, `document` | Required when attachment present |
| `content.attachments[].url` | string (URL) | Conditional | max:2048 | Required when attachment present |
| `content.attachments[].filename` | string | No | max:255 | — |
| `content.attachments[].mime_type` | string | No | max:100 | — |
| `shortcut` | string | No | regex `/[a-z0-9_-]+` max:50 | Slash command shortcut e.g. `/welcome` |
| `category` | string | No | max:50 | Category name |
| `is_shared` | boolean | No | — | Visible to all agents in the company |
| `sort_order` | integer | No | min:0 | Display order |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "title": "Shipping Confirmation (updated)",
    "shortcut": "/shipped",
    "content": {
      "body": "Your order has shipped and will arrive in 3-5 business days.",
      "attachments": []
    },
    "category": "orders",
    "is_shared": true,
    "sort_order": 1,
    "created_by": "01hqydxwtxdj3kmzp3bz7jk73g",
    "creator": null,
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

**Error responses**

- `404` — Template not found or access denied

---

### `DELETE` `/2023-11/admin/inbox/reply-templates/{template}`

Delete a reply template. Only the creator or shared templates may be deleted.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `template` | ULID | Reply template ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

---

## Sticker Packs

Sticker packs group small transparent images for use in conversations. Global packs (managed by Genuka) cannot be modified by company admins.

---

### `GET` `/2023-11/admin/inbox/sticker-packs`

List sticker packs available to the company (includes global packs).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[is_active]` | boolean | No | — | Filter by active status |
| `filter[search]` | string | No | — | Search by name |
| `sort` | string | No | `sort_order,name` | Allowed: `sort_order`, `name`, `created_at` |
| `per_page` | integer | No | 50 | Results per cursor page |

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "is_global": false,
      "name": "Fun Pack",
      "description": "Fun stickers for everyday use",
      "is_active": true,
      "sort_order": 0,
      "sticker_count": 12,
      "stickers": [
        {
          "id": 42,
          "url": "https://cdn.genuka.com/stickers/fun/01.png",
          "name": "thumbs-up",
          "file_name": "thumbs-up.png",
          "mime_type": "image/png",
          "size": 18432
        }
      ],
      "created_at": "2024-03-15T10:00:00.000000Z",
      "updated_at": "2024-03-15T10:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/inbox/sticker-packs`

Create a sticker pack. Stickers may be uploaded immediately via multipart form-data.

- **Request body:** `multipart/form-data`

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:100 | Pack name |
| `description` | string | No | max:500 | Description |
| `is_active` | boolean | No | default: true | Enable/disable |
| `sort_order` | integer | No | min:0, default 0 | Display order |
| `stickers[]` | file (image) | No | each max:5 MB | Initial sticker images |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq7xwtxdj3kmzp3bz7jk11kl",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "is_global": false,
    "name": "Fun Pack",
    "description": "Fun stickers for everyday use",
    "is_active": true,
    "sort_order": 0,
    "sticker_count": 0,
    "stickers": [],
    "created_at": "2024-03-16T09:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

---

### `GET` `/2023-11/admin/inbox/sticker-packs/{pack}`

Get a single sticker pack.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pack` | ULID | Sticker pack ID |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "is_global": false,
    "name": "Fun Pack",
    "description": "Fun stickers for everyday use",
    "is_active": true,
    "sort_order": 0,
    "sticker_count": 12,
    "stickers": [
      {
        "id": 42,
        "url": "https://cdn.genuka.com/stickers/fun/01.png",
        "name": "thumbs-up",
        "file_name": "thumbs-up.png",
        "mime_type": "image/png",
        "size": 18432
      }
    ],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-15T10:00:00.000000Z"
  }
}
```

---

### `PATCH` `/2023-11/admin/inbox/sticker-packs/{pack}`

Update a sticker pack's metadata. Global packs (`is_global = true`) cannot be modified.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pack` | ULID | Sticker pack ID |

- **Request body:** all optional

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:100 | — |
| `description` | string | No | max:500 | — |
| `is_active` | boolean | No | — | — |
| `sort_order` | integer | No | min:0 | — |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "is_global": false,
    "name": "Fun Pack",
    "description": "Updated description",
    "is_active": true,
    "sort_order": 1,
    "sticker_count": 12,
    "stickers": [],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

**Error responses**

- `403` — Pack is a global pack

---

### `DELETE` `/2023-11/admin/inbox/sticker-packs/{pack}`

Delete a sticker pack and all its stickers. Global packs cannot be deleted.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pack` | ULID | Sticker pack ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/admin/inbox/sticker-packs/{pack}/stickers`

Add stickers to an existing pack. Global packs cannot be modified.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pack` | ULID | Sticker pack ID |

- **Request body:** `multipart/form-data`

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `stickers[]` | file (image) | Yes | min:1, each max:5 MB | Sticker images to add |

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "is_global": false,
    "name": "Fun Pack",
    "description": "Fun stickers for everyday use",
    "is_active": true,
    "sort_order": 0,
    "sticker_count": 3,
    "stickers": [
      {
        "id": 43,
        "url": "https://cdn.genuka.com/stickers/fun/new1.png",
        "name": "new1",
        "file_name": "new1.png",
        "mime_type": "image/png",
        "size": 14200
      }
    ],
    "created_at": "2024-03-15T10:00:00.000000Z",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

---

### `DELETE` `/2023-11/admin/inbox/sticker-packs/{pack}/stickers/{media}`

Remove a single sticker from a pack. Global packs cannot be modified.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pack` | ULID | Sticker pack ID |
| `media` | integer | Media record ID |

**Success response — `200 OK`**

```json
{ "success": true }
```

**Error responses**

- `403` — Pack is global
- `404` — Sticker not found in this pack

---

## GIPHY

These endpoints proxy the GIPHY API and require a valid GIPHY API key to be configured server-side.

---

### `GET` `/2023-11/admin/inbox/giphy/search`

Search GIFs via GIPHY.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `q` | string | Yes | — | Search query (max:200) |
| `limit` | integer | No | 20 | Results per page (1–50) |
| `offset` | integer | No | 0 | Result offset |

**Request example**

```http
GET /2023-11/admin/inbox/giphy/search?q=thumbs+up&limit=10 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "xT9IgG50Fb7Mi0prBC",
      "title": "Thumbs Up",
      "preview_url": "https://media.giphy.com/..._ds.gif",
      "preview_width": 200,
      "preview_height": 100,
      "url": "https://media.giphy.com/.../giphy.gif",
      "mp4_url": "https://media.giphy.com/.../giphy.mp4",
      "width": 480,
      "height": 270
    }
  ],
  "pagination": { "total_count": 1200, "count": 10, "offset": 0 }
}
```

**Error responses**

- `422` — Missing required `q` parameter
- `503` — GIPHY API key not configured

---

### `GET` `/2023-11/admin/inbox/giphy/trending`

Get trending GIFs. Results are cached for 5 minutes.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `limit` | integer | No | 20 | Number of trending GIFs to return (1–50) |

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "3oEjHAUOqG3lSS0f1C",
      "title": "Excited",
      "preview_url": "https://media.giphy.com/...",
      "preview_width": 200,
      "preview_height": 100,
      "url": "https://media.giphy.com/.../giphy.gif",
      "mp4_url": "https://media.giphy.com/.../giphy.mp4",
      "width": 480,
      "height": 270
    }
  ]
}
```

---

## Public Webchat

These endpoints are used by the customer-facing webchat widget. They do **not** require the standard `Authorization: Bearer` or `X-Company` headers — the conversation ID acts as the identifier.

> Internal notes (type `internal`) are **never** returned to the customer.

---

### `GET` `/2023-11/inbox/chat/{conversation}/messages`

List messages for a webchat conversation. Returns messages in descending order (newest first).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | No | 50 | Results per cursor page |

**Success response — `200 OK`**

Messages in the standard `MessageResource` shape, with an additional `conversation` key in the response envelope:

```json
{
  "data": [ { "...": "(message objects, no internal type)" } ],
  "conversation": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "primary_channel": "whatsapp",
    "return_link": "https://wa.me/237699000000",
    "company": {
      "name": "Acme Corp",
      "logo": "https://cdn.genuka.com/companies/acme/logo.png"
    },
    "assigned_user": {
      "name": "Jane Agent"
    }
  }
}
```

---

### `POST` `/2023-11/inbox/chat/{conversation}/messages`

Send a message from the customer via webchat.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `type` | string | Yes | — | Message type (e.g. `text`). `internal` is forbidden |
| `content` | object | Yes | — | Message content |
| `content.body` | string | Conditional | required for `type=text`, max:5000 | Text body |

**Request example**

```http
POST /2023-11/inbox/chat/01hqydxwtxdj3kmzp3bz7jk73g/messages HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
Accept: application/json

{
  "type": "text",
  "content": { "body": "Hi, I need help with my order." }
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01jq8xwtxdj3kmzp3bz7jk22mn",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "sender_type": "customer",
    "sender_id": null,
    "sender_name": "John Doe",
    "type": "text",
    "content": { "body": "Hi, I need help with my order." },
    "source_channel": "web",
    "display_channel": null,
    "status": "sent",
    "reply_to_id": null,
    "external_id": null,
    "created_at": "2024-03-15T10:25:00.000000Z",
    "updated_at": "2024-03-15T10:25:00.000000Z",
    "delivered_at": null,
    "read_at": null,
    "edited_at": null,
    "deleted_at": null,
    "media": [],
    "reactions": [],
    "reply_to": null,
    "sender": null,
    "read_by": []
  }
}
```

**Error responses**

- `403` — Attempted to send an `internal` message type

---

### `POST` `/2023-11/inbox/chat/{conversation}/read`

Acknowledge that the customer has read the conversation. No per-customer read tracking is performed; this is a no-op acknowledgement.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

### `POST` `/2023-11/inbox/chat/{conversation}/request-unlock`

Request a WhatsApp unlock link. Returns a `wa.me` deep link the customer can tap to send the unlock code.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Conversation ID |

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{
  "message": "Envoyez le message de déblocage via WhatsApp.",
  "unlock_url": "https://wa.me/237699000000?text=UNLOCK%3A01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Error responses**

- `404` — Conversation not found

---

### `POST` `/2023-11/inbox/chat/{conversation}/typing`

Broadcast a customer-is-typing indicator to the conversation's agent-side channel.

**Request body:** None — no body required for this action endpoint.

**Success response — `200 OK`**

```json
{ "success": true }
```

---

## Inbox API (Agent / Integration Tokens)

These endpoints authenticate via a dedicated **Inbox API token** issued by the agent management flow. The token must be passed as `Authorization: InboxToken <token>`. The token carries scopes (`send_text`, `send_template`, `send_media`, `upload_media`) and allowed channel/connectable restrictions. Billable message types (e.g. WhatsApp template messages) debit one credit from the company wallet.

---

### `GET` `/2023-11/inbox/me`

Return information about the authenticated Inbox API token. Useful for credential health checks (n8n, Zapier, etc.).

**Request example**

```http
GET /2023-11/inbox/me HTTP/1.1
Host: api.genuka.com
Authorization: InboxToken gia_yourTokenHere
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "token": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Order Bot (agent token)",
      "prefix": "gia_abc",
      "scopes": ["send_text", "send_template", "send_media", "upload_media"],
      "allowed_channels": ["whatsapp"],
      "rate_limit": null,
      "last_used_at": "2024-03-15T10:23:45.000000Z",
      "expires_at": null
    },
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "agent_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "api_version": "2023-11"
  }
}
```

---

### `POST` `/2023-11/inbox/messages`

Send a message to a recipient. Automatically creates the conversation if one does not already exist for this recipient on the given channel.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `channel` | string | Yes | — | Channel to use (e.g. `whatsapp`) |
| `to` | string | Yes | — | Recipient address (e.g. phone number for WhatsApp) |
| `type` | string | Yes | one of InboxMessageType values | Message type |
| `content` | object | Yes | — | Message content (shape matches type) |
| `reply_to_id` | string | No | — | ID of message to reply to |
| `metadata` | object | No | — | Arbitrary metadata |

**Request example**

```http
POST /2023-11/inbox/messages HTTP/1.1
Host: api.genuka.com
Authorization: InboxToken gia_yourTokenHere
Content-Type: application/json
Accept: application/json

{
  "channel": "whatsapp",
  "to": "+237612345678",
  "type": "text",
  "content": { "body": "Hello! Your order #1234 has shipped." }
}
```

**Success response — `201 Created`**

```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "channel": "whatsapp",
    "type": "text",
    "status": "sent",
    "external_id": "wamid.ABCD1234",
    "was_converted": false,
    "original_type": null,
    "created_at": "2024-03-15T10:23:45.000000Z"
  }
}
```

**Error responses**

- `402` — Insufficient credits (`insufficient_credits`)
- `403` — Token not allowed to send on the requested channel (`channel_not_allowed`) or insufficient scope (`insufficient_scope`)
- `422` — Invalid recipient (`invalid_recipient`)
- `502` — Channel dispatch failed (`message_failed`)

---

### `POST` `/2023-11/inbox/conversations/{conversation}/messages`

Send a message into an existing conversation using an Inbox API token.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `conversation` | ULID | Existing conversation ID |

- **Request body:** same as `POST /2023-11/inbox/messages` minus `channel` and `to` (those are derived from the existing conversation).

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `type` | string | Yes | one of InboxMessageType values | Message type |
| `content` | object | Yes | — | Message content |
| `reply_to_id` | string | No | — | Message to reply to |
| `metadata` | object | No | — | Arbitrary metadata |

**Success response — `201 Created`**

```json
{
  "data": {
    "id": "01jq9xwtxdj3kmzp3bz7jk33op",
    "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "channel": "whatsapp",
    "type": "text",
    "status": "sent",
    "external_id": "wamid.EFGH5678",
    "was_converted": false,
    "original_type": null,
    "created_at": "2024-03-15T10:30:00.000000Z"
  }
}
```

**Error responses**

- `402` — Insufficient credits
- `403` — Channel not allowed for this token or insufficient scope
- `404` — Conversation not found for the token's company (`conversation_not_found`)
- `502` — Channel dispatch failed
