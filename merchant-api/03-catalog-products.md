---
title: "Catalog — Products, Variants & Options"
description: "The Catalog domain manages the full product lifecycle: physical goods, digital items, and bookable services. Each **Product** may have multiple **Variants** (SK"
sidebarTitle: "Products & Variants"
---

The Catalog domain manages the full product lifecycle: physical goods, digital items, and bookable services. Each **Product** may have multiple **Variants** (SKU-level records) and **Options** (size, color, etc.). The domain also exposes **Custom Fields** (per-model metadata schema definitions), **Custom Objects** (freeform data schemas with typed fields), and a generic **Table Model** query endpoint for direct table access.

There are two route groups: `2023-11/admin/…` (authenticated, requires `X-Company`) and `2023-11/…` (public, scoped to a storefront via `X-Shop`).

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/admin/products` | List products (admin) |
| `POST` | `/2023-11/admin/products` | Create a product |
| `POST` | `/2023-11/admin/products/bulk-assign-professionals` | Bulk-assign professionals to service variants |
| `POST` | `/2023-11/admin/products/export` | Export products (async, xlsx/csv/pdf) |
| `GET` | `/2023-11/admin/products/export/fields` | List available export columns |
| `GET` | `/2023-11/admin/products/handle/{product}` | Get product by handle (admin) |
| `POST` | `/2023-11/admin/products/import` | Import products from file (async) |
| `POST` | `/2023-11/admin/products/import-chunk` | Upload a single import chunk |
| `POST` | `/2023-11/admin/products/merge` | Merge multiple products into one |
| `POST` | `/2023-11/admin/products/merge-chunk` | Merge uploaded chunks and start import job |
| `POST` | `/2023-11/admin/products/migration` | Create product for data migration |
| `POST` | `/2023-11/admin/products/migration/{product}/options` | Create option for migration product |
| `POST` | `/2023-11/admin/products/migration/{product}/variants` | Create variant for migration product |
| `GET` | `/2023-11/admin/products/{product}` | Get product by ID (admin) |
| `PUT` | `/2023-11/admin/products/{product}` | Update a product |
| `DELETE` | `/2023-11/admin/products/{product}` | Delete (soft) a product |
| `GET` | `/2023-11/admin/products/{product}/analytics` | Aggregated analytics for a product |
| `GET` | `/2023-11/admin/products/{product}/details-by-shops` | Per-shop revenue/profit breakdown |
| `GET` | `/2023-11/admin/products/{product}/global-rank` | Global sales rank of a product |
| `POST` | `/2023-11/admin/products/{product}/options` | Create an option for a product |
| `GET` | `/2023-11/admin/products/{product}/quantity-sold` | Quantity-sold time-series |
| `GET` | `/2023-11/admin/products/{product}/rank-in-shops` | Per-shop rank for a product |
| `POST` | `/2023-11/admin/products/{product}/restore` | Restore a soft-deleted product |
| `GET` | `/2023-11/admin/products/{product}/sales` | Revenue time-series for a product |
| `GET` | `/2023-11/admin/products/{product}/sales-by-gender` | Sales breakdown by customer gender |
| `GET` | `/2023-11/admin/products/{product}/sales-by-shop` | Sales breakdown by shop |
| `GET` | `/2023-11/admin/products/{product}/sales-by-type-of-customer` | Sales breakdown by customer type |
| `GET` | `/2023-11/admin/products/{product}/top-customers` | Top customers for a product |
| `GET` | `/2023-11/admin/products/{product}/top-related-products` | Products most often co-purchased |
| `POST` | `/2023-11/admin/products/{product}/variants` | Create a variant for a product |
| `PUT` | `/2023-11/admin/products/{product}/{field}` | Update a single field of a product |
| `GET` | `/2023-11/products` | List products (public) |
| `GET` | `/2023-11/products/count` | Count all products |
| `GET` | `/2023-11/products/handle/{handle}` | Get published product by handle (public) |
| `GET` | `/2023-11/products/handle/{product}/similar` | Get similar products by handle |
| `GET` | `/2023-11/products/{product}` | Get product by ID (public) |
| `GET` | `/2023-11/products/{product}/similar` | Get similar products by ID |
| `GET` | `/2023-11/products/{product}/variants/{variant}` | Get a specific variant of a product |
| `GET` | `/2023-11/admin/productVariants` | List product variants (admin) |
| `GET` | `/2023-11/admin/productVariants/barcode/{barcode}` | Lookup variant by barcode |
| `GET` | `/2023-11/admin/productVariants/{productVariant}` | Get a variant by ID (admin) |
| `GET` | `/2023-11/productVariants` | List product variants (public) |
| `GET` | `/2023-11/productVariants/{productVariant}` | Get a variant by ID (public) |
| `GET` | `/2023-11/admin/custom-fields` | List custom field definitions |
| `POST` | `/2023-11/admin/custom-fields` | Create a custom field definition |
| `GET` | `/2023-11/admin/custom-fields/{customField}` | Get a custom field definition |
| `PUT` | `/2023-11/admin/custom-fields/{customField}` | Update a custom field definition |
| `DELETE` | `/2023-11/admin/custom-fields/{customField}` | Delete a custom field definition |
| `GET` | `/2023-11/admin/custom-objects` | List custom object schemas (admin) |
| `POST` | `/2023-11/admin/custom-objects` | Create a custom object schema |
| `GET` | `/2023-11/admin/custom-objects/{customObject}` | Get a custom object schema |
| `PUT` | `/2023-11/admin/custom-objects/{customObject}` | Update a custom object schema |
| `DELETE` | `/2023-11/admin/custom-objects/{customObject}` | Delete a custom object schema |
| `GET` | `/2023-11/admin/custom-objects/{customObject}/records` | List records of a custom object (admin) |
| `POST` | `/2023-11/admin/custom-objects/{customObject}/records` | Create a record in a custom object |
| `GET` | `/2023-11/admin/custom-objects/{customObject}/records/{record}` | Get a custom object record |
| `PUT` | `/2023-11/admin/custom-objects/{customObject}/records/{record}` | Update a custom object record |
| `DELETE` | `/2023-11/admin/custom-objects/{customObject}/records/{record}` | Delete a custom object record |
| `GET` | `/2023-11/custom-objects` | List public custom object schemas |
| `GET` | `/2023-11/custom-objects/{customObject}` | Get a public custom object schema |
| `GET` | `/2023-11/custom-objects/{customObject}/records` | List records (public) |
| `POST` | `/2023-11/custom-objects/{customObject}/records` | Create a record (public, if allowed) |
| `GET` | `/2023-11/custom-objects/{customObject}/records/{record}` | Get a record (public) |
| `GET` | `/2023-11/admin/table_models/{table}` | Query any allowed table generically |

---

## Products (Admin)

### `GET` `/2023-11/admin/products`
List products for the authenticated company with rich filtering, sorting, and include support.

**Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | no | 1 | Page number |
| `per_page` | integer | no | 15 | Results per page (max 100) |
| `simple_paginate` | boolean | no | false | Use `simplePaginate` (no total count) for better performance |
| `sort` | string | no | `title` | Sort field. Prefix with `-` for descending. Allowed: `title`, `handle`, `price`, `stocks`, `has_stock_first`, `tags`, `total_orders`, `total_ordered_amount`, `published`, `vendor`, `ordersCount`, `collections`, `created_at`, `updated_at` |
| `include` | string | no | — | Comma-separated relations: `collections`, `shops`, `orders`, `stocks`, `supplier`, `variants`, `variants.supplierproduct`, `variants.stocks`, `tags`, `options`, `variants.warehouseQuantities`, `variants.composite_stocks`, `service.professionals`, `variants.service.professionals`, `total_ordered_quantity`, `total_ordered_amount` |
| `fields` | string | no | — | Comma-separated sparse fieldset: `variants`, `medias`, `tags`, `options`, `menus`, `shops`, `supplier`, `collections`, `remaining_stocks`, `min_price`, `compare_at_price`, `preview_image` |
| `filter[published]` | integer | no | — | `1` published only, `0` unpublished only |
| `filter[type]` | string | no | — | Product type, e.g. `service`, `physical_goods` |
| `filter[supplier_id]` | string | no | — | Filter by supplier ULID |
| `filter[active]` | boolean | no | — | Pass `false` to include soft-deleted products |
| `filter[has_variants]` | boolean | no | — | Filter products that have at least one variant |
| `filter[has_stocks]` | boolean | no | — | Filter products with available stock |
| `filter[tags]` | string | no | — | Comma-separated tag names |
| `filter[optionValues]` | string | no | — | Comma-separated option values |
| `filter[priceRange][]` | number | no | — | Two-element array: `filter[priceRange][]=100&filter[priceRange][]=500` |
| `filter[collections]` | string | no | — | Comma-separated collection ULIDs |
| `filter[shops]` | string | no | — | Comma-separated shop ULIDs |
| `filter[suppliers]` | string | no | — | Comma-separated supplier ULIDs |
| `filter[ids][]` | array | no | — | Filter by exact product IDs |
| `filter[search]` | string | no | — | Full-text search (title, handle, vendor, variant title/barcode/sku, tags, options, metadata) |
| `filter[created_at][start]` | date | no | — | Created at range start (ISO 8601) |
| `filter[created_at][end]` | date | no | — | Created at range end |
| `filter[updated_at][start]` | date | no | — | Updated at range start |
| `filter[updated_at][end]` | date | no | — | Updated at range end |
| `whereColumn` | string | no | — | Raw column filter (e.g. `metadata->shopifyId`) |
| `whereOperator` | string | no | `=` | Operator; use `in` for array values |
| `whereValue` | string | no | — | Value; for `in`, a JSON-encoded array |
| `tags` | string | no | — | Tag filter used for the `tags` sort |
| `startDate` | date | no | — | Used when including `total_ordered_amount` |
| `endDate` | date | no | — | Used when including `total_ordered_amount` |
| `all` | boolean | no | — | Include soft-deleted products (`withTrashed`) |

Request example:

```http
GET /2023-11/admin/products?page=1&per_page=15&sort=-created_at&include=shops,tags&filter[published]=1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01k74h4acesr6y7vp6krqt5dqf",
      "title": "Arachide blanc du ndole",
      "handle": "arachide-blanc-du-ndole",
      "content": "Une description ici",
      "published": 1,
      "type": "physical_goods",
      "vendor": null,
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": null,
      "is_shippable": 1,
      "is_taxable": 1,
      "created_at": "2025-10-09T13:03:54.000000Z",
      "updated_at": "2025-10-09T13:03:54.000000Z",
      "deleted_at": null,
      "min_price": 6500.00,
      "compare_at_price": null,
      "first_variant": { "...": "ProductVariantResource" },
      "has_variants": true,
      "variants_count": 1,
      "follow_stock": false,
      "remaining_stocks": null,
      "medias": [],
      "tags": [],
      "options": [],
      "menus": [],
      "shops": [],
      "supplier": null
    }
  ],
  "pagination": {
    "total": 84,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 6
  }
}
```

Errors:
- `400` — missing `X-Company` header (`company_id_required`)
- `401` — invalid or expired token
- `422` — invalid filter/sort column

---

### `POST` `/2023-11/admin/products`
Create a new product (physical good or service). Variants and options can be embedded in the same request.

**Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | yes | max:255 | Product title |
| `handle` | string | no | slug | URL-safe handle; auto-slugified |
| `content` | string | no | — | Product description / HTML body |
| `published` | integer/boolean | no | `0` or `1` | Whether the product is publicly visible |
| `type` | string | no | — | Product type (e.g. `physical_goods`, `service`) |
| `vendor` | string | no | — | Brand or vendor name |
| `metadata` | object | no | — | Arbitrary key-value metadata |
| `is_shippable` | boolean | no | — | Whether the product requires shipping |
| `is_taxable` | boolean | no | — | Whether the product is taxable |
| `supplier_id` | string | no | ULID | Linked supplier ID |
| `price` | number | no | — | Shorthand price (used for service-type products) |
| `duration_minutes` | integer | no | — | Service duration in minutes |
| `buffer_minutes` | integer | no | — | Buffer time between bookings (minutes) |
| `professionals` | array | no | `[{id}]` | Professionals who can perform this service |
| `variants` | array | no | — | Array of variant objects (see variant fields below) |
| `options` | array | no | — | Array of option objects |
| `medias` | array | no | — | Media file attachments |
| `tags` | array | no | `[{name}]` | Tags to attach; new tags are created automatically |
| `images` | array | no | `[{src}]` | Images by URL (used during migration) |

*Variant object fields:*

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | yes | Variant title |
| `price` | number | yes | Selling price |
| `compared_at_price` | number | no | Original/compare-at price |
| `inventory_quantity` | integer | no | Initial stock quantity |
| `position` | integer | no | Display position |
| `sku` | string | no | Stock keeping unit |
| `barcode` | string | no | Barcode value |
| `follow_stock` | boolean | no | Whether to track stock |

Request example:

```http
POST /2023-11/admin/products HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "title": "Arachide blanc du ndole",
  "handle": "arachide-blanc-du-ndole",
  "content": "Une description ici",
  "published": 1,
  "variants": [
    {
      "title": "Default Title",
      "price": 6500,
      "compared_at_price": null,
      "inventory_quantity": 32,
      "position": 1,
      "sku": "le paquet",
      "barcode": "456340560"
    }
  ],
  "options": [
    {
      "title": "Title",
      "position": 1,
      "values": ["Default Title"]
    }
  ],
  "medias": [],
  "tags": [
    { "name": "Tag Nouveau 1" },
    { "name": "Tag Nouveau 2" }
  ]
}
```

Success response `200 OK` — `ProductDetailResource`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Arachide blanc du ndole",
  "handle": "arachide-blanc-du-ndole",
  "content": "Une description ici",
  "published": 1,
  "type": "physical_goods",
  "vendor": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null,
  "min_price": 6500.00,
  "compare_at_price": null,
  "first_variant": {
    "id": "01k74h4addkn48aptf2xyp5cyw",
    "title": "Default Title",
    "base_title": "Default Title",
    "price": 6500.00,
    "compare_at_price": null,
    "position": 1,
    "sku": "le paquet",
    "barcode": "456340560",
    "follow_stock": false,
    "taxable": 1,
    "options": [0],
    "metadata": null,
    "product_id": "01k74h4acesr6y7vp6krqt5dqf",
    "image_id": null,
    "min_order_quantity": 1,
    "whatsapp_product_id": null,
    "stocks": [],
    "supplierproduct": null,
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "professionals": [],
    "deposit": null,
    "composite_stocks": [],
    "estimated_quantity": null,
    "estimated_quantity_by_warehouse": [],
    "is_bundle": false,
    "bundle_items": [],
    "created_at": "2025-10-09T13:03:54.000000Z",
    "updated_at": "2025-10-09T13:03:54.000000Z",
    "deleted_at": null
  },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": false,
  "remaining_stocks": null,
  "medias": [],
  "tags": [
    { "id": "01k746f3hn5q8z1kw9m69v1h6a", "name": "TAG NOUVEAU 1" },
    { "id": "01k746f3jd4tc7z3jkdf5nm4bw", "name": "TAG NOUVEAU 2" }
  ],
  "variants": [
    {
      "id": "01k74h4addkn48aptf2xyp5cyw",
      "title": "Default Title",
      "price": 6500.00,
      "compare_at_price": null,
      "position": 1,
      "sku": "le paquet",
      "barcode": "456340560",
      "follow_stock": false,
      "options": [0]
    }
  ],
  "options": [
    {
      "id": "01k74h4afrgjy1st477j1esybm",
      "title": "Title",
      "position": 1,
      "values": ["Default Title"],
      "metadata": null,
      "product_id": "01k74h4acesr6y7vp6krqt5dqf",
      "created_at": "2025-10-09T13:03:54.000000Z",
      "updated_at": "2025-10-09T13:03:54.000000Z",
      "deleted_at": null
    }
  ],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

Errors:
- `400` — missing `X-Company`
- `401` — invalid token
- `422` — validation failure

---

### `GET` `/2023-11/admin/products/{product}`
Retrieve a single product by ULID. Includes soft-deleted products. Passing `?analytics=true` switches the response to the aggregated analytics payload.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `include` | string | no | Comma-separated: `collections`, `shops`, `orders`, `variants.stocks`, `shops.warehouses`, `total_ordered_quantity`, `total_ordered_amount` |
| `analytics` | boolean | no | When `true`, returns the analytics payload instead |

Request example:

```http
GET /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf?include=shops,collections HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Arachide blanc du ndole",
  "handle": "arachide-blanc-du-ndole-pSzIiNNp",
  "content": "Une description ici",
  "published": 1,
  "type": "physical_goods",
  "vendor": null,
  "metadata": { "image_ready": false },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null,
  "min_price": 6500.00,
  "compare_at_price": null,
  "first_variant": {
    "id": "01k74h4addkn48aptf2xyp5cyw",
    "title": "Default Title",
    "base_title": "Default Title",
    "price": 6500.00,
    "compare_at_price": null,
    "position": 1,
    "sku": "le paquet",
    "barcode": "456340560",
    "follow_stock": false,
    "taxable": 1,
    "options": [0],
    "metadata": null,
    "product_id": "01k74h4acesr6y7vp6krqt5dqf",
    "image_id": null,
    "min_order_quantity": 1,
    "whatsapp_product_id": null,
    "stocks": [],
    "supplierproduct": null,
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "professionals": [],
    "deposit": null,
    "composite_stocks": [],
    "estimated_quantity": null,
    "estimated_quantity_by_warehouse": [],
    "is_bundle": false,
    "bundle_items": [],
    "created_at": "2025-10-09T13:03:54.000000Z",
    "updated_at": "2025-10-09T13:03:54.000000Z",
    "deleted_at": null
  },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": false,
  "remaining_stocks": null,
  "medias": [],
  "tags": [],
  "variants": [ { "...": "variant objects" } ],
  "options": [
    {
      "id": "01k74h4afrgjy1st477j1esybm",
      "title": "Title",
      "position": 1,
      "values": ["Default Title"],
      "metadata": null,
      "product_id": "01k74h4acesr6y7vp6krqt5dqf",
      "created_at": "2025-10-09T13:03:54.000000Z",
      "updated_at": "2025-10-09T13:03:54.000000Z",
      "deleted_at": null
    }
  ],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

Errors:
- `404` — product not found

---

### `PUT` `/2023-11/admin/products/{product}`
Full update of a product. The request body mirrors the create body; variants and options embedded in the payload are upserted by ID.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Request body** (all fields optional — omitted fields retain their current values):

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | no | max:255 | Product title |
| `handle` | string | no | slug | URL-safe handle; auto-slugified |
| `content` | string | no | — | Product description / HTML body |
| `published` | integer/boolean | no | `0` or `1` | Whether the product is publicly visible |
| `type` | string | no | — | Product type (e.g. `physical_goods`, `service`) |
| `vendor` | string | no | — | Brand or vendor name |
| `metadata` | object | no | — | Arbitrary key-value metadata |
| `is_shippable` | boolean | no | — | Whether the product requires shipping |
| `is_taxable` | boolean | no | — | Whether the product is taxable |
| `supplier_id` | string | no | ULID | Linked supplier ID |
| `price` | number | no | — | Shorthand price (used for service-type products) |
| `duration_minutes` | integer | no | — | Service duration in minutes |
| `buffer_minutes` | integer | no | — | Buffer time between bookings (minutes) |
| `professionals` | array | no | `[{id}]` | Professionals who can perform this service |
| `variants` | array | no | — | Array of variant objects; existing variants are upserted by `id` |
| `options` | array | no | — | Array of option objects |
| `medias` | array | no | — | Media file attachments |
| `tags` | array | no | `[{name}]` | Tags to attach; new tags are created automatically |
| `images` | array | no | `[{src}]` | Images by URL (used during migration) |

Request example:

```http
PUT /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "title": "Arachide blanc du ndole (updated)",
  "type": "physical_goods",
  "published": 1,
  "price": 7000,
  "tags": [{ "name": "Promo" }]
}
```

Success response `200 OK` — `ProductDetailResource`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Arachide blanc du ndole (updated)",
  "handle": "arachide-blanc-du-ndole-pSzIiNNp",
  "content": "Une description ici",
  "published": 1,
  "type": "physical_goods",
  "vendor": null,
  "metadata": { "image_ready": false },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T14:10:00.000000Z",
  "deleted_at": null,
  "min_price": 7000.00,
  "compare_at_price": null,
  "first_variant": {
    "id": "01k74h4addkn48aptf2xyp5cyw",
    "title": "Default Title",
    "base_title": "Default Title",
    "price": 7000.00,
    "compare_at_price": null,
    "position": 1,
    "sku": "le paquet",
    "barcode": "456340560",
    "follow_stock": false,
    "taxable": 1,
    "options": [0],
    "metadata": null,
    "product_id": "01k74h4acesr6y7vp6krqt5dqf",
    "image_id": null,
    "min_order_quantity": 1,
    "whatsapp_product_id": null,
    "stocks": [],
    "supplierproduct": null,
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "professionals": [],
    "deposit": null,
    "composite_stocks": [],
    "estimated_quantity": null,
    "estimated_quantity_by_warehouse": [],
    "is_bundle": false,
    "bundle_items": [],
    "created_at": "2025-10-09T13:03:54.000000Z",
    "updated_at": "2025-10-09T14:10:00.000000Z",
    "deleted_at": null
  },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": false,
  "remaining_stocks": null,
  "medias": [],
  "tags": [{ "id": "01k746f3hn5q8z1kw9m69v1h6a", "name": "PROMO" }],
  "variants": [],
  "options": [],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

Errors:
- `404` — product not found
- `422` — validation failure

---

### `DELETE` `/2023-11/admin/products/{product}`
Soft-delete a product. Products with orders can only be deleted by a `manager`, `admin`, or `superadmin` role. Pass `?soft=true` to skip the order-count guard.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `soft` | string | no | Pass `true` to bypass the orders guard |

Request example:

```http
DELETE /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "message": "Product deleted successfully" }
```

Errors:
- `400` — product has orders and caller lacks sufficient role
- `404` — product not found
- `500` — unexpected error

---

### `POST` `/2023-11/admin/products/{product}/restore`
Restore a soft-deleted product.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID (including trashed) |

Request example:

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf/restore HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "message": "Product restored successfully." }
```

---

### `PUT` `/2023-11/admin/products/{product}/{field}`
Update a single field on a product without a full body. Special handling exists for `shops` (syncs pivot) and `published` (toggles boolean).

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |
| `field` | string | Field name to update (e.g. `published`, `title`, `shops`) |

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `value` | mixed | yes | New value. For `shops`, an array of shop ULIDs. For `published`, `0` or `1`. |

Request example:

```http
PUT /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf/published HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{ "value": "1" }
```

Success response `200 OK` — `ProductDetailResource`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Arachide blanc du ndole",
  "handle": "arachide-blanc-du-ndole-pSzIiNNp",
  "content": "Une description ici",
  "published": 1,
  "type": "physical_goods",
  "vendor": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T14:15:00.000000Z",
  "deleted_at": null,
  "min_price": 6500.00,
  "compare_at_price": null,
  "first_variant": { "...": "ProductVariantResource" },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": false,
  "remaining_stocks": null,
  "medias": [],
  "tags": [],
  "variants": [],
  "options": [],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

---

### `GET` `/2023-11/admin/products/handle/{product}`
Retrieve a product by its URL handle (admin version — includes unpublished/trashed).

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | URL handle string |

Success response `200 OK` — `ProductDetailResource`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Arachide blanc du ndole",
  "handle": "arachide-blanc-du-ndole",
  "content": "Une description ici",
  "published": 1,
  "type": "physical_goods",
  "vendor": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null,
  "min_price": 6500.00,
  "compare_at_price": null,
  "first_variant": { "...": "ProductVariantResource" },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": false,
  "remaining_stocks": null,
  "medias": [],
  "tags": [],
  "variants": [],
  "options": [],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

Errors:
- `404` — no product found with that handle

---

### `GET` `/2023-11/admin/products/{product}/analytics`
Return aggregated analytics for a product: top customers, total orders, total sales value, total quantity sold, top shops, and top related products.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `limit` | integer | no | 5 | Number of items in each top-N list |

Request example:

```http
GET /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf/analytics?limit=5 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "topCustomers": [ { "id": "...", "name": "..." } ],
  "totalOrders": 42,
  "totalSales": 273000.00,
  "totalQuantity": 56,
  "topShops": [
    { "shop_id": "01hxxx", "orders_count": 15 }
  ],
  "topRelatedProducts": [ { "...": "ProductDetailResource" } ]
}
```

---

### `POST` `/2023-11/admin/products/bulk-assign-professionals`
Bulk-assign one or more professionals to the services of multiple products.

**Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `product_ids` | array | yes | min:1, each must exist in `products` | ULIDs of products to update |
| `professional_ids` | array | yes | min:1, each must exist in `users` | ULIDs of professionals to assign |

Request example:

```http
POST /2023-11/admin/products/bulk-assign-professionals HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "product_ids": ["01k74h4acesr6y7vp6krqt5dqf"],
  "professional_ids": ["01hk7qfgkyd4cg9vd8h7jr749d"]
}
```

Success response `200 OK`:

```json
{
  "message": "Professionals assigned successfully",
  "assigned_count": 1,
  "professionals_count": 1
}
```

Errors:
- `422` — invalid product/professional IDs

---

### `POST` `/2023-11/admin/products/export`
Trigger an asynchronous product export. The file is generated in the background and the user is notified on completion.

**Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `format` | string | yes | `xlsx`, `csv`, or `pdf` | Output format |
| `start_date` | date | no | — | Filter products created after this date |
| `end_date` | date | no | after_or_equal:start_date | Filter products created before this date |
| `columns` | array | no | — | Specific columns to include in the export |
| `supplier` | boolean | no | — | Include supplier columns |

Request example:

```http
POST /2023-11/admin/products/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "format": "xlsx",
  "start_date": "2025-01-01",
  "end_date": "2025-06-30"
}
```

Success response `200 OK` — export job is queued:

```json
{
  "message": "Export initiated successfully. You will be notified when it's completed.",
  "job_id": "exports:01k74h4acesr6y7vp6krqt5dqf",
  "status": "queued"
}
```

Errors:
- `401` — user not authenticated
- `422` — invalid `format`, invalid date range

---

### `GET` `/2023-11/admin/products/export/fields`
List the columns available for product exports.

Request example:

```http
GET /2023-11/admin/products/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "columns": ["ID", "Title", "Handle", "Price", "SKU", "Barcode", "..."]
}
```

---

### `POST` `/2023-11/admin/products/import`
Import products from an Excel or CSV file. Processing is asynchronous; the user is notified when complete.

**Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `import_file` | file | yes | mimes: `xlsx`, `xls`, `csv` | The import file |

Request example:

```http
POST /2023-11/admin/products/import HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: multipart/form-data
Accept: application/json

import_file=@products.xlsx
```

Success response `200 OK`:

```json
{
  "message": "Import initiated successfully. You will be notified when it's completed."
}
```

---

### `POST` `/2023-11/admin/products/import-chunk`
Upload a single chunk of a large import file. Chunks are stored temporarily in S3 and later merged with `merge-chunk`.

**Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `chunk` | file | yes | required\|file | Binary chunk data |
| `chunkIndex` | integer | yes | required\|integer | Zero-based chunk index |
| `uploadId` | string | yes | required\|string | Unique ID identifying the upload session |

Success response `200 OK`:

```json
{
  "success": true,
  "message": "Chunk 0 uploaded successfully",
  "chunkPath": "imports/tmp/upload-abc123/chunk_0"
}
```

---

### `POST` `/2023-11/admin/products/merge-chunk`
Merge all uploaded chunks into a final file and dispatch the import job.

**Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `uploadId` | string | yes | required\|string | Upload session ID (must match chunks) |
| `totalChunks` | integer | yes | required\|integer | Total number of chunks to merge |
| `fileName` | string | yes | required\|string | Original filename (used for the final S3 key) |

Success response `200 OK`:

```json
{
  "success": true,
  "message": "Import initiated successfully. You will be notified when it's completed.",
  "filePath": "imports/upload-abc123_products.xlsx",
  "fileSize": 204800
}
```

---

### `POST` `/2023-11/admin/products/merge`
Merge multiple products into a single canonical product, transferring orders, collections, tags, shops, stocks, variants, and media.

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `ids` | array | yes | At least 2 product ULIDs to merge |
| `mainProductId` | string | no | ULID of the product to keep. If omitted, the most recently created published product is used. |

Request example:

```http
POST /2023-11/admin/products/merge HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "ids": ["01k74h4acesr6y7vp6krqt5dqf", "01k74h4b0xzw2fqr9mncde1234"],
  "mainProductId": "01k74h4acesr6y7vp6krqt5dqf"
}
```

Success response `200 OK`:

```json
{
  "message": "Products merged successfully",
  "product": { "...": "Product model attributes" }
}
```

Errors:
- `400` — fewer than 2 product IDs provided
- `500` — merge transaction failed

---

### `POST` `/2023-11/admin/products/migration`
Create a product for data migration purposes (bypasses normal upsert process, sets exact timestamps).

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | yes | Product title |
| `handle` | string | yes | URL handle (slugified) |
| `content` | string | no | Description |
| `published` | mixed | no | Boolean, `"true"`, or `1` |
| `metadata` | object | no | Arbitrary metadata |
| `created_at` | datetime | no | Exact creation timestamp to preserve |
| `updated_at` | datetime | no | Exact update timestamp to preserve |
| `images` | array | no | `[{src, id}]` — images from external URLs |

Success response `200 OK` — raw Product model JSON:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Migrated Product",
  "handle": "migrated-product",
  "content": "Imported from legacy system",
  "published": 1,
  "type": null,
  "vendor": null,
  "metadata": { "shopifyId": "7654321" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2023-05-10T09:00:00.000000Z",
  "updated_at": "2023-05-10T09:00:00.000000Z",
  "deleted_at": null
}
```

---

### `POST` `/2023-11/admin/products/migration/{product}/options`
Create an option on a migration product. Identical behavior to `POST /2023-11/admin/products/{product}/options`.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Migration product ULID |

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | yes | Option name (e.g. `Size`) |
| `position` | integer | no | Display position |
| `values` | array | no | Array of allowed value strings |
| `metadata` | object | no | Arbitrary metadata |

Success response `200 OK` — raw ProductOption JSON:

```json
{
  "id": "01k74h4afrgjy1st477j1esybm",
  "title": "Size",
  "position": 1,
  "values": ["S", "M", "L"],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "created_at": "2023-05-10T09:00:00.000000Z",
  "updated_at": "2023-05-10T09:00:00.000000Z",
  "deleted_at": null
}
```

---

### `POST` `/2023-11/admin/products/migration/{product}/variants`
Create a variant on a migration product. Identical behavior to `POST /2023-11/admin/products/{product}/variants`.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Migration product ULID |

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | yes | Variant display title |
| `price` | number | yes | Selling price |
| `compare_at_price` | number | no | Original / compare-at price |
| `position` | integer | no | Display order |
| `sku` | string | no | SKU |
| `barcode` | string | no | Barcode |
| `follow_stock` | boolean | no | Track inventory |
| `options` | array | no | Array of option value indexes (e.g. `[0, 1]`) |
| `metadata` | object | no | Arbitrary metadata |
| `image_id` | string | no | Media ULID to associate |
| `min_order_quantity` | integer | no | Minimum order quantity |

Success response `200 OK` — raw ProductVariant JSON:

```json
{
  "id": "01k74h4addkn48aptf2xyp5cyw",
  "title": "Default Title",
  "price": 6500.00,
  "compare_at_price": null,
  "position": 1,
  "sku": "le paquet",
  "barcode": "456340560",
  "follow_stock": false,
  "taxable": 1,
  "options": [0],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "created_at": "2023-05-10T09:00:00.000000Z",
  "updated_at": "2023-05-10T09:00:00.000000Z",
  "deleted_at": null
}
```

---

### `POST` `/2023-11/admin/products/{product}/options`
Create a product option (e.g. Size, Color) with its allowed values.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | yes | Option name (e.g. `Size`) |
| `position` | integer | no | Display position |
| `values` | array | no | Array of allowed value strings |
| `metadata` | object | no | Arbitrary metadata |

Request example:

```http
POST /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf/options HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "title": "Size",
  "position": 1,
  "values": ["S", "M", "L"]
}
```

Success response `200 OK` — raw ProductOption JSON:

```json
{
  "id": "01k74h4afrgjy1st477j1esybm",
  "title": "Size",
  "position": 1,
  "values": ["S", "M", "L"],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null
}
```

---

### `POST` `/2023-11/admin/products/{product}/variants`
Create a new variant for a product.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `title` | string | yes | Variant display title |
| `price` | number | yes | Selling price |
| `compare_at_price` | number | no | Original / compare-at price |
| `position` | integer | no | Display order |
| `sku` | string | no | SKU |
| `barcode` | string | no | Barcode |
| `follow_stock` | boolean | no | Track inventory |
| `options` | array | no | Array of option value indexes |
| `metadata` | object | no | Arbitrary metadata |
| `image_id` | string | no | Media ULID to associate |
| `min_order_quantity` | integer | no | Minimum order quantity |

Request example:

```http
POST /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf/variants HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "title": "Large / Blue",
  "price": 7500,
  "sku": "PROD-L-BLU",
  "options": [2, 1]
}
```

Success response `200 OK` — raw ProductVariant JSON:

```json
{
  "id": "01k74h4addkn48aptf2xyp5cyw",
  "title": "Large / Blue",
  "price": 7500.00,
  "compare_at_price": null,
  "position": 2,
  "sku": "PROD-L-BLU",
  "barcode": null,
  "follow_stock": false,
  "taxable": 1,
  "options": [2, 1],
  "metadata": [],
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "created_at": "2025-10-09T13:10:00.000000Z",
  "updated_at": "2025-10-09T13:10:00.000000Z",
  "deleted_at": null
}
```

---

## Products — Analytics

### `GET` `/2023-11/admin/products/{product}/sales`
Revenue time-series for a product, with optional comparison period.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | date | no | current month start | Period start (`YYYY-MM-DD`) |
| `end_date` | date | no | current month end | Period end |
| `compare_start_date` | date | no | prior month start | Comparison period start |
| `compare_end_date` | date | no | prior month end | Comparison period end |
| `revenueType` | string | no | all | Comma-separated revenue type handles |
| `paid_only` | string | no | `false` | Pass `true` to include only paid orders |

Request example:

```http
GET /2023-11/admin/products/01k74h4acesr6y7vp6krqt5dqf/sales?start_date=2025-09-01&end_date=2025-09-30 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "label": "Total Sales",
  "start_date": "2025-09-01",
  "end_date": "2025-09-30",
  "compare_start_date": "2025-08-01",
  "compare_end_date": "2025-08-31",
  "percentage": 12.5,
  "data": { "2025-09-01": 15000.00, "2025-09-02": 0 },
  "dataCount": 45000.00,
  "compare": 40000.00,
  "compareCount": { "2025-08-01": 12000.00 }
}
```

---

### `GET` `/2023-11/admin/products/{product}/quantity-sold`
Units-sold time-series for a product with comparison period. Same query parameters as `/sales`, but `data` values are integer quantities.

**Path parameters:** same as `/sales`.
**Query parameters:** same as `/sales`.

Success response `200 OK`:

```json
{
  "label": "Total Sales",
  "start_date": "2025-09-01",
  "end_date": "2025-09-30",
  "compare_start_date": "2025-08-01",
  "compare_end_date": "2025-08-31",
  "percentage": 8.3,
  "data": { "2025-09-01": 3, "2025-09-02": 0, "2025-09-03": 5 },
  "dataCount": 26,
  "compare": 24,
  "compareCount": { "2025-08-01": 2, "2025-08-02": 4 }
}
```

---

### `GET` `/2023-11/admin/products/{product}/sales-by-shop`
Total revenue grouped by shop for a given period.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

**Query parameters:**

| Name | Type | Default | Description |
|---|---|---|---|
| `start_date` | date | current month start | Period start |
| `end_date` | date | current month end | Period end |

Success response `200 OK`:

```json
{
  "01hshop1": 45000.00,
  "01hshop2": 12500.00
}
```

---

### `GET` `/2023-11/admin/products/{product}/sales-by-gender`
Sales revenue grouped by customer gender.

**Query parameters:** `start_date`, `end_date`.

Success response `200 OK`:

```json
{
  "MALE": 30000.00,
  "FEMALE": 27500.00,
  "UNKNOWN": 5000.00
}
```

---

### `GET` `/2023-11/admin/products/{product}/sales-by-type-of-customer`
Sales revenue grouped by customer type.

**Query parameters:** `start_date`, `end_date`.

Success response `200 OK`:

```json
{
  "individual": 40000.00,
  "business": 22500.00
}
```

---

### `GET` `/2023-11/admin/products/{product}/global-rank`
The product's sales rank among all company products for the given period.

**Query parameters:** `start_date`, `end_date`.

Success response `200 OK`:

```json
{
  "rank": 3,
  "total": 84
}
```

---

### `GET` `/2023-11/admin/products/{product}/rank-in-shops`
Per-shop sales rank of the product. Returns a map of shop ULID to integer rank position.

**Query parameters:** `start_date`, `end_date`.

Success response `200 OK`:

```json
{
  "01hshop1xyz0000000000000001": 1,
  "01hshop2xyz0000000000000002": 4,
  "01hshop3xyz0000000000000003": 2
}
```

---

### `GET` `/2023-11/admin/products/{product}/details-by-shops`
Revenue, profit, production cost, and quantity sold broken down per shop.

**Query parameters:** `start_date`, `end_date`.

Success response `200 OK`:

```json
[
  {
    "shop_id": "01hshop1",
    "total_quantity": 12,
    "total_profit": 18000.00,
    "total_revenue": 78000.00,
    "total_production_cost": 60000.00
  }
]
```

---

### `GET` `/2023-11/admin/products/{product}/top-customers`
Top customers ranked by spend on this product.

**Query parameters:**

| Name | Type | Default | Description |
|---|---|---|---|
| `start_date` | date | current month start | Period start |
| `end_date` | date | current month end | Period end |
| `limit` | integer | 5 | Number of customers to return |

Success response `200 OK` — `CustomerResource` collection:

```json
[
  {
    "id": "01hcust1234567890abcdef123",
    "first_name": "Jean",
    "last_name": "Dupont",
    "gender": "MALE",
    "email": "jean.dupont@example.com",
    "phone": "+237612345678",
    "preferred_language": "fr",
    "birthdate": null,
    "company_name": null,
    "type": "individual",
    "registration_number": null,
    "tax_exempt": false,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "metadata": null,
    "tax_number": null,
    "created_at": "2024-01-15T08:30:00.000000Z",
    "updated_at": "2025-09-20T11:00:00.000000Z",
    "addresses": [],
    "contacts": [],
    "custom_fields": null,
    "default_address": null,
    "medias": [],
    "notes": null,
    "tags": [],
    "badges": []
  }
]
```

---

### `GET` `/2023-11/admin/products/{product}/top-related-products`
Products most frequently co-purchased with this product.

**Query parameters:** `start_date`, `end_date`, `limit` (default 5).

Success response `200 OK` — `ProductDetailResource` collection:

```json
[
  {
    "id": "01k74h4b0xzw2fqr9mncde1234",
    "title": "Huile de palme raffinée",
    "handle": "huile-de-palme-raffinee",
    "content": null,
    "published": 1,
    "type": "physical_goods",
    "vendor": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "supplier_id": null,
    "is_shippable": 1,
    "is_taxable": 1,
    "created_at": "2025-10-01T10:00:00.000000Z",
    "updated_at": "2025-10-01T10:00:00.000000Z",
    "deleted_at": null,
    "min_price": 3500.00,
    "compare_at_price": null,
    "first_variant": { "...": "ProductVariantResource" },
    "has_variants": true,
    "variants_count": 1,
    "follow_stock": false,
    "remaining_stocks": null,
    "medias": [],
    "tags": [],
    "variants": [],
    "options": [],
    "menus": [],
    "shops": [],
    "supplier": null,
    "collections": [],
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "deposit": null,
    "professionals": []
  }
]
```

---

## Products (Public)

### `GET` `/2023-11/products`
Public product listing. Scoped to the storefront identified by `X-Shop`. Supports the same filtering and sorting as the admin list. Public users cannot search by SKU, and `metadata.downloadable_link` is stripped from the response.

Query parameters are identical to the admin listing. The `X-Company` header is required; `X-Shop` is optional but recommended for shop-scoped results.

Request example:

```http
GET /2023-11/products?filter[published]=1&include=variants.warehouseQuantities&fields=variants HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
X-Shop: 01hshop1
Accept: application/json
```

Success response `200 OK` — same paginated `ProductListResource` shape as the admin listing:

```json
{
  "data": [
    {
      "id": "01k74h4acesr6y7vp6krqt5dqf",
      "title": "Apple AirPods",
      "handle": "apple-airpods",
      "content": "<p>Wireless earbuds.</p>",
      "published": 1,
      "type": "physical_goods",
      "vendor": "Apple",
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": null,
      "is_shippable": 1,
      "is_taxable": 1,
      "created_at": "2025-08-01T10:00:00.000000Z",
      "updated_at": "2025-09-15T08:00:00.000000Z",
      "deleted_at": null,
      "min_price": 95000.00,
      "compare_at_price": 105000.00,
      "first_variant": { "...": "ProductVariantResource" },
      "has_variants": true,
      "variants_count": 1,
      "follow_stock": true,
      "remaining_stocks": null,
      "medias": [],
      "tags": [],
      "variants": [],
      "options": [],
      "menus": [],
      "shops": [],
      "supplier": null
    }
  ],
  "pagination": {
    "total": 84,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 6
  }
}
```

---

### `GET` `/2023-11/products/count`
Return the total number of published products.

Request example:

```http
GET /2023-11/products/count HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "count": 84 }
```

---

### `GET` `/2023-11/products/handle/{handle}`
Retrieve a published product by its URL handle. Result is cached for 24 hours.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `handle` | string | URL handle (e.g. `apple-airpods`) |

Request example:

```http
GET /2023-11/products/handle/apple-airpods HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — `ProductDetailResource`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Apple AirPods",
  "handle": "apple-airpods",
  "content": "<p>Wireless earbuds with active noise cancellation.</p>",
  "published": 1,
  "type": "physical_goods",
  "vendor": "Apple",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-08-01T10:00:00.000000Z",
  "updated_at": "2025-09-15T08:00:00.000000Z",
  "deleted_at": null,
  "min_price": 95000.00,
  "compare_at_price": 105000.00,
  "first_variant": {
    "id": "01k74h4addkn48aptf2xyp5cyw",
    "title": "Apple AirPods / Default Title",
    "base_title": "Default Title",
    "price": 95000.00,
    "compare_at_price": 105000.00,
    "position": 1,
    "sku": "AIRPODS-PRO-2",
    "barcode": "194252977729",
    "follow_stock": true,
    "taxable": 1,
    "options": [0],
    "metadata": null,
    "product_id": "01k74h4acesr6y7vp6krqt5dqf",
    "image_id": null,
    "min_order_quantity": 1,
    "whatsapp_product_id": null,
    "stocks": [],
    "supplierproduct": null,
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "professionals": [],
    "deposit": null,
    "composite_stocks": [],
    "estimated_quantity": 15,
    "estimated_quantity_by_warehouse": [],
    "is_bundle": false,
    "bundle_items": [],
    "created_at": "2025-08-01T10:00:00.000000Z",
    "updated_at": "2025-09-15T08:00:00.000000Z",
    "deleted_at": null
  },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": true,
  "remaining_stocks": null,
  "medias": [],
  "tags": [],
  "variants": [],
  "options": [],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

Errors:
- `404` — product not found or not published

---

### `GET` `/2023-11/products/{product}`
Retrieve a single published product by ULID.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |

Success response `200 OK` — `ProductDetailResource`:

```json
{
  "id": "01k74h4acesr6y7vp6krqt5dqf",
  "title": "Apple AirPods",
  "handle": "apple-airpods",
  "content": "<p>Wireless earbuds with active noise cancellation.</p>",
  "published": 1,
  "type": "physical_goods",
  "vendor": "Apple",
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "is_shippable": 1,
  "is_taxable": 1,
  "created_at": "2025-08-01T10:00:00.000000Z",
  "updated_at": "2025-09-15T08:00:00.000000Z",
  "deleted_at": null,
  "min_price": 95000.00,
  "compare_at_price": 105000.00,
  "first_variant": {
    "id": "01k74h4addkn48aptf2xyp5cyw",
    "title": "Apple AirPods / Default Title",
    "base_title": "Default Title",
    "price": 95000.00,
    "compare_at_price": 105000.00,
    "position": 1,
    "sku": "AIRPODS-PRO-2",
    "barcode": "194252977729",
    "follow_stock": true,
    "taxable": 1,
    "options": [0],
    "metadata": null,
    "product_id": "01k74h4acesr6y7vp6krqt5dqf",
    "image_id": null,
    "min_order_quantity": 1,
    "whatsapp_product_id": null,
    "stocks": [],
    "supplierproduct": null,
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "professionals": [],
    "deposit": null,
    "composite_stocks": [],
    "estimated_quantity": 15,
    "estimated_quantity_by_warehouse": [],
    "is_bundle": false,
    "bundle_items": [],
    "created_at": "2025-08-01T10:00:00.000000Z",
    "updated_at": "2025-09-15T08:00:00.000000Z",
    "deleted_at": null
  },
  "has_variants": true,
  "variants_count": 1,
  "follow_stock": true,
  "remaining_stocks": null,
  "medias": [],
  "tags": [],
  "variants": [],
  "options": [],
  "menus": [],
  "shops": [],
  "supplier": null,
  "collections": [],
  "duration_minutes": 0,
  "buffer_minutes": 0,
  "deposit": null,
  "professionals": []
}
```

Errors:
- `404` — product not found or not published

---

### `GET` `/2023-11/products/handle/{product}/similar`
### `GET` `/2023-11/products/{product}/similar`
Return similar products for a given product (by handle or ULID). Results are cached for 30 minutes. The algorithm tries co-purchased products first, then same-collection products, then same-price-range products, then random products.

**Query parameters:**

| Name | Type | Default | Description |
|---|---|---|---|
| `limit` | integer | 5 | Number of similar products to return |

Success response `200 OK` — `ProductDetailResource` collection:

```json
[
  {
    "id": "01k74h4b0xzw2fqr9mncde5678",
    "title": "Samsung Galaxy Buds",
    "handle": "samsung-galaxy-buds",
    "content": null,
    "published": 1,
    "type": "physical_goods",
    "vendor": "Samsung",
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "supplier_id": null,
    "is_shippable": 1,
    "is_taxable": 1,
    "created_at": "2025-08-10T10:00:00.000000Z",
    "updated_at": "2025-09-10T08:00:00.000000Z",
    "deleted_at": null,
    "min_price": 75000.00,
    "compare_at_price": null,
    "first_variant": { "...": "ProductVariantResource" },
    "has_variants": true,
    "variants_count": 1,
    "follow_stock": false,
    "remaining_stocks": null,
    "medias": [],
    "tags": [],
    "variants": [],
    "options": [],
    "menus": [],
    "shops": [],
    "supplier": null,
    "collections": [],
    "duration_minutes": 0,
    "buffer_minutes": 0,
    "deposit": null,
    "professionals": []
  }
]
```

---

### `GET` `/2023-11/products/{product}/variants/{variant}`
Get a specific variant of a product. Optionally check live stock availability.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `product` | string | Product ULID |
| `variant` | string | Variant ULID |

**Query parameters:**

| Name | Type | Default | Description |
|---|---|---|---|
| `check_stock` | string | — | Pass `true` to return live stock-check data instead of the variant resource |
| `shop_id` | string | — | Shop ULID for stock check |

Request example:

```http
GET /2023-11/products/01j69ct0e91w4x80c6qdgx5xy6/variants/01j69ct0f70f4rzaghchyysrz6?check_stock=true HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — `ProductVariantResource`:

```json
{
  "id": "01j69ct0f70f4rzaghchyysrz6",
  "title": "Apple AirPods / Default Title",
  "base_title": "Default Title",
  "price": 95000.00,
  "compare_at_price": 105000.00,
  "position": 1,
  "sku": "AIRPODS-PRO-2",
  "barcode": "194252977729",
  "follow_stock": true,
  "taxable": 1,
  "options": [0],
  "metadata": null,
  "product_id": "01j69ct0e91w4x80c6qdgx5xy6",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "stocks": [],
  "supplierproduct": null,
  "preview_image": null,
  "estimated_quantity": 15,
  "estimated_quantity_by_warehouse": [],
  "estimated_quantity_by_shop": null,
  "is_bundle": false,
  "bundle_items": [],
  "composite_stocks": [],
  "quantity_alert": null,
  "created_at": "2025-08-01T10:00:00.000000Z",
  "updated_at": "2025-09-15T08:00:00.000000Z",
  "deleted_at": null
}
```

Errors:
- `404` — product or variant not found, or variant does not belong to product

---

## Product Variants (Admin)

### `GET` `/2023-11/admin/productVariants`
List all product variants for the company.

**Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | no | 1 | Page number |
| `per_page` | integer | no | 15 | Results per page (max 100) |
| `sort` | string | no | `title` | Sort field. Prefix `-` for descending. Allowed: `title`, `price`, `created_at`, `updated_at`, `total_orders`, `product_title`, `has_stock_first` |
| `include` | string | no | — | Comma-separated: `product`, `product.collections`, `product.variants`, `product.menus`, `product.options`, `product.shops`, `supplierproduct`, `supplierproducts`, `warehouseQuantities.warehouse`, `service.professionals`, `total_ordered_quantity` |
| `filter[search]` | string | no | — | Search across product title, handle, variant title, barcode, SKU |
| `filter[title]` | string | no | — | Partial match on variant title |
| `filter[published]` | integer | no | — | Filter by product published status |
| `filter[type]` | string | no | — | Filter by product type |
| `filter[product_id]` | string | no | — | Filter by parent product ULID |
| `filter[shop_id]` | string | no | — | Filter by shop ULID |
| `filter[collections]` | string | no | — | Comma-separated collection ULIDs |
| `filter[updated_at][start]` | date | no | — | Updated at range start |
| `filter[updated_at][end]` | date | no | — | Updated at range end |

Request example:

```http
GET /2023-11/admin/productVariants?include=product&sort=updated_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — paginated `ProductVariantResource` collection.

The variant resource includes:

```json
{
  "id": "01k74h4addkn48aptf2xyp5cyw",
  "title": "Arachide blanc du ndole / Default Title",
  "base_title": "Default Title",
  "price": 6500.00,
  "compare_at_price": null,
  "position": 1,
  "sku": "le paquet",
  "barcode": "456340560",
  "follow_stock": false,
  "taxable": 1,
  "options": [0],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "stocks": [],
  "supplierproduct": null,
  "preview_image": null,
  "estimated_quantity": null,
  "estimated_quantity_by_warehouse": [],
  "estimated_quantity_by_shop": null,
  "is_bundle": false,
  "bundle_items": [],
  "composite_stocks": [],
  "quantity_alert": null,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null
}
```

---

### `GET` `/2023-11/admin/productVariants/barcode/{barcode}`
Look up a variant by exact barcode. Returns 404 if not found.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `barcode` | string | Barcode string |

Request example:

```http
GET /2023-11/admin/productVariants/barcode/456340560 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — `ProductVariantResource` (with `product` relation loaded):

```json
{
  "id": "01k74h4addkn48aptf2xyp5cyw",
  "title": "Arachide blanc du ndole / Default Title",
  "base_title": "Default Title",
  "price": 6500.00,
  "compare_at_price": null,
  "position": 1,
  "sku": "le paquet",
  "barcode": "456340560",
  "follow_stock": false,
  "taxable": 1,
  "options": [0],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "stocks": [],
  "supplierproduct": null,
  "preview_image": null,
  "estimated_quantity": null,
  "estimated_quantity_by_warehouse": [],
  "estimated_quantity_by_shop": null,
  "is_bundle": false,
  "bundle_items": [],
  "composite_stocks": [],
  "quantity_alert": null,
  "product": {
    "id": "01k74h4acesr6y7vp6krqt5dqf",
    "title": "Arachide blanc du ndole",
    "handle": "arachide-blanc-du-ndole",
    "published": 1,
    "type": "physical_goods",
    "vendor": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "min_price": 6500.00,
    "medias": [],
    "variants": [],
    "options": [],
    "menus": [],
    "shops": []
  },
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null
}
```

Errors:
- `404` — no variant with that barcode

---

### `GET` `/2023-11/admin/productVariants/{productVariant}`
Get a single variant by ULID.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productVariant` | string | Variant ULID |

Success response `200 OK` — `ProductVariantResource` (with `product` relation loaded):

```json
{
  "id": "01k74h4addkn48aptf2xyp5cyw",
  "title": "Arachide blanc du ndole / Default Title",
  "base_title": "Default Title",
  "price": 6500.00,
  "compare_at_price": null,
  "position": 1,
  "sku": "le paquet",
  "barcode": "456340560",
  "follow_stock": false,
  "taxable": 1,
  "options": [0],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "stocks": [],
  "supplierproduct": null,
  "preview_image": null,
  "estimated_quantity": null,
  "estimated_quantity_by_warehouse": [],
  "estimated_quantity_by_shop": null,
  "is_bundle": false,
  "bundle_items": [],
  "composite_stocks": [],
  "quantity_alert": null,
  "product": {
    "id": "01k74h4acesr6y7vp6krqt5dqf",
    "title": "Arachide blanc du ndole",
    "handle": "arachide-blanc-du-ndole",
    "published": 1,
    "type": "physical_goods",
    "vendor": null,
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "min_price": 6500.00,
    "medias": [],
    "variants": [],
    "options": [],
    "menus": [],
    "shops": []
  },
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z",
  "deleted_at": null
}
```

---

## Product Variants (Public)

### `GET` `/2023-11/productVariants`
Public variant listing. Same query parameters as the admin version.

Success response `200 OK` — paginated `ProductVariantResource` collection (`metadata.downloadable_link` is stripped for public users):

```json
{
  "data": [
    {
      "id": "01k74h4addkn48aptf2xyp5cyw",
      "title": "Apple AirPods / Default Title",
      "base_title": "Default Title",
      "price": 95000.00,
      "compare_at_price": 105000.00,
      "position": 1,
      "sku": "AIRPODS-PRO-2",
      "barcode": "194252977729",
      "follow_stock": true,
      "taxable": 1,
      "options": [0],
      "metadata": null,
      "product_id": "01k74h4acesr6y7vp6krqt5dqf",
      "image_id": null,
      "min_order_quantity": 1,
      "whatsapp_product_id": null,
      "stocks": [],
      "supplierproduct": null,
      "preview_image": null,
      "estimated_quantity": 15,
      "estimated_quantity_by_warehouse": [],
      "estimated_quantity_by_shop": null,
      "is_bundle": false,
      "bundle_items": [],
      "composite_stocks": [],
      "quantity_alert": null,
      "created_at": "2025-08-01T10:00:00.000000Z",
      "updated_at": "2025-09-15T08:00:00.000000Z",
      "deleted_at": null
    }
  ],
  "pagination": {
    "total": 120,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 8
  }
}
```

### `GET` `/2023-11/productVariants/{productVariant}`
Get a single variant by ULID (public).

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productVariant` | string | Variant ULID |

Success response `200 OK` — `ProductVariantResource` (with `product` relation loaded):

```json
{
  "id": "01k74h4addkn48aptf2xyp5cyw",
  "title": "Apple AirPods / Default Title",
  "base_title": "Default Title",
  "price": 95000.00,
  "compare_at_price": 105000.00,
  "position": 1,
  "sku": "AIRPODS-PRO-2",
  "barcode": "194252977729",
  "follow_stock": true,
  "taxable": 1,
  "options": [0],
  "metadata": null,
  "product_id": "01k74h4acesr6y7vp6krqt5dqf",
  "image_id": null,
  "min_order_quantity": 1,
  "whatsapp_product_id": null,
  "stocks": [],
  "supplierproduct": null,
  "preview_image": null,
  "estimated_quantity": 15,
  "estimated_quantity_by_warehouse": [],
  "estimated_quantity_by_shop": null,
  "is_bundle": false,
  "bundle_items": [],
  "composite_stocks": [],
  "quantity_alert": null,
  "product": {
    "id": "01k74h4acesr6y7vp6krqt5dqf",
    "title": "Apple AirPods",
    "handle": "apple-airpods",
    "published": 1,
    "type": "physical_goods",
    "vendor": "Apple",
    "metadata": {},
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "min_price": 95000.00,
    "medias": [],
    "variants": [],
    "options": [],
    "menus": [],
    "shops": []
  },
  "created_at": "2025-08-01T10:00:00.000000Z",
  "updated_at": "2025-09-15T08:00:00.000000Z",
  "deleted_at": null
}
```

---

## Custom Fields

Custom Fields define per-model metadata schemas. Each field is scoped to a specific `model` string (e.g. `products`, `customers`) and a `company_id`.

### `GET` `/2023-11/admin/custom-fields`
List all custom field definitions for the company.

**Query parameters:**

| Name | Type | Description |
|---|---|---|
| `filter[model]` | string | Exact match on model name |
| `filter[type]` | string | Exact match on field type |
| `filter[label]` | string | Partial match on label |
| `filter[key]` | string | Partial match on key |
| `sort` | string | `key`, `label`, `model`, `created_at`, `updated_at`. Default `-created_at` |

Request example:

```http
GET /2023-11/admin/custom-fields?filter[model]=products HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — array of `CustomFieldResource`:

```json
[
  {
    "id": "01k7cffield01qwerty1234567",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "app_id": null,
    "key": "origin_country",
    "label": "Country of Origin",
    "type": "text",
    "model": "products",
    "value": null,
    "required": false,
    "source": null,
    "items": null,
    "created_at": "2025-10-09T13:03:54.000000Z",
    "updated_at": "2025-10-09T13:03:54.000000Z"
  }
]
```

---

### `POST` `/2023-11/admin/custom-fields`
Create a new custom field definition.

**Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | yes | `max:191`, `regex:/^[a-z0-9_]+$/`, unique per company+model | Machine-readable key |
| `label` | string | yes | `max:255` | Human-readable label |
| `type` | string | yes | `text`, `number`, `checkbox`, `list`, `date`, `datetime` | Field type (`relation` is reserved for custom objects) |
| `model` | string | yes | `max:255` | Model name this field applies to (e.g. `products`) |
| `value` | string | no | — | Default value |
| `required` | boolean | no | — | Whether the field is required |
| `source` | string | no | `max:255` | Data source identifier |
| `app_id` | string | no | `max:26` | App ULID that owns this field |
| `items` | array | cond. | required when `type=list` | Allowed list items |

Request example:

```http
POST /2023-11/admin/custom-fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "key": "origin_country",
  "label": "Country of Origin",
  "type": "text",
  "model": "products",
  "required": false
}
```

Success response `201 Created` — `CustomFieldResource`:

```json
{
  "id": "01k7cffield01qwerty1234567",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "app_id": null,
  "key": "origin_country",
  "label": "Country of Origin",
  "type": "text",
  "model": "products",
  "value": null,
  "required": false,
  "source": null,
  "items": null,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z"
}
```

Errors:
- `422` — key already exists for this model, invalid type, missing required fields

---

### `GET` `/2023-11/admin/custom-fields/{customField}`
Get a single custom field definition.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `customField` | string | Custom field ULID |

Success response `200 OK` — `CustomFieldResource`:

```json
{
  "id": "01k7cffield01qwerty1234567",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "app_id": null,
  "key": "origin_country",
  "label": "Country of Origin",
  "type": "text",
  "model": "products",
  "value": null,
  "required": false,
  "source": null,
  "items": null,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z"
}
```

---

### `PUT` `/2023-11/admin/custom-fields/{customField}`
Update a custom field definition. All fields are optional (partial update via `sometimes` rules).

**Path parameters:** same as GET.

**Request body** (all fields optional):

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | no | max:191, `regex:/^[a-z0-9_]+$/`, unique per company+model (ignores self) | Machine-readable key |
| `label` | string | no | max:255 | Human-readable label |
| `type` | string | no | `text`, `number`, `checkbox`, `list`, `date`, `datetime` | Field type (`relation` is reserved for custom objects) |
| `model` | string | no | max:255 | Model name this field applies to (e.g. `products`) |
| `value` | string | no | — | Default value |
| `required` | boolean | no | — | Whether the field is required |
| `source` | string | no | max:255 | Data source identifier |
| `app_id` | string | no | max:26 | App ULID that owns this field |
| `items` | array | no | excluded unless `type=list` | Allowed list items |

Success response `200 OK` — `CustomFieldResource`:

```json
{
  "id": "01k7cffield01qwerty1234567",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "app_id": null,
  "key": "origin_country",
  "label": "Country of Origin (Updated)",
  "type": "text",
  "model": "products",
  "value": null,
  "required": true,
  "source": null,
  "items": null,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-10T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/custom-fields/{customField}`
Delete a custom field definition.

**Path parameters:** same as GET.

Success response `204 No Content` — no body.

---

## Custom Objects (Admin)

Custom Objects are user-defined data schemas with typed field definitions. Each object has a `schema` (array of field definitions) and stores data in **records**.

### `GET` `/2023-11/admin/custom-objects`
List all custom object schemas for the company.

**Query parameters:**

| Name | Type | Description |
|---|---|---|
| `filter[key]` | string | Partial match on key |
| `filter[name_singular]` | string | Partial match on singular name |
| `filter[is_public]` | boolean | Filter by public visibility |
| `filter[search]` | string | Permissive search across key, name_singular, name_plural |
| `sort` | string | `key`, `name_singular`, `created_at`, `updated_at`. Default `name_singular` |

Success response `200 OK` — array of `CustomObjectResource`:

```json
[
  {
    "id": "01k7custobj01234567890abcd",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "app_id": null,
    "key": "size_charts",
    "name_singular": "Size Chart",
    "name_plural": "Size Charts",
    "icon": "table",
    "schema": [
      {
        "key": "brand",
        "label": "Brand",
        "type": "text",
        "required": true,
        "is_public": true
      }
    ],
    "settings": null,
    "is_public": true,
    "allow_public_create": false,
    "records_count": 5,
    "created_at": "2025-10-01T00:00:00.000000Z",
    "updated_at": "2025-10-01T00:00:00.000000Z"
  }
]
```

---

### `POST` `/2023-11/admin/custom-objects`
Create a new custom object schema.

**Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | yes | `max:191`, `regex:/^[a-z0-9_-]+$/`, unique per company | Machine-readable key |
| `name_singular` | string | yes | `max:255` | Singular display name |
| `name_plural` | string | yes | `max:255` | Plural display name |
| `icon` | string | no | `max:64` | Icon identifier |
| `is_public` | boolean | no | — | Expose this object to the public API |
| `allow_public_create` | boolean | no | — | Allow unauthenticated record creation |
| `settings` | object | no | — | Arbitrary settings |
| `schema` | array | yes | `min:1`, schema items must be valid | Field definitions |

*Schema item fields:*

| Field | Type | Required | Description |
|---|---|---|---|
| `key` | string | yes | Field key (`/^[a-z0-9_]+$/`) |
| `label` | string | yes | Display label |
| `type` | string | yes | `text`, `number`, `checkbox`, `list`, `date`, `datetime`, or `relation` |
| `required` | boolean | no | Whether the field is required on record creation |
| `is_public` | boolean | no | Whether the field is visible in the public API |
| `items` | array | cond. | Required when `type=list` |
| `target` | string | cond. | Required when `type=relation`; must be an allowed relation target |
| `multiple` | boolean | no | Allow multiple values (for relation fields) |

Request example:

```http
POST /2023-11/admin/custom-objects HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "key": "size_charts",
  "name_singular": "Size Chart",
  "name_plural": "Size Charts",
  "is_public": true,
  "allow_public_create": false,
  "schema": [
    { "key": "brand", "label": "Brand", "type": "text", "required": true, "is_public": true },
    { "key": "size_s_chest", "label": "Size S - Chest (cm)", "type": "number", "is_public": true }
  ]
}
```

Success response `201 Created` — `CustomObjectResource`:

```json
{
  "id": "01k7custobj01234567890abcd",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "app_id": null,
  "key": "size_charts",
  "name_singular": "Size Chart",
  "name_plural": "Size Charts",
  "icon": null,
  "schema": [
    { "key": "brand", "label": "Brand", "type": "text", "required": true, "is_public": true },
    { "key": "size_s_chest", "label": "Size S - Chest (cm)", "type": "number", "required": false, "is_public": true }
  ],
  "settings": null,
  "is_public": true,
  "allow_public_create": false,
  "records_count": 0,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z"
}
```

Errors:
- `422` — duplicate key, invalid field type, duplicate schema keys

---

### `GET` `/2023-11/admin/custom-objects/{customObject}`
Get a single custom object schema (includes `records_count`).

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `customObject` | string | Custom object ULID |

Success response `200 OK` — `CustomObjectResource`:

```json
{
  "id": "01k7custobj01234567890abcd",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "app_id": null,
  "key": "size_charts",
  "name_singular": "Size Chart",
  "name_plural": "Size Charts",
  "icon": "table",
  "schema": [
    { "key": "brand", "label": "Brand", "type": "text", "required": true, "is_public": true },
    { "key": "size_s_chest", "label": "Size S - Chest (cm)", "type": "number", "required": false, "is_public": true }
  ],
  "settings": null,
  "is_public": true,
  "allow_public_create": false,
  "records_count": 5,
  "created_at": "2025-10-01T00:00:00.000000Z",
  "updated_at": "2025-10-01T00:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/custom-objects/{customObject}`
Update a custom object schema. When schema fields are removed, related record data for those fields is pruned.

**Path parameters:** same as GET.

**Request body** (all fields optional):

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `key` | string | no | max:191, `regex:/^[a-z0-9_-]+$/`, unique per company (ignores self) | Machine-readable key |
| `name_singular` | string | no | max:255 | Singular display name |
| `name_plural` | string | no | max:255 | Plural display name |
| `icon` | string | no | max:64 | Icon identifier |
| `is_public` | boolean | no | — | Expose this object to the public API |
| `allow_public_create` | boolean | no | — | Allow unauthenticated record creation |
| `settings` | object | no | — | Arbitrary settings |
| `schema` | array | no | min:1 if provided; items must be valid | Field definitions — see schema item fields below. Removing a field from `schema` will delete that field's data from all existing records. |

> **Note:** Removing a field from `schema` will delete that field's data from all existing records via the `CustomObjectRelationSynchronizer`.

Success response `200 OK` — `CustomObjectResource`:

```json
{
  "id": "01k7custobj01234567890abcd",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "app_id": null,
  "key": "size_charts",
  "name_singular": "Size Chart",
  "name_plural": "Size Charts",
  "icon": "table",
  "schema": [
    { "key": "brand", "label": "Brand", "type": "text", "required": true, "is_public": true },
    { "key": "size_s_chest", "label": "Size S - Chest (cm)", "type": "number", "required": false, "is_public": true },
    { "key": "size_m_chest", "label": "Size M - Chest (cm)", "type": "number", "required": false, "is_public": true }
  ],
  "settings": null,
  "is_public": true,
  "allow_public_create": false,
  "records_count": 5,
  "created_at": "2025-10-01T00:00:00.000000Z",
  "updated_at": "2025-10-10T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/custom-objects/{customObject}`
Delete a custom object schema and all its records.

Success response `204 No Content` — no body.

---

## Custom Object Records (Admin)

### `GET` `/2023-11/admin/custom-objects/{customObject}/records`
List records belonging to a custom object.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `customObject` | string | Custom object ULID |

**Query parameters:**

| Name | Type | Description |
|---|---|---|
| `filter[display_name]` | string | Partial match on display name |
| `filter[search]` | string | Permissive search on display name |
| `sort` | string | `display_name`, `created_at`, `updated_at`. Default `-created_at` |
| `page` / `per_page` | integer | Pagination |

Success response `200 OK` — paginated `CustomObjectRecordResource`:

```json
{
  "data": [
    {
      "id": "01k7record01234567890abcde",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "custom_object_id": "01k7custobj01234567890abcd",
      "display_name": "Nike Size Chart",
      "data": { "brand": "Nike", "size_s_chest": 88 },
      "created_by": "01huser1234567890abcdef123",
      "custom_object": null,
      "created_at": "2025-10-09T13:03:54.000000Z",
      "updated_at": "2025-10-09T13:03:54.000000Z"
    }
  ],
  "pagination": { "total": 5, "count": 5, "per_page": 15, "current_page": 1, "total_pages": 1 }
}
```

---

### `POST` `/2023-11/admin/custom-objects/{customObject}/records`
Create a new record in a custom object. `data` is validated against the schema field rules at creation time.

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `customObject` | string | Custom object ULID |

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `display_name` | string | no | Human-readable record name |
| `data` | object | no | Key-value payload matching the object schema |

Request example:

```http
POST /2023-11/admin/custom-objects/01k7custobj01234567890abcd/records HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "display_name": "Nike Size Chart",
  "data": {
    "brand": "Nike",
    "size_s_chest": 88
  }
}
```

Success response `201 Created` — `CustomObjectRecordResource`:

```json
{
  "id": "01k7record01234567890abcde",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "custom_object_id": "01k7custobj01234567890abcd",
  "display_name": "Nike Size Chart",
  "data": { "brand": "Nike", "size_s_chest": 88 },
  "created_by": "01huser1234567890abcdef123",
  "custom_object": null,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z"
}
```

Errors:
- `422` — data does not satisfy schema validation rules

---

### `GET` `/2023-11/admin/custom-objects/{customObject}/records/{record}`
Get a single record (with `customObject` relation loaded).

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `customObject` | string | Custom object ULID |
| `record` | string | Record ULID |

Success response `200 OK` — `CustomObjectRecordResource`:

```json
{
  "id": "01k7record01234567890abcde",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "custom_object_id": "01k7custobj01234567890abcd",
  "display_name": "Nike Size Chart",
  "data": { "brand": "Nike", "size_s_chest": 88 },
  "created_by": "01huser1234567890abcdef123",
  "custom_object": {
    "id": "01k7custobj01234567890abcd",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "key": "size_charts",
    "name_singular": "Size Chart",
    "name_plural": "Size Charts",
    "icon": "table",
    "schema": [
      { "key": "brand", "label": "Brand", "type": "text", "required": true, "is_public": true }
    ],
    "is_public": true,
    "allow_public_create": false,
    "records_count": 5,
    "created_at": "2025-10-01T00:00:00.000000Z",
    "updated_at": "2025-10-01T00:00:00.000000Z"
  },
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-09T13:03:54.000000Z"
}
```

Errors:
- `404` — record does not belong to this custom object

---

### `PUT` `/2023-11/admin/custom-objects/{customObject}/records/{record}`
Update a record. Omitting `data` from the request body preserves existing field values.

**Path parameters:** same as GET.

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `display_name` | string | no | New display name |
| `data` | object | no | Replacement payload; if omitted, existing data is preserved |

Success response `200 OK` — `CustomObjectRecordResource`:

```json
{
  "id": "01k7record01234567890abcde",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "custom_object_id": "01k7custobj01234567890abcd",
  "display_name": "Nike Size Chart (Updated)",
  "data": { "brand": "Nike", "size_s_chest": 90 },
  "created_by": "01huser1234567890abcdef123",
  "custom_object": null,
  "created_at": "2025-10-09T13:03:54.000000Z",
  "updated_at": "2025-10-10T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/custom-objects/{customObject}/records/{record}`
Delete a record and prune its relation references.

Success response `204 No Content` — no body.

---

## Custom Objects (Public)

Only objects with `is_public: true` are accessible via the public endpoints. Internal fields (`settings`, `app_id`, `company_id`) are hidden. Record data is filtered to `is_public: true` schema fields only.

### `GET` `/2023-11/custom-objects`
List public custom object schemas.

**Query parameters:** `X-Company` required; no auth token needed.

Success response `200 OK` — array of `CustomObjectPublicResource`:

```json
[
  {
    "id": "01k7custobj01234567890abcd",
    "key": "size_charts",
    "name_singular": "Size Chart",
    "name_plural": "Size Charts",
    "icon": "table",
    "schema": [
      { "key": "brand", "label": "Brand", "type": "text" }
    ],
    "allow_public_create": false
  }
]
```

---

### `GET` `/2023-11/custom-objects/{customObject}`
Get a single public custom object.

Success response `200 OK` — `CustomObjectPublicResource`:

```json
{
  "id": "01k7custobj01234567890abcd",
  "key": "size_charts",
  "name_singular": "Size Chart",
  "name_plural": "Size Charts",
  "icon": "table",
  "schema": [
    { "key": "brand", "label": "Brand", "type": "text" },
    { "key": "size_s_chest", "label": "Size S - Chest (cm)", "type": "number" }
  ],
  "allow_public_create": false
}
```

Errors:
- `404` — object not found or not public

---

### `GET` `/2023-11/custom-objects/{customObject}/records`
List records of a public custom object.

**Query parameters:**

| Name | Type | Description |
|---|---|---|
| `filter[display_name]` | string | Partial match on display name |
| `sort` | string | `display_name`, `created_at`. Default `-created_at` |
| `page` / `per_page` | integer | Pagination |

Success response `200 OK` — paginated `CustomObjectRecordPublicResource`:

```json
{
  "data": [
    {
      "id": "01k7record01234567890abcde",
      "custom_object_id": "01k7custobj01234567890abcd",
      "display_name": "Nike Size Chart",
      "data": { "brand": "Nike" },
      "created_at": "2025-10-09T13:03:54.000000Z"
    }
  ],
  "pagination": { "total": 5, "count": 5, "per_page": 15, "current_page": 1, "total_pages": 1 }
}
```

---

### `POST` `/2023-11/custom-objects/{customObject}/records`
Create a record in a public custom object. Requires `allow_public_create: true` on the object. Data is validated against the public-writable schema fields only.

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `display_name` | string | no | Record display name |
| `data` | object | no | Key-value payload (restricted to public-writable fields) |

Success response `201 Created` — `CustomObjectRecordPublicResource`:

```json
{
  "id": "01k7record09876543210fedcba",
  "custom_object_id": "01k7custobj01234567890abcd",
  "display_name": "Adidas Size Chart",
  "data": { "brand": "Adidas" },
  "created_at": "2025-10-09T14:00:00.000000Z"
}
```

Errors:
- `403` — object does not allow public record creation

---

### `GET` `/2023-11/custom-objects/{customObject}/records/{record}`
Get a single public record.

Success response `200 OK` — `CustomObjectRecordPublicResource`:

```json
{
  "id": "01k7record01234567890abcde",
  "custom_object_id": "01k7custobj01234567890abcd",
  "display_name": "Nike Size Chart",
  "data": { "brand": "Nike" },
  "created_at": "2025-10-09T13:03:54.000000Z"
}
```

---

## Table Model

### `GET` `/2023-11/admin/table_models/{table}`
Generic paginated query against an allowed database table. All results are scoped to the authenticated company (via `company_id` column or a configured `through` join).

**Path parameters:**

| Name | Type | Description |
|---|---|---|
| `table` | string | Whitelisted table name (configured in `config/table_model.php`) |

**Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | no | 15 | Results per page |
| `sort` | string | no | — | Comma-separated column names; prefix `-` for descending |
| `filter[<column>]` | mixed | no | — | Filter by any valid column. Array values default to `IN` |
| `filter[<column>_operator]` | string | no | `eq` | Override operator: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `like`, `notLike`, `in`, `notIn` |

Request example:

```http
GET /2023-11/admin/table_models/product_variants?filter[product_id]=01k74h4acesr6y7vp6krqt5dqf&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — Laravel paginator JSON (raw rows, no Resource transformation):

```json
{
  "current_page": 1,
  "data": [ { "id": "...", "title": "...", "..." : "..." } ],
  "from": 1,
  "last_page": 3,
  "per_page": 15,
  "to": 15,
  "total": 42
}
```

Errors:
- `400` — invalid table name characters
- `403` — table not in the allowed list or missing company scope
- `404` — table does not exist
- `422` — invalid filter column, invalid sort column
