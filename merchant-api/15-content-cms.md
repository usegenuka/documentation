---
title: "Content & Storefront — Blogs, Pages, Domains, Media"
description: "This domain covers the storefront content layer: blog containers and their articles, static pages, custom domain management (with Caddy-backed provisioning and "
sidebarTitle: "Content & CMS"
---

This domain covers the storefront content layer: blog containers and their articles, static pages, custom domain management (with Caddy-backed provisioning and Plausible analytics integration), theme configuration for storefronts, and the media library (both direct file upload and S3 presigned / multipart upload). Public read endpoints for blogs and pages require no authentication; all write operations and the full media/upload surface require a valid bearer token and company header.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/blogs` | List blogs (admin, paginated) |
| POST | `/2023-11/admin/blogs` | Create a blog |
| GET | `/2023-11/admin/blogs/handle/{blog}` | Get a blog by handle (admin) |
| GET | `/2023-11/admin/blogs/{blog}` | Get a blog by ID (admin) |
| PUT | `/2023-11/admin/blogs/{blog}` | Update a blog |
| DELETE | `/2023-11/admin/blogs/{blog}` | Delete a blog |
| GET | `/2023-11/blogs` | List blogs (public, paginated) |
| GET | `/2023-11/blogs/count` | Count of all blogs (public) |
| GET | `/2023-11/blogs/handle/{blog}` | Get a blog by handle (public) |
| GET | `/2023-11/blogs/{blog}` | Get a blog by ID (public) |
| GET | `/2023-11/admin/pages` | List pages (admin, paginated) |
| POST | `/2023-11/admin/pages` | Create a page |
| GET | `/2023-11/admin/pages/handle/{page}` | Get a page by handle (admin) |
| GET | `/2023-11/admin/pages/{page}` | Get a page by ID (admin) |
| PUT | `/2023-11/admin/pages/{page}` | Update a page |
| DELETE | `/2023-11/admin/pages/{page}` | Delete a page |
| GET | `/2023-11/pages` | List pages (public, paginated) |
| GET | `/2023-11/pages/count` | Count of all pages (public) |
| GET | `/2023-11/pages/handle/{page}` | Get a page by handle (public) |
| GET | `/2023-11/pages/{page}` | Get a page by ID (public) |
| GET | `/2023-11/admin/domains` | List domains |
| POST | `/2023-11/admin/domains` | Add a custom domain |
| GET | `/2023-11/admin/domains/{domain}` | Get a domain |
| PUT | `/2023-11/admin/domains/{domain}` | Update a domain |
| DELETE | `/2023-11/admin/domains/{domain}` | Remove a domain |
| GET | `/2023-11/admin/domains/{domain}/analytics` | Aggregate analytics for a domain |
| GET | `/2023-11/admin/domains/{domain}/analytics/timeseries` | Visitor timeseries for a domain |
| GET | `/2023-11/admin/domains/{domain}/analytics/top-countries` | Top visitor countries |
| GET | `/2023-11/admin/domains/{domain}/analytics/top-devices` | Top visitor devices |
| GET | `/2023-11/admin/domains/{domain}/analytics/top-pages` | Top visited pages |
| GET | `/2023-11/admin/domains/{domain}/analytics/top-sources` | Top traffic sources |
| GET | `/2023-11/admin/theme-configurations` | List theme configurations |
| GET | `/2023-11/admin/theme-configurations/{themeConfiguration}` | Get a theme configuration |
| PUT | `/2023-11/admin/theme-configurations/{themeConfiguration}` | Update a theme configuration |
| GET | `/2023-11/admin/medias` | List media files |
| POST | `/2023-11/admin/medias` | Upload media files |
| GET | `/2023-11/admin/medias/stats` | Media library usage statistics |
| DELETE | `/2023-11/admin/medias/{media}` | Delete a media file |
| DELETE | `/2023-11/upload/by-key` | Delete an S3 object by key |
| POST | `/2023-11/upload/multipart/abort` | Abort a multipart S3 upload |
| POST | `/2023-11/upload/multipart/complete` | Complete a multipart S3 upload |
| POST | `/2023-11/upload/multipart/create` | Initiate a multipart S3 upload |
| POST | `/2023-11/upload/presign` | Get a presigned S3 PUT URL |

---

## Blogs

Blogs are content containers that group articles. Each blog belongs to a company and has a URL-safe handle that is unique within that company. A blog can have an optional cover image (stored in the Spatie media library). The `articles` relationship can be sideloaded.

### `GET` `/2023-11/admin/blogs`

List all blogs for the authenticated company. Supports search and sorting.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Full-text search across `title` and `handle` (also searches related articles) |
| `include` | string | No | — | Comma-separated relations to eager-load. Allowed: `articles` |
| `sort` | string | No | `title` | Sort column. Prefix with `-` for descending. Allowed: `title`, `handle`, `published`, `created_at`, `updated_at` |
| `page` | integer | No | `1` | Page number |
| `per_page` | integer | No | `15` | Results per page |

```http
GET /2023-11/admin/blogs?include=articles&filter[search]=news&sort=-created_at&per_page=10 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hma99pbymwb5zrh2s6p7nhcm",
      "title": "Company News",
      "handle": "company-news",
      "content": null,
      "metadata": { "shopifyId": "123" },
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "medias": []
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 10, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Missing or invalid bearer token.
- `403` — Token does not have access to the specified company.

---

### `POST` `/2023-11/admin/blogs`

Create a new blog for the authenticated company.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | — | Human-readable blog title |
| `handle` | string | No | Slug-formatted; auto-derived from `title` if omitted | URL-safe identifier (must be unique per company) |
| `metadata` | object | No | — | Arbitrary key-value metadata (e.g. `{"shopifyId": "123"}`) |
| `content` | string | No | — | Blog description or intro content |
| `uploadImageUrl` | string | No | Valid URL | Cover image fetched from this URL |
| `uploadImageBase64` | string | No | Base64 string | Cover image uploaded from base64 data |
| `created_at` | string | No | ISO 8601 | Override creation timestamp (useful for migrations) |
| `updated_at` | string | No | ISO 8601 | Override updated timestamp |

```http
POST /2023-11/admin/blogs HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Company News",
  "handle": "company-news",
  "metadata": { "shopifyId": "123" },
  "content": "Latest updates from our team."
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hma99pbymwb5zrh2s6p7nhcm",
  "title": "Company News",
  "handle": "company-news",
  "content": "Latest updates from our team.",
  "metadata": { "shopifyId": "123" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized action.

---

### `GET` `/2023-11/admin/blogs/handle/{blog}`

Retrieve a single blog by its handle slug (admin route).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `blog` | string | The blog's URL handle (e.g. `company-news`) |

```http
GET /2023-11/admin/blogs/handle/company-news HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hma99pbymwb5zrh2s6p7nhcm",
  "title": "Company News",
  "handle": "company-news",
  "content": "Latest updates from our team.",
  "metadata": { "shopifyId": "123" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "medias": []
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — No blog found with that handle.

---

### `GET` `/2023-11/admin/blogs/{blog}`

Retrieve a single blog by its ULID (admin route).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `blog` | string (ULID) | The blog's ULID identifier |

```http
GET /2023-11/admin/blogs/01hma99pbymwb5zrh2s6p7nhcm HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hma99pbymwb5zrh2s6p7nhcm",
  "title": "Company News",
  "handle": "company-news",
  "content": "Latest updates from our team.",
  "metadata": { "shopifyId": "123" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "medias": []
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Blog not found.

---

### `PUT` `/2023-11/admin/blogs/{blog}`

Update a blog's fields. Only supplied fields are changed.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `blog` | string (ULID) | The blog's ULID identifier |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | — | Updated blog title |
| `handle` | string | No | — | Updated URL handle |
| `metadata` | object | No | — | Replaced metadata object |
| `uploadImageUrl` | string | No | Valid URL | Replace cover image from URL |
| `uploadImageBase64` | string | No | Base64 string | Replace cover image from base64 |

```http
PUT /2023-11/admin/blogs/01hma99pbymwb5zrh2s6p7nhcm HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Company News & Updates",
  "handle": "company-news-updates",
  "metadata": { "shopifyId": "123" }
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hma99pbymwb5zrh2s6p7nhcm",
  "title": "Company News & Updates",
  "handle": "company-news-updates",
  "content": "Latest updates from our team.",
  "metadata": { "shopifyId": "123" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "medias": []
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Blog not found.

---

### `DELETE` `/2023-11/admin/blogs/{blog}`

Permanently delete a blog.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `blog` | string (ULID) | The blog's ULID identifier |

```http
DELETE /2023-11/admin/blogs/01hma99pbymwb5zrh2s6p7nhcm HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): empty body (no content).

**Error responses**
- `401` — Unauthenticated.
- `404` — Blog not found.

---

### `GET` `/2023-11/blogs`

List all blogs for a storefront (public, no auth required). Accepts the same filter/sort/pagination query parameters as the admin list endpoint.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search across `title` and `handle` |
| `include` | string | No | — | Allowed: `articles` |
| `sort` | string | No | `title` | Allowed: `title`, `handle`, `published`, `created_at`, `updated_at` |
| `page` | integer | No | `1` | Page number |
| `per_page` | integer | No | `15` | Results per page |

```http
GET /2023-11/blogs?per_page=5 HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hma99pbymwb5zrh2s6p7nhcm",
      "title": "Company News",
      "handle": "company-news",
      "content": "Latest updates from our team.",
      "metadata": { "shopifyId": "123" },
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "medias": []
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 5, "to": 1, "total": 1 }
}
```

**Error responses**
- `403` — Company header missing or invalid.

---

### `GET` `/2023-11/blogs/count`

Return the total number of blogs for the company. No pagination.

```http
GET /2023-11/blogs/count HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "count": 12 }
```

---

### `GET` `/2023-11/blogs/handle/{blog}`

Retrieve a single blog by handle (public).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `blog` | string | The blog's URL handle |

```http
GET /2023-11/blogs/handle/company-news HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hma99pbymwb5zrh2s6p7nhcm",
  "title": "Company News",
  "handle": "company-news",
  "content": "Latest updates from our team.",
  "metadata": { "shopifyId": "123" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "medias": []
}
```

**Error responses**
- `404` — Blog not found.

---

### `GET` `/2023-11/blogs/{blog}`

Retrieve a single blog by ULID (public).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `blog` | string (ULID) | The blog's ULID identifier |

```http
GET /2023-11/blogs/01hma99pbymwb5zrh2s6p7nhcm HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hma99pbymwb5zrh2s6p7nhcm",
  "title": "Company News",
  "handle": "company-news",
  "content": "Latest updates from our team.",
  "metadata": { "shopifyId": "123" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "medias": []
}
```

**Error responses**
- `404` — Blog not found.

---

## Pages

Static CMS pages (e.g. Terms & Conditions, About Us). Each page belongs to a company, has a unique handle, a rich text `content` field, optional metadata, and an optional cover image. The public endpoints require only `X-Company`.

### `GET` `/2023-11/admin/pages`

List all pages for the authenticated company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search across `title` and `handle` |
| `sort` | string | No | `title` | Allowed: `title`, `handle`, `published`, `created_at`, `updated_at` |
| `page` | integer | No | `1` | Page number |
| `per_page` | integer | No | `15` | Results per page |

```http
GET /2023-11/admin/pages?sort=-created_at HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrf96vstxncp3w9vnht175xs",
      "title": "Terms & Conditions",
      "handle": "terms",
      "content": "Last Updated: February 15, 2024...",
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-02-15T08:00:00.000000Z",
      "updated_at": "2024-02-15T08:00:00.000000Z",
      "medias": []
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `POST` `/2023-11/admin/pages`

Create a new CMS page.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | — | Page title |
| `handle` | string | Yes | Slug-formatted; unique per company | URL-safe identifier (e.g. `terms`, `about`) |
| `content` | string | No | — | Full page body text or HTML |
| `metadata` | object | No | — | Arbitrary metadata |
| `uploadImageUrl` | string | No | Valid URL | Cover image from URL |
| `uploadImageBase64` | string | No | Base64 string | Cover image from base64 |
| `created_at` | string | No | ISO 8601 | Override creation timestamp |
| `updated_at` | string | No | ISO 8601 | Override updated timestamp |

```http
POST /2023-11/admin/pages HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: February 15, 2024\n\nWelcome to our store..."
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrf96vstxncp3w9vnht175xs",
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: February 15, 2024\n\nWelcome to our store...",
  "metadata": [],
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-02-15T08:00:00.000000Z",
  "updated_at": "2024-02-15T08:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Validation failed (e.g. duplicate handle within company).

---

### `GET` `/2023-11/admin/pages/handle/{page}`

Retrieve a single page by its handle (admin).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `page` | string | The page's URL handle |

```http
GET /2023-11/admin/pages/handle/terms HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrf96vstxncp3w9vnht175xs",
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: February 15, 2024\n\nWelcome to our store...",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-02-15T08:00:00.000000Z",
  "updated_at": "2024-02-15T08:00:00.000000Z",
  "medias": []
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Page not found.

---

### `GET` `/2023-11/admin/pages/{page}`

Retrieve a single page by ULID (admin).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `page` | string (ULID) | The page's ULID identifier |

```http
GET /2023-11/admin/pages/01hrf96vstxncp3w9vnht175xs HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrf96vstxncp3w9vnht175xs",
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: February 15, 2024\n\nWelcome to our store...",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-02-15T08:00:00.000000Z",
  "updated_at": "2024-02-15T08:00:00.000000Z",
  "medias": []
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Page not found.

---

### `PUT` `/2023-11/admin/pages/{page}`

Update a page. Only supplied fields are changed.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `page` | string (ULID) | The page's ULID identifier |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | — | Updated title |
| `handle` | string | No | Slug | Updated handle |
| `content` | string | No | — | Updated body |
| `metadata` | object | No | — | Replaced metadata |
| `uploadImageUrl` | string | No | Valid URL | Replace cover image from URL |
| `uploadImageBase64` | string | No | Base64 string | Replace cover image from base64 |

```http
PUT /2023-11/admin/pages/01hrf96vstxncp3w9vnht175xs HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "content": "Last Updated: March 1, 2024\n\nWelcome to our store (updated)..."
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrf96vstxncp3w9vnht175xs",
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: March 1, 2024\n\nWelcome to our store (updated)...",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-02-15T08:00:00.000000Z",
  "updated_at": "2024-03-01T09:00:00.000000Z",
  "medias": []
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Page not found.

---

### `DELETE` `/2023-11/admin/pages/{page}`

Permanently delete a page.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `page` | string (ULID) | The page's ULID identifier |

```http
DELETE /2023-11/admin/pages/01hrf96vstxncp3w9vnht175xs HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`204 No Content`): empty body.

**Error responses**
- `401` — Unauthenticated.
- `404` — Page not found.

---

### `GET` `/2023-11/pages`

List all pages (public storefront). Same filter/sort/pagination as the admin list.

```http
GET /2023-11/pages HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrf96vstxncp3w9vnht175xs",
      "title": "Terms & Conditions",
      "handle": "terms",
      "content": "Last Updated: February 15, 2024...",
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-02-15T08:00:00.000000Z",
      "updated_at": "2024-02-15T08:00:00.000000Z",
      "medias": []
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

---

### `GET` `/2023-11/pages/count`

Return the total number of pages for the company.

```http
GET /2023-11/pages/count HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "count": 5 }
```

---

### `GET` `/2023-11/pages/handle/{page}`

Retrieve a page by handle (public).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `page` | string | The page's URL handle |

```http
GET /2023-11/pages/handle/terms HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrf96vstxncp3w9vnht175xs",
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: February 15, 2024\n\nWelcome to our store...",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-02-15T08:00:00.000000Z",
  "updated_at": "2024-02-15T08:00:00.000000Z",
  "medias": []
}
```

**Error responses**
- `404` — Page not found for that handle.

---

### `GET` `/2023-11/pages/{page}`

Retrieve a page by ULID (public).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `page` | string (ULID) | The page's ULID identifier |

```http
GET /2023-11/pages/01hrf96vstxncp3w9vnht175xs HTTP/1.1
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrf96vstxncp3w9vnht175xs",
  "title": "Terms & Conditions",
  "handle": "terms",
  "content": "Last Updated: February 15, 2024\n\nWelcome to our store...",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-02-15T08:00:00.000000Z",
  "updated_at": "2024-02-15T08:00:00.000000Z",
  "medias": []
}
```

**Error responses**
- `404` — Page not found.

---

## Domains

Custom domain management. When a domain is created or updated the API provisions it on the Caddy reverse proxy and registers it with Plausible for analytics tracking. Custom (non-`.genuka.com`) domains must have their DNS A record pointing to the server's IP before the API will accept them.

### `GET` `/2023-11/admin/domains`

List all domains for the authenticated company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[domain]` | string | No | — | Partial-match filter on the domain string |
| `sort` | string | No | — | Allowed: `domain`, `created_at`, `updated_at` |
| `page` | integer | No | `1` | Page number |
| `per_page` | integer | No | `15` | Results per page |

```http
GET /2023-11/admin/domains HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hrm5ndtmy0y02s0g6091g9wr",
      "domain": "shop.example.com",
      "is_primary": false,
      "main": true,
      "is_active": true,
      "shop_id": null,
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-03-01T09:00:00.000000Z",
      "updated_at": "2024-03-01T09:00:00.000000Z",
      "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Inc." }
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `POST` `/2023-11/admin/domains`

Register a new custom domain. For non-`.genuka.com` domains the DNS A record must point to the Caddy server IP before the request is made.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `domain` | string | Yes | `unique:domains`; must not be in the excluded list; DNS A record must point to server (for non-`.genuka.com` domains) | Fully qualified domain name |
| `shop_id` | string (ULID) | No | — | Associate the domain with a specific shop |

```http
POST /2023-11/admin/domains HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "domain": "shop.example.com"
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrm5ndtmy0y02s0g6091g9wr",
  "domain": "shop.example.com",
  "is_primary": false,
  "main": true,
  "is_active": true,
  "shop_id": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-01T09:00:00.000000Z",
  "updated_at": "2024-03-01T09:00:00.000000Z"
}
```

**Error responses**
- `400` — Domain is not allowed (excluded list or DNS does not point to server).
- `400` — Company already has the maximum number of `.genuka.com` subdomains.
- `422` — `domain` is required or already taken.
- `500` — Caddy provisioning failed.

---

### `GET` `/2023-11/admin/domains/{domain}`

Retrieve a single domain by ULID.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrm5ndtmy0y02s0g6091g9wr",
  "domain": "shop.example.com",
  "is_primary": false,
  "main": true,
  "is_active": true,
  "shop_id": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-01T09:00:00.000000Z",
  "updated_at": "2024-03-01T09:00:00.000000Z"
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Domain not found.

---

### `PUT` `/2023-11/admin/domains/{domain}`

Update a domain's hostname or main-domain flag. If the hostname changes, the old Caddy site is removed and a new one is provisioned.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `domain` | string | Yes | Unique per company (except current); DNS must point to server for non-`.genuka.com` | New fully qualified domain name |
| `main` | boolean | No | — | Set to `true` to promote this domain to primary (demotes any existing primary) |
| `shop_id` | string (ULID) | No | — | Reassociate with a shop |

```http
PUT /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "domain": "store.example.com",
  "main": true
}
```

**Success response** (`200 OK`):
```json
{
  "id": "01hrm5ndtmy0y02s0g6091g9wr",
  "domain": "store.example.com",
  "is_primary": false,
  "main": true,
  "is_active": true,
  "shop_id": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-01T09:00:00.000000Z",
  "updated_at": "2024-03-01T10:00:00.000000Z"
}
```

**Error responses**
- `400` — Domain not allowed or DNS check failed.
- `422` — Validation errors.
- `500` — Caddy provisioning failed.

---

### `DELETE` `/2023-11/admin/domains/{domain}`

Remove a domain and de-provision it from the Caddy reverse proxy. If this was the main domain, the first remaining domain is automatically promoted.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

```http
DELETE /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Domain deleted successfully" }
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Domain not found.
- `500` — Caddy de-provisioning failed.

---

## Domain Analytics

Analytics are powered by a Plausible instance. All analytics endpoints accept a date range via query parameters; dates default to the last 30 days when omitted.

### `GET` `/2023-11/admin/domains/{domain}/analytics`

Aggregate traffic metrics (visitors, pageviews, bounce rate, visit duration) for a domain.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string | No | 30 days ago | Start of date range (`YYYY-MM-DD`) |
| `endDate` | string | No | Today | End of date range (`YYYY-MM-DD`) |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr/analytics?startDate=2024-03-01&endDate=2024-03-31 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "results": {
    "visitors": { "value": 1245 },
    "pageviews": { "value": 3876 },
    "bounce_rate": { "value": 52 },
    "visit_duration": { "value": 187 }
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Domain not found.
- `500` — Plausible API error.

---

### `GET` `/2023-11/admin/domains/{domain}/analytics/timeseries`

Daily visitor and visit counts for a date range.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string | No | 30 days ago | Start of date range (`YYYY-MM-DD`) |
| `endDate` | string | No | Today | End of date range (`YYYY-MM-DD`) |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr/analytics/timeseries?startDate=2024-03-01&endDate=2024-03-31 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "results": [
    { "date": "2024-03-01", "visitors": 42, "visits": 51 },
    { "date": "2024-03-02", "visitors": 38, "visits": 45 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `500` — Plausible API error.

---

### `GET` `/2023-11/admin/domains/{domain}/analytics/top-countries`

Top 10 visitor countries for a domain in a date range.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string | No | 30 days ago | Start of date range (`YYYY-MM-DD`) |
| `endDate` | string | No | Today | End of date range (`YYYY-MM-DD`) |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr/analytics/top-countries?startDate=2024-03-01&endDate=2024-03-31 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "results": [
    { "country": "CM", "visitors": 812 },
    { "country": "FR", "visitors": 210 },
    { "country": "NG", "visitors": 89 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `500` — Plausible API error.

---

### `GET` `/2023-11/admin/domains/{domain}/analytics/top-devices`

Top 10 device types (Desktop, Mobile, Tablet) for a domain in a date range.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string | No | 30 days ago | Start of date range (`YYYY-MM-DD`) |
| `endDate` | string | No | Today | End of date range (`YYYY-MM-DD`) |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr/analytics/top-devices?startDate=2024-03-01&endDate=2024-03-31 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "results": [
    { "device": "Mobile", "visitors": 900 },
    { "device": "Desktop", "visitors": 320 },
    { "device": "Tablet", "visitors": 25 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `500` — Plausible API error.

---

### `GET` `/2023-11/admin/domains/{domain}/analytics/top-pages`

Top 10 most visited pages on a domain in a date range.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string | No | 30 days ago | Start of date range (`YYYY-MM-DD`) |
| `endDate` | string | No | Today | End of date range (`YYYY-MM-DD`) |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr/analytics/top-pages?startDate=2024-03-01&endDate=2024-03-31 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "results": [
    { "page": "/", "visitors": 640 },
    { "page": "/products", "visitors": 410 },
    { "page": "/contact", "visitors": 95 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `500` — Plausible API error.

---

### `GET` `/2023-11/admin/domains/{domain}/analytics/top-sources`

Top 10 referral/traffic sources for a domain in a date range.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `domain` | string (ULID) | The domain's ULID identifier |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string | No | 30 days ago | Start of date range (`YYYY-MM-DD`) |
| `endDate` | string | No | Today | End of date range (`YYYY-MM-DD`) |

```http
GET /2023-11/admin/domains/01hrm5ndtmy0y02s0g6091g9wr/analytics/top-sources?startDate=2024-03-01&endDate=2024-03-31 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "results": [
    { "source": "Google", "visitors": 530 },
    { "source": "Direct / None", "visitors": 380 },
    { "source": "Facebook", "visitors": 210 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `500` — Plausible API error.

---

## Theme Configurations

Theme configurations link a storefront template to a company and hold a JSON `configuration` blob (colors, fonts, layout settings, etc.). Setting `is_active` to `true` promotes that theme and re-provisions all company domains in Caddy. Only one theme can be active at a time; activating a new one automatically deactivates all others.

### `GET` `/2023-11/admin/theme-configurations`

List all theme configurations for the authenticated company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `include` | string | No | — | Allowed: `template` |
| `page` | integer | No | `1` | Page number |
| `per_page` | integer | No | `15` | Results per page |

```http
GET /2023-11/admin/theme-configurations?include=template HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqz4m7bxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "template_id": "01hp1bzt5xdj3kmzp3bz7jk000",
      "is_active": true,
      "configuration": {
        "primary_color": "#3b82f6",
        "font": "Inter"
      },
      "metadata": null,
      "created_at": "2024-03-10T12:00:00.000000Z",
      "updated_at": "2024-03-10T12:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 15, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/theme-configurations/{themeConfiguration}`

Retrieve a single theme configuration by ULID. Eager-loads `template` and `company`.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `themeConfiguration` | string (ULID) | The theme configuration's ULID |

```http
GET /2023-11/admin/theme-configurations/01hqz4m7bxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqz4m7bxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "template_id": "01hp1bzt5xdj3kmzp3bz7jk000",
  "is_active": true,
  "configuration": { "primary_color": "#3b82f6", "font": "Inter" },
  "metadata": null,
  "created_at": "2024-03-10T12:00:00.000000Z",
  "updated_at": "2024-03-10T12:00:00.000000Z",
  "template": { "id": "01hp1bzt5xdj3kmzp3bz7jk000", "name": "Modern Store" },
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Acme Inc." }
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Theme configuration not found.

---

### `PUT` `/2023-11/admin/theme-configurations/{themeConfiguration}`

Update a theme configuration. Send either `configuration` to update the theme settings JSON, or `is_active` to switch the active theme. Mixing both in a single request is not supported — the handler checks `configuration` first.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `themeConfiguration` | string (ULID) | The theme configuration's ULID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `configuration` | object | Conditional | — | Full replacement of the theme JSON settings blob |
| `is_active` | boolean | Conditional | — | Set to `true` to activate this theme (deactivates all others and re-provisions Caddy domains) |

```http
PUT /2023-11/admin/theme-configurations/01hqz4m7bxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "configuration": {
    "primary_color": "#10b981",
    "font": "Poppins"
  }
}
```

**Success response** (`200 OK`):
```json
{
  "themeConfiguration": {
    "id": "01hqz4m7bxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "template_id": "01hp1bzt5xdj3kmzp3bz7jk000",
    "is_active": true,
    "configuration": { "primary_color": "#10b981", "font": "Poppins" },
    "metadata": null,
    "created_at": "2024-03-10T12:00:00.000000Z",
    "updated_at": "2024-03-10T12:05:00.000000Z"
  },
  "domains": [
    { "id": "01hrm5ndtmy0y02s0g6091g9wr", "domain": "shop.example.com", "main": true }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Theme configuration not found.
- `500` — Caddy re-provisioning failed when activating a theme.

---

## Media Library

Uploaded files are stored via Spatie Media Library and served from S3. Each media record belongs to a model (product, blog, page, company, etc.) and has image conversion variants (`micro`, `thumb`, `large`, `webp`). Access is permission-gated: users with `manage_media_files` see all company media; others see only the media associated with models they have permission to view.

### `GET` `/2023-11/admin/medias`

List media files for the authenticated company.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search across `file_name`, `mime_type`, `model_type`, `collection_name` |
| `filter[model_key]` | string | No | — | Filter by model type key (e.g. `product`, `blog`, `page`) |
| `filter[folder]` | string | No | — | Filter by S3 folder path prefix |
| `sort` | string | No | `-created_at` | Sort column (prefix `-` for desc). Allowed: `created_at`, `updated_at`, `file_name`, `mime_type`, `size`, `model_type`, `collection_name` |
| `page` | integer | No | `1` | Page number |
| `per_page` | integer | No | `15` | Results per page (max 100) |

```http
GET /2023-11/admin/medias?filter[model_key]=product&sort=-size&per_page=20 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": 1842,
      "link": "https://cdn.genuka.com/company/01hqydxwtxdj3kmzp3bz7jk73g/products/hero.jpg",
      "collection_name": "library",
      "mime_type": "image/jpeg",
      "file_name": "hero.jpg",
      "size": 204800,
      "model_id": "01hqz4m7bxdj3kmzp3bz7jk73g",
      "model_type": "App\\Models\\Product",
      "model_key": "product",
      "folder_key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products",
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "micro": "https://cdn.genuka.com/.../conversions/hero-micro.jpg",
      "thumb": "https://cdn.genuka.com/.../conversions/hero-thumb.jpg",
      "large": "https://cdn.genuka.com/.../conversions/hero-large.jpg",
      "webp": "https://cdn.genuka.com/.../conversions/hero.webp"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "from": 1, "last_page": 1, "per_page": 20, "to": 1, "total": 1 }
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Company not found.

---

### `POST` `/2023-11/admin/medias`

Upload one or more media files to the company library. Supports two modes:

1. **Traditional multipart upload** — send files in a `files[]` form-data field.
2. **S3 presigned registration** — after uploading directly to S3 via `POST /upload/presign`, register the uploaded keys by sending a `medias` JSON array.

**Request body — multipart file upload** (`multipart/form-data`)

| Field | Type | Required | Description |
|---|---|---|---|
| `files[]` | file | Yes | One or more file uploads |

**Request body — S3 key registration** (`application/json`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `medias` | array | Yes | — | Array of S3 key descriptors |
| `medias[].s3_key` | string | Yes | — | S3 object key (must exist in S3) |
| `medias[].file_name` | string | No | — | Display file name; defaults to basename of `s3_key` |
| `medias[].mime_type` | string | No | — | MIME type of the file |
| `medias[].size` | integer | No | — | File size in bytes |

```http
POST /2023-11/admin/medias HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "medias": [
    {
      "s3_key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/banner.jpg",
      "file_name": "banner.jpg",
      "mime_type": "image/jpeg",
      "size": 512000
    }
  ]
}
```

**Success response** (`200 OK`): array of `MediaResource` objects (same shape as list items).

```json
[
  {
    "id": 1843,
    "link": "https://cdn.genuka.com/company/01hqydxwtxdj3kmzp3bz7jk73g/products/banner.jpg",
    "collection_name": "library",
    "mime_type": "image/jpeg",
    "file_name": "banner.jpg",
    "size": 512000,
    "model_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "model_type": "App\\Models\\Company",
    "model_key": "company",
    "folder_key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products",
    "created_at": "2024-03-15T11:00:00.000000Z",
    "updated_at": "2024-03-15T11:00:00.000000Z",
    "micro": "...",
    "thumb": "...",
    "large": "...",
    "webp": "..."
  }
]
```

**Error responses**
- `400` — Error registering S3 key or uploading file.
- `401` — Unauthenticated.
- `404` — Company not found or `files` field missing.
- `500` — Internal upload error.

---

### `GET` `/2023-11/admin/medias/stats`

Return aggregate usage statistics for the company media library: total file count, total storage in bytes, a breakdown by model type, and a breakdown by S3 folder (top 50 folders by file count).

```http
GET /2023-11/admin/medias/stats HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "total_count": 342,
  "total_size": 1073741824,
  "models": [
    { "key": "product", "count": 200, "size": 734003200 },
    { "key": "blog", "count": 80, "size": 209715200 },
    { "key": "company", "count": 62, "size": 130023424 }
  ],
  "folders": [
    { "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products", "count": 200, "size": 734003200 },
    { "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/blogs", "count": 80, "size": 209715200 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Company not found.

---

### `DELETE` `/2023-11/admin/medias/{media}`

Permanently delete a media record (and its associated S3 file / conversions).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `media` | integer | The media record's integer ID |

```http
DELETE /2023-11/admin/medias/1843 HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Media deleted" }
```

**Error responses**
- `401` — Unauthenticated.
- `404` — Media record not found.

---

## S3 Upload

These endpoints facilitate direct client-to-S3 uploads, bypassing the API server for file transfer. The recommended flow for large files is: `POST /upload/presign` → PUT to the presigned URL → `POST /admin/medias` (S3 key registration). For files over 5 MB, use the multipart flow: `POST /upload/multipart/create` → PUT each part → `POST /upload/multipart/complete` → `POST /admin/medias`.

S3 keys are scoped to `company/<companyId>/<model>/<collection>/` to enforce tenant isolation. The `DELETE /upload/by-key` endpoint enforces the same prefix restriction.

### `POST` `/2023-11/upload/presign`

Generate a presigned S3 PUT URL for single-file direct upload. The client uploads the file directly to S3 using the returned URL, then registers the object with `POST /admin/medias`.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `content_type` | string | Yes | — | MIME type of the file (e.g. `image/jpeg`) |
| `ext` | string | Yes | — | File extension without leading dot (e.g. `jpg`) |
| `model` | string | Yes | — | Model context key (e.g. `products`, `blogs`, `pages`) |
| `collection` | string | Yes | — | Media collection name (e.g. `gallery`, `header`) |
| `filename` | string | No | Random ULID | Desired base filename (without extension) |

```http
POST /2023-11/upload/presign HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "content_type": "image/jpeg",
  "ext": "jpg",
  "model": "products",
  "collection": "gallery",
  "filename": "product-hero"
}
```

**Success response** (`200 OK`):
```json
{
  "url": "https://bucket-genuka.s3.amazonaws.com/company/01hqydxwtxdj3kmzp3bz7jk73g/products/gallery/product-hero.jpg?X-Amz-Signature=...",
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/gallery/product-hero.jpg"
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Missing required field (`content_type`, `ext`, `model`, or `collection`).

---

### `POST` `/2023-11/upload/multipart/create`

Initiate an S3 multipart upload and receive presigned PUT URLs for each part. Use for files larger than 5 MB. Parts must be at least 5 MB each (except the last).

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `content_type` | string | Yes | — | MIME type of the file |
| `ext` | string | Yes | — | File extension without dot |
| `model` | string | Yes | — | Model context key |
| `collection` | string | Yes | — | Media collection name |
| `part_count` | integer | Yes | `min:1`, `max:10000` | Number of parts the file will be split into |
| `filename` | string | No | Random ULID | Desired base filename |

```http
POST /2023-11/upload/multipart/create HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "content_type": "video/mp4",
  "ext": "mp4",
  "model": "products",
  "collection": "videos",
  "filename": "product-demo",
  "part_count": 3
}
```

**Success response** (`200 OK`):
```json
{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/videos/product-demo.mp4",
  "upload_id": "VXBsb2FkIElEIGV4YW1wbGU",
  "urls": {
    "1": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=1&uploadId=...&X-Amz-Signature=...",
    "2": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=2&uploadId=...&X-Amz-Signature=...",
    "3": "https://bucket-genuka.s3.amazonaws.com/...?partNumber=3&uploadId=...&X-Amz-Signature=..."
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Validation failed (e.g. `part_count` out of range).

---

### `POST` `/2023-11/upload/multipart/complete`

Complete a multipart upload after all parts have been PUT to S3. The client must supply the `ETag` header value returned by S3 for each part.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | Yes | — | The S3 object key returned by `multipart/create` |
| `upload_id` | string | Yes | — | The multipart upload ID returned by `multipart/create` |
| `parts` | array | Yes | `min:1` | Array of part completion objects |
| `parts[].PartNumber` | integer | Yes | `min:1` | Part number (1-based) |
| `parts[].ETag` | string | Yes | — | ETag value from the S3 PUT response for that part |

```http
POST /2023-11/upload/multipart/complete HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/videos/product-demo.mp4",
  "upload_id": "VXBsb2FkIElEIGV4YW1wbGU",
  "parts": [
    { "PartNumber": 1, "ETag": "\"a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4\"" },
    { "PartNumber": 2, "ETag": "\"b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5\"" },
    { "PartNumber": 3, "ETag": "\"c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6\"" }
  ]
}
```

**Success response** (`200 OK`): the AWS S3 CompleteMultipartUpload result object, typically including the final object `Location`, `Bucket`, `Key`, and `ETag`.

```json
{
  "Location": "https://bucket-genuka.s3.amazonaws.com/company/01hqydxwtxdj3kmzp3bz7jk73g/products/videos/product-demo.mp4",
  "Bucket": "bucket-genuka",
  "Key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/videos/product-demo.mp4",
  "ETag": "\"abc123multipart\""
}
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Missing or invalid `key`, `upload_id`, or `parts` fields.

---

### `POST` `/2023-11/upload/multipart/abort`

Abort an in-progress multipart upload, freeing any partially uploaded parts from S3.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | Yes | — | The S3 object key |
| `upload_id` | string | Yes | — | The multipart upload ID to abort |

```http
POST /2023-11/upload/multipart/abort HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/videos/product-demo.mp4",
  "upload_id": "VXBsb2FkIElEIGV4YW1wbGU"
}
```

**Success response** (`200 OK`):
```json
{ "message": "Multipart upload aborted." }
```

**Error responses**
- `401` — Unauthenticated.
- `422` — Missing `key` or `upload_id`.

---

### `DELETE` `/2023-11/upload/by-key`

Delete an S3 object directly by its key. Only keys under the authenticated company's prefix (`company/<companyId>/`) are permitted.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | Yes | Must start with `company/<companyId>/` | S3 object key to delete |

```http
DELETE /2023-11/upload/by-key HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "key": "company/01hqydxwtxdj3kmzp3bz7jk73g/products/gallery/product-hero.jpg"
}
```

**Success response** (`200 OK`):
```json
{ "message": "Object deleted." }
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Key does not start with the authenticated company's prefix (`{ "message": "Unauthorized key prefix" }`).
- `422` — `key` field missing.
