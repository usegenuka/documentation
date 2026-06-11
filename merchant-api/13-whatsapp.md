---
title: "WhatsApp (Messaging, Campaigns & Calling)"
description: "This domain covers the full lifecycle of WhatsApp Business integration in Genuka: connecting phone numbers via Meta Embedded Signup or manually, managing messag"
sidebarTitle: "WhatsApp"
---

This domain covers the full lifecycle of WhatsApp Business integration in Genuka: connecting phone numbers via Meta Embedded Signup or manually, managing message-template campaigns, sending ad-hoc messages through the proxy API, and initiating/monitoring VoIP calls using the WhatsApp Calling (FreeSWITCH-backed) feature. Webhooks from Meta are received and processed here, automatically creating customers and routing messages into the OmniBox inbox.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/whatsapp` | WhatsApp entry point (placeholder) |
| GET | `/2023-11/whatsapp/login` | Render WhatsApp login view |
| GET | `/2023-11/whatsapp/auth/redirect` | Redirect to Facebook OAuth |
| GET | `/2023-11/whatsapp/auth/callback` | Handle Facebook OAuth callback |
| GET | `/2023-11/whatsapp/webhook` | Meta webhook verification (hub challenge) |
| POST | `/2023-11/whatsapp/webhook` | Receive Meta webhook events |
| GET | `/2023-11/whatsapp/calls` | List active calls |
| POST | `/2023-11/whatsapp/calls` | Initiate an outbound call |
| GET | `/2023-11/whatsapp/calls/ice-servers` | Get TURN/STUN ICE server credentials |
| GET | `/2023-11/whatsapp/calls/{call}` | Show a single call |
| POST | `/2023-11/whatsapp/calls/{call}/terminate` | Terminate a call |
| POST | `/2023-11/whatsapp/calls/{call}/verto-credentials` | Get Verto WebRTC credentials |
| POST | `/2023-11/whatsapp/calls/freeswitch/dialplan` | FreeSWITCH dialplan callback (internal) |
| POST | `/2023-11/whatsapp/calls/freeswitch/directory` | FreeSWITCH directory callback (internal) |
| POST | `/2023-11/whatsapp/calls/freeswitch/event` | FreeSWITCH event fallback (internal) |
| POST | `/2023-11/whatsapp/calls/freeswitch/recording-uploaded` | Recording upload acknowledgment (internal) |
| GET | `/whatsapp/sync/{company}` | Render WhatsApp sync page |
| GET | `/2023-11/admin/whatsapp/oauth` | Get OAuth URL for Embedded Signup |
| POST | `/2023-11/admin/whatsapp/oauth/callback` | Handle Embedded Signup OAuth code exchange |
| GET | `/2023-11/admin/whatsapp/connections` | List WhatsApp connections |
| POST | `/2023-11/admin/whatsapp/connections` | Add a connection manually |
| PUT | `/2023-11/admin/whatsapp/connections/{connection}` | Update a connection |
| DELETE | `/2023-11/admin/whatsapp/connections/{connection}` | Delete a connection |
| GET | `/2023-11/admin/whatsapp/connections/{connection}/coexistence-status` | Check coexistence status |
| POST | `/2023-11/admin/whatsapp/connections/{connection}/set-main` | Set connection as main |
| POST | `/2023-11/admin/whatsapp/connections/{connection}/sync-contacts` | Sync contacts (coexistence) |
| POST | `/2023-11/admin/whatsapp/connections/{connection}/sync-history` | Sync message history (coexistence) |
| POST | `/2023-11/admin/whatsapp/connections/{connection}/sync-history-complete` | Full contact + history sync |
| GET | `/2023-11/admin/whatsapp/packages` | List WhatsApp token packages |
| GET | `/2023-11/admin/whatsapp/campaigns` | List campaigns |
| POST | `/2023-11/admin/whatsapp/campaigns` | Create a campaign |
| POST | `/2023-11/admin/whatsapp/campaigns/bulk-delete` | Bulk delete campaigns |
| GET | `/2023-11/admin/whatsapp/campaigns/{campaign}` | Get a campaign |
| PUT | `/2023-11/admin/whatsapp/campaigns/{campaign}` | Update a campaign |
| DELETE | `/2023-11/admin/whatsapp/campaigns/{campaign}` | Delete a campaign |
| GET | `/2023-11/admin/whatsapp/campaigns/{campaign}/pre-launch` | Pre-launch cost estimate |
| POST | `/2023-11/admin/whatsapp/campaigns/{campaign}/launch` | Launch a campaign |
| POST | `/2023-11/admin/whatsapp/campaigns/{campaign}/cancel-scheduled` | Cancel a scheduled campaign |
| GET | `/2023-11/admin/whatsapp/campaigns/{campaign}/pre-retry-failed` | Pre-retry-failed cost estimate |
| POST | `/2023-11/admin/whatsapp/campaigns/{campaign}/retry-failed` | Retry all failed messages |
| POST | `/2023-11/admin/whatsapp/campaigns/{campaign}/duplicate` | Duplicate a campaign |
| GET | `/2023-11/admin/whatsapp/campaigns/{campaign}/customers` | List campaign recipients |
| POST | `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/export` | Export campaign recipients |
| GET | `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/export/fields` | List exportable fields |
| GET | `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/{customer}` | Get recipient detail |
| POST | `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/{customer}/retry` | Retry one recipient |
| GET | `/2023-11/admin/whatsapp/templates` | List campaign templates |
| POST | `/2023-11/admin/whatsapp/templates` | Create & submit a template |
| POST | `/2023-11/admin/whatsapp/templates/bulk-delete` | Bulk delete templates |
| GET | `/2023-11/admin/whatsapp/templates/{campaignTemplate}` | Get a template |
| PUT | `/2023-11/admin/whatsapp/templates/{campaignTemplate}` | Update a template |
| DELETE | `/2023-11/admin/whatsapp/templates/{campaignTemplate}` | Delete a template |
| POST | `/2023-11/admin/whatsapp/templates/{campaignTemplate}/duplicate` | Duplicate a template |
| POST | `/2023-11/admin/whatsapp/templates/{campaignTemplate}/preview` | Preview rendered template |
| POST | `/2023-11/admin/whatsapp/templates/{campaignTemplate}/test` | Send a test message |
| GET | `/2023-11/admin/whatsapp/proxy-logs` | List proxy API logs |
| GET | `/2023-11/admin/whatsapp/proxy-tokens` | List proxy API tokens |
| POST | `/2023-11/admin/whatsapp/proxy-tokens` | Create a proxy API token |
| POST | `/2023-11/admin/whatsapp/proxy-tokens/{token}/revoke` | Revoke a proxy token |
| DELETE | `/2023-11/admin/whatsapp/proxy-tokens/{token}` | Delete a proxy token |
| POST | `/2023-11/whatsapp-proxy/send` | Send a message via proxy |
| POST | `/2023-11/whatsapp-proxy/media` | Upload media via proxy |
| POST | `/2023-11/whatsapp-proxy/typing` | Send typing indicator via proxy |

---

## Connections

### `GET` `/2023-11/admin/whatsapp/connections`
List all WhatsApp Business connections for the current company, ordered by `is_main` descending.

```http
GET /2023-11/admin/whatsapp/connections HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "id": "01hqydxwtxej3kmzp3bz7jk999",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "facebook_user_id": "1234567890",
    "business_id": "987654321",
    "business_name": "Acme Corp",
    "waba_id": "102938475600001",
    "waba_name": "Acme Corp WABA",
    "phone_number_id": "111222333444555",
    "display_phone_number": "+237600000001",
    "verified_name": "Acme Store",
    "custom_name": null,
    "is_main": true,
    "calling_enabled": false,
    "raw_payload": {},
    "created_at": "2024-03-15T10:30:00.000000Z",
    "updated_at": "2024-03-15T10:30:00.000000Z"
  }
]
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/whatsapp/connections`
Manually register a WhatsApp Business number. The WABA must already be subscribed to the Genuka Meta app; the endpoint verifies this before creating the record.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `business_id` | string | Yes | max:255 | Meta Business Manager ID |
| `business_name` | string | No | max:255 | Human-readable business name |
| `waba_id` | string | Yes | max:255 | WhatsApp Business Account ID |
| `waba_name` | string | No | max:255 | WABA display name |
| `phone_number_id` | string | Yes | max:255 | Meta phone number ID |
| `display_phone_number` | string | Yes | max:255 | E.164 phone number shown to users |
| `verified_name` | string | No | max:255 | Meta-verified business name |
| `custom_name` | string | No | max:255 | Internal label override |
| `is_main` | boolean | No | | Set as the main outbound number |

```http
POST /2023-11/admin/whatsapp/connections HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "business_id": "987654321",
  "business_name": "Acme Corp",
  "waba_id": "102938475600001",
  "phone_number_id": "111222333444555",
  "display_phone_number": "+237600000001",
  "verified_name": "Acme Store",
  "is_main": true
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk999",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "facebook_user_id": null,
  "business_id": "987654321",
  "business_name": "Acme Corp",
  "waba_id": "102938475600001",
  "waba_name": null,
  "phone_number_id": "111222333444555",
  "display_phone_number": "+237600000001",
  "verified_name": "Acme Store",
  "custom_name": null,
  "is_main": true,
  "calling_enabled": false,
  "raw_payload": {
    "onboarding_type": "manual",
    "created_at": "2024-03-15T10:30:00+00:00"
  },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `400` — Missing `companyId` header.
- `401` — Unauthenticated.
- `422` — Connection already exists for this phone number ID, or Genuka app is not subscribed to the WABA.
- `502` — Could not reach Meta to verify WABA subscription.

---

### `PUT` `/2023-11/admin/whatsapp/connections/{connection}`
Update editable fields on a connection (name overrides and identifiers only — token is not updatable here).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `custom_name` | string | No | max:255 | Display name override |
| `business_id` | string | No | max:255 | |
| `business_name` | string | No | max:255 | |
| `waba_id` | string | No | max:255 | |
| `waba_name` | string | No | max:255 | |
| `phone_number_id` | string | No | max:255 | |
| `display_phone_number` | string | No | max:255 | |
| `verified_name` | string | No | max:255 | |

```http
PUT /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "custom_name": "Main Sales Number"
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk999",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "facebook_user_id": null,
  "business_id": "987654321",
  "business_name": "Acme Corp",
  "waba_id": "102938475600001",
  "waba_name": "Acme Corp WABA",
  "phone_number_id": "111222333444555",
  "display_phone_number": "+237600000001",
  "verified_name": "Acme Store",
  "custom_name": "Main Sales Number",
  "is_main": true,
  "calling_enabled": false,
  "raw_payload": {},
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z"
}
```

**Error responses**
- `401`, `403`, `404`, `422`

---

### `DELETE` `/2023-11/admin/whatsapp/connections/{connection}`
Delete a WhatsApp connection. Requires explicit confirmation in the request body.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `confirmation` | boolean | Yes | accepted | Must be `true` |

```http
DELETE /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "confirmation": true
}
```

**Success response** (`200 OK`):
```json
{ "message": "WhatsApp connection deleted successfully." }
```

**Error responses**
- `401`, `403`, `404`
- `422` — `confirmation` field missing or not `true`.

---

### `POST` `/2023-11/admin/whatsapp/connections/{connection}/set-main`
Designate a connection as the default (main) outbound number for the company. All other connections are unset.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999/set-main HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk999",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "facebook_user_id": null,
  "business_id": "987654321",
  "business_name": "Acme Corp",
  "waba_id": "102938475600001",
  "waba_name": "Acme Corp WABA",
  "phone_number_id": "111222333444555",
  "display_phone_number": "+237600000001",
  "verified_name": "Acme Store",
  "custom_name": null,
  "is_main": true,
  "calling_enabled": false,
  "raw_payload": {},
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T11:05:00.000000Z"
}
```

---

### `GET` `/2023-11/admin/whatsapp/connections/{connection}/coexistence-status`
Check whether the phone number is registered for both Cloud API and the WhatsApp Business App (coexistence mode).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

```http
GET /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999/coexistence-status HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "is_coexistence": true,
  "is_on_biz_app": true,
  "platform_type": "CLOUD_API"
}
```

**Error responses**
- `401`, `404`
- `500` — Meta Graph API unreachable.

---

### `POST` `/2023-11/admin/whatsapp/connections/{connection}/sync-contacts`
Trigger an asynchronous contacts synchronization from the WhatsApp Business App (coexistence). Results are delivered via webhooks.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999/sync-contacts HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "success": true,
  "request_id": "abc123def456",
  "message": "Contacts synchronization initiated. Webhooks will be sent with contact information."
}
```

**Error responses**
- `401`, `404`, `500`

---

### `POST` `/2023-11/admin/whatsapp/connections/{connection}/sync-history`
Trigger an asynchronous message history synchronization (up to 6 months). Must be called after `sync-contacts`.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999/sync-history HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "success": true,
  "request_id": "xyz789ghi000",
  "message": "Message history synchronization initiated. Webhooks will be sent with message history."
}
```

**Error responses**
- `401`, `404`, `500`

---

### `POST` `/2023-11/admin/whatsapp/connections/{connection}/sync-history-complete`
Run a full coexistence sync in sequence: contacts first, then history (dispatched with a delay). The number must be in coexistence mode.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connection` | string (ULID) | Connection ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/connections/01hqydxwtxej3kmzp3bz7jk999/sync-history-complete HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "success": true,
  "contacts_request_id": "abc123def456",
  "message": "Synchronisation lancée. Les contacts et l'historique arriveront progressivement via webhooks."
}
```

**Error responses**
- `401`, `404`
- `422` — Number is not in coexistence mode, or the 24-hour sync window has passed.
- `500` — Meta API error.

---

## OAuth / Embedded Signup

### `GET` `/2023-11/admin/whatsapp/oauth`
Returns the Facebook OAuth URL to begin the Embedded Signup flow for adding a WhatsApp Business number.

```http
GET /2023-11/admin/whatsapp/oauth HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "url": "https://business.facebook.com/messaging/whatsapp/onboard/?app_id=...&config_id=...&state=..."
}
```

---

### `POST` `/2023-11/admin/whatsapp/oauth/callback`
Exchange the authorization `code` from Embedded Signup for a long-lived token. Registers the phone number, subscribes webhooks, and initiates coexistence sync.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `code` | string | Yes | OAuth authorization code |
| `waba_id` | string | Yes | WhatsApp Business Account ID from signup flow |
| `phone_number_id` | string | Yes | Phone number ID selected during signup |
| `business_id` | string | No | Meta Business Manager ID |

```http
POST /2023-11/admin/whatsapp/oauth/callback HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "code": "AQC...",
  "waba_id": "102938475600001",
  "phone_number_id": "111222333444555",
  "business_id": "987654321"
}
```

**Success response** (`200 OK`):
```json
{ "status": "success" }
```

**Error responses**
- `400` — Missing `code` or `companyId`.
- `500` — Meta API error during token exchange.

---

### `GET` `/2023-11/whatsapp/auth/redirect`
Browser redirect to the classic Facebook OAuth URL (non-Embedded Signup flow). Not used in current production.

---

### `GET` `/2023-11/whatsapp/auth/callback`
Handles the OAuth redirect from Facebook, exchanges the code, discovers WABAs/phone numbers, and persists them. Redirects the browser to the frontend settings page.

---

## Webhooks

### `GET` `/2023-11/whatsapp/webhook`
Meta webhook verification endpoint. Meta sends a `hub.challenge` that this endpoint echoes back when the `hub.verify_token` matches.

**Query parameters**

| Name | Type | Required | Description |
|---|---|---|---|
| `hub.mode` | string | Yes | Always `subscribe` |
| `hub.challenge` | string | Yes | Random challenge string |
| `hub.verify_token` | string | Yes | Must match server-side token |

**Success response** (`200 OK`): Plain-text challenge string.

---

### `POST` `/2023-11/whatsapp/webhook`
Receive and process Meta webhook events (messages, statuses, template status updates, call events, coexistence sync events). Validates the `X-Hub-Signature-256` header. Returns `204` immediately; processing is synchronous but fast.

> **Note:** No `Authorization` or `X-Company` headers are needed — this endpoint is called directly by Meta. The `X-Hub-Signature-256` header is used for request verification.

**Incoming payload (sent by Meta)**

| Field | Type | Description |
|---|---|---|
| `object` | string | Always `"whatsapp_business_account"` |
| `entry` | array | Array of WABA entry objects |
| `entry[].id` | string | WABA ID |
| `entry[].changes` | array | Array of change objects |
| `entry[].changes[].field` | string | Event type: `messages`, `message_template_status_update`, `account_update`, `calls`, `history`, `smb_app_state_sync`, `smb_message_echoes` |
| `entry[].changes[].value` | object | Event payload — shape varies by `field` |
| `entry[].changes[].value.messaging_product` | string | Always `"whatsapp"` |
| `entry[].changes[].value.metadata.phone_number_id` | string | Meta phone number ID that received the event |
| `entry[].changes[].value.metadata.display_phone_number` | string | E.164 display number |
| `entry[].changes[].value.contacts` | array | Contact info for inbound messages: `[{ "wa_id": "...", "profile": { "name": "..." } }]` |
| `entry[].changes[].value.messages` | array | Inbound message objects (when `field=messages`) |
| `entry[].changes[].value.statuses` | array | Delivery status updates (when `field=messages`): each has `id`, `status` (`sent`\|`delivered`\|`read`\|`failed`), `timestamp`, `recipient_id` |

```http
POST /2023-11/whatsapp/webhook HTTP/1.1
X-Hub-Signature-256: sha256=<hmac>
Content-Type: application/json

{
  "object": "whatsapp_business_account",
  "entry": [{ "changes": [{ "field": "messages", "value": { ... } }] }]
}
```

**Success response** (`204 No Content`)

**Error responses**
- `403` — Invalid `X-Hub-Signature-256` signature.

---

## Packages

### `GET` `/2023-11/admin/whatsapp/packages`
Returns the product configured as the WhatsApp token purchase package, with pricing variants optionally converted to the company's currency.

```http
GET /2023-11/admin/whatsapp/packages HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): A `ProductDetailResource` shape — same schema as `GET /2023-11/admin/products/{product}` — with optional `converted_price` per variant and `source_currency` / `target_currency` fields.

```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk800",
  "name": "WhatsApp Token Pack",
  "description": "Purchase WhatsApp messaging credits.",
  "price": 5000,
  "variants": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk801",
      "name": "100 credits",
      "price": 5000,
      "converted_price": 3.05
    },
    {
      "id": "01hqydxwtxej3kmzp3bz7jk802",
      "name": "500 credits",
      "price": 20000,
      "converted_price": 12.20
    }
  ],
  "source_currency": "XAF",
  "target_currency": "USD"
}
```

---

## Campaign Templates

### `GET` `/2023-11/admin/whatsapp/templates`
List campaign templates. When `filter[channel_type]=whatsapp` (the default), templates are synced from Meta in the background (cached 30 min per company).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[channel_type]` | string | No | `whatsapp` | One of `whatsapp`, `sms`, `email` |
| `filter[name]` | string | No | | Partial name match |
| `filter[status]` | string | No | | Exact status (`pending`, `approved`, `rejected`, `paused`) |
| `filter[category]` | string | No | | Exact category (`MARKETING`, `UTILITY`, `AUTHENTICATION`) |
| `filter[language]` | string | No | | Exact language code (`en`, `fr`, etc.) |
| `filter[search]` | string | No | | Full-text search across name, description, content, components |
| `sort` | string | No | `-created_at` | Fields: `name`, `status`, `category`, `created_at`, `updated_at` |
| `per_page` | integer | No | `5` | |
| `page` | integer | No | `1` | |
| `all` | boolean | No | `false` | Include soft-deleted templates |

```http
GET /2023-11/admin/whatsapp/templates?filter[channel_type]=whatsapp&filter[status]=approved&per_page=10 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated `CampaignTemplateResource` collection.
```json
{
  "data": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk001",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "order_confirmation",
      "provider_name": "acme_corp_order_confirmation",
      "channel_type": "whatsapp",
      "category": "UTILITY",
      "language": "en",
      "status": "approved",
      "waba_id": "102938475600001",
      "components": [
        { "type": "HEADER", "format": "TEXT", "text": "Your order is confirmed!" },
        { "type": "BODY", "text": "Hi {{1}}, your order #{{2}} is confirmed." },
        { "type": "FOOTER", "text": "Conçu avec Genuka.com, pour Acme Corp." }
      ],
      "content": null,
      "description": null,
      "phone_numbers": [
        {
          "id": "01hqydxwtxej3kmzp3bz7jk999",
          "phone_number_id": "111222333444555",
          "display_phone_number": "+237600000001",
          "verified_name": "Acme Store",
          "is_main": true,
          "waba_id": "102938475600001"
        }
      ],
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 5, "total": 1 }
}
```

---

### `POST` `/2023-11/admin/whatsapp/templates`
Create a campaign template locally and submit it to the channel provider for approval (WhatsApp requires Meta review; SMS/Email auto-approve). Accepts `multipart/form-data` when uploading header media.

**Request body** (WhatsApp)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:191 | Template name (snake_case recommended) |
| `channel_type` | string | No | `whatsapp\|sms\|email` | Defaults to `whatsapp` |
| `language` | string | Yes | max:6 | BCP-47 language code, e.g. `en`, `fr` |
| `category` | string | Yes | max:50 | `MARKETING`, `UTILITY`, or `AUTHENTICATION` |
| `components` | array | Yes (WA) | | WhatsApp template components array |
| `waba_id` | string | Yes (WA) | | WABA the template belongs to |
| `content` | object | Yes (SMS/Email) | | `{ "body": "...", "subject": "..." }` |
| `medias` | array | No | | Header media uploads; each item has `collection_name`, `file`\|`src`\|`base64` |

```http
POST /2023-11/admin/whatsapp/templates HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "promo_summer_sale",
  "channel_type": "whatsapp",
  "language": "en",
  "category": "MARKETING",
  "waba_id": "102938475600001",
  "components": [
    { "type": "HEADER", "format": "TEXT", "text": "Summer Sale 🎉" },
    {
      "type": "BODY",
      "text": "Hi {{1}}, enjoy up to {{2}}% off this weekend!",
      "example": { "body_text": [["Marie", "30"]] }
    },
    {
      "type": "BUTTONS",
      "buttons": [{ "type": "URL", "text": "Shop now", "url": "https://shop.example.com" }]
    }
  ]
}
```

**Success response** (`200 OK`): `CampaignTemplateResource` — see GET single template below.

**Error responses**
- `401`, `422`
- `4xx/5xx` from Meta (provider API errors are proxied with a structured body):
```json
{
  "error": "Provider API Error",
  "message": "Template name already exists.",
  "code": 2388057,
  "fields": []
}
```

---

### `POST` `/2023-11/admin/whatsapp/templates/bulk-delete`
Delete multiple templates.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `ids` | array | Yes | Array of template IDs |

```http
POST /2023-11/admin/whatsapp/templates/bulk-delete HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "ids": ["01hqydxwtxej3kmzp3bz7jk001", "01hqydxwtxej3kmzp3bz7jk002"]
}
```

**Success response** (`200 OK`):
```json
{ "message": "Templates deleted successfully" }
```

---

### `GET` `/2023-11/admin/whatsapp/templates/{campaignTemplate}`
Get a single template. Pass `?sync=true` to refresh the approval status from Meta before returning.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `sync` | boolean | No | `false` | Re-fetch status from Meta before returning |

```http
GET /2023-11/admin/whatsapp/templates/01hqydxwtxej3kmzp3bz7jk001?sync=true HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk001",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "order_confirmation",
  "provider_name": "acme_corp_order_confirmation",
  "channel_type": "whatsapp",
  "category": "UTILITY",
  "language": "en",
  "status": "approved",
  "waba_id": "102938475600001",
  "components": [
    { "type": "HEADER", "format": "TEXT", "text": "Your order is confirmed!" },
    { "type": "BODY", "text": "Hi {{1}}, your order #{{2}} is confirmed." },
    { "type": "FOOTER", "text": "Conçu avec Genuka.com, pour Acme Corp." }
  ],
  "content": null,
  "description": null,
  "phone_numbers": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk999",
      "phone_number_id": "111222333444555",
      "display_phone_number": "+237600000001",
      "verified_name": "Acme Store",
      "is_main": true,
      "waba_id": "102938475600001"
    }
  ],
  "status_events": [
    { "id": 1, "status": "pending", "created_at": "2024-03-15T10:30:00.000000Z" },
    { "id": 2, "status": "approved", "created_at": "2024-03-16T08:00:00.000000Z" }
  ],
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-16T08:00:00.000000Z"
}
```

**Error responses**
- `401`, `403`, `404`

---

### `PUT` `/2023-11/admin/whatsapp/templates/{campaignTemplate}`
Update template fields. Pass `?resubmit=true` to re-submit the updated template to the provider for approval.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:191 | |
| `language` | string | No | max:6 | |
| `category` | string | No | max:50 | |
| `components` | array | No | | WhatsApp components |
| `content` | array | No | | SMS/Email content |
| `resubmit` | boolean | No | | Query param — triggers provider re-submission |

```http
PUT /2023-11/admin/whatsapp/templates/01hqydxwtxej3kmzp3bz7jk001?resubmit=true HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "components": [
    { "type": "HEADER", "format": "TEXT", "text": "Order Confirmed!" },
    { "type": "BODY", "text": "Hi {{1}}, your order #{{2}} has been confirmed and is being processed." },
    { "type": "FOOTER", "text": "Conçu avec Genuka.com, pour Acme Corp." }
  ]
}
```

**Success response** (`200 OK`): Updated `CampaignTemplateResource` (same shape as GET single template, with `status_events` loaded).
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk001",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "order_confirmation",
  "provider_name": "acme_corp_order_confirmation",
  "channel_type": "whatsapp",
  "category": "UTILITY",
  "language": "en",
  "status": "pending",
  "waba_id": "102938475600001",
  "components": [
    { "type": "HEADER", "format": "TEXT", "text": "Order Confirmed!" },
    { "type": "BODY", "text": "Hi {{1}}, your order #{{2}} has been confirmed and is being processed." },
    { "type": "FOOTER", "text": "Conçu avec Genuka.com, pour Acme Corp." }
  ],
  "content": null,
  "description": null,
  "phone_numbers": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk999",
      "phone_number_id": "111222333444555",
      "display_phone_number": "+237600000001",
      "verified_name": "Acme Store",
      "is_main": true,
      "waba_id": "102938475600001"
    }
  ],
  "status_events": [
    { "id": 1, "status": "pending", "created_at": "2024-03-15T10:30:00.000000Z" },
    { "id": 2, "status": "approved", "created_at": "2024-03-16T08:00:00.000000Z" },
    { "id": 3, "status": "pending", "created_at": "2024-03-17T10:00:00.000000Z" }
  ],
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-17T10:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/whatsapp/templates/{campaignTemplate}`
Soft-delete a template.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Success response** (`204 No Content`)

---

### `POST` `/2023-11/admin/whatsapp/templates/{campaignTemplate}/duplicate`
Duplicate a template (creates a copy with status `draft`).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/templates/01hqydxwtxej3kmzp3bz7jk001/duplicate HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Newly created `CampaignTemplateResource` with `"status": "draft"`.
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk003",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "order_confirmation",
  "provider_name": "acme_corp_order_confirmation_copy",
  "channel_type": "whatsapp",
  "category": "UTILITY",
  "language": "en",
  "status": "draft",
  "waba_id": "102938475600001",
  "components": [
    { "type": "HEADER", "format": "TEXT", "text": "Your order is confirmed!" },
    { "type": "BODY", "text": "Hi {{1}}, your order #{{2}} is confirmed." },
    { "type": "FOOTER", "text": "Conçu avec Genuka.com, pour Acme Corp." }
  ],
  "content": null,
  "description": null,
  "phone_numbers": [],
  "created_at": "2024-03-16T09:00:00.000000Z",
  "updated_at": "2024-03-16T09:00:00.000000Z"
}
```

---

### `POST` `/2023-11/admin/whatsapp/templates/{campaignTemplate}/preview`
Render a preview of the template with provided variable values. Substitutes `{{N}}` placeholders with the supplied values.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `variables` | object | No | Key-value map of variable index to value, e.g. `{ "1": "Marie", "2": "12345" }` |

```http
POST /2023-11/admin/whatsapp/templates/01hqydxwtxej3kmzp3bz7jk001/preview HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "variables": {
    "1": "Marie",
    "2": "12345"
  }
}
```

**Success response** (`200 OK`): Rendered component text with variables substituted.
```json
{
  "preview": {
    "header": "Your order is confirmed!",
    "body": "Hi Marie, your order #12345 is confirmed.",
    "footer": "Conçu avec Genuka.com, pour Acme Corp.",
    "buttons": []
  }
}
```

---

### `POST` `/2023-11/admin/whatsapp/templates/{campaignTemplate}/test`
Send a test message using the template to a specified phone number.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaignTemplate` | string (ULID) | Template ID |

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `to` | string | Yes | Recipient phone number (digits only, no `+`) |
| `variables` | object | No | Key-value map of variable index to value |
| `whatsapp_connection_id` | string | No | Connection ULID to use for sending; defaults to main connection |

```http
POST /2023-11/admin/whatsapp/templates/01hqydxwtxej3kmzp3bz7jk001/test HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "to": "237690000001",
  "variables": {
    "1": "Marie",
    "2": "TEST-001"
  }
}
```

**Success response** (`200 OK`):
```json
{
  "message": "Test message sent successfully.",
  "meta_message_id": "wamid.abc123def456"
}
```

**Error responses**
- `401`, `422`
- `502` — Meta rejected the test message.

---

## Campaigns

### `GET` `/2023-11/admin/whatsapp/campaigns`
List campaigns with filtering, sorting, and pagination.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[channel_type]` | string | No | | Exact: `whatsapp`, `sms`, `email` |
| `filter[scheduled_at][start]` | string | No | | ISO 8601 lower bound |
| `filter[scheduled_at][end]` | string | No | | ISO 8601 upper bound |
| `filter[id]` | string | No | | Exact campaign ID |
| `filter[search]` | string | No | | Full-text on name, description |
| `sort` | string | No | `scheduled_at` | Fields: `name`, `scheduled_at`, `status`, `created_at` |
| `include` | string | No | | Comma-separated: `company`, `campaignTemplate`, `campaignCustomers`, `customers`, `buttonClicks`, `connection` |
| `per_page` | integer | No | `15` | |
| `page` | integer | No | `1` | |

```http
GET /2023-11/admin/whatsapp/campaigns?filter[channel_type]=whatsapp&sort=-scheduled_at&per_page=10 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated `CampaignResource` collection.
```json
{
  "data": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk100",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Summer Promo 2024",
      "description": null,
      "channel_type": "whatsapp",
      "message_type": "template",
      "status": "scheduled",
      "scheduled_at": "2024-07-01T08:00:00.000000Z",
      "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
      "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
      "all_customers": false,
      "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
      "excluded_tag_ids": null,
      "cost_estimate_tokens": 120,
      "free_message_content": null,
      "variable_overrides": [],
      "created_at": "2024-06-15T10:00:00.000000Z",
      "updated_at": "2024-06-15T10:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 15, "total": 1 }
}
```

---

### `POST` `/2023-11/admin/whatsapp/campaigns`
Create a campaign. Set `status` to `scheduled` with a future `scheduled_at` to schedule, or use `launch_immediately: true` to start right away.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:255 | Campaign display name |
| `status` | string | Yes | `draft\|scheduled\|sent\|cancelled` | Initial status |
| `channel_type` | string | No | `whatsapp\|sms\|email` | Defaults to `whatsapp` |
| `campaign_template_id` | string | No | exists | Template ULID |
| `whatsapp_connection_id` | string | No | exists | Connection ULID |
| `message_type` | string | No | `template\|free_message` | Defaults to `template` |
| `free_message_content` | object | No | | Required when `message_type=free_message`; see schema below |
| `free_message_content.type` | string | Cond. | `text\|image\|video\|document\|audio\|location\|contacts\|interactive_buttons\|interactive_list\|interactive_cta` | |
| `scheduled_at` | string | No | date, after:now | ISO 8601 schedule datetime |
| `launch_immediately` | boolean | No | | Launch as soon as created |
| `customers` | array | No | | List of `{ "id": "<ULID>" }` objects |
| `tag_ids` | array | No | | Customer tag ULIDs to include |
| `excluded_tag_ids` | array | No | | Customer tag ULIDs to exclude |
| `all_customers` | boolean | No | | Target all company customers with a valid contact |
| `variable_overrides` | array | No | | Per-variable mapping; each item: `{ "variable": "1", "type": "customer\|fixed", "value": "..." }` |
| `description` | string | No | | Internal note |
| `metadata` | object | No | | Arbitrary metadata |

```http
POST /2023-11/admin/whatsapp/campaigns HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "July Promo",
  "status": "draft",
  "channel_type": "whatsapp",
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "message_type": "template",
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "variable_overrides": [
    { "variable": "1", "type": "customer", "value": "first_name" }
  ]
}
```

**Success response** (`200 OK`): `CampaignResource`.
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk100",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "July Promo",
  "description": null,
  "channel_type": "whatsapp",
  "message_type": "template",
  "status": "draft",
  "scheduled_at": null,
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "all_customers": false,
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "excluded_tag_ids": null,
  "cost_estimate_tokens": 0,
  "free_message_content": null,
  "variable_overrides": [
    { "variable": "1", "type": "customer", "value": "first_name" }
  ],
  "created_at": "2024-06-15T10:00:00.000000Z",
  "updated_at": "2024-06-15T10:00:00.000000Z"
}
```

**Error responses**
- `401`, `422`
- `500` — Internal error (transaction rolled back).

---

### `POST` `/2023-11/admin/whatsapp/campaigns/bulk-delete`
Delete multiple campaigns.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `campaign_ids` | array | Yes | Array of campaign IDs |

```http
POST /2023-11/admin/whatsapp/campaigns/bulk-delete HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "campaign_ids": ["01hqydxwtxej3kmzp3bz7jk100", "01hqydxwtxej3kmzp3bz7jk101"]
}
```

**Success response** (`204 No Content`)

---

### `GET` `/2023-11/admin/whatsapp/campaigns/{campaign}`
Get a single campaign with its template, customers, connection, and button-click stats.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

```http
GET /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): `CampaignResource` with eager-loaded relations, delivery `stats`, and `button_stats`:
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk100",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "July Promo",
  "description": null,
  "channel_type": "whatsapp",
  "message_type": "template",
  "status": "completed",
  "scheduled_at": "2024-07-01T08:00:00.000000Z",
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "all_customers": false,
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "excluded_tag_ids": null,
  "cost_estimate_tokens": 120,
  "free_message_content": null,
  "variable_overrides": [],
  "template": { "id": "01hqydxwtxej3kmzp3bz7jk001", "name": "order_confirmation", "status": "approved" },
  "whatsapp_connection": { "id": "01hqydxwtxej3kmzp3bz7jk999", "display_phone_number": "+237600000001" },
  "stats": {
    "total": 120,
    "sent": 118,
    "delivered": 110,
    "read": 87,
    "failed": 2,
    "pending": 0,
    "delivery_rate": 93.2,
    "read_rate": 79.1,
    "failure_rate": 1.7
  },
  "button_stats": {
    "total_clicks": 34,
    "unique_clickers": 29,
    "by_button": [
      { "button_id": "shop_now", "button_text": "Shop now", "clicks": 34, "unique_clickers": 29, "rate": 100.0 }
    ]
  },
  "created_at": "2024-06-15T10:00:00.000000Z",
  "updated_at": "2024-07-01T12:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/whatsapp/campaigns/{campaign}`
Update campaign fields. Setting `status` to `running` immediately dispatches the send job; setting it to `paused` or `draft` pauses it.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:255 | Campaign display name |
| `description` | string | No | | Internal note |
| `channel_type` | string | No | `whatsapp\|sms\|email` | |
| `campaign_template_id` | string | No | exists | Template ULID |
| `whatsapp_connection_id` | string | No | exists | Connection ULID |
| `message_type` | string | No | `template\|free_message` | |
| `free_message_content` | object | No | | Free-form message content |
| `status` | string | No | `draft\|scheduled\|paused\|running\|completed\|failed` | Setting `running` dispatches the send job |
| `scheduled_at` | string | No | date | ISO 8601 datetime |
| `customers` | array | No | | List of `{ "id": "<ULID>" }` |
| `tag_ids` | array | No | | Tag ULIDs to include |
| `excluded_tag_ids` | array | No | | Tag ULIDs to exclude |
| `all_customers` | boolean | No | | |
| `variable_overrides` | array | No | | Per-variable mapping array |
| `metadata` | object | No | | Arbitrary metadata |

```http
PUT /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "scheduled_at": "2024-08-01T09:00:00Z",
  "status": "scheduled"
}
```

**Success response** (`200 OK`): Updated `CampaignResource` (same shape as GET single campaign).
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk100",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "July Promo",
  "description": null,
  "channel_type": "whatsapp",
  "message_type": "template",
  "status": "scheduled",
  "scheduled_at": "2024-08-01T09:00:00.000000Z",
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "all_customers": false,
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "excluded_tag_ids": null,
  "cost_estimate_tokens": 0,
  "free_message_content": null,
  "variable_overrides": [
    { "variable": "1", "type": "customer", "value": "first_name" }
  ],
  "created_at": "2024-06-15T10:00:00.000000Z",
  "updated_at": "2024-06-20T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/whatsapp/campaigns/{campaign}`
Hard-delete a campaign.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Success response** (`204 No Content`)

---

### `GET` `/2023-11/admin/whatsapp/campaigns/{campaign}/pre-launch`
Estimate the cost and recipient count before launching. Returns credit balance check.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

```http
GET /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/pre-launch HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "credits_per_message": 0.5,
  "estimated_credits": 60.0,
  "current_balance": 200.0,
  "has_sufficient_balance": true,
  "is_free": false,
  "customers_count": 120,
  "channel_type": "whatsapp",
  "selection_method": "tags"
}
```

---

### `POST` `/2023-11/admin/whatsapp/campaigns/{campaign}/launch`
Launch the campaign immediately. Schedules or dispatches the send job.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/launch HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Updated `CampaignResource` with `"status": "running"` (same shape as GET single campaign, including `stats` and `button_stats` if customers are loaded).
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk100",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "July Promo",
  "description": null,
  "channel_type": "whatsapp",
  "message_type": "template",
  "status": "running",
  "scheduled_at": "2024-07-01T08:00:00.000000Z",
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "all_customers": false,
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "excluded_tag_ids": null,
  "cost_estimate_tokens": 120,
  "free_message_content": null,
  "variable_overrides": [],
  "template": { "id": "01hqydxwtxej3kmzp3bz7jk001", "name": "order_confirmation", "status": "approved" },
  "whatsapp_connection": { "id": "01hqydxwtxej3kmzp3bz7jk999", "display_phone_number": "+237600000001" },
  "stats": {
    "total": 120,
    "sent": 0,
    "delivered": 0,
    "read": 0,
    "failed": 0,
    "pending": 120,
    "delivery_rate": 0.0,
    "read_rate": 0.0,
    "failure_rate": 0.0
  },
  "button_stats": {
    "total_clicks": 0,
    "unique_clickers": 0,
    "by_button": []
  },
  "created_at": "2024-06-15T10:00:00.000000Z",
  "updated_at": "2024-07-01T08:00:05.000000Z"
}
```

**Error responses**
- `401`, `404`, `500`

---

### `POST` `/2023-11/admin/whatsapp/campaigns/{campaign}/cancel-scheduled`
Cancel a campaign that is in `scheduled` status. Returns the campaign to `draft` with `scheduled_at` cleared.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/cancel-scheduled HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "message": "Scheduled campaign canceled successfully",
  "campaign": {
    "id": "01hqydxwtxej3kmzp3bz7jk100",
    "name": "July Promo",
    "status": "draft",
    "scheduled_at": null,
    "channel_type": "whatsapp",
    "message_type": "template",
    "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
    "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
    "created_at": "2024-06-15T10:00:00.000000Z",
    "updated_at": "2024-06-20T09:00:00.000000Z"
  }
}
```

**Error responses**
- `400` — Campaign is not in `scheduled` status, or has no scheduled job ID.

---

### `GET` `/2023-11/admin/whatsapp/campaigns/{campaign}/pre-retry-failed`
Estimate the cost of retrying all failed messages.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Success response** (`200 OK`):
```json
{
  "credits_per_message": 0.5,
  "estimated_credits": 1.0,
  "current_balance": 200.0,
  "has_sufficient_balance": true,
  "is_free": false,
  "failed_count": 2,
  "customers_count": 2
}
```

---

### `POST` `/2023-11/admin/whatsapp/campaigns/{campaign}/retry-failed`
Retry sending to all recipients that have `status = failed`.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/retry-failed HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Updated `CampaignResource` (same shape as GET single campaign, with updated `stats`).
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk100",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "July Promo",
  "description": null,
  "channel_type": "whatsapp",
  "message_type": "template",
  "status": "completed",
  "scheduled_at": "2024-07-01T08:00:00.000000Z",
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "all_customers": false,
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "excluded_tag_ids": null,
  "cost_estimate_tokens": 120,
  "free_message_content": null,
  "variable_overrides": [],
  "template": { "id": "01hqydxwtxej3kmzp3bz7jk001", "name": "order_confirmation", "status": "approved" },
  "whatsapp_connection": { "id": "01hqydxwtxej3kmzp3bz7jk999", "display_phone_number": "+237600000001" },
  "stats": {
    "total": 120,
    "sent": 120,
    "delivered": 112,
    "read": 87,
    "failed": 0,
    "pending": 0,
    "delivery_rate": 93.3,
    "read_rate": 77.7,
    "failure_rate": 0.0
  },
  "button_stats": {
    "total_clicks": 34,
    "unique_clickers": 29,
    "by_button": [
      { "button_id": "shop_now", "button_text": "Shop now", "clicks": 34, "unique_clickers": 29, "rate": 100.0 }
    ]
  },
  "created_at": "2024-06-15T10:00:00.000000Z",
  "updated_at": "2024-07-01T13:00:00.000000Z"
}
```

---

### `POST` `/2023-11/admin/whatsapp/campaigns/{campaign}/duplicate`
Create a copy of the campaign (same settings, all recipients copied as `pending`, status set to `draft`).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/duplicate HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): New `CampaignResource` with `"status": "draft"` and `"name": "July Promo (copy)"`.
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk102",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "July Promo (copy)",
  "description": null,
  "channel_type": "whatsapp",
  "message_type": "template",
  "status": "draft",
  "scheduled_at": null,
  "campaign_template_id": "01hqydxwtxej3kmzp3bz7jk001",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "all_customers": false,
  "tag_ids": ["01hqydxwtxej3kmzp3bz7jk010"],
  "excluded_tag_ids": null,
  "cost_estimate_tokens": 0,
  "free_message_content": null,
  "variable_overrides": [],
  "created_at": "2024-06-16T10:00:00.000000Z",
  "updated_at": "2024-06-16T10:00:00.000000Z"
}
```

---

### `GET` `/2023-11/admin/whatsapp/campaigns/{campaign}/customers`
List recipients of a campaign with their delivery pivot data.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[status]` | string | No | | `pending`, `sent`, `delivered`, `read`, `failed` |
| `filter[search]` | string | No | | Search by first name, last name, email, phone |
| `filter[button_clicked]` | string | No | | Filter to customers who clicked a specific button text |
| `sort` | string | No | `-campaign_customer.created_at` | `name`, `status`, `sent_at`, `delivered_at`, `read_at` |
| `per_page` | integer | No | `15` | |
| `page` | integer | No | `1` | |

```http
GET /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/customers?filter[status]=failed HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated customer list.
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk050",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "first_name": "Marie",
      "last_name": "Dupont",
      "email": "marie.dupont@example.com",
      "phone": "+237690000001",
      "company_name": null,
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-01-10T08:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 15, "total": 2 }
}
```

---

### `POST` `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/export`
Export campaign recipients to CSV/XLSX asynchronously. Job is queued; the response returns an export job reference.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

**Query parameters**

| Name | Type | Required | Description |
|---|---|---|---|
| `format` | string | No | `csv` (default) or `xlsx` |
| `columns` | string/array | No | Specific column keys to export |
| `filter[*]` | any | No | Same filters as the customers list |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/customers/export?format=csv HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`202 Accepted`): Async export queued. The download link is sent by email when ready.
```json
{
  "status": "queued",
  "message": "Your export is being generated. You will receive an email with the download link once it is ready."
}
```

---

### `GET` `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/export/fields`
List available columns for the campaign customers export.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |

```http
GET /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/customers/export/fields HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "columns": [
    "First name",
    "Last name",
    "Company name",
    "Email",
    "Phone",
    "Contact",
    "Status",
    "Error message",
    "Sent at",
    "Delivered at",
    "Read at",
    "Failed at",
    "Cost (USD)",
    "Campaign name",
    "Channel"
  ]
}
```

---

### `GET` `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/{customer}`
Get detailed delivery information for a single recipient, including status events and button clicks.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |
| `customer` | string (ULID) | Customer ID |

```http
GET /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/customers/01hqydxwtxdj3kmzp3bz7jk050 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "customer": { "id": "01hqydxwtxdj3kmzp3bz7jk050", "first_name": "Marie", "last_name": "Dupont" },
  "conversation_id": "01hqydxwtxej3kmzp3bz7jk200",
  "campaign_customer": {
    "id": "01hqydxwtxej3kmzp3bz7jk300",
    "contact": "+237690000001",
    "status": "read",
    "sent_at": "2024-07-01T08:05:00.000000Z",
    "delivered_at": "2024-07-01T08:06:00.000000Z",
    "failed_at": null,
    "read_at": "2024-07-01T08:10:00.000000Z",
    "error_message": null,
    "cost_usd": 0.005,
    "provider_message_id": "wamid.abc123",
    "metadata": null,
    "created_at": "2024-07-01T07:00:00.000000Z",
    "updated_at": "2024-07-01T08:10:00.000000Z"
  },
  "status_events": [
    { "id": 1, "status": "sent", "created_at": "2024-07-01T08:05:00.000000Z" },
    { "id": 2, "status": "delivered", "created_at": "2024-07-01T08:06:00.000000Z" },
    { "id": 3, "status": "read", "created_at": "2024-07-01T08:10:00.000000Z" }
  ],
  "button_clicks": [
    { "id": 1, "button_id": "shop_now", "button_text": "Shop now", "clicked_at": "2024-07-01T08:12:00.000000Z" }
  ]
}
```

**Error responses**
- `401`, `403`, `404`

---

### `POST` `/2023-11/admin/whatsapp/campaigns/{campaign}/customers/{customer}/retry`
Retry sending the campaign message to a single failed recipient.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `campaign` | string (ULID) | Campaign ID |
| `customer` | string (ULID) | Customer ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/campaigns/01hqydxwtxej3kmzp3bz7jk100/customers/01hqydxwtxdj3kmzp3bz7jk050/retry HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "message": "Message resent successfully.",
  "status": "sent"
}
```

**Error responses**
- `401`, `404`
- `422` — Provider rejected the retry attempt.

---

## Proxy API

The WhatsApp Proxy API lets third-party systems send WhatsApp messages on behalf of a company using scoped bearer tokens (`InboxApiToken`). The token is passed via the standard `Authorization: Bearer` header but is a different token type from the admin session token — it is issued via the proxy token management endpoints below.

### `POST` `/2023-11/whatsapp-proxy/send`
Send a WhatsApp message using a proxy token.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `to` | string | Yes | min:7, max:20 | Recipient phone (digits only, no `+`) |
| `type` | string | Yes | `text\|template\|image\|video\|audio\|document\|sticker\|location\|contacts\|interactive\|reaction` | Message type |
| `text` | object | Cond. | | Required when `type=text`: `{ "body": "Hello!", "preview_url": false }` |
| `template` | object | Cond. | | Required when `type=template`: `{ "name": "...", "language": { "code": "en" }, "components": [...] }` |
| `image` | object | Cond. | | Required when `type=image` |
| `video` | object | Cond. | | Required when `type=video` |
| `audio` | object | Cond. | | Required when `type=audio` |
| `document` | object | Cond. | | Required when `type=document` |
| `sticker` | object | Cond. | | Required when `type=sticker` |
| `location` | object | Cond. | | Required when `type=location` |
| `contacts` | array | Cond. | | Required when `type=contacts` |
| `interactive` | object | Cond. | | Required when `type=interactive` |
| `reaction` | object | Cond. | | Required when `type=reaction` |
| `context` | object | No | | `{ "message_id": "wamid.xxx" }` to quote a message |
| `biz_opaque_callback_data` | string | No | | Opaque callback data for webhook tracking |

```http
POST /2023-11/whatsapp-proxy/send HTTP/1.1
Authorization: Bearer <proxy_token>
Content-Type: application/json
Accept: application/json

{
  "to": "237690000001",
  "type": "text",
  "text": {
    "body": "Hello from the proxy!",
    "preview_url": false
  }
}
```

**Success response** (`200 OK`):
```json
{
  "messaging_product": "whatsapp",
  "contacts": [{ "wa_id": "237690000001" }],
  "messages": [{ "id": "wamid.abc123def456" }]
}
```

**Error responses**
- `402` — Insufficient credits.
- `403` — Token lacks the required scope.
- `422` — WhatsApp connection not found.
- `502` — Meta API rejected the message.

---

### `POST` `/2023-11/whatsapp-proxy/media`
Upload a media file via the proxy. Returns a Meta media ID usable in subsequent `send` calls.

**Request body**: `multipart/form-data`

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | max:100MB | Media file to upload |

```http
POST /2023-11/whatsapp-proxy/media HTTP/1.1
Authorization: Bearer <proxy_token>
Content-Type: multipart/form-data
Accept: application/json

--boundary
Content-Disposition: form-data; name="file"; filename="promo.jpg"
Content-Type: image/jpeg

<binary data>
--boundary--
```

**Success response** (`200 OK`):
```json
{ "id": "1234567890123456" }
```

**Error responses**
- `403` — Token lacks `upload_media` scope.
- `422` — Connection not found.
- `502` — Meta upload failed.

---

### `POST` `/2023-11/whatsapp-proxy/typing`
Send a typing indicator to a recipient.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `to` | string | Yes | Recipient phone number |

```http
POST /2023-11/whatsapp-proxy/typing HTTP/1.1
Authorization: Bearer <proxy_token>
Content-Type: application/json
Accept: application/json

{
  "to": "237690000001"
}
```

**Success response** (`200 OK`):
```json
{ "success": true }
```

**Error responses**
- `403` — Token lacks `send_text` scope.
- `422` — Connection not found.
- `502` — Meta API error.

---

## Proxy Tokens (Admin)

### `GET` `/2023-11/admin/whatsapp/proxy-tokens`
List all proxy API tokens for the company.

```http
GET /2023-11/admin/whatsapp/proxy-tokens HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk400",
      "name": "N8N Integration",
      "token_prefix": "gwa_abc",
      "connection_id": "01hqydxwtxej3kmzp3bz7jk999",
      "connection_phone": "+237600000001",
      "scopes": ["send_text", "send_template", "send_media", "upload_media"],
      "rate_limit": 60,
      "is_active": true,
      "last_used_at": "2024-06-10T14:00:00+00:00",
      "expires_at": null,
      "created_at": "2024-06-01T09:00:00+00:00"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/whatsapp/proxy-tokens`
Create a proxy API token. The full plain token is returned **only once** in the creation response.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:255 | Token label |
| `connection_id` | string | No | exists | WhatsApp connection ULID to link |
| `scopes` | array | No | `send_text\|send_template\|send_media\|upload_media` | Defaults to all four scopes |
| `rate_limit` | integer | No | min:1, max:1000 | Max requests per minute (default 60) |
| `expires_at` | string | No | date, after:now | Token expiry |
| `metadata` | object | No | | Arbitrary metadata |

```http
POST /2023-11/admin/whatsapp/proxy-tokens HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "N8N Integration",
  "connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "scopes": ["send_text", "send_template"],
  "rate_limit": 30
}
```

**Success response** (`201 Created`):
```json
{
  "data": {
    "id": "01hqydxwtxej3kmzp3bz7jk400",
    "name": "N8N Integration",
    "token_prefix": "gwa_abc",
    "connection_id": "01hqydxwtxej3kmzp3bz7jk999",
    "scopes": ["send_text", "send_template"],
    "rate_limit": 30,
    "expires_at": null,
    "created_at": "2024-06-01T09:00:00+00:00"
  },
  "plain_token": "gwa_abcXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "warning": "Store this token securely. It will not be shown again."
}
```

---

### `POST` `/2023-11/admin/whatsapp/proxy-tokens/{token}/revoke`
Deactivate a token without deleting it (`is_active` set to `false`).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `token` | string (ULID) | Token ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/whatsapp/proxy-tokens/01hqydxwtxej3kmzp3bz7jk400/revoke HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Token revoked successfully." }
```

**Error responses**
- `404` — Token not found or belongs to another company.

---

### `DELETE` `/2023-11/admin/whatsapp/proxy-tokens/{token}`
Permanently delete a proxy token.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `token` | string (ULID) | Token ID |

**Success response** (`204 No Content`)

**Error responses**
- `404` — Token not found.

---

## Proxy Logs

### `GET` `/2023-11/admin/whatsapp/proxy-logs`
List proxy API request logs for the company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | No | `25` | |
| `page` | integer | No | `1` | |

```http
GET /2023-11/admin/whatsapp/proxy-logs?per_page=20 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated log collection.
```json
{
  "data": [
    {
      "id": 1001,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "token": { "id": "01hqydxwtxej3kmzp3bz7jk400", "name": "N8N Integration" },
      "connection": { "id": "01hqydxwtxej3kmzp3bz7jk999", "display_phone_number": "+237600000001" },
      "message_type": "text",
      "to": "237690000001",
      "status": "success",
      "meta_message_id": "wamid.abc123",
      "credits_charged": 0.5,
      "created_at": "2024-06-10T14:00:00Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": "..." },
  "meta": { "current_page": 1, "per_page": 25, "total": 42 }
}
```

---

## Calling (WhatsApp Calls)

WhatsApp Calling is powered by a FreeSWITCH media server. The browser client uses the Verto WebRTC protocol. Call state changes are broadcast via Laravel Reverb (WebSockets).

### `GET` `/2023-11/whatsapp/calls`
List calls for the company. By default returns only active calls (`ringing`, `connecting`, `in_progress`).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `active_only` | boolean | No | `true` | Set `false` to include ended/completed calls |

```http
GET /2023-11/whatsapp/calls?active_only=false HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Array of `CallResource`:
```json
{
  "data": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk500",
      "external_call_id": "fs-uuid-1234",
      "direction": "outbound",
      "status": "in_progress",
      "is_active": true,
      "is_terminal": false,
      "end_reason": null,
      "from_number": "+237600000001",
      "to_number": "237690000002",
      "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
      "conversation_id": "01hqydxwtxej3kmzp3bz7jk200",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "initiated_by_user_id": "01hqydxwtxdj3kmzp3bz7jk001",
      "accepted_by_user_id": null,
      "started_at": "2024-06-15T10:30:00+00:00",
      "accepted_at": null,
      "ended_at": null,
      "duration_seconds": null,
      "recording_status": "none",
      "recording_uploaded_at": null,
      "recording_deleted_at": null,
      "freeswitch_conference_name": "genuka_01hqydx",
      "participants": [
        {
          "id": "01hqydxwtxej3kmzp3bz7jk600",
          "user_id": "01hqydxwtxdj3kmzp3bz7jk001",
          "user": { "id": "01hqydxwtxdj3kmzp3bz7jk001", "name": "Wilfried D.", "avatar_url": null },
          "role": "initiator",
          "status": "active",
          "joined_at": "2024-06-15T10:30:00+00:00",
          "left_at": null,
          "mute_count": 0,
          "unmute_count": 0,
          "was_kicked": false
        }
      ],
      "participants_count": 1,
      "can": {
        "join": true,
        "barge_in": false,
        "terminate": true,
        "view_recording": false,
        "delete_recording": false,
        "download_recording": false
      },
      "created_at": "2024-06-15T10:30:00+00:00",
      "updated_at": "2024-06-15T10:30:00+00:00"
    }
  ]
}
```

---

### `POST` `/2023-11/whatsapp/calls`
Initiate an outbound call. Requires the `call.initiate` permission. The user is subject to a daily outbound quota.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `whatsapp_connection_id` | string | Yes | exists, belongs to company | Connection used for the outbound call |
| `conversation_id` | string | Yes | exists, belongs to company | Inbox conversation the call is linked to |
| `to_number` | string | Yes | 8–15 digits, no `+` | E.164 number without the leading `+` |

```http
POST /2023-11/whatsapp/calls HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "conversation_id": "01hqydxwtxej3kmzp3bz7jk200",
  "to_number": "237690000002"
}
```

**Success response** (`201 Created`): `CallResource` (same shape as the listing above).
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk500",
  "external_call_id": "fs-uuid-5678",
  "direction": "outbound",
  "status": "ringing",
  "is_active": true,
  "is_terminal": false,
  "end_reason": null,
  "from_number": "+237600000001",
  "to_number": "237690000002",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "conversation_id": "01hqydxwtxej3kmzp3bz7jk200",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "initiated_by_user_id": "01hqydxwtxdj3kmzp3bz7jk001",
  "accepted_by_user_id": null,
  "started_at": "2024-06-15T10:30:00+00:00",
  "accepted_at": null,
  "ended_at": null,
  "duration_seconds": null,
  "recording_status": "none",
  "recording_uploaded_at": null,
  "recording_deleted_at": null,
  "freeswitch_conference_name": "genuka_01hqydx",
  "participants": [],
  "participants_count": 0,
  "can": {
    "join": true,
    "barge_in": false,
    "terminate": true,
    "view_recording": false,
    "delete_recording": false,
    "download_recording": false
  },
  "created_at": "2024-06-15T10:30:00+00:00",
  "updated_at": "2024-06-15T10:30:00+00:00"
}
```

**Error responses**
- `401`, `403` — Missing `call.initiate` permission.
- `412` — Calling not enabled on the connection, or number not eligible.
- `423` — Outbound paused due to unanswered calls threshold.
- `429` — Daily outbound quota exceeded.
- `503` — FreeSWITCH media server unavailable.

---

### `GET` `/2023-11/whatsapp/calls/ice-servers`
Get STUN/TURN ICE server credentials (short-lived, ~5 minutes). Used by the browser WebRTC client before joining a call.

```http
GET /2023-11/whatsapp/calls/ice-servers HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "iceServers": [
    { "urls": "stun:media.genuka.com:3478" },
    {
      "urls": "turn:media.genuka.com:3478",
      "username": "user_abc123",
      "credential": "secret_xyz"
    }
  ],
  "expires_at": "2024-06-15T10:35:00Z"
}
```

**Error responses**
- `401`
- `403` — Missing `call.listen` or `call.initiate` permission.

---

### `GET` `/2023-11/whatsapp/calls/{call}`
Get details of a single call.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `call` | string (ULID) | Call ID |

**Success response** (`200 OK`): `CallResource` (same shape as the listing above).
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk500",
  "external_call_id": "fs-uuid-1234",
  "direction": "outbound",
  "status": "completed",
  "is_active": false,
  "is_terminal": true,
  "end_reason": "normal_clearing",
  "from_number": "+237600000001",
  "to_number": "237690000002",
  "whatsapp_connection_id": "01hqydxwtxej3kmzp3bz7jk999",
  "conversation_id": "01hqydxwtxej3kmzp3bz7jk200",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "initiated_by_user_id": "01hqydxwtxdj3kmzp3bz7jk001",
  "accepted_by_user_id": "01hqydxwtxdj3kmzp3bz7jk001",
  "started_at": "2024-06-15T10:30:00+00:00",
  "accepted_at": "2024-06-15T10:30:15+00:00",
  "ended_at": "2024-06-15T10:35:42+00:00",
  "duration_seconds": 327,
  "recording_status": "uploaded",
  "recording_uploaded_at": "2024-06-15T10:36:10+00:00",
  "recording_deleted_at": null,
  "freeswitch_conference_name": "genuka_01hqydx",
  "participants": [
    {
      "id": "01hqydxwtxej3kmzp3bz7jk600",
      "user_id": "01hqydxwtxdj3kmzp3bz7jk001",
      "user": { "id": "01hqydxwtxdj3kmzp3bz7jk001", "name": "Wilfried D.", "avatar_url": null },
      "role": "initiator",
      "status": "left",
      "joined_at": "2024-06-15T10:30:00+00:00",
      "left_at": "2024-06-15T10:35:42+00:00",
      "mute_count": 1,
      "unmute_count": 1,
      "was_kicked": false
    }
  ],
  "participants_count": 1,
  "can": {
    "join": false,
    "barge_in": false,
    "terminate": false,
    "view_recording": true,
    "delete_recording": false,
    "download_recording": false
  },
  "created_at": "2024-06-15T10:30:00+00:00",
  "updated_at": "2024-06-15T10:36:10+00:00"
}
```

**Error responses**
- `401`, `404`

---

### `POST` `/2023-11/whatsapp/calls/{call}/terminate`
End the call for all participants. Requires the `call.terminate` permission.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `call` | string (ULID) | Call ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/whatsapp/calls/01hqydxwtxej3kmzp3bz7jk500/terminate HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Updated `CallResource` with terminal status.
```json
{
  "id": "01hqydxwtxej3kmzp3bz7jk500",
  "direction": "outbound",
  "status": "completed",
  "is_active": false,
  "is_terminal": true,
  "end_reason": "terminated_by_agent",
  "ended_at": "2024-06-15T10:33:00+00:00",
  "duration_seconds": 180,
  "recording_status": "none",
  "participants_count": 1,
  "can": {
    "join": false,
    "barge_in": false,
    "terminate": false,
    "view_recording": false,
    "delete_recording": false,
    "download_recording": false
  }
}
```

**Error responses**
- `401`, `403`, `404`

---

### `POST` `/2023-11/whatsapp/calls/{call}/verto-credentials`
Issue short-lived Verto WebRTC credentials for the authenticated user to join the call's conference.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `call` | string (ULID) | Call ID |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/whatsapp/calls/01hqydxwtxej3kmzp3bz7jk500/verto-credentials HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "login": "user_01hqydxwtxdj3kmzp3bz7jk001",
  "passwd": "<short_lived_jwt>",
  "sessid": "sess_abc123",
  "wss_url": "wss://media.genuka.com:8082",
  "conference": "genuka_01hqydx"
}
```

**Error responses**
- `401`, `404` — Call not found or user is not an active participant.

---

## FreeSWITCH Internal Endpoints

These endpoints are protected by HTTP Basic Auth and an IP allowlist (`FreeSwitchInternalAuth` middleware). They are called exclusively by the FreeSWITCH media server and are not intended for external use.

### `POST` `/2023-11/whatsapp/calls/freeswitch/dialplan`
XML cURL dynamic dialplan handler. FreeSWITCH POSTs the leg's channel variables as a `application/x-www-form-urlencoded` body. Returns `<not found/>` to fall back to the static dialplan file (`genuka_calls.xml`) in the current phase; future phases will return dynamic XML for conference routing.

> **Note:** This endpoint is protected by `FreeSwitchInternalAuth` (HTTP Basic Auth + IP allowlist). It is called exclusively by the FreeSWITCH media server — do not call it directly.

**Request body** (`application/x-www-form-urlencoded` — sent by FreeSWITCH):

| Field | Type | Description |
|---|---|---|
| `Caller-Destination-Number` | string | The dialed number / conference name being routed |
| `Unique-ID` | string | FreeSWITCH channel UUID for this leg |
| `Caller-Caller-ID-Number` | string | Originating caller ID number |
| `Caller-Caller-ID-Name` | string | Originating caller ID name |
| `domain_name` | string | FreeSWITCH SIP domain |
| `variable_*` | string | Additional channel variables prefixed with `variable_` |

> **Response format:** `text/xml` — not JSON. The response body is a FreeSWITCH XML document (see example below).

```http
POST /2023-11/whatsapp/calls/freeswitch/dialplan HTTP/1.1
Authorization: Basic <base64_credentials>
Content-Type: application/x-www-form-urlencoded

Caller-Destination-Number=genuka_01hqydx&Unique-ID=fs-uuid-1234&domain_name=media.genuka.com
```

**Success response** (`200 OK`, `Content-Type: text/xml`):
```xml
<?xml version="1.0"?>
<document type="freeswitch/xml">
  <section name="result">
    <result status="not found"/>
  </section>
</document>
```

---

### `POST` `/2023-11/whatsapp/calls/freeswitch/directory`
XML cURL directory handler. FreeSWITCH `mod_verto` calls this endpoint to authenticate a Verto WebRTC login before the dialplan runs. The endpoint looks up the short-lived token cached by `VertoTokenService` for the given login and echoes it back as the expected cleartext password. Returns `<not found/>` for unknown or expired sessions, causing FreeSWITCH to reject the connection.

> **Note:** This endpoint is protected by `FreeSwitchInternalAuth` (HTTP Basic Auth + IP allowlist). It is called exclusively by the FreeSWITCH media server — do not call it directly.

**Request body** (`application/x-www-form-urlencoded` — sent by FreeSWITCH):

| Field | Type | Description |
|---|---|---|
| `user` | string | Verto login identifier, e.g. `user_<ulid>` |
| `domain` | string | FreeSWITCH SIP domain, e.g. `media.genuka.com` |
| `purpose` | string | Directory lookup purpose (e.g. `gateways`, `network-list`, `dialplan`) |
| `action` | string | FreeSWITCH directory action |

> **Response format:** `text/xml` — not JSON. The response body is a FreeSWITCH XML document (see examples below).

```http
POST /2023-11/whatsapp/calls/freeswitch/directory HTTP/1.1
Authorization: Basic <base64_credentials>
Content-Type: application/x-www-form-urlencoded

user=user_01hqydxwtxdj3kmzp3bz7jk001&domain=media.genuka.com&action=sip_auth&purpose=auth
```

**Success response — user found** (`200 OK`, `Content-Type: text/xml`):
```xml
<?xml version="1.0"?>
<document type="freeswitch/xml">
  <section name="directory">
    <domain name="media.genuka.com">
      <user id="user_01hqydxwtxdj3kmzp3bz7jk001">
        <params>
          <param name="password" value="<short_lived_token>"/>
          <param name="jsonrpc-allowed-methods" value="verto"/>
        </params>
        <variables>
          <variable name="user_context" value="genuka_calls"/>
        </variables>
      </user>
    </domain>
  </section>
</document>
```

**Response — user not found / token expired** (`200 OK`, `Content-Type: text/xml`):
```xml
<?xml version="1.0"?>
<document type="freeswitch/xml">
  <section name="result">
    <result status="not found"/>
  </section>
</document>
```

### `POST` `/2023-11/whatsapp/calls/freeswitch/event`
HTTP fallback for FS events when the ESL connection is briefly unavailable. Handles `CHANNEL_HANGUP_COMPLETE` to mark calls as completed.

**Request body** (JSON):

| Field | Type | Description |
|---|---|---|
| `event` | string | FS event name, e.g. `CHANNEL_HANGUP_COMPLETE` |
| `call_id` | string | Genuka call ULID |
| `uuid` | string | FreeSWITCH leg UUID |
| `hangup_cause` | string | FS hangup cause code |
| `billsec` | integer | Billed seconds |

**Success response** (`204 No Content`)

### `POST` `/2023-11/whatsapp/calls/freeswitch/recording-uploaded`
Acknowledgment that a call recording has been uploaded to S3. Logs the confirmation.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `call_id` | string (ULID) | Yes | Genuka call ID |
| `remote_path` | string | Yes | S3 path where the recording was uploaded, e.g. `s3://bucket/recordings/call.mp3` |

**Success response** (`200 OK`):
```json
{ "ok": true }
```

---

## Miscellaneous

### `GET` `/2023-11/whatsapp`
Entry-point placeholder (the `index` action). Currently maps to `WhatsappAPIController@index` which is not implemented — returns `500` if called directly.

### `GET` `/2023-11/whatsapp/login`
Renders the `whatsapp.login` Blade view. Browser-only; not an API endpoint.

### `GET` `/whatsapp/sync/{company}`
Renders the `whatsapp.login` Blade view for a specific company (legacy sync page). Browser-only.
