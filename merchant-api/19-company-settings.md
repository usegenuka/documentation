---
title: "Company, Shops, Users & Settings"
description: "This domain covers the core tenant configuration surface: reading and updating company profile data, managing shops (point-of-sale locations), users and their i"
sidebarTitle: "Company & Settings"
---

This domain covers the core tenant configuration surface: reading and updating company profile data, managing shops (point-of-sale locations), users and their invitations, role-based access control (RBAC), onboarding progress, Genuka platform subscriptions, full-text search (Meilisearch), AI token wallets, mobile-sync, and various utility endpoints (plans catalog, notification event enums, document number generation).

All admin endpoints require a valid bearer token and an `X-Company` header that scopes every query to the authenticated company. Public endpoints (`/2023-11/company`, `/2023-11/shops`, `/2023-11/plans`) are unauthenticated.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|--------|------|-------------|
| GET | `/2023-11/admin/company` | Get authenticated company (full detail) |
| PUT | `/2023-11/admin/company` | Update company profile |
| GET | `/2023-11/admin/company/app-shortcuts` | Get company app shortcuts |
| GET | `/2023-11/admin/company/document-number` | Generate next document number |
| POST | `/2023-11/admin/company/medias` | Queue a company media upload from S3 |
| PATCH | `/2023-11/admin/company/metadata` | Update company metadata only |
| GET | `/2023-11/admin/company/onboarding` | Get company onboarding record |
| GET | `/2023-11/admin/company/variables` | Get company template variables |
| GET | `/2023-11/admin/company/wallets` | List company AI token wallet transactions |
| GET | `/2023-11/admin/company/website` | Get company main domain |
| GET | `/2023-11/company` | Public: get company by `X-Company` header |
| GET | `/2023-11/company/filters` | Public: get company storefront filters |
| GET | `/2023-11/company/from-domain` | Public: resolve company by custom domain |
| GET | `/2023-11/admin/settings` | Get company settings key-value map |
| PUT | `/2023-11/admin/settings` | Update company settings |
| GET | `/2023-11/admin/variables` | Get company template variables (alias) |
| PUT | `/2023-11/admin/variables` | Update company template variables |
| GET | `/2023-11/admin/shops` | List shops (admin, paginated) |
| POST | `/2023-11/admin/shops` | Create a shop |
| GET | `/2023-11/admin/shops/{shop}` | Get shop detail |
| PUT | `/2023-11/admin/shops/{shop}` | Update a shop |
| DELETE | `/2023-11/admin/shops/{shop}` | Delete a shop |
| GET | `/2023-11/shops` | Public: list shops |
| GET | `/2023-11/shops/count` | Public: total shop count |
| GET | `/2023-11/shops/handle/{shop}` | Public: get shop by slug |
| GET | `/2023-11/shops/{shop}` | Public: get shop by ID |
| GET | `/2023-11/admin/roles` | List company roles |
| POST | `/2023-11/admin/roles` | Create a custom role |
| GET | `/2023-11/admin/roles/permissions/catalog` | Get all available permissions grouped by domain |
| PUT | `/2023-11/admin/roles/assign/{user}` | Assign a role to a user |
| GET | `/2023-11/admin/roles/{role}` | Get role detail |
| PUT | `/2023-11/admin/roles/{role}` | Update a role |
| DELETE | `/2023-11/admin/roles/{role}` | Delete a role |
| GET | `/2023-11/admin/onboarding` | Get onboarding entity counts |
| POST | `/2023-11/admin/onboarding/start` | Start or retrieve company onboarding |
| PUT | `/2023-11/admin/onboarding/{onboarding}` | Update onboarding step/metadata |
| GET | `/2023-11/admin/subscriptions` | List legacy subscriptions |
| GET | `/2023-11/admin/genuka/subscriptions` | List Genuka platform subscriptions |
| POST | `/2023-11/admin/genuka/subscriptions` | Create a new Genuka subscription |
| POST | `/2023-11/admin/genuka/subscriptions/renew` | Renew the active Genuka subscription |
| GET | `/2023-11/admin/genuka/subscriptions/renewal-reminder` | Get renewal reminder state |
| GET | `/2023-11/admin/genuka/subscriptions/{subscription}` | Get a Genuka subscription |
| PUT | `/2023-11/admin/genuka/subscriptions/{subscription}` | Update a Genuka subscription |
| DELETE | `/2023-11/admin/genuka/subscriptions/{subscription}` | Delete a Genuka subscription |
| GET | `/2023-11/plans` | Public: list all plans |
| GET | `/2023-11/plans/{plan}` | Public: get plan by lookup key |
| GET | `/2023-11/admin/search` | Get Meilisearch tenant token |
| POST | `/2023-11/admin/search` | Server-side full-text search |
| GET | `/2023-11/enums/notification-events` | List notification event enum values |
| GET | `/2023-11/admin/sync/pull` | Pull entity changes to mobile |
| POST | `/2023-11/admin/sync/push` | Push local operations from mobile |
| GET | `/2023-11/admin/users` | List users |
| POST | `/2023-11/admin/users` | Create / invite a user |
| GET | `/2023-11/admin/users/invitations/pending` | List pending invitations |
| DELETE | `/2023-11/admin/users/invitations/{user}` | Cancel a pending invitation |
| POST | `/2023-11/admin/users/invitations/{user}/resend` | Resend an invitation |
| PUT | `/2023-11/admin/users/metadata` | Update current user's metadata |
| GET | `/2023-11/admin/users/stats` | Get dashboard stats for authenticated user |
| GET | `/2023-11/admin/users/{user}` | Get user detail |
| PUT | `/2023-11/admin/users/{user}` | Update a user |
| DELETE | `/2023-11/admin/users/{user}` | Delete / detach a user |

---

## Company

### `GET` `/2023-11/admin/company`

Returns the full company profile for the authenticated user's active company.

```http
GET /2023-11/admin/company HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Acme Store",
  "description": "Premium goods shop",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "logoUrl": "https://cdn.genuka.com/logo.jpg",
  "handle": "acme-store",
  "company_code": "ACM-001",
  "active_template": "default",
  "metadata": { "rccm": "RC/DLA/2020/B/1234", "fiscalID": "P123456789" },
  "type": null,
  "address": {
    "id": "01hrx...",
    "line1": "12 Rue de la Paix",
    "city": "Douala",
    "country": "CM",
    "phone": "+237600000000"
  },
  "subscription": {
    "id": "01hrx...",
    "plan_lookup_key": "pro-monthly",
    "status": "active",
    "starts_at": "2024-01-01T00:00:00.000000Z",
    "ends_at": "2024-02-01T00:00:00.000000Z",
    "plan": { "id": "01hrx...", "name": "Pro", "price": 500000, "currency_code": "XAF" }
  },
  "plan": { "name": "Pro", "price": 500000 },
  "medias": [],
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `401` — Missing or invalid bearer token.
- `403` — User does not have access to this company.

---

### `PUT` `/2023-11/admin/company`

Update company profile fields. All fields are optional (partial update).

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `name` | string | No | — | Company display name |
| `currency_code` | string | No | — | ISO 4217 currency code (e.g. `XAF`) |
| `currency_name` | string | No | — | Currency display name (e.g. `FCFA`) |
| `description` | string | No | — | Company description |
| `metadata` | object | No | — | Arbitrary key-value metadata |
| `logoS3Key` | string | No | — | S3 key of a pre-uploaded logo image. Triggers async media processing. |
| `address` | object | No | — | Address object: `line1`, `city`, `country`, `phone`, `zip` |

```http
PUT /2023-11/admin/company HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Acme Store Updated",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "description": "Premium goods shop - updated",
  "logoS3Key": "logos/01hqydxwtxdj3kmzp3bz7jk73g/logo.jpg",
  "address": {
    "line1": "15 Avenue de la Liberté",
    "city": "Yaoundé",
    "country": "CM",
    "phone": "+237699000000"
  }
}
```

**Success response** (`200 OK`): Returns the raw company model (direct Eloquent serialization — not wrapped in `FullCompanyResource`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Acme Store Updated",
  "description": "Premium goods shop - updated",
  "handle": "acme-store",
  "company_code": "ACM-001",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "logoUrl": "https://cdn.genuka.com/logos/01hqydxwtxdj3kmzp3bz7jk73g/logo.jpg",
  "active_template": "default",
  "metadata": { "rccm": "RC/DLA/2020/B/1234", "fiscalID": "P123456789" },
  "type": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Validation errors.

---

### `GET` `/2023-11/admin/company/app-shortcuts`

Returns the array of app shortcuts stored on the company's metadata.

```http
GET /2023-11/admin/company/app-shortcuts HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  { "label": "POS", "url": "/pos", "icon": "shopping-cart" },
  { "label": "Inventory", "url": "/stocks", "icon": "archive" }
]
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/company/document-number`

Generate the next sequential document number for a given document type.

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `type` | string | Yes | Document type enum value: `ORDER`, `INV`, `PROD`, `QUOTE`, `SO`, `PO`, `BILL`, `PAYMENT`, `DLV`, `RET`, `CN` |

```http
GET /2023-11/admin/company/document-number?type=INV HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
"INV-2024-00042"
```

**Error responses**
- `401` — Unauthenticated.
- `422` — `type` is missing or not a valid `DocumentType` enum value.

---

### `POST` `/2023-11/admin/company/medias`

Queue an asynchronous job to import a media file from S3 into the company's media library.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `key` | string | Yes | — | S3 object key of the file to import |
| `collectionName` | string | Yes | — | Spatie media collection name (e.g. `logo`, `banner`) |

```http
POST /2023-11/admin/company/medias HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "key": "uploads/01hqydxwtxdj3kmzp3bz7jk73g/banner.jpg",
  "collectionName": "banner"
}
```

**Success response** (`202 Accepted`):
```json
{
  "status": "queued",
  "message": "Media processing has started."
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — `key` or `collectionName` missing.
- `500` — S3 or queue error.

---

### `PATCH` `/2023-11/admin/company/metadata`

Update only the `metadata` field of the company.

**Request body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `metadata` | object | No | Arbitrary metadata key-value pairs to persist |

```http
PATCH /2023-11/admin/company/metadata HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "metadata": {
    "rccm": "RC/DLA/2020/B/9999",
    "fiscalID": "P987654321",
    "contact": { "email": "info@acme.cm", "phone": "+237699000001" }
  }
}
```

**Success response** (`200 OK`): Returns the raw company model (direct Eloquent serialization):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Acme Store",
  "description": "Premium goods shop",
  "handle": "acme-store",
  "company_code": "ACM-001",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "logoUrl": "https://cdn.genuka.com/logo.jpg",
  "active_template": "default",
  "metadata": {
    "rccm": "RC/DLA/2020/B/9999",
    "fiscalID": "P987654321",
    "contact": { "email": "info@acme.cm", "phone": "+237699000001" }
  },
  "type": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T11:05:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/company/onboarding`

Returns the company's onboarding record.

```http
GET /2023-11/admin/company/onboarding HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrx...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "step": "products",
  "metadata": { "source": "web", "industry": "retail" },
  "source": "web",
  "industry": "retail",
  "finished_at": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-02T00:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/company/variables`

Returns the template variables stored in the company's metadata.

```http
GET /2023-11/admin/company/variables HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "order": { "prefix": "ORD", "suffix": "", "padding": 5 },
  "invoice": { "prefix": "INV", "footer_note": "Thank you for your business." },
  "revenue": { "include_shipping": true, "include_taxes": true }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/company/wallets`

Returns paginated AI token wallet transactions for the company.

**Query parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `per_page` | integer | No | 200 | Items per page |
| `page` | integer | No | 1 | Page number |

```http
GET /2023-11/admin/company/wallets HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrx...",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "amount": 500.0,
      "last_amount": 1000.0,
      "new_amount": 1500.0,
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
- `401` — Unauthenticated.
- `422` — User is not linked to a company.

---

### `GET` `/2023-11/admin/company/website`

Returns the company's main domain record.

```http
GET /2023-11/admin/company/website HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrx...",
  "domain": "acme.genuka.shop",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "is_primary": true,
  "created_at": "2024-01-01T00:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.

---

## Public Company Endpoints

### `GET` `/2023-11/company`

Public endpoint. Returns the company identified by the `X-Company` header (or `companyId` query param) with storefront data.

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `companyId` | string | No | ULID of the company (falls back to `X-Company` header) |

```http
GET /2023-11/company HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Acme Store",
  "description": "Premium goods shop",
  "handle": "acme-store",
  "company_code": "ACM-001",
  "logoUrl": null,
  "logo": null,
  "storefront": { "theme": "default", "primary_color": "#000000" },
  "storeFrontFilters": { "categories": [], "brands": [] },
  "paymentMethods": [],
  "variables": { "order": { "prefix": "ORD" } },
  "currency": { "code": "XAF", "name": "FCFA" },
  "currencies": [{ "code": "XAF", "label": "FCFA", "value": 1 }],
  "website": "acme.genuka.shop",
  "hasBlogs": false,
  "contact": { "email": "info@acme.cm" },
  "address": { "line1": "12 Rue de la Paix", "city": "Douala", "country": "CM" },
  "business_id": "RC/DLA/2020/B/1234",
  "tax_id": "P123456789"
}
```

**Error responses**
- `404` — Company not found (both `companyId` and `domain` absent or not matching).

---

### `GET` `/2023-11/company/filters`

Public endpoint. Returns the storefront filter configuration for the company.

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `companyId` | string | Yes | ULID of the company |

```http
GET /2023-11/company/filters?companyId=01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "categories": ["Electronics", "Clothing"],
  "brands": ["Apple", "Samsung"],
  "price_ranges": [{ "min": 0, "max": 10000 }]
}
```

**Error responses**
- `404` — Company not found.
- `500` — Unexpected server error.

---

### `GET` `/2023-11/company/from-domain`

Public endpoint. Resolves a company by a custom domain name (cached for 4 weeks).

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `domain` | string | Yes | Custom domain (e.g. `shop.acme.cm` or `acme.genuka.shop`) |

```http
GET /2023-11/company/from-domain?domain=chicandcheaprahiempire.genuka.shop HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`): Same shape as `GET /2023-11/company` — the `PublicCompanyResource` transformation is applied:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Chic & Cheap Empire",
  "description": "Fashion store",
  "handle": "chicandcheaprahiempire",
  "company_code": "CCE-001",
  "logoUrl": "https://cdn.genuka.com/logo.jpg",
  "logo": null,
  "storefront": { "theme": "default", "primary_color": "#ff5733" },
  "storeFrontFilters": { "categories": ["Women", "Men"], "brands": [] },
  "paymentMethods": [
    { "id": "01hrx...", "name": "MTN Mobile Money", "processor": "pawapay", "status": true }
  ],
  "variables": { "order": { "prefix": "ORD", "padding": 5 } },
  "currency": { "code": "XAF", "name": "FCFA" },
  "currencies": [{ "code": "XAF", "label": "FCFA", "value": 1 }],
  "website": "chicandcheaprahiempire.genuka.shop",
  "hasBlogs": false,
  "contact": { "email": "contact@chicandcheap.cm", "phone": "+237699000000" },
  "address": { "line1": "Avenue Kennedy", "city": "Yaoundé", "country": "CM" },
  "business_id": "RC/YDE/2021/B/5678",
  "tax_id": "P111222333"
}
```

**Error responses**
- `404` — Domain not registered to any company.

---

## Settings

### `GET` `/2023-11/admin/settings`

Returns the company's settings as a flat key-value map. Users with `company.*` permission receive all settings; users with `treasury_accounts.*` permission receive only `cash_control.*` settings.

```http
GET /2023-11/admin/settings HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "revenue.include_shipping": true,
  "revenue.include_taxes": true,
  "revenue.include_fees": false,
  "revenue.include_tips": false,
  "cash_control.require_opening_balance": true,
  "notifications.low_stock_threshold": 5
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — User lacks both `company.*` and `treasury_accounts.*` permissions.

---

### `PUT` `/2023-11/admin/settings`

Update one or more company settings. Requires `company.*` permission for non–cash-control settings; `treasury_accounts.*` is sufficient for `cash_control.*` settings only.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `settings` | object | Yes | `array` | Key-value map of settings to update. Values may be any scalar or null. |

```http
PUT /2023-11/admin/settings HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "settings": {
    "revenue.include_shipping": true,
    "revenue.include_taxes": false,
    "cash_control.require_opening_balance": false
  }
}
```

**Success response** (`200 OK`): Returns the updated settings map (filtered by the caller's permission level):
```json
{
  "revenue.include_shipping": true,
  "revenue.include_taxes": false,
  "revenue.include_fees": false,
  "revenue.include_tips": false,
  "cash_control.require_opening_balance": false,
  "notifications.low_stock_threshold": 5
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — User lacks required permissions, or tried to update non–cash-control keys without `company.*`.
- `422` — `settings` field missing or not an array.

---

## Variables

### `GET` `/2023-11/admin/variables`

Alias for `GET /2023-11/admin/company/variables`. Returns the company template variables.

```http
GET /2023-11/admin/variables HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "order": { "prefix": "ORD", "suffix": "", "padding": 5 },
  "invoice": { "prefix": "INV", "footer_note": "Thank you for your business." },
  "revenue": {
    "include_shipping": true,
    "include_taxes": true,
    "include_fees": false,
    "include_tips": false
  }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `PUT` `/2023-11/admin/variables`

Replace the company's template variables. Also syncs custom fields from the variable definitions and mirrors revenue settings to the `company_settings` table.

**Request body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `variables` | object | No | Full variables map to persist |

```http
PUT /2023-11/admin/variables HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "variables": {
    "order": { "prefix": "ORD", "padding": 5 },
    "invoice": { "prefix": "INV", "footer_note": "Thank you!" },
    "revenue": {
      "include_shipping": true,
      "include_taxes": true,
      "include_fees": false,
      "include_tips": false
    }
  }
}
```

**Success response** (`200 OK`): Returns the updated variables object (the `variables` key stored in `company.metadata`):
```json
{
  "order": { "prefix": "ORD", "padding": 5 },
  "invoice": { "prefix": "INV", "footer_note": "Thank you!" },
  "revenue": {
    "include_shipping": true,
    "include_taxes": true,
    "include_fees": false,
    "include_tips": false
  }
}
```

**Error responses**
- `401` — Unauthenticated.

---

## Shops

### `GET` `/2023-11/admin/shops`

List shops belonging to the authenticated company. Non-admin users only see shops they are assigned to.

**Query parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `per_page` | integer | No | 15 | Items per page |
| `page` | integer | No | 1 | Page number |
| `include` | string | No | — | Comma-separated relations: `orders`, `customers`, `products` |
| `filter[updated_at][start]` | string | No | — | Filter by updated_at start date (ISO 8601) |
| `filter[updated_at][end]` | string | No | — | Filter by updated_at end date (ISO 8601) |
| `sort` | string | No | — | Sort field; supports `total_orders`, `total_bills` (with `startDate`/`endDate` params) |
| `startDate` | string | No | Start of current month | Start date for aggregate sort calculations |
| `endDate` | string | No | End of current month | End date for aggregate sort calculations |

```http
GET /2023-11/admin/shops?per_page=10&include=orders HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01k1amypzhgs7fngfx1r9d37pn",
      "name": "Downtown Store",
      "slug": "downtown-store",
      "description": "Our main Douala location",
      "currency_code": "XAF",
      "currency_name": "FCFA",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "address": { "line1": "5 Rue de la Joie", "city": "Douala", "country": "CM" },
      "warehouses": [],
      "domains": [],
      "logoUrl": "",
      "logo": null,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "deleted_at": null
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/shops`

Create a new shop.

**Request body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Shop display name |
| `description` | string | No | Shop description |
| `currency_code` | string | No | ISO 4217 currency code |
| `currency_name` | string | No | Currency display name |
| `company_id` | string | No | Company ULID (defaults to authenticated company) |
| `address` | object | No | Address: `line1` (required), `city` (required), `country` (required), `phone` |
| `warehouses` | array | No | Array of `{ "id": "<warehouseId>" }` objects to link |
| `uploadImageBase64` | string | No | Base64-encoded logo image |
| `metadata` | object | No | Arbitrary metadata |

```http
POST /2023-11/admin/shops HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Yaoundé Branch",
  "description": "Our Yaoundé location",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address": {
    "line1": "10 Avenue Kennedy",
    "city": "Yaoundé",
    "country": "CM",
    "phone": "+237699000002"
  }
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01k6t493wqcwr1g9xc0hnfchgf",
  "name": "Yaoundé Branch",
  "slug": "yaounde-branch",
  "description": "Our Yaoundé location",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address": {
    "id": "01hrx...",
    "line1": "10 Avenue Kennedy",
    "city": "Yaoundé",
    "country": "CM",
    "phone": "+237699000002"
  },
  "warehouses": [],
  "domains": [],
  "logoUrl": "",
  "logo": null,
  "metadata": null,
  "created_at": "2024-03-15T11:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `401` — Unauthenticated.
- `500` — Database transaction failure.

---

### `GET` `/2023-11/admin/shops/{shop}`

Get details for a specific shop. The authenticated user must have the shop assigned.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `shop` | string | Shop ULID |

```http
GET /2023-11/admin/shops/01k1amypzhgs7fngfx1r9d37pn HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01k1amypzhgs7fngfx1r9d37pn",
  "name": "Downtown Store",
  "slug": "downtown-store",
  "description": "Our main Douala location",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address": {
    "id": "01hrx...",
    "line1": "5 Rue de la Joie",
    "city": "Douala",
    "country": "CM",
    "phone": "+237699000001"
  },
  "warehouses": [
    { "id": "01hrx...", "name": "Main Warehouse", "company_id": "01hqydxwtxdj3kmzp3bz7jk73g" }
  ],
  "domains": [],
  "logoUrl": "https://cdn.genuka.com/shops/downtown-logo.jpg",
  "logo": null,
  "metadata": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — User is not assigned to this shop.
- `404` — Shop not found.

---

### `PUT` `/2023-11/admin/shops/{shop}`

Update a shop. All fields are optional (partial update). The authenticated user must be assigned to the shop.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `shop` | string | Shop ULID |

**Request body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | No | Shop display name |
| `description` | string | No | Shop description |
| `currency_code` | string | No | ISO 4217 currency code |
| `currency_name` | string | No | Currency display name |
| `company_id` | string | No | Company ULID (defaults to authenticated company) |
| `address` | object | No | Address: `line1`, `city`, `country`, `phone` |
| `warehouses` | array | No | Array of `{ "id": "<warehouseId>" }` objects to link |
| `uploadImageBase64` | string | No | Base64-encoded logo image |
| `metadata` | object | No | Arbitrary metadata |

```http
PUT /2023-11/admin/shops/01k1amypzhgs7fngfx1r9d37pn HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Yaoundé Branch - Updated"
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01k1amypzhgs7fngfx1r9d37pn",
  "name": "Yaoundé Branch - Updated",
  "slug": "yaounde-branch",
  "description": "Our Yaoundé location",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address": {
    "id": "01hrx...",
    "line1": "10 Avenue Kennedy",
    "city": "Yaoundé",
    "country": "CM",
    "phone": "+237699000002"
  },
  "warehouses": [],
  "domains": [],
  "logoUrl": "",
  "logo": null,
  "metadata": null,
  "created_at": "2024-03-15T11:00:00.000000Z",
  "updated_at": "2024-03-15T11:30:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — User not assigned to this shop.
- `404` — Shop not found.
- `500` — Database transaction failure.

---

### `DELETE` `/2023-11/admin/shops/{shop}`

Permanently delete a shop (force-delete, no soft-delete).

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `shop` | string | Shop ULID |

```http
DELETE /2023-11/admin/shops/01k1amypzhgs7fngfx1r9d37pn HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`204 No Content`):
```json
{ "message": "Deleted with success" }
```

**Error responses**
- `401` — Unauthenticated.
- `403` — User not assigned to this shop.
- `404` — Shop not found.
- `500` — Database transaction failure.

---

## Public Shop Endpoints

### `GET` `/2023-11/shops`

Public endpoint. List shops (scoped to the current company via `X-Company` or `companyId`).

**Query parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `filter[search]` | string | No | — | Full-text search on name and description |
| `sort` | string | No | `name` | Sort field: `name`, `description`, `currency_name`, `currency_code`, `created_at`, `updated_at` |
| `per_page` | integer | No | 15 | Items per page |
| `page` | integer | No | 1 | Page number |

```http
GET /2023-11/shops?filter[search]=downtown HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01k1amypzhgs7fngfx1r9d37pn",
      "name": "Downtown Store",
      "slug": "downtown-store",
      "description": "Our main Douala location",
      "currency_code": "XAF",
      "currency_name": "FCFA",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "address": { "line1": "5 Rue de la Joie", "city": "Douala", "country": "CM" },
      "warehouses": [],
      "domains": [],
      "logoUrl": "",
      "logo": null,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "deleted_at": null
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

---

### `GET` `/2023-11/shops/count`

Public endpoint. Returns the total number of shops across all companies.

```http
GET /2023-11/shops/count HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "count": 42 }
```

---

### `GET` `/2023-11/shops/handle/{shop}`

Public endpoint. Get a shop by its slug. If `companyId` query param is provided, scopes to that company.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `shop` | string | Shop slug |

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `companyId` | string | No | Scope lookup to a specific company |

```http
GET /2023-11/shops/handle/downtown-store?companyId=01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01k1amypzhgs7fngfx1r9d37pn",
  "name": "Downtown Store",
  "slug": "downtown-store",
  "description": "Our main Douala location",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address": { "line1": "5 Rue de la Joie", "city": "Douala", "country": "CM" },
  "warehouses": [],
  "domains": [],
  "logoUrl": "",
  "logo": null,
  "metadata": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `404` — Shop not found for this company.

---

### `GET` `/2023-11/shops/{shop}`

Public endpoint. Get a shop by its ULID.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `shop` | string | Shop ULID |

```http
GET /2023-11/shops/01k6t493wqcwr1g9xc0hnfchgf HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01k6t493wqcwr1g9xc0hnfchgf",
  "name": "Yaoundé Branch",
  "slug": "yaounde-branch",
  "description": "Our Yaoundé location",
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address": { "line1": "10 Avenue Kennedy", "city": "Yaoundé", "country": "CM" },
  "warehouses": [],
  "domains": [],
  "logoUrl": "",
  "logo": null,
  "metadata": null,
  "created_at": "2024-03-15T11:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `404` — Shop not found.

---

## Roles

### `GET` `/2023-11/admin/roles`

List all roles for the current company (excluding the `superadmin` role). Includes permission count and user count.

**Query parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `per_page` | integer | No | 50 | Items per page |
| `page` | integer | No | 1 | Page number |
| `filter[is_system]` | boolean | No | — | Filter by system role flag |
| `filter[search]` | string | No | — | Search on name, display_name, description |
| `sort` | string | No | `is_system desc, display_name` | Sort: `name`, `display_name`, `created_at`, `updated_at` |

```http
GET /2023-11/admin/roles?per_page=20 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrx...",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "cashier",
      "display_name": "Cashier",
      "description": "Can process sales and view inventory",
      "is_system": false,
      "metadata": null,
      "permissions": ["orders.view", "orders.create", "products.view"],
      "users_count": 3,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 50, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/roles`

Create a custom role for the company. Non-admin actors cannot assign permissions beyond their own.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `name` | string | Yes | `max:64`, `regex:/^[a-z0-9_\-]+$/`, unique per company | Machine-readable role identifier |
| `display_name` | string | Yes | `max:120` | Human-readable role name |
| `description` | string | No | `max:2000` | Role description |
| `permissions` | array | Yes | Values must be valid `Permission` enum values or `*` | List of permission strings |

```http
POST /2023-11/admin/roles HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "cashier",
  "display_name": "Cashier",
  "description": "Can process sales and view products",
  "permissions": ["orders.view", "orders.create", "products.view"]
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01k8zz1abcde2fg3hi4jk5lm6n",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "cashier",
  "display_name": "Cashier",
  "description": "Can process sales and view products",
  "is_system": false,
  "metadata": null,
  "permissions": ["orders.view", "orders.create", "products.view"],
  "users_count": 0,
  "created_at": "2024-03-15T11:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Validation failure (name taken, invalid permissions, reserved name).

---

### `GET` `/2023-11/admin/roles/permissions/catalog`

Returns all available permission values grouped by domain.

```http
GET /2023-11/admin/roles/permissions/catalog HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "domain": "orders",
      "label": "Orders",
      "permissions": [
        { "value": "orders.*", "label": "All order permissions", "is_wildcard": true },
        { "value": "orders.view", "label": "View orders", "is_wildcard": false },
        { "value": "orders.create", "label": "Create orders", "is_wildcard": false }
      ]
    },
    {
      "domain": "products",
      "label": "Products",
      "permissions": [
        { "value": "products.*", "label": "All product permissions", "is_wildcard": true },
        { "value": "products.view", "label": "View products", "is_wildcard": false }
      ]
    }
  ]
}
```

---

### `PUT` `/2023-11/admin/roles/assign/{user}`

Assign a role to a user within the current company. Non-admin actors cannot assign a role that grants permissions they do not themselves hold.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `user` | string | User ULID |

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `role_id` | string | Yes | Must exist in `company_roles` for this company | ULID of the role to assign |

```http
PUT /2023-11/admin/roles/assign/01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "role_id": "01hrx..."
}
```

**Success response** (`200 OK`):
```json
{
  "message": "Role assigned.",
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "role_id": "01hrx...",
  "role_name": "cashier"
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Actor lacks permission to assign this role (privilege escalation prevention).
- `404` — Role or user not found / user does not belong to this company.
- `422` — `role_id` missing or invalid.

---

### `GET` `/2023-11/admin/roles/{role}`

Get a single role with its permissions and user count.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `role` | string | Role ULID |

```http
GET /2023-11/admin/roles/01k8zz1abcde2fg3hi4jk5lm6n HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01k8zz1abcde2fg3hi4jk5lm6n",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "cashier",
  "display_name": "Cashier",
  "description": "Can process sales and view products",
  "is_system": false,
  "metadata": null,
  "permissions": ["orders.view", "orders.create", "products.view"],
  "users_count": 3,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Role not found or belongs to a different company.

---

### `PUT` `/2023-11/admin/roles/{role}`

Update a role. System roles (`is_system = true`) cannot have their `name` changed.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `role` | string | Role ULID |

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `name` | string | No | `max:64`, `regex:/^[a-z0-9_\-]+$/`, unique per company | New machine-readable name |
| `display_name` | string | No | `max:120` | New display name |
| `description` | string | No | `max:2000` | New description |
| `permissions` | array | No | Valid `Permission` enum values | Full replacement permission list |

```http
PUT /2023-11/admin/roles/01k8zz1abcde2fg3hi4jk5lm6n HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "display_name": "Senior Cashier",
  "permissions": ["orders.view", "orders.create", "orders.update", "products.view"]
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01k8zz1abcde2fg3hi4jk5lm6n",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "cashier",
  "display_name": "Senior Cashier",
  "description": "Can process sales and view products",
  "is_system": false,
  "metadata": null,
  "permissions": ["orders.view", "orders.create", "orders.update", "products.view"],
  "users_count": 3,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T12:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Role not found.
- `422` — Attempting to rename a system role, or invalid permissions.

---

### `DELETE` `/2023-11/admin/roles/{role}`

Delete a custom role. Blocked if `is_system = true` or if the role is assigned to any users.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `role` | string | Role ULID |

```http
DELETE /2023-11/admin/roles/01k8zz1abcde2fg3hi4jk5lm6n HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Role deleted." }
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Role not found.
- `422` — Role is a system role or still assigned to users.

---

## Onboarding

### `GET` `/2023-11/admin/onboarding`

Returns entity counts for all onboarding tasks (products, customers, orders, users, and many more).

```http
GET /2023-11/admin/onboarding HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "products": 15,
  "customers": 42,
  "orders": 130,
  "users": 3,
  "invoices": 87,
  "bills": 10,
  "suppliers": 5,
  "taxes": 2,
  "shipping_fees": 3,
  "pickup_locations": 1,
  "warehouses": 2,
  "stock_movements": 500,
  "services": 4,
  "collections": 8,
  "discounts": 2,
  "webhooks": 1,
  "apps_installed": 0,
  "returns": 3,
  "quotations": 7,
  "delivery_notes": 12,
  "courier_assignments": 5,
  "promotions": 1,
  "inventory_alerts": 0,
  "users_with_pin_code": 2,
  "products_with_barcodes": 6,
  "themes_configured": 1,
  "whatsapp_setup": 1,
  "bundle_products": 0,
  "digital_products": 0,
  "stocks": 30,
  "bill_of_materials": 0,
  "production_orders": 0,
  "payment_methods_configured": 2
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/onboarding/start`

Start a new onboarding flow for the company. If one already exists it is returned unchanged.

**Request body**: None. This endpoint requires no request body.

```http
POST /2023-11/admin/onboarding/start HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrx...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "step": "source",
  "metadata": {},
  "finished_at": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `PUT` `/2023-11/admin/onboarding/{onboarding}`

Advance or update onboarding progress. All body fields except `step` are stored in `metadata`.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `onboarding` | string | Onboarding record ULID |

**Request body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `step` | string | No | Current step name. Use `completed` to mark as finished. |
| `*` | any | No | Any additional key-value pairs are merged into `metadata` |

```http
PUT /2023-11/admin/onboarding/01hrx... HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "step": "products",
  "source": "facebook_ads",
  "industry": "fashion"
}
```

**Success response** (`200 OK`): Returns the `OnboardingResource` — metadata keys are extracted to the top level:
```json
{
  "id": "01hrx...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "step": "products",
  "metadata": { "source": "facebook_ads", "industry": "fashion" },
  "source": "facebook_ads",
  "industry": "fashion",
  "finished_at": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-03T09:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Onboarding record not found.

---

## Subscriptions

### `GET` `/2023-11/admin/subscriptions`

List legacy subscriptions for the company. Supports filtering and pagination.

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `filter[id]` | string | No | Exact match on ID |
| `filter[status]` | string | No | Exact match on status |
| `filter[frequency]` | string | No | Exact match on frequency |
| `per_page` | integer | No | Items per page (default 15) |
| `sort` | string | No | `created_at`, `updated_at` |

```http
GET /2023-11/admin/subscriptions?filter[status]=active HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrx...",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "customer_id": null,
      "status": "active",
      "frequency": "monthly",
      "interval": 1,
      "start_date": "2024-01-01T00:00:00.000000Z",
      "end_date": "2024-12-31T00:00:00.000000Z",
      "next_billing_date": "2024-04-01T00:00:00.000000Z",
      "trial_ends_at": null,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-03-01T00:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

## Genuka Subscriptions

### `GET` `/2023-11/admin/genuka/subscriptions`

List Genuka platform subscriptions for the company.

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `filter[status]` | string | No | Exact match: `pending`, `active`, `terminated`, `expired` |
| `filter[search]` | string | No | Search on `plan_lookup_key` |
| `filter[created_at][start]` | string | No | Date range start |
| `filter[created_at][end]` | string | No | Date range end |
| `sort` | string | No | `created_at`, `updated_at`, `plan_lookup_key`, `status` |
| `per_page` | integer | No | Default 15 |

```http
GET /2023-11/admin/genuka/subscriptions?filter[status]=active HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrx...",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "plan_lookup_key": "pro-monthly",
      "status": "active",
      "total": 500000,
      "currency_code": "XAF",
      "payment_method": "mobile_money",
      "starts_at": "2024-03-01T00:00:00.000000Z",
      "ends_at": "2024-04-01T00:00:00.000000Z",
      "trial_ends_at": null,
      "metadata": {},
      "plan": { "id": "01hrx...", "name": "Pro", "lookup_key": "pro-monthly", "price": 500000, "duration_in_days": 30 },
      "created_at": "2024-03-01T00:00:00.000000Z",
      "updated_at": "2024-03-01T00:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/genuka/subscriptions`

Create a new Genuka subscription. Handled by `NewSubscriptionProcess`.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `plan_lookup_key` | string | Yes | Must exist in `plans.lookup_key` | The plan to subscribe to |
| `payment_method` | string | Yes | `mobile_money` or `card` | Payment channel |
| `phone` | string | Conditional | Required when `payment_method = mobile_money` | Mobile money phone number |
| `correspondent` | string | No | — | Mobile money operator (e.g. `MTN`, `ORANGE`) |
| `currency_code` | string | No | 3 chars | Currency code for the payment |
| `callback` | string | No | Valid URL | Redirect URL after payment (card flow) |

```http
POST /2023-11/admin/genuka/subscriptions HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "plan_lookup_key": "pro-monthly",
  "payment_method": "mobile_money",
  "phone": "+237699000000",
  "currency_code": "XAF"
}
```

**Success response** (`200 OK`): Returns the new `GenukaSubscriptionResource`:
```json
{
  "id": "01k9aabbccdd1ee2ff3gg4hh5i",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "plan_lookup_key": "pro-monthly",
  "status": "pending",
  "total": 500000,
  "currency_code": "XAF",
  "payment_method": "mobile_money",
  "starts_at": null,
  "ends_at": null,
  "trial_ends_at": null,
  "metadata": {},
  "plan": { "id": "01hrx...", "name": "Pro", "lookup_key": "pro-monthly", "price": 500000, "duration_in_days": 30 },
  "created_at": "2024-03-15T12:00:00.000000Z",
  "updated_at": "2024-03-15T12:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Invalid plan or payment data.

---

### `POST` `/2023-11/admin/genuka/subscriptions/renew`

Renew the active Genuka subscription via mobile money or card. Only company admins may call this.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `payment_method` | string | Yes | `mobile_money` or `card` | Payment channel |
| `phone` | string | Conditional | Required when `payment_method = mobile_money`; min 6 chars | Mobile money phone number |
| `correspondent` | string | No | — | Mobile money operator (e.g. `MTN`, `ORANGE`) |
| `plan_lookup_key` | string | No | Must exist in `plans.lookup_key` | Override plan; defaults to current plan |
| `currency_code` | string | No | 3 chars | Currency for the payment |
| `callback` | string | No | Valid URL | Redirect URL after payment (card flow) |

```http
POST /2023-11/admin/genuka/subscriptions/renew HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "payment_method": "mobile_money",
  "phone": "+237699000000",
  "correspondent": "MTN",
  "plan_lookup_key": "pro-monthly",
  "currency_code": "XAF"
}
```

**Success response** (`200 OK`):
```json
{
  "subscription_id": "01hrx...",
  "status": "pending",
  "payment_url": null,
  "message": "Payment initiated. Awaiting confirmation."
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — User is not a company admin.
- `422` — No associated company, or payment/Stripe error.

---

### `GET` `/2023-11/admin/genuka/subscriptions/renewal-reminder`

Returns whether the company's subscription needs renewal and the reminder level to display.

```http
GET /2023-11/admin/genuka/subscriptions/renewal-reminder HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "needs_renewal": true,
  "in_grace_period": false,
  "subscription": {
    "id": "01hrx...",
    "plan_lookup_key": "pro-monthly",
    "plan_name": "Pro",
    "amount": 500000.0,
    "currency_code": "XAF",
    "ends_at": "2024-04-01T00:00:00.000000Z",
    "starts_at": "2024-03-01T00:00:00.000000Z"
  },
  "days_remaining": 5,
  "reminder_level": "warning"
}
```

---

### `GET` `/2023-11/admin/genuka/subscriptions/{subscription}`

Get a single Genuka subscription record.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `subscription` | string | GenukaSubscription ULID |

```http
GET /2023-11/admin/genuka/subscriptions/01k9aabbccdd1ee2ff3gg4hh5i HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Returns a `GenukaSubscriptionResource`. Note: the controller `show` method body is currently a stub and returns an empty response — the shape below reflects what would be returned once implemented.
```json
{
  "id": "01k9aabbccdd1ee2ff3gg4hh5i",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "plan_lookup_key": "pro-monthly",
  "status": "active",
  "total": 500000,
  "currency_code": "XAF",
  "payment_method": "mobile_money",
  "starts_at": "2024-03-01T00:00:00.000000Z",
  "ends_at": "2024-04-01T00:00:00.000000Z",
  "trial_ends_at": null,
  "metadata": {},
  "plan": {
    "id": "01hrx...",
    "name": "Pro",
    "lookup_key": "pro-monthly",
    "price": 500000,
    "duration_in_days": 30
  },
  "created_at": "2024-03-01T00:00:00.000000Z",
  "updated_at": "2024-03-01T00:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Subscription not found.

---

### `PUT` `/2023-11/admin/genuka/subscriptions/{subscription}`

Update a Genuka subscription record.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `subscription` | string | GenukaSubscription ULID |

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `status` | string | No | `pending`, `active`, `terminated`, `expired` | New subscription status |
| `starts_at` | string | No | ISO 8601 datetime | Override subscription start date |
| `ends_at` | string | No | ISO 8601 datetime | Override subscription end date |
| `plan_lookup_key` | string | No | Must exist in `plans.lookup_key` | Change the associated plan |
| `metadata` | object | No | — | Arbitrary metadata to merge |

```http
PUT /2023-11/admin/genuka/subscriptions/01k9aabbccdd1ee2ff3gg4hh5i HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "status": "terminated"
}
```

**Success response** (`200 OK`): Note — the controller `update` method body is currently a stub and returns an empty response. The shape below reflects the expected output once implemented.
```json
{
  "id": "01k9aabbccdd1ee2ff3gg4hh5i",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "plan_lookup_key": "pro-monthly",
  "status": "terminated",
  "total": 500000,
  "currency_code": "XAF",
  "payment_method": "mobile_money",
  "starts_at": "2024-03-01T00:00:00.000000Z",
  "ends_at": "2024-04-01T00:00:00.000000Z",
  "trial_ends_at": null,
  "metadata": {},
  "plan": {
    "id": "01hrx...",
    "name": "Pro",
    "lookup_key": "pro-monthly",
    "price": 500000,
    "duration_in_days": 30
  },
  "created_at": "2024-03-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T12:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Subscription not found.

---

### `DELETE` `/2023-11/admin/genuka/subscriptions/{subscription}`

Delete a Genuka subscription record.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `subscription` | string | GenukaSubscription ULID |

```http
DELETE /2023-11/admin/genuka/subscriptions/01k9aabbccdd1ee2ff3gg4hh5i HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Note — the controller `destroy` method body is currently a stub and returns an empty response. The expected response once implemented:
```json
{ "message": "Subscription deleted." }
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Subscription not found.

---

## Plans (Public)

### `GET` `/2023-11/plans`

Public endpoint. Returns all available Genuka plans.

```http
GET /2023-11/plans HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "id": "01hrx...",
    "name": "Starter",
    "description": "For small businesses",
    "price": 100000,
    "currency_code": "XAF",
    "currency_name": "FCFA",
    "lookup_key": "starter-monthly",
    "duration_in_days": 30,
    "created_at": "2024-01-01T00:00:00.000000Z",
    "updated_at": "2024-01-01T00:00:00.000000Z"
  }
]
```

---

### `GET` `/2023-11/plans/{plan}`

Public endpoint. Get a specific plan by its `lookup_key`.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `plan` | string | Plan `lookup_key` (e.g. `pro-monthly`) |

```http
GET /2023-11/plans/pro-monthly HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrx...",
  "name": "Pro",
  "description": "For growing businesses",
  "price": 500000,
  "currency_code": "XAF",
  "currency_name": "FCFA",
  "lookup_key": "pro-monthly",
  "duration_in_days": 30,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z"
}
```

**Error responses**
- `404` — Plan not found.

---

## Search

### `GET` `/2023-11/admin/search`

Generate a scoped Meilisearch tenant token valid for 12 hours. The token is automatically filtered to the authenticated company (and optionally a specific shop via `X-Shop` header).

**Headers**

| Header | Required | Description |
|--------|----------|-------------|
| `X-Company` | Yes | Company ULID — used as the Meilisearch filter |
| `X-Shop` | No | Shop ULID — adds an additional `shop_id` filter to the token |

```http
GET /2023-11/admin/search HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
X-Shop: 01k1amypzhgs7fngfx1r9d37pn
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "host": "https://search.genuka.com",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Error responses**
- `400` — `X-Company` header missing.
- `401` — Unauthenticated.
- `500` — Meilisearch configuration incomplete or token generation error.

---

### `POST` `/2023-11/admin/search`

Server-side multi-index full-text search across all company data in Meilisearch. Returns up to `limit` hits per index.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `q` | string | No | Min 2 chars to return results | Search query |
| `indexes` | array | No | — | List of index names to search. Defaults to all available indexes. |
| `limit` | integer | No | 1–20, default 5 | Max results per index |

Default indexes searched: `products`, `orders`, `customers`, `suppliers`, `supplierproducts`, `deliveries`, `pages`, `pickup_locations`, `payments`, `bill_of_materials`, `articles`, `document_number_formats`, `templates`, `campaigns`, `stock_movements`, `taxes`, `webhooks`, `calendar_events`, `addresses`.

```http
POST /2023-11/admin/search HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "q": "laptop",
  "indexes": ["products", "orders"],
  "limit": 5
}
```

**Success response** (`200 OK`):
```json
{
  "results": {
    "products": [
      { "id": "01hrx...", "name": "Laptop Pro 15", "_formatted": { "name": "<em>Laptop</em> Pro 15" } }
    ],
    "orders": []
  }
}
```

**Error responses**
- `400` — `companyId` missing (derived from `X-Company` header).
- `401` — Unauthenticated.

---

## Enums

### `GET` `/2023-11/enums/notification-events`

Public endpoint. Returns all notification event enum values with their labels and descriptions.

```http
GET /2023-11/enums/notification-events HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "value": "order.new",
    "label": "New Order",
    "description": "Triggered when a new order is placed"
  },
  {
    "value": "payment.received",
    "label": "Payment Received",
    "description": "Triggered when a payment is confirmed"
  },
  {
    "value": "stock.alert",
    "label": "Stock Alert",
    "description": "Triggered when stock falls below threshold"
  }
]
```

---

## Mobile Sync

### `GET` `/2023-11/admin/sync/pull`

Pull entity changes from the server since the given cursor timestamp. Designed for offline-first mobile apps.

**Query parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `cursor` | string | No | Beginning of time | ISO 8601 timestamp. Returns records updated after this point. |
| `entity_types` | string | No | All types | Comma-separated entity type names: `product`, `customer`, `order`, `order_product`, `payment` |
| `limit` | integer | No | 50 | Max changes to return (1–100) |

```http
GET /2023-11/admin/sync/pull?cursor=2024-03-01T00:00:00Z&entity_types=product,customer&limit=50 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "cursor": "2024-03-15T10:30:00.000000Z",
  "has_more": false,
  "changes": [
    {
      "entity_type": "product",
      "operation": "update",
      "entity": {
        "id": "01hrx...",
        "name": "Updated Product",
        "price": 5000
      }
    }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Validation failure on query params.

---

### `POST` `/2023-11/admin/sync/push`

Push local operations (creates, updates, deletes) from mobile to the server. Up to 50 operations per request.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `operations` | array | Yes | 1–50 items | List of operation objects |
| `operations[].idempotency_key` | string | Yes | — | Unique key to deduplicate retries |
| `operations[].entity_type` | string | Yes | `product`, `customer`, `order`, `order_product`, `payment` | Entity type |
| `operations[].operation` | string | Yes | `create`, `update`, `delete` | Operation type |
| `operations[].local_id` | string | Yes | — | Client-side local ID for the entity |
| `operations[].payload` | object | Yes | — | Entity data for create/update operations |

```http
POST /2023-11/admin/sync/push HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "operations": [
    {
      "idempotency_key": "550e8400-e29b-41d4-a716-446655440000",
      "entity_type": "order",
      "operation": "create",
      "local_id": "local-order-123",
      "payload": {
        "customer_id": "01hrx...",
        "amount": 25000,
        "shop_id": "01k1amypzhgs7fngfx1r9d37pn"
      }
    }
  ]
}
```

**Success response** (`200 OK`):
```json
{
  "results": [
    {
      "idempotency_key": "550e8400-e29b-41d4-a716-446655440000",
      "success": true,
      "server_id": "01hrx...",
      "error": null,
      "conflict": false,
      "server_entity": null
    }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Validation failure (missing fields, invalid entity type or operation).
- `500` — Server-side processing error.

---

## Users

### `GET` `/2023-11/admin/users`

List users belonging to the current company.

**Query parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `per_page` | integer | No | 15 | Items per page |
| `page` | integer | No | 1 | Page number |
| `include` | string | No | — | Comma-separated: `shops`, `orders` |
| `filter[search]` | string | No | — | Search on name, email, phone, role |
| `filter[role]` | string | No | — | Filter by role name |
| `filter[shop_id]` | string | No | — | Filter users assigned to a specific shop |
| `filter[ids]` | array | No | — | Filter by specific user IDs |
| `filter[updated_at][start]` | string | No | — | Updated after this date (ISO 8601) |
| `filter[updated_at][end]` | string | No | — | Updated before this date (ISO 8601) |
| `sort` | string | No | `first_name` | Sort: `first_name`, `last_name`, `email`, `phone`, `created_at`, `updated_at`, `role` |
| `check_date` | string | No | — | ISO 8601 datetime; when provided each user includes `is_available` and `calendar_events` |

```http
GET /2023-11/admin/users?filter[role]=admin&per_page=20 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01k7ee7anat9j4bn5v71v70qck",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "name": "Wilfried Djopa",
      "email": "wilfried@acme.cm",
      "phone": "+237699000000",
      "role": "admin",
      "opt_in": 1,
      "birthdate": null,
      "last_login_at": "2024-03-15T10:00:00.000000Z",
      "last_login_ip": "41.205.x.x",
      "last_activity": "2024-03-15T10:30:00.000000Z",
      "metadata": null,
      "pin_enabled": false,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shops": [],
      "allPermissions": ["orders.*", "products.*"],
      "permissions": ["orders.*", "products.*"],
      "medias": [],
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "deleted_at": null
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/users`

Create a new user and attach them to the company, or invite an existing user to join. If the email already exists in the system, an invitation is sent and `code: "INVITATION_SENT"` is returned.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `email` | string | Yes | Valid email | User email address |
| `first_name` | string | No | — | First name |
| `last_name` | string | No | — | Last name |
| `phone` | string | No | — | Phone number |
| `role` | string | Yes | Valid `Role` enum value: `admin`, `manager`, `employee`, `agent`, `courier` | Company role |
| `password` | string | No | — | Initial password (if creating new user) |
| `permissions` | array | No | Valid `Permission` enum values | Explicit permission overrides |
| `shop_ids` | array | No | Existing shop ULIDs | Shops to assign the user to |

```http
POST /2023-11/admin/users HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Marie",
  "last_name": "Dupont",
  "email": "marie.dupont@acme.cm",
  "phone": "+237699000003",
  "role": "employee",
  "password": "SecurePass!123",
  "shop_ids": ["01k1amypzhgs7fngfx1r9d37pn"],
  "permissions": [
    "orders.view", "orders.create",
    "products.view",
    "customers.view"
  ]
}
```

**Success response** (`201 Created` for new user / `200 OK` for invitation):
```json
{
  "id": "01k7ee7anat9j4bn5v71v70qck",
  "first_name": "Marie",
  "last_name": "Dupont",
  "email": "marie.dupont@acme.cm",
  "role": "employee",
  "permissions": ["orders.view", "orders.create", "products.view", "customers.view"],
  "shops": [{ "id": "01k1amypzhgs7fngfx1r9d37pn", "name": "Downtown Store" }],
  "created_at": "2024-03-15T10:30:00.000000Z"
}
```

When user already exists:
```json
{
  "message": "Invitation sent.",
  "code": "INVITATION_SENT",
  "user": { "id": "01k7ee7anat9j4bn5v71v70qck", "email": "marie.dupont@acme.cm" }
}
```

**Error responses**
- `400` — Business rule violation (e.g. user already in company).
- `401` — Unauthenticated.
- `422` — Validation failure.

---

### `GET` `/2023-11/admin/users/invitations/pending`

List all pending invitations for the current company.

```http
GET /2023-11/admin/users/invitations/pending HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": 42,
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "role": "employee",
      "invited_at": "2024-03-14T09:00:00.000000Z",
      "invited_by": "01hrx...",
      "first_name": "Marie",
      "last_name": "Dupont",
      "email": "marie.dupont@acme.cm"
    }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `DELETE` `/2023-11/admin/users/invitations/{user}`

Cancel a pending invitation for a user.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `user` | string | User ULID whose pending invitation to cancel |

```http
DELETE /2023-11/admin/users/invitations/01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Invitation cancelled." }
```

**Error responses**
- `401` — Unauthenticated.
- `404` — No pending invitation found for this user in this company.

---

### `POST` `/2023-11/admin/users/invitations/{user}/resend`

Resend an invitation email to the specified user. Generates a new invitation token and resets the expiry clock (7-day window).

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `user` | string | User ULID to resend the invitation to |

**Request body**: None. The user to reinvite is identified solely by the `{user}` path parameter.

```http
POST /2023-11/admin/users/invitations/01k7ee7anat9j4bn5v71v70qck/resend HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Invitation resent." }
```

**Error responses**
- `401` — Unauthenticated.
- `404` — No pending invitation found for this user.

---

### `PUT` `/2023-11/admin/users/metadata`

Update arbitrary metadata key-value pairs for the authenticated user. Any key from the request body (except `metadata` and `anonymous`) is merged into the user's metadata object.

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `*` | any | No | — | Any arbitrary key-value pair. All keys except `metadata` and `anonymous` are merged into the user's `metadata` field. |

```http
PUT /2023-11/admin/users/metadata HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "preferred_language": "fr",
  "dashboard_layout": "grid",
  "theme": "dark"
}
```

**Success response** (`200 OK`): Returns the updated `UserResource`:
```json
{
  "id": "01k7ee7anat9j4bn5v71v70qck",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "name": "Wilfried Djopa",
  "email": "wilfried@acme.cm",
  "phone": "+237699000000",
  "role": "admin",
  "opt_in": 1,
  "birthdate": null,
  "last_login_at": "2024-03-15T10:00:00.000000Z",
  "last_login_ip": "41.205.x.x",
  "last_activity": "2024-03-15T10:30:00.000000Z",
  "metadata": {
    "preferred_language": "fr",
    "dashboard_layout": "grid",
    "theme": "dark"
  },
  "pin_enabled": false,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shops": [],
  "allPermissions": ["orders.*", "products.*"],
  "permissions": ["orders.*", "products.*"],
  "medias": [],
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T12:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/users/stats`

Returns aggregated dashboard statistics for the authenticated user over a date range. Each metric includes daily values for the period, a total, a comparison against the previous equal-length period, and a percentage change.

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `start_date` | string | No | Start of period (ISO 8601). Defaults to start of current month. |
| `end_date` | string | No | End of period (ISO 8601). Defaults to end of current month. |

```http
GET /2023-11/admin/users/stats?start_date=2024-03-01&end_date=2024-03-31 HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Returns an array of three metric objects — `Total sales`, `Orders`, and `Services performed`:
```json
[
  {
    "label": "Total sales",
    "data": [0, 25000, 0, 50000, 12000, 0, 0, 8000, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    "total": 95000,
    "previous": 72000,
    "percentage": 31.94
  },
  {
    "label": "Orders",
    "data": [0, 1, 0, 2, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    "total": 5,
    "previous": 4,
    "percentage": 25.0
  },
  {
    "label": "Services performed",
    "data": [0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    "total": 1,
    "previous": 0,
    "percentage": 100.0
  }
]
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/users/{user}`

Get a single user's details. A user may always fetch their own record. Admins may fetch any user in the same company.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `user` | string | User ULID |

**Query parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `include` | array | No | Relations to load (e.g. `availabilities`) |
| `check_date` | string | No | ISO 8601 datetime; adds `is_available` and `calendar_events` to the response |

```http
GET /2023-11/admin/users/01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Returns a `UserResource` (same shape as list item):
```json
{
  "id": "01k7ee7anat9j4bn5v71v70qck",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "name": "Wilfried Djopa",
  "email": "wilfried@acme.cm",
  "phone": "+237699000000",
  "role": "admin",
  "opt_in": 1,
  "birthdate": null,
  "last_login_at": "2024-03-15T10:00:00.000000Z",
  "last_login_ip": "41.205.x.x",
  "last_activity": "2024-03-15T10:30:00.000000Z",
  "metadata": null,
  "pin_enabled": false,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shops": [],
  "allPermissions": ["orders.*", "products.*"],
  "permissions": ["orders.*", "products.*"],
  "medias": [],
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Non-admin trying to view another user, or target is in a different company.
- `404` — User not found.

---

### `PUT` `/2023-11/admin/users/{user}`

Update a user's profile, role, permissions, and shop assignments.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `user` | string | User ULID |

**Request body**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `first_name` | string | No | — | First name |
| `last_name` | string | No | — | Last name |
| `email` | string | No | Unique among users (ignores self) | Email address |
| `phone` | string | No | — | Phone number |
| `role` | string | Yes | Valid `Role` enum value | Company role |
| `permissions` | array | No | Valid `Permission` enum values | Explicit permissions |
| `shop_ids` | array | No | Valid shop ULIDs | Replace shop assignments |
| `birthdate` | string | No | — | ISO 8601 date |
| `opt_in` | integer | No | 0 or 1 | Marketing opt-in flag |
| `metadata` | object | No | — | Arbitrary metadata |

```http
PUT /2023-11/admin/users/01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Marie",
  "last_name": "Dupont",
  "role": "manager",
  "shop_ids": ["01k1amypzhgs7fngfx1r9d37pn"],
  "permissions": ["orders.*", "products.*", "customers.*"]
}
```

**Success response** (`200 OK`): Returns the updated `UserResource`:
```json
{
  "id": "01k7ee7anat9j4bn5v71v70qck",
  "first_name": "Marie",
  "last_name": "Dupont",
  "name": "Marie Dupont",
  "email": "marie.dupont@acme.cm",
  "phone": "+237699000003",
  "role": "manager",
  "opt_in": 0,
  "birthdate": null,
  "last_login_at": null,
  "last_login_ip": null,
  "last_activity": null,
  "metadata": null,
  "pin_enabled": false,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shops": [
    { "id": "01k1amypzhgs7fngfx1r9d37pn", "name": "Downtown Store", "slug": "downtown-store" }
  ],
  "allPermissions": ["orders.*", "products.*", "customers.*"],
  "permissions": ["orders.*", "products.*", "customers.*"],
  "medias": [],
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T13:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `400` / `403` — Business rule violation (not in the same company, etc.).
- `401` — Unauthenticated.
- `422` — Validation failure.

---

### `DELETE` `/2023-11/admin/users/{user}`

Delete or detach a user. Requires admin role. If the user belongs to multiple companies, they are only detached from the current company. If they belong to only one company, the user record is soft-deleted. The last admin in a company cannot be removed.

**Path parameters**

| Name | Type | Description |
|------|------|-------------|
| `user` | string | User ULID |

```http
DELETE /2023-11/admin/users/01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Authorization: Bearer <access_token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "User deleted" }
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Caller is not an admin.
- `500` — Cannot remove last admin, or database transaction failure.
