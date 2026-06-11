---
title: "Marketing — Campaigns, Templates & Notifications"
description: "The Marketing domain covers the full lifecycle of outbound messaging campaigns (WhatsApp, SMS, Email), the message templates those campaigns reference, storefro"
sidebarTitle: "Marketing & Campaigns"
---

The Marketing domain covers the full lifecycle of outbound messaging campaigns (WhatsApp, SMS, Email), the message templates those campaigns reference, storefront-theme templates, email and SMS sending connections, Web Push subscriptions, notification channels, and per-user notification preferences. Campaigns can target individual customers, tag-based segments, or the entire customer base and support template messages, free-form messages, and scheduled delivery.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/campaigns` | List campaigns |
| POST | `/2023-11/admin/campaigns` | Create a campaign |
| POST | `/2023-11/admin/campaigns/bulk-delete` | Bulk delete campaigns |
| GET | `/2023-11/admin/campaigns/{campaign}` | Get a campaign |
| PUT | `/2023-11/admin/campaigns/{campaign}` | Update a campaign |
| DELETE | `/2023-11/admin/campaigns/{campaign}` | Delete a campaign |
| POST | `/2023-11/admin/campaigns/{campaign}/cancel-scheduled` | Cancel a scheduled campaign |
| GET | `/2023-11/admin/campaigns/{campaign}/customers` | List campaign recipients |
| POST | `/2023-11/admin/campaigns/{campaign}/customers/export` | Export campaign recipients |
| GET | `/2023-11/admin/campaigns/{campaign}/customers/export/fields` | List exportable recipient fields |
| GET | `/2023-11/admin/campaigns/{campaign}/customers/{customer}` | Get recipient delivery detail |
| POST | `/2023-11/admin/campaigns/{campaign}/customers/{customer}/retry` | Retry message for one recipient |
| POST | `/2023-11/admin/campaigns/{campaign}/duplicate` | Duplicate a campaign |
| POST | `/2023-11/admin/campaigns/{campaign}/launch` | Launch a campaign |
| GET | `/2023-11/admin/campaigns/{campaign}/pre-launch` | Pre-launch cost estimate |
| GET | `/2023-11/admin/campaigns/{campaign}/pre-retry-failed` | Pre-retry-failed cost estimate |
| POST | `/2023-11/admin/campaigns/{campaign}/retry-failed` | Retry all failed recipients |
| GET | `/2023-11/admin/campaign-templates` | List campaign templates |
| POST | `/2023-11/admin/campaign-templates` | Create & submit a campaign template |
| POST | `/2023-11/admin/campaign-templates/bulk-delete` | Bulk delete campaign templates |
| GET | `/2023-11/admin/campaign-templates/{campaignTemplate}` | Get a campaign template |
| PUT | `/2023-11/admin/campaign-templates/{campaignTemplate}` | Update a campaign template |
| DELETE | `/2023-11/admin/campaign-templates/{campaignTemplate}` | Delete (soft-delete) a campaign template |
| GET | `/2023-11/admin/templates` | List storefront templates |
| POST | `/2023-11/admin/templates/apps/sync` | Sync external storefront template |
| GET | `/2023-11/admin/templates/{template}` | Get a storefront template |
| POST | `/2023-11/admin/templates/{template}` | Associate a storefront template with the company |
| GET | `/2023-11/admin/email-connections` | List email connections |
| POST | `/2023-11/admin/email-connections` | Create an email connection |
| PUT | `/2023-11/admin/email-connections/{emailConnection}` | Update an email connection |
| DELETE | `/2023-11/admin/email-connections/{emailConnection}` | Delete an email connection |
| POST | `/2023-11/admin/email-connections/{emailConnection}/set-main` | Set an email connection as primary |
| GET | `/2023-11/admin/sms-connections` | List SMS connections |
| POST | `/2023-11/admin/sms-connections` | Create an SMS connection |
| PUT | `/2023-11/admin/sms-connections/{smsConnection}` | Update an SMS connection |
| DELETE | `/2023-11/admin/sms-connections/{smsConnection}` | Delete an SMS connection |
| POST | `/2023-11/admin/sms-connections/{smsConnection}/set-main` | Set an SMS connection as primary |
| POST | `/2023-11/admin/push/subscribe` | Subscribe to Web Push notifications |
| GET | `/2023-11/admin/push/subscriptions` | List current user's push subscriptions |
| GET | `/2023-11/admin/push/test/info` | Get push subscription debug info |
| POST | `/2023-11/admin/push/test/send` | Send a test push notification |
| POST | `/2023-11/admin/push/unsubscribe` | Unsubscribe from Web Push |
| GET | `/2023-11/admin/notification-channels` | List notification channels |
| POST | `/2023-11/admin/notification-channels` | Add a notification channel |
| PUT | `/2023-11/admin/notification-channels/{notificationChannel}` | Update a notification channel |
| DELETE | `/2023-11/admin/notification-channels/{notificationChannel}` | Delete a notification channel |
| GET | `/2023-11/admin/notification-preferences` | List notification preferences |
| POST | `/2023-11/admin/notification-preferences/create` | Create a notification preference |
| POST | `/2023-11/admin/notification-preferences/initialize-defaults` | Initialize default notification preferences |
| PUT | `/2023-11/admin/notification-preferences/{preference}` | Update a notification preference |

---

## Campaigns

### `GET` `/2023-11/admin/campaigns`
List all campaigns for the authenticated company, with optional filtering, sorting, and pagination.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[channel_type]` | string | No | — | Exact match on channel: `whatsapp`, `sms`, `email` |
| `filter[scheduled_at][start]` | string (ISO 8601) | No | — | Lower bound for `scheduled_at` |
| `filter[scheduled_at][end]` | string (ISO 8601) | No | — | Upper bound for `scheduled_at` |
| `filter[id]` | string (ULID) | No | — | Exact match on campaign ID |
| `filter[search]` | string | No | — | Full-text search across `name` and `description` |
| `sort` | string | No | `scheduled_at` | Sortable fields: `name`, `scheduled_at`, `status`, `cost_estimate_tokens`, `company_id`, `created_at`, `updated_at`. Prefix with `-` for descending. |
| `include` | string | No | — | Comma-separated eager loads: `company`, `campaignTemplate`, `campaignCustomers`, `customers`, `buttonClicks`, `connection` |
| `per_page` | integer | No | 15 | Results per page (max 100) |
| `page` | integer | No | 1 | Page number |

**Request example:**

```http
GET /2023-11/admin/campaigns?filter[channel_type]=whatsapp&sort=-created_at&per_page=15 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "channel_type": "whatsapp",
      "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
      "whatsapp_connection_id": null,
      "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
      "connection_type": "App\\Models\\WhatsappConnection",
      "message_type": "template",
      "free_message_content": null,
      "name": "Summer Sale 2024",
      "description": "Promotional campaign for summer collection",
      "status": "scheduled",
      "scheduled_at": "2024-07-01T09:00:00.000000Z",
      "scheduled_job_id": 42,
      "cost_estimate_tokens": 350,
      "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
      "excluded_tag_ids": null,
      "all_customers": false,
      "variable_overrides": [
        { "variable": "{{1}}", "type": "customer", "value": "first_name" }
      ],
      "metadata": null,
      "created_at": "2024-06-15T10:23:45.000000Z",
      "updated_at": "2024-06-15T10:23:45.000000Z"
    }
  ],
  "pagination": {
    "total": 42,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 3
  }
}
```

**Error responses:**
- `400` — Missing `X-Company` header (`company_id_required`)
- `401` — Invalid or missing token

---

### `POST` `/2023-11/admin/campaigns`
Create a new campaign. Optionally attach individual customers and compute conversation-window eligibility for free-message campaigns.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:255 | Campaign display name |
| `status` | string | Yes | `draft`, `scheduled`, `sent`, `cancelled` | Initial status |
| `channel_type` | string | No | `whatsapp`, `sms`, `email` | Defaults to `whatsapp` |
| `campaign_template_id` | string (ULID) | No | exists:campaign_templates | Linked campaign template |
| `whatsapp_connection_id` | string (ULID) | No | exists:whatsapp_connections | Deprecated — use `connection_id` / `connection_type` |
| `connection_id` | string | No | — | Polymorphic connection ID |
| `connection_type` | string | No | — | Polymorphic connection type (e.g. `App\Models\WhatsappConnection`) |
| `message_type` | string | No | `template`, `free_message` | Defaults to `template` |
| `free_message_content` | object | No | Required when `message_type=free_message` | Free-form message payload (see structure below) |
| `free_message_content.type` | string | Cond. | `text`, `image`, `video`, `document`, `audio`, `location`, `contacts`, `interactive_buttons`, `interactive_list`, `interactive_cta` | Required when `message_type=free_message` |
| `free_message_content.text` | string | No | — | Text body |
| `free_message_content.interactive` | object | No | — | Interactive message object |
| `description` | string | No | — | Internal description |
| `scheduled_at` | string (ISO 8601) | No | after:now | UTC datetime for scheduled delivery |
| `launch_immediately` | boolean | No | — | Launch as soon as created |
| `customers` | array | No | — | Array of `{ "id": "<ulid>" }` objects |
| `tag_ids` | array | No | each exists:tags | Target audience by tag IDs |
| `excluded_tag_ids` | array | No | each exists:tags | Tags to exclude from audience |
| `all_customers` | boolean | No | — | Target all company customers with a contact |
| `metadata` | object | No | — | Arbitrary key-value metadata |
| `variable_overrides` | array | No | — | Template variable mappings |
| `variable_overrides[].variable` | string | Cond. | required when array present | Variable placeholder (e.g. `{{1}}`) |
| `variable_overrides[].type` | string | Cond. | `customer`, `fixed` | Source type |
| `variable_overrides[].value` | string | No | — | Customer field name or fixed value |

**Request example:**

```http
POST /2023-11/admin/campaigns HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Summer Sale 2024",
  "status": "draft",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
  "connection_type": "App\\Models\\WhatsappConnection",
  "message_type": "template",
  "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
  "variable_overrides": [
    { "variable": "{{1}}", "type": "customer", "value": "first_name" }
  ]
}
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "whatsapp_connection_id": null,
  "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
  "connection_type": "App\\Models\\WhatsappConnection",
  "message_type": "template",
  "free_message_content": null,
  "name": "Summer Sale 2024",
  "description": null,
  "status": "draft",
  "scheduled_at": null,
  "scheduled_job_id": null,
  "cost_estimate_tokens": 0,
  "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
  "excluded_tag_ids": null,
  "all_customers": false,
  "variable_overrides": [
    { "variable": "{{1}}", "type": "customer", "value": "first_name" }
  ],
  "metadata": null,
  "created_at": "2024-06-15T10:23:45.000000Z",
  "updated_at": "2024-06-15T10:23:45.000000Z"
}
```

**Error responses:**
- `400` — Missing `X-Company` header
- `422` — Validation failure (e.g. invalid `status` value, non-existent `campaign_template_id`)

  ```json
  {
    "message": "The status field is required.",
    "errors": { "status": ["The status field is required."] }
  }
  ```
- `500` — Internal error (transaction rolled back)

---

### `POST` `/2023-11/admin/campaigns/bulk-delete`
Permanently delete multiple campaigns in a single request.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `campaign_ids` | array | Yes | each exists:campaigns | IDs of campaigns to delete |

**Request example:**

```http
POST /2023-11/admin/campaigns/bulk-delete HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "campaign_ids": [
    "01hqydxwtxdj3kmzp3bz7jk73g",
    "01hqydxwtxdj3kmzp3bz7jk74a"
  ]
}
```

**Success response — `204 No Content`:** Empty body.

**Error responses:**
- `422` — One or more IDs do not exist
- `500` — Transaction failure

---

### `GET` `/2023-11/admin/campaigns/{campaign}`
Retrieve a single campaign including its template, customers, WhatsApp connection, and button-click stats.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

```http
GET /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "channel_type": "whatsapp",
  "name": "Summer Sale 2024",
  "status": "completed",
  "message_type": "template",
  "free_message_content": null,
  "scheduled_at": "2024-07-01T09:00:00.000000Z",
  "cost_estimate_tokens": 350,
  "tag_ids": null,
  "excluded_tag_ids": null,
  "all_customers": false,
  "variable_overrides": null,
  "metadata": null,
  "created_at": "2024-06-15T10:23:45.000000Z",
  "updated_at": "2024-07-01T09:05:00.000000Z",
  "stats": {
    "total": 350,
    "sent": 348,
    "delivered": 320,
    "read": 210,
    "failed": 2,
    "pending": 0,
    "delivery_rate": 91.9,
    "read_rate": 65.6,
    "failure_rate": 0.6
  },
  "button_stats": {
    "total_clicks": 45,
    "unique_clickers": 38,
    "by_button": [
      {
        "button_id": "btn_1",
        "button_text": "Shop Now",
        "clicks": 45,
        "unique_clickers": 38,
        "rate": 100.0
      }
    ]
  }
}
```

**Error responses:**
- `404` — Campaign not found or belongs to another company

---

### `PUT` `/2023-11/admin/campaigns/{campaign}`
Update a campaign. If `status` is set to `running` a `SendCampaignJob` is dispatched immediately.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

- **Request body:** All fields optional (same field set as create, with status accepting additionally `paused`, `running`, `completed`, `failed`).

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:255 | — |
| `status` | string | No | `draft`, `scheduled`, `paused`, `running`, `completed`, `failed` | Triggers job dispatch when set to `running` |
| `channel_type` | string | No | `whatsapp`, `sms`, `email` | — |
| `campaign_template_id` | string (ULID) | No | exists:campaign_templates | — |
| `connection_id` | string | No | — | — |
| `connection_type` | string | No | — | — |
| `message_type` | string | No | `template`, `free_message` | — |
| `free_message_content` | object | No | — | — |
| `description` | string | No | — | — |
| `scheduled_at` | string | No | datetime | — |
| `customers` | array | No | each exists:customers | — |
| `tag_ids` | array | No | each exists:tags | — |
| `excluded_tag_ids` | array | No | each exists:tags | — |
| `all_customers` | boolean | No | — | — |
| `metadata` | object | No | — | — |
| `variable_overrides` | array | No | — | — |

**Request example:**

```http
PUT /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "status": "scheduled",
  "scheduled_at": "2024-08-01T08:00:00Z"
}
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "whatsapp_connection_id": null,
  "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
  "connection_type": "App\\Models\\WhatsappConnection",
  "message_type": "template",
  "free_message_content": null,
  "name": "Summer Sale 2024",
  "description": "Promotional campaign for summer collection",
  "status": "scheduled",
  "scheduled_at": "2024-08-01T08:00:00.000000Z",
  "scheduled_job_id": 55,
  "cost_estimate_tokens": 350,
  "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
  "excluded_tag_ids": null,
  "all_customers": false,
  "variable_overrides": [
    { "variable": "{{1}}", "type": "customer", "value": "first_name" }
  ],
  "metadata": null,
  "created_at": "2024-06-15T10:23:45.000000Z",
  "updated_at": "2024-06-20T08:00:00.000000Z"
}
```

**Error responses:**
- `404` — Campaign not found
- `422` — Validation failure
- `500` — Transaction failure

---

### `DELETE` `/2023-11/admin/campaigns/{campaign}`
Permanently delete a single campaign.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

```http
DELETE /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `204 No Content`:** Empty body.

**Error responses:**
- `404` — Campaign not found

---

### `POST` `/2023-11/admin/campaigns/{campaign}/cancel-scheduled`
Cancel a pending scheduled campaign job. The campaign status reverts to `draft` and `scheduled_at` is cleared.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/cancel-scheduled HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "message": "Scheduled campaign canceled successfully",
  "campaign": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "status": "draft", "scheduled_at": null }
}
```

**Error responses:**
- `400` — Campaign is not in `scheduled` status, or has no `scheduled_job_id`
- `404` — Scheduled job has already started and could not be canceled
- `500` — Internal error

---

### `GET` `/2023-11/admin/campaigns/{campaign}/customers`
Paginated list of customers attached to a campaign, with per-customer delivery status.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[status]` | string | No | — | Delivery status: `pending`, `sent`, `delivered`, `read`, `failed` |
| `filter[id]` | string (ULID) | No | — | Exact match on customer ID |
| `filter[search]` | string | No | — | Search across `first_name`, `last_name`, `email`, `phone`, `company_name` |
| `filter[button_clicked]` | string | No | — | Filter to customers who clicked a specific button text |
| `sort` | string | No | `-campaign_customer.created_at` | Sortable: `name`, `first_name`, `last_name`, `email`, `phone`, `created_at`, `status`, `sent_at`, `delivered_at`, `read_at` |
| `per_page` | integer | No | 15 | — |
| `page` | integer | No | 1 | — |

**Request example:**

```http
GET /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/customers?filter[status]=failed&per_page=15 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73k",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "email": "wdjopa@example.com",
      "phone": "+33613202181",
      "company_name": "Example Corp",
      "gender": null,
      "preferred_language": "fr",
      "birthdate": null,
      "type": "individual",
      "registration_number": null,
      "tax_exempt": false,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "metadata": null,
      "tax_number": null,
      "medias": [],
      "pivot": {
        "campaign_id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "customer_id": "01hqydxwtxdj3kmzp3bz7jk73k",
        "status": "failed",
        "contact": "+33613202181",
        "sent_at": null,
        "delivered_at": null,
        "read_at": null,
        "failed_at": "2024-07-01T09:02:00.000000Z",
        "error_message": "Invalid phone number"
      },
      "created_at": "2024-05-01T08:00:00.000000Z",
      "updated_at": "2024-05-01T08:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 2,
    "count": 2,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

---

### `POST` `/2023-11/admin/campaigns/{campaign}/customers/export`
Trigger an async export of campaign recipients. The export job runs in the background; the user is notified on completion.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `format` | string | No | `csv` | Export format (`csv`, `xlsx`) |
| `columns` | array | No | all | Column slugs to include |
| `filter` | object | No | — | Same filter keys as the customers list endpoint |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/customers/export?format=csv HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "message": "Export job queued. You will be notified when the file is ready.",
  "job_id": "export_01hqydxwtxdj3kmzp3bz7jk73g"
}
```

> Note: The response shape is produced by `AsyncExportManager`. The exact fields may vary; the key indication is an HTTP 200 acknowledging the job was dispatched. The file is delivered asynchronously via a notification.

---

### `GET` `/2023-11/admin/campaigns/{campaign}/customers/export/fields`
Return the list of column slugs available for export.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

```http
GET /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/customers/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "columns": ["first_name", "last_name", "email", "phone", "status", "sent_at", "delivered_at", "read_at", "failed_at"]
}
```

---

### `GET` `/2023-11/admin/campaigns/{campaign}/customers/{customer}`
Retrieve full delivery detail for a single recipient including status events, button clicks, and the most recent inbox conversation.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |
| `customer` | string (ULID) | Customer ID |

**Request example:**

```http
GET /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/customers/01hqydxwtxdj3kmzp3bz7jk73k HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "customer": { "id": "01hqydxwtxdj3kmzp3bz7jk73k", "first_name": "Wilfried", "last_name": "Djopa" },
  "conversation_id": "01hqydxwtxdj3kmzp3bz7jk73m",
  "campaign_customer": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73n",
    "contact": "+33613202181",
    "status": "read",
    "sent_at": "2024-07-01T09:01:05.000000Z",
    "delivered_at": "2024-07-01T09:01:12.000000Z",
    "failed_at": null,
    "read_at": "2024-07-01T09:05:30.000000Z",
    "error_message": null,
    "cost_usd": 0.005,
    "provider_message_id": "wamid.abc123",
    "metadata": null,
    "created_at": "2024-06-15T10:23:45.000000Z",
    "updated_at": "2024-07-01T09:05:30.000000Z"
  },
  "status_events": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73o", "status": "sent", "created_at": "2024-07-01T09:01:05.000000Z" },
    { "id": "01hqydxwtxdj3kmzp3bz7jk73p", "status": "delivered", "created_at": "2024-07-01T09:01:12.000000Z" },
    { "id": "01hqydxwtxdj3kmzp3bz7jk73q", "status": "read", "created_at": "2024-07-01T09:05:30.000000Z" }
  ],
  "button_clicks": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73r", "button_id": "btn_1", "button_text": "Shop Now", "clicked_at": "2024-07-01T09:07:00.000000Z" }
  ]
}
```

**Error responses:**
- `404` — Campaign–customer record not found

---

### `POST` `/2023-11/admin/campaigns/{campaign}/customers/{customer}/retry`
Re-send the campaign message to a single recipient.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |
| `customer` | string (ULID) | Customer ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/customers/01hqydxwtxdj3kmzp3bz7jk73k/retry HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "message": "Message resent successfully.",
  "status": "sent"
}
```

**Error responses:**
- `404` — No campaign–customer pivot record found
- `422` — Provider rejected the retry

---

### `POST` `/2023-11/admin/campaigns/{campaign}/duplicate`
Create an exact copy of a campaign (with all recipients) in `draft` status with the name suffixed ` (copy)`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/duplicate HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk74c",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "whatsapp_connection_id": null,
  "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
  "connection_type": "App\\Models\\WhatsappConnection",
  "message_type": "template",
  "free_message_content": null,
  "name": "Summer Sale 2024 (copy)",
  "description": "Promotional campaign for summer collection",
  "status": "draft",
  "scheduled_at": null,
  "scheduled_job_id": null,
  "cost_estimate_tokens": 350,
  "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
  "excluded_tag_ids": null,
  "all_customers": false,
  "variable_overrides": [
    { "variable": "{{1}}", "type": "customer", "value": "first_name" }
  ],
  "metadata": null,
  "created_at": "2024-06-20T11:00:00.000000Z",
  "updated_at": "2024-06-20T11:00:00.000000Z"
}
```

**Error responses:**
- `404` — Source campaign not found
- `500` — Transaction failure

---

### `GET` `/2023-11/admin/campaigns/{campaign}/pre-launch`
Estimate recipient count, per-message credit cost, and current wallet balance before launching a campaign.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

```http
GET /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/pre-launch HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "customers_count": 350,
  "channel_type": "whatsapp",
  "selection_method": "tags",
  "credits_per_message": 0.005,
  "estimated_credits": 1.75,
  "current_balance": 25.00,
  "has_sufficient_balance": true,
  "is_free": false
}
```

---

### `POST` `/2023-11/admin/campaigns/{campaign}/launch`
Launch a campaign immediately. Delegates to `CampaignService::launchCampaign`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/launch HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "whatsapp_connection_id": null,
  "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
  "connection_type": "App\\Models\\WhatsappConnection",
  "message_type": "template",
  "free_message_content": null,
  "name": "Summer Sale 2024",
  "description": "Promotional campaign for summer collection",
  "status": "running",
  "scheduled_at": null,
  "scheduled_job_id": null,
  "cost_estimate_tokens": 350,
  "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
  "excluded_tag_ids": null,
  "all_customers": false,
  "variable_overrides": [
    { "variable": "{{1}}", "type": "customer", "value": "first_name" }
  ],
  "metadata": null,
  "created_at": "2024-06-15T10:23:45.000000Z",
  "updated_at": "2024-07-01T09:00:05.000000Z"
}
```

**Error responses:**
- `404` — Campaign not found
- `500` — Launch error (e.g. insufficient balance, provider unreachable)

---

### `GET` `/2023-11/admin/campaigns/{campaign}/pre-retry-failed`
Return the count of failed recipients and cost estimate before retrying all failed messages.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

```http
GET /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/pre-retry-failed HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "failed_count": 12,
  "customers_count": 12,
  "credits_per_message": 0.005,
  "estimated_credits": 0.06,
  "current_balance": 25.00,
  "has_sufficient_balance": true,
  "is_free": false
}
```

---

### `POST` `/2023-11/admin/campaigns/{campaign}/retry-failed`
Retry sending the campaign message to all recipients currently in `failed` status.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/campaigns/01hqydxwtxdj3kmzp3bz7jk73g/retry-failed HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "whatsapp_connection_id": null,
  "connection_id": "01hqydxwtxdj3kmzp3bz7jk73i",
  "connection_type": "App\\Models\\WhatsappConnection",
  "message_type": "template",
  "free_message_content": null,
  "name": "Summer Sale 2024",
  "description": "Promotional campaign for summer collection",
  "status": "running",
  "scheduled_at": null,
  "scheduled_job_id": null,
  "cost_estimate_tokens": 350,
  "tag_ids": ["01hqydxwtxdj3kmzp3bz7jk73j"],
  "excluded_tag_ids": null,
  "all_customers": false,
  "variable_overrides": [
    { "variable": "{{1}}", "type": "customer", "value": "first_name" }
  ],
  "metadata": null,
  "created_at": "2024-06-15T10:23:45.000000Z",
  "updated_at": "2024-07-02T10:00:00.000000Z",
  "stats": {
    "total": 350,
    "sent": 348,
    "delivered": 320,
    "read": 210,
    "failed": 2,
    "pending": 0,
    "delivery_rate": 91.9,
    "read_rate": 65.6,
    "failure_rate": 0.6
  }
}
```

**Error responses:**
- `404` — Campaign not found
- `500` — Retry error

---

## Campaign Templates

Campaign templates are pre-approved message templates for WhatsApp, SMS, or email campaigns. WhatsApp templates require Meta review (`status: pending → approved`); SMS and email templates are auto-approved.

### `GET` `/2023-11/admin/campaign-templates`
List campaign templates. WhatsApp templates are synced from the provider (cached for 30 minutes).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[channel_type]` | string | No | `whatsapp` | `whatsapp`, `sms`, `email` |
| `filter[name]` | string | No | — | Partial match on name |
| `filter[provider_name]` | string | No | — | Partial match on provider name |
| `filter[id]` | string (ULID) | No | — | Exact match |
| `filter[waba_id]` | string | No | — | Exact match on WhatsApp Business Account ID |
| `filter[language]` | string | No | — | Exact match (e.g. `fr`, `en`) |
| `filter[category]` | string | No | — | Exact match (e.g. `MARKETING`, `UTILITY`) |
| `filter[status]` | string | No | — | `pending`, `approved`, `rejected`, `paused` |
| `filter[search]` | string | No | — | Full-text search across name, description, provider_name, content, components, category |
| `sort` | string | No | `-created_at` | `name`, `description`, `type`, `status`, `created_at`, `updated_at`, `category` |
| `include` | string | No | — | `campaigns` |
| `all` | boolean | No | false | Include soft-deleted templates |
| `per_page` | integer | No | 5 | — |
| `page` | integer | No | 1 | — |

**Request example:**

```http
GET /2023-11/admin/campaign-templates?filter[channel_type]=whatsapp&filter[status]=approved&per_page=10 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73h",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Summer Promo",
      "channel_type": "whatsapp",
      "provider_name": "my_company_summer_promo",
      "provider_id": "1234567890",
      "waba_id": "987654321",
      "language": "en",
      "category": "MARKETING",
      "status": "approved",
      "components": [
        { "type": "HEADER", "format": "TEXT", "text": "Special Offer!" },
        { "type": "BODY", "text": "Hi {{1}}, check out our summer sale." },
        { "type": "FOOTER", "text": "Powered by Genuka.com, for My Company." }
      ],
      "content": null,
      "phone_numbers": [
        {
          "id": "01hqydxwtxdj3kmzp3bz7jk73i",
          "phone_number_id": "112233445566",
          "display_phone_number": "+33600000000",
          "verified_name": "My Company",
          "is_main": true,
          "waba_id": "987654321"
        }
      ],
      "created_at": "2024-06-01T08:00:00.000000Z",
      "updated_at": "2024-06-05T12:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 8,
    "count": 5,
    "per_page": 5,
    "current_page": 1,
    "total_pages": 2
  }
}
```

---

### `POST` `/2023-11/admin/campaign-templates`
Create a campaign template locally and submit it to the provider for approval. For WhatsApp, a `FOOTER` component is auto-appended if absent.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:191 | Human-readable template name |
| `language` | string | Yes | max:6 | BCP 47 language code, e.g. `fr`, `en` |
| `category` | string | Yes | max:50 | `MARKETING`, `UTILITY`, `AUTHENTICATION` |
| `channel_type` | string | No | `whatsapp`, `sms`, `email` | Defaults to `whatsapp` |
| `components` | array | Cond. | Required for WhatsApp | WhatsApp template components (HEADER, BODY, FOOTER, BUTTONS) |
| `waba_id` | string | Cond. | Required for WhatsApp | WhatsApp Business Account ID |
| `content` | object | Cond. | Required for SMS/Email | `{ "body": "...", "subject": "..." }` (subject required for email) |
| `medias` | array | No | — | Media files for template header (`collection_name`, `src`/`file`/`base64`) |

**Request example (WhatsApp):**

```http
POST /2023-11/admin/campaign-templates HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Summer Promo",
  "language": "fr",
  "category": "MARKETING",
  "channel_type": "whatsapp",
  "waba_id": "987654321",
  "components": [
    {
      "type": "BODY",
      "text": "Bonjour {{1}}, découvrez nos offres d'été !"
    }
  ]
}
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Summer Promo",
  "channel_type": "whatsapp",
  "provider_name": "summer_promo",
  "provider_id": "1234567890",
  "waba_id": "987654321",
  "language": "fr",
  "category": "MARKETING",
  "status": "pending",
  "components": [
    { "type": "BODY", "text": "Bonjour {{1}}, découvrez nos offres d'été !" },
    { "type": "FOOTER", "text": "Powered by Genuka.com, for My Company." }
  ],
  "content": null,
  "phone_numbers": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73i",
      "phone_number_id": "112233445566",
      "display_phone_number": "+33600000000",
      "verified_name": "My Company",
      "is_main": true,
      "waba_id": "987654321"
    }
  ],
  "created_at": "2024-06-01T08:00:00.000000Z",
  "updated_at": "2024-06-01T08:00:00.000000Z"
}
```

**Error responses:**
- `422` — Validation failure
- `500` — Provider API error or transaction failure (includes `fields` array indicating blamed fields from Meta)

  ```json
  {
    "error": "Provider API Error",
    "message": "Invalid template component",
    "code": 100,
    "fields": [["components", "BODY", "text"]]
  }
  ```

---

### `POST` `/2023-11/admin/campaign-templates/bulk-delete`
Soft-delete multiple campaign templates.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `ids` | array | Yes | each exists:campaign_templates | IDs of templates to delete |

**Request example:**

```http
POST /2023-11/admin/campaign-templates/bulk-delete HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "ids": ["01hqydxwtxdj3kmzp3bz7jk73h", "01hqydxwtxdj3kmzp3bz7jk74b"]
}
```

**Success response — `200 OK`:**

```json
{ "message": "Templates deleted successfully" }
```

---

### `GET` `/2023-11/admin/campaign-templates/{campaignTemplate}`
Retrieve a single campaign template. Pass `?sync=true` to force a provider status check before returning.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `sync` | boolean | No | false | Re-fetch status from the provider (WhatsApp only) |

**Request example:**

```http
GET /2023-11/admin/campaign-templates/01hqydxwtxdj3kmzp3bz7jk73h?sync=true HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Summer Promo",
  "channel_type": "whatsapp",
  "provider_name": "summer_promo",
  "provider_id": "1234567890",
  "waba_id": "987654321",
  "language": "fr",
  "category": "MARKETING",
  "status": "approved",
  "components": [
    { "type": "HEADER", "format": "TEXT", "text": "Special Offer!" },
    { "type": "BODY", "text": "Bonjour {{1}}, découvrez nos offres d'été !" },
    { "type": "FOOTER", "text": "Powered by Genuka.com, for My Company." }
  ],
  "content": null,
  "phone_numbers": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73i",
      "phone_number_id": "112233445566",
      "display_phone_number": "+33600000000",
      "verified_name": "My Company",
      "is_main": true,
      "waba_id": "987654321"
    }
  ],
  "created_at": "2024-06-01T08:00:00.000000Z",
  "updated_at": "2024-06-05T12:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/campaign-templates/{campaignTemplate}`
Update a campaign template. Pass `?resubmit=true` to re-submit to the provider after updating.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `resubmit` | boolean | No | false | Re-submit to the provider for re-approval |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:191 | — |
| `language` | string | No | max:6 | — |
| `category` | string | No | max:50 | — |
| `components` | array | No | — | WhatsApp components array |
| `content` | object | No | — | SMS/email content object |

**Request example:**

```http
PUT /2023-11/admin/campaign-templates/01hqydxwtxdj3kmzp3bz7jk73h HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Summer Promo v2",
  "components": [
    { "type": "BODY", "text": "Hi {{1}}, our summer sale is live!" }
  ]
}
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73h",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Summer Promo v2",
  "channel_type": "whatsapp",
  "provider_name": "summer_promo_v2",
  "provider_id": "1234567890",
  "waba_id": "987654321",
  "language": "fr",
  "category": "MARKETING",
  "status": "pending",
  "components": [
    { "type": "BODY", "text": "Hi {{1}}, our summer sale is live!" },
    { "type": "FOOTER", "text": "Powered by Genuka.com, for My Company." }
  ],
  "content": null,
  "phone_numbers": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73i",
      "phone_number_id": "112233445566",
      "display_phone_number": "+33600000000",
      "verified_name": "My Company",
      "is_main": true,
      "waba_id": "987654321"
    }
  ],
  "created_at": "2024-06-01T08:00:00.000000Z",
  "updated_at": "2024-06-10T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/campaign-templates/{campaignTemplate}`
Soft-delete a single campaign template.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Request example:**

```http
DELETE /2023-11/admin/campaign-templates/01hqydxwtxdj3kmzp3bz7jk73h HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `204 No Content`:** Empty body.

---

## Storefront Templates

Storefront themes/templates are visual design templates that can be associated with the company's online shop. These are distinct from campaign message templates.

### `GET` `/2023-11/admin/templates`
List available storefront templates.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[name]` | string | No | — | Partial match |
| `filter[description]` | string | No | — | Partial match |
| `filter[price]` | string | No | — | Partial match |
| `filter[currency]` | string | No | — | Partial match |
| `filter[created_at][start]` | string | No | — | Lower bound |
| `filter[created_at][end]` | string | No | — | Upper bound |
| `filter[search]` | string | No | — | Full-text search |
| `sort` | string | No | — | `created_at`, `updated_at`, `price`, `name`, `configurationsCount` |
| `include` | string | No | — | `themeConfigurations` |
| `per_page` | integer | No | 15 | — |

**Request example:**

```http
GET /2023-11/admin/templates HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73s",
      "name": "Minimal Store",
      "description": "Clean minimal e-commerce theme",
      "price": 0.00,
      "currency": "USD",
      "medias": [],
      "themeConfigurations": [],
      "created_at": "2024-01-10T10:00:00.000000Z",
      "updated_at": "2024-01-10T10:00:00.000000Z"
    }
  ],
  "pagination": { "total": 5, "count": 5, "per_page": 15, "current_page": 1, "total_pages": 1 }
}
```

---

### `POST` `/2023-11/admin/templates/apps/sync`
Dispatch a background job to synchronize an external storefront template from a remote location.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `location` | string | Yes | — | URL or identifier of the external template source |
| `success_url` | string | No | — | Redirect URL on successful sync |
| `failure_url` | string | No | — | Redirect URL on sync failure |

**Request example:**

```http
POST /2023-11/admin/templates/apps/sync HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "location": "https://templates.genuka.com/themes/minimal-v2.zip",
  "success_url": "https://app.example.com/templates?status=success",
  "failure_url": "https://app.example.com/templates?status=error"
}
```

**Success response — `200 OK`:**

```json
{
  "message": "Template synchronization in progress. You will be notified upon completion."
}
```

---

### `GET` `/2023-11/admin/templates/{template}`
Get a single storefront template with its theme configurations.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `template` | string (ULID) | Template ID |

**Request example:**

```http
GET /2023-11/admin/templates/01hqydxwtxdj3kmzp3bz7jk73s HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73s",
  "name": "Minimal Store",
  "description": "Clean minimal e-commerce theme",
  "price": 0.00,
  "currency": "USD",
  "metadata": {
    "default": {
      "primary_color": "#3B82F6",
      "font": "Inter"
    }
  },
  "medias": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73z",
      "url": "https://cdn.genuka.com/templates/minimal-preview.jpg",
      "mime_type": "image/jpeg",
      "file_name": "minimal-preview.jpg",
      "size": 102400,
      "collection_name": "preview"
    }
  ],
  "themeConfigurations": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73t",
      "template_id": "01hqydxwtxdj3kmzp3bz7jk73s",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "is_active": true,
      "configuration": {
        "primary_color": "#3B82F6",
        "font": "Inter"
      },
      "metadata": null,
      "created_at": "2024-06-15T10:23:45.000000Z",
      "updated_at": "2024-06-15T10:23:45.000000Z"
    }
  ],
  "created_at": "2024-01-10T10:00:00.000000Z",
  "updated_at": "2024-01-10T10:00:00.000000Z"
}
```

---

### `POST` `/2023-11/admin/templates/{template}`
Associate a storefront template with the company. Deactivates any other active theme configuration and registers the template's domains with Caddy.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `template` | string (ULID) | Template ID |

**Request body:** None — no body required. The template to associate is identified by the path parameter; the company is resolved from the authenticated user's session.

**Request example:**

```http
POST /2023-11/admin/templates/01hqydxwtxdj3kmzp3bz7jk73s HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73t",
  "template_id": "01hqydxwtxdj3kmzp3bz7jk73s",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "is_active": true,
  "configuration": {
    "primary_color": "#3B82F6",
    "font": "Inter"
  },
  "metadata": null,
  "created_at": "2024-06-15T10:23:45.000000Z",
  "updated_at": "2024-06-15T10:23:45.000000Z"
}
```

**Error responses:**
- `500` — Domain registration on the CDN/Caddy server failed

---

## Email Connections

Email connections store the SMTP/API credentials used to send email campaigns and transactional messages.

### `GET` `/2023-11/admin/email-connections`
List all email connections for the company, ordered by `is_main` then `created_at` descending.

**Request example:**

```http
GET /2023-11/admin/email-connections HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73u",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "provider": "smtp",
    "from_email": "hello@example.com",
    "from_name": "Example Store",
    "custom_name": "Main SMTP",
    "is_main": true,
    "created_at": "2024-04-01T08:00:00.000000Z",
    "updated_at": "2024-04-01T08:00:00.000000Z"
  }
]
```

> Note: `credentials_enc` is encrypted at rest and is never exposed in the response.

---

### `POST` `/2023-11/admin/email-connections`
Create a new email connection. The first connection for the company is automatically set as the primary (`is_main: true`).

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `provider` | string | Yes | `smtp`, `ses`, `sendgrid`, `mailgun` | Email provider |
| `from_email` | string | Yes | valid email | Sender address |
| `from_name` | string | No | max:255 | Sender display name |
| `custom_name` | string | No | max:255 | Internal label |
| `credentials` | object | Yes | Provider-specific (see below) | Provider credentials |

**SMTP credentials:**

| Field | Type | Required | Description |
|---|---|---|---|
| `credentials.host` | string | Yes | SMTP hostname |
| `credentials.port` | integer | Yes | SMTP port |
| `credentials.username` | string | Yes | SMTP username |
| `credentials.password` | string | Yes | SMTP password |
| `credentials.encryption` | string | No | `tls` or `ssl` |

**Amazon SES credentials:**

| Field | Type | Required | Description |
|---|---|---|---|
| `credentials.api_key` | string | Yes | AWS Access Key ID |
| `credentials.secret_key` | string | Yes | AWS Secret Access Key |
| `credentials.region` | string | Yes | AWS region (e.g. `us-east-1`) |

**Mailgun credentials:**

| Field | Type | Required | Description |
|---|---|---|---|
| `credentials.api_key` | string | Yes | Mailgun API key |
| `credentials.domain` | string | Yes | Mailgun sending domain |

**SendGrid credentials:**

| Field | Type | Required | Description |
|---|---|---|---|
| `credentials.api_key` | string | Yes | SendGrid API key |

**Request example:**

```http
POST /2023-11/admin/email-connections HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "provider": "smtp",
  "from_email": "hello@example.com",
  "from_name": "Example Store",
  "custom_name": "Main SMTP",
  "credentials": {
    "host": "smtp.example.com",
    "port": 587,
    "username": "smtp_user",
    "password": "smtp_password",
    "encryption": "tls"
  }
}
```

**Success response — `201 Created`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73u",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "provider": "smtp",
  "from_email": "hello@example.com",
  "from_name": "Example Store",
  "custom_name": "Main SMTP",
  "is_main": true,
  "created_at": "2024-04-01T08:00:00.000000Z",
  "updated_at": "2024-04-01T08:00:00.000000Z"
}
```

**Error responses:**
- `422` — Validation failure (e.g. invalid provider, missing credential fields)

---

### `PUT` `/2023-11/admin/email-connections/{emailConnection}`
Update an email connection. Credentials are merged (not replaced) when provided.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `emailConnection` | string (ULID) | Email connection ID |

- **Request body:** All fields optional (same as store, `provider` and `credentials` are `sometimes`).

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `from_email` | string | No | valid email | — |
| `from_name` | string | No | max:255 | — |
| `custom_name` | string | No | max:255 | — |
| `provider` | string | No | valid enum | — |
| `credentials` | object | No | Provider-specific | Merged with existing credentials |

**Request example:**

```http
PUT /2023-11/admin/email-connections/01hqydxwtxdj3kmzp3bz7jk73u HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "from_name": "Example Store Updated"
}
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73u",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "provider": "smtp",
  "from_email": "hello@example.com",
  "from_name": "Example Store Updated",
  "custom_name": "Main SMTP",
  "is_main": true,
  "created_at": "2024-04-01T08:00:00.000000Z",
  "updated_at": "2024-04-10T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/email-connections/{emailConnection}`
Delete an email connection.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `emailConnection` | string (ULID) | Email connection ID |

**Request example:**

```http
DELETE /2023-11/admin/email-connections/01hqydxwtxdj3kmzp3bz7jk73u HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `204 No Content`:** Empty body.

---

### `POST` `/2023-11/admin/email-connections/{emailConnection}/set-main`
Promote an email connection to primary. Atomically clears `is_main` on all other connections for the company.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `emailConnection` | string (ULID) | Email connection ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/email-connections/01hqydxwtxdj3kmzp3bz7jk73u/set-main HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73u",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "provider": "smtp",
  "from_email": "hello@example.com",
  "from_name": "Example Store",
  "custom_name": "Main SMTP",
  "is_main": true,
  "created_at": "2024-04-01T08:00:00.000000Z",
  "updated_at": "2024-04-15T10:00:00.000000Z"
}
```

---

## SMS Connections

SMS connections store credentials for third-party SMS providers. For `smsgate` providers, webhooks are registered asynchronously when the connection is created or credentials change.

### `GET` `/2023-11/admin/sms-connections`
List all SMS connections for the company.

**Request example:**

```http
GET /2023-11/admin/sms-connections HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73v",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "provider": "smsgate",
    "phone_number": "+33600000000",
    "custom_name": "Main SMS Gateway",
    "is_main": true,
    "created_at": "2024-04-01T08:00:00.000000Z",
    "updated_at": "2024-04-01T08:00:00.000000Z"
  }
]
```

> Note: `credentials_enc` is encrypted at rest and never exposed in the response.

---

### `POST` `/2023-11/admin/sms-connections`
Create a new SMS connection. Webhooks are registered asynchronously for `smsgate` provider.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `provider` | string | Yes | `smsgate`, `twilio`, `vonage`, `infobip` | SMS provider |
| `phone_number` | string | No | — | Sender phone number |
| `custom_name` | string | No | max:255 | Internal label |
| `credentials` | object | Yes | Provider-specific | API credentials (stored encrypted) |

**Request example:**

```http
POST /2023-11/admin/sms-connections HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "provider": "twilio",
  "phone_number": "+33600000000",
  "custom_name": "Twilio Primary",
  "credentials": {
    "api_key": "ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "secret_key": "your_auth_token"
  }
}
```

**Success response — `201 Created`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73v",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "provider": "twilio",
  "phone_number": "+33600000000",
  "custom_name": "Twilio Primary",
  "is_main": true,
  "created_at": "2024-04-01T08:00:00.000000Z",
  "updated_at": "2024-04-01T08:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/sms-connections/{smsConnection}`
Update an SMS connection. Credentials are merged. If credentials change on a `smsgate` connection, webhooks are re-synced asynchronously.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `smsConnection` | string (ULID) | SMS connection ID |

- **Request body:** All fields optional.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `custom_name` | string | No | max:255 | — |
| `phone_number` | string | No | — | — |
| `provider` | string | No | valid enum | — |
| `credentials` | object | No | — | Merged with existing credentials |

**Request example:**

```http
PUT /2023-11/admin/sms-connections/01hqydxwtxdj3kmzp3bz7jk73v HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "custom_name": "Twilio Primary (updated)"
}
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73v",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "provider": "twilio",
  "phone_number": "+33600000000",
  "custom_name": "Twilio Primary (updated)",
  "is_main": true,
  "created_at": "2024-04-01T08:00:00.000000Z",
  "updated_at": "2024-04-10T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/sms-connections/{smsConnection}`
Delete an SMS connection. For `smsgate`, webhooks are removed asynchronously before deletion.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `smsConnection` | string (ULID) | SMS connection ID |

**Request example:**

```http
DELETE /2023-11/admin/sms-connections/01hqydxwtxdj3kmzp3bz7jk73v HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `204 No Content`:** Empty body.

---

### `POST` `/2023-11/admin/sms-connections/{smsConnection}/set-main`
Promote an SMS connection to primary. Atomically clears `is_main` on all others.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `smsConnection` | string (ULID) | SMS connection ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/sms-connections/01hqydxwtxdj3kmzp3bz7jk73v/set-main HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73v",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "provider": "twilio",
  "phone_number": "+33600000000",
  "custom_name": "Twilio Primary",
  "is_main": true,
  "created_at": "2024-04-01T08:00:00.000000Z",
  "updated_at": "2024-04-15T10:00:00.000000Z"
}
```

---

## Web Push

### `POST` `/2023-11/admin/push/subscribe`
Register a browser Web Push subscription for the authenticated user. Replaces any existing subscription at the same endpoint.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `endpoint` | string | Yes | valid URL | Browser push endpoint URL |
| `keys.auth` | string | Yes | — | VAPID auth secret |
| `keys.p256dh` | string | Yes | — | VAPID public key |

**Request example:**

```http
POST /2023-11/admin/push/subscribe HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "endpoint": "https://fcm.googleapis.com/fcm/send/abc123",
  "keys": {
    "auth": "abcdefghijk",
    "p256dh": "BNxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
}
```

**Success response — `200 OK`:**

```json
{
  "success": true,
  "message": "Subscribed successfully",
  "data": {
    "endpoint": "https://fcm.googleapis.com/fcm/send/abc123",
    "content_encoding": "aesgcm",
    "created_at": "2024-06-15T10:23:45.000000Z"
  }
}
```

**Error responses:**
- `401` — No authenticated user
- `422` — Validation failure

---

### `GET` `/2023-11/admin/push/subscriptions`
List all active push subscriptions for the authenticated user.

**Request example:**

```http
GET /2023-11/admin/push/subscriptions HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "success": true,
  "data": [
    {
      "endpoint": "https://fcm.googleapis.com/fcm/send/abc123",
      "content_encoding": "aesgcm",
      "created_at": "2024-06-15T10:23:45.000000Z"
    }
  ]
}
```

---

### `GET` `/2023-11/admin/push/test/info`
Return debug information about the authenticated user's push subscriptions.

**Request example:**

```http
GET /2023-11/admin/push/test/info HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "success": true,
  "user": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "type": "App\\Models\\User",
    "email": "user@example.com"
  },
  "subscriptions": [
    { "id": 1, "endpoint": "https://fcm.googleapis.com/fcm/send/abc123", "created_at": "2024-06-15T10:23:45.000000Z" }
  ],
  "total": 1,
  "notification_permission": "Check in browser console"
}
```

---

### `POST` `/2023-11/admin/push/test/send`
Send a test Web Push notification to all of the authenticated user's active subscriptions.

- **Request body:** All fields optional.

| Field | Type | Required | Default | Description |
|---|---|---|---|---|
| `title` | string | No | `Test Notification` | Notification title |
| `body` | string | No | `This is a test push notification from Genuka` | Notification body |
| `icon` | string | No | `/logo.png` | Icon URL |
| `badge` | string | No | `/logo.png` | Badge icon URL |
| `url` | string | No | `/dashboard` | URL to open on click |

**Request example:**

```http
POST /2023-11/admin/push/test/send HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Hello from Genuka",
  "body": "Your campaign just launched!",
  "url": "/dashboard/campaigns"
}
```

**Success response — `200 OK`:**

```json
{
  "success": true,
  "message": "Test notification sent to 1 subscription(s)",
  "data": {
    "sent": 1,
    "failed": 0,
    "total": 1,
    "errors": []
  }
}
```

**Error responses:**
- `401` — No authenticated user
- `404` — No push subscriptions found

---

### `POST` `/2023-11/admin/push/unsubscribe`
Remove a Web Push subscription by endpoint.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `endpoint` | string | Yes | valid URL | Endpoint URL to unsubscribe |

**Request example:**

```http
POST /2023-11/admin/push/unsubscribe HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "endpoint": "https://fcm.googleapis.com/fcm/send/abc123"
}
```

**Success response — `200 OK`:**

```json
{
  "success": true,
  "message": "Unsubscribed successfully"
}
```

---

## Notification Channels

Notification channels are specific contact points (email address, phone number, push endpoint) associated with a user or customer, used to route system notifications.

### `GET` `/2023-11/admin/notification-channels`
List notification channels with optional filtering and pagination.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[channel]` | string | No | — | Exact match (e.g. `email`, `sms`, `push`) |
| `filter[language]` | string | No | — | Exact match |
| `filter[is_active]` | boolean | No | — | Exact match |
| `filter[value]` | string | No | — | Partial match on channel value |
| `filter[user_id]` | string (ULID) | No | — | Filter to channels for a specific user |
| `filter[customer_id]` | string (ULID) | No | — | Filter to channels for a specific customer |
| `filter[created_at][start]` | string | No | — | Lower bound |
| `filter[created_at][end]` | string | No | — | Upper bound |
| `filter[search]` | string | No | — | Search across `channel` and `value` |
| `sort` | string | No | `channel` | `channel`, `value`, `language`, `is_active`, `created_at`, `updated_at` |
| `per_page` | integer | No | 15 | — |

**Request example:**

```http
GET /2023-11/admin/notification-channels?filter[channel]=email HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73w",
      "channel": "email",
      "value": "alerts@example.com",
      "language": null,
      "is_active": true,
      "metadata": null,
      "notifiable_type": "App\\Models\\User",
      "notifiable_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "medias": null,
      "created_at": "2024-06-15T10:23:45.000000Z",
      "updated_at": "2024-06-15T10:23:45.000000Z"
    }
  ],
  "pagination": {
    "total": 3,
    "count": 3,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

---

### `POST` `/2023-11/admin/notification-channels`
Add a notification channel for the authenticated user (or a specified user via `user_id` in the request).

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `channel` | string | Yes | — | Channel type (e.g. `email`, `sms`, `push`) |
| `value` | string | Yes | — | Channel value (e.g. email address, phone number) |

**Request example:**

```http
POST /2023-11/admin/notification-channels HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "channel": "email",
  "value": "alerts@example.com"
}
```

**Success response — `200 OK`:**

```json
{
  "message": "Notification channel added successfully",
  "channel": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73w",
    "channel": "email",
    "value": "alerts@example.com",
    "is_active": true,
    "language": null,
    "created_at": "2024-06-15T10:23:45.000000Z"
  }
}
```

---

### `PUT` `/2023-11/admin/notification-channels/{notificationChannel}`
Update a notification channel.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `notificationChannel` | string (ULID) | Channel ID |

- **Request body:** No fields are currently processed — the controller method is a no-op stub. Send an empty body or omit it entirely.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| *(none)* | — | — | — | No fields accepted at this time |

**Request example:**

```http
PUT /2023-11/admin/notification-channels/01hqydxwtxdj3kmzp3bz7jk73w HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{}
```

**Success response — `200 OK`:**

```json
null
```

> Note: The controller method is currently a no-op stub and returns no body. The endpoint is registered but does not perform any update.

---

### `DELETE` `/2023-11/admin/notification-channels/{notificationChannel}`
Delete a notification channel.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `notificationChannel` | string (ULID) | Channel ID |

**Request example:**

```http
DELETE /2023-11/admin/notification-channels/01hqydxwtxdj3kmzp3bz7jk73w HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{
  "message": "Notification channel deleted successfully"
}
```

---

## Notification Preferences

Notification preferences control which system events trigger notifications for the authenticated user, and via which channels.

Supported event values: `order.new`, `shipping.new`, `payment.received`, `stock.alert`, `import.completed`, `import.failed`, `export.completed`, `export.failed`, `bill.due_reminder`, `order.payment_reminder`, `customer.birthdays_weekly`.

### `GET` `/2023-11/admin/notification-preferences`
List all notification preferences for the authenticated user.

**Request example:**

```http
GET /2023-11/admin/notification-preferences HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73x",
    "notifiable_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "notifiable_type": "App\\Models\\User",
    "event": "order.new",
    "channel_id": "01hqydxwtxdj3kmzp3bz7jk73w",
    "enabled": true,
    "channel": {
      "id": "01hqydxwtxdj3kmzp3bz7jk73w",
      "channel": "email",
      "value": "alerts@example.com"
    },
    "created_at": "2024-06-15T10:23:45.000000Z",
    "updated_at": "2024-06-15T10:23:45.000000Z"
  }
]
```

---

### `POST` `/2023-11/admin/notification-preferences/create`
Create a notification preference. If a preference for the same (event, channel_id) pair already exists, it is updated instead.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `event` | string | Yes | valid enum value | Event identifier (see list above) |
| `channel_id` | string (ULID) | Yes | exists:notification_channels | Notification channel to use |
| `enabled` | boolean | Yes | — | Whether this preference is active |

**Request example:**

```http
POST /2023-11/admin/notification-preferences/create HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "event": "order.new",
  "channel_id": "01hqydxwtxdj3kmzp3bz7jk73w",
  "enabled": true
}
```

**Success response — `200 OK`:**

```json
{
  "status": "success",
  "message": "Préférence créée",
  "preference": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73x",
    "event": "order.new",
    "channel_id": "01hqydxwtxdj3kmzp3bz7jk73w",
    "enabled": true,
    "channel": { "id": "01hqydxwtxdj3kmzp3bz7jk73w", "channel": "email", "value": "alerts@example.com" }
  }
}
```

**Error responses:**
- `422` — Invalid event value, non-existent channel_id, or missing `enabled`

---

### `POST` `/2023-11/admin/notification-preferences/initialize-defaults`
Create default preferences (enabled) for every known event and every notification channel belonging to the authenticated user. Existing preferences are not overwritten.

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/notification-preferences/initialize-defaults HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`:**

```json
{ "status": "initialized" }
```

---

### `PUT` `/2023-11/admin/notification-preferences/{preference}`
Enable or disable a specific notification preference.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `preference` | string (ULID) | Preference ID (must belong to the authenticated user) |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `enabled` | boolean | Yes | — | New enabled state |

**Request example:**

```http
PUT /2023-11/admin/notification-preferences/01hqydxwtxdj3kmzp3bz7jk73x HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "enabled": false
}
```

**Success response — `200 OK`:**

```json
{
  "status": "success",
  "message": "Préférence mise à jour",
  "preference": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73x",
    "event": "order.new",
    "channel_id": "01hqydxwtxdj3kmzp3bz7jk73w",
    "enabled": false,
    "channel": { "id": "01hqydxwtxdj3kmzp3bz7jk73w", "channel": "email", "value": "alerts@example.com" }
  }
}
```

**Error responses:**
- `403` — Preference belongs to another user (enforced by route-model binding scope)
- `404` — Event type not recognized
- `422` — Missing or invalid `enabled` field
