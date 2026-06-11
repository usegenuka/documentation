---
title: "Catalog Organization — Collections, Articles, Tags"
description: "Collections group products into navigable storefronts sections and support nested sub-collections. Articles are blog-style content entries attached to a Blog an"
sidebarTitle: "Collections & Tags"
---

Collections group products into navigable storefronts sections and support nested sub-collections. Articles are blog-style content entries attached to a Blog and support full-text search. Tags are free-form labels that can be attached to any taggable resource (product, customer, order, article, page, and more) via the polymorphic Taggable join table. All four resources are scoped to the authenticated tenant company.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/admin/collections` | List collections (admin) |
| `POST` | `/2023-11/admin/collections` | Create a collection |
| `GET` | `/2023-11/admin/collections/handle/{collection}` | Get collection by handle (admin) |
| `GET` | `/2023-11/admin/collections/{collection}` | Get collection by ID (admin) |
| `PUT` | `/2023-11/admin/collections/{collection}` | Update a collection |
| `DELETE` | `/2023-11/admin/collections/{collection}` | Delete a collection |
| `POST` | `/2023-11/admin/collections/{collection}/products/link` | Link products to a collection |
| `GET` | `/2023-11/collections` | List collections (public) |
| `GET` | `/2023-11/collections/count` | Count all collections |
| `GET` | `/2023-11/collections/handle/{collection}` | Get collection by handle (public) |
| `GET` | `/2023-11/collections/handle/{collection}/products` | List products in collection by handle |
| `GET` | `/2023-11/collections/{collection}` | Get collection by ID (public) |
| `GET` | `/2023-11/collections/{collection}/products` | List products in collection by ID |
| `POST` | `/2023-11/admin/articles` | Create an article |
| `GET` | `/2023-11/admin/articles` | List articles (admin) |
| `GET` | `/2023-11/admin/articles/handle/{article}` | Get article by handle (admin) |
| `PUT` | `/2023-11/admin/articles/{article}` | Update an article |
| `GET` | `/2023-11/admin/articles/{article}` | Get article by ID (admin) |
| `DELETE` | `/2023-11/admin/articles/{article}` | Delete an article |
| `GET` | `/2023-11/articles` | List articles (public) |
| `GET` | `/2023-11/articles/count` | Count all articles |
| `GET` | `/2023-11/articles/handle/{article}` | Get article by handle (public) |
| `GET` | `/2023-11/articles/{article}` | Get article by ID (public) |
| `GET` | `/2023-11/admin/tags` | List tags |
| `POST` | `/2023-11/admin/tags` | Create a tag |
| `GET` | `/2023-11/admin/tags/{tag}` | Get tag by ID |
| `PUT` | `/2023-11/admin/tags/{tag}` | Update a tag |
| `DELETE` | `/2023-11/admin/tags/{tag}` | Delete a tag |
| `POST` | `/2023-11/admin/tags/{tag}/restore` | Restore a soft-deleted tag |
| `GET` | `/2023-11/admin/tags/{tag}/taggables` | List taggables for a tag |
| `POST` | `/2023-11/admin/taggables` | Create a taggable (stub) |
| `GET` | `/2023-11/admin/taggables` | List taggables |
| `GET` | `/2023-11/admin/taggables/handle/{taggable}` | Get taggable by handle (stub) |
| `PUT` | `/2023-11/admin/taggables/{taggable}` | Update a taggable (stub) |
| `GET` | `/2023-11/admin/taggables/{taggable}` | Get taggable by ID (stub) |
| `DELETE` | `/2023-11/admin/taggables/{taggable}` | Delete a taggable (stub) |

---

## Collections

A Collection groups products for navigation and merchandising. Collections support a single level of nesting via `parent_collection_id` and carry a cover image stored through Spatie Media Library (with `micro`, `thumb`, and `large` conversions).

---

### `GET` `/2023-11/admin/collections`

List collections for the authenticated company with filtering, sorting, and optional relation includes.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page (max 100) |
| `filter[title]` | string | No | — | Partial match on title |
| `filter[handle]` | string | No | — | Partial match on handle |
| `filter[content]` | string | No | — | Partial match on content |
| `filter[search]` | string | No | — | Full-text permissive search across `title`, `content`, `handle` |
| `filter[withProducts]` | any | No | — | Restrict to collections that have at least one product |
| `filter[updated_at][start]` | string (ISO 8601) | No | — | Filter collections updated on or after this date |
| `filter[updated_at][end]` | string (ISO 8601) | No | — | Filter collections updated on or before this date |
| `include` | string | No | — | Comma-separated: `tags`, `productsCount`, `subCollections`, `products` |
| `sort` | string | No | — | Sort field; prefix with `-` for descending. Allowed: `title`, `handle`, `content`, `products_count`, `sub_collections_count`, `created_at`, `updated_at` |
| `published_only` | integer | No | — | When `1`, the `products_count` appended field counts only published products |
| `products_published` | boolean | No | `true` | When including `products`, filters to published status (ignored if `any_products=1`) |
| `any_products` | integer | No | — | When `1`, includes products regardless of published status |
| `whereColumn` | string | No | — | Raw column equality filter (used with `whereOperator` and `whereValue`) |
| `whereOperator` | string | No | — | SQL operator for `whereColumn` filter |
| `whereValue` | string | No | — | Value for `whereColumn` filter |

**Request example:**

```http
GET /2023-11/admin/collections?filter[search]=fruits&include=tags,productsCount&sort=-created_at&per_page=20 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hea29gpx259j0mq164hxpt0f",
      "title": "Fruits & Vegetables",
      "handle": "fruits-vegetables",
      "content": "Fresh seasonal produce.",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "parent_collection_id": null,
      "metadata": { "shopifyId": "gid://shopify/Collection/123" },
      "products_count": 42,
      "medias": [
        {
          "id": 18,
          "link": "https://cdn.example.com/media/18/fruits.jpg",
          "collection_name": "default",
          "mime_type": "image/jpeg",
          "file_name": "fruits.jpg",
          "size": 204800,
          "model_id": "01hea29gpx259j0mq164hxpt0f",
          "model_type": "App\\Models\\Collection",
          "model_key": "collection",
          "folder_key": "company/01hqyd/collection",
          "micro": "https://cdn.example.com/media/18/conversions/fruits-micro.jpg",
          "thumb": "https://cdn.example.com/media/18/conversions/fruits-thumb.jpg",
          "large": "https://cdn.example.com/media/18/conversions/fruits-large.jpg",
          "webp": "https://cdn.example.com/media/18/conversions/fruits-webp.webp",
          "created_at": "2024-03-01T08:00:00.000000Z",
          "updated_at": "2024-03-01T08:00:00.000000Z"
        }
      ],
      "products": null,
      "tags": [
        { "id": "01hr...", "name": "seasonal", "company_id": "01hqydxwtxdj3kmzp3bz7jk73g", "taggables_count": 5, "created_at": "2024-01-10T00:00:00.000000Z", "updated_at": "2024-01-10T00:00:00.000000Z" }
      ],
      "created_at": "2024-03-01T08:00:00.000000Z",
      "updated_at": "2024-04-10T12:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 8,
    "count": 8,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

**Error responses:**

- `400` — Missing `X-Company` header (`company_id_required`)
- `401` — Invalid or expired token
- `403` — Token lacks the required OAuth scope (`invalid_scope`)

---

### `POST` `/2023-11/admin/collections`

Create a new collection. The handle is slugified from the `handle` field, falling back to `title`. Returns a 500-level error (as a runtime exception) if a collection with the resulting handle already exists for this company.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | — | Display name |
| `handle` | string | No | Slugified | URL-friendly identifier; falls back to `title` slug if omitted |
| `content` | string | No | — | HTML or plain-text description |
| `parent_collection_id` | string (ULID) | No | Existing collection ID | Parent collection for nesting |
| `metadata` | object | No | — | Arbitrary key-value data (e.g. external IDs) |
| `uploadImageUrl` | string (URL) | No | Valid URL | Remote image URL to attach as cover media |
| `uploadImageBase64` | string | No | Base64 encoded | Base64 image to attach as cover media (used if `uploadImageUrl` is absent) |

**Request example:**

```http
POST /2023-11/admin/collections HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Collection test",
  "handle": "handle-collection",
  "content": "Collection description"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hea29gpx259j0mq164hxpt0f",
  "title": "Collection test",
  "handle": "handle-collection",
  "content": "Collection description",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "parent_collection_id": null,
  "metadata": null,
  "products_count": 0,
  "medias": [],
  "products": null,
  "created_at": "2024-06-01T10:00:00.000000Z",
  "updated_at": "2024-06-01T10:00:00.000000Z"
}
```

**Error responses:**

- `400` — Missing `X-Company` header
- `401` — Invalid token
- `500` — A collection with this handle already exists for the company

---

### `GET` `/2023-11/admin/collections/handle/{collection}`

Retrieve a single collection by its URL handle.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string | Collection handle (e.g. `fruits-vegetables`) |

**Request example:**

```http
GET /2023-11/admin/collections/handle/fruits-vegetables HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hea29gpx259j0mq164hxpt0f",
  "title": "Fruits & Vegetables",
  "handle": "fruits-vegetables",
  "content": "Fresh seasonal produce.",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "parent_collection_id": null,
  "metadata": { "shopifyId": "gid://shopify/Collection/123" },
  "products_count": 42,
  "medias": [
    {
      "id": 18,
      "link": "https://cdn.example.com/media/18/fruits.jpg",
      "collection_name": "default",
      "mime_type": "image/jpeg",
      "file_name": "fruits.jpg",
      "size": 204800,
      "model_id": "01hea29gpx259j0mq164hxpt0f",
      "model_type": "App\\Models\\Collection",
      "model_key": "collection",
      "folder_key": "company/01hqyd/collection",
      "micro": "https://cdn.example.com/media/18/conversions/fruits-micro.jpg",
      "thumb": "https://cdn.example.com/media/18/conversions/fruits-thumb.jpg",
      "large": "https://cdn.example.com/media/18/conversions/fruits-large.jpg",
      "webp": "https://cdn.example.com/media/18/conversions/fruits-webp.webp",
      "created_at": "2024-03-01T08:00:00.000000Z",
      "updated_at": "2024-03-01T08:00:00.000000Z"
    }
  ],
  "products": null,
  "tags": [],
  "created_at": "2024-03-01T08:00:00.000000Z",
  "updated_at": "2024-04-10T12:00:00.000000Z"
}
```

**Error responses:**

- `401` — Invalid token
- `404` — No collection with this handle exists for the company

---

### `GET` `/2023-11/admin/collections/{collection}`

Retrieve a single collection by its ULID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string (ULID) | Collection ID |

**Request example:**

```http
GET /2023-11/admin/collections/01hea29gpx259j0mq164hxpt0f HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hea29gpx259j0mq164hxpt0f",
  "title": "Fruits & Vegetables",
  "handle": "fruits-vegetables",
  "content": "Fresh seasonal produce.",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "parent_collection_id": null,
  "metadata": { "shopifyId": "gid://shopify/Collection/123" },
  "products_count": 42,
  "medias": [
    {
      "id": 18,
      "link": "https://cdn.example.com/media/18/fruits.jpg",
      "collection_name": "default",
      "mime_type": "image/jpeg",
      "file_name": "fruits.jpg",
      "size": 204800,
      "model_id": "01hea29gpx259j0mq164hxpt0f",
      "model_type": "App\\Models\\Collection",
      "model_key": "collection",
      "folder_key": "company/01hqyd/collection",
      "micro": "https://cdn.example.com/media/18/conversions/fruits-micro.jpg",
      "thumb": "https://cdn.example.com/media/18/conversions/fruits-thumb.jpg",
      "large": "https://cdn.example.com/media/18/conversions/fruits-large.jpg",
      "webp": "https://cdn.example.com/media/18/conversions/fruits-webp.webp",
      "created_at": "2024-03-01T08:00:00.000000Z",
      "updated_at": "2024-03-01T08:00:00.000000Z"
    }
  ],
  "products": null,
  "tags": [],
  "created_at": "2024-03-01T08:00:00.000000Z",
  "updated_at": "2024-04-10T12:00:00.000000Z"
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Collection not found

---

### `PUT` `/2023-11/admin/collections/{collection}`

Update an existing collection. All fields are optional; only supplied fields are changed. Setting `uploadImageUrl` or `uploadImageBase64` replaces the existing cover media. Setting `parent_collection_id` to `null` (or omitting it) clears the parent.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string (ULID) | Collection ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | — | New display name; also re-slugifies the handle if provided without an explicit `handle` |
| `handle` | string | No | Slugified | New URL-friendly identifier |
| `content` | string | No | — | New description |
| `metadata` | object | No | — | Replaces the metadata object entirely |
| `parent_collection_id` | string (ULID)\|null | No | Existing collection ID | Pass `null` to detach from parent |
| `uploadImageUrl` | string (URL) | No | Valid URL | Replaces cover image with a remote URL |
| `uploadImageBase64` | string | No | Base64 encoded | Replaces cover image with a base64 upload |

**Request example:**

```http
PUT /2023-11/admin/collections/01hea29gpx259j0mq164hxpt0f HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Collection test",
  "handle": "handle-collection-2",
  "content": "Collection description"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hea29gpx259j0mq164hxpt0f",
  "title": "Collection test",
  "handle": "handle-collection-2",
  "content": "Collection description",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "parent_collection_id": null,
  "metadata": null,
  "products_count": 0,
  "medias": [],
  "products": null,
  "created_at": "2024-06-01T10:00:00.000000Z",
  "updated_at": "2024-06-02T09:30:00.000000Z"
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Collection not found

---

### `DELETE` `/2023-11/admin/collections/{collection}`

Delete a collection. The response body is empty (no `204` status code is set explicitly by this endpoint; the Laravel response defaults to `200` with no body).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string (ULID) | Collection ID |

**Request example:**

```http
DELETE /2023-11/admin/collections/01hea29gpx259j0mq164hxpt0f HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:** Empty body.

**Error responses:**

- `401` — Invalid token
- `404` — Collection not found

---

### `POST` `/2023-11/admin/collections/{collection}/products/link`

Attach one or more products to a collection. Duplicate attach errors are swallowed and reported in the `errors` array without failing the whole request.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string (ULID) | Collection ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `productIds` | array of strings (ULID) | Yes | Existing product IDs | Products to attach to the collection |

**Request example:**

```http
POST /2023-11/admin/collections/01hea29gpx259j0mq164hxpt0f/products/link HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "productIds": [
    "01hpqx3fmn0zz2r6vhxwabcd01",
    "01hpqx3fmn0zz2r6vhxwabcd02"
  ]
}
```

**Success response `200 OK`:**

```json
{
  "status": "terminated",
  "errors": []
}
```

If some products could not be linked (e.g. already attached), their error messages are listed in `errors`:

```json
{
  "status": "terminated",
  "errors": [
    "Error when linking Collection test with Product A. Message: Integrity constraint violation..."
  ]
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Collection not found

---

### `GET` `/2023-11/collections`

Public endpoint — list collections scoped to the company identified by `X-Company`. Supports the same filters, includes, and sorts as the admin list endpoint.

- **Query parameters:** Same as [`GET /2023-11/admin/collections`](#get-2023-11admincollections).

**Request example:**

```http
GET /2023-11/collections?filter[search]=aromes&include=productsCount HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hea29gpx259j0mq164hxpt0f",
      "title": "Fruits & Vegetables",
      "handle": "fruits-vegetables",
      "content": "Fresh seasonal produce.",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "parent_collection_id": null,
      "metadata": null,
      "products_count": 42,
      "medias": [],
      "products": null,
      "created_at": "2024-03-01T08:00:00.000000Z",
      "updated_at": "2024-04-10T12:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 8,
    "count": 8,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

**Error responses:**

- `400` — Missing `X-Company` header

---

### `GET` `/2023-11/collections/count`

Return the total number of collections for the company.

**Request example:**

```http
GET /2023-11/collections/count HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "count": 14
}
```

**Error responses:**

- `400` — Missing `X-Company` header

---

### `GET` `/2023-11/collections/handle/{collection}`

Public — retrieve a single collection by handle.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string | Collection handle |

**Request example:**

```http
GET /2023-11/collections/handle/aromes HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hea29gpx259j0mq164hxpt0f",
  "title": "Fruits & Vegetables",
  "handle": "fruits-vegetables",
  "content": "Fresh seasonal produce.",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "parent_collection_id": null,
  "metadata": null,
  "products_count": 42,
  "medias": [],
  "products": null,
  "created_at": "2024-03-01T08:00:00.000000Z",
  "updated_at": "2024-04-10T12:00:00.000000Z"
}
```

**Error responses:**

- `404` — Collection not found

---

### `GET` `/2023-11/collections/handle/{collection}/products`

Public — list all products belonging to the collection identified by handle. Returns the full `ProductDetailResource` shape for each product.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string | Collection handle |

**Request example:**

```http
GET /2023-11/collections/handle/aromes/products HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
[
  {
    "id": "01hpqx3fmn0zz2r6vhxwabcd01",
    "title": "Arome Vanille",
    "handle": "arome-vanille",
    "published": true,
    "content": "<p>Natural vanilla arome.</p>",
    "vendor": "GenukaShop",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "metadata": null,
    "service_id": null,
    "created_at": "2024-04-01T08:00:00.000000Z",
    "updated_at": "2024-05-01T12:00:00.000000Z",
    "deleted_at": null,
    "min_price": "2500.00",
    "compare_at_price": "3000.00",
    "remaining_stocks": 0,
    "first_variant": {
      "id": "01hpqx3var0000000000000001",
      "product_id": "01hpqx3fmn0zz2r6vhxwabcd01",
      "title": "Default Title",
      "sku": "AROME-V-001",
      "price": "2500.00",
      "compare_at_price": "3000.00",
      "follow_stock": false,
      "quantity": 0,
      "created_at": "2024-04-01T08:00:00.000000Z",
      "updated_at": "2024-05-01T12:00:00.000000Z"
    },
    "has_variants": false,
    "variants_count": 1,
    "follow_stock": false,
    "medias": [],
    "tags": [],
    "variants": [
      {
        "id": "01hpqx3var0000000000000001",
        "product_id": "01hpqx3fmn0zz2r6vhxwabcd01",
        "title": "Default Title",
        "sku": "AROME-V-001",
        "price": "2500.00",
        "compare_at_price": "3000.00",
        "follow_stock": false,
        "quantity": 0,
        "created_at": "2024-04-01T08:00:00.000000Z",
        "updated_at": "2024-05-01T12:00:00.000000Z"
      }
    ],
    "options": [],
    "menus": [],
    "shops": [],
    "supplier": null,
    "collections": []
  }
]
```

> The response is an unwrapped JSON array (no pagination wrapper), reflecting all products in the collection.

**Error responses:**

- `404` — Collection not found

---

### `GET` `/2023-11/collections/{collection}`

Public — retrieve a single collection by ULID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string (ULID) | Collection ID |

**Request example:**

```http
GET /2023-11/collections/01hea29gpx259j0mq164hxpt0f HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hea29gpx259j0mq164hxpt0f",
  "title": "Fruits & Vegetables",
  "handle": "fruits-vegetables",
  "content": "Fresh seasonal produce.",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "parent_collection_id": null,
  "metadata": null,
  "products_count": 42,
  "medias": [],
  "products": null,
  "created_at": "2024-03-01T08:00:00.000000Z",
  "updated_at": "2024-04-10T12:00:00.000000Z"
}
```

**Error responses:**

- `404` — Collection not found

---

### `GET` `/2023-11/collections/{collection}/products`

Public — list all products belonging to the collection identified by ULID. Identical behavior to the handle variant.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `collection` | string (ULID) | Collection ID |

**Request example:**

```http
GET /2023-11/collections/01hea29gpx259j0mq164hxpt0f/products HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
[
  {
    "id": "01hpqx3fmn0zz2r6vhxwabcd01",
    "title": "Arome Vanille",
    "handle": "arome-vanille",
    "published": true,
    "min_price": "2500.00",
    "compare_at_price": "3000.00",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "created_at": "2024-04-01T08:00:00.000000Z",
    "updated_at": "2024-05-01T12:00:00.000000Z"
  }
]
```

> The response is an unwrapped JSON array (no pagination wrapper), reflecting all products in the collection.

**Error responses:**

- `404` — Collection not found

---

## Articles

Articles are rich-content entries (blog posts, guides) associated with a Blog and optionally tagged. Each article can carry a cover image and be toggled between draft and published.

---

### `POST` `/2023-11/admin/articles`

Create a new article.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | — | Article title |
| `handle` | string | No | Slugified | URL handle; falls back to `title` slug |
| `content` | string | No | — | HTML body |
| `description` | string | No | — | Short excerpt |
| `published` | boolean | No | `false` | Whether the article is publicly visible |
| `author` | string | No | — | Author name |
| `blog_id` | string (ULID) | No | Existing blog ID | Blog this article belongs to |
| `metadata` | object | No | — | Arbitrary key-value data |
| `created_at` | string (ISO 8601) | No | Current time | Override creation timestamp (useful for imports) |
| `updated_at` | string (ISO 8601) | No | Current time | Override updated timestamp |
| `uploadImageUrl` | string (URL) | No | Valid URL | Remote image to attach as cover |
| `uploadImageBase64` | string | No | Base64 encoded | Base64 image to attach as cover (used if `uploadImageUrl` absent) |

**Request example:**

```http
POST /2023-11/admin/articles HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Title",
  "handle": "handle-herzcde",
  "metadata": { "shopifyId": "id" },
  "content": "body_html"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
  "title": "Title",
  "handle": "handle-herzcde",
  "description": null,
  "published": false,
  "author": null,
  "content": "body_html",
  "metadata": { "shopifyId": "id" },
  "blog_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-06-01T10:00:00.000000Z",
  "updated_at": "2024-06-01T10:00:00.000000Z",
  "blog": null,
  "medias": []
}
```

**Error responses:**

- `400` — Missing `X-Company` header
- `401` — Invalid token

---

### `GET` `/2023-11/admin/articles`

List articles for the authenticated company with filtering, sorting, and optional includes.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[published]` | boolean | No | — | Exact match on `published` flag |
| `filter[blog_id]` | string (ULID) | No | — | Exact match on blog ID |
| `filter[blog_handle]` | string | No | — | Filter by associated blog's handle |
| `filter[search]` | string | No | — | Full-text search across `title`, `handle`, `author`, `description`, `content`, associated `tags.title`, and `blog.title`/`blog.handle` |
| `filter[created_at][start]` | string (ISO 8601) | No | — | Articles created on or after this date |
| `filter[created_at][end]` | string (ISO 8601) | No | — | Articles created on or before this date |
| `include` | string | No | — | Comma-separated: `tags`, `blog` |
| `sort` | string | No | `title` | Prefix with `-` for descending. Allowed: `title`, `handle`, `published`, `vendor`, `created_at`, `updated_at` |
| `whereColumn` | string | No | — | Raw column filter (combined with `whereOperator` and `whereValue`) |
| `whereOperator` | string | No | — | SQL operator; use `in` to match multiple values (provide `whereValue` as JSON array string) |
| `whereValue` | string | No | — | Value for `whereColumn` filter |

**Request example:**

```http
GET /2023-11/admin/articles?filter[published]=true&filter[blog_handle]=our-blog&include=tags&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
      "title": "8 bienfaits du melon",
      "handle": "08-bienfaits-du-melon",
      "description": "Discover why melon is a superfruit.",
      "published": true,
      "author": "Jane Doe",
      "content": "<p>Melon is...</p>",
      "metadata": null,
      "blog_id": "01hp9x3f0m0zz2r6vhxwaaaa01",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-05-10T08:30:00.000000Z",
      "updated_at": "2024-05-20T14:00:00.000000Z",
      "blog": {
        "id": "01hp9x3f0m0zz2r6vhxwaaaa01",
        "title": "Our Blog",
        "handle": "our-blog"
      },
      "medias": [],
      "tags": [
        { "id": "01hr...", "name": "nutrition", "company_id": "01hqydxwtxdj3kmzp3bz7jk73g", "created_at": "...", "updated_at": "..." }
      ]
    }
  ],
  "pagination": {
    "total": 25,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 2
  }
}
```

**Error responses:**

- `400` — Missing `X-Company` header
- `401` — Invalid token

---

### `GET` `/2023-11/admin/articles/handle/{article}`

Retrieve a single article by its URL handle.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `article` | string | Article handle |

**Request example:**

```http
GET /2023-11/admin/articles/handle/08-bienfaits-du-melon HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
  "title": "8 bienfaits du melon",
  "handle": "08-bienfaits-du-melon",
  "description": "Discover why melon is a superfruit.",
  "published": true,
  "author": "Jane Doe",
  "content": "<p>Melon is...</p>",
  "metadata": null,
  "blog_id": "01hp9x3f0m0zz2r6vhxwaaaa01",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-05-10T08:30:00.000000Z",
  "updated_at": "2024-05-20T14:00:00.000000Z",
  "blog": {
    "id": "01hp9x3f0m0zz2r6vhxwaaaa01",
    "title": "Our Blog",
    "handle": "our-blog"
  },
  "medias": []
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Article not found

---

### `GET` `/2023-11/admin/articles/{article}`

Retrieve a single article by ULID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `article` | string (ULID) | Article ID |

**Request example:**

```http
GET /2023-11/admin/articles/01hpar4kbx8z5m2yq7ntw3cd8e HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
  "title": "8 bienfaits du melon",
  "handle": "08-bienfaits-du-melon",
  "description": "Discover why melon is a superfruit.",
  "published": true,
  "author": "Jane Doe",
  "content": "<p>Melon is...</p>",
  "metadata": null,
  "blog_id": "01hp9x3f0m0zz2r6vhxwaaaa01",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-05-10T08:30:00.000000Z",
  "updated_at": "2024-05-20T14:00:00.000000Z",
  "blog": {
    "id": "01hp9x3f0m0zz2r6vhxwaaaa01",
    "title": "Our Blog",
    "handle": "our-blog"
  },
  "medias": []
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Article not found

---

### `PUT` `/2023-11/admin/articles/{article}`

Update an article. All fields are optional; only the supplied fields are changed.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `article` | string (ULID) | Article ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | — | New title |
| `handle` | string | No | — | New handle (not automatically slugified on update) |
| `content` | string | No | — | New HTML body |
| `author` | string | No | — | New author |
| `published` | boolean | No | — | New published state |
| `metadata` | object | No | — | Replaces the metadata object |
| `blog_id` | string (ULID) | No | Existing blog ID | Move article to a different blog |
| `uploadImageUrl` | string (URL) | No | Valid URL | Replaces cover image with a remote URL |
| `uploadImageBase64` | string | No | Base64 encoded | Replaces cover image with a base64 upload |

**Request example:**

```http
PUT /2023-11/admin/articles/01hpar4kbx8z5m2yq7ntw3cd8e HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Title",
  "handle": "handle-herzcde",
  "metadata": { "shopifyId": "id" },
  "content": "body_html"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
  "title": "Title",
  "handle": "handle-herzcde",
  "description": null,
  "published": false,
  "author": null,
  "content": "body_html",
  "metadata": { "shopifyId": "id" },
  "blog_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-06-01T10:00:00.000000Z",
  "updated_at": "2024-06-02T09:30:00.000000Z",
  "blog": null,
  "medias": []
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Article not found

---

### `DELETE` `/2023-11/admin/articles/{article}`

Permanently delete an article.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `article` | string (ULID) | Article ID |

**Request example:**

```http
DELETE /2023-11/admin/articles/01hpar4kbx8z5m2yq7ntw3cd8e HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `204 No Content`:** Empty body.

**Error responses:**

- `401` — Invalid token
- `404` — Article not found

---

### `GET` `/2023-11/articles`

Public — list articles for the company. Supports the same filters, includes, and sorts as the admin list endpoint.

- **Query parameters:** Same as [`GET /2023-11/admin/articles`](#get-2023-11adminarticles).

**Request example:**

```http
GET /2023-11/articles?filter[published]=true&include=tags,blog&sort=-created_at HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
      "title": "8 bienfaits du melon",
      "handle": "08-bienfaits-du-melon",
      "description": "Discover why melon is a superfruit.",
      "published": true,
      "author": "Jane Doe",
      "content": "<p>Melon is...</p>",
      "metadata": null,
      "blog_id": "01hp9x3f0m0zz2r6vhxwaaaa01",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-05-10T08:30:00.000000Z",
      "updated_at": "2024-05-20T14:00:00.000000Z",
      "blog": {
        "id": "01hp9x3f0m0zz2r6vhxwaaaa01",
        "title": "Our Blog",
        "handle": "our-blog"
      },
      "medias": []
    }
  ],
  "pagination": {
    "total": 47,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 4
  }
}
```

---

### `GET` `/2023-11/articles/count`

Return the total number of articles for the company.

**Request example:**

```http
GET /2023-11/articles/count HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "count": 47
}
```

---

### `GET` `/2023-11/articles/handle/{article}`

Public — retrieve a single article by handle.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `article` | string | Article handle |

**Request example:**

```http
GET /2023-11/articles/handle/08-bienfaits-du-melon HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
  "title": "8 bienfaits du melon",
  "handle": "08-bienfaits-du-melon",
  "description": "Discover why melon is a superfruit.",
  "published": true,
  "author": "Jane Doe",
  "content": "<p>Melon is...</p>",
  "metadata": null,
  "blog_id": "01hp9x3f0m0zz2r6vhxwaaaa01",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-05-10T08:30:00.000000Z",
  "updated_at": "2024-05-20T14:00:00.000000Z",
  "blog": {
    "id": "01hp9x3f0m0zz2r6vhxwaaaa01",
    "title": "Our Blog",
    "handle": "our-blog"
  },
  "medias": []
}
```

**Error responses:**

- `404` — Article not found

---

### `GET` `/2023-11/articles/{article}`

Public — retrieve a single article by ULID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `article` | string (ULID) | Article ID |

**Request example:**

```http
GET /2023-11/articles/01hpar4kbx8z5m2yq7ntw3cd8e HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hpar4kbx8z5m2yq7ntw3cd8e",
  "title": "8 bienfaits du melon",
  "handle": "08-bienfaits-du-melon",
  "description": "Discover why melon is a superfruit.",
  "published": true,
  "author": "Jane Doe",
  "content": "<p>Melon is...</p>",
  "metadata": null,
  "blog_id": "01hp9x3f0m0zz2r6vhxwaaaa01",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-05-10T08:30:00.000000Z",
  "updated_at": "2024-05-20T14:00:00.000000Z",
  "blog": {
    "id": "01hp9x3f0m0zz2r6vhxwaaaa01",
    "title": "Our Blog",
    "handle": "our-blog"
  },
  "medias": []
}
```

**Error responses:**

- `404` — Article not found

---

## Tags

Tags are company-scoped labels attached to any supported resource through a polymorphic `taggables` join table. Supported taggable types are: `shop`, `customer`, `order`, `user`, `product`, `blog`, `page`, `article`. The tag create and index endpoints can optionally target a specific resource by passing `model_type` + `model_id`.

---

### `GET` `/2023-11/admin/tags`

List all tags for the company. Optionally retrieve only tags for a specific resource instance by passing `model_type` and `model_id`.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `model_type` | string | No | — | One of: `shop`, `customer`, `order`, `user`, `product`, `blog`, `page`, `article`. When provided with `model_id`, returns only that resource's tags (bypasses pagination) |
| `model_id` | string (ULID) | No | — | ID of the resource; used together with `model_type` |
| `filter[id]` | string (ULID) | No | — | Exact match on tag ID |
| `filter[name]` | string | No | — | Partial match on tag name |
| `filter[search]` | string | No | — | Search by name |
| `filter[type]` | string | No | — | Filter to tags used by a specific resource type (same values as `model_type`) |
| `include` | string | No | — | `taggables` to include all taggable pivot records |
| `sort` | string | No | — | Allowed: `name`, `created_at`, `updated_at`, `taggables_count` |

**Request example:**

```http
GET /2023-11/admin/tags?filter[search]=seasonal&sort=-taggables_count HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hr3xytag000000000000001a",
      "name": "seasonal",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "taggables_count": 12,
      "created_at": "2024-01-10T00:00:00.000000Z",
      "updated_at": "2024-01-10T00:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 1,
    "count": 1,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

> When `model_type` + `model_id` are provided, the response is an unwrapped JSON array of tag objects (no pagination wrapper).

**Error responses:**

- `400` — Missing `X-Company` header
- `401` — Invalid token
- `404` — Resource (`model_type`/`model_id`) not found
- `422` — `model_type` is not one of the allowed values

---

### `POST` `/2023-11/admin/tags`

Create a new tag for the company. If a tag with the same name already exists, the existing tag is returned (idempotent). Optionally attach the new (or existing) tag directly to a resource by supplying `model_type` and `model_id`.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | `max:255` | Tag name |
| `model_type` | string | No | One of the allowed resource types | When provided with `model_id`, attaches the tag to this resource |
| `model_id` | string (ULID) | No | Existing resource ID | Resource to attach the tag to |

**Request example:**

```http
POST /2023-11/admin/tags HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "seasonal"
}
```

**Success response `201 Created`** (new tag) **or `200 OK`** (existing tag returned):

```json
{
  "id": "01hr3xytag000000000000001a",
  "name": "seasonal",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "taggables_count": null,
  "created_at": "2024-01-10T00:00:00.000000Z",
  "updated_at": "2024-01-10T00:00:00.000000Z"
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Resource (`model_type`/`model_id`) not found
- `422` — `name` required or `model_type` not in allowed list

---

### `GET` `/2023-11/admin/tags/{tag}`

Retrieve a single tag by ULID. Include `?includes=taggables` to load all attached taggable pivot records with their associated taggable entities.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `tag` | string (ULID) | Tag ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `includes` | string | No | — | Pass `taggables` to sideload taggable pivot records |

**Request example:**

```http
GET /2023-11/admin/tags/01hr3xytag000000000000001a?includes=taggables HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "id": "01hr3xytag000000000000001a",
  "name": "seasonal",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "taggables_count": 3,
  "created_at": "2024-01-10T00:00:00.000000Z",
  "updated_at": "2024-01-10T00:00:00.000000Z",
  "taggables": [
    {
      "id": "01hr3xtbl000000000000000a1",
      "tag_id": "01hr3xytag000000000000001a",
      "taggable_id": "01hpqx3fmn0zz2r6vhxwabcd01",
      "taggable_type": "App\\Models\\Product",
      "is_auto": false,
      "metadata": null,
      "tag_name": "seasonal",
      "type": "product",
      "taggable_label": "Arome Vanille",
      "created_at": "2024-02-01T00:00:00.000000Z",
      "updated_at": "2024-02-01T00:00:00.000000Z"
    }
  ]
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Tag not found

---

### `PUT` `/2023-11/admin/tags/{tag}`

Rename a tag.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `tag` | string (ULID) | Tag ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | `max:255` | New tag name |

**Request example:**

```http
PUT /2023-11/admin/tags/01hr3xytag000000000000001a HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "seasonal-produce"
}
```

**Success response `200 OK`:**

```json
{
  "id": "01hr3xytag000000000000001a",
  "name": "seasonal-produce",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "taggables_count": 3,
  "created_at": "2024-01-10T00:00:00.000000Z",
  "updated_at": "2024-06-02T09:30:00.000000Z"
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Tag not found
- `422` — `name` required

---

### `DELETE` `/2023-11/admin/tags/{tag}`

Delete a tag. The `taggables` pivot rows are removed via database cascade.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `tag` | string (ULID) | Tag ID |

**Request example:**

```http
DELETE /2023-11/admin/tags/01hr3xytag000000000000001a HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "message": "Tag deleted."
}
```

**Error responses:**

- `401` — Invalid token
- `404` — Tag not found

---

### `POST` `/2023-11/admin/tags/{tag}/restore`

Restore a soft-deleted tag.

> **Note:** The `restore` method is declared in the route but is not yet implemented in `TagController`. Calling this endpoint will result in a `500` error until the method is added.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `tag` | string (ULID) | Tag ID |

**Request example:**

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/tags/01hr3xytag000000000000001a/restore HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK` (anticipated once implemented):**

```json
{
  "id": "01hr3xytag000000000000001a",
  "name": "seasonal",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "taggables_count": 3,
  "created_at": "2024-01-10T00:00:00.000000Z",
  "updated_at": "2024-06-02T09:30:00.000000Z"
}
```

**Error responses:**

- `500` — Method not implemented

---

### `GET` `/2023-11/admin/tags/{tag}/taggables`

List all taggable pivot records for a specific tag, with pagination and filtering.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `tag` | string (ULID) | Tag ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[taggable_type]` | string | No | — | Exact match on the fully-qualified class name (e.g. `App\Models\Product`) |
| `sort` | string | No | — | Allowed: `created_at`, `updated_at` |

**Request example:**

```http
GET /2023-11/admin/tags/01hr3xytag000000000000001a/taggables?filter[taggable_type]=App\Models\Product HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hr3xtbl000000000000000a1",
      "tag_id": "01hr3xytag000000000000001a",
      "taggable_id": "01hpqx3fmn0zz2r6vhxwabcd01",
      "taggable_type": "App\\Models\\Product",
      "is_auto": false,
      "metadata": null,
      "tag_name": "seasonal",
      "type": "product",
      "taggable_label": "Arome Vanille",
      "tag": {
        "id": "01hr3xytag000000000000001a",
        "name": "seasonal",
        "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "created_at": "2024-01-10T00:00:00.000000Z",
        "updated_at": "2024-01-10T00:00:00.000000Z"
      },
      "taggable": {
        "id": "01hpqx3fmn0zz2r6vhxwabcd01",
        "type": "product",
        "title": "Arome Vanille"
      },
      "created_at": "2024-02-01T00:00:00.000000Z",
      "updated_at": "2024-02-01T00:00:00.000000Z"
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

**Error responses:**

- `401` — Invalid token
- `404` — Tag not found

---

## Taggables

The Taggable resource represents a single tag–resource association. Most write operations (`create`, `show by handle`, `update`, `show`, `destroy`) are **stub endpoints** — their controller methods are registered but have no implementation, and will return an empty `200 OK` or unexpected behavior until implemented.

> The only fully implemented endpoints in this resource group are **List** and the tag-scoped **List taggables** described under [Tags](#tags).

---

### `POST` `/2023-11/admin/taggables`

Create a taggable association. **Not implemented** — the `store` method is an empty stub.

- **Request body (anticipated — not validated by the stub):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `tag_id` | string (ULID) | Yes | Existing tag ID | The tag to attach |
| `taggable_id` | string (ULID) | Yes | Existing resource ID | ID of the resource to tag |
| `taggable_type` | string | Yes | Fully-qualified class name | Polymorphic type, e.g. `App\Models\Product` |
| `is_auto` | boolean | No | — | Whether the association was created automatically |
| `metadata` | object | No | — | Arbitrary key-value data |

**Request example:**

```http
POST /2023-11/admin/taggables HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{}
```

**Success response `200 OK`:**

```json
{}
```

> Returns an empty `200 OK` until the method is implemented.

---

### `GET` `/2023-11/admin/taggables`

List all taggable pivot records for the company, filtered to records whose tag belongs to the authenticated company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[tag_id]` | string (ULID) | No | — | Exact match on tag ID |
| `filter[taggable_type]` | string | No | — | Exact match on taggable type class name (e.g. `App\Models\Product`) |
| `filter[taggable_id]` | string (ULID) | No | — | Exact match on taggable resource ID |
| `filter[search]` | string | No | — | Scored search across `title`/`name` columns and associated tag fields |
| `sort` | string | No | — | Allowed: `name`, `created_at`, `updated_at` |

**Request example:**

```http
GET /2023-11/admin/taggables?filter[tag_id]=01hr3xytag000000000000001a&filter[taggable_type]=App\Models\Product HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response `200 OK`:**

```json
{
  "data": [
    {
      "id": "01hr3xtbl000000000000000a1",
      "tag_id": "01hr3xytag000000000000001a",
      "taggable_id": "01hpqx3fmn0zz2r6vhxwabcd01",
      "taggable_type": "App\\Models\\Product",
      "is_auto": false,
      "metadata": null,
      "tag_name": "seasonal",
      "type": "product",
      "taggable_label": "Arome Vanille",
      "tag": {
        "id": "01hr3xytag000000000000001a",
        "name": "seasonal",
        "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "created_at": "2024-01-10T00:00:00.000000Z",
        "updated_at": "2024-01-10T00:00:00.000000Z"
      },
      "created_at": "2024-02-01T00:00:00.000000Z",
      "updated_at": "2024-02-01T00:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 15,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

**Error responses:**

- `400` — Missing `X-Company` header
- `401` — Invalid token

---

### `GET` `/2023-11/admin/taggables/handle/{taggable}`

Retrieve a taggable by handle. **Not implemented** — the `show` method is an empty stub.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `taggable` | string | Taggable handle |

**Success response `200 OK`:**

```json
{}
```

> Returns an empty `200 OK` until the method is implemented.

---

### `GET` `/2023-11/admin/taggables/{taggable}`

Retrieve a single taggable by ULID. **Not implemented** — the `show` method is an empty stub.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `taggable` | string (ULID) | Taggable ID |

**Success response `200 OK`:**

```json
{}
```

> Returns an empty `200 OK` until the method is implemented.

---

### `PUT` `/2023-11/admin/taggables/{taggable}`

Update a taggable record. **Not implemented** — the `update` method is an empty stub.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `taggable` | string (ULID) | Taggable ID |

- **Request body (anticipated — not validated by the stub):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `tag_id` | string (ULID) | No | Existing tag ID | Replace the associated tag |
| `taggable_id` | string (ULID) | No | Existing resource ID | Replace the tagged resource |
| `taggable_type` | string | No | Fully-qualified class name | Replace the polymorphic type, e.g. `App\Models\Product` |
| `is_auto` | boolean | No | — | Toggle whether the association was created automatically |
| `metadata` | object | No | — | Replace the metadata object |

**Success response `200 OK`:**

```json
{}
```

> Returns an empty `200 OK` until the method is implemented.

---

### `DELETE` `/2023-11/admin/taggables/{taggable}`

Delete a taggable record. **Not implemented** — the `destroy` method is an empty stub.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `taggable` | string (ULID) | Taggable ID |

**Success response `200 OK`:**

```json
{}
```

> Returns an empty `200 OK` until the method is implemented.
