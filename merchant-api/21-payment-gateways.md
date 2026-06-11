---
title: "Payment Gateway Integrations & Webhooks"
description: "This domain covers every payment provider integration offered by the Genuka API: Stripe, PayDunya, Pawapay, Mamoni, Taramoney, PayPal, and Genuka Pay. Each gate"
sidebarTitle: "Payment Gateways"
---

This domain covers every payment provider integration offered by the Genuka API: Stripe, PayDunya, Pawapay, Mamoni, Taramoney, PayPal, and Genuka Pay. Each gateway exposes a checkout initiation endpoint and a webhook receiver that the provider calls back to confirm payment status. Two additional utility endpoints are included: a Caddy reverse-proxy configuration reader used for deployment infrastructure, and a public company profile endpoint used by storefronts to retrieve branding and payment options without authentication. Channel notification webhooks for SMS Gate and Telegram are also documented here.

Most gateway endpoints are public (no `Authorization` header required) because they are called directly from customer-facing checkout pages or from third-party providers. See the per-endpoint notes for exceptions.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/stripe/callback` | Handle Stripe OAuth Connect callback |
| `POST` | `/2023-11/stripe/checkout` | Create a Stripe checkout session |
| `POST` | `/2023-11/stripe/webhooks` | Receive Stripe platform webhook events |
| `POST` | `/2023-11/stripe/webhooks/{paymentMethod}` | Receive per-merchant Stripe webhook events (direct mode) |
| `GET` | `/2023-11/paydunya/callback` | Handle PayDunya payment callback |
| `POST` | `/2023-11/paydunya/checkout` | Create a PayDunya checkout session |
| `POST` | `/2023-11/paydunya/webhooks` | Receive PayDunya webhook events (POST) |
| `GET` | `/2023-11/paydunya/webhooks` | Receive PayDunya webhook events (GET) |
| `POST` | `/2023-11/pawapay/checkout` | Create a Pawapay hosted checkout session |
| `POST` | `/2023-11/pawapay/deposits/direct` | Trigger a direct Pawapay USSD/STK-push deposit |
| `GET` | `/2023-11/pawapay/deposits/{depositId}/status` | Poll status of a Pawapay deposit |
| `GET` | `/2023-11/pawapay/predict-correspondent` | Predict the mobile-money operator for a phone number |
| `POST` | `/2023-11/pawapay/webhooks` | Receive Pawapay webhook events (POST) |
| `GET` | `/2023-11/pawapay/webhooks` | Receive Pawapay webhook events (GET) |
| `POST` | `/2023-11/mamoni/check` | Check a Mamoni transaction status |
| `POST` | `/2023-11/mamoni/checkout` | Create a Mamoni checkout session |
| `POST` | `/2023-11/mamoni/init` | Initialize a Mamoni mobile-money transaction |
| `POST` | `/2023-11/mamoni/webhooks` | Receive Mamoni webhook events |
| `POST` | `/2023-11/taramoney/checkout` | Create a Taramoney checkout session |
| `POST` | `/2023-11/taramoney/webhooks/{timestamp_order}` | Receive Taramoney webhook events (POST) |
| `GET` | `/2023-11/taramoney/webhooks/{timestamp_order}` | Receive Taramoney webhook events (GET) |
| `POST` | `/2023-11/paypal/checkout` | Create a PayPal checkout session |
| `POST` | `/2023-11/genuka-pay/initiate` | Initiate a Genuka Pay mobile-money payment |
| `POST` | `/2023-11/genuka-pay/webhooks/{companyId}` | Receive Genuka Pay webhook events |
| `POST` | `/2023-11/smsgate/webhook/{connectionId}` | Receive SMS Gate delivery events |
| `POST` | `/2023-11/telegram/webhook` | Receive Telegram bot webhook updates |
| `GET\|POST` | `/2023-11/broadcasting/auth` | Authenticate a Laravel Echo / Pusher channel |
| `GET` | `/2023-11/caddy-configuration` | Retrieve the latest Caddy reverse-proxy configuration |
| `GET` | `/2023-11/companies/{company}` | Retrieve public company profile (storefront) |

---

## Stripe

### `GET` `/2023-11/stripe/callback`

Handle the Stripe OAuth Connect redirect. Stripe sends `code` and `state` query parameters after the merchant authorises Genuka on their Stripe account. On success the merchant is redirected to the dashboard payment-methods page. On failure all Stripe payment methods for the company are deactivated.

> **Note:** This endpoint is called by Stripe's servers, not by your application. Do not call it directly.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `code` | string | Yes | — | OAuth authorisation code from Stripe |
| `state` | string | Yes | — | CSRF state token in the format `<state>:<company_id>` |

```http
GET /2023-11/stripe/callback?code=ac_1abc&state=abc123:01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
```

**Success response** (`302 Found`): Redirects to the frontend payment-methods page.

**Error responses**
- `302 Found` — invalid state token; redirects to login with `error=Invalid state parameter`.
- `302 Found` — Stripe token exchange failed; redirects to login with error message.

---

### `POST` `/2023-11/stripe/checkout`

Create a Stripe hosted checkout session for an order or an app subscription. Returns the session `id` and redirect `url`. If the company currency is not supported by Stripe (e.g. XAF, XOF) the amount is automatically converted to USD.

> **Auth:** Not required. Called from customer-facing checkout pages.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Conditional | ULID | Order to pay. Required when `app_subscription_id` is absent. |
| `app_subscription_id` | string | Conditional | ULID | App subscription to pay. Required when `order_id` is absent. |
| `amount` | number | No | — | Override amount (uses order/subscription total if omitted) |
| `success_url` | string | Yes | URL | URL to redirect to after successful payment |
| `cancel_url` | string | Yes | URL | URL to redirect to after cancelled payment |

```http
POST /2023-11/stripe/checkout HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel"
}
```

**Success response** (`200 OK`):
```json
{
  "id": "cs_test_a1B2c3D4e5F6g7H8i9J0",
  "url": "https://checkout.stripe.com/pay/cs_test_a1B2c3D4e5F6g7H8i9J0"
}
```

**Error responses**
- `400 Bad Request` — order/subscription not found or Stripe session creation failed: `{ "error": "No such order." }`
- `404 Not Found` — order/subscription ULID does not exist.

---

### `POST` `/2023-11/stripe/webhooks`

Platform-level Stripe webhook receiver. Stripe calls this endpoint to notify of payment events (e.g. `checkout.session.completed`). Stripe verifies the payload signature using the platform webhook secret. Always respond `200 OK` to prevent retries.

> **Auth:** Not required. Called by Stripe servers only.

```http
POST /2023-11/stripe/webhooks HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
Stripe-Signature: t=1710000000,v1=abc123...

{
  "id": "evt_1abc",
  "type": "checkout.session.completed",
  "data": { "object": { ... } }
}
```

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `id` | string | Stripe event identifier (e.g. `evt_1abc`) |
| `type` | string | Event type. Handled: `checkout.session.completed`, `invoice.payment_failed`. All other types are acknowledged and ignored. |
| `data` | object | Event payload wrapper |
| `data.object` | object | The Stripe object associated with the event (a `Session` for `checkout.session.completed`, an `Invoice` for `invoice.payment_failed`) |

**Success response** (`200 OK`):
```json
{ "status": "ok" }
```

**Error responses**
- `400 Bad Request` — signature verification failed.

---

### `POST` `/2023-11/stripe/webhooks/{paymentMethod}`

Per-merchant webhook receiver for Stripe accounts connected in **direct mode**. The signature is verified using the individual merchant's webhook secret stored in their payment method configuration.

> **Auth:** Not required. Called by Stripe servers only.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `paymentMethod` | string (ULID) | ID of the merchant's PaymentMethod record |

```http
POST /2023-11/stripe/webhooks/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
Stripe-Signature: t=1710000000,v1=def456...

{
  "id": "evt_2xyz",
  "type": "checkout.session.completed",
  "data": { "object": { ... } }
}
```

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `id` | string | Stripe event identifier (e.g. `evt_2xyz`) |
| `type` | string | Event type. Handled: `checkout.session.completed`, `invoice.payment_failed`. All other types are acknowledged and ignored. |
| `data` | object | Event payload wrapper |
| `data.object` | object | The Stripe object associated with the event (a `Session` for `checkout.session.completed`, an `Invoice` for `invoice.payment_failed`) |

**Success response** (`200 OK`):
```json
{ "status": "ok" }
```

**Error responses**
- `404 Not Found` — `paymentMethod` ULID does not exist.
- `400 Bad Request` — signature verification failed.

---

## PayDunya

### `GET` `/2023-11/paydunya/callback`

Payment return URL that PayDunya redirects the customer to after a checkout attempt. Always returns `200 OK`; actual payment confirmation happens via the webhook.

> **Auth:** Not required.

```http
GET /2023-11/paydunya/callback HTTP/1.1
Host: api.genuka.com
```

**Success response** (`200 OK`):
```json
{ "status": "success" }
```

---

### `POST` `/2023-11/paydunya/checkout`

Create a PayDunya hosted checkout session for an order. Returns the PayDunya invoice token and redirect URL.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Yes | ULID | Order to pay |
| `amount` | number | No | — | Override amount (uses `amount_due` if omitted) |
| `success_url` | string | No | URL | Redirect URL on success |
| `cancel_url` | string | No | URL | Redirect URL on cancel |

```http
POST /2023-11/paydunya/checkout HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel"
}
```

**Success response** (`200 OK`):
```json
{
  "id": "pi_dunya_abc123",
  "url": "https://app.paydunya.com/sandbox/invoice/pi_dunya_abc123"
}
```

**Error responses**
- `400 Bad Request` — order not found or PayDunya session creation failed: `{ "error": "..." }`

---

### `POST` `/2023-11/paydunya/webhooks`

PayDunya webhook receiver (POST). Receives payment confirmation events from PayDunya servers. Looks up the order by `order_id`, validates the callback, and records a payment if successful.

> **Auth:** Not required — called by PayDunya servers, not by API clients.

**Incoming payload** (sent by PayDunya):

| Field | Type | Description |
|---|---|---|
| `order_id` | string (ULID) | Genuka order ID passed as metadata during checkout |
| `amount` | number | Payment amount in the transaction currency |
| `status` | string | `completed`, `pending`, or `failed` |
| `token` | string | PayDunya transaction token |

```json
{
  "order_id": "01k76xt7z87katy392s6w335cp",
  "amount": 15000,
  "status": "completed",
  "token": "pdx_abc123def456"
}
```

**Success response** (`200 OK`):
```json
{ "message": "Webhook processed." }
```

**Error responses:**
- `404` — Order not found for the given `order_id`.
- `500` — Unexpected error during payment processing.

---

### `GET` `/2023-11/paydunya/webhooks`

PayDunya webhook receiver (GET). PayDunya sends both GET and POST callbacks on payment events. The gateway handler validates the `hash` signature and updates the corresponding order payment status.

> **Auth:** Not required. Called by PayDunya servers only.

**Query parameters** (sent by PayDunya as GET params):

| Name | Type | Description |
|---|---|---|
| `order_id` | string | Genuka order ID |
| `token` | string | PayDunya transaction token |
| `status` | string | Payment status |

**Success response** (`200 OK`):
```json
{ "message": "Webhook processed." }
```

> **Query parameter:** PayDunya must be configured to append `?company_id=<companyId>` to the webhook URL so the handler can resolve the merchant's API keys for signature verification.

```http
POST /2023-11/paydunya/webhooks?company_id=01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "data": {
    "hash": "sha512_of_master_key",
    "status": "completed",
    "invoice": {
      "token": "pi_dunya_abc123",
      "total_amount": 500000,
      "description": "Payment for order #1001"
    },
    "custom_data": {
      "reference": "01hqydxwtxdj3kmzp3bz7jk73g"
    }
  }
}
```

**Inbound webhook payload fields (inside `data`)**

| Field | Type | Description |
|---|---|---|
| `hash` | string | SHA-512 of the merchant's PayDunya master key — used for signature verification |
| `status` | string | `completed` triggers payment processing; any other value is acknowledged without processing |
| `invoice.token` | string | PayDunya invoice token |
| `invoice.total_amount` | number | Amount in the smallest currency unit (divided by 100 for 2-decimal currencies) |
| `custom_data.reference` | string | Order ULID or `GENUKA_<id>` for platform subscriptions |

**Success response** (`200 OK`):
```json
{ "status": "success" }
```

**Error responses**
- `400 Bad Request` — signature verification failed: `{ "error": "Invalid Signature" }`

---

## Pawapay

### `POST` `/2023-11/pawapay/checkout`

Create a Pawapay hosted checkout page for an order, a Genuka subscription, or an app subscription. Returns a redirect URL to the Pawapay checkout widget. The currency is automatically resolved from the order's stored currency-conversion metadata when present.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Conditional | ULID | Order to pay. One of `order_id`, `subscription_id`, or `app_subscription_id` required. |
| `subscription_id` | string | Conditional | ULID | Genuka platform subscription to pay. |
| `app_subscription_id` | string | Conditional | ULID | App subscription to pay. |
| `amount` | number | No | — | Override amount |
| `callback` | string | No | URL | Preferred success redirect URL (alias for `success_url`) |
| `success_url` | string | No | URL | Success redirect URL (used when `callback` is absent) |
| `cancel_url` | string | No | URL | Cancel redirect URL |
| `reason` | string | No | — | Payment description shown to the customer |
| `lang` | string | No | — | 2-letter locale code (e.g. `fr`, `en`). Defaults to app locale. |
| `currency` | string | No | — | ISO 4217 currency code (for subscriptions; defaults to `XAF`) |

```http
POST /2023-11/pawapay/checkout HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel",
  "lang": "fr"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://checkout.pawapay.io/deposit/01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Error responses**
- `400 Bad Request` — missing required entity or gateway error: `{ "error": "No order_id, subscription_id, or app_subscription_id provided" }`
- `404 Not Found` — entity ULID does not exist.

---

### `POST` `/2023-11/pawapay/deposits/direct`

Trigger a direct Pawapay deposit (USSD push / STK push) without a redirect page. The customer receives an in-app or USSD prompt on their phone. Returns the deposit ID and initial status.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Yes | ULID | Order to pay |
| `phone` | string | Conditional | E.164 | Customer phone number. Falls back to order customer phone if omitted. |
| `amount` | number | No | — | Override amount |
| `correspondent` | string | No | — | Mobile money operator code (e.g. `MTN_MOMO_CMR`). Predicted automatically if omitted. |
| `country` | string | No | — | ISO 3166-1 alpha-2 country code (e.g. `CM`) |
| `customer_message` | string | No | — | Custom message shown on the USSD/push prompt |
| `reason` | string | No | — | Payment description (defaults to order reference) |
| `lang` | string | No | — | Locale code |

```http
POST /2023-11/pawapay/deposits/direct HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "phone": "+237677123456",
  "correspondent": "MTN_MOMO_CMR"
}
```

**Success response** (`200 OK`):
```json
{
  "status": "ACCEPTED",
  "depositId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "correspondent": "MTN_MOMO_CMR"
}
```

**Error responses**
- `400 Bad Request` — `order_id` missing, phone cannot be resolved, or gateway rejected: `{ "error": "Customer phone is required for direct deposit", "error_code": null, "gateway_response": null }`
- `404 Not Found` — order ULID does not exist.

---

### `GET` `/2023-11/pawapay/deposits/{depositId}/status`

Poll the status of a previously initiated Pawapay deposit. Delegates to the Pawapay API using the company's credentials.

> **Auth:** Not required.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `depositId` | string (UUID) | Deposit UUID returned by `/pawapay/deposits/direct` |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `order_id` | string | No | — | Order ULID used to resolve the company's Pawapay credentials |

```http
GET /2023-11/pawapay/deposits/a1b2c3d4-e5f6-7890-abcd-ef1234567890/status?order_id=01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
```

**Success response** (`200 OK`):

The API wraps the raw Pawapay response. The outer envelope always contains `status` (from the Pawapay response) and `raw` (the full Pawapay deposit object).

```json
{
  "status": "COMPLETED",
  "raw": {
    "depositId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "status": "COMPLETED",
    "requestedAmount": "5000",
    "currency": "XAF",
    "correspondent": "MTN_MOMO_CMR",
    "msisdn": "237677123456",
    "statementDescription": "Payment for order #1001",
    "created": "2024-03-10T12:00:00.000Z",
    "respondedByPayer": "2024-03-10T12:01:30.000Z"
  }
}
```

**Error responses**
- `400 Bad Request` — Pawapay API error: `{ "error": "..." }`

---

### `GET` `/2023-11/pawapay/predict-correspondent`

Predict the mobile money operator (correspondent) for a given phone number by querying the Pawapay prediction API.

> **Auth:** Not required.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `phone` | string | Yes (alias: `msisdn`) | — | Phone number in E.164 or local format |

```http
GET /2023-11/pawapay/predict-correspondent?phone=%2B237677123456 HTTP/1.1
Host: api.genuka.com
```

**Success response** (`200 OK`):
```json
{
  "correspondent": "MTN_MOMO_CMR",
  "country": "CM",
  "msisdn": "237677123456"
}
```

**Error responses**
- `400 Bad Request` — `phone` parameter missing: `{ "error": "phone is required" }`
- `422 Unprocessable Entity` — operator cannot be determined: `{ "error": "Could not determine the operator for this phone number" }`

---

### `POST` `/2023-11/pawapay/webhooks`

Pawapay deposit status webhook (POST). Pawapay calls this endpoint when a deposit's status changes (e.g. `COMPLETED`, `FAILED`). The handler resolves the order via metadata stored on the deposit and records the payment.

> **Auth:** Not required — called by Pawapay servers, not by API clients.

**Incoming payload** (sent by Pawapay):

| Field | Type | Description |
|---|---|---|
| `depositId` | string (UUID) | Pawapay deposit identifier |
| `status` | string | `COMPLETED`, `FAILED`, or `EXPIRED` |
| `amount` | string | Decimal amount (e.g. `"15000"`) |
| `currency` | string | ISO 4217 currency code (e.g. `XAF`) |
| `correspondentId` | string | Mobile-money operator code (e.g. `MTN_MOMO_CMR`) |
| `msisdn` | string | Customer phone number |
| `metadata` | object | Contains `order_id`, `deposit_id`, `company_id`, `reference` stored during checkout |

```json
{
  "depositId": "f4401bd2-1568-4140-bf2d-eb77d2b2b639",
  "status": "COMPLETED",
  "amount": "15000",
  "currency": "XAF",
  "correspondentId": "MTN_MOMO_CMR",
  "msisdn": "237677000000",
  "metadata": {
    "order_id": "01k76xt7z87katy392s6w335cp",
    "deposit_id": "f4401bd2-1568-4140-bf2d-eb77d2b2b639",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "reference": "#ORDER_1174"
  }
}
```

**Success response** (`200 OK`):
```json
{ "message": "Webhook processed." }
```

---

### `GET` `/2023-11/pawapay/webhooks`

Pawapay webhook receiver (GET). Pawapay sends both GET and POST callbacks on deposit status changes. The handler updates order payment status.

> **Auth:** Not required. Called by Pawapay servers only.

**Success response** (`200 OK`):
```json
{ "message": "Webhook received." }
```

```http
POST /2023-11/pawapay/webhooks HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "depositId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "status": "COMPLETED",
  "requestedAmount": "5000",
  "currency": "XAF",
  "correspondent": "MTN_MOMO_CMR",
  "metadata": {
    "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73h",
    "deposit_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
  }
}
```

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `depositId` | string (UUID) | Pawapay deposit identifier |
| `status` | string | `COMPLETED`, `FAILED`, or `DUPLICATE_IGNORED` |
| `requestedAmount` | string | Amount as a string |
| `currency` | string | ISO 4217 currency code |
| `correspondent` | string | Mobile money operator code |
| `metadata` | object | Passthrough metadata set during deposit creation; contains `order_id`, `company_id`, `deposit_id` |

**Success response** (`200 OK`):
```json
{ "status": "ok" }
```

---

## Mamoni

### `POST` `/2023-11/mamoni/init`

Initialize a Mamoni mobile-money transaction directly (without a hosted page). Calls the Mamoni gateway to trigger a payment prompt on the customer's phone.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Conditional | ULID | Order to pay. Determines the external reference. |
| `companyId` | string | Yes | ULID | Company ULID used to resolve Mamoni credentials |
| `amount` | number | Yes | — | Amount to charge (rounded per currency precision) |
| `phone` | string | Yes | E.164 | Customer mobile phone number |
| `external_reference` | string | No | — | Fallback external reference when `order_id` is absent |

```http
POST /2023-11/mamoni/init HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
  "amount": 5000,
  "phone": "+237677123456"
}
```

**Success response** (`200 OK`):
```json
{
  "status": "PENDING",
  "transactionId": "mam_txn_abc123",
  "reference": "ORDER_01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Error responses**
- `400 Bad Request` — gateway error: `{ "error": "..." }`

---

### `POST` `/2023-11/mamoni/check`

Check the status of a previously initiated Mamoni transaction by its reference.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `reference` | string | Yes | — | Mamoni transaction reference to check |
| `companyId` | string | Yes | ULID | Company ULID used to resolve Mamoni credentials |

```http
POST /2023-11/mamoni/check HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "reference": "mam_txn_abc123",
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Success response** (`200 OK` when `SUCCESSFUL`, `202 Accepted` otherwise):
```json
{
  "data": {
    "status": "SUCCESSFUL",
    "transactionId": "mam_txn_abc123",
    "amount": 5000,
    "currency": "XAF"
  }
}
```

**Error responses**
- `400 Bad Request` — gateway error: `{ "error": "..." }`

---

### `POST` `/2023-11/mamoni/checkout`

Create a Mamoni-hosted checkout session for an order.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Yes | ULID | Order to pay |
| `companyId` | string | No | ULID | Company ULID (defaults to order's company) |
| `amount` | number | No | — | Override amount |
| `phone` | string | No | E.164 | Customer phone (falls back to order customer phone) |
| `success_url` | string | No | URL | Redirect URL on success |
| `cancel_url` | string | No | URL | Redirect URL on cancel |

```http
POST /2023-11/mamoni/checkout HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://pay.mamoni.io/checkout/abc123"
}
```

**Error responses**
- `400 Bad Request` — order not found or gateway error: `{ "error": "..." }`

---

### `POST` `/2023-11/mamoni/webhooks`

Mamoni webhook receiver. Mamoni calls this to notify of transaction status changes. The handler updates the order payment state.

> **Auth:** Not required. Called by Mamoni servers only.

```http
POST /2023-11/mamoni/webhooks HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "external_reference": "ORDER_01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "SUCCESSFUL",
  "amount": 5000,
  "currency": "XAF"
}
```

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `external_reference` | string | Reference set during `initTransaction` — format `ORDER_<id>` for orders |
| `status` | string | `SUCCESSFUL` triggers payment processing; any other value is logged and ignored |
| `amount` | number | Transaction amount |
| `currency` | string | ISO 4217 currency code |

**Success response** (`200 OK`):
```json
{ "status": "success" }
```

---

## Taramoney

### `POST` `/2023-11/taramoney/checkout`

Create a Taramoney checkout session for an order, a Genuka subscription, or an app subscription. Returns a redirect URL to the Taramoney payment page.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Conditional | ULID | Order to pay. One of `order_id`, `subscription_id`, or `app_subscription_id` required. |
| `subscription_id` | string | Conditional | ULID | Genuka platform subscription to pay. |
| `app_subscription_id` | string | Conditional | ULID | App subscription to pay. |
| `amount` | number | No | — | Override amount |
| `success_url` | string | No | URL | Redirect URL on success |
| `cancel_url` | string | No | URL | Redirect URL on cancel |
| `callback` | string | No | URL | Alternative success redirect (preferred over `success_url`) |

```http
POST /2023-11/taramoney/checkout HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://pay.taramoney.io/checkout/abc123"
}
```

**Error responses**
- `400 Bad Request` — missing required entity or gateway error: `{ "error": "No order_id, subscription_id, or app_subscription_id provided" }`
- `404 Not Found` — entity ULID does not exist.

---

### `POST` `/2023-11/taramoney/webhooks/{timestamp_order}`

Taramoney payment webhook (POST). Taramoney passes the order creation timestamp as a URL segment when confirming a payment. The handler finds the order and records the payment.

> **Auth:** Not required — called by Taramoney servers, not by API clients.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `timestamp_order` | string | Unix timestamp identifying the order, set during checkout initiation |

**Incoming payload** (sent by Taramoney):

| Field | Type | Description |
|---|---|---|
| `order_id` | string (ULID) | Genuka order ID |
| `status` | string | `success` or `failed` |
| `amount` | number | Confirmed payment amount |
| `transaction_id` | string | Taramoney transaction reference |

```json
{
  "order_id": "01k76xt7z87katy392s6w335cp",
  "status": "success",
  "amount": 15000,
  "transaction_id": "TM-2024-ABC123"
}
```

**Success response** (`200 OK`):
```json
{ "message": "Webhook processed." }
```

---

### `GET` `/2023-11/taramoney/webhooks/{timestamp_order}`

Taramoney webhook receiver (GET). Taramoney passes the order timestamp as a path segment during callback. Both GET and POST are accepted.

> **Auth:** Not required. Called by Taramoney servers only.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `timestamp_order` | string | Unix timestamp identifying the order |

**Success response** (`200 OK`):
```json
{ "message": "Webhook received." }
```

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `timestamp_order` | string | Timestamp+order identifier included by Taramoney in the callback URL |

```http
POST /2023-11/taramoney/webhooks/1710000000__01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "status": "SUCCESS",
  "amount": 5000,
  "currency": "XAF",
  "reference": "ORDER_01hqydxwtxdj3kmzp3bz7jk73g",
  "transactionId": "tara_txn_abc123"
}
```

> **Note:** The route segment `{timestamp_order}` encodes both the Unix timestamp and the order ULID separated by `__` (double underscore), e.g. `1710000000__01hqydxwtxdj3kmzp3bz7jk73g`. This segment is constructed by the API when building the webhook URL for Taramoney and is used to resolve the order without trusting the request body.

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `status` | string | `SUCCESS` triggers payment processing; any other value (e.g. `FAILED`, `PENDING`) is acknowledged without processing |
| `amount` | number | Transaction amount (may be in cents for precision-2 currencies) |
| `currency` | string | ISO 4217 currency code |
| `reference` | string | Merchant reference set during checkout — format `ORDER_<id>` for orders |
| `transactionId` | string | Taramoney internal transaction identifier |

**Success response** (`200 OK`):
```json
{ "status": "success" }
```

**Non-success payment response** (`200 OK` — payment not confirmed):
```json
{ "status": "failed" }
```

---

## PayPal

### `POST` `/2023-11/paypal/checkout`

Create a PayPal checkout session for an order. Returns a redirect URL to the PayPal payment page.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Yes | ULID | Order to pay |
| `amount` | number | No | — | Override amount (uses `amount_due` if omitted) |
| `success_url` | string | No | URL | Redirect URL on success |
| `cancel_url` | string | No | URL | Redirect URL on cancel |

```http
POST /2023-11/paypal/checkout HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://www.paypal.com/checkoutnow?token=EC-1AB23456CD789012E"
}
```

**Error responses**
- `400 Bad Request` — order not found or PayPal session creation failed: `{ "error": "..." }`
- `404 Not Found` — order ULID does not exist.

---

## Genuka Pay

### `POST` `/2023-11/genuka-pay/initiate`

Initiate a Genuka Pay mobile-money payment for an order, a Genuka subscription, or an app subscription. Genuka Pay is an aggregated mobile money gateway. Returns a redirect URL. The amount is automatically resolved from the order's currency-conversion metadata when present.

> **Auth:** Not required.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string | Conditional | ULID | Order to pay. One of `order_id`, `subscription_id`, or `app_subscription_id` required. |
| `subscription_id` | string | Conditional | ULID | Genuka platform subscription to pay. |
| `app_subscription_id` | string | Conditional | ULID | App subscription to pay. |
| `amount` | number | No | — | Override amount |
| `payer_phone` | string | No | E.164 | Customer phone (falls back to order customer phone) |
| `callback` | string | No | URL | Preferred success redirect URL |
| `success_url` | string | No | URL | Success redirect URL (used when `callback` absent) |
| `cancel_url` | string | No | URL | Cancel redirect URL |
| `reason` | string | No | — | Payment description |
| `lang` | string | No | — | Locale code (e.g. `fr`, `en`) |
| `currency` | string | No | — | ISO 4217 code for subscriptions (defaults to `XAF`) |

```http
POST /2023-11/genuka-pay/initiate HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "order_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "payer_phone": "+237677123456",
  "success_url": "https://mystore.com/checkout/success",
  "cancel_url": "https://mystore.com/checkout/cancel",
  "lang": "fr"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://pay.genuka.com/checkout/abc123"
}
```

**Error responses**
- `400 Bad Request` — missing required entity or gateway error: `{ "error": "No order_id, subscription_id, or app_subscription_id provided" }`
- `404 Not Found` — entity ULID does not exist.

---

### `POST` `/2023-11/genuka-pay/webhooks/{companyId}`

Genuka Pay webhook receiver. The signature in the request headers is validated before processing. On a valid event the corresponding order or subscription payment status is updated.

> **Auth:** Not required. Called by Genuka Pay servers only.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `companyId` | string (ULID) | Company ULID that owns the payment method |

```http
POST /2023-11/genuka-pay/webhooks/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
X-Genuka-Signature: sha256=abc123...

{
  "event": "transaction.success",
  "data": {
    "transaction_id": "gp_txn_abc123",
    "idempotency_key": "ORDER_01hqydxwtxdj3kmzp3bz7jk73g",
    "status": "SUCCESS",
    "amount": 5000,
    "metadata": {
      "type": "order",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73h"
    }
  }
}
```

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `event` | string | `transaction.success` triggers payment processing; `transaction.failed` marks the payment as failed |
| `data.transaction_id` | string | Genuka Pay internal transaction identifier |
| `data.idempotency_key` | string | Entity reference — format `ORDER_<id>`, `SUB_<id>`, `APP_SUB_<id>`, or `TXN_<id>` |
| `data.status` | string | `SUCCESS` or `FAILED` |
| `data.amount` | number | Amount charged |
| `data.metadata` | object | Passthrough metadata; `type` can be `order` or `AI_TOKEN_TOPUP` |

**Success response** (`200 OK`):
```json
{ "status": "ok" }
```

**Error responses**
- `401 Unauthorized` — invalid webhook signature: `{ "error": "Invalid signature" }`

---

## SMS Gate

### `POST` `/2023-11/smsgate/webhook/{connectionId}`

Inbound webhook from an SMS Gate Android device. Routes to the appropriate handler based on the `X-Webhook-Event` request header. Supported events:

| Event | Behaviour |
|---|---|
| `sms:received` | Creates or finds the customer and inbox conversation, delegates to `ChannelManager` |
| `sms:sent` | Updates campaign customer and inbox message delivery status to `sent` |
| `sms:delivered` | Updates statuses to `delivered` |
| `sms:failed` | Updates statuses to `failed`, records error messages |
| `mms:received` | Acknowledged but not yet processed |
| _(unknown)_ | Returns `200 ok` silently |

> **Auth:** Not required. Called by SMS Gate device.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `connectionId` | string (ULID) | ID of the `SmsConnection` record for this device |

**Request headers (from SMS Gate)**

| Header | Description |
|---|---|
| `X-Webhook-Event` | Event type: `sms:received`, `sms:sent`, `sms:delivered`, `sms:failed`, `mms:received` |

**Request body (sms:received)**

| Field | Type | Description |
|---|---|---|
| `phoneNumber` | string | Sender's phone number |
| `message` | string | SMS body text |
| `id` | string | External message ID from the device |
| `deviceId` | string | SMS Gate device identifier |

```http
POST /2023-11/smsgate/webhook/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
X-Webhook-Event: sms:received

{
  "phoneNumber": "+237677123456",
  "message": "Hello, I need help",
  "id": "sms_ext_abc123",
  "deviceId": "device_xyz"
}
```

**Request body (sms:sent / sms:delivered / sms:failed)**

For delivery status events the gateway sends `id` (the original message ID) and `state` (lowercase status string). For failed events an optional `recipients` array carries per-recipient error details.

| Field | Type | Description |
|---|---|---|
| `id` | string | Original outbound message ID (matches `provider_message_id` on `CampaignCustomer`) |
| `state` | string | `sent`, `delivered`, or `failed` |
| `recipients` | array | (Failed only) Array of recipient objects with `state` and `error` fields |

```http
POST /2023-11/smsgate/webhook/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
X-Webhook-Event: sms:delivered

{
  "id": "sms_ext_abc123",
  "state": "delivered"
}
```

```http
POST /2023-11/smsgate/webhook/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
X-Webhook-Event: sms:failed

{
  "id": "sms_ext_abc123",
  "state": "failed",
  "recipients": [
    { "state": "Failed", "error": "Subscriber absent" }
  ]
}
```

**Success response** (`200 OK`):
```json
{ "status": "ok" }
```

**Error responses**
- `200 OK` with `{ "status": "ignored" }` — `connectionId` not found (always 200 to prevent retries).

---

## Telegram

### `POST` `/2023-11/telegram/webhook`

Telegram Bot API webhook receiver. Processes incoming Telegram updates and supports the following bot commands:

| Command | Behaviour |
|---|---|
| `/register <userId> [lang]` | Links the Telegram chat to a Genuka user and subscribes them to notifications in the given language |
| `/language <code>` | Updates the notification language for the registered chat |
| `/help` | Returns the list of available commands |
| `/start` | Replies with the chat's Telegram ID |

> **Auth:** Not required. Called by Telegram servers only.

```http
POST /2023-11/telegram/webhook HTTP/1.1
Host: api.genuka.com
Content-Type: application/json

{
  "update_id": 123456789,
  "message": {
    "message_id": 1,
    "from": { "id": 987654321, "username": "johndoe" },
    "chat": { "id": 987654321, "type": "private" },
    "text": "/register 01hqydxwtxdj3kmzp3bz7jk73g fr"
  }
}
```

**Inbound webhook payload fields**

| Field | Type | Description |
|---|---|---|
| `update_id` | integer | Telegram update identifier (unique per bot) |
| `message` | object | Present for text messages and bot commands |
| `message.message_id` | integer | Telegram message identifier |
| `message.from` | object | Sender information (`id`, `username`, `first_name`, etc.) |
| `message.chat` | object | Chat context (`id`, `type`). `chat.id` is the Telegram chat ID stored as the notification channel value |
| `message.text` | string | Message text. Bot commands `/register`, `/language`, `/help`, `/start` are parsed here |
| `my_chat_member` | object | Present when the bot membership status changes (e.g. user blocks the bot). `my_chat_member.chat.id` is used as the chat ID fallback when `message` is absent |

**Success response** (`200 OK`):
```json
{ "success": true }
```

---

## Broadcasting

### `GET|POST` `/2023-11/broadcasting/auth`

Authenticate a private or presence Laravel Echo / Pusher channel. This is a standard Laravel broadcasting auth endpoint consumed by the frontend's `laravel-echo` client.

> **Auth:** Required — `Authorization: Bearer <token>` + `X-Company: <companyId>`.

```http
POST /2023-11/broadcasting/auth HTTP/1.1
Host: api.genuka.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "socket_id": "123456.7890123",
  "channel_name": "private-company.01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Success response** (`200 OK`):
```json
{
  "auth": "app_key:hmac_signature"
}
```

**Error responses**
- `401 Unauthorized` — unauthenticated or channel subscription forbidden.
- `403 Forbidden` — user not authorized to join the requested channel.

---

## Caddy Configuration

### `GET` `/2023-11/caddy-configuration`

Retrieve the latest Caddy reverse-proxy JSON configuration stored in S3. Used by the deployment infrastructure to synchronize Caddy routing rules.

> **Auth:** Not required (internal infrastructure use).

```http
GET /2023-11/caddy-configuration HTTP/1.1
Host: api.genuka.com
Accept: application/json
```

**Success response** (`200 OK` — Content-Type: `application/json`):

Raw Caddy configuration JSON as stored in S3. Structure varies; a minimal example:
```json
{
  "apps": {
    "http": {
      "servers": {
        "srv0": {
          "routes": [
            {
              "match": [{ "host": ["myshop.genuka.com"] }],
              "handle": [{ "handler": "reverse_proxy", "upstreams": [{ "dial": "app:3000" }] }],
              "terminal": true
            }
          ]
        }
      }
    }
  }
}
```

**Error responses**
- `404 Not Found` — no configuration file found in S3: `{ "error": "No configuration files found." }`

---

## Public Company Profile

### `GET` `/2023-11/companies/{company}`

Retrieve a public company profile. This is the primary endpoint used by storefronts and checkout pages to obtain branding, available payment methods, currencies, and shipping options. No authentication required.

> **Auth:** Not required.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `company` | string (ULID) | Company ULID |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `include` | string | No | — | Comma-separated eager-load relations. Supported: `shippingFees` |

```http
GET /2023-11/companies/01hqydxwtxdj3kmzp3bz7jk73g?include=shippingFees HTTP/1.1
Host: api.genuka.com
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "My Shop",
  "description": "The best shop in town",
  "handle": "myshop",
  "type": "retail",
  "company_code": "MSH001",
  "address": {
    "street": "123 Main St",
    "city": "Douala",
    "country": "CM"
  },
  "currency": {
    "code": "XAF",
    "name": "CFA Franc BEAC"
  },
  "currencies": [
    { "code": "XAF", "label": "CFA Franc BEAC", "value": 1 },
    { "code": "USD", "label": "US Dollar", "value": 0.0016 }
  ],
  "website": "https://myshop.genuka.com",
  "logo": {
    "id": 42,
    "url": "https://cdn.genuka.com/companies/logo.jpg",
    "mime_type": "image/jpeg"
  },
  "paymentMethods": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73h",
      "name": "Mobile Money",
      "processor": "pawapay",
      "status": 1,
      "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk73i"
    }
  ],
  "storefront": { },
  "storeFrontFilters": null,
  "variables": { "primaryColor": "#FF5733" },
  "contact": {
    "email": "contact@myshop.com",
    "phone": "+237677123456"
  },
  "business_id": "RC/DLA/2020/B/1234",
  "tax_id": "M012345678901N",
  "hasBlogs": false
}
```

**Error responses**
- `404 Not Found` — company ULID does not exist: `{ "message": "No query results for model." }`
