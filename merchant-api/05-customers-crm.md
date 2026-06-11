---
title: "Customers & CRM"
description: "The Customers & CRM domain covers the full lifecycle of customer records and related CRM data. It exposes two surfaces: an **admin** surface (authenticated with"
sidebarTitle: "Customers & CRM"
---

The Customers & CRM domain covers the full lifecycle of customer records and related CRM data. It exposes two surfaces: an **admin** surface (authenticated with a staff bearer token) for managing customer records, contacts, addresses, notes, and note categories; and a **customer-facing** surface (authenticated with a customer bearer token) for self-service profile management, address management, and order placement. Key sub-resources include contacts (typed contact entries: phone, email, person), addresses (shipping/billing), CRM notes with media attachments, and note categories.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/2023-11/admin/customers` | List customers (filterable, sortable, paginated) |
| `POST` | `/2023-11/admin/customers` | Create a customer |
| `POST` | `/2023-11/admin/customers/bulk-delete` | Bulk delete customers |
| `POST` | `/2023-11/admin/customers/bulk-tags` | Bulk add/remove tags on customers |
| `POST` | `/2023-11/admin/customers/bulk-update` | Bulk update customer fields |
| `GET` | `/2023-11/admin/customers/count` | Get total customer count |
| `POST` | `/2023-11/admin/customers/export` | Trigger async customer export |
| `GET` | `/2023-11/admin/customers/export/fields` | List available export columns |
| `POST` | `/2023-11/admin/customers/import` | Import customers from file |
| `POST` | `/2023-11/admin/customers/import-chunk` | Upload one chunk of a chunked import |
| `POST` | `/2023-11/admin/customers/merge` | Merge multiple customer records |
| `POST` | `/2023-11/admin/customers/merge-chunk` | Finalise a chunked merge |
| `GET` | `/2023-11/admin/customers/overview` | Get customer overview stats |
| `GET` | `/2023-11/admin/customers/{customer}` | Get a single customer |
| `PUT` | `/2023-11/admin/customers/{customer}` | Update a customer |
| `DELETE` | `/2023-11/admin/customers/{customer}` | Delete a customer |
| `GET` | `/2023-11/admin/customers/{customer}/activities` | Get customer activity feed |
| `GET` | `/2023-11/admin/customers/{customer}/campaigns` | List campaigns the customer received |
| `GET` | `/2023-11/admin/customers/{customer}/contacts` | List contacts for a customer |
| `POST` | `/2023-11/admin/customers/{customer}/contacts` | Add a contact to a customer |
| `POST` | `/2023-11/admin/customers/{customer}/contacts/bulk` | Bulk manage contacts |
| `GET` | `/2023-11/admin/customers/{customer}/contacts/{contact}` | Get a single contact |
| `PUT` | `/2023-11/admin/customers/{customer}/contacts/{contact}` | Update a contact |
| `DELETE` | `/2023-11/admin/customers/{customer}/contacts/{contact}` | Delete a contact |
| `POST` | `/2023-11/admin/customers/{customer}/contacts/{contact}/set-primary` | Set a contact as primary |
| `POST` | `/2023-11/admin/customers/{customer}/notes` | Create a note scoped to a customer |
| `PATCH` | `/2023-11/admin/customers/{customer}/notes/{customerNote}` | Update a note scoped to a customer |
| `PUT` | `/2023-11/admin/customers/{customer}/tags` | Replace all tags on a customer |
| `POST` | `/2023-11/admin/customers/{customer}/tags` | Attach tags to a customer |
| `DELETE` | `/2023-11/admin/customers/{customer}/tags/{tag}` | Detach a tag from a customer |
| `GET` | `/2023-11/admin/customers/{customer}/top-products` | Get top products purchased by a customer |
| `GET` | `/2023-11/admin/customers/{customer}/top-products/categories` | Get top product categories for a customer |
| `PATCH` | `/2023-11/admin/customers/{customer}/{field}` | Update a single custom field |
| `GET` | `/2023-11/admin/addresses` | List addresses for a model |
| `POST` | `/2023-11/admin/addresses` | Create an address for a model |
| `GET` | `/2023-11/admin/addresses/{address}` | Get a single address |
| `PUT` | `/2023-11/admin/addresses/{address}` | Update an address |
| `DELETE` | `/2023-11/admin/addresses/{address}` | Delete an address |
| `GET` | `/2023-11/admin/customer-notes` | List CRM notes |
| `POST` | `/2023-11/admin/customer-notes` | Create a CRM note |
| `GET` | `/2023-11/admin/customer-notes/{customerNote}` | Get a single CRM note |
| `PUT` | `/2023-11/admin/customer-notes/{customerNote}` | Update a CRM note |
| `DELETE` | `/2023-11/admin/customer-notes/{customerNote}` | Delete a CRM note |
| `GET` | `/2023-11/admin/customer-note-categories` | List note categories |
| `POST` | `/2023-11/admin/customer-note-categories` | Create a note category |
| `GET` | `/2023-11/admin/customer-note-categories/{customerNoteCategory}` | Get a note category |
| `PUT` | `/2023-11/admin/customer-note-categories/{customerNoteCategory}` | Update a note category |
| `DELETE` | `/2023-11/admin/customer-note-categories/{customerNoteCategory}` | Delete a note category |
| `POST` | `/2023-11/customers/check-identity` | Resolve account holder name for a phone number |
| `POST` | `/2023-11/customers/login` | Customer login |
| `POST` | `/2023-11/customers/logout` | Customer logout |
| `POST` | `/2023-11/customers/register` | Customer registration |
| `GET` | `/2023-11/customers/profile` | Get authenticated customer profile |
| `PUT` | `/2023-11/customers/profile` | Update authenticated customer profile |
| `PUT` | `/2023-11/customers/profile/password` | Update customer password |
| `POST` | `/2023-11/customers/password/forgot` | Send password reset email |
| `POST` | `/2023-11/customers/password/reset` | Reset password with token |
| `POST` | `/2023-11/customers/password/reset-link` | Get a raw reset token (no email) |
| `GET` | `/2023-11/customers/addresses` | List authenticated customer's addresses |
| `POST` | `/2023-11/customers/addresses` | Create an address for the authenticated customer |
| `GET` | `/2023-11/customers/addresses/{address}` | Get a single address |
| `PUT` | `/2023-11/customers/addresses/{address}` | Update an address |
| `DELETE` | `/2023-11/customers/addresses/{address}` | Delete an address |
| `POST` | `/2023-11/customers/orders` | Place an order (public/storefront) |
| `GET` | `/2023-11/customers/orders` | List authenticated customer's orders |
| `GET` | `/2023-11/customers/orders/{orderId}` | Get a single order |
| `PUT` | `/2023-11/customers/orders/{order}` | Update an order |
| `POST` | `/2023-11/customers/orders/{order}/payments` | Generate a payment link for an order |

---

## Customers (Admin)

### `GET` `/2023-11/admin/customers`
List customers with filtering, sorting, and pagination.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page (max 100) |
| `sort` | string | No | — | Comma-separated field(s). Prefix `-` for descending. Supported: `first_name`, `last_name`, `email`, `phone`, `created_at`, `updated_at`, `orders_sum_amount`, `orders_count` |
| `include` | string | No | — | Comma-separated relations to load: `orders_sum_amount`, `ordersCount`, `lastOrder` |
| `filter[search]` | string | No | — | Full-text search across name, email, phone |
| `filter[activity][start]` | date | No | — | Filter by last activity date (start) |
| `filter[activity][end]` | date | No | — | Filter by last activity date (end) |
| `anonymous` | boolean | No | false | Mask PII in the response |

**Request example:**

```http
GET /2023-11/admin/customers?page=1&per_page=15&sort=-created_at&include=ordersCount,lastOrder HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01k74k19r8cc6ksb89a1fnh6md",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "email": "wilfried@example.com",
      "phone": "+237695762595",
      "gender": "M",
      "birthdate": null,
      "type": "individual",
      "company_name": null,
      "registration_number": null,
      "tax_number": null,
      "preferred_language": null,
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "last_activity": "2025-10-09T13:37:19.000000Z",
      "deleted_at": null,
      "created_at": "2025-10-09T13:37:12.000000Z",
      "updated_at": "2025-10-09T13:37:19.000000Z",
      "custom_fields": [],
      "default_address": null,
      "addresses": [],
      "contacts": [],
      "medias": [],
      "notes": [],
      "tags": [],
      "badges": []
    }
  ],
  "pagination": {
    "total": 284,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 19
  }
}
```

**Error responses:**
- `400` — missing `X-Company` header (`company_id_required`)
- `401` — invalid or expired token
- `403` — insufficient permissions (`invalid_scope`)

---

### `POST` `/2023-11/admin/customers`
Create a new customer. Supports optional duplicate detection before creation.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `first_name` | string | Conditionally | Required unless `last_name` present | Customer first name |
| `last_name` | string | Conditionally | Required unless `first_name` present | Customer last name |
| `email` | string | Conditionally | Required unless `phone` present; must be unique | Customer email |
| `phone` | string | Conditionally | Required unless `email` present; must be unique within company | E.164 phone number |
| `gender` | string | No | One of: `M`, `F`, `other` | Gender |
| `type` | string | No | One of: `individual`, `company` | Customer type |
| `company_name` | string | No | — | Company name (when `type=company`) |
| `company_id` | ULID | No | Must exist in `companies` | Override tenant company |
| `birthdate` | string | No | — | Date of birth (`YYYY-MM-DD`) |
| `preferred_language` | string | No | — | BCP-47 language code |
| `registration_number` | string | No | — | Business registration number |
| `tax_number` | string | No | — | Tax/VAT number |
| `metadata` | object | No | — | Arbitrary key-value metadata |
| `tags` | array | No | — | Tags to attach; each item: `{"name": "tag-name"}` or `{"id": "<tagId>"}` |
| `medias` | array | No | — | Media to attach; each item: `{"src": "<url>", "name": "filename.jpg"}` |
| `detect_duplicate` | boolean | No | — | When `true`, check for duplicates before creating |

**Request example:**

```http
POST /2023-11/admin/customers HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Test",
  "last_name": "Customer",
  "email": "test+3@genuka.com",
  "phone": "+237678120940",
  "tags": [{"name": "vip"}],
  "medias": [{"src": "https://genuka.com/logo.png", "name": "profile.jpg"}]
}
```

**Success response `200 OK`** (or `200 OK` with `duplicate_detected: true` when duplicate found):

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Test",
  "last_name": "Customer",
  "email": "test+3@genuka.com",
  "phone": "+237678120940",
  "gender": null,
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": null,
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-10-09T13:37:12.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [{"id": "01hztag001abc", "name": "vip"}],
  "badges": []
}
```

When `detect_duplicate=true` and a duplicate is found:

```json
{
  "duplicate_detected": true,
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Test",
    "last_name": "Customer",
    "email": "test+3@genuka.com",
    "phone": "+237678120940",
    "gender": null,
    "birthdate": null,
    "type": "individual",
    "company_name": null,
    "registration_number": null,
    "tax_number": null,
    "preferred_language": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "last_activity": null,
    "deleted_at": null,
    "created_at": "2025-10-09T13:37:12.000000Z",
    "updated_at": "2025-10-09T13:37:12.000000Z",
    "custom_fields": [],
    "default_address": null,
    "addresses": [],
    "contacts": [],
    "medias": [],
    "notes": [],
    "tags": [],
    "badges": []
  }
}
```

**Error responses:**
- `400` — missing required fields or duplicate email/phone
- `422` — validation failure with `errors` object

---

### `GET` `/2023-11/admin/customers/count`
Returns the total number of customers for the current company.

**Request example:**

```http
GET /2023-11/admin/customers/count HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "count": 1842
}
```

---

### `GET` `/2023-11/admin/customers/overview`
Returns aggregated customer statistics (new customers, returning rate, etc.).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `start_date` | date | No | — | Start of the analysis period |
| `end_date` | date | No | — | End of the analysis period |

**Request example:**

```http
GET /2023-11/admin/customers/overview?start_date=2025-01-01&end_date=2025-06-30 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "range": {
    "start": "2025-01-01T00:00:00+00:00",
    "end": "2025-06-30T23:59:59+00:00"
  },
  "kpis": {
    "total": 1842,
    "active": {
      "value": 134,
      "delta_pct": 12.5
    },
    "vip": 47,
    "at_risk": 23,
    "total_spent": 48750000.0,
    "at_risk_threshold_days": 90,
    "vip_min_orders": 5
  },
  "summary": {
    "new_customers": {
      "value": 134,
      "delta_pct": 8.1
    },
    "revenue": {
      "value": 48750000.0,
      "delta_pct": 15.3,
      "sparkline": [320000, 410000, 390000, 520000, 480000, 610000, 590000, 700000, 680000, 750000, 820000, 900000, 870000, 960000]
    },
    "top_customers": [],
    "follow_up_recommendations": []
  }
}
```

---

### `GET` `/2023-11/admin/customers/{customer}`
Get a single customer by ID. Loads addresses, tags, media. Optionally loads stats and last order.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `include` | string | No | — | Set to `lastOrder` to include the most recent order |
| `simple` | boolean | No | false | Return minimal response without stats computation |

**Request example:**

```http
GET /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-10-09T13:37:19.000000Z",
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-10-09T13:37:19.000000Z",
  "custom_fields": [],
  "default_address": {
    "id": "01hhg5988jrd441hx0yerftzkt",
    "label": "Home",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "phone": "+237695762595",
    "email": "wilfried@example.com",
    "company": null,
    "line1": "9 Rue Sainte-Honorine",
    "line2": null,
    "city": "Montesson",
    "state": null,
    "country": "France",
    "postal_code": "78360",
    "latitude": null,
    "longitude": null,
    "is_primary": true,
    "is_billing": true,
    "is_shipping": true,
    "metadata": {},
    "addressable_type": "App\\Models\\Customer",
    "addressable_id": "01k74k19r8cc6ksb89a1fnh6md",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "created_at": "2024-03-15T10:23:45.000000Z",
    "updated_at": "2024-03-15T10:23:45.000000Z"
  },
  "addresses": [
    {
      "id": "01hhg5988jrd441hx0yerftzkt",
      "label": "Home",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "company": null,
      "phone": "+237695762595",
      "email": "wilfried@example.com",
      "line1": "9 Rue Sainte-Honorine",
      "line2": null,
      "city": "Montesson",
      "state": null,
      "country": "France",
      "postal_code": "78360",
      "latitude": null,
      "longitude": null,
      "is_primary": true,
      "is_billing": true,
      "is_shipping": true,
      "metadata": {},
      "addressable_type": "App\\Models\\Customer",
      "addressable_id": "01k74k19r8cc6ksb89a1fnh6md",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z"
    }
  ],
  "contacts": [],
  "medias": [
    {
      "id": 81,
      "link": "https://cdn.genuka.com/company/81/logo.png",
      "collection_name": "default",
      "mime_type": "image/png",
      "file_name": "logo.png",
      "size": 10830,
      "created_at": "2025-10-09T13:37:13.000000Z",
      "updated_at": "2025-10-09T13:37:13.000000Z",
      "micro": "https://cdn.genuka.com/company/81/conversions/logo-micro.jpg",
      "thumb": "https://cdn.genuka.com/company/81/conversions/logo-thumb.jpg",
      "large": "https://cdn.genuka.com/company/81/conversions/logo-large.jpg"
    }
  ],
  "notes": [],
  "tags": [],
  "badges": [],
  "stats": {
    "orders_count": 5,
    "orders_total": 45000.00,
    "average_order_value": 9000.00,
    "last_order_at": "2025-06-01T10:00:00.000000Z"
  }
}
```

**Error responses:**
- `401` — invalid token
- `404` — customer not found

---

### `PUT` `/2023-11/admin/customers/{customer}`
Update a customer. Returns the updated customer (same shape as `GET` show).

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Request body:** Any subset of the customer fields. The `email` and `phone` fields are validated for uniqueness (ignoring the current customer).

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `first_name` | string | No | — | First name |
| `last_name` | string | No | — | Last name |
| `email` | string | No | Unique across company | Email address |
| `phone` | string | No | Unique within company | Phone number |
| `gender` | string | No | One of: `M`, `F`, `other` | Gender |
| `type` | string | No | One of: `individual`, `company` | Customer type |
| `company_name` | string | No | — | Company name |
| `birthdate` | string | No | — | `YYYY-MM-DD` |
| `preferred_language` | string | No | — | BCP-47 language tag |
| `registration_number` | string | No | — | Business registration number |
| `tax_number` | string | No | — | Tax/VAT number |
| `metadata` | object | No | — | Arbitrary key-value metadata |
| `medias` | array | No | — | Replace media attachments |

**Request example:**

```http
PUT /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "type": "individual",
  "birthdate": "1990-05-15"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": "1990-05-15",
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-10-09T13:37:19.000000Z",
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [],
  "badges": []
}
```

**Error responses:**
- `400` — duplicate email or phone (`The email has already been taken.`)
- `401` — invalid token
- `404` — customer not found
- `422` — validation failure

---

### `DELETE` `/2023-11/admin/customers/{customer}`
Soft-delete a customer. Pass `?force=true` to permanently delete.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `force` | boolean | No | false | Permanently delete (forceDelete) |

**Request example:**

```http
DELETE /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "message": "Customer deleted"
}
```

**Error responses:**
- `401` — invalid token
- `404` — customer not found

---

### `POST` `/2023-11/admin/customers/bulk-delete`
Delete multiple customers in a single request.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `customer_ids` | array | Yes | Array of ULIDs | IDs of customers to delete |

**Request example:**

```http
POST /2023-11/admin/customers/bulk-delete HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "customer_ids": [
    "01k74k19r8cc6ksb89a1fnh6md",
    "01k74k1abc123def456ghi789j"
  ]
}
```

**Success response `200 OK`:**

```json
{
  "success": true,
  "deleted_count": 2,
  "message": "All selected customers are deleted"
}
```

If some deletions fail, `warning: true` and `failed_ids` are added to the response.

---

### `POST` `/2023-11/admin/customers/bulk-update`
Update fields across multiple customers in one request. Up to 100 customers per call.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `customers` | array | Yes | 1–100 items | Array of customer patch objects |
| `customers.*.id` | ULID | Yes | Must exist | Target customer ID |
| `customers.*.first_name` | string | No | max:255 | First name |
| `customers.*.last_name` | string | No | max:255 | Last name |
| `customers.*.email` | string | No | email, max:255 | Email |
| `customers.*.phone` | string | No | max:255 | Phone |
| `customers.*.gender` | string | No | `M`, `F`, or `other` | Gender |
| `customers.*.type` | string | No | `individual` or `company` | Customer type |
| `customers.*.company_name` | string | No | max:255 | Company name |
| `customers.*.registration_number` | string | No | max:255 | Registration number |
| `customers.*.tax_number` | string | No | max:255 | Tax number |
| `customers.*.birthdate` | string | No | `YYYY-MM-DD` or `MM-DD` | Birthdate |
| `customers.*.metadata` | object | No | — | Metadata object |

**Request example:**

```http
POST /2023-11/admin/customers/bulk-update HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "customers": [
    {"id": "01k74k19r8cc6ksb89a1fnh6md", "gender": "M"},
    {"id": "01k74k1abc123def456ghi789j", "type": "company", "company_name": "Acme Corp"}
  ]
}
```

**Success response `200 OK`:**

```json
{
  "success": true,
  "updated_count": 2,
  "message": "Updated successfully"
}
```

---

### `POST` `/2023-11/admin/customers/bulk-tags`
Add and/or remove tags across multiple customers in one call. Up to 100 customers.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `customer_ids` | array | Yes | 1–100 ULIDs, each must exist | Customers to tag |
| `add_tags` | array | No | — | Tags to add; each: `{"id": "<tagId>"}` or `{"name": "label"}` |
| `remove_tag_ids` | array | No | Each must exist in `tags` | Tag IDs to remove |

**Request example:**

```http
POST /2023-11/admin/customers/bulk-tags HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "customer_ids": ["01k74k19r8cc6ksb89a1fnh6md"],
  "add_tags": [{"name": "premium"}],
  "remove_tag_ids": ["01hzabc123tagid"]
}
```

**Success response `200 OK`:**

```json
{
  "success": true,
  "count": 1
}
```

---

### `POST` `/2023-11/admin/customers/merge`
Merge multiple customer records into one, combining orders, addresses, and other relations.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `ids` | array | Yes | Minimum 2 ULIDs | IDs of customers to merge |
| `mainCustomerId` | ULID | No | — | Which customer to keep; defaults to the first |

**Request example:**

```http
POST /2023-11/admin/customers/merge HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "ids": ["01k74k19r8cc6ksb89a1fnh6md", "01k74k1abc123def456ghi789j"],
  "mainCustomerId": "01k74k19r8cc6ksb89a1fnh6md"
}
```

**Success response `200 OK`:**

```json
{
  "message": "Customers merged successfully",
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "wilfried@example.com",
    "phone": "+237695762595",
    "gender": "M",
    "birthdate": null,
    "type": "individual",
    "company_name": null,
    "registration_number": null,
    "tax_number": null,
    "preferred_language": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "last_activity": "2025-10-09T13:37:19.000000Z",
    "deleted_at": null,
    "created_at": "2025-10-09T13:37:12.000000Z",
    "updated_at": "2025-10-09T13:37:19.000000Z"
  }
}
```

**Error responses:**
- `400` / `422` — business rule violation (e.g. customers belong to different companies)

---

### `POST` `/2023-11/admin/customers/export`
Trigger an asynchronous export of customers to CSV or XLSX. The export is processed in the background and the result is sent to the requesting user.

- **Request body (form data or JSON):**

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `format` | string | No | `csv` | Export format: `csv` or `xlsx` |
| `columns` | array | No | All | Column keys to export (see `GET /export/fields`) |
| Any filter param | — | No | — | Same filters as the list endpoint are forwarded to the export |

**Request example:**

```http
POST /2023-11/admin/customers/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "format": "xlsx",
  "columns": ["first_name", "last_name", "email", "phone"]
}
```

**Success response `200 OK`:**

```json
{
  "message": "Export started. You will receive a download link by email when it is ready.",
  "job_id": "01hzjob001abc"
}
```

---

### `GET` `/2023-11/admin/customers/export/fields`
Return the list of column keys available for the customer export.

**Request example:**

```http
GET /2023-11/admin/customers/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "columns": ["first_name", "last_name", "email", "phone", "gender", "birthdate", "type", "company_name", "created_at"]
}
```

---

### `POST` `/2023-11/admin/customers/import`
Import customers from a spreadsheet file (`xlsx`, `xls`, or `csv`). The file is processed synchronously and returns a summary.

- **Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `import_file` | file | Yes | `mimes:xlsx,xls,csv` | The import file |

**Request example:**

```http
POST /2023-11/admin/customers/import HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: multipart/form-data

import_file=<file>
```

**Success response `200 OK`:**

```json
{
  "imported": 42,
  "skipped": 3,
  "errors": []
}
```

---

### `POST` `/2023-11/admin/customers/import-chunk`
Upload one chunk of a large import file (chunked multipart upload protocol). Call `merge-chunk` after all chunks are uploaded.

- **Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `chunk` | file | Yes | — | Binary chunk of the import file |
| `chunkIndex` | integer | Yes | — | Zero-based chunk index |
| `uploadId` | string | Yes | — | Unique identifier for this upload session |

**Success response `200 OK`:**

```json
{
  "success": true,
  "chunkIndex": 0
}
```

---

### `POST` `/2023-11/admin/customers/merge-chunk`
Finalise a chunked import by merging all uploaded chunks and processing the full file.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `uploadId` | string | Yes | — | Upload session ID used during chunk uploads |
| `totalChunks` | integer | Yes | — | Total number of chunks uploaded |
| `fileName` | string | Yes | — | Original file name (used to detect format) |

**Success response `200 OK`:**

```json
{
  "imported": 38,
  "skipped": 2,
  "errors": []
}
```

---

### `GET` `/2023-11/admin/customers/{customer}/activities`
Retrieve a cursor-paginated activity feed for a customer (orders, notes, status changes, etc.).

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `cursor` | string | No | — | Cursor for pagination (returned from previous response) |
| `limit` | integer | No | Service default | Number of activities to return |

**Request example:**

```http
GET /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/activities?limit=20 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "order_created_1748772000_a1b2c3d4",
      "type": "order_created",
      "title": "Order created",
      "description": "#ORD-2025-001 · 10 000 · pending",
      "occurred_at": "2025-06-01T10:00:00+00:00",
      "metadata": {
        "order_id": "01hzorder123abc",
        "amount": 10000.0,
        "status": "pending"
      },
      "icon_hint": "shopping-bag"
    },
    {
      "id": "note_added_1748685600_b2c3d4e5",
      "type": "note_added",
      "title": "Note added",
      "description": "Customer called about order delay.",
      "occurred_at": "2025-05-31T10:00:00+00:00",
      "metadata": {
        "note_id": "01hznote123abc"
      },
      "icon_hint": "file-text"
    }
  ],
  "meta": {
    "next_cursor": "2025-05-31T10:00:00+00:00",
    "limit": 20
  }
}
```

---

### `GET` `/2023-11/admin/customers/{customer}/campaigns`
List campaigns that included this customer, paginated.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `per_page` | integer | No | 15 | Results per page |

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hzcampaign001abc",
      "name": "Summer Promo",
      "channel_type": "whatsapp",
      "message_type": "template",
      "free_message_content": null,
      "status": "sent",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2025-05-01T09:00:00.000000Z",
      "updated_at": "2025-05-01T09:30:00.000000Z",
      "template": null,
      "whatsapp_connection": null,
      "connection": null,
      "customer_status": {
        "status": "delivered",
        "contact": "+237695762595",
        "sent_at": "2025-05-01T09:05:00.000000Z",
        "delivered_at": "2025-05-01T09:06:00.000000Z",
        "read_at": "2025-05-01T09:10:00.000000Z",
        "failed_at": null,
        "error_message": null
      }
    }
  ],
  "links": {
    "first": "https://api.genuka.com/2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/campaigns?page=1",
    "last": "https://api.genuka.com/2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/campaigns?page=1",
    "prev": null,
    "next": null
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 1,
    "per_page": 15,
    "to": 1,
    "total": 1
  }
}
```

---

### `GET` `/2023-11/admin/customers/{customer}/top-products`
Get the products most frequently purchased by this customer, with optional filters.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `period` | string | No | — | Predefined period (e.g. `last_30_days`) |
| `from` | date | No | — | Start date |
| `to` | date | No | — | End date |
| `category_id` | ULID | No | — | Filter by product category |
| `sort` | string | No | — | Sort column |
| `search` | string | No | — | Full-text search |
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hkb1j1z86twgmy3f2cn6aafh",
      "title": "Product A",
      "handle": "product-a",
      "category": "Electronics",
      "category_id": "01hzcat001abc",
      "total_quantity": 5,
      "total_amount": 25000.0,
      "avg_unit_price": 5000.0,
      "last_purchase_date": "2025-05-15T00:00:00.000000Z",
      "first_purchase_date": "2025-01-10T00:00:00.000000Z",
      "orders_count": 3,
      "medias": []
    }
  ],
  "meta": {
    "current_page": 1,
    "per_page": 12,
    "total": 1,
    "last_page": 1,
    "from": null,
    "to": null
  },
  "summary": {
    "total_quantity": 5,
    "total_amount": 25000.0,
    "unique_products": 1
  },
  "insights": {}
}
```

---

### `GET` `/2023-11/admin/customers/{customer}/top-products/categories`
Get the product categories most purchased by this customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hzcat001abc",
      "title": "Electronics"
    },
    {
      "id": "01hzcat002xyz",
      "title": "Accessories"
    }
  ]
}
```

---

### `PUT` `/2023-11/admin/customers/{customer}/tags`
Replace all tags on a customer (idempotent sync).

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `tags` | array | Yes | Full desired tag list; each: `{"name": "label"}` or `{"id": "<tagId>"}` |

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-10-09T13:37:19.000000Z",
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-10-09T13:37:19.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [
    { "id": "01hztag001abc", "name": "vip" }
  ],
  "badges": []
}
```

---

### `POST` `/2023-11/admin/customers/{customer}/tags`
Attach one or more tags to a customer without removing existing ones.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `tags` | array | Yes | — | Tags to add |
| `tags.*.id` | ULID | No | Must exist in `tags` | Existing tag ID |
| `tags.*.name` | string | Conditionally | Required if no `id` | New tag name (max 255) |

**Request example:**

```http
POST /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/tags HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "tags": [{"name": "loyal"}, {"id": "01hzabc123tagid"}]
}
```

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-10-09T13:37:19.000000Z",
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-10-09T13:37:19.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [
    { "id": "01hztag001abc", "name": "loyal" },
    { "id": "01hzabc123tagid", "name": "vip" }
  ],
  "badges": []
}
```

---

### `DELETE` `/2023-11/admin/customers/{customer}/tags/{tag}`
Remove a single tag from a customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |
| `tag` | ULID | Tag ID |

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-10-09T13:37:19.000000Z",
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-10-09T13:37:19.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [
    { "id": "01hztag001abc", "name": "loyal" }
  ],
  "badges": []
}
```

---

### `PATCH` `/2023-11/admin/customers/{customer}/{field}`
Update a single custom field stored in `metadata.custom_fields`.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |
| `field` | string | Custom field key name |

- **Request body:** Must contain a property whose key matches `{field}`.

**Request example:**

```http
PATCH /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/loyalty_points HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "loyalty_points": 500
}
```

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried@example.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {
    "custom_fields": {
      "loyalty_points": 500
    }
  },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-10-09T13:37:19.000000Z",
  "deleted_at": null,
  "created_at": "2025-10-09T13:37:12.000000Z",
  "updated_at": "2025-06-09T12:00:00.000000Z",
  "custom_fields": {
    "loyalty_points": 500
  },
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [],
  "badges": []
}
```

**Error responses:**
- `400` — field key not found in request body

---

## Contacts (Admin)

Contacts are typed contact entries (phone, email, or person) attached to a customer. They supersede the flat `phone`/`email` columns for multi-contact customers.

### `GET` `/2023-11/admin/customers/{customer}/contacts`
List all contacts for a customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Query parameters (via Spatie QueryBuilder):**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `filter[type]` | string | No | `phone`, `email`, or `person` |
| `filter[label]` | string | No | Exact match on label |
| `filter[is_primary]` | boolean | No | Filter by primary flag |
| `filter[is_verified]` | boolean | No | Filter by verification status |
| `filter[is_whatsapp]` | boolean | No | Filter WhatsApp-enabled contacts |
| `sort` | string | No | `created_at`, `type`, `label` |

**Request example:**

```http
GET /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/contacts?filter[type]=phone HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hzcontact123abc",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "contactable_type": "App\\Models\\Customer",
      "contactable_id": "01k74k19r8cc6ksb89a1fnh6md",
      "type": "phone",
      "label": "Mobile",
      "value": "+237695762595",
      "first_name": null,
      "last_name": null,
      "full_name": null,
      "position": null,
      "gender": null,
      "preferred_language": null,
      "is_primary": true,
      "is_verified": false,
      "metadata": null,
      "created_at": "2025-03-01T08:00:00.000000Z",
      "updated_at": "2025-03-01T08:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/customers/{customer}/contacts`
Add a new contact entry to a customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `type` | string | Yes | `phone`, `email`, or `person` | Contact type |
| `label` | string | No | max:255 | Human label (e.g. "Mobile", "Work") |
| `value` | string | No | max:255 | Phone number or email address |
| `first_name` | string | No | max:255 | Person first name (type=person) |
| `last_name` | string | No | max:255 | Person last name (type=person) |
| `position` | string | No | max:255 | Job title (type=person) |
| `gender` | string | No | max:50 | Gender |
| `preferred_language` | string | No | max:10 | Preferred language |
| `is_primary` | boolean | No | — | Mark as primary contact of this type |
| `is_verified` | boolean | No | — | Mark as verified |
| `is_whatsapp` | boolean | No | — | Is this a WhatsApp number |
| `metadata` | object | No | — | Arbitrary metadata |

**Request example:**

```http
POST /2023-11/admin/customers/01k74k19r8cc6ksb89a1fnh6md/contacts HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "type": "phone",
  "label": "Mobile",
  "value": "+237695762595",
  "is_primary": true,
  "is_whatsapp": true
}
```

**Success response `201 Created`:**

```json
{
  "success": true,
  "message": "Contact created successfully",
  "data": {
    "id": "01hzcontact123abc",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "contactable_type": "App\\Models\\Customer",
    "contactable_id": "01k74k19r8cc6ksb89a1fnh6md",
    "type": "phone",
    "label": "Mobile",
    "value": "+237695762595",
    "first_name": null,
    "last_name": null,
    "full_name": null,
    "position": null,
    "gender": null,
    "preferred_language": null,
    "is_primary": true,
    "is_verified": false,
    "metadata": null,
    "created_at": "2025-06-09T10:00:00.000000Z",
    "updated_at": "2025-06-09T10:00:00.000000Z"
  }
}
```

**Error responses:**
- `422` — validation failure (e.g. invalid `type`)

---

### `GET` `/2023-11/admin/customers/{customer}/contacts/{contact}`
Get a single contact belonging to a customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |
| `contact` | ULID | Contact ID |

**Success response `200 OK`:**

```json
{
  "success": true,
  "data": {
    "id": "01hzcontact123abc",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "contactable_type": "App\\Models\\Customer",
    "contactable_id": "01k74k19r8cc6ksb89a1fnh6md",
    "type": "phone",
    "label": "Mobile",
    "value": "+237695762595",
    "first_name": null,
    "last_name": null,
    "full_name": null,
    "position": null,
    "gender": null,
    "preferred_language": null,
    "is_primary": true,
    "is_verified": false,
    "metadata": null,
    "created_at": "2025-03-01T08:00:00.000000Z",
    "updated_at": "2025-03-01T08:00:00.000000Z"
  }
}
```

**Error responses:**
- `404` — contact does not belong to this customer

---

### `PUT` `/2023-11/admin/customers/{customer}/contacts/{contact}`
Update a contact. All fields are optional.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |
| `contact` | ULID | Contact ID |

- **Request body:** All fields are optional. Setting `is_primary: true` promotes this contact to primary within its type group.

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `type` | string | No | `phone`, `email`, or `person` | Contact type |
| `label` | string | No | max:255 | Human label (e.g. "Mobile", "Work") |
| `value` | string | No | max:255 | Phone number or email address |
| `first_name` | string | No | max:255 | Person first name (type=person) |
| `last_name` | string | No | max:255 | Person last name (type=person) |
| `position` | string | No | max:255 | Job title (type=person) |
| `gender` | string | No | max:50 | Gender |
| `preferred_language` | string | No | max:10 | Preferred language |
| `is_primary` | boolean | No | — | Promote to primary contact of this type |
| `is_verified` | boolean | No | — | Mark as verified |
| `is_whatsapp` | boolean | No | — | Is this a WhatsApp number |
| `metadata` | object | No | — | Arbitrary metadata |

**Success response `200 OK`:**

```json
{
  "success": true,
  "message": "Contact updated successfully",
  "data": {
    "id": "01hzcontact123abc",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "contactable_type": "App\\Models\\Customer",
    "contactable_id": "01k74k19r8cc6ksb89a1fnh6md",
    "type": "phone",
    "label": "Mobile",
    "value": "+237695762595",
    "first_name": null,
    "last_name": null,
    "full_name": null,
    "position": null,
    "gender": null,
    "preferred_language": null,
    "is_primary": true,
    "is_verified": false,
    "metadata": null,
    "created_at": "2025-03-01T08:00:00.000000Z",
    "updated_at": "2025-06-09T10:00:00.000000Z"
  }
}
```

---

### `DELETE` `/2023-11/admin/customers/{customer}/contacts/{contact}`
Delete a contact. The primary `person`-type contact cannot be deleted.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |
| `contact` | ULID | Contact ID |

**Success response `200 OK`:**

```json
{
  "success": true,
  "message": "Contact deleted successfully"
}
```

**Error responses:**
- `404` — contact does not belong to this customer
- `422` — cannot delete the primary person contact

---

### `POST` `/2023-11/admin/customers/{customer}/contacts/{contact}/set-primary`
Promote a contact to primary within its type group.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |
| `contact` | ULID | Contact ID |

- **Request body:** None. All inputs are path parameters; no body fields are consumed.

**Success response `200 OK`:**

```json
{
  "success": true,
  "message": "Contact set as primary successfully",
  "data": {
    "id": "01hzcontact123abc",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "contactable_type": "App\\Models\\Customer",
    "contactable_id": "01k74k19r8cc6ksb89a1fnh6md",
    "type": "phone",
    "label": "Mobile",
    "value": "+237695762595",
    "first_name": null,
    "last_name": null,
    "full_name": null,
    "position": null,
    "gender": null,
    "preferred_language": null,
    "is_primary": true,
    "is_verified": false,
    "metadata": null,
    "created_at": "2025-03-01T08:00:00.000000Z",
    "updated_at": "2025-06-09T10:05:00.000000Z"
  }
}
```

---

### `POST` `/2023-11/admin/customers/{customer}/contacts/bulk`
Replace or synchronise all contacts for a customer in one call.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `contacts` | array | Yes | — | Full desired contact list |
| `contacts.*.type` | string | Yes | `phone`, `email`, `person` | Contact type |
| `contacts.*.label` | string | No | max:255 | Label |
| `contacts.*.value` | string | No | max:255 | Value (phone/email) |
| (other per-contact fields) | — | No | Same as `POST /contacts` | See single-contact create |

**Success response `200 OK`:**

```json
{
  "success": true,
  "message": "Contacts updated successfully",
  "data": [
    {
      "id": "01hzcontact123abc",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "contactable_type": "App\\Models\\Customer",
      "contactable_id": "01k74k19r8cc6ksb89a1fnh6md",
      "type": "phone",
      "label": "Mobile",
      "value": "+237695762595",
      "first_name": null,
      "last_name": null,
      "full_name": null,
      "position": null,
      "gender": null,
      "preferred_language": null,
      "is_primary": true,
      "is_verified": false,
      "metadata": null,
      "created_at": "2025-03-01T08:00:00.000000Z",
      "updated_at": "2025-06-09T10:00:00.000000Z"
    }
  ]
}
```

---

## Addresses (Admin)

Admin address endpoints are polymorphic: they work for customers, orders, suppliers, etc. The `model_type` and `model_id` query parameters identify the owner.

**Supported `model_type` values:** `customer`, `company`, `shop`, `pickupLocation`, `warehouse`, `supplier`, `order`, `bill`, `payment`, `delivery`

### `GET` `/2023-11/admin/addresses`
List all addresses belonging to a model instance.

- **Query parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `model_type` | string | Yes | One of the supported model type keys |
| `model_id` | ULID | Yes | ID of the owner |

**Request example:**

```http
GET /2023-11/admin/addresses?model_type=customer&model_id=01hhfzv31ebp2cdyhymxzrkw8w HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hhg5988jrd441hx0yerftzkt",
      "label": "Default address",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "company": "La Mater, inc.",
      "phone": null,
      "email": null,
      "line1": "9 Rue Sainte-Honorine",
      "line2": null,
      "city": "Montesson",
      "state": null,
      "country": "France",
      "postal_code": "78360",
      "latitude": null,
      "longitude": null,
      "is_primary": true,
      "is_billing": true,
      "is_shipping": true,
      "metadata": {},
      "addressable_type": "App\\Models\\Customer",
      "addressable_id": "01hhfzv31ebp2cdyhymxzrkw8w",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z"
    }
  ]
}
```

**Error responses:**
- `400` — missing or invalid `model_type` / `model_id`
- `404` — owner model not found

---

### `POST` `/2023-11/admin/addresses`
Create an address and attach it to a model.

- **Query parameters:** `model_type` and `model_id` (same as `GET`)

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `first_name` | string | No | Recipient first name |
| `last_name` | string | No | Recipient last name |
| `company` | string | No | Company name on the address |
| `line1` | string | No | Street line 1 |
| `line2` | string | No | Street line 2 |
| `city` | string | No | City |
| `state` | string | No | State / region |
| `country` | string | No | Country name |
| `postal_code` | string | No | Postal / ZIP code |
| `phone` | string | No | Phone for this address |
| `email` | string | No | Email for this address |
| `latitude` | numeric | No | GPS latitude |
| `longitude` | numeric | No | GPS longitude |
| `is_primary` | boolean | No | Mark as primary address |
| `is_billing` | boolean | No | Mark as billing address |
| `is_shipping` | boolean | No | Mark as shipping address |
| `label` | string | No | Human label (e.g. "Home") |

**Request example:**

```http
POST /2023-11/admin/addresses?model_type=customer&model_id=01hhfzv31ebp2cdyhymxzrkw8w HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company": "La Mater, inc.",
  "line1": "9 Rue Sainte-Honorine",
  "city": "Montesson",
  "postal_code": "78360",
  "country": "France",
  "is_primary": true,
  "is_billing": true,
  "is_shipping": true
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hhg5988jrd441hx0yerftzkt",
  "label": "Default address",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company": "La Mater, inc.",
  "phone": null,
  "email": null,
  "line1": "9 Rue Sainte-Honorine",
  "line2": null,
  "city": "Montesson",
  "state": null,
  "country": "France",
  "postal_code": "78360",
  "latitude": null,
  "longitude": null,
  "is_primary": true,
  "is_billing": true,
  "is_shipping": true,
  "metadata": {},
  "addressable_type": "App\\Models\\Customer",
  "addressable_id": "01hhfzv31ebp2cdyhymxzrkw8w",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z"
}
```

---

### `GET` `/2023-11/admin/addresses/{address}`
Get a single address by ID.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `address` | ULID | Address ID |

**Success response `200 OK`:**

```json
{
  "id": "01hhg5988jrd441hx0yerftzkt",
  "label": "Default address",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company": "La Mater, inc.",
  "phone": null,
  "email": null,
  "line1": "9 Rue Sainte-Honorine",
  "line2": null,
  "city": "Montesson",
  "state": null,
  "country": "France",
  "postal_code": "78360",
  "latitude": null,
  "longitude": null,
  "is_primary": true,
  "is_billing": true,
  "is_shipping": true,
  "metadata": {},
  "addressable_type": "App\\Models\\Customer",
  "addressable_id": "01hhfzv31ebp2cdyhymxzrkw8w",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z"
}
```

---

### `PUT` `/2023-11/admin/addresses/{address}`
Update an address. Requires `model_type` and `model_id` query params to verify ownership.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `address` | ULID | Address ID |

- **Query parameters:** `model_type`, `model_id`

- **Request body:** All fields are optional.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `first_name` | string | No | Recipient first name |
| `last_name` | string | No | Recipient last name |
| `company` | string | No | Company name on the address |
| `line1` | string | No | Street line 1 |
| `line2` | string | No | Street line 2 |
| `city` | string | No | City |
| `state` | string | No | State / region |
| `country` | string | No | Country name |
| `postal_code` | string | No | Postal / ZIP code |
| `phone` | string | No | Phone for this address |
| `email` | string | No | Email for this address |
| `latitude` | numeric | No | GPS latitude |
| `longitude` | numeric | No | GPS longitude |
| `is_primary` | boolean | No | Mark as primary address |
| `is_billing` | boolean | No | Mark as billing address |
| `is_shipping` | boolean | No | Mark as shipping address |
| `label` | string | No | Human label (e.g. "Home") |

**Request example:**

```http
PUT /2023-11/admin/addresses/01hhg5988jrd441hx0yerftzkt?model_type=customer&model_id=01hhfzv31ebp2cdyhymxzrkw8w HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "label": "Default address",
  "country": "France",
  "is_primary": false,
  "is_shipping": true
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hhg5988jrd441hx0yerftzkt",
  "label": "Default address",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company": "La Mater, inc.",
  "phone": null,
  "email": null,
  "line1": "9 Rue Sainte-Honorine",
  "line2": null,
  "city": "Montesson",
  "state": null,
  "country": "France",
  "postal_code": "78360",
  "latitude": null,
  "longitude": null,
  "is_primary": false,
  "is_billing": true,
  "is_shipping": true,
  "metadata": {},
  "addressable_type": "App\\Models\\Customer",
  "addressable_id": "01hhfzv31ebp2cdyhymxzrkw8w",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

**Error responses:**
- `400` — address does not belong to the specified model

---

### `DELETE` `/2023-11/admin/addresses/{address}`
Delete an address. Requires `model_type` and `model_id` query params to verify ownership.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `address` | ULID | Address ID |

- **Query parameters:** `model_type`, `model_id`

**Success response `200 OK`:**

```json
{
  "message": "Address deleted."
}
```

**Error responses:**
- `400` — address does not belong to the specified model

---

## Customer Notes (Admin)

Notes are free-text CRM annotations (with optional media attachments) written by staff on a customer record.

### `GET` `/2023-11/admin/customer-notes`
List all CRM notes, with optional filtering.

- **Query parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `filter[search]` | string | No | Full-text search in `content` |
| `filter[customer_id]` | ULID | No | Filter notes for a specific customer |
| `filter[user_id]` | ULID | No | Filter notes by authoring user |
| `filter[category_id]` | ULID | No | Filter by note category |
| `include` | string | No | Comma-separated: `customer`, `user`, `media`, `category` |
| `sort` | string | No | `created_at` (default desc), `content` |

**Request example:**

```http
GET /2023-11/admin/customer-notes?filter[customer_id]=01k85q84qg8xn3jn7ys04pkzyq&include=user,category HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hznote123abc",
      "customer_id": "01k85q84qg8xn3jn7ys04pkzyq",
      "customer": null,
      "user_id": "01jp7wkfk0gp8w9zb9jts2hdty",
      "user": { "id": "01jp7wkfk0gp8w9zb9jts2hdty", "name": "Admin User" },
      "category_id": null,
      "category": null,
      "content": "Customer called about order delay.",
      "metadata": null,
      "medias": [],
      "created_at": "2025-06-09T10:00:00.000000Z",
      "updated_at": "2025-06-09T10:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/customer-notes`
Create a CRM note.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `content` | string | Yes | — | Note text |
| `customer_id` | ULID | Yes | Must exist in `customers` | Customer this note belongs to |
| `user_id` | ULID | No | Must exist in `users` | Override authoring user (defaults to authenticated user) |
| `category_id` | ULID | No | Must exist in `customer_note_categories` | Note category |
| `metadata` | object | No | — | Arbitrary metadata |
| `medias` | array | No | — | Media attachments |
| `medias.*.id` | integer | No | — | ID of existing media record |
| `medias.*.s3_key` | string | No | — | S3 object key |
| `medias.*.file_name` | string | No | max:255 | File name |
| `medias.*.collection_name` | string | No | — | Media collection (defaults to `default`) |
| `medias.*.disk` | string | No | `s3_private` or `s3` | Storage disk |

**Request example:**

```http
POST /2023-11/admin/customer-notes HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "content": "Customer requested a callback.",
  "customer_id": "01k85q84qg8xn3jn7ys04pkzyq",
  "category_id": null,
  "medias": [
    {
      "s3_key": "notes/attachments/file.pdf",
      "file_name": "file.pdf",
      "collection_name": "default",
      "disk": "s3_private"
    }
  ]
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hznote123abc",
  "customer_id": "01k85q84qg8xn3jn7ys04pkzyq",
  "customer": null,
  "user_id": "01jp7wkfk0gp8w9zb9jts2hdty",
  "user": { "id": "01jp7wkfk0gp8w9zb9jts2hdty", "name": "Admin User" },
  "category_id": null,
  "category": null,
  "content": "Customer requested a callback.",
  "metadata": null,
  "medias": [],
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

**Error responses:**
- `422` — `customer_id` does not exist or `content` missing

---

### `GET` `/2023-11/admin/customer-notes/{customerNote}`
Get a single CRM note.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customerNote` | ULID | Note ID |

**Success response `200 OK`:**

```json
{
  "id": "01hznote123abc",
  "customer_id": "01k85q84qg8xn3jn7ys04pkzyq",
  "customer": null,
  "user_id": "01jp7wkfk0gp8w9zb9jts2hdty",
  "user": { "id": "01jp7wkfk0gp8w9zb9jts2hdty", "name": "Admin User" },
  "category_id": "01hzcat001abc",
  "category": {
    "id": "01hzcat001abc",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "label": "Support",
    "color": "#3B82F6",
    "notes_count": 12,
    "created_at": "2025-01-01T00:00:00.000000Z",
    "updated_at": "2025-01-01T00:00:00.000000Z"
  },
  "content": "Customer called about order delay.",
  "metadata": null,
  "medias": [],
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/customer-notes/{customerNote}`
Update a CRM note.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customerNote` | ULID | Note ID |

- **Request body:** All fields optional (`sometimes`).

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `content` | string | No | — | Updated note text |
| `user_id` | ULID | No | Must exist in `users` | Reassign author |
| `category_id` | ULID | No | Must exist in `customer_note_categories` | Update category |
| `metadata` | object | No | — | Replace metadata |
| `medias` | array | No | — | Replace media attachments (same shape as create) |

**Success response `200 OK`:**

```json
{
  "id": "01hznote123abc",
  "customer_id": "01k85q84qg8xn3jn7ys04pkzyq",
  "customer": null,
  "user_id": "01jp7wkfk0gp8w9zb9jts2hdty",
  "user": { "id": "01jp7wkfk0gp8w9zb9jts2hdty", "name": "Admin User" },
  "category_id": null,
  "category": null,
  "content": "Customer called about order delay — follow up scheduled.",
  "metadata": null,
  "medias": [],
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T11:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/customer-notes/{customerNote}`
Delete a CRM note.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customerNote` | ULID | Note ID |

**Response:** `204 No Content` — empty body.

---

### `POST` `/2023-11/admin/customers/{customer}/notes`
Create a note scoped directly to a customer (shorthand for `POST /admin/customer-notes` with the customer ID pre-filled).

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID (auto-filled into `customer_id`) |

- **Request body:** `customer_id` is automatically set from the path parameter and does not need to be sent.

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `content` | string | Yes | — | Note text |
| `user_id` | ULID | No | Must exist in `users` | Override authoring user (defaults to authenticated user) |
| `category_id` | ULID | No | Must exist in `customer_note_categories` | Note category |
| `metadata` | object | No | — | Arbitrary metadata |
| `medias` | array | No | — | Media attachments |
| `medias.*.id` | integer | No | — | ID of existing media record |
| `medias.*.s3_key` | string | No | — | S3 object key |
| `medias.*.file_name` | string | No | max:255 | File name |
| `medias.*.collection_name` | string | No | — | Media collection (defaults to `default`) |
| `medias.*.disk` | string | No | `s3_private` or `s3` | Storage disk |
| `medias.*.order_column` | integer | No | — | Sort order within the collection |

**Success response `200 OK`:**

```json
{
  "id": "01hznote456def",
  "customer_id": "01k74k19r8cc6ksb89a1fnh6md",
  "customer": null,
  "user_id": "01jp7wkfk0gp8w9zb9jts2hdty",
  "user": { "id": "01jp7wkfk0gp8w9zb9jts2hdty", "name": "Admin User" },
  "category_id": null,
  "category": null,
  "content": "Customer requested a callback.",
  "metadata": null,
  "medias": [],
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

---

### `PATCH` `/2023-11/admin/customers/{customer}/notes/{customerNote}`
Update a note that belongs to a specific customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customer` | ULID | Customer ID (ownership verified) |
| `customerNote` | ULID | Note ID |

- **Request body:** All fields are optional.

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `content` | string | No | — | Updated note text |
| `user_id` | ULID | No | Must exist in `users` | Reassign author |
| `category_id` | ULID | No | Must exist in `customer_note_categories` | Update category |
| `metadata` | object | No | — | Replace metadata |
| `medias` | array | No | — | Replace media attachments |
| `medias.*.id` | integer | No | — | ID of existing media record |
| `medias.*.s3_key` | string | No | — | S3 object key |
| `medias.*.file_name` | string | No | max:255 | File name |
| `medias.*.collection_name` | string | No | — | Media collection (defaults to `default`) |
| `medias.*.disk` | string | No | `s3_private` or `s3` | Storage disk |
| `medias.*.order_column` | integer | No | — | Sort order within the collection |

**Success response `200 OK`:**

```json
{
  "id": "01hznote456def",
  "customer_id": "01k74k19r8cc6ksb89a1fnh6md",
  "customer": null,
  "user_id": "01jp7wkfk0gp8w9zb9jts2hdty",
  "user": { "id": "01jp7wkfk0gp8w9zb9jts2hdty", "name": "Admin User" },
  "category_id": null,
  "category": null,
  "content": "Customer called about order delay — follow up scheduled.",
  "metadata": null,
  "medias": [],
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T11:30:00.000000Z"
}
```

**Error responses:**
- `404` — note does not belong to this customer

---

## Customer Note Categories (Admin)

### `GET` `/2023-11/admin/customer-note-categories`
List all note categories for the current company.

- **Query parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `filter[label]` | string | No | Partial match on label |
| `filter[search]` | string | No | Full-text search on `label` |
| `sort` | string | No | `label` (default), `created_at`, `updated_at` |

**Request example:**

```http
GET /2023-11/admin/customer-note-categories HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hzcat001abc",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "label": "Support",
      "color": "#3B82F6",
      "notes_count": 12,
      "created_at": "2025-01-01T00:00:00.000000Z",
      "updated_at": "2025-01-01T00:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/customer-note-categories`
Create a note category.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `label` | string | Yes | max:255; unique within company | Category label |
| `color` | string | No | max:32 | Hex color code (e.g. `#3B82F6`) |

**Request example:**

```http
POST /2023-11/admin/customer-note-categories HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "label": "Feedback",
  "color": "#10B981"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hzcat002xyz",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "label": "Feedback",
  "color": "#10B981",
  "notes_count": 0,
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

**Error responses:**
- `422` — label already used within this company

---

### `GET` `/2023-11/admin/customer-note-categories/{customerNoteCategory}`
Get a single category.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customerNoteCategory` | ULID | Category ID |

**Success response `200 OK`:**

```json
{
  "id": "01hzcat001abc",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "label": "Support",
  "color": "#3B82F6",
  "notes_count": 12,
  "created_at": "2025-01-01T00:00:00.000000Z",
  "updated_at": "2025-01-01T00:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/customer-note-categories/{customerNoteCategory}`
Update a note category.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customerNoteCategory` | ULID | Category ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `label` | string | No | max:255; unique within company (ignoring self) | Updated label |
| `color` | string | No | max:32 | Updated hex color |

**Success response `200 OK`:**

```json
{
  "id": "01hzcat001abc",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "label": "After-Sales",
  "color": "#6366F1",
  "notes_count": 12,
  "created_at": "2025-01-01T00:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/customer-note-categories/{customerNoteCategory}`
Delete a note category.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `customerNoteCategory` | ULID | Category ID |

**Response:** `204 No Content` — empty body.

---

## Customer Self-Service (Storefront)

The following endpoints are used by the storefront / customer portal. Authentication uses a customer bearer token issued at login or registration. The `X-Company` header is still required.

### `POST` `/2023-11/customers/register`
Register a new customer account.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `first_name` | string | Conditionally | Required unless `last_name` present | First name |
| `last_name` | string | Conditionally | Required unless `first_name` present | Last name |
| `email` | string | Conditionally | Required unless `phone`; unique within company | Email |
| `phone` | string | Conditionally | Required unless `email`; unique within company | Phone |
| `password` | string | Yes | — | Plain-text password |
| `gender` | string | No | `M`, `F`, or `other` | Gender |
| `company_id` | ULID | No | — | Tenant company (can also be passed via `X-Company`) |

**Request example:**

```http
POST /2023-11/customers/register HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "customer@example.com",
  "password": "s3cr3tP@ss"
}
```

**Success response `200 OK`:**

```json
{
  "token": "eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiIwMWs3NGs...",
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "customer@example.com",
    "phone": null,
    "gender": null,
    "birthdate": null,
    "type": "individual",
    "company_name": null,
    "registration_number": null,
    "tax_number": null,
    "preferred_language": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "last_activity": null,
    "deleted_at": null,
    "created_at": "2025-06-09T10:00:00.000000Z",
    "updated_at": "2025-06-09T10:00:00.000000Z",
    "custom_fields": [],
    "default_address": null,
    "addresses": [],
    "contacts": [],
    "medias": [],
    "notes": [],
    "tags": [],
    "badges": []
  }
}
```

**Error responses:**
- `400` — validation failure or duplicate email/phone

---

### `POST` `/2023-11/customers/login`
Authenticate a customer and return an access token.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `email` | string | Conditionally | Required unless `phone` | Customer email |
| `phone` | string | Conditionally | Required unless `email` | Customer phone |
| `password` | string | Yes | — | Customer password |

**Request example:**

```http
POST /2023-11/customers/login HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "email": "wilfried.djopa@gmail.com",
  "password": "password"
}
```

**Success response `200 OK`:**

```json
{
  "token": "eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiIwMWs3NGs...",
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "wilfried.djopa@gmail.com",
    "phone": "+237695762595",
    "gender": "M",
    "birthdate": null,
    "type": "individual",
    "company_name": null,
    "registration_number": null,
    "tax_number": null,
    "preferred_language": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "last_activity": "2025-06-01T10:00:00.000000Z",
    "deleted_at": null,
    "created_at": "2025-01-01T08:00:00.000000Z",
    "updated_at": "2025-06-01T10:00:00.000000Z",
    "custom_fields": [],
    "default_address": null,
    "addresses": [],
    "contacts": [],
    "medias": [],
    "notes": [],
    "tags": [],
    "badges": []
  }
}
```

**Error responses:**
- `400` / `401` — invalid credentials

---

### `POST` `/2023-11/customers/logout`
Revoke the current customer access token.

Requires: `Authorization: Bearer <customer-token>`

- **Request body:** None. The token to revoke is identified from the `Authorization` header; no body fields are consumed.

**Request example:**

```http
POST /2023-11/customers/logout HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <customer-token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "message": "Successfully logged out"
}
```

---

### `POST` `/2023-11/customers/password/reset-link`
Generate a password reset token and return it directly (no email is sent). Useful for custom storefront flows that handle delivery themselves.

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | string | Yes | Customer email |

**Request example:**

```http
POST /2023-11/customers/password/reset-link HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "email": "wilfried.djopa@gmail.com"
}
```

**Success response `200 OK`:**

```json
{
  "token": "LaxutBdr9rzpGLV3hLHYaTROQZ9Bqzom78Nm75dnQC7OO4Ie2IWsUAT0EBWM"
}
```

**Error responses:**
- `404` — email not found

---

### `POST` `/2023-11/customers/password/forgot`
Send a password reset link email (standard Laravel broker flow).

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | string | Yes | Customer email |

**Success response `200 OK`:**

```json
{
  "message": "We have emailed your password reset link!"
}
```

---

### `POST` `/2023-11/customers/password/reset`
Reset a customer password using a token received via email or `reset-link`.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `email` | string | Yes | — | Customer email |
| `token` | string | Yes | — | Reset token |
| `password` | string | Yes | min:8; confirmed | New password |
| `password_confirmation` | string | Yes | — | Must match `password` |

**Request example:**

```http
POST /2023-11/customers/password/reset HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "email": "wilfried.djopa@gmail.com",
  "token": "LaxutBdr9rzpGLV3hLHYaTROQZ9Bqzom78Nm75dnQC7OO4Ie2IWsUAT0EBWM",
  "password": "newP@ssw0rd",
  "password_confirmation": "newP@ssw0rd"
}
```

**Success response `200 OK`:**

```json
{
  "message": "Your password has been reset!",
  "status": "passwords.reset"
}
```

**Error responses:**
- `400` — invalid or expired token

---

### `GET` `/2023-11/customers/profile`
Get the authenticated customer's profile (with associated company).

Requires: `Authorization: Bearer <customer-token>`

**Request example:**

```http
GET /2023-11/customers/profile HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <customer-token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried.djopa@gmail.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-06-01T10:00:00.000000Z",
  "deleted_at": null,
  "created_at": "2025-01-01T08:00:00.000000Z",
  "updated_at": "2025-06-01T10:00:00.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [],
  "badges": []
}
```

---

### `PUT` `/2023-11/customers/profile`
Update the authenticated customer's profile.

Requires: `Authorization: Bearer <customer-token>`

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `first_name` | string | Conditionally | Required unless `last_name` | First name |
| `last_name` | string | Conditionally | Required unless `first_name` | Last name |
| `email` | string | Conditionally | Required unless `phone`; unique | Email |
| `phone` | string | Conditionally | Required unless `email`; unique | Phone |
| `gender` | string | No | `M`, `F`, or `other` | Gender |
| `metadata` | object | No | — | Arbitrary metadata |

**Success response `200 OK`:**

```json
{
  "id": "01k74k19r8cc6ksb89a1fnh6md",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wilfried.djopa@gmail.com",
  "phone": "+237695762595",
  "gender": "M",
  "birthdate": null,
  "type": "individual",
  "company_name": null,
  "registration_number": null,
  "tax_number": null,
  "preferred_language": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "last_activity": "2025-06-01T10:00:00.000000Z",
  "deleted_at": null,
  "created_at": "2025-01-01T08:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z",
  "custom_fields": [],
  "default_address": null,
  "addresses": [],
  "contacts": [],
  "medias": [],
  "notes": [],
  "tags": [],
  "badges": []
}
```

---

### `PUT` `/2023-11/customers/profile/password`
Change the authenticated customer's password.

Requires: `Authorization: Bearer <customer-token>`

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `current_password` | string | Yes | Existing password (must be correct) |
| `new_password` | string | Yes | New password |

**Request example:**

```http
PUT /2023-11/customers/profile/password HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <customer-token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "current_password": "oldPassword",
  "new_password": "newS3cr3t"
}
```

**Success response `200 OK`:**

```json
{
  "message": "Password updated"
}
```

**Error responses:**
- `400` — current password incorrect

---

## Customer Addresses (Storefront)

These endpoints operate on the authenticated customer's own addresses. No `model_type` / `model_id` params are needed.

### `GET` `/2023-11/customers/addresses`
List the authenticated customer's addresses (paginated).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `per_page` | integer | No | 15 | Page size |
| `sort` | string | No | `line1` | Sort field |
| `filter[search]` | string | No | — | Search across name, phone, email, address fields |

**Request example:**

```http
GET /2023-11/customers/addresses HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <customer-token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hhg5988jrd441hx0yerftzkt",
      "label": "Home",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "company": null,
      "phone": "+237695762595",
      "email": null,
      "line1": "9 Rue Sainte-Honorine",
      "line2": null,
      "city": "Montesson",
      "state": null,
      "country": "France",
      "postal_code": "78360",
      "latitude": null,
      "longitude": null,
      "is_primary": true,
      "is_billing": true,
      "is_shipping": true,
      "metadata": null,
      "addressable_type": "App\\Models\\Customer",
      "addressable_id": "01k74k19r8cc6ksb89a1fnh6md",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z"
    }
  ],
  "links": {
    "first": "https://api.genuka.com/2023-11/customers/addresses?page=1",
    "last": "https://api.genuka.com/2023-11/customers/addresses?page=1",
    "prev": null,
    "next": null
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 1,
    "per_page": 15,
    "to": 1,
    "total": 1
  }
}
```

---

### `POST` `/2023-11/customers/addresses`
Create an address for the authenticated customer.

- **Request body:** No `model_type` or `model_id` parameters are needed — the address is automatically scoped to the authenticated customer.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `label` | string | No | Human label (e.g. "Home") |
| `first_name` | string | No | Recipient first name |
| `last_name` | string | No | Recipient last name |
| `phone` | string | No | Phone for this address |
| `email` | string | No | Email for this address |
| `company` | string | No | Company name on the address |
| `line1` | string | No | Street line 1 |
| `line2` | string | No | Street line 2 |
| `city` | string | No | City |
| `state` | string | No | State / region |
| `country` | string | No | Country name |
| `postal_code` | string | No | Postal / ZIP code |
| `latitude` | numeric | No | GPS latitude |
| `longitude` | numeric | No | GPS longitude |
| `is_primary` | boolean | No | Mark as primary address |
| `is_billing` | boolean | No | Mark as billing address |
| `is_shipping` | boolean | No | Mark as shipping address |
| `metadata` | object | No | Arbitrary metadata |

**Request example:**

```http
POST /2023-11/customers/addresses HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <customer-token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "label": "Home",
  "first_name": "Sorel",
  "last_name": "Djopa",
  "phone": "+237677338495",
  "email": "sorel.djopa@example.com",
  "company": "La Mater SARL",
  "line1": "Ekorezok",
  "line2": "Pres du lycee bilingue",
  "city": "Yaounde",
  "country": "Cameroun",
  "postal_code": "99999",
  "is_primary": true,
  "is_shipping": true,
  "is_billing": false
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hjyfgedeypz6d53n21d39rqt",
  "label": "Home",
  "first_name": "Sorel",
  "last_name": "Djopa",
  "company": "La Mater SARL",
  "phone": "+237677338495",
  "email": "sorel.djopa@example.com",
  "line1": "Ekorezok",
  "line2": "Pres du lycee bilingue",
  "city": "Yaounde",
  "state": null,
  "country": "Cameroun",
  "postal_code": "99999",
  "latitude": null,
  "longitude": null,
  "is_primary": true,
  "is_billing": false,
  "is_shipping": true,
  "metadata": null,
  "addressable_type": "App\\Models\\Customer",
  "addressable_id": "01k74k19r8cc6ksb89a1fnh6md",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

---

### `GET` `/2023-11/customers/addresses/{address}`
Get a specific address belonging to the authenticated customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `address` | ULID | Address ID |

**Success response `200 OK`:**

```json
{
  "id": "01hjyfgedeypz6d53n21d39rqt",
  "label": "Home",
  "first_name": "Sorel",
  "last_name": "Djopa",
  "company": "La Mater SARL",
  "phone": "+237677338495",
  "email": "sorel.djopa@example.com",
  "line1": "Ekorezok",
  "line2": "Pres du lycee bilingue",
  "city": "Yaounde",
  "state": null,
  "country": "Cameroun",
  "postal_code": "99999",
  "latitude": null,
  "longitude": null,
  "is_primary": true,
  "is_billing": false,
  "is_shipping": true,
  "metadata": null,
  "addressable_type": "App\\Models\\Customer",
  "addressable_id": "01k74k19r8cc6ksb89a1fnh6md",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/customers/addresses/{address}`
Update an address belonging to the authenticated customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `address` | ULID | Address ID |

- **Request body:** All fields are optional; omitted fields keep their current value.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `label` | string | No | Human label (e.g. "Home") |
| `first_name` | string | No | Recipient first name |
| `last_name` | string | No | Recipient last name |
| `phone` | string | No | Phone for this address |
| `email` | string | No | Email for this address |
| `company` | string | No | Company name on the address |
| `line1` | string | No | Street line 1 |
| `line2` | string | No | Street line 2 |
| `city` | string | No | City |
| `state` | string | No | State / region |
| `country` | string | No | Country name |
| `postal_code` | string | No | Postal / ZIP code |
| `latitude` | numeric | No | GPS latitude |
| `longitude` | numeric | No | GPS longitude |
| `is_primary` | boolean | No | Mark as primary address |
| `is_billing` | boolean | No | Mark as billing address |
| `is_shipping` | boolean | No | Mark as shipping address |
| `metadata` | object | No | Merged into existing metadata |

**Success response `200 OK`:**

```json
{
  "id": "01hjyfgedeypz6d53n21d39rqt",
  "label": "Home",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company": null,
  "phone": null,
  "email": null,
  "line1": "72 Rue de Brement",
  "line2": null,
  "city": "Noisy-le-Sec",
  "state": null,
  "country": "France",
  "postal_code": "93130",
  "latitude": null,
  "longitude": null,
  "is_primary": true,
  "is_billing": false,
  "is_shipping": true,
  "metadata": null,
  "addressable_type": "App\\Models\\Customer",
  "addressable_id": "01k74k19r8cc6ksb89a1fnh6md",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T11:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/customers/addresses/{address}`
Delete an address belonging to the authenticated customer.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `address` | ULID | Address ID |

**Success response `200 OK`:**

```json
{
  "message": "Address deleted"
}
```

---

## Customer Orders (Storefront)

### `POST` `/2023-11/customers/orders`
Place a new order (public endpoint — also works without authentication for guest checkout).

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `shop_id` | ULID | No | Storefront shop |
| `company_id` | ULID | Yes | Tenant company |
| `customer` | object | Yes | Customer info (or existing customer `id`) |
| `customer.id` | ULID | No | Existing customer ID (use instead of creating new) |
| `customer.first_name` | string | No | First name |
| `customer.last_name` | string | No | Last name |
| `customer.email` | string | No | Email |
| `customer.phone` | string | No | Phone |
| `customer.force` | boolean | No | If `true`, reuse existing customer instead of erroring on duplicate |
| `products` | array | Yes | Line items |
| `products.*.id` | ULID | No | Existing product/variant ID |
| `products.*.title` | string | No | Product title (for custom items) |
| `products.*.price` | numeric | Yes | Unit price |
| `products.*.quantity` | integer | Yes | Quantity |
| `source` | string | No | Order source (e.g. `WHATSAPP`, `WEB`) |
| `billing` | object | No | Billing config |
| `billing.method` | string | No | Payment method / treasury account type |
| `shipping` | object | No | Shipping config |
| `shipping.mode` | string | No | `delivery` or `pickup` |
| `shipping.amount` | numeric | No | Shipping fee amount |
| `shipping.address_id` | ULID | No | Existing shipping address ID |
| `success_url` | string | No | Redirect URL after successful payment |
| `cancel_url` | string | No | Redirect URL after payment cancellation |
| `authenticate` | boolean | No | Issue a customer token alongside the order response |
| `deferred_payment` | boolean | No | Skip immediate payment link generation |

**Request example:**

```http
POST /2023-11/customers/orders HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "shop_id": "01hphmgyze0etams9dx505f9nh",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "customer": {
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "wdjopa@example.com",
    "phone": "+237695762595"
  },
  "source": "WEB",
  "billing": {"method": "notchpay"},
  "shipping": {"mode": "delivery", "amount": 1500.00},
  "products": [{"title": "Product A", "price": 5000.00, "quantity": 2}]
}
```

**Success response `200 OK`:** `OrderDetailResource` with additional fields:

```json
{
  "id": "01hzorder123abc",
  "reference": "ORD-2025-001",
  "amount": 10000.00,
  "amount_due": 10000.00,
  "payment_link": "https://pay.example.com/session/abc",
  "token": "<customer-token>",
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "wdjopa@example.com",
    "phone": "+237695762595",
    "gender": "M",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "created_at": "2025-01-01T08:00:00.000000Z",
    "updated_at": "2025-06-09T10:00:00.000000Z"
  }
}
```

(`token` and `customer` only present when `authenticate: true` is sent)

---

### `GET` `/2023-11/customers/orders`
List orders for the authenticated customer (paginated).

- **Query parameters:** Standard pagination (`page`, `per_page`). Supports `filter[reference]`, `filter[billing->status]`, `filter[shipping->status]`, `include`, and `sort`.

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hzorder123abc",
      "number": "ORD-2025-001",
      "reference": "ORD-2025-001",
      "state": "invoice",
      "amount": 11500.0,
      "amount_due": 11500.0,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "customer_id": "01k74k19r8cc6ksb89a1fnh6md",
      "shop_id": "01hphmgyze0etams9dx505f9nh",
      "source": "WEB",
      "billing": {
        "method": "notchpay",
        "status": "pending",
        "address_id": null,
        "address": null,
        "treasury_account": null
      },
      "shipping": {
        "mode": "delivery",
        "amount": 1500.0,
        "status": "pending",
        "address_id": null,
        "address": null
      },
      "metadata": {},
      "expires_at": null,
      "deleted_at": null,
      "created_at": "2025-06-09T10:00:00.000000Z",
      "updated_at": "2025-06-09T10:00:00.000000Z",
      "products": [],
      "customer": null,
      "payments": [],
      "custom_fields": [],
      "adjustments": [],
      "user": null,
      "subscriptions": [],
      "taxes": [],
      "discounts": [],
      "company": null,
      "delivery": null,
      "deliveries": [],
      "invoices": [],
      "invoices_count": 0,
      "returns": [],
      "returns_count": 0,
      "credit_notes": [],
      "addresses": [],
      "shipping_fee": null,
      "pickup_location": null,
      "medias": [],
      "private_medias": [],
      "shop": null,
      "signature": null,
      "bills": []
    }
  ],
  "links": {
    "first": "https://api.genuka.com/2023-11/customers/orders?page=1",
    "last": "https://api.genuka.com/2023-11/customers/orders?page=1",
    "prev": null,
    "next": null
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 1,
    "per_page": 15,
    "to": 1,
    "total": 1
  }
}
```

---

### `GET` `/2023-11/customers/orders/{orderId}`
Get a single order by ID (public — no auth required if order ID is known).

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `orderId` | ULID | Order ID |

**Success response `200 OK`:**

```json
{
  "id": "01hzorder123abc",
  "number": "ORD-2025-001",
  "reference": "ORD-2025-001",
  "state": "invoice",
  "amount": 11500.0,
  "amount_due": 11500.0,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "customer_id": "01k74k19r8cc6ksb89a1fnh6md",
  "shop_id": "01hphmgyze0etams9dx505f9nh",
  "source": "WEB",
  "billing": {
    "method": "notchpay",
    "status": "pending",
    "address_id": null,
    "address": null,
    "treasury_account": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 1500.0,
    "status": "pending",
    "address_id": null,
    "address": null
  },
  "metadata": {},
  "expires_at": null,
  "deleted_at": null,
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z",
  "products": [
    {
      "id": "01hkb1j1z86twgmy3f2cn6aafh",
      "title": "Product A",
      "price": 10000.0,
      "quantity": 1
    }
  ],
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "wilfried@example.com",
    "phone": "+237695762595"
  },
  "payments": [],
  "custom_fields": [],
  "adjustments": [],
  "user": null,
  "subscriptions": [],
  "taxes": [],
  "discounts": [],
  "company": null,
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "addresses": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "shop": null,
  "signature": null,
  "bills": [],
  "amount_due": 11500.0
}
```

---

### `PUT` `/2023-11/customers/orders/{order}`
Update an existing order (re-runs the upsert process with the given order ID).

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `order` | ULID | Order ID |

- **Request body:** Same shape as `POST /customers/orders`; all fields are optional for updates.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `shop_id` | ULID | No | Storefront shop |
| `company_id` | ULID | No | Tenant company |
| `customer` | object | No | Customer info or existing customer `id` |
| `customer.id` | ULID | No | Existing customer ID |
| `customer.first_name` | string | No | First name |
| `customer.last_name` | string | No | Last name |
| `customer.email` | string | No | Email |
| `customer.phone` | string | No | Phone |
| `customer.force` | boolean | No | Reuse existing customer on duplicate match |
| `products` | array | No | Line items to replace |
| `products.*.id` | ULID | No | Existing product/variant ID |
| `products.*.title` | string | No | Product title (for custom line items) |
| `products.*.price` | numeric | No | Unit price |
| `products.*.quantity` | integer | No | Quantity |
| `source` | string | No | Order source (e.g. `WHATSAPP`, `WEB`) |
| `billing` | object | No | Billing config |
| `billing.method` | string | No | Payment method / treasury account type |
| `shipping` | object | No | Shipping config |
| `shipping.mode` | string | No | `delivery` or `pickup` |
| `shipping.amount` | numeric | No | Shipping fee amount |
| `shipping.address_id` | ULID | No | Existing shipping address ID |
| `success_url` | string | No | Redirect URL after successful payment |
| `cancel_url` | string | No | Redirect URL after payment cancellation |
| `deferred_payment` | boolean | No | Skip payment link generation |

**Success response `200 OK`:**

```json
{
  "id": "01hzorder123abc",
  "number": "ORD-2025-001",
  "reference": "ORD-2025-001",
  "state": "invoice",
  "amount": 11500.0,
  "amount_due": 11500.0,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "customer_id": "01k74k19r8cc6ksb89a1fnh6md",
  "shop_id": "01hphmgyze0etams9dx505f9nh",
  "source": "WEB",
  "billing": {
    "method": "notchpay",
    "status": "pending",
    "address_id": null,
    "address": null,
    "treasury_account": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 1500.0,
    "status": "pending",
    "address_id": null,
    "address": null
  },
  "metadata": {},
  "expires_at": null,
  "deleted_at": null,
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T11:00:00.000000Z",
  "products": [
    {
      "id": "01hkb1j1z86twgmy3f2cn6aafh",
      "title": "Product A",
      "price": 10000.0,
      "quantity": 1
    }
  ],
  "customer": {
    "id": "01k74k19r8cc6ksb89a1fnh6md",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "email": "wdjopa@example.com",
    "phone": "+237695762595"
  },
  "payments": [],
  "custom_fields": [],
  "adjustments": [],
  "user": null,
  "subscriptions": [],
  "taxes": [],
  "discounts": [],
  "company": null,
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "addresses": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "shop": null,
  "signature": null,
  "bills": []
}
```

---

### `POST` `/2023-11/customers/orders/{order}/payments`
Generate a new payment link for an existing order.

- **Path parameters:**

| Name | Type | Description |
|------|------|-------------|
| `order` | ULID | Order ID |

- **Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `amount_due` | numeric | No | Override amount; defaults to `order.amount_due` |
| `success_url` | string | No | Redirect URL after payment |
| `cancel_url` | string | No | Redirect URL on cancel |

**Request example:**

```http
POST /2023-11/customers/orders/01hzorder123abc/payments HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "success_url": "https://myshop.com/order/success"
}
```

**Success response `200 OK`:**

```json
{
  "url": "https://pay.example.com/session/newlink"
}
```

**Error responses:**
- `400` — payment link could not be generated (no matching treasury account or provider error)

---

## Customer Identity Check (Public)

### `POST` `/2023-11/customers/check-identity`
Resolve the account holder name for a given phone number. Currently limited to Cameroon (`cm`) numbers via the Peex identity service. Results are cached for one month.

- **Request body:**

| Field | Type | Required | Rules | Description |
|-------|------|----------|-------|-------------|
| `phone` | string | Yes | max:30 | Phone number to verify |
| `country_code` | string | No | Only `cm` is accepted | Country code (defaults to `cm`) |

**Request example:**

```http
POST /2023-11/customers/check-identity HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "phone": "+237695762595",
  "country_code": "cm"
}
```

**Success response `200 OK`:**

```json
{
  "phone": "+237695762595",
  "name": "Wilfried Djopa",
  "verified": true
}
```

**Error responses:**
- `422` — phone missing or unsupported `country_code`
