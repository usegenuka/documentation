---
title: "Payments & Point of Sale"
description: "This domain covers four related resources. **Payments** are the financial transactions attached to invoices, bills, and credit notes. **Payment methods** config"
sidebarTitle: "Payments & POS"
---

This domain covers four related resources. **Payments** are the financial transactions attached to invoices, bills, and credit notes. **Payment methods** configure which payment processors (Stripe, NotchPay, cash, etc.) are active for a company. **Cash register sessions** model the full POS cash-control lifecycle — open, close, approve/reject differences. **Cash denominations** are the reference list of bills and coins used when counting a cash drawer.

All cash-register endpoints require the `cash_control.enabled` company setting to be `true`; they return `403` otherwise.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/admin/payments` | List payments |
| `POST` | `/2023-11/admin/payments/export` | Async-export payments to XLSX/PDF |
| `GET` | `/2023-11/admin/payments/export/fields` | List available export column names |
| `GET` | `/2023-11/admin/payments/{payment}` | Get a single payment |
| `GET` | `/2023-11/admin/payment-methods` | List payment methods |
| `POST` | `/2023-11/admin/payment-methods` | Create (or upsert) a payment method |
| `GET` | `/2023-11/admin/payment-methods/stripe/connect` | Start Stripe Connect OAuth flow |
| `POST` | `/2023-11/admin/payment-methods/stripe/direct-keys` | Save Stripe direct API keys |
| `GET` | `/2023-11/admin/payment-methods/stripe/webhook-url` | Get the Stripe direct webhook URL |
| `GET` | `/2023-11/admin/payment-methods/{paymentMethod}` | Get a single payment method |
| `PUT` | `/2023-11/admin/payment-methods/{paymentMethod}` | Update a payment method |
| `DELETE` | `/2023-11/admin/payment-methods/{paymentMethod}` | Delete a payment method |
| `GET` | `/2023-11/admin/cash-register-sessions` | List cash register sessions |
| `POST` | `/2023-11/admin/cash-register-sessions` | Open a cash register session |
| `GET` | `/2023-11/admin/cash-register-sessions/active` | List currently-open sessions |
| `GET` | `/2023-11/admin/cash-register-sessions/enums` | Get cash-register enum values |
| `GET` | `/2023-11/admin/cash-register-sessions/statistics` | Aggregate session statistics |
| `GET` | `/2023-11/admin/cash-register-sessions/{session}` | Get a single session with summary |
| `POST` | `/2023-11/admin/cash-register-sessions/{session}/close` | Close a session |
| `POST` | `/2023-11/admin/cash-register-sessions/{session}/approve-difference` | Approve a cash difference |
| `POST` | `/2023-11/admin/cash-register-sessions/{session}/reject-closure` | Reject a closure and reopen |
| `GET` | `/2023-11/admin/cash-register-sessions/{session}/transactions` | List transactions for a session |
| `GET` | `/2023-11/admin/cash-register-sessions/{session}/history` | List audit history for a session |
| `GET` | `/2023-11/admin/cash-denominations` | List cash denominations for a currency |

---

## Payments

### `GET` `/2023-11/admin/payments`

List all payments for the authenticated company, with rich filtering and sorting.

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `page` | integer | No | `1` | Page number |
  | `per_page` | integer | No | `15` | Results per page (max 100) |
  | `include` | string | No | — | Comma-separated relations: `address`, `user`, `bookEntries`, `payable`, `company`, `treasuryAccount` |
  | `filter[payable_id]` | string (ULID) | No | — | Exact match on the payable's ID |
  | `filter[payable_type]` | string | No | — | Payable class, e.g. `App\Models\Invoice` |
  | `filter[type]` | string | No | — | `inbound` or `outbound` |
  | `filter[treasury_account_id]` | string (ULID) | No | — | Exact match on treasury account |
  | `filter[order_id]` | string (ULID) | No | — | Payments whose invoice belongs to this order |
  | `filter[invoice_id]` | string (ULID) | No | — | Payments whose payable is this invoice |
  | `filter[credit_note_id]` | string (ULID) | No | — | Payments whose payable is this credit note |
  | `filter[search]` | string | No | — | Full-text search across method, reference, amount, supplier/customer name, invoice reference |
  | `filter[created_at][start]` | string (ISO 8601) | No | — | Filter by creation date range start |
  | `filter[created_at][end]` | string (ISO 8601) | No | — | Filter by creation date range end |
  | `filter[updated_at][start]` | string (ISO 8601) | No | — | Filter by update date range start |
  | `filter[updated_at][end]` | string (ISO 8601) | No | — | Filter by update date range end |
  | `sort` | string | No | — | One of `reference`, `type`, `amount`, `created_at`, `updated_at`. Prefix with `-` for descending |
  | `whereColumn` | string | No | — | Low-level column filter (use `whereOperator` + `whereValue`) |
  | `whereOperator` | string | No | — | SQL operator, e.g. `=`, `>`, `in` |
  | `whereValue` | string | No | — | Value for `whereColumn`; JSON array string when `whereOperator` is `in` |

Request example:

```http
GET /2023-11/admin/payments?filter[type]=inbound&filter[order_id]=01k76xt7z87katy392s6w335cp&sort=-created_at&page=1&per_page=10 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01k1amyq08htzkeg79xd7f9pct",
      "user_id": "01hphmgyze0etams9dx505f9nh",
      "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
      "address_id": null,
      "company_id": "01hphmgyyxa5t7vmptkt7353vs",
      "payable_id": "01k76xt7z87katy392s6w335cp",
      "payable_type": "App\\Models\\Invoice",
      "type": "inbound",
      "status": "paid",
      "amount": 160.00,
      "currency": null,
      "currency_code": "XAF",
      "method": "bank",
      "reference": "PAY-2024-0042",
      "due_date": null,
      "paid_at": "2024-03-15T10:23:45.000000Z",
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "address": null,
      "user": null,
      "created_by_user": null,
      "treasuryAccount": {
        "id": "01jbxvcy88h9w33qcta4p5e1z8",
        "label": "Banque principale",
        "type": "bank",
        "balance": 4500.00,
        "currency_code": "XAF"
      },
      "payable": null
    }
  ],
  "pagination": {
    "total": 284,
    "count": 10,
    "per_page": 10,
    "current_page": 1,
    "total_pages": 29
  }
}
```

Error responses:
- `400` — missing `X-Company` header (`company_id_required`)
- `401` — invalid or expired token
- `403` — insufficient scope

---

### `GET` `/2023-11/admin/payments/export/fields`

Return the list of column names that can be requested in the export.

Request example:

```http
GET /2023-11/admin/payments/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "columns": [
    "id",
    "reference",
    "amount",
    "method",
    "type",
    "status",
    "paid_at",
    "created_at"
  ]
}
```

---

### `POST` `/2023-11/admin/payments/export`

Trigger an async export of payments. The same filter/sort parameters accepted by the list endpoint can be sent in the body. Returns immediately with a job reference; the file is delivered asynchronously (e.g. by email or notification).

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `format` | string | No | `xlsx` \| `pdf` | Output format; defaults to `xlsx` |
  | `columns` | array of strings | No | Values from `/export/fields` | Columns to include; all columns included if omitted |
  | `filter[*]` | mixed | No | Same as list endpoint | Any filter supported by the payments list |
  | `sort` | string | No | Same as list endpoint | Sort order for the export |

Request example:

```http
POST /2023-11/admin/payments/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "format": "xlsx",
  "columns": ["reference", "amount", "method", "paid_at"],
  "filter[type]": "inbound",
  "filter[created_at][start]": "2024-01-01T00:00:00Z",
  "filter[created_at][end]": "2024-03-31T23:59:59Z"
}
```

Success response — varies by async export handler (typically `200 OK` with a job/download reference or a direct file download):

```json
{
  "message": "Export started. You will receive a notification when it is ready.",
  "job_id": "01k1amyq08htzkeg79xd7f9pct"
}
```

Error responses:
- `401` — not authenticated
- `403` — insufficient scope

---

### `GET` `/2023-11/admin/payments/{payment}`

Return a single payment with its full set of relationships loaded (payable, treasury account, address, creator, and — for invoice payables — the linked order).

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `payment` | string (ULID) | ID of the payment |

Request example:

```http
GET /2023-11/admin/payments/01k1amyq08htzkeg79xd7f9pct HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01k1amyq08htzkeg79xd7f9pct",
  "user_id": "01hphmgyze0etams9dx505f9nh",
  "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
  "address_id": null,
  "company_id": "01hphmgyyxa5t7vmptkt7353vs",
  "payable_id": "01k76xt7z87katy392s6w335cp",
  "payable_type": "App\\Models\\Invoice",
  "type": "inbound",
  "status": "paid",
  "amount": 160.00,
  "currency": null,
  "currency_code": "XAF",
  "method": "bank",
  "reference": "PAY-2024-0042",
  "due_date": null,
  "paid_at": "2024-03-15T10:23:45.000000Z",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "address": null,
  "user": {
    "id": "01hphmgyze0etams9dx505f9nh",
    "name": "Wilfried Djopa"
  },
  "created_by_user": {
    "id": "01hphmgyze0etams9dx505f9nh",
    "name": "Wilfried Djopa"
  },
  "treasuryAccount": {
    "id": "01jbxvcy88h9w33qcta4p5e1z8",
    "label": "Banque principale",
    "type": "bank",
    "balance": 4500.00,
    "currency_code": "XAF"
  },
  "payable": {
    "id": "01k76xt7z87katy392s6w335cp",
    "reference": "INV-2024-0101",
    "order": {
      "id": "01k76xt7z87katy392s6w335cp",
      "reference": "#ORD-0101"
    },
    "created_by_user": {
      "id": "01hphmgyze0etams9dx505f9nh",
      "name": "Wilfried Djopa"
    }
  }
}
```

Error responses:
- `401` — invalid or expired token
- `403` — payment belongs to a different company
- `404` — payment not found

---

## Payment Methods

### `GET` `/2023-11/admin/payment-methods`

List all payment methods configured for the company. Each record includes its linked treasury account and non-secret configuration values (sensitive keys such as `secret`, `private`, `token` are masked with `********************************`).

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `page` | integer | No | `1` | Page number |
  | `per_page` | integer | No | `5` | Results per page |
  | `include` | string | No | — | `treasuryAccount` |

Request example:

```http
GET /2023-11/admin/payment-methods?per_page=10&include=treasuryAccount HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01jbxvcy88h9w33qcta4p5e1z8",
      "company_id": "01hphmgyyxa5t7vmptkt7353vs",
      "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
      "name": "Stripe",
      "account_id": "acct_1AbCdEfGhIjKlMnO",
      "processor": "stripe",
      "status": true,
      "metadata": null,
      "configurations": {
        "mode": "direct",
        "stripe_account_id": "acct_1AbCdEfGhIjKlMnO",
        "publishable_key": "pk_live_...",
        "secret_key": "********************************",
        "webhook_secret": "********************************"
      },
      "treasury_account": {
        "id": "01jbxvcy88h9w33qcta4p5e1z8",
        "label": "Stripe",
        "type": "stripe",
        "balance": 0.00,
        "currency_code": "XAF"
      }
    }
  ],
  "pagination": {
    "total": 3,
    "count": 3,
    "per_page": 5,
    "current_page": 1,
    "total_pages": 1
  }
}
```

---

### `POST` `/2023-11/admin/payment-methods`

Create a payment method for the company, or update it if one already exists for the same processor. A matching treasury account is created automatically if none exists.

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `processor` | string | Yes | One of `cash`, `notchpay`, `stripe`, `paydunya`, `taramoney`, `pawapay`, `paypal`, `mamoni`, `flutterwave`, `genuka_pay` | Payment processor identifier |
  | `configurations` | object | No | Key-value pairs | Processor-specific settings (stored encrypted) |
  | `account_id` | string | No | — | External account identifier (e.g. Stripe account ID) |
  | `status` | boolean | No | — | Enable (`true`) or disable (`false`) the method |
  | `metadata` | object | No | — | Arbitrary metadata |

Request example:

```http
POST /2023-11/admin/payment-methods HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "processor": "notchpay",
  "configurations": {
    "public_key": "pk_sandbox_xxxxxxxxxxxx",
    "secret_key": "sk_sandbox_xxxxxxxxxxxx"
  },
  "status": true
}
```

Success response `201 Created`:

```json
{
  "id": "01jbxvcy88h9w33qcta4p5e1z8",
  "company_id": "01hphmgyyxa5t7vmptkt7353vs",
  "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
  "name": "NotchPay",
  "account_id": null,
  "processor": "notchpay",
  "status": true,
  "metadata": null,
  "configurations": {
    "public_key": "pk_sandbox_xxxxxxxxxxxx",
    "secret_key": "********************************"
  }
}
```

Error responses:
- `400` — `processor` value is not in the allowed list
- `500` — internal error during creation (transaction rolled back)

---

### `GET` `/2023-11/admin/payment-methods/stripe/connect`

Initiate the Stripe Connect OAuth flow. Returns a Stripe-hosted authorization URL. Redirect the user's browser to the returned `url` to begin the connection.

Request example:

```http
GET /2023-11/admin/payment-methods/stripe/connect HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "url": "https://connect.stripe.com/oauth/authorize?response_type=code&scope=read_write&client_id=ca_xxx&redirect_uri=https%3A%2F%2Fapi.genuka.com%2F...&state=abc123%3A01hphmgyyxa5t7vmptkt7353vs"
}
```

Error responses:
- `401` — invalid or expired token

---

### `POST` `/2023-11/admin/payment-methods/stripe/direct-keys`

Save Stripe direct-mode credentials (publishable key, secret key, webhook secret). The secret key is validated against the Stripe API before saving. Switches the Stripe payment method to `mode=direct`.

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `publishable_key` | string | Yes | Must start with `pk_` | Stripe publishable key |
  | `secret_key` | string | Yes | Must be a valid Stripe secret key | Stripe secret key |
  | `webhook_secret` | string | Yes | Must start with `whsec_` | Stripe webhook signing secret |

Request example:

```http
POST /2023-11/admin/payment-methods/stripe/direct-keys HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "publishable_key": "pk_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "secret_key": "sk_live_<your_secret_key>",
  "webhook_secret": "whsec_xxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

Success response `200 OK`:

```json
{
  "payment_method": {
    "id": "01jbxvcy88h9w33qcta4p5e1z8",
    "company_id": "01hphmgyyxa5t7vmptkt7353vs",
    "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
    "name": "Stripe",
    "account_id": "acct_1AbCdEfGhIjKlMnO",
    "processor": "stripe",
    "status": true,
    "metadata": null
  },
  "webhook_url": "https://api.genuka.com/2023-11/stripe/webhooks/01jbxvcy88h9w33qcta4p5e1z8"
}
```

Error responses:
- `422` — invalid Stripe secret key (authentication failed) or validation error
- `401` — invalid or expired token

---

### `GET` `/2023-11/admin/payment-methods/stripe/webhook-url`

Retrieve the webhook URL the merchant must register in their Stripe dashboard when using direct-mode integration. Returns `null` if no Stripe payment method exists for the company.

Request example:

```http
GET /2023-11/admin/payment-methods/stripe/webhook-url HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "webhook_url": "https://api.genuka.com/2023-11/stripe/webhooks/01jbxvcy88h9w33qcta4p5e1z8"
}
```

When no Stripe method exists:

```json
{
  "webhook_url": null
}
```

---

### `GET` `/2023-11/admin/payment-methods/{paymentMethod}`

Return a single payment method by ID.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `paymentMethod` | string (ULID) | ID of the payment method |

Request example:

```http
GET /2023-11/admin/payment-methods/01jbxvcy88h9w33qcta4p5e1z8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01jbxvcy88h9w33qcta4p5e1z8",
  "company_id": "01hphmgyyxa5t7vmptkt7353vs",
  "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
  "name": "Cash",
  "account_id": null,
  "processor": "cash",
  "status": true,
  "metadata": null,
  "configurations": {}
}
```

Error responses:
- `404` — payment method not found
- `401` — invalid or expired token

---

### `PUT` `/2023-11/admin/payment-methods/{paymentMethod}`

Update an existing payment method. Only the provided fields are modified.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `paymentMethod` | string (ULID) | ID of the payment method |

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `account_id` | string | No | — | External account identifier |
  | `status` | boolean | No | — | Enable or disable the method |
  | `metadata` | object | No | — | Arbitrary metadata |
  | `configurations` | object | No | — | Key-value pairs to upsert (sensitive values stored encrypted) |
  | `processor` | string | No | One of the valid processor values | If provided, must be a valid `PaymentMethods` enum value |

Request example:

```http
PUT /2023-11/admin/payment-methods/01jbxvcy88h9w33qcta4p5e1z8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "status": false,
  "metadata": {
    "disabled_reason": "switching provider"
  }
}
```

Success response `200 OK`:

```json
{
  "id": "01jbxvcy88h9w33qcta4p5e1z8",
  "company_id": "01hphmgyyxa5t7vmptkt7353vs",
  "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
  "name": "Stripe",
  "account_id": "acct_1AbCdEfGhIjKlMnO",
  "processor": "stripe",
  "status": false,
  "metadata": {
    "disabled_reason": "switching provider"
  },
  "configurations": {
    "mode": "direct",
    "stripe_account_id": "acct_1AbCdEfGhIjKlMnO",
    "publishable_key": "pk_live_...",
    "secret_key": "********************************",
    "webhook_secret": "********************************"
  }
}
```

Error responses:
- `400` — `processor` value is not in the allowed list
- `404` — payment method not found
- `401` — invalid or expired token

---

### `DELETE` `/2023-11/admin/payment-methods/{paymentMethod}`

Delete a payment method. Returns an empty `200 OK` response.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `paymentMethod` | string (ULID) | ID of the payment method |

Request example:

```http
DELETE /2023-11/admin/payment-methods/01jbxvcy88h9w33qcta4p5e1z8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

**Response:** `200 OK` — empty body (the controller returns no JSON on successful deletion).

Error responses:
- `404` — payment method not found
- `401` — invalid or expired token

---

## Cash Register Sessions

> **Feature flag:** all cash register session endpoints require the company setting `cash_control.enabled = true`. If disabled, every request to this resource returns `403 Forbidden` with `{"message": "Cash control is disabled for this company."}`.

### `GET` `/2023-11/admin/cash-register-sessions`

List cash register sessions for the company, sorted by most-recently-opened first.

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `page` | integer | No | `1` | Page number |
  | `per_page` | integer | No | `15` | Results per page |
  | `paginate` | string | No | — | Pass `false` to return all records without pagination |
  | `include` | string | No | — | Comma-separated: `treasuryAccount`, `user`, `approvedBy`, `shop`, `company`, `transactions`, `history` |
  | `filter[treasury_account_id]` | string (ULID) | No | — | Filter by treasury account |
  | `filter[user_id]` | string (ULID) | No | — | Filter by user who opened the session |
  | `filter[shop_id]` | string (ULID) | No | — | Filter by shop |
  | `filter[company_id]` | string (ULID) | No | — | Filter by company |
  | `filter[status]` | string | No | — | `opened`, `pending_approval`, `closed`, or `rejected` |
  | `filter[created_at][start]` | string (ISO 8601) | No | — | Session opened_at range start |
  | `filter[created_at][end]` | string (ISO 8601) | No | — | Session opened_at range end |
  | `filter[search]` | string | No | — | Search by treasury account label or user name |
  | `sort` | string | No | `-opened_at` | One of `opened_at`, `closed_at`, `balance_start`, `difference`, `created_at` |

Request example:

```http
GET /2023-11/admin/cash-register-sessions?filter[status]=opened&include=treasuryAccount,user&per_page=10 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01k1amyq08htzkeg79xd7f9pct",
      "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
      "treasury_account": { "id": "01jbxvcy88h9w33qcta4p5e1z8", "label": "Caisse principale", "type": "cash" },
      "user_id": "01hphmgyze0etams9dx505f9nh",
      "user": { "id": "01hphmgyze0etams9dx505f9nh", "name": "Wilfried Djopa" },
      "shop_id": "01hphmgyze0etams9dx505f9na",
      "shop": null,
      "currency_code": "XAF",
      "company_id": "01hphmgyyxa5t7vmptkt7353vs",
      "company": null,
      "status": "opened",
      "balance_start": 5000.00,
      "balance_expected": 5000.00,
      "balance_counted": null,
      "difference": null,
      "difference_percentage": null,
      "difference_reason": null,
      "difference_status": null,
      "approved_by": null,
      "approved_by_user": null,
      "approved_at": null,
      "opened_at": "2024-03-15T08:00:00+00:00",
      "closed_at": null,
      "metadata": null,
      "is_opened": true,
      "is_closed": false,
      "is_pending_approval": false,
      "has_difference": false,
      "created_at": "2024-03-15T08:00:00+00:00",
      "updated_at": "2024-03-15T08:00:00+00:00"
    }
  ],
  "pagination": {
    "total": 12,
    "count": 10,
    "per_page": 10,
    "current_page": 1,
    "total_pages": 2
  }
}
```

---

### `POST` `/2023-11/admin/cash-register-sessions`

Open a new cash register session. If `balance_start` is greater than 0, `cash_count_details` is required.

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `treasury_account_id` | string (ULID) | Yes | Must exist in `treasury_accounts` | Treasury account to associate with the session |
  | `balance_start` | number | No | `min:0` | Opening cash balance; defaults to `0` |
  | `cash_count_details` | array | Conditional | Required when `balance_start > 0`; each item needs `denomination_id` + `quantity` | Denomination breakdown of the opening count |
  | `cash_count_details[].denomination_id` | string (ULID) | Conditional | Must exist in `cash_denominations` | ID of the denomination |
  | `cash_count_details[].quantity` | integer | Conditional | `min:0` | Number of bills/coins of this denomination |
  | `metadata` | object | No | — | Additional metadata |
  | `metadata.notes` | string | No | `max:500` | Opening notes |

Request example:

```http
POST /2023-11/admin/cash-register-sessions HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
  "balance_start": 5000.00,
  "cash_count_details": [
    { "denomination_id": "01jbxvcy88h9w33qcta4p5e000", "quantity": 10 },
    { "denomination_id": "01jbxvcy88h9w33qcta4p5e001", "quantity": 5 }
  ],
  "metadata": { "notes": "Morning opening" }
}
```

Success response `201 Created`:

```json
{
  "message": "Cash register session opened",
  "session": {
    "id": "01k1amyq08htzkeg79xd7f9pct",
    "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
    "treasury_account": { "id": "01jbxvcy88h9w33qcta4p5e1z8", "label": "Caisse principale" },
    "user_id": "01hphmgyze0etams9dx505f9nh",
    "user": { "id": "01hphmgyze0etams9dx505f9nh", "name": "Wilfried Djopa" },
    "status": "opened",
    "balance_start": 5000.00,
    "balance_expected": 5000.00,
    "balance_counted": null,
    "difference": null,
    "opened_at": "2024-03-15T08:00:00+00:00",
    "closed_at": null,
    "is_opened": true,
    "is_closed": false,
    "is_pending_approval": false,
    "has_difference": false,
    "metadata": { "notes": "Morning opening" },
    "created_at": "2024-03-15T08:00:00+00:00",
    "updated_at": "2024-03-15T08:00:00+00:00"
  }
}
```

Error responses:
- `403` — cash control disabled, or not authorized to open sessions
- `422` — validation failure (e.g. missing `treasury_account_id`, invalid denomination)

---

### `GET` `/2023-11/admin/cash-register-sessions/active`

Return all sessions with `status=opened` that were opened within the last 24 hours. Results can be scoped by treasury account or user.

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `treasury_account_id` | string (ULID) | No | — | Filter by treasury account |
  | `user_id` | string (ULID) | No | — | Filter by user |

Request example:

```http
GET /2023-11/admin/cash-register-sessions/active?treasury_account_id=01jbxvcy88h9w33qcta4p5e1z8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`: array of `CashRegisterSessionResource` objects (no pagination wrapper).

```json
{
  "data": [
    {
      "id": "01k1amyq08htzkeg79xd7f9pct",
      "status": "opened",
      "balance_start": 5000.00,
      "balance_expected": 8500.00,
      "opened_at": "2024-03-15T08:00:00+00:00",
      "treasury_account": { "id": "01jbxvcy88h9w33qcta4p5e1z8", "label": "Caisse principale" },
      "user": { "id": "01hphmgyze0etams9dx505f9nh", "name": "Wilfried Djopa" }
    }
  ]
}
```

---

### `GET` `/2023-11/admin/cash-register-sessions/enums`

Return all enumeration values used by the cash register domain (statuses, difference reasons, transaction types, and actions).

Request example:

```http
GET /2023-11/admin/cash-register-sessions/enums HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "statuses": [
    { "value": "opened", "label": "Opened" },
    { "value": "pending_approval", "label": "Pending Approval" },
    { "value": "closed", "label": "Closed" },
    { "value": "rejected", "label": "Rejected" }
  ],
  "difference_reasons": [
    { "value": "none", "label": "No Difference" },
    { "value": "rounding", "label": "Rounding" },
    { "value": "change_mistake", "label": "Change Mistake" },
    { "value": "lost_receipt", "label": "Lost Receipt" },
    { "value": "system_error", "label": "System Error" },
    { "value": "found_cash", "label": "Found Cash" },
    { "value": "suspected_theft", "label": "Suspected Theft" },
    { "value": "other", "label": "Other" }
  ],
  "transaction_types": [
    { "value": "payment_inbound", "label": "Payment Received" },
    { "value": "payment_outbound", "label": "Payment Made" },
    { "value": "adjustment_positive", "label": "Positive Adjustment" },
    { "value": "adjustment_negative", "label": "Negative Adjustment" },
    { "value": "expense", "label": "Expense" },
    { "value": "transfer_out", "label": "Transfer Out" },
    { "value": "transfer_in", "label": "Transfer In" }
  ],
  "actions": [
    { "value": "opened", "label": "Opened" },
    { "value": "closed", "label": "Closed" },
    { "value": "approved", "label": "Approved" },
    { "value": "rejected", "label": "Rejected" },
    { "value": "adjusted", "label": "Adjusted" }
  ]
}
```

---

### `GET` `/2023-11/admin/cash-register-sessions/statistics`

Return aggregated statistics for cash register sessions in a date range.

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `treasury_account_id` | string (ULID) | No | — | Scope to a specific treasury account |
  | `start_date` | string (ISO 8601 or date) | No | Start of current month | Range start |
  | `end_date` | string (ISO 8601 or date) | No | End of current day | Range end |

Request example:

```http
GET /2023-11/admin/cash-register-sessions/statistics?start_date=2024-03-01&end_date=2024-03-31 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "start_date": "2024-03-01T00:00:00+00:00",
  "end_date": "2024-03-31T23:59:59+00:00",
  "total_sessions": 22,
  "accurate_sessions": 18,
  "accuracy_rate": 81.82,
  "total_income": 450000.00,
  "total_counted": 449200.00,
  "total_difference": -800.00,
  "average_difference": -36.36,
  "total_with_differences": 4
}
```

---

### `GET` `/2023-11/admin/cash-register-sessions/{session}`

Return a single session with all relationships loaded, plus a computed summary from the cash register service.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `session` | string (ULID) | ID of the cash register session |

Request example:

```http
GET /2023-11/admin/cash-register-sessions/01k1amyq08htzkeg79xd7f9pct HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "session": {
    "id": "01k1amyq08htzkeg79xd7f9pct",
    "treasury_account_id": "01jbxvcy88h9w33qcta4p5e1z8",
    "treasury_account": { "id": "01jbxvcy88h9w33qcta4p5e1z8", "label": "Caisse principale" },
    "user_id": "01hphmgyze0etams9dx505f9nh",
    "user": { "id": "01hphmgyze0etams9dx505f9nh", "name": "Wilfried Djopa" },
    "status": "closed",
    "balance_start": 5000.00,
    "balance_expected": 8500.00,
    "balance_counted": 8300.00,
    "difference": -200.00,
    "difference_percentage": -2.35,
    "difference_reason": "change_mistake",
    "difference_status": "pending_approval",
    "approved_by": null,
    "approved_by_user": null,
    "approved_at": null,
    "opened_at": "2024-03-15T08:00:00+00:00",
    "closed_at": "2024-03-15T18:00:00+00:00",
    "metadata": null,
    "is_opened": false,
    "is_closed": true,
    "is_pending_approval": true,
    "has_difference": true,
    "transactions_count": 14,
    "history_count": 2,
    "created_at": "2024-03-15T08:00:00+00:00",
    "updated_at": "2024-03-15T18:00:00+00:00"
  },
  "summary": {
    "total_inbound": 4000.00,
    "total_outbound": 500.00,
    "net": 3500.00,
    "requires_approval": true
  }
}
```

Error responses:
- `403` — cash control disabled, or not authorized to view this session
- `404` — session not found

---

### `POST` `/2023-11/admin/cash-register-sessions/{session}/close`

Close an open cash register session. The session must have `status=opened`. If the counted balance differs from the expected balance, the response indicates whether manager approval is required.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `session` | string (ULID) | ID of the session to close |

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `balance_counted` | number | No | `min:0` | Physically counted closing balance; defaults to `0` |
  | `difference_reason` | string | No | One of: `none`, `rounding`, `change_mistake`, `lost_receipt`, `system_error`, `found_cash`, `suspected_theft`, `other` | Reason for any difference |
  | `notes` | string | No | `max:1000` | Closing notes |
  | `cash_count_details` | array | No | Each item needs `denomination_id` + `quantity` | Denomination breakdown of the closing count |
  | `cash_count_details[].denomination_id` | string (ULID) | Conditional | Must exist in `cash_denominations` | ID of the denomination |
  | `cash_count_details[].quantity` | integer | Conditional | `min:0` | Number of bills/coins of this denomination |

Request example:

```http
POST /2023-11/admin/cash-register-sessions/01k1amyq08htzkeg79xd7f9pct/close HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "balance_counted": 8300.00,
  "difference_reason": "change_mistake",
  "notes": "Short by 200 XAF, likely a change error.",
  "cash_count_details": [
    { "denomination_id": "01jbxvcy88h9w33qcta4p5e000", "quantity": 16 },
    { "denomination_id": "01jbxvcy88h9w33qcta4p5e001", "quantity": 3 }
  ]
}
```

Success response `200 OK`:

```json
{
  "message": "Cash register closed. Awaiting approval.",
  "session": {
    "id": "01k1amyq08htzkeg79xd7f9pct",
    "status": "pending_approval",
    "balance_expected": 8500.00,
    "balance_counted": 8300.00,
    "difference": -200.00,
    "closed_at": "2024-03-15T18:00:00+00:00",
    "is_pending_approval": true
  },
  "summary": {
    "total_inbound": 4000.00,
    "total_outbound": 500.00,
    "net": 3500.00,
    "requires_approval": true
  },
  "alert": "Difference of -200.00 XAF detected.",
  "requires_approval": true
}
```

Error responses:
- `400` — session is not in `opened` status
- `403` — cash control disabled, or not authorized to close this session
- `404` — session not found
- `422` — validation failure

---

### `POST` `/2023-11/admin/cash-register-sessions/{session}/approve-difference`

Approve a cash difference for a session in `pending_approval` status. The session transitions to `closed` and any difference adjustments are applied.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `session` | string (ULID) | ID of the session |

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `notes` | string | No | `max:1000` | Approval notes |

Request example:

```http
POST /2023-11/admin/cash-register-sessions/01k1amyq08htzkeg79xd7f9pct/approve-difference HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "notes": "Reviewed and approved. Change error confirmed."
}
```

Success response `200 OK`:

```json
{
  "message": "Difference approved and applied",
  "session": {
    "id": "01k1amyq08htzkeg79xd7f9pct",
    "status": "closed",
    "approved_by": "01hphmgyze0etams9dx505f9nh",
    "approved_at": "2024-03-15T19:00:00+00:00",
    "is_pending_approval": false,
    "is_closed": true
  }
}
```

Error responses:
- `400` — session is not in `pending_approval` status
- `403` — cash control disabled, or not authorized to approve differences
- `404` — session not found

---

### `POST` `/2023-11/admin/cash-register-sessions/{session}/reject-closure`

Reject a pending or closed session and reopen it. The session must have a `closed_at` timestamp. The session transitions back to `opened` so the cashier can recount.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `session` | string (ULID) | ID of the session |

- **Request body:**

  | Field | Type | Required | Rules | Description |
  |---|---|---|---|---|
  | `reason` | string | Yes | `max:1000` | Reason for rejecting the closure |

Request example:

```http
POST /2023-11/admin/cash-register-sessions/01k1amyq08htzkeg79xd7f9pct/reject-closure HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
Content-Type: application/json

{
  "reason": "Count appears incorrect. Please recount and resubmit."
}
```

Success response `200 OK`:

```json
{
  "message": "Closure rejected. Cash register reopened.",
  "session": {
    "id": "01k1amyq08htzkeg79xd7f9pct",
    "status": "opened",
    "is_opened": true,
    "is_closed": false,
    "closed_at": null
  }
}
```

Error responses:
- `400` — session has no `closed_at` (was never closed)
- `403` — cash control disabled, or not authorized to reject closures
- `404` — session not found
- `422` — `reason` is missing

---

### `GET` `/2023-11/admin/cash-register-sessions/{session}/transactions`

List the cash register transactions (payments, adjustments, expenses, transfers) within a session.

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `session` | string (ULID) | ID of the session |

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `page` | integer | No | `1` | Page number |
  | `per_page` | integer | No | `15` | Results per page |

Request example:

```http
GET /2023-11/admin/cash-register-sessions/01k1amyq08htzkeg79xd7f9pct/transactions?per_page=20 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01k1amyq08htzkeg79xd7f9aaa",
      "cash_register_session_id": "01k1amyq08htzkeg79xd7f9pct",
      "payment_id": "01k1amyq08htzkeg79xd7f9bbb",
      "payment": null,
      "type": "payment_inbound",
      "description": "Payment for order #ORD-0101",
      "amount": 3500.00,
      "signed_amount": 3500.00,
      "reference": "PAY-2024-0042",
      "metadata": null,
      "is_inbound": true,
      "is_outbound": false,
      "created_at": "2024-03-15T10:00:00+00:00",
      "updated_at": "2024-03-15T10:00:00+00:00"
    }
  ],
  "pagination": {
    "total": 14,
    "count": 14,
    "per_page": 20,
    "current_page": 1,
    "total_pages": 1
  }
}
```

Error responses:
- `403` — cash control disabled
- `404` — session not found

---

### `GET` `/2023-11/admin/cash-register-sessions/{session}/history`

List the audit history entries for a session (open, close, approve, reject, adjust events).

- **Path parameters:**

  | Name | Type | Description |
  |---|---|---|
  | `session` | string (ULID) | ID of the session |

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `page` | integer | No | `1` | Page number |
  | `per_page` | integer | No | `15` | Results per page |

Request example:

```http
GET /2023-11/admin/cash-register-sessions/01k1amyq08htzkeg79xd7f9pct/history HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01k1amyq08htzkeg79xd7f9cc1",
      "cash_register_session_id": "01k1amyq08htzkeg79xd7f9pct",
      "action": "opened",
      "user_id": "01hphmgyze0etams9dx505f9nh",
      "user": { "id": "01hphmgyze0etams9dx505f9nh", "name": "Wilfried Djopa" },
      "metadata": { "balance_start": 5000.00 },
      "created_at": "2024-03-15T08:00:00+00:00",
      "updated_at": "2024-03-15T08:00:00+00:00"
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

`action` is one of: `opened`, `closed`, `approved`, `rejected`, `adjusted`.

Error responses:
- `403` — cash control disabled
- `404` — session not found

---

## Cash Denominations

### `GET` `/2023-11/admin/cash-denominations`

Return the list of active cash denominations (bills and coins) for a given currency. Results are cached for one week. Denominations are ordered by `sort_order` descending, then `value` descending (largest first).

- **Query parameters:**

  | Name | Type | Required | Default | Description |
  |---|---|---|---|---|
  | `currency_code` | string | Yes | — | ISO 4217 currency code (e.g. `XAF`, `EUR`, `USD`) |

Request example:

```http
GET /2023-11/admin/cash-denominations?currency_code=XAF HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hphmgyyxa5t7vmptkt7353vs
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01jbxvcy88h9w33qcta4p5e000",
      "currency_code": "XAF",
      "type": "bill",
      "value": 10000.00,
      "label": "10 000 FCFA",
      "sort_order": 100,
      "is_active": true
    },
    {
      "id": "01jbxvcy88h9w33qcta4p5e001",
      "currency_code": "XAF",
      "type": "bill",
      "value": 5000.00,
      "label": "5 000 FCFA",
      "sort_order": 90,
      "is_active": true
    },
    {
      "id": "01jbxvcy88h9w33qcta4p5e002",
      "currency_code": "XAF",
      "type": "coin",
      "value": 500.00,
      "label": "500 FCFA",
      "sort_order": 50,
      "is_active": true
    }
  ]
}
```

Note: this endpoint returns a plain collection (no pagination wrapper).

Error responses:
- `422` — `currency_code` query parameter is missing
- `401` — invalid or expired token
