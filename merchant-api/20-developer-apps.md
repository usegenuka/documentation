---
title: "Developer Platform — Apps, OAuth, API Keys & Webhooks"
description: "The Developer Platform lets third-party developers register and manage apps, integrate them into merchant stores via OAuth 2.0, issue API keys for direct access"
sidebarTitle: "Developer Platform"
---

The Developer Platform lets third-party developers register and manage apps, integrate them into merchant stores via OAuth 2.0, issue API keys for direct access, and subscribe to real-time business events via webhooks. There are two route groups: `/developer/*` for developer-account-scoped operations (building and publishing apps) and `/admin/*` for merchant-scoped operations (installing apps, managing webhooks and API keys from within a store).

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/developer/auth/login` | Developer auth form redirect (no-op, HTML redirect) |
| `POST` | `/2023-11/developer/auth/login` | Developer login — returns access token |
| `POST` | `/2023-11/developer/auth/logout` | Revoke developer token |
| `GET` | `/2023-11/developer/auth/register` | Developer register form redirect (no-op) |
| `POST` | `/2023-11/developer/auth/register` | Register developer account + company |
| `POST` | `/2023-11/developer/auth/password/email` | Send password-reset link |
| `POST` | `/2023-11/developer/auth/password/reset` | Reset developer password |
| `GET` | `/2023-11/developer/me` | Get authenticated developer profile |
| `DELETE` | `/2023-11/developer/me/delete` | Delete authenticated developer account |
| `PUT` | `/2023-11/developer/me/metadata` | Update authenticated developer metadata |
| `GET` | `/2023-11/developer/apps` | List apps owned by developer company |
| `POST` | `/2023-11/developer/apps` | Create a new app |
| `GET` | `/2023-11/developer/apps/{app}` | Get app details |
| `PUT` | `/2023-11/developer/apps/{app}` | Update app |
| `DELETE` | `/2023-11/developer/apps/{app}` | Delete app (draft only) |
| `GET` | `/2023-11/developer/apps/{app}/secret` | Reveal app client secret |
| `PATCH` | `/2023-11/developer/apps/{app}/status` | Change app status |
| `GET` | `/2023-11/developer/apps-reviews` | List app reviews for developer's apps |
| `POST` | `/2023-11/developer/apps-reviews` | Create an app review |
| `GET` | `/2023-11/developer/apps-reviews/{reviewId}` | Get app review details |
| `PUT` | `/2023-11/developer/apps-reviews/{reviewId}` | Update app review |
| `DELETE` | `/2023-11/developer/apps-reviews/{reviewId}` | Delete app review |
| `GET` | `/2023-11/developer/app-shortcuts` | List app shortcuts |
| `POST` | `/2023-11/developer/app-shortcuts` | Create app shortcut |
| `GET` | `/2023-11/developer/app-shortcuts/{appShortcut}` | Get app shortcut |
| `PUT` | `/2023-11/developer/app-shortcuts/{appShortcut}` | Update app shortcut |
| `DELETE` | `/2023-11/developer/app-shortcuts/{appShortcut}` | Delete app shortcut |
| `POST` | `/2023-11/developer/apps/oauth/initiate` | Initiate OAuth authorization flow |
| `POST` | `/2023-11/developer/apps/oauth/authorize` | Authorize OAuth request (approve/deny) |
| `POST` | `/2023-11/developer/apps/oauth/refresh` | Refresh OAuth access token |
| `GET` | `/2023-11/developer/payment-methods` | List payment methods |
| `POST` | `/2023-11/developer/payment-methods` | Create / update payment method |
| `GET` | `/2023-11/developer/payment-methods/{paymentMethod}` | Get payment method |
| `PUT` | `/2023-11/developer/payment-methods/{paymentMethod}` | Update payment method |
| `DELETE` | `/2023-11/developer/payment-methods/{paymentMethod}` | Delete payment method |
| `GET` | `/2023-11/developer/payment-methods/stripe/connect` | Get Stripe Connect OAuth URL |
| `POST` | `/2023-11/developer/payment-methods/stripe/direct-keys` | Save direct Stripe keys |
| `GET` | `/2023-11/developer/payment-methods/stripe/webhook-url` | Get Stripe direct webhook URL |
| `POST` | `/2023-11/developer/upload/presign` | Generate S3 presigned PUT URL |
| `POST` | `/2023-11/developer/upload/multipart/create` | Create S3 multipart upload |
| `POST` | `/2023-11/developer/upload/multipart/complete` | Complete S3 multipart upload |
| `POST` | `/2023-11/developer/upload/multipart/abort` | Abort S3 multipart upload |
| `DELETE` | `/2023-11/developer/upload/by-key` | Delete S3 object by key |
| `GET` | `/2023-11/developer/wallets` | List developer company wallet transactions |
| `POST` | `/2023-11/developer/withdraw` | Submit a withdrawal request |
| `GET` | `/2023-11/developer/withdraw/withdraw-requests` | List withdrawal requests |
| `GET` | `/2023-11/admin/apps` | List all published apps (merchant view) |
| `GET` | `/2023-11/admin/apps/{app}` | Get app details (merchant view) |
| `PATCH` | `/2023-11/admin/apps/{app}/status` | Change app status (admin/super-admin) |
| `POST` | `/2023-11/admin/apps/oauth/initiate` | Initiate OAuth (merchant) |
| `POST` | `/2023-11/admin/apps/oauth/authorize` | Authorize OAuth (merchant) |
| `POST` | `/2023-11/admin/apps/oauth/refresh` | Refresh OAuth token (merchant) |
| `POST` | `/2023-11/admin/apps/subscribe` | Subscribe company to an app plan |
| `GET` | `/2023-11/admin/apps-installed` | List installed apps for company |
| `GET` | `/2023-11/admin/apps-installed/{appInstall}` | Get installed app + redirect URL |
| `DELETE` | `/2023-11/admin/apps-installed/{appInstall}` | Uninstall app from company |
| `GET` | `/2023-11/admin/apps-reviews` | List app reviews (admin/super-admin) |
| `POST` | `/2023-11/admin/apps-reviews` | Create app review |
| `GET` | `/2023-11/admin/apps-reviews/{reviewId}` | Get app review |
| `PUT` | `/2023-11/admin/apps-reviews/{reviewId}` | Update app review |
| `DELETE` | `/2023-11/admin/apps-reviews/{reviewId}` | Delete app review |
| `GET` | `/2023-11/admin/apikeys` | List API keys for company |
| `POST` | `/2023-11/admin/apikeys` | Create API key |
| `DELETE` | `/2023-11/admin/apikeys/{apikey}` | Delete API key |
| `GET` | `/2023-11/admin/webhooks` | List webhooks |
| `POST` | `/2023-11/admin/webhooks` | Create webhook |
| `GET` | `/2023-11/admin/webhooks/count` | Count webhooks |
| `GET` | `/2023-11/admin/webhooks/{webhook}` | Get webhook |
| `PUT` | `/2023-11/admin/webhooks/{webhook}` | Update webhook |
| `DELETE` | `/2023-11/admin/webhooks/{webhook}` | Delete webhook |
| `PATCH` | `/2023-11/admin/webhooks/{webhook}/toggle` | Toggle webhook active state |
| `POST` | `/2023-11/admin/webhooks/{webhook}/test` | Send test event to webhook |
| `PATCH` | `/2023-11/admin/webhooks/{webhook}/regenerate-secret` | Regenerate webhook signing secret |

---

## Developer Auth

These endpoints use the `developer` guard (Laravel Sanctum). They do **not** require `X-Company` header.

### `GET` `/2023-11/developer/auth/login`

No-op endpoint — returns a redirect placeholder for the login form. Not useful for API clients.

**Success response** (`200 OK`): HTML redirect or empty body.

---

### `POST` `/2023-11/developer/auth/login`

Authenticate a developer account and return a Sanctum access token.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `email` | string | Yes | valid email | Developer email address |
| `password` | string | Yes | — | Developer password |

```http
POST /2023-11/developer/auth/login HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "email": "dev@example.com",
  "password": "secret1234"
}
```

**Success response** (`200 OK`):
```json
{
  "token": "1|AbCdEfGhIjKlMnOpQrStUvWxYz123456",
  "name": "Jane Smith",
  "abilities": ["*"],
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Error responses**
- `401` — `{ "message": "Invalid email or password." }`

---

### `POST` `/2023-11/developer/auth/logout`

Revoke the current developer access token and its refresh token.

**Request body:** None — authentication is performed via the `Authorization: Bearer <token>` header only.

```http
POST /2023-11/developer/auth/logout HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Successfully logged out" }
```

**Error responses**
- `401` — `{ "message": "Unauthenticated." }`

---

### `GET` `/2023-11/developer/auth/register`

No-op endpoint — redirect placeholder. Not useful for API clients.

---

### `POST` `/2023-11/developer/auth/register`

Register a new developer account. Creates both a Company (type=`developer`) and a Developer user, then issues a Sanctum token.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company.name` | string | Yes | — | Developer company name |
| `company.currency_code` | string | Yes | — | ISO 4217 currency code (e.g. `USD`, `XAF`) |
| `company.currency_name` | string | No | — | Currency display name |
| `company.description` | string | No | — | Company description |
| `company.metadata` | object | No | — | Arbitrary key-value metadata |
| `company.logoUrl` | string | No | valid URL | Logo URL to fetch and attach |
| `user.first_name` | string | Yes | — | Developer first name |
| `user.last_name` | string | Yes | — | Developer last name |
| `user.email` | string | Yes | unique | Developer email |
| `user.phone` | string | Yes | — | Developer phone |
| `user.password` | string | Yes | — | Password |

```http
POST /2023-11/developer/auth/register HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "company": {
    "name": "Acme Dev Studio",
    "currency_code": "USD",
    "currency_name": "US Dollar"
  },
  "user": {
    "first_name": "Jane",
    "last_name": "Smith",
    "email": "jane@acmedev.io",
    "phone": "+12025550101",
    "password": "secret1234"
  }
}
```

**Success response** (`200 OK`):
```json
{
  "token": "2|XyZ...",
  "name": "Jane Smith",
  "abilities": ["products.*", "orders.*"],
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g"
}
```

**Error responses**
- `400` — `{ "message": "Company payload is required" }`
- `400` — `{ "message": "User with given email already exists. Please login" }`

---

### `POST` `/2023-11/developer/auth/password/email`

Send a password-reset link to a developer email address.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `email` | string | Yes | valid email | Developer email |

```http
POST /2023-11/developer/auth/password/email HTTP/1.1
Content-Type: application/json
Accept: application/json

{ "email": "jane@acmedev.io" }
```

**Success response** (`200 OK`):
```json
{ "message": "We have emailed your password reset link." }
```

**Error responses**
- `400` — `{ "message": "We can't find a user with that email address." }`
- `422` — `{ "message": "The given data was invalid.", "errors": { "email": ["The email field is required."] } }`

---

### `POST` `/2023-11/developer/auth/password/reset`

Reset developer password using the token from the reset email.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `token` | string | Yes | — | Reset token from email link |
| `email` | string | Yes | valid email | Developer email |
| `password` | string | Yes | min 8 chars, confirmed | New password |
| `password_confirmation` | string | Yes | must match `password` | Confirm new password |

```http
POST /2023-11/developer/auth/password/reset HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "token": "aAbBcCdDeEfF1234567890abcdef",
  "email": "jane@acmedev.io",
  "password": "newSecret42!",
  "password_confirmation": "newSecret42!"
}
```

**Success response** (`200 OK`):
```json
{ "message": "Your password has been reset." }
```

**Error responses**
- `400` — `{ "message": "This password reset token is invalid." }`

---

## Developer Profile

### `GET` `/2023-11/developer/me`

Get the authenticated developer's profile including company stats.

```http
GET /2023-11/developer/me HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "first_name": "Jane",
  "last_name": "Smith",
  "email": "jane@acmedev.io",
  "company": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "name": "Acme Dev Studio",
    "type": "developer",
    "currency_code": "USD"
  },
  "totalApps": 3,
  "walletBalance": 250.00,
  "totalInstallations": 42,
  "draftApps": 1,
  "appPubliched": 2
}
```

**Error responses**
- `401` — `{ "message": "Unauthenticated." }`

---

### `DELETE` `/2023-11/developer/me/delete`

Permanently delete the authenticated developer account. All Passport tokens are revoked first.

```http
DELETE /2023-11/developer/me/delete HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Utilisateur supprimé avec succès" }
```

**Error responses**
- `401` — `{ "message": "Unauthenticated." }`

---

### `PUT` `/2023-11/developer/me/metadata`

Merge arbitrary key-value pairs into the developer's `metadata` JSON column. Existing keys not in the request are preserved.

**Request body**: Any flat JSON object. All top-level keys (except the reserved keys `metadata` and `anonymous`) are merged into the stored metadata object. There is no fixed schema — send any key-value pairs your application needs.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `<any key>` | any | No | — | Arbitrary metadata key to upsert (e.g. `lang`, `timezone`, `theme`) |

```http
PUT /2023-11/developer/me/metadata HTTP/1.1
Authorization: Bearer <token>
Content-Type: application/json
Accept: application/json

{
  "lang": "en",
  "timezone": "Africa/Douala",
  "marketing_opted_in": true
}
```

**Success response** (`200 OK`): Returns the updated developer user resource.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "first_name": "Jane",
  "last_name": "Smith",
  "email": "jane@acmedev.io",
  "phone": "+12025550101",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "metadata": {
    "lang": "en",
    "timezone": "Africa/Douala",
    "marketing_opted_in": true
  },
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `401` — `{ "message": "Unauthenticated." }`

---

## Apps (Developer)

### `GET` `/2023-11/developer/apps`

List apps. Developer-type companies see only their own apps. Business-type companies see only published apps. Super-admins see all.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Fuzzy search on `name` |
| `filter[name]` | string | No | — | Partial match on name |
| `include` | string | No | — | Comma-separated: `company`, `medias`, `developers` |
| `sort` | string | No | `name` | One of: `name`, `price`, `created_at`, `updated_at` (prefix `-` for DESC) |
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page |

```http
GET /2023-11/developer/apps?filter[search]=crm&include=company&per_page=10 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01k1az40v1f44bwbs1rc459dxy",
      "name": "CRM Connect",
      "handle": "crm-connect",
      "description": "A CRM integration app",
      "callback_url": "https://myapp.io/oauth/callback",
      "redirect_url": "https://myapp.io/welcome",
      "webhook_url": "https://myapp.io/webhooks",
      "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
      "client_secret": "•••••••••••••••",
      "status": "published",
      "version": "1.0.0",
      "price": 0,
      "configuration": { "scopes": ["products.*", "orders.*"] },
      "metadata": {},
      "support_email": "support@myapp.io",
      "support_url": "https://myapp.io/support",
      "privacy_policy_url": "https://myapp.io/privacy",
      "terms_of_service_url": "https://myapp.io/terms",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "submitted_at": null,
      "published_at": "2024-03-15T10:30:00.000000Z",
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "plans": [
        { "id": "01k1...", "type": "monthly", "label": "Monthly", "price": 0 }
      ],
      "installState": 0,
      "installed": false,
      "installation": null,
      "logo": null,
      "medias": [],
      "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 10, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — `{ "message": "Unauthenticated." }`

---

### `POST` `/2023-11/developer/apps`

Create a new app. A Passport OAuth client is automatically created and the `client_id`/`client_secret` are returned (the only time the secret is shown in full).

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | unique, max 255 | App name |
| `companyId` | string | Yes | exists in companies | Owning company ULID |
| `callback_url` | string | Yes | valid URL, max 255 | OAuth redirect URI |
| `description` | string | No | max 255 | Short description |
| `support_email` | string | No | valid email, max 255 | Support email |
| `redirect_url` | string | No | valid URL | Post-install redirect |
| `webhook_url` | string | No | valid URL | Webhook URL for install/uninstall events |
| `metadata` | string | No | valid JSON | Arbitrary JSON metadata |
| `price` | number | No | min 0 | Base price (0 = free) |
| `currency_code` | string | No | max 3 | ISO 4217 code (default `USD`) |
| `currency_name` | string | No | max 255 | Currency display name |
| `scopes` | array | No | — | Permission scopes the app requests |
| `privacy_policy_url` | string | No | valid URL, max 255 | Privacy policy URL |
| `terms_of_service_url` | string | No | valid URL, max 255 | Terms of service URL |
| `support_url` | string | No | — | Support page URL |
| `version` | string | No | — | App version string |
| `plans` | array | Yes | — | At least one plan object |
| `plans[].type` | string | Yes (with plans) | `monthly`, `quarterly`, `annual` | Billing cycle |
| `plans[].price` | number | Yes (with plans) | min 0 | Price for this plan |
| `medias` | array | No | — | Media attachments (`s3_key`, `collection_name`, `file_name`) |

```http
POST /2023-11/developer/apps HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "CRM Connect",
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
  "callback_url": "https://myapp.io/oauth/callback",
  "description": "A CRM integration app",
  "support_email": "support@myapp.io",
  "price": 0,
  "currency_code": "USD",
  "scopes": ["products.*", "orders.*"],
  "plans": [
    { "type": "monthly", "price": 0 }
  ]
}
```

**Success response** (`200 OK`): App object with `client_secret` shown in full (only revealed at creation time).
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "A CRM integration app",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "EkLhw01kOPGk1DZGAqOq8iGJ6v4F9kpUyudhPKWa",
  "status": "draft",
  "version": null,
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": null,
  "support_email": "support@myapp.io",
  "support_url": null,
  "privacy_policy_url": null,
  "terms_of_service_url": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": null,
  "published_at": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 0,
  "installed": false,
  "installation": null,
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

**Error responses**
- `400` — `{ "error": "The name has already been taken." }`
- `422` — `{ "message": "The given data was invalid.", "errors": { "name": ["The name field is required."] } }`

---

### `GET` `/2023-11/developer/apps/{app}`

Get details of a single app.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

```http
GET /2023-11/developer/apps/01k1az40v1f44bwbs1rc459dxy HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "A CRM integration app",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "•••••••••••••••",
  "status": "published",
  "version": "1.0.0",
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": {},
  "support_email": "support@myapp.io",
  "support_url": "https://myapp.io/support",
  "privacy_policy_url": "https://myapp.io/privacy",
  "terms_of_service_url": "https://myapp.io/terms",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": null,
  "published_at": "2024-03-15T10:30:00.000000Z",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 0,
  "installed": false,
  "installation": null,
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

**Error responses**
- `404` — `{ "message": "No query results for model." }`

---

### `PUT` `/2023-11/developer/apps/{app}`

Update an existing app. Only fields provided are updated. Pass `regenerate_keys: true` to rotate the OAuth client ID and secret.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `companyId` | string | Yes | exists in companies | Company ULID |
| `name` | string | No | unique (excluding self), max 255 | New app name |
| `description` | string | No | max 255 | Updated description |
| `callback_url` | string | No | valid URL, max 255 | OAuth redirect URI |
| `redirect_url` | string | No | valid URL, max 255 | Post-install redirect |
| `webhook_url` | string | No | valid URL, max 255 | Webhook URL |
| `support_email` | string | No | valid email, max 255 | Support email |
| `price` | number | No | min 0 | Base price |
| `currency_code` | string | No | max 3 | Currency code |
| `currency_name` | string | No | max 255 | Currency name |
| `privacy_policy_url` | string | No | valid URL, max 255 | Privacy policy URL |
| `terms_of_service_url` | string | No | valid URL, max 255 | Terms URL |
| `version` | string | No | — | Version string |
| `support_url` | string | No | — | Support URL |
| `status` | string | No | `pending` only | Submit for review |
| `scopes` | array | No | — | Updated permission scopes |
| `plans` | array | No | — | Upsert billing plans |
| `regenerate_keys` | boolean | No | — | Rotate `client_id` / `client_secret` |
| `medias` | array | No | — | S3 media to attach |

```http
PUT /2023-11/developer/apps/01k1az40v1f44bwbs1rc459dxy HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
  "description": "Updated CRM integration",
  "status": "pending"
}
```

**Success response** (`200 OK`): Updated app resource.
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "Updated CRM integration",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "•••••••••••••••",
  "status": "pending",
  "version": "1.0.0",
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": {},
  "support_email": "support@myapp.io",
  "support_url": "https://myapp.io/support",
  "privacy_policy_url": "https://myapp.io/privacy",
  "terms_of_service_url": "https://myapp.io/terms",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": "2024-03-15T10:30:00.000000Z",
  "published_at": null,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 0,
  "installed": false,
  "installation": null,
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

**Error responses**
- `400` — `{ "error": "..." }`
- `404` — `{ "message": "No query results for model." }`

---

### `DELETE` `/2023-11/developer/apps/{app}`

Delete an app. Only allowed when status is `draft` and the calling user has the `admin` role in the owning company.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

```http
DELETE /2023-11/developer/apps/01k1az40v1f44bwbs1rc459dxy HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "App deleted successfully" }
```

**Error responses**
- `403` — `{ "error": "App does not belong to the company" }`
- `403` — `{ "error": "App is not in draft status" }`
- `403` — `{ "error": "You are not authorized to delete this app" }`

---

### `GET` `/2023-11/developer/apps/{app}/secret`

Reveal the full `client_secret` for an app. The secret is masked (`•••`) in all other responses.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

```http
GET /2023-11/developer/apps/01k1az40v1f44bwbs1rc459dxy/secret HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): App resource with `client_secret` in plaintext.
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "A CRM integration app",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "EkLhw01kOPGk1DZGAqOq8iGJ6v4F9kpUyudhPKWa",
  "status": "published",
  "version": "1.0.0",
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": {},
  "support_email": "support@myapp.io",
  "support_url": "https://myapp.io/support",
  "privacy_policy_url": "https://myapp.io/privacy",
  "terms_of_service_url": "https://myapp.io/terms",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": null,
  "published_at": "2024-03-15T10:30:00.000000Z",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 0,
  "installed": false,
  "installation": null,
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

**Error responses**
- `404` — `{ "message": "No query results for model." }`

---

### `PATCH` `/2023-11/developer/apps/{app}/status`

Change the lifecycle status of an app. Moving to `pending` records `submitted_at`; moving to `published` records `published_at` and triggers an email notification to the developer admin.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `status` | string | Yes | `draft`, `pending`, `rejected`, `published`, `archived` | New status |

```http
PATCH /2023-11/developer/apps/01k1az40v1f44bwbs1rc459dxy/status HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "status": "pending" }
```

**Success response** (`200 OK`): Updated app resource.
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "A CRM integration app",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "•••••••••••••••",
  "status": "pending",
  "version": "1.0.0",
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": {},
  "support_email": "support@myapp.io",
  "support_url": "https://myapp.io/support",
  "privacy_policy_url": "https://myapp.io/privacy",
  "terms_of_service_url": "https://myapp.io/terms",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": "2024-03-15T10:30:00.000000Z",
  "published_at": null,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 0,
  "installed": false,
  "installation": null,
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

**Error responses**
- `422` — `{ "message": "The given data was invalid.", "errors": { "status": ["..."] } }`

---

## App Reviews

### `GET` `/2023-11/developer/apps-reviews` and `GET` `/2023-11/admin/apps-reviews`

List app reviews. Developer-company users see reviews for their own apps only; super-admins see all.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[app_id]` | string | No | — | Filter by app ULID |
| `filter[reviewer_id]` | string | No | — | Filter by reviewer user ID |
| `filter[status]` | string | No | — | `pending`, `in_progress`, or `completed` |
| `filter[search]` | string | No | — | Search across app name, reviewer name, comments |
| `include` | string | No | — | `app`, `reviewer` |
| `sort` | string | No | `-created_at` | `created_at`, `updated_at`, `started_at`, `completed_at` |
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 15 | — |

```http
GET /2023-11/developer/apps-reviews?filter[status]=pending&include=app HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated list of review objects.
```json
{
  "data": [
    {
      "id": "01k1bz40v1f44bwbs1rc459dxy",
      "app_id": "01k1az40v1f44bwbs1rc459dxy",
      "reviewer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "status": "pending",
      "comments": "Awaiting initial review",
      "started_at": null,
      "completed_at": null,
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "app": { "id": "01k1az40v1f44bwbs1rc459dxy", "name": "CRM Connect" }
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

---

### `POST` `/2023-11/developer/apps-reviews` and `POST` `/2023-11/admin/apps-reviews`

Create a new review record for an app.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `app_id` | string | Yes | exists in apps | App ULID to review |
| `reviewer_id` | string | Yes | exists in users | Reviewer user ID |
| `status` | string | Yes | `pending`, `in_progress`, `completed` | Initial status |
| `comments` | string | No | — | Review notes |
| `started_at` | string | No | valid date | When review started |
| `completed_at` | string | No | valid date | When review was completed |

```http
POST /2023-11/admin/apps-reviews HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "reviewer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "pending"
}
```

**Success response** (`201 Created`): Review object with `app` and `reviewer` relations loaded.
```json
{
  "id": "01k1bz40v1f44bwbs1rc459dxy",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "reviewer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "pending",
  "comments": null,
  "started_at": null,
  "completed_at": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "app": {
    "id": "01k1az40v1f44bwbs1rc459dxy",
    "name": "CRM Connect",
    "handle": "crm-connect",
    "status": "pending"
  },
  "reviewer": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "first_name": "Jane",
    "last_name": "Smith",
    "email": "jane@acmedev.io"
  }
}
```

---

### `GET` `/2023-11/developer/apps-reviews/{reviewId}` and `GET` `/2023-11/admin/apps-reviews/{reviewId}`

Get a single review with its related app.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `reviewId` | string (ULID) | Review ID |

**Success response** (`200 OK`): Review object with `app` loaded.
```json
{
  "id": "01k1bz40v1f44bwbs1rc459dxy",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "reviewer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "in_progress",
  "comments": "Checking OAuth flow and permission scopes.",
  "started_at": "2024-03-16T09:00:00.000000Z",
  "completed_at": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "app": {
    "id": "01k1az40v1f44bwbs1rc459dxy",
    "name": "CRM Connect",
    "handle": "crm-connect",
    "status": "pending"
  }
}
```

---

### `PUT` `/2023-11/developer/apps-reviews/{reviewId}` and `PUT` `/2023-11/admin/apps-reviews/{reviewId}`

Update an existing review.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `reviewId` | string (ULID) | Review ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `reviewer_id` | string | Yes | exists in users | Reviewer user ID |
| `app_id` | string | No | exists in apps | App to associate |
| `status` | string | No | `pending`, `in_progress`, `completed` | Updated status |
| `comments` | string | No | nullable | Updated comments |
| `started_at` | string | No | nullable date | Start timestamp |
| `completed_at` | string | No | nullable date | Completion timestamp |

**Success response** (`200 OK`): Updated review with `app` and `reviewer`.
```json
{
  "id": "01k1bz40v1f44bwbs1rc459dxy",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "reviewer_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "completed",
  "comments": "All checks passed. Ready to publish.",
  "started_at": "2024-03-16T09:00:00.000000Z",
  "completed_at": "2024-03-17T14:00:00.000000Z",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-17T14:00:00.000000Z",
  "app": {
    "id": "01k1az40v1f44bwbs1rc459dxy",
    "name": "CRM Connect",
    "handle": "crm-connect",
    "status": "pending"
  },
  "reviewer": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "first_name": "Jane",
    "last_name": "Smith",
    "email": "jane@acmedev.io"
  }
}
```

---

### `DELETE` `/2023-11/developer/apps-reviews/{reviewId}` and `DELETE` `/2023-11/admin/apps-reviews/{reviewId}`

Delete a review.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `reviewId` | string (ULID) | Review ID |

**Success response** (`200 OK`):
```json
{ "message": "Review deleted successfully" }
```

---

## App Shortcuts

App shortcuts are deep-link entries injected into the merchant dashboard UI by an installed app.

### `GET` `/2023-11/developer/app-shortcuts`

List app shortcuts. Results are filtered by the authenticated company via query filters.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search across app name, shortcut name, url, color, location |
| `filter[app_id]` | string | No | — | Filter by app ULID |
| `filter[company_id]` | string | No | — | Filter by company ULID |
| `filter[app_install_id]` | string | No | — | Filter by app install ID |
| `filter[location]` | string | No | — | Partial match on location string |
| `sort` | string | No | — | `created_at`, `updated_at` |
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 15 | — |

```http
GET /2023-11/developer/app-shortcuts?filter[app_id]=01k1az40v1f44bwbs1rc459dxy HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated list of app shortcut objects.
```json
{
  "data": [
    {
      "id": "01k2az40v1f44bwbs1rc459dxy",
      "app_id": "01k1az40v1f44bwbs1rc459dxy",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Open CRM",
      "icon": "link",
      "url": "https://myapp.io/crm",
      "color": "#4F46E5",
      "location": "sidebar",
      "is_active": true,
      "is_external": true,
      "metadata": {},
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

---

### `POST` `/2023-11/developer/app-shortcuts`

Create an app shortcut.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `app_id` | string | Yes | — | App ULID |
| `name` | string | Yes | — | Display name |
| `url` | string | Yes | — | Target URL |
| `icon` | string | No | — | Icon identifier |
| `color` | string | No | — | Hex color |
| `location` | string | No | — | Dashboard location (e.g. `sidebar`, `header`) |
| `is_active` | boolean | No | — | Whether shortcut is visible |
| `is_external` | boolean | No | — | Whether link opens in new tab |
| `metadata` | object | No | — | Arbitrary metadata |

```http
POST /2023-11/developer/app-shortcuts HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "Open CRM",
  "url": "https://myapp.io/crm",
  "location": "sidebar",
  "is_active": true,
  "is_external": true
}
```

**Success response** (`201 Created`): App shortcut resource object.
```json
{
  "id": "01k2az40v1f44bwbs1rc459dxy",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Open CRM",
  "icon": null,
  "url": "https://myapp.io/crm",
  "color": null,
  "location": "sidebar",
  "is_active": true,
  "is_external": true,
  "metadata": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

---

### `GET` `/2023-11/developer/app-shortcuts/{appShortcut}`

Get a single shortcut. Only returned if the shortcut belongs to the requesting company.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `appShortcut` | string (ULID) | Shortcut ID |

**Success response** (`200 OK`): Shortcut resource object.
```json
{
  "id": "01k2az40v1f44bwbs1rc459dxy",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Open CRM",
  "icon": "link",
  "url": "https://myapp.io/crm",
  "color": "#4F46E5",
  "location": "sidebar",
  "is_active": true,
  "is_external": true,
  "metadata": {},
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `404` — `{ "error": "App shortcut not available for this company" }`

---

### `PUT` `/2023-11/developer/app-shortcuts/{appShortcut}`

Update a shortcut. Only available if the shortcut belongs to the requesting company.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `appShortcut` | string (ULID) | Shortcut ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `app_id` | string | No | — | App ULID |
| `name` | string | No | — | Display name |
| `url` | string | No | — | Target URL |
| `icon` | string | No | — | Icon identifier |
| `color` | string | No | — | Hex color |
| `location` | string | No | — | Dashboard location (e.g. `sidebar`, `header`) |
| `is_active` | boolean | No | — | Whether shortcut is visible |
| `is_external` | boolean | No | — | Whether link opens in new tab |
| `metadata` | object | No | — | Arbitrary metadata |

```http
PUT /2023-11/developer/app-shortcuts/01k2az40v1f44bwbs1rc459dxy HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "color": "#7C3AED", "is_active": false }
```

**Success response** (`200 OK`): Updated shortcut resource.
```json
{
  "id": "01k2az40v1f44bwbs1rc459dxy",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Open CRM",
  "icon": "link",
  "url": "https://myapp.io/crm",
  "color": "#7C3AED",
  "location": "sidebar",
  "is_active": false,
  "is_external": true,
  "metadata": {},
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-16T08:00:00.000000Z"
}
```

**Error responses**
- `404` — `{ "error": "App shortcut not available for this company" }`

---

### `DELETE` `/2023-11/developer/app-shortcuts/{appShortcut}`

Delete a shortcut.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `appShortcut` | string (ULID) | Shortcut ID |

**Success response** (`200 OK`):
```json
{ "message": "App shortcut uninstalled" }
```

**Error responses**
- `404` — `{ "error": "App shortcut not available for this company" }`

---

## OAuth 2.0

The Genuka OAuth flow allows apps to act on behalf of a merchant company. The flow is:

1. **Initiate** — app calls `/oauth/initiate` with its `client_id` to get a short-lived `request_code`.
2. **Authorize** — merchant user approves in the dashboard, which calls `/oauth/authorize`. On approval, an authorization code is generated and the callback URL receives an HMAC-signed redirect.
3. **Exchange** — app exchanges the `code` for an access token (see the OAuth token exchange endpoint, not listed separately here; call `/2023-11/admin/apps/oauth/token` directly with client credentials).
4. **Refresh** — call `/oauth/refresh` with the refresh token to rotate credentials.

---

### `POST` `/2023-11/developer/apps/oauth/initiate` and `POST` `/2023-11/admin/apps/oauth/initiate`

Begin the authorization flow. Returns a `request_code` that the app should pass to the authorization UI.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `client_id` | string | Yes | — | Passport OAuth client UUID |
| `scopes` | array | No | — | Requested permission scopes |

```http
POST /2023-11/admin/apps/oauth/initiate HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "scopes": ["products.*", "orders.*"]
}
```

**Success response** (`200 OK`):
```json
{ "request_code": "cQCjP3WFMMZIO4iOoc1gaaxQevoof2guiplLjKl0" }
```

---

### `POST` `/2023-11/developer/apps/oauth/authorize` and `POST` `/2023-11/admin/apps/oauth/authorize`

Approve or deny an authorization request. On approval, creates or updates an `AppInstall` record, generates an auth code, and returns the signed redirect URL that the frontend should follow.

Requires the requesting company to have an active subscription if the app has a non-zero price.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `app_id` | string | Yes | exists in apps | App ULID |
| `approved` | boolean | Yes | — | `true` to approve, `false` to deny |
| `accepted_scopes` | array | No | — | Scopes the merchant explicitly accepted |
| `metadata` | object | No | — | Installation metadata |

```http
POST /2023-11/admin/apps/oauth/authorize HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "accepted_scopes": ["products.*", "orders.*"],
  "approved": true
}
```

**Success response** (`200 OK`):
```json
{
  "redirect_url": "https://myapp.io/oauth/callback?code=abc123&company_id=01hqydxwtxdj3kmzp3bz7jk73g&timestamp=1710500000&hmac=...&hmac2=..."
}
```

**Error responses**
- `400` — `{ "error": "Invalid company" }`
- `400` — `{ "error": "Invalid app" }`
- `200` — `{ "message": "Authorization denied" }` (when `approved: false` or subscription missing)

---

### `POST` `/2023-11/developer/apps/oauth/refresh` and `POST` `/2023-11/admin/apps/oauth/refresh`

Exchange a refresh token for new access and refresh tokens.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `refresh_token` | string | Yes | — | Current refresh token |
| `client_id` | string | Yes | — | Passport OAuth client UUID |
| `client_secret` | string | Yes | — | OAuth client secret |

```http
POST /2023-11/admin/apps/oauth/refresh HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "refresh_token": "def50200...",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "EkLhw01kOPGk1DZGAqOq8iGJ6v4F9kpUyudhPKWa"
}
```

**Success response** (`200 OK`):
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
  "refresh_token": "def50200...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Error responses**
- `400` — `{ "error": "Invalid or expired refresh token" }`

---

## Apps (Admin / Merchant)

### `GET` `/2023-11/admin/apps`

List apps visible to the merchant. Business-company users see published apps only; super-admins see all.

Same query parameters as `GET /2023-11/developer/apps`.

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01k1az40v1f44bwbs1rc459dxy",
      "name": "CRM Connect",
      "handle": "crm-connect",
      "description": "A CRM integration app",
      "callback_url": "https://myapp.io/oauth/callback",
      "redirect_url": "https://myapp.io/welcome",
      "webhook_url": "https://myapp.io/webhooks",
      "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
      "client_secret": "•••••••••••••••",
      "status": "published",
      "version": "1.0.0",
      "price": 0.0,
      "configuration": { "scopes": ["products.*", "orders.*"] },
      "metadata": {},
      "support_email": "support@myapp.io",
      "support_url": "https://myapp.io/support",
      "privacy_policy_url": "https://myapp.io/privacy",
      "terms_of_service_url": "https://myapp.io/terms",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "submitted_at": null,
      "published_at": "2024-03-15T10:30:00.000000Z",
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "plans": [
        { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
      ],
      "installState": 0,
      "installed": false,
      "installation": null,
      "logo": null,
      "medias": [],
      "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

---

### `GET` `/2023-11/admin/apps/{app}`

Get a single app (merchant view).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

**Success response** (`200 OK`): App resource.
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "A CRM integration app",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "•••••••••••••••",
  "status": "published",
  "version": "1.0.0",
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": {},
  "support_email": "support@myapp.io",
  "support_url": "https://myapp.io/support",
  "privacy_policy_url": "https://myapp.io/privacy",
  "terms_of_service_url": "https://myapp.io/terms",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": null,
  "published_at": "2024-03-15T10:30:00.000000Z",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 2,
  "installed": true,
  "installation": { "id": "01j85px2cskf7twdnyp1ja8bw8", "active": true },
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

---

### `PATCH` `/2023-11/admin/apps/{app}/status`

Update app status. For platform admins / super-admins to publish, reject, or archive apps.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `app` | string (ULID) | App ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `status` | string | Yes | `draft`, `pending`, `rejected`, `published`, `archived` | New status |

```http
PATCH /2023-11/admin/apps/01k1az40v1f44bwbs1rc459dxy/status HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "status": "published" }
```

**Success response** (`200 OK`): Updated app resource.
```json
{
  "id": "01k1az40v1f44bwbs1rc459dxy",
  "name": "CRM Connect",
  "handle": "crm-connect",
  "description": "A CRM integration app",
  "callback_url": "https://myapp.io/oauth/callback",
  "redirect_url": "https://myapp.io/welcome",
  "webhook_url": "https://myapp.io/webhooks",
  "client_id": "9cf0fb5b-99b5-4249-b5b9-0e532547dafd",
  "client_secret": "•••••••••••••••",
  "status": "published",
  "version": "1.0.0",
  "price": 0.0,
  "configuration": { "scopes": ["products.*", "orders.*"] },
  "metadata": {},
  "support_email": "support@myapp.io",
  "support_url": "https://myapp.io/support",
  "privacy_policy_url": "https://myapp.io/privacy",
  "terms_of_service_url": "https://myapp.io/terms",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "submitted_at": "2024-03-14T09:00:00.000000Z",
  "published_at": "2024-03-15T10:30:00.000000Z",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "plans": [
    { "id": "01k1bz40v1f44bwbs1rc459dxy", "type": "monthly", "label": "Monthly", "price": 0 }
  ],
  "installState": 0,
  "installed": false,
  "installation": null,
  "logo": null,
  "medias": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Dev Studio" }
}
```

---

### `POST` `/2023-11/admin/apps/subscribe`

Subscribe a company to an app plan. Initiates payment via Stripe or PawaPay and returns a redirect URL to the payment page.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `app_id` | string | Yes | exists in apps | App ULID |
| `app_plan_id` | string | Yes | exists in app_plans | Plan ULID |
| `company_id` | string | Yes | exists in companies | Company ULID |
| `payment_method` | string | Yes | `stripe`, `pawapay` | Payment gateway to use |
| `success_url` | string | No | — | Redirect after payment success |
| `cancel_url` | string | No | — | Redirect after payment cancel |

```http
POST /2023-11/admin/apps/subscribe HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "app_plan_id": "01k1bzplan1f44bwbs1rc459dxy",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "payment_method": "stripe",
  "success_url": "https://dashboard.genuka.com/apps?subscribed=1",
  "cancel_url": "https://dashboard.genuka.com/apps"
}
```

**Success response** (`200 OK`):
```json
{
  "message": "Subscription process initiated successfully",
  "payment_method": "stripe",
  "redirect_url": "https://checkout.stripe.com/pay/cs_test_..."
}
```

**Error responses**
- `400` — `{ "message": "Payment URL unavailable", "payment_method": "stripe" }`
- `422` — validation errors

---

## Installed Apps

### `GET` `/2023-11/admin/apps-installed`

List all app installations for the current company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search across app name, handle, description, callback URL |
| `filter[app_id]` | string | No | — | Filter by app ULID |
| `filter[company_id]` | string | No | — | Filter by company ULID |
| `filter[status]` | string | No | — | Filter by status |
| `filter[scopes]` | string | No | — | Partial match on scopes |
| `include` | string | No | — | `app`, `shortcuts` |
| `sort` | string | No | — | `created_at`, `updated_at` |
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 15 | — |

```http
GET /2023-11/admin/apps-installed?include=shortcuts HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01j85px2cskf7twdnyp1ja8bw8",
      "app_id": "01k1az40v1f44bwbs1rc459dxy",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "installed_at": "2024-03-15T10:30:00.000000Z",
      "scopes": ["products.*", "orders.*"],
      "metadata": {},
      "active": true,
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "app": { "id": "01k1az40v1f44bwbs1rc459dxy", "name": "CRM Connect" },
      "shortcuts": []
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

---

### `GET` `/2023-11/admin/apps-installed/{appInstall}`

Get a single installation. Also returns a signed `redirect_url` that the app can use to open the merchant's store context.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `appInstall` | string (ULID) | Installation ID |

```http
GET /2023-11/admin/apps-installed/01j85px2cskf7twdnyp1ja8bw8 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01j85px2cskf7twdnyp1ja8bw8",
  "app_id": "01k1az40v1f44bwbs1rc459dxy",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "installed_at": "2024-03-15T10:30:00.000000Z",
  "scopes": ["products.*"],
  "active": true,
  "app": { "id": "01k1az40v1f44bwbs1rc459dxy", "name": "CRM Connect" },
  "shortcuts": [],
  "redirect_url": "https://myapp.io/welcome?company_id=01hqydxwtxdj3kmzp3bz7jk73g&timestamp=1710500000&hmac=...&hmac2=..."
}
```

**Error responses**
- `404` — `{ "error": "App not installed by the company" }`

---

### `DELETE` `/2023-11/admin/apps-installed/{appInstall}`

Uninstall an app from the company. Dispatches a webhook notification to the app's `webhook_url` if configured.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `appInstall` | string (ULID) | Installation ID |

```http
DELETE /2023-11/admin/apps-installed/01j85px2cskf7twdnyp1ja8bw8 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "App uninstalled" }
```

**Error responses**
- `404` — `{ "error": "App not installed by the company" }`

---

## API Keys

API keys allow programmatic access to the Genuka API without OAuth. Only users with the `admin`, `manager`, or `superadmin` role can list keys.

### `GET` `/2023-11/admin/apikeys`

List API keys.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `sort` | string | No | — | `expires_at`, `created_at`, `updated_at` |
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 15 | — |

```http
GET /2023-11/admin/apikeys HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated list of API key objects.
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk000",
      "name": "Production Key",
      "description": "Used by production server",
      "key": "aBcDeFgHiJkLmNoPqRsTuVwXyZ123456",
      "expires_at": "2025-03-15T10:30:00.000000Z",
      "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — `{ "error_code": "UNAUTHORIZED", "message": "You are not authorized to list apikeys.", "role": "..." }`

---

### `POST` `/2023-11/admin/apikeys`

Create a new API key. The key value is randomly generated (32 characters). Expires in one year by default.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | — | Human-readable name |
| `description` | string | No | — | Description of usage |
| `expires_at` | string | No | valid date | Custom expiry (defaults to +1 year) |

```http
POST /2023-11/admin/apikeys HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "Production Key",
  "description": "Used by production server"
}
```

**Success response** (`200 OK`): API key resource — includes the `key` value; store it immediately as it is not re-displayable.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk000",
  "name": "Production Key",
  "description": "Used by production server",
  "key": "aBcDeFgHiJkLmNoPqRsTuVwXyZ123456",
  "expires_at": "2025-03-15T10:30:00.000000Z",
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/apikeys/{apikey}`

Permanently delete an API key.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `apikey` | string (ULID) | API key ID |

```http
DELETE /2023-11/admin/apikeys/01hqydxwtxdj3kmzp3bz7jk000 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "APIKey deleted" }
```

---

## Webhooks

Webhooks let your app receive real-time POST notifications when business events occur. Genuka signs each delivery with an HMAC-SHA256 signature using the webhook's `secret`. The `secret` is only returned at creation time and after a regeneration.

Webhooks are automatically disabled after 10 consecutive delivery failures.

### Available events

Events follow the pattern `{resource}.{action}`. Resources include: `delivery`, `order`, `customer`, `invoice`, `payment`, `address`, `shop`, `product`, `product_variant`, `stock`, `stock_warehouse`, `supplier`, `supplier_product`, `tag`, `taggable`, `user`, `tax`, `discount`, `pickup_location`, `shipping_fee`, `warehouse`, `transfer`, `order_product`, `product_return`, `service`, `campaign`, `campaign_template`, `campaign_customer`, `inbox_conversation`, `inbox_message`. Actions for most resources: `created`, `updated`, `deleted`, `restored`.

---

### `GET` `/2023-11/admin/webhooks`

List webhooks for the current company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[is_active]` | boolean | No | — | Filter by active state |
| `filter[url]` | string | No | — | Partial match on URL |
| `filter[events]` | string | No | — | Comma-separated event names to filter on |
| `filter[search]` | string | No | — | Search description, URL, or events |
| `filter[failure_count_gte]` | integer | No | — | Minimum failure count |
| `filter[last_triggered]` | string | No | — | `never` or any other value (ever triggered) |
| `sort` | string | No | `-created_at` | `created_at`, `updated_at`, `description`, `last_triggered_at`, `failure_count` |
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 15 | — |

```http
GET /2023-11/admin/webhooks?filter[is_active]=true&sort=-created_at HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk001",
      "description": "Order notifications",
      "url": "https://myapp.io/webhooks/orders",
      "events": ["order.created", "order.updated"],
      "is_active": true,
      "last_triggered_at": "2024-03-14T09:00:00.000000Z",
      "failure_count": 0,
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-03-14T09:00:00.000000Z",
      "metadata": null
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

> Note: `secret` is hidden in list and show responses. It is only returned when the webhook is first created or the secret is regenerated.

---

### `GET` `/2023-11/admin/webhooks/count`

Return the total number of webhooks for the current company.

```http
GET /2023-11/admin/webhooks/count HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "count": 3 }
```

---

### `POST` `/2023-11/admin/webhooks`

Create a new webhook. The signing `secret` (32 random characters) is generated automatically and included in the response **once only**.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `url` | string | Yes | valid HTTPS URL | Endpoint that will receive event POSTs |
| `events` | array | Yes | min 1 item, each must be a valid event name | Events to subscribe to |
| `description` | string | No | max 255 | Human-readable label |
| `is_active` | boolean | No | — | Whether to start enabled (default `true`) |

```http
POST /2023-11/admin/webhooks HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "name": "Order webhook",
  "url": "https://myapp.io/webhooks",
  "events": ["order.created", "order.updated", "customer.created"]
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "description": null,
  "url": "https://myapp.io/webhooks",
  "events": ["order.created", "order.updated", "customer.created"],
  "secret": "aBcDeFgHiJkLmNoPqRsTuVwXyZ123456",
  "is_active": true,
  "last_triggered_at": null,
  "failure_count": 0,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "metadata": null
}
```

**Error responses**
- `422` — `{ "message": "The given data was invalid.", "errors": { "url": ["The URL must use the HTTPS protocol"], "events": ["At least one event must be selected"] } }`

---

### `GET` `/2023-11/admin/webhooks/{webhook}`

Get a single webhook.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `webhook` | string (ULID) | Webhook ID |

**Success response** (`200 OK`): Webhook resource (secret hidden).
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "description": "Order notifications",
  "url": "https://myapp.io/webhooks/orders",
  "events": ["order.created", "order.updated"],
  "is_active": true,
  "last_triggered_at": "2024-03-14T09:00:00.000000Z",
  "failure_count": 0,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-14T09:00:00.000000Z",
  "metadata": null
}
```

**Error responses**
- `404` — `{ "message": "Webhook not found" }`

---

### `PUT` `/2023-11/admin/webhooks/{webhook}`

Update a webhook's URL, events, description, or active state.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `webhook` | string (ULID) | Webhook ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `url` | string | Yes | valid HTTPS URL | Updated endpoint URL |
| `events` | array | Yes | min 1 item, each must be a valid event name | Updated event subscriptions |
| `description` | string | No | max 255 | Human-readable label |
| `is_active` | boolean | No | — | Enable or disable the webhook |

```http
PUT /2023-11/admin/webhooks/01hqydxwtxdj3kmzp3bz7jk001 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "url": "https://myapp.io/webhooks/v2",
  "events": ["order.created", "order.updated", "order.deleted"],
  "description": "All order events"
}
```

**Success response** (`200 OK`): Updated webhook resource.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "description": "All order events",
  "url": "https://myapp.io/webhooks/v2",
  "events": ["order.created", "order.updated", "order.deleted"],
  "is_active": true,
  "last_triggered_at": "2024-03-14T09:00:00.000000Z",
  "failure_count": 0,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "metadata": null
}
```

**Error responses**
- `404` — `{ "message": "Webhook not found" }`
- `422` — validation errors

---

### `DELETE` `/2023-11/admin/webhooks/{webhook}`

Delete (soft-delete) a webhook.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `webhook` | string (ULID) | Webhook ID |

**Success response** (`200 OK`): Deleted webhook resource.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "description": "Order notifications",
  "url": "https://myapp.io/webhooks/orders",
  "events": ["order.created", "order.updated"],
  "is_active": true,
  "last_triggered_at": "2024-03-14T09:00:00.000000Z",
  "failure_count": 0,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "metadata": null
}
```

**Error responses**
- `404` — `{ "message": "Webhook not found" }`

---

### `PATCH` `/2023-11/admin/webhooks/{webhook}/toggle`

Toggle the `is_active` flag on a webhook. Also resets the `failure_count` to 0.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `webhook` | string (ULID) | Webhook ID |

**Request body:** None — no body required for this action endpoint.

```http
PATCH /2023-11/admin/webhooks/01hqydxwtxdj3kmzp3bz7jk001/toggle HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Updated webhook resource with toggled `is_active`.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "description": "Order notifications",
  "url": "https://myapp.io/webhooks/orders",
  "events": ["order.created", "order.updated"],
  "is_active": false,
  "last_triggered_at": "2024-03-14T09:00:00.000000Z",
  "failure_count": 0,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "metadata": null
}
```

**Error responses**
- `404` — `{ "message": "Webhook not found" }`

---

### `POST` `/2023-11/admin/webhooks/{webhook}/test`

Send a test event payload to the webhook URL. Useful for verifying connectivity.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `webhook` | string (ULID) | Webhook ID |

**Request body:** None — the test payload is constructed automatically from the stored webhook record (id, company_id, secret).

```http
POST /2023-11/admin/webhooks/01hqydxwtxdj3kmzp3bz7jk001/test HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Webhook test sent successfully" }
```

The test payload sent to your endpoint:
```json
{
  "event": "test",
  "webhook_id": "01hqydxwtxdj3kmzp3bz7jk001",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "message": "This is a test webhook from Genuka.",
  "timestamp": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `404` — `{ "message": "Webhook not found" }`
- `500` — `{ "message": "Error sending test webhook", "error": "..." }`

---

### `PATCH` `/2023-11/admin/webhooks/{webhook}/regenerate-secret`

Generate a new signing secret for the webhook. Resets `failure_count` to 0. The new secret is returned in the response — update your HMAC verification logic accordingly.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `webhook` | string (ULID) | Webhook ID |

**Request body:** None — no body required for this action endpoint.

```http
PATCH /2023-11/admin/webhooks/01hqydxwtxdj3kmzp3bz7jk001/regenerate-secret HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Webhook resource with new `secret` visible.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "description": "Order notifications",
  "url": "https://myapp.io/webhooks/orders",
  "events": ["order.created", "order.updated"],
  "secret": "NewXyZ123AbCdEfGhIjKlMnOpQrStUvW",
  "is_active": true,
  "last_triggered_at": "2024-03-14T09:00:00.000000Z",
  "failure_count": 0,
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "metadata": null
}
```

---

## Payment Methods

### `GET` `/2023-11/developer/payment-methods`

List payment methods for the company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 5 | Items per page |

```http
GET /2023-11/developer/payment-methods HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated list of payment method objects.
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk002",
      "processor": "stripe",
      "name": "Stripe",
      "status": 1,
      "account_id": "acct_1234ABC",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk003",
      "configurations": { "mode": "connect", "stripe_account_id": "acct_1234ABC" },
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 5, "to": 1, "total": 1 }
}
```

---

### `POST` `/2023-11/developer/payment-methods`

Create a new payment method (or update the existing one for the same processor). Also creates a linked treasury account if none exists.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `processor` | string | Yes | one of: `cash`, `notchpay`, `stripe`, `paydunya`, `taramoney`, `pawapay`, `paypal`, `mamoni`, `flutterwave`, `genuka_pay` | Payment processor |
| `configurations` | object | No | — | Processor-specific configuration key-value pairs |

```http
POST /2023-11/developer/payment-methods HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "processor": "notchpay",
  "configurations": {
    "public_key": "pub_...",
    "private_key": "pri_..."
  }
}
```

**Success response** (`201 Created`): Payment method resource.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk002",
  "processor": "notchpay",
  "name": "Notchpay",
  "status": 1,
  "account_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk003",
  "configurations": {
    "public_key": "pub_...",
    "private_key": "pri_..."
  },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `400` — `{ "message": "Invalid payment processor" }`
- `500` — `{ "message": "An error occurred while creating the payment method" }`

---

### `GET` `/2023-11/developer/payment-methods/{paymentMethod}`

Get a single payment method.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `paymentMethod` | string (ULID) | Payment method ID |

**Success response** (`200 OK`): Payment method resource.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk002",
  "processor": "stripe",
  "name": "Stripe",
  "status": 1,
  "account_id": "acct_1234ABC",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk003",
  "configurations": { "mode": "connect", "stripe_account_id": "acct_1234ABC" },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

---

### `PUT` `/2023-11/developer/payment-methods/{paymentMethod}`

Update a payment method's `account_id`, `status`, `metadata`, and `configurations`.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `paymentMethod` | string (ULID) | Payment method ID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `account_id` | string | No | — | External processor account identifier |
| `status` | boolean | No | — | Enable (`true`) or disable (`false`) |
| `metadata` | object | No | — | Arbitrary metadata |
| `configurations` | object | No | — | Processor configuration |

**Success response** (`200 OK`): Updated payment method resource.
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk002",
  "processor": "stripe",
  "name": "Stripe",
  "status": 0,
  "account_id": "acct_1234ABC",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk003",
  "configurations": { "mode": "connect", "stripe_account_id": "acct_1234ABC", "webhook_secret": "whsec_..." },
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-16T08:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/developer/payment-methods/{paymentMethod}`

Delete a payment method.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `paymentMethod` | string (ULID) | Payment method ID |

**Success response** (`204 No Content`): Empty body.

---

### `GET` `/2023-11/developer/payment-methods/stripe/connect`

Initiate Stripe Connect OAuth. Returns the Stripe OAuth authorization URL the user should be redirected to.

```http
GET /2023-11/developer/payment-methods/stripe/connect HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "url": "https://connect.stripe.com/oauth/authorize?client_id=...&state=...&response_type=code&scope=read_write&redirect_uri=..." }
```

---

### `POST` `/2023-11/developer/payment-methods/stripe/direct-keys`

Save direct Stripe integration keys (publishable key, secret key, webhook signing secret). Validates the secret key against Stripe before saving. Switches the integration mode to `direct`.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `publishable_key` | string | Yes | must start with `pk_` | Stripe publishable key |
| `secret_key` | string | Yes | — | Stripe secret key (validated live against Stripe API) |
| `webhook_secret` | string | Yes | must start with `whsec_` | Stripe webhook signing secret |

```http
POST /2023-11/developer/payment-methods/stripe/direct-keys HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "publishable_key": "pk_live_...",
  "secret_key": "sk_live_...",
  "webhook_secret": "whsec_..."
}
```

**Success response** (`200 OK`):
```json
{
  "payment_method": { "id": "01hqydxwtxdj3kmzp3bz7jk002", "processor": "stripe", "status": 1 },
  "webhook_url": "https://api.genuka.com/stripe/webhooks/01hqydxwtxdj3kmzp3bz7jk002"
}
```

**Error responses**
- `422` — `{ "message": "Invalid Stripe secret key." }`
- `422` — `{ "message": "Could not validate Stripe credentials: ..." }`

---

### `GET` `/2023-11/developer/payment-methods/stripe/webhook-url`

Get the Stripe webhook endpoint URL to register in the Stripe dashboard for direct-mode merchants.

```http
GET /2023-11/developer/payment-methods/stripe/webhook-url HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "webhook_url": "https://api.genuka.com/stripe/webhooks/01hqydxwtxdj3kmzp3bz7jk002" }
```

If no Stripe payment method is configured:
```json
{ "webhook_url": null }
```

---

## File Uploads (S3)

Genuka uses a two-step upload pattern: first obtain a presigned URL, then PUT directly to S3. For large files, use the multipart flow.

### `POST` `/2023-11/developer/upload/presign`

Generate a presigned S3 PUT URL for single-file upload.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `content_type` | string | Yes | — | MIME type of the file (e.g. `image/jpeg`) |
| `ext` | string | Yes | — | File extension without dot (e.g. `jpg`) |
| `model` | string | Yes | — | Owning model name (e.g. `App`, `Product`) |
| `collection` | string | Yes | — | Media collection name (e.g. `logo`, `screenshots`) |
| `filename` | string | No | — | Custom filename (auto-generated ULID if omitted) |

```http
POST /2023-11/developer/upload/presign HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "content_type": "image/jpeg",
  "ext": "jpg",
  "model": "App",
  "collection": "logo"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://bucket-genuka.s3.amazonaws.com/company/01hqydxwtxdj3kmzp3bz7jk73g/App/logo/01k1az40v1f44bwbs1rc459dxy.jpg?X-Amz-Signature=...",
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/App/logo/01k1az40v1f44bwbs1rc459dxy.jpg"
}
```

After upload: pass `key` in the `medias[].s3_key` field when creating or updating the model.

---

### `POST` `/2023-11/developer/upload/multipart/create`

Create a multipart upload and get presigned URLs for all parts.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `content_type` | string | Yes | — | MIME type |
| `ext` | string | Yes | — | File extension |
| `model` | string | Yes | — | Owning model |
| `collection` | string | Yes | — | Media collection |
| `part_count` | integer | Yes | min 1, max 10000 | Number of parts to upload |
| `filename` | string | No | — | Custom filename |

```http
POST /2023-11/developer/upload/multipart/create HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "content_type": "video/mp4",
  "ext": "mp4",
  "model": "App",
  "collection": "screenshots",
  "part_count": 5
}
```

**Success response** (`200 OK`):
```json
{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/App/screenshots/01k1az.mp4",
  "upload_id": "VXBsb2FkSWQ...",
  "urls": {
    "1": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=1&...",
    "2": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=2&...",
    "3": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=3&...",
    "4": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=4&...",
    "5": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=5&..."
  }
}
```

---

### `POST` `/2023-11/developer/upload/multipart/complete`

Complete a multipart upload after all parts have been PUT to S3.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | Yes | — | S3 object key (from `/multipart/create`) |
| `upload_id` | string | Yes | — | Multipart upload ID |
| `parts` | array | Yes | min 1 item | Completed part list |
| `parts[].PartNumber` | integer | Yes | min 1 | Part number (1-indexed) |
| `parts[].ETag` | string | Yes | — | ETag returned by S3 on each part PUT |

```http
POST /2023-11/developer/upload/multipart/complete HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/App/screenshots/01k1az.mp4",
  "upload_id": "VXBsb2FkSWQ...",
  "parts": [
    { "PartNumber": 1, "ETag": "\"abc123\"" },
    { "PartNumber": 2, "ETag": "\"def456\"" }
  ]
}
```

**Success response** (`200 OK`): S3 CompleteMultipartUpload result.
```json
{
  "Location": "https://bucket-genuka.s3.amazonaws.com/company/01hqydxwtxdj3kmzp3bz7jk73g/App/screenshots/01k1az.mp4",
  "Bucket": "bucket-genuka",
  "Key": "company/01hqydxwtxdj3kmzp3bz7jk73g/App/screenshots/01k1az.mp4",
  "ETag": "\"d41d8cd98f00b204e9800998ecf8427e-2\""
}
```

---

### `POST` `/2023-11/developer/upload/multipart/abort`

Abort an in-progress multipart upload.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | Yes | — | S3 object key |
| `upload_id` | string | Yes | — | Multipart upload ID |

```http
POST /2023-11/developer/upload/multipart/abort HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/App/screenshots/01k1az.mp4",
  "upload_id": "VXBsb2FkSWQ..."
}
```

**Success response** (`200 OK`):
```json
{ "message": "Multipart upload aborted." }
```

---

### `DELETE` `/2023-11/developer/upload/by-key`

Delete an S3 object by its key. Only keys under `company/{companyId}/` are permitted.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | Yes | must start with `company/{companyId}/` | S3 object key to delete |

```http
DELETE /2023-11/developer/upload/by-key HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/App/logo/01k1az.jpg" }
```

**Success response** (`200 OK`):
```json
{ "message": "Object deleted." }
```

**Error responses**
- `403` — `{ "message": "Unauthorized key prefix" }`

---

## Wallets & Withdrawals

### `GET` `/2023-11/developer/wallets`

List wallet transaction ledger entries for the developer's company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 200 | Items per page |

```http
GET /2023-11/developer/wallets HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated wallet entries.
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk004",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "last_amount": 100.00,
      "new_amount": 150.00,
      "amount": 50.00,
      "comment": "App installation revenue",
      "is_withdraw": false,
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 200, "to": 1, "total": 1 }
}
```

**Error responses**
- `422` — `{ "message": "User is not linked to any company." }`

---

### `POST` `/2023-11/developer/withdraw`

Submit a withdrawal request against the company wallet balance. Only one pending request is allowed at a time.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `amount` | number | Yes | min 1, must not exceed balance | Amount to withdraw |
| `name` | string | Yes | max 255 | Recipient name |
| `phone` | string | Yes | max 50 | Recipient phone number |
| `comment` | string | No | max 255 | Optional note |

```http
POST /2023-11/developer/withdraw HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "amount": 50.00,
  "name": "Jane Smith",
  "phone": "+12025550101",
  "comment": "Monthly payout"
}
```

**Success response** (`201 Created`):
```json
{
  "message": "Withdraw request created successfully",
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk005",
    "amount": 50.00,
    "name": "Jane Smith",
    "phone": "+12025550101",
    "comment": "Monthly payout",
    "status": "pending",
    "created_at": "2024-03-15T10:30:00.000000Z",
    "updated_at": "2024-03-15T10:30:00.000000Z"
  }
}
```

**Error responses**
- `422` — `{ "message": "Insufficient wallet balance." }`
- `422` — `{ "message": "You already have a withdraw request in progress." }`

---

### `GET` `/2023-11/developer/withdraw/withdraw-requests`

List all withdrawal requests for the developer's company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 200 | Items per page |

```http
GET /2023-11/developer/withdraw/withdraw-requests HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Paginated withdraw requests.
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk005",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "amount": 50.00,
      "name": "Jane Smith",
      "phone": "+12025550101",
      "comment": "Monthly payout",
      "status": "pending",
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 200, "to": 1, "total": 1 }
}
```

Possible `status` values: `pending`, `approved`, `rejected`.
