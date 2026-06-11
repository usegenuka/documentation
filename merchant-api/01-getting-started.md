---
title: "Getting Started with the Genuka API"
sidebarTitle: "Getting Started"
---

## 1. Overview

The Genuka API is a multi-tenant REST API for building commerce, inventory, messaging, and accounting integrations on top of the Genuka platform. All responses are JSON.

**Production base URL**

```
https://api.genuka.com
```

There is one environment: production at `api.genuka.com`. There is no separate sandbox URL.

---

## 2. Versioning

The API uses date-based path versioning. Every resource endpoint is nested under the version prefix:

```
https://api.genuka.com/2023-11/<resource>
```

For example:

```
GET https://api.genuka.com/2023-11/admin/products
GET https://api.genuka.com/2023-11/admin/orders
```

The version `2023-11` is the current stable version. Breaking changes will be introduced under a new date prefix while the previous version remains accessible during a deprecation period.

---

## 3. Authentication

The API uses Laravel Passport personal access tokens (Bearer JWT). You must include a valid token on every authenticated request.

### Obtaining a token

Send a `POST` request to the login endpoint:

```http
POST https://api.genuka.com/2023-11/auth/login
Content-Type: application/json
Accept: application/json

{
  "email": "user@example.com",
  "password": "your_password"
}
```

A successful response returns a `plainTextToken` (the token you store and reuse) along with the user's `companyId`:

```json
{
  "plainTextToken": "1|abc123...",
  "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_code": "MYSTORE",
  "name": "Alice",
  "accessToken": { ... }
}
```

Store `plainTextToken` securely. Include it in every subsequent request as a Bearer token.

For full authentication details — registration, PIN login, password reset, multi-company users — see [02-authentication.md](./02-authentication.md).

---

## 4. Required Headers

Every authenticated request to the admin API must include all four headers below.

| Header | Required | Value | Notes |
|---|---|---|---|
| `Authorization` | Yes | `Bearer <plainTextToken>` | Token obtained from `/2023-11/auth/login` |
| `X-Company` | Yes | `<companyId>` | ULID of the tenant company. Omitting it returns HTTP 422. |
| `Accept` | Yes | `application/json` | Ensures JSON error responses instead of HTML. |
| `Content-Type` | Yes (write ops) | `application/json` | Required for `POST`, `PUT`, `PATCH` requests with a body. |

**Example authenticated request**

```http
GET https://api.genuka.com/2023-11/admin/products
Authorization: Bearer 1|abc123...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Optional shop header**

Many endpoints are additionally scoped to a shop (point of sale / storefront). Pass the shop ULID to restrict results to that shop:

| Header | Required | Value |
|---|---|---|
| `X-Shop` | No | ULID of the active shop |

---

## 5. Multi-Tenancy

The Genuka API is multi-tenant. Each company is an isolated tenant. All data — products, orders, customers, payments, etc. — is scoped to the company identified by `X-Company`.

**How it works**

- The `X-Company` header value is read by the `CheckCurrentCompany` middleware and injected as `companyId` into the request context.
- Eloquent global scopes (via `CompanyScope`) automatically append `WHERE company_id = ?` to every query for scoped models.
- The `owner` middleware (applied to the `/admin` route group) additionally verifies that the authenticated user belongs to that company. If the user's company does not match `X-Company`, the request is rejected with HTTP 401.

**Missing or wrong company**

| Situation | HTTP status | Response |
|---|---|---|
| `X-Company` header absent | 422 | `{"message":"Please provide a Company Id","error":"company_id_required","code":400}` |
| User does not own the company | 401 | `{"message":"You don't have the permission to manage this company..."}` |

---

## 6. Pagination

All paginated list endpoints return the same JSON envelope. The API does **not** use the default Laravel `data/links/meta` wrapper. It uses a custom `pagination` object:

```json
{
  "data": [ { ... }, { ... } ],
  "pagination": {
    "total": 284,
    "count": 15,
    "per_page": 15,
    "current_page": 2,
    "total_pages": 19
  }
}
```

### Pagination query parameters

| Parameter | Type | Default | Maximum | Description |
|---|---|---|---|---|
| `page` | integer | `1` | — | Page number to retrieve |
| `per_page` | integer | `15` | `100` | Number of items per page |

**Example**

```http
GET /2023-11/admin/products?page=3&per_page=25
```

Some endpoints also support `simple_paginate=1` which returns a cursor-based response without a total count (faster on very large tables).

---

## 7. Filtering, Sorting, and Search

The API uses [Spatie Laravel Query Builder](https://spatie.be/docs/laravel-query-builder) conventions. Parameters are namespaced with brackets.

### Filtering

```
filter[<field>]=<value>
```

Examples:

```
GET /2023-11/admin/products?filter[published]=true
GET /2023-11/admin/products?filter[type]=physical
GET /2023-11/admin/products?filter[search]=blue+shirt
GET /2023-11/admin/customers?filter[search]=alice
```

Multiple filters can be combined. Refer to each resource's documentation for its allowed filter names.

### Sorting

```
sort=<field>           # ascending
sort=-<field>          # descending (prefix with -)
```

Examples:

```
GET /2023-11/admin/products?sort=title
GET /2023-11/admin/products?sort=-created_at
GET /2023-11/admin/orders?sort=-created_at
```

### Including relationships

```
include=<relation>,<relation>
```

Example:

```
GET /2023-11/admin/products?include=variants,tags,collections
```

Only whitelisted includes are accepted. Requesting an unknown include returns a 400 error.

### Field selection

```
fields=id,title,handle,min_price
```

Restricts the response to the listed fields, reducing payload size.

### Date range filtering

Many index endpoints accept `startDate` and `endDate` parameters (ISO 8601 format):

```
GET /2023-11/admin/products?startDate=2024-01-01&endDate=2024-12-31
```

---

## 8. Error Handling

The API returns a consistent JSON error envelope for all error responses.

### Error envelope shape

```json
{
  "message": "Human-readable description of the error",
  "error": "machine_readable_error_code",
  "code": 422
}
```

For validation errors (HTTP 422), Laravel includes an additional `errors` object with per-field messages:

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "email": ["The email field is required."],
    "password": ["The password must be at least 8 characters."]
  }
}
```

### HTTP status codes

| Status | Meaning | Example `error` value |
|---|---|---|
| `200 OK` | Success | — |
| `201 Created` | Resource created | — |
| `400 Bad Request` | Malformed request or missing company ID | `company_id_required` |
| `401 Unauthorized` | Invalid token or wrong company | `unauthenticated` / custom message |
| `403 Forbidden` | Valid token but insufficient scope/permission | `invalid_scope` |
| `404 Not Found` | Resource does not exist | `model_not_found` |
| `422 Unprocessable Entity` | Validation failed | — (see `errors` object) |
| `429 Too Many Requests` | Rate limit exceeded | `too_many_requests` |
| `500 Internal Server Error` | Unexpected server error | `internal_server_error` |

**401 example**

```json
{
  "message": "You don't have the permission to manage this company. Company ID: 01hqydxwtxdj3kmzp3bz7jk73g",
  "error": "unauthenticated",
  "code": 401
}
```

**403 example** (invalid OAuth scope)

```json
{
  "message": "You don't have access to this.",
  "error": "invalid_scope",
  "code": 403
}
```

**422 example** (missing company header)

```json
{
  "message": "Please provide a Company Id",
  "error": "company_id_required",
  "code": 400
}
```

**500 example**

```json
{
  "message": "An error occurred: ...",
  "error": "internal_server_error",
  "code": 500
}
```

---

## 9. Data Formats

### Resource IDs — ULIDs

All resource identifiers are [ULIDs](https://github.com/ulid/spec) — 26-character alphanumeric strings that are lexicographically sortable by creation time.

```
01hqydxwtxdj3kmzp3bz7jk73g
```

ULIDs are case-insensitive but are stored and returned in lowercase.

### Timestamps

All timestamps are returned in **ISO 8601** format, UTC:

```
"created_at": "2024-03-15T10:23:45.000000Z"
"updated_at": "2024-03-15T11:00:00.000000Z"
```

### Money / Prices

Monetary amounts (prices, order totals, payment amounts) are stored and returned as **decimal numbers** (floating point, two decimal places). There is no integer-cent encoding.

```json
{
  "price": 12500.00,
  "compare_at_price": 15000.00,
  "amount": 37500.50
}
```

Currency is not embedded on individual resources. The company's configured currency is available on the company object:

```json
{
  "currency_code": "XAF",
  "currency_name": "CFA Franc BEAC"
}
```

---

## 10. Rate Limiting

The `api` middleware group applies throttling to all `/2023-11` routes via Laravel's rate limiter.

| Limiter | Limit | Key |
|---|---|---|
| `api` (default) | **6 000 requests per minute** | Authenticated user ID, or IP address for unauthenticated requests |
| `sync` | 60 requests/minute + 500 requests/hour | IP address |
| `chat` (public webchat) | 30 requests/minute | Bearer token, or IP address |

When the limit is exceeded, the server responds with **HTTP 429**. Laravel includes standard rate-limit headers in responses:

```
X-RateLimit-Limit: 6000
X-RateLimit-Remaining: 5874
Retry-After: 12
```
