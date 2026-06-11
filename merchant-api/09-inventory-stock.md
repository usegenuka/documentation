---
title: "Inventory & Stock"
description: "The Inventory & Stock domain manages physical goods across one or more warehouses. It covers **Warehouses** (storage locations), **Stocks** (SKU-level inventory"
sidebarTitle: "Inventory & Stock"
---

The Inventory & Stock domain manages physical goods across one or more warehouses. It covers **Warehouses** (storage locations), **Stocks** (SKU-level inventory records with per-warehouse quantities), **Stock History** (audit log of quantity changes), **Stock Movements** (inter-warehouse transfers), **Stock Inventories** (physical count sessions), and **Transfers** (treasury-account fund movements). All write operations are scoped to the authenticated company and respect the active shop/warehouse access rules.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/warehouses` | List warehouses |
| POST | `/2023-11/admin/warehouses` | Create a warehouse |
| GET | `/2023-11/admin/warehouses/{warehouse}` | Get a warehouse |
| PUT | `/2023-11/admin/warehouses/{warehouse}` | Update a warehouse |
| DELETE | `/2023-11/admin/warehouses/{warehouse}` | Delete a warehouse |
| GET | `/2023-11/admin/stocks` | List stocks |
| POST | `/2023-11/admin/stocks` | Create a stock |
| GET | `/2023-11/admin/stocks/analytics` | Get stock analytics |
| POST | `/2023-11/admin/stocks/export` | Export stocks asynchronously |
| GET | `/2023-11/admin/stocks/export/fields` | List available export columns |
| POST | `/2023-11/admin/stocks/add-stock` | Receive stock into a warehouse |
| POST | `/2023-11/admin/stocks/move-stock` | Adjust stock quantity in a shop warehouse |
| GET | `/2023-11/admin/stocks/{stock}` | Get a stock |
| PUT | `/2023-11/admin/stocks/{stock}` | Update a stock |
| DELETE | `/2023-11/admin/stocks/{stock}` | Delete a stock |
| GET | `/2023-11/admin/stockHistory` | List stock history entries |
| POST | `/2023-11/admin/stockHistory/{stock}` | Create a stock history entry |
| GET | `/2023-11/admin/stockHistory/{stockHistory}` | Get a stock history entry |
| PUT | `/2023-11/admin/stockHistory/{stockHistory}` | Update a stock history entry |
| DELETE | `/2023-11/admin/stockHistory/{stockHistory}` | Delete a stock history entry |
| GET | `/2023-11/admin/stockMovement` | List stock movements |
| POST | `/2023-11/admin/stockMovement` | Create a stock movement |
| GET | `/2023-11/admin/stockMovement/{stockMovement}` | Get a stock movement |
| PUT | `/2023-11/admin/stockMovement/{stockMovement}` | Update a stock movement |
| DELETE | `/2023-11/admin/stockMovement/{stockMovement}` | Delete a stock movement |
| PATCH | `/2023-11/admin/stockMovement/{stockMovement}/status` | Update movement status |
| GET | `/2023-11/admin/inventories` | List stock inventories |
| POST | `/2023-11/admin/inventories` | Create a stock inventory |
| GET | `/2023-11/admin/inventories/{inventory}` | Get a stock inventory |
| PUT | `/2023-11/admin/inventories/{inventory}` | Update inventory metadata |
| POST | `/2023-11/admin/inventories/{inventory}/cancel` | Cancel an inventory |
| PATCH | `/2023-11/admin/inventories/{inventory}/lines/{line}` | Update an inventory line |
| DELETE | `/2023-11/admin/inventories/{inventory}/medias/{media}` | Delete an inventory proof media |
| POST | `/2023-11/admin/inventories/{inventory}/validate` | Validate an inventory (finalise counts) |
| GET | `/2023-11/admin/transfers` | List treasury transfers |
| POST | `/2023-11/admin/transfers` | Create a treasury transfer |
| GET | `/2023-11/admin/transfers/{transfer}` | Get a treasury transfer |
| PUT | `/2023-11/admin/transfers/{transfer}` | Update a treasury transfer |
| DELETE | `/2023-11/admin/transfers/{transfer}` | Delete a treasury transfer |

---

## Warehouses

### `GET` `/2023-11/admin/warehouses`

List all warehouses accessible to the authenticated user. Managers with assigned warehouses see only their subset.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page (max 100) |
| `filter[id]` | string | No | — | Exact match on warehouse ID |
| `filter[company_id]` | string | No | — | Exact match on company ID |
| `filter[name]` | string | No | — | Partial match on name |
| `filter[shops]` | array | No | — | Array of shop IDs; returns warehouses linked to those shops |
| `filter[search]` | string | No | — | Full-text search on `name` |
| `include` | string | No | — | Comma-separated: `shops`, `address` |

Request example:

```http
GET /2023-11/admin/warehouses?per_page=10&include=address HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01k1amyq271bm81tncbepdxa2j",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Main Warehouse Douala",
      "description": "Primary storage location in Douala",
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "address": {
        "id": "01jq...",
        "line1": "Rue des Palmiers",
        "city": "Douala",
        "country": "CM",
        "phone": "+237600000001"
      },
      "shops": [],
      "logoUrl": "https://cdn.genuka.com/warehouses/logo.webp",
      "logo": null
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

Error responses:
- `400` — missing `X-Company` header
- `401` — invalid or expired token

---

### `POST` `/2023-11/admin/warehouses`

Create a new warehouse for the company.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | — | Warehouse display name |
| `description` | string | No | — | Optional description |
| `address` | object | No | requires `line1`, `city`, `country` | Physical address |
| `address.line1` | string | Yes (if address) | — | Street address |
| `address.city` | string | Yes (if address) | — | City |
| `address.country` | string | Yes (if address) | — | ISO 3166-1 alpha-2 country code |
| `address.phone` | string | No | — | Contact phone |
| `uploadImageBase64` | string | No | — | Base64-encoded logo image |

Request example:

```http
POST /2023-11/admin/warehouses HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Test Warehouse",
  "description": "Warehouse in Douala",
  "address": {
    "line1": "Rue de la Joie",
    "city": "Douala",
    "country": "CM",
    "phone": "+237600000001"
  }
}
```

Success response `200 OK`:

```json
{
  "id": "01k1amyq271bm81tncbepdxa2j",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Test Warehouse",
  "description": "Warehouse in Douala",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "address": {
    "id": "01jq...",
    "line1": "Rue de la Joie",
    "city": "Douala",
    "country": "CM",
    "phone": "+237600000001"
  },
  "shops": [],
  "logoUrl": null,
  "logo": null
}
```

Error responses:
- `400` — missing `X-Company` header
- `401` — invalid token
- `500` — internal error (e.g. image upload failure)

---

### `GET` `/2023-11/admin/warehouses/{warehouse}`

Retrieve a single warehouse by ID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `warehouse` | ULID | Warehouse ID |

Request example:

```http
GET /2023-11/admin/warehouses/01k1amyq271bm81tncbepdxa2j HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01k1amyq271bm81tncbepdxa2j",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Main Warehouse Douala",
  "description": "Primary storage location in Douala",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "address": {
    "id": "01jq...",
    "line1": "Rue des Palmiers",
    "city": "Douala",
    "country": "CM",
    "phone": "+237600000001"
  },
  "shops": [],
  "logoUrl": "https://cdn.genuka.com/warehouses/logo.webp",
  "logo": null
}
```

Error responses:
- `401` — invalid token
- `404` — warehouse not found or not accessible

---

### `PUT` `/2023-11/admin/warehouses/{warehouse}`

Update an existing warehouse.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `warehouse` | ULID | Warehouse ID |

- **Request body:** All fields are optional. Only provided fields are updated.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | — | Warehouse display name |
| `description` | string | No | — | Optional description |
| `address` | object | No | requires `line1`, `city`, `country` | Physical address |
| `address.line1` | string | Yes (if address) | — | Street address |
| `address.city` | string | Yes (if address) | — | City |
| `address.country` | string | Yes (if address) | — | ISO 3166-1 alpha-2 country code |
| `address.phone` | string | No | — | Contact phone |
| `uploadImageBase64` | string | No | — | Base64-encoded logo image |

Request example:

```http
PUT /2023-11/admin/warehouses/01k1amyq271bm81tncbepdxa2j HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Main Warehouse - Yaoundé"
}
```

Success response `200 OK`:

```json
{
  "id": "01k1amyq271bm81tncbepdxa2j",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Main Warehouse - Yaoundé",
  "description": "Primary storage location in Douala",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "deleted_at": null,
  "address": {
    "id": "01jq...",
    "line1": "Rue des Palmiers",
    "city": "Douala",
    "country": "CM",
    "phone": "+237600000001"
  },
  "shops": [],
  "logoUrl": null,
  "logo": null
}
```

Error responses:
- `401` — invalid token
- `404` — warehouse not found
- `500` — internal error

---

### `DELETE` `/2023-11/admin/warehouses/{warehouse}`

Permanently delete a warehouse (force-deletes, bypassing soft-delete).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `warehouse` | ULID | Warehouse ID |

Request example:

```http
DELETE /2023-11/admin/warehouses/01k1amyq271bm81tncbepdxa2j HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `204 No Content`:

```json
{ "message": "Deleted with success" }
```

Error responses:
- `401` — invalid token
- `404` — warehouse not found

---

## Stocks

A **Stock** is a company-level inventory record for one product variant, with quantities tracked per warehouse via `stocks_warehouse` pivot entries.

### `GET` `/2023-11/admin/stocks`

List stocks with optional filtering, sorting, and relationship loading.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page |
| `sort` | string | No | — | Sort field: `title`, `quantity`, `price`, `quantity_alert`, `created_at`, `updated_at`. Prefix with `-` for descending |
| `include` | string | No | — | Comma-separated: `warehouses`, `productVariant.product`, `productVariantCompositions` |
| `filter[title]` | string | No | — | Partial match on title |
| `filter[price]` | string | No | — | Partial match on price |
| `filter[out_of_stock]` | string | No | — | `true` = only zero-quantity stocks; `false` = only in-stock |
| `filter[quantity_alert]` | string | No | — | `true` = quantity at or below alert threshold |
| `filter[warehouse_ids]` | array | No | — | Filter to stocks present in these warehouse IDs |
| `filter[search]` | string | No | — | Full-text search on title, SKU, barcode, price (with relevance scoring) |
| `filter[product_variants]` | string | No | — | Filter by product variant ID |
| `filter[updated_at][start]` | string | No | — | ISO 8601 date range start |
| `filter[updated_at][end]` | string | No | — | ISO 8601 date range end |

Request example:

```http
GET /2023-11/admin/stocks?page=1&per_page=10&sort=title&include=warehouses,productVariant.product&filter[out_of_stock]=false HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01jdgq057ffyenbe8fxafgeac6",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "title": "Action Short / Navy",
      "price": 8500.00,
      "quantity_alert": 5,
      "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "medias": [],
      "quantity": 65,
      "stocks_warehouse": [
        {
          "id": 4,
          "stock_id": "01jdgq057ffyenbe8fxafgeac6",
          "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
          "quantity": 65.0,
          "purchase_price": 5000.00,
          "expiration_date": null,
          "production_date": null,
          "batch_number": null,
          "metadata": null
        }
      ],
      "product_variant": {
        "id": "01k77apj286gns7ap98hn5ejhc",
        "title": "Navy",
        "sku": "AS-NVY-001",
        "price": 8500.00,
        "product": { "id": "...", "title": "Action Short" }
      },
      "warehouses": [
        {
          "id": "01k1amyq271bm81tncbepdxa2j",
          "name": "Main Warehouse Douala"
        }
      ],
      "product_variant_compositions": []
    }
  ],
  "pagination": {
    "total": 120,
    "count": 10,
    "per_page": 10,
    "current_page": 1,
    "total_pages": 12
  }
}
```

Error responses:
- `400` — missing `X-Company` header
- `401` — invalid token

---

### `POST` `/2023-11/admin/stocks`

Create a new stock record, optionally linking it to a product variant and assigning warehouse quantities.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | Unique per company | Display name for this stock |
| `price` | number | No | — | Unit price (defaults to 0) |
| `quantity_alert` | integer | No | — | Low-stock alert threshold (defaults to 5) |
| `product_variant_id` | ULID | No | exists:product_variants | Link to a product variant |
| `stocks_warehouse` | array | No | — | Warehouse quantity assignments |
| `stocks_warehouse[].warehouse_id` | ULID | Yes (per item) | exists:warehouses | Warehouse to assign quantity to |
| `stocks_warehouse[].quantity` | number | No | — | Initial quantity in that warehouse |
| `stocks_warehouse[].purchase_price` | number | No | — | Purchase/cost price for this lot |
| `stocks_warehouse[].expiration_date` | string | No | ISO 8601 date | Expiration date |
| `stocks_warehouse[].production_date` | string | No | ISO 8601 date | Production date |
| `stocks_warehouse[].batch_number` | string | No | — | Batch/lot number |
| `stocks_warehouse[].is_new` | boolean | No | — | `true` forces creation of a new warehouse entry |
| `uploadImageUrl` | string | No | — | URL of an image to attach |
| `uploadImageBase64` | string | No | — | Base64-encoded image to attach |

Request example:

```http
POST /2023-11/admin/stocks HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
  "title": "Action Short / Navy",
  "quantity_alert": 5,
  "stocks_warehouse": [
    {
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "quantity": 50,
      "is_new": true
    }
  ]
}
```

Success response `200 OK`:

```json
{
  "id": "01jdgq057ffyenbe8fxafgeac6",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "title": "Action Short / Navy",
  "price": 0.00,
  "quantity_alert": 5,
  "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "medias": [],
  "quantity": 50,
  "stocks_warehouse": [
    {
      "id": 4,
      "stock_id": "01jdgq057ffyenbe8fxafgeac6",
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "quantity": 50.0,
      "purchase_price": null,
      "expiration_date": null,
      "production_date": null,
      "batch_number": null,
      "metadata": null
    }
  ],
  "product_variant": {
    "id": "01k77apj286gns7ap98hn5ejhc",
    "title": "Navy",
    "sku": "AS-NVY-001",
    "price": 8500.00,
    "product": { "id": "01k77apj00000ns7ap98hn5000", "title": "Action Short" }
  },
  "warehouses": [
    {
      "id": "01k1amyq271bm81tncbepdxa2j",
      "name": "Main Warehouse Douala",
      "description": null,
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "address": null,
      "shops": [],
      "logoUrl": null,
      "logo": null
    }
  ],
  "product_variant_compositions": []
}
```

Error responses:
- `400` — missing `X-Company` header
- `401` — invalid token
- `422` — `title` already exists for this company, or validation errors
- `500` — internal error (warehouse sync or media upload failure)

---

### `GET` `/2023-11/admin/stocks/analytics`

Returns aggregate inventory statistics for the company (or active shop).

Request example:

```http
GET /2023-11/admin/stocks/analytics HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "inventory_value": 4250000.00,
  "total_stocks": 3840
}
```

- `inventory_value` — total cost of all stock on hand, computed as `SUM(quantity × COALESCE(supplier_price, stock_price, variant_price, 0))`
- `total_stocks` — total unit quantity across all warehouses

Error responses:
- `401` — invalid token
- `400` — missing `X-Company` header

---

### `POST` `/2023-11/admin/stocks/export`

Trigger an asynchronous export of the stocks list. Returns immediately; the export is processed in the background.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `format` | string | No | `xlsx` / `csv` | Export file format (default `xlsx`) |
| `columns` | array | No | — | Subset of column keys to include (see `/export/fields`) |
| `filters` | object | No | — | Same filter keys as `GET /admin/stocks` |

Request example:

```http
POST /2023-11/admin/stocks/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "format": "xlsx",
  "columns": ["title", "quantity", "price"]
}
```

Success response `200 OK`:

```json
{
  "id": "01jz...",
  "status": "pending",
  "format": "xlsx",
  "download_url": null,
  "created_at": "2024-03-15T10:23:45.000000Z"
}
```

The export runs asynchronously. Poll the returned job record or wait for the notification to retrieve the `download_url` once `status` becomes `completed`.

Error responses:
- `401` — invalid or unauthenticated request

---

### `GET` `/2023-11/admin/stocks/export/fields`

List all available column keys that can be requested in a stock export.

Request example:

```http
GET /2023-11/admin/stocks/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "columns": ["title", "quantity", "price", "quantity_alert", "sku", "barcode", "warehouse"]
}
```

Error responses:
- `401` — invalid token

---

### `POST` `/2023-11/admin/stocks/add-stock`

Receive stock into a warehouse. Each line creates a new `stock_warehouse` lot entry. The system resolves or auto-creates a stock record from a `product_variant_id` if no `stock_id` is given.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `warehouse_id` | ULID | Yes | exists:warehouses | Target warehouse |
| `lines` | array | Yes | min:1 | Items to receive |
| `lines[].stock_id` | ULID | Conditional | required without `product_variant_id` | Existing stock to receive into |
| `lines[].product_variant_id` | ULID | Conditional | required without `stock_id` | Auto-resolve or create stock from this variant |
| `lines[].quantity` | number | Yes | gt:0 | Quantity received |
| `lines[].purchase_price` | number | No | min:0 | Purchase/cost price for this lot |
| `lines[].production_date` | string | No | valid date | Production date |
| `lines[].expiration_date` | string | No | valid date | Expiration / best-before date |
| `lines[].batch_number` | string | No | — | Batch/lot identifier |

Request example:

```http
POST /2023-11/admin/stocks/add-stock HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "lines": [
    {
      "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
      "quantity": 100,
      "purchase_price": 4500.00,
      "expiration_date": "2026-12-31",
      "batch_number": "BATCH-2026-001"
    }
  ]
}
```

Success response `200 OK`:

```json
{
  "status": "success",
  "message": "Stock added successfully",
  "count": 1
}
```

Error responses:
- `400` — insufficient stock, mismatched warehouse, or general error
- `403` — warehouse not found or not accessible
- `422` — validation errors (missing required fields, invalid quantity)

---

### `POST` `/2023-11/admin/stocks/move-stock`

Manually adjust (add or remove) stock quantities in a warehouse belonging to the active shop. Requires `X-Shop` header (or `shopId` in the body).

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `stocks` | array | Yes | min:1 | Adjustment items |
| `stocks[].warehouse_id` | ULID | Yes | exists:warehouses | Target warehouse |
| `stocks[].stock_warehouse_id` | integer | Yes | exists:stock_warehouse | Specific stock-warehouse lot to adjust |
| `stocks[].quantity` | number | Yes | numeric, min:1 | Quantity to add or remove |
| `stocks[].type` | string | Yes | `add` or `remove` | Direction of adjustment |
| `stocks[].reason` | string | No | — | Human-readable reason for the adjustment (logged in activity) |

Request example:

```http
POST /2023-11/admin/stocks/move-stock HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
X-Shop: 01jsh8rqz9s0p0mx8j2d4qy5kh
Accept: application/json
Content-Type: application/json

{
  "stocks": [
    {
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "stock_warehouse_id": 4,
      "quantity": 10,
      "type": "remove",
      "reason": "Damaged goods"
    }
  ]
}
```

Success response `200 OK`:

```json
{
  "status": "success",
  "message": "Stock updated successfully"
}
```

Error responses:
- `400` — shop not provided, insufficient stock, or warehouse mismatch
- `403` — company or shop not found / not accessible
- `422` — validation errors

---

### `GET` `/2023-11/admin/stocks/{stock}`

Retrieve a single stock with related product variant, compositions, and warehouses.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stock` | ULID | Stock ID |

Request example:

```http
GET /2023-11/admin/stocks/01jdgq057ffyenbe8fxafgeac6 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01jdgq057ffyenbe8fxafgeac6",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "title": "Action Short / Navy",
  "price": 8500.00,
  "quantity_alert": 5,
  "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "medias": [],
  "quantity": 65,
  "stocks_warehouse": [
    {
      "id": 4,
      "stock_id": "01jdgq057ffyenbe8fxafgeac6",
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "quantity": 65.0,
      "purchase_price": 5000.00,
      "expiration_date": null,
      "production_date": null,
      "batch_number": null,
      "metadata": null
    }
  ],
  "product_variant": {
    "id": "01k77apj286gns7ap98hn5ejhc",
    "title": "Navy",
    "sku": "AS-NVY-001",
    "price": 8500.00,
    "product": { "id": "01k77apj00000ns7ap98hn5000", "title": "Action Short" }
  },
  "warehouses": [
    {
      "id": "01k1amyq271bm81tncbepdxa2j",
      "name": "Main Warehouse Douala",
      "description": "Primary storage location in Douala",
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "address": null,
      "shops": [],
      "logoUrl": null,
      "logo": null
    }
  ],
  "product_variant_compositions": []
}
```

Error responses:
- `401` — invalid token
- `404` — stock not found

---

### `PUT` `/2023-11/admin/stocks/{stock}`

Update a stock record. All fields are optional on update.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stock` | ULID | Stock ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | Unique per company | Display name for this stock |
| `price` | number | No | — | Unit price |
| `quantity_alert` | integer | No | — | Low-stock alert threshold |
| `product_variant_id` | ULID | No | exists:product_variants | Link to a product variant |
| `stocks_warehouse` | array | No | — | Warehouse quantity assignments |
| `stocks_warehouse[].warehouse_id` | ULID | Yes (per item) | exists:warehouses | Warehouse to assign quantity to |
| `stocks_warehouse[].quantity` | number | No | — | Quantity in that warehouse |
| `stocks_warehouse[].purchase_price` | number | No | — | Purchase/cost price for this lot |
| `stocks_warehouse[].expiration_date` | string | No | ISO 8601 date | Expiration date |
| `stocks_warehouse[].production_date` | string | No | ISO 8601 date | Production date |
| `stocks_warehouse[].batch_number` | string | No | — | Batch/lot number |
| `stocks_warehouse[].is_new` | boolean | No | — | `true` forces creation of a new warehouse entry |
| `uploadImageUrl` | string | No | — | URL of an image to attach |
| `uploadImageBase64` | string | No | — | Base64-encoded image to attach |

Request example:

```http
PUT /2023-11/admin/stocks/01jdgq057ffyenbe8fxafgeac6 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Action Short / Navy (updated)"
}
```

Success response `200 OK`:

```json
{
  "id": "01jdgq057ffyenbe8fxafgeac6",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "title": "Action Short / Navy (updated)",
  "price": 8500.00,
  "quantity_alert": 5,
  "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T11:30:00.000000Z",
  "deleted_at": null,
  "medias": [],
  "quantity": 65,
  "stocks_warehouse": [
    {
      "id": 4,
      "stock_id": "01jdgq057ffyenbe8fxafgeac6",
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "quantity": 65.0,
      "purchase_price": 5000.00,
      "expiration_date": null,
      "production_date": null,
      "batch_number": null,
      "metadata": null
    }
  ],
  "product_variant": {
    "id": "01k77apj286gns7ap98hn5ejhc",
    "title": "Navy",
    "sku": "AS-NVY-001",
    "price": 8500.00,
    "product": { "id": "01k77apj00000ns7ap98hn5000", "title": "Action Short" }
  },
  "warehouses": [
    {
      "id": "01k1amyq271bm81tncbepdxa2j",
      "name": "Main Warehouse Douala",
      "description": "Primary storage location in Douala",
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "address": null,
      "shops": [],
      "logoUrl": null,
      "logo": null
    }
  ],
  "product_variant_compositions": []
}
```

Error responses:
- `401` — invalid token
- `404` — stock not found
- `500` — internal error

---

### `DELETE` `/2023-11/admin/stocks/{stock}`

Soft-delete a stock record and cascade-delete all its `stock_warehouse` entries.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stock` | ULID | Stock ID |

Request example:

```http
DELETE /2023-11/admin/stocks/01jdgq057ffyenbe8fxafgeac6 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "message": "Stock deleted successfully" }
```

Error responses:
- `401` — invalid token
- `404` — stock not found

---

## Stock History

Stock history records an audit log of every quantity change per warehouse lot. Each entry has a `type` (e.g. `sale`, `purchase`, `adjustment`, `inventory`, `movement`), a signed `quantity_delta`, and the running balance after the change (`balance_after`). Unlike the old ledger format, entries are immutable — the `PUT` endpoint route exists but its controller method is not yet implemented.

### `GET` `/2023-11/admin/stockHistory`

List stock history entries. Results are automatically filtered to warehouses accessible to the current user.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page |
| `sort` | string | No | `-date` | Sort field: `date`, `created_at`, `updated_at`, `quantity_delta` |
| `include` | string | No | — | Comma-separated: `stock`, `stock.productVariant`, `stock.productVariant.product`, `warehouse`, `fromWarehouse`, `toWarehouse`, `user`, `source` |
| `filter[type]` | string | No | — | Exact match on movement type |
| `filter[stock_id]` | ULID | No | — | Filter by stock |
| `filter[warehouse_id]` | ULID | No | — | Filter by warehouse (matches `warehouse_id`, `from_warehouse_id`, or `to_warehouse_id`) |
| `filter[from_warehouse_id]` | ULID | No | — | Exact match on source warehouse |
| `filter[to_warehouse_id]` | ULID | No | — | Exact match on destination warehouse |
| `filter[user_id]` | ULID | No | — | Exact match on user |
| `filter[direction]` | string | No | — | `in` = positive delta only; `out` = negative delta only |
| `filter[source_type]` | string | No | — | Alias: `order`, `bill`, `return`, `inventory`, `movement` |
| `filter[source_id]` | string | No | — | Source document ID |
| `filter[product_id]` | ULID | No | — | Filter by product (via stock → productVariant) |
| `filter[date_from]` | string | No | — | ISO 8601 date lower bound (inclusive) |
| `filter[date_to]` | string | No | — | ISO 8601 date upper bound (inclusive) |
| `filter[search]` | string | No | — | Search on `reason` and linked stock `title` |

Request example:

```http
GET /2023-11/admin/stockHistory?filter[stock_id]=01jdgq057ffyenbe8fxafgeac6&sort=-date&include=stock,warehouse HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": 42,
      "date": "2024-03-15T00:00:00.000000Z",
      "type": "purchase",
      "type_label": "Purchase",
      "direction": "in",
      "quantity_delta": 100.0,
      "balance_after": 165.0,
      "reason": "BATCH-2026-001 received",
      "stock_id": "01jdgq057ffyenbe8fxafgeac6",
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "from_warehouse_id": null,
      "to_warehouse_id": null,
      "user_id": "01jq...",
      "source_type": "order",
      "source_id": "01jz...",
      "created_at": "2024-03-15T10:23:45.000000Z",
      "stock": {
        "id": "01jdgq057ffyenbe8fxafgeac6",
        "title": "Action Short / Navy",
        "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
        "product_variant": {
          "id": "01k77apj286gns7ap98hn5ejhc",
          "title": "Action Short / Navy",
          "product_id": "01k77apj00000ns7ap98hn5000",
          "product": { "id": "01k77apj00000ns7ap98hn5000", "title": "Action Short" }
        }
      },
      "warehouse": { "id": "01k1amyq271bm81tncbepdxa2j", "name": "Main Warehouse Douala" },
      "from_warehouse": null,
      "to_warehouse": null,
      "user": { "id": "01jq...", "name": "Wilfried D." },
      "source": {
        "type": "order",
        "id": "01jz...",
        "reference": "ORD-2024-0042"
      },
      "total_quantity": 165.0
    }
  ],
  "pagination": {
    "total": 10,
    "count": 10,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

Field notes:
- `quantity_delta` — signed change: positive = stock in, negative = stock out
- `balance_after` — running total of the stock in that warehouse after this entry (aliased from `quantity` column)
- `total_quantity` — present only when the list was built with a cumulative running total query; omitted otherwise
- `source_type` — short alias (`order`, `bill`, `return`, `inventory`, `movement`)

Error responses:
- `400` — missing `X-Company`
- `401` — invalid token

---

### `POST` `/2023-11/admin/stockHistory/{stock}`

Manually append a stock history entry for a given stock.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stock` | ULID | Stock ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `warehouse_id` | ULID | Yes | — | Warehouse this entry belongs to |
| `quantity` | number | Yes | — | Running balance quantity at this point in time |
| `date` | string | Yes | ISO 8601 | Date of the record |

Request example:

```http
POST /2023-11/admin/stockHistory/01jdgq057ffyenbe8fxafgeac6 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "quantity": 80,
  "date": "2024-04-01"
}
```

Success response `200 OK`:

```json
{
  "id": 43,
  "date": "2024-04-01T00:00:00.000000Z",
  "type": null,
  "type_label": null,
  "direction": null,
  "quantity_delta": null,
  "balance_after": 80.0,
  "reason": null,
  "stock_id": "01jdgq057ffyenbe8fxafgeac6",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "from_warehouse_id": null,
  "to_warehouse_id": null,
  "user_id": null,
  "source_type": null,
  "source_id": null,
  "created_at": "2024-04-01T08:00:00.000000Z"
}
```

Error responses:
- `401` — invalid token
- `404` — stock not found
- `500` — internal error

---

### `GET` `/2023-11/admin/stockHistory/{stockHistory}`

Retrieve a single stock history entry.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockHistory` | integer | Stock history entry ID |

Request example:

```http
GET /2023-11/admin/stockHistory/43 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": 43,
  "date": "2024-04-01T00:00:00.000000Z",
  "type": "purchase",
  "type_label": "Purchase",
  "direction": "in",
  "quantity_delta": 80.0,
  "balance_after": 80.0,
  "reason": "Manual entry",
  "stock_id": "01jdgq057ffyenbe8fxafgeac6",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "from_warehouse_id": null,
  "to_warehouse_id": null,
  "user_id": null,
  "source_type": null,
  "source_id": null,
  "created_at": "2024-04-01T08:00:00.000000Z"
}
```

Error responses:
- `401` — invalid token
- `404` — history entry not found

---

### `PUT` `/2023-11/admin/stockHistory/{stockHistory}`

> **Note:** The controller `update` method is not yet implemented. The route exists but currently returns an empty response. Stock history entries are immutable audit records; use this endpoint only if the controller is updated in a future release.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockHistory` | integer | Stock history entry ID |

- **Request body (anticipated):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `quantity` | number | No | numeric | Updated running balance quantity |
| `date` | string | No | ISO 8601 | Updated date of the record |
| `reason` | string | No | — | Human-readable description of the change |

Success response `200 OK` (anticipated once implemented):

```json
{
  "id": 43,
  "date": "2024-04-01T00:00:00.000000Z",
  "type": "purchase",
  "type_label": "Purchase",
  "direction": "in",
  "quantity_delta": null,
  "balance_after": 90.0,
  "reason": "Corrected balance",
  "stock_id": "01jdgq057ffyenbe8fxafgeac6",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "from_warehouse_id": null,
  "to_warehouse_id": null,
  "user_id": null,
  "source_type": null,
  "source_id": null,
  "created_at": "2024-04-01T08:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/stockHistory/{stockHistory}`

Delete a stock history entry.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockHistory` | integer | Stock history entry ID |

Request example:

```http
DELETE /2023-11/admin/stockHistory/43 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "message": "Stock history deleted successfully" }
```

Error responses:
- `401` — invalid token
- `404` — history entry not found

---

## Stock Movements

A **Stock Movement** represents an inter-warehouse transfer of stock. Status values: `scheduled`, `in_progress`, `completed`. A completed movement cannot be modified.

### `GET` `/2023-11/admin/stockMovement`

List stock movements with filtering and sorting.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page |
| `sort` | string | No | — | Sort field: `reference`, `status`, `due_date`, `created_at`, `updated_at` |
| `filter[status]` | string | No | — | Exact: `scheduled`, `in_progress`, or `completed` |
| `filter[reference]` | string | No | — | Partial match on reference |
| `filter[from_warehouse_id]` | ULID | No | — | Exact match on source warehouse |
| `filter[to_warehouse_id]` | ULID | No | — | Exact match on destination warehouse |
| `filter[search]` | string | No | — | Full-text search on reference, description, warehouse names, user name |
| `filter[created_at][start]` | string | No | — | ISO 8601 range start |
| `filter[created_at][end]` | string | No | — | ISO 8601 range end |
| `filter[due_date][start]` | string | No | — | Due date range start |
| `filter[due_date][end]` | string | No | — | Due date range end |

Request example:

```http
GET /2023-11/admin/stockMovement?page=1&per_page=10&sort=reference&filter[status]=scheduled HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01js1nt7txeqrtbe9ysvmabwxn",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "user_id": "01jq...",
      "reference": "MVT-20250601-001",
      "description": "Monthly restock from main warehouse",
      "from_warehouse_id": "01jndxrcgr1r8ypmt42ytq5z3s",
      "to_warehouse_id": "01jndxrctaz5rs5f68mj94e83b",
      "status": "scheduled",
      "status_label": "Scheduled",
      "due_date": "2025-06-05T00:00:00.000000Z",
      "created_at": "2025-06-01T09:00:00.000000Z",
      "updated_at": "2025-06-01T09:00:00.000000Z",
      "deleted_at": null,
      "from_warehouse": { "id": "01jndxrcgr1r8ypmt42ytq5z3s", "name": "Main Warehouse" },
      "to_warehouse": { "id": "01jndxrctaz5rs5f68mj94e83b", "name": "Branch Warehouse" },
      "user": { "id": "01jq...", "name": "Wilfried D." },
      "lines": [
        {
          "id": "01js...",
          "stock_movement_id": "01js1nt7txeqrtbe9ysvmabwxn",
          "stock_warehouse_id": 4,
          "quantity": 1,
          "created_at": "2025-06-01T09:00:00.000000Z",
          "updated_at": "2025-06-01T09:00:00.000000Z",
          "stock": { "id": "01jdgq...", "title": "Action Short / Navy" }
        }
      ],
      "medias": []
    }
  ],
  "pagination": {
    "total": 5,
    "count": 5,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

Error responses:
- `400` — missing `X-Company`
- `401` — invalid token

---

### `POST` `/2023-11/admin/stockMovement`

Create a new stock movement between two warehouses.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `from_warehouse_id` | ULID | Yes | exists:warehouses, different from `to_warehouse_id` | Source warehouse |
| `to_warehouse_id` | ULID | Yes | exists:warehouses, different from `from_warehouse_id` | Destination warehouse |
| `due_date` | string | Yes | valid date | Expected completion date |
| `status` | string | Yes | `scheduled`, `in_progress`, or `completed` | Initial status |
| `description` | string | No | — | Optional description |
| `lines` | array | Yes | min:1 | Items to move |
| `lines[].stock_warehouse_id` | integer | Conditional | required without `stock_id`; exists:stock_warehouse | Specific lot to move from |
| `lines[].stock_id` | ULID | Conditional | required without `stock_warehouse_id`; exists:stocks | Stock to move |
| `lines[].quantity` | number | Yes | numeric, min:1 | Quantity to transfer |

Request example:

```http
POST /2023-11/admin/stockMovement HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "from_warehouse_id": "01jndxrcgr1r8ypmt42ytq5z3s",
  "to_warehouse_id": "01jndxrctaz5rs5f68mj94e83b",
  "due_date": "2025-06-05",
  "status": "scheduled",
  "description": "Monthly restock",
  "lines": [
    {
      "stock_warehouse_id": 4,
      "quantity": 1
    }
  ]
}
```

Success response `200 OK`:

```json
{
  "id": "01js1nt7txeqrtbe9ysvmabwxn",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "MVT-20250601-001",
  "description": "Monthly restock",
  "from_warehouse_id": "01jndxrcgr1r8ypmt42ytq5z3s",
  "to_warehouse_id": "01jndxrctaz5rs5f68mj94e83b",
  "status": "scheduled",
  "status_label": "Scheduled",
  "due_date": "2025-06-05T00:00:00.000000Z",
  "created_at": "2025-06-01T09:00:00.000000Z",
  "updated_at": "2025-06-01T09:00:00.000000Z",
  "deleted_at": null,
  "from_warehouse": {
    "id": "01jndxrcgr1r8ypmt42ytq5z3s",
    "name": "Main Warehouse",
    "description": null,
    "metadata": null,
    "created_at": "2024-01-01T00:00:00.000000Z",
    "updated_at": "2024-01-01T00:00:00.000000Z",
    "deleted_at": null,
    "address": null,
    "shops": [],
    "logoUrl": null,
    "logo": null
  },
  "to_warehouse": {
    "id": "01jndxrctaz5rs5f68mj94e83b",
    "name": "Branch Warehouse",
    "description": null,
    "metadata": null,
    "created_at": "2024-01-01T00:00:00.000000Z",
    "updated_at": "2024-01-01T00:00:00.000000Z",
    "deleted_at": null,
    "address": null,
    "shops": [],
    "logoUrl": null,
    "logo": null
  },
  "user": { "id": "01jq...", "name": "Wilfried D." },
  "lines": [
    {
      "id": "01js...",
      "stock_movement_id": "01js1nt7txeqrtbe9ysvmabwxn",
      "stock_warehouse_id": 4,
      "quantity": 1,
      "created_at": "2025-06-01T09:00:00.000000Z",
      "updated_at": "2025-06-01T09:00:00.000000Z",
      "stock": { "id": "01jdgq057ffyenbe8fxafgeac6", "title": "Action Short / Navy" }
    }
  ],
  "medias": []
}
```

Error responses:
- `401` — invalid token
- `422` — source and destination warehouses are the same; lines missing; validation errors

---

### `GET` `/2023-11/admin/stockMovement/{stockMovement}`

Retrieve a single stock movement with all related data.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockMovement` | ULID | Stock movement ID |

Request example:

```http
GET /2023-11/admin/stockMovement/01js1nt7txeqrtbe9ysvmabwxn HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01js1nt7txeqrtbe9ysvmabwxn",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "MVT-20250601-001",
  "description": "Monthly restock from main warehouse",
  "from_warehouse_id": "01jndxrcgr1r8ypmt42ytq5z3s",
  "to_warehouse_id": "01jndxrctaz5rs5f68mj94e83b",
  "status": "scheduled",
  "status_label": "Scheduled",
  "due_date": "2025-06-05T00:00:00.000000Z",
  "created_at": "2025-06-01T09:00:00.000000Z",
  "updated_at": "2025-06-01T09:00:00.000000Z",
  "deleted_at": null,
  "from_warehouse": {
    "id": "01jndxrcgr1r8ypmt42ytq5z3s",
    "name": "Main Warehouse",
    "description": null,
    "metadata": null,
    "created_at": "2024-01-01T00:00:00.000000Z",
    "updated_at": "2024-01-01T00:00:00.000000Z",
    "deleted_at": null,
    "address": null,
    "shops": [],
    "logoUrl": null,
    "logo": null
  },
  "to_warehouse": {
    "id": "01jndxrctaz5rs5f68mj94e83b",
    "name": "Branch Warehouse",
    "description": null,
    "metadata": null,
    "created_at": "2024-01-01T00:00:00.000000Z",
    "updated_at": "2024-01-01T00:00:00.000000Z",
    "deleted_at": null,
    "address": null,
    "shops": [],
    "logoUrl": null,
    "logo": null
  },
  "user": { "id": "01jq...", "name": "Wilfried D." },
  "lines": [
    {
      "id": "01js...",
      "stock_movement_id": "01js1nt7txeqrtbe9ysvmabwxn",
      "stock_warehouse_id": 4,
      "quantity": 1,
      "created_at": "2025-06-01T09:00:00.000000Z",
      "updated_at": "2025-06-01T09:00:00.000000Z",
      "stock": {
        "id": "01jdgq057ffyenbe8fxafgeac6",
        "title": "Action Short / Navy",
        "product_variant": {
          "id": "01k77apj286gns7ap98hn5ejhc",
          "title": "Navy",
          "product": { "id": "01k77apj00000ns7ap98hn5000", "title": "Action Short" }
        }
      }
    }
  ],
  "medias": []
}
```

Error responses:
- `401` — invalid token
- `404` — movement not found

---

### `PUT` `/2023-11/admin/stockMovement/{stockMovement}`

Update a stock movement. All fields are optional.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockMovement` | ULID | Stock movement ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `from_warehouse_id` | ULID | No | exists:warehouses | New source warehouse |
| `to_warehouse_id` | ULID | No | exists:warehouses | New destination warehouse |
| `due_date` | string | No | valid date | Updated due date |
| `status` | string | No | `scheduled`, `in_progress`, `completed` | Updated status |
| `description` | string | No | max:500 | Updated description |
| `lines` | array | No | — | Updated lines (full replacement; omit `id` to create new lines, include `id` to update existing) |
| `lines[].id` | ULID | No | exists:stock_movement_lines | ID of existing line to update |
| `lines[].stock_warehouse_id` | integer | No | exists:stock_warehouse | Lot reference |
| `lines[].stock_id` | ULID | No | exists:stocks | Stock reference |
| `lines[].quantity` | number | Yes (if lines provided) | min:0.001 | Quantity |

Request example:

```http
PUT /2023-11/admin/stockMovement/01js1nt7txeqrtbe9ysvmabwxn HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "description": "Updated transfer description",
  "due_date": "2025-06-10",
  "lines": [
    {
      "stock_warehouse_id": 4,
      "quantity": 4
    }
  ]
}
```

Success response `200 OK`:

```json
{
  "id": "01js1nt7txeqrtbe9ysvmabwxn",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "MVT-20250601-001",
  "description": "Updated transfer description",
  "from_warehouse_id": "01jndxrcgr1r8ypmt42ytq5z3s",
  "to_warehouse_id": "01jndxrctaz5rs5f68mj94e83b",
  "status": "scheduled",
  "status_label": "Scheduled",
  "due_date": "2025-06-10T00:00:00.000000Z",
  "created_at": "2025-06-01T09:00:00.000000Z",
  "updated_at": "2025-06-01T10:00:00.000000Z",
  "deleted_at": null,
  "from_warehouse": { "id": "01jndxrcgr1r8ypmt42ytq5z3s", "name": "Main Warehouse", "description": null, "metadata": null, "created_at": "2024-01-01T00:00:00.000000Z", "updated_at": "2024-01-01T00:00:00.000000Z", "deleted_at": null, "address": null, "shops": [], "logoUrl": null, "logo": null },
  "to_warehouse": { "id": "01jndxrctaz5rs5f68mj94e83b", "name": "Branch Warehouse", "description": null, "metadata": null, "created_at": "2024-01-01T00:00:00.000000Z", "updated_at": "2024-01-01T00:00:00.000000Z", "deleted_at": null, "address": null, "shops": [], "logoUrl": null, "logo": null },
  "user": { "id": "01jq...", "name": "Wilfried D." },
  "lines": [
    {
      "id": "01js...",
      "stock_movement_id": "01js1nt7txeqrtbe9ysvmabwxn",
      "stock_warehouse_id": 4,
      "quantity": 4,
      "created_at": "2025-06-01T09:00:00.000000Z",
      "updated_at": "2025-06-01T10:00:00.000000Z",
      "stock": { "id": "01jdgq057ffyenbe8fxafgeac6", "title": "Action Short / Navy" }
    }
  ],
  "medias": []
}
```

Error responses:
- `401` — invalid token
- `404` — movement not found
- `422` — validation errors

---

### `DELETE` `/2023-11/admin/stockMovement/{stockMovement}`

Soft-delete a stock movement. Pass `force=true` to permanently delete.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockMovement` | ULID | Stock movement ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `force` | boolean | No | false | If `true`, permanently deletes the record |

Request example:

```http
DELETE /2023-11/admin/stockMovement/01js1nt7txeqrtbe9ysvmabwxn HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "message": "Stock movement deleted" }
```

Error responses:
- `401` — invalid token
- `404` — movement not found

---

### `PATCH` `/2023-11/admin/stockMovement/{stockMovement}/status`

Transition a stock movement to a new status. When transitioning to `completed`, source stock quantities are decremented and destination stock quantities are incremented atomically.

> A movement that is already `completed` cannot be modified and returns `400`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `stockMovement` | ULID | Stock movement ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `status` | string | Yes | `scheduled`, `in_progress`, or `completed` | New status |

Request example:

```http
PATCH /2023-11/admin/stockMovement/01js1nt7txeqrtbe9ysvmabwxn/status HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "status": "completed"
}
```

Success response `200 OK`:

```json
{
  "id": "01js1nt7txeqrtbe9ysvmabwxn",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "MVT-20250601-001",
  "description": "Monthly restock from main warehouse",
  "from_warehouse_id": "01jndxrcgr1r8ypmt42ytq5z3s",
  "to_warehouse_id": "01jndxrctaz5rs5f68mj94e83b",
  "status": "completed",
  "status_label": "Completed",
  "due_date": "2025-06-05T00:00:00.000000Z",
  "created_at": "2025-06-01T09:00:00.000000Z",
  "updated_at": "2025-06-05T14:00:00.000000Z",
  "deleted_at": null,
  "from_warehouse": { "id": "01jndxrcgr1r8ypmt42ytq5z3s", "name": "Main Warehouse", "description": null, "metadata": null, "created_at": "2024-01-01T00:00:00.000000Z", "updated_at": "2024-01-01T00:00:00.000000Z", "deleted_at": null, "address": null, "shops": [], "logoUrl": null, "logo": null },
  "to_warehouse": { "id": "01jndxrctaz5rs5f68mj94e83b", "name": "Branch Warehouse", "description": null, "metadata": null, "created_at": "2024-01-01T00:00:00.000000Z", "updated_at": "2024-01-01T00:00:00.000000Z", "deleted_at": null, "address": null, "shops": [], "logoUrl": null, "logo": null },
  "user": { "id": "01jq...", "name": "Wilfried D." },
  "lines": [
    {
      "id": "01js...",
      "stock_movement_id": "01js1nt7txeqrtbe9ysvmabwxn",
      "stock_warehouse_id": 4,
      "quantity": 1,
      "created_at": "2025-06-01T09:00:00.000000Z",
      "updated_at": "2025-06-01T09:00:00.000000Z",
      "stock": { "id": "01jdgq057ffyenbe8fxafgeac6", "title": "Action Short / Navy" }
    }
  ],
  "medias": []
}
```

Error responses:
- `400` — movement is already completed
- `401` — invalid token
- `404` — movement not found
- `422` — invalid status value

---

## Stock Inventories

A **Stock Inventory** is a physical count session for one warehouse. Statuses: `draft` → `in_progress` → `validated` (or `cancelled`). Only `draft` and `in_progress` inventories are editable. Validation generates accounting adjustment entries.

### `GET` `/2023-11/admin/inventories`

List stock inventories with filtering and sorting.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page |
| `paginate` | string | No | — | Pass `false` to return all results without pagination |
| `sort` | string | No | `-date` | Sort field: `date`, `created_at`, `status` |
| `include` | string | No | — | Comma-separated: `warehouse`, `validatedBy`, `lines`, `lines.stock`, `lines.productVariant` |
| `filter[status]` | string | No | — | Exact: `draft`, `in_progress`, `validated`, or `cancelled` |
| `filter[warehouse_id]` | ULID | No | — | Filter by warehouse |
| `filter[search]` | string | No | — | Search on `reference` and `reason` |

Request example:

```http
GET /2023-11/admin/inventories?filter[status]=draft&include=warehouse HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01jv...",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
      "reference": "INV-2026-0001",
      "date": "2026-01-15T00:00:00.000000Z",
      "status": "draft",
      "reason": "Annual inventory",
      "notes": null,
      "validated_by": null,
      "validated_at": null,
      "created_at": "2026-01-14T10:00:00.000000Z",
      "updated_at": "2026-01-14T10:00:00.000000Z",
      "warehouse": { "id": "01k1amyq271bm81tncbepdxa2j", "name": "Main Warehouse Douala" }
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

Error responses:
- `400` — missing `X-Company`
- `401` — invalid token

---

### `POST` `/2023-11/admin/inventories`

Create a stock inventory session. If `stock_ids` is omitted, all stocks with quantity > 0 in the target warehouse are automatically included as lines (streamed in batches).

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `warehouse_id` | ULID | Yes | exists:warehouses | Warehouse to count |
| `date` | string | Yes | valid date | Inventory date |
| `reason` | string | No | max:255 | Reason or label |
| `notes` | string | No | — | Internal notes |
| `stock_ids` | array | No | exists:stocks | Explicit list of stock IDs to include (partial/spot-check inventory) |
| `stock_filters` | object | No | — | Filters applied when auto-including stocks |
| `stock_filters.out_of_stock` | string | No | `"false"` | `"true"` to include zero-quantity stocks |
| `stock_filters.collection_ids` | array | No | — | Limit to stocks linked to these collection IDs |
| `stock_filters.supplier_ids` | array | No | — | Limit to stocks from these supplier IDs |
| `stock_filters.search` | string | No | max:255 | Filter by stock title |
| `stock_filters.title` | string | No | max:255 | Alias for `search` |

Request example:

```http
POST /2023-11/admin/inventories HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "date": "2026-01-15",
  "reason": "Annual inventory",
  "notes": "Full count",
  "stock_filters": {
    "out_of_stock": "false"
  }
}
```

Success response `201 Created`:

```json
{
  "id": "01jv...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "reference": "INV-2026-0001",
  "date": "2026-01-15",
  "status": "draft",
  "reason": "Annual inventory",
  "notes": "Full count",
  "validated_by": null,
  "validated_at": null,
  "created_at": "2026-01-14T10:00:00.000000Z",
  "updated_at": "2026-01-14T10:00:00.000000Z",
  "warehouse": { "id": "01k1amyq271bm81tncbepdxa2j", "name": "Main Warehouse Douala" },
  "lines_count": 240
}
```

Error responses:
- `401` — invalid token
- `422` — validation errors

---

### `GET` `/2023-11/admin/inventories/{inventory}`

Retrieve a single inventory with all its lines, media, and related warehouse.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `inventory` | ULID | Inventory ID |

Request example:

```http
GET /2023-11/admin/inventories/01jv... HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01jv...",
  "reference": "INV-2026-0001",
  "date": "2026-01-15",
  "status": "in_progress",
  "reason": "Annual inventory",
  "notes": null,
  "warehouse": { "id": "01k1amyq271bm81tncbepdxa2j", "name": "Main Warehouse Douala" },
  "validated_by": null,
  "validated_at": null,
  "media": [],
  "lines": [
    {
      "id": "01jw...",
      "inventory_id": "01jv...",
      "stock_id": "01jdgq057ffyenbe8fxafgeac6",
      "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
      "expected_quantity": "65.0000",
      "counted_quantity": null,
      "difference": null,
      "unit_cost": "5000.0000",
      "comment": null,
      "media": []
    }
  ]
}
```

Error responses:
- `401` — invalid token
- `404` — inventory not found

---

### `PUT` `/2023-11/admin/inventories/{inventory}`

Update an inventory's top-level metadata (`reason`, `notes`, `date`) and manage proof media attachments. Only editable when status is `draft` or `in_progress`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `inventory` | ULID | Inventory ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `reason` | string | No | max:255 | Updated reason |
| `notes` | string | No | — | Updated notes |
| `date` | string | No | valid date | Updated inventory date |
| `new_medias` | array | No | — | New proof files to attach (uploaded to S3) |
| `new_medias[].s3_key` | string | Yes (per item) | — | S3 object key |
| `new_medias[].file_name` | string | Yes (per item) | — | File name for the attachment |
| `new_medias[].mime_type` | string | No | — | MIME type |
| `new_medias[].size` | integer | No | — | File size in bytes |
| `delete_media_ids` | array | No | — | Array of media IDs to delete |

Request example:

```http
PUT /2023-11/admin/inventories/01jv... HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "notes": "Counting in progress - 3 counters assigned",
  "date": "2026-01-16"
}
```

Success response `200 OK`:

```json
{
  "id": "01jv...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "reference": "INV-2026-0001",
  "date": "2026-01-16",
  "status": "draft",
  "reason": "Annual inventory",
  "notes": "Counting in progress - 3 counters assigned",
  "validated_by": null,
  "validated_at": null,
  "created_at": "2026-01-14T10:00:00.000000Z",
  "updated_at": "2026-01-14T11:00:00.000000Z",
  "media": []
}
```

Error responses:
- `400` — inventory is not in an editable state
- `401` — invalid token
- `404` — inventory not found

---

### `POST` `/2023-11/admin/inventories/{inventory}/cancel`

Cancel a `draft` or `in_progress` inventory. Cannot cancel a `validated` inventory.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `inventory` | ULID | Inventory ID |

Request example:

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/inventories/01jv.../cancel HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01jv...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "reference": "INV-2026-0001",
  "date": "2026-01-15",
  "status": "cancelled",
  "reason": "Annual inventory",
  "notes": null,
  "validated_by": null,
  "validated_at": null,
  "created_at": "2026-01-14T10:00:00.000000Z",
  "updated_at": "2026-01-14T12:00:00.000000Z"
}
```

Error responses:
- `400` — inventory cannot be cancelled (e.g. already validated)
- `401` — invalid token
- `404` — inventory not found

---

### `PATCH` `/2023-11/admin/inventories/{inventory}/lines/{line}`

Update the counted quantity for a single inventory line. The first count on any line auto-transitions the inventory from `draft` to `in_progress`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `inventory` | ULID | Inventory ID |
| `line` | ULID | Inventory line ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `counted_quantity` | number | Yes | numeric, min:0 | The physically counted quantity |
| `comment` | string | No | — | Counter's comment |
| `unit_cost` | number | No | numeric, min:0 | Override the unit cost for adjustment value calculation |

Request example:

```http
PATCH /2023-11/admin/inventories/01jv.../lines/01jw... HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "counted_quantity": 62,
  "comment": "3 units found damaged"
}
```

Success response `200 OK`:

```json
{
  "id": "01jw...",
  "inventory_id": "01jv...",
  "stock_id": "01jdgq057ffyenbe8fxafgeac6",
  "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
  "expected_quantity": "65.0000",
  "counted_quantity": "62.0000",
  "difference": "-3.0000",
  "unit_cost": "5000.0000",
  "comment": "3 units found damaged",
  "created_at": "2026-01-15T09:00:00.000000Z",
  "updated_at": "2026-01-15T11:00:00.000000Z"
}
```

Error responses:
- `400` — inventory not editable
- `401` — invalid token
- `404` — inventory or line not found
- `422` — `counted_quantity` missing or invalid
- `422` — line does not belong to this inventory

---

### `DELETE` `/2023-11/admin/inventories/{inventory}/medias/{media}`

Remove a proof media attachment from an inventory. Only allowed while the inventory is still editable.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `inventory` | ULID | Inventory ID |
| `media` | integer | Media ID |

Request example:

```http
DELETE /2023-11/admin/inventories/01jv.../medias/17 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{ "message": "Media deleted successfully" }
```

Error responses:
- `400` — inventory not editable
- `401` — invalid token
- `404` — inventory or media not found

---

### `POST` `/2023-11/admin/inventories/{inventory}/validate`

Validate a fully-counted inventory. Requirements: (1) inventory must be `in_progress`, (2) all lines must have a `counted_quantity`, (3) at least one proof media must be attached. On success: stock quantities are adjusted in the warehouse, one accounting transaction is generated for all differences, and the inventory status is set to `validated`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `inventory` | ULID | Inventory ID |

- **Request body:** None — this is a pure action endpoint. No body is required.

Request example:

```http
POST /2023-11/admin/inventories/01jv.../validate HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01jv...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "warehouse_id": "01k1amyq271bm81tncbepdxa2j",
  "reference": "INV-2026-0001",
  "date": "2026-01-15",
  "status": "validated",
  "reason": "Annual inventory",
  "notes": null,
  "validated_by": "01jq...",
  "validated_at": "2026-01-15T14:30:00.000000Z",
  "created_at": "2026-01-14T10:00:00.000000Z",
  "updated_at": "2026-01-15T14:30:00.000000Z",
  "warehouse": { "id": "01k1amyq271bm81tncbepdxa2j", "name": "Main Warehouse Douala" },
  "lines": [
    {
      "id": "01jw...",
      "inventory_id": "01jv...",
      "stock_id": "01jdgq057ffyenbe8fxafgeac6",
      "product_variant_id": "01k77apj286gns7ap98hn5ejhc",
      "expected_quantity": "65.0000",
      "counted_quantity": "62.0000",
      "difference": "-3.0000",
      "unit_cost": "5000.0000",
      "comment": "3 units found damaged",
      "created_at": "2026-01-15T09:00:00.000000Z",
      "updated_at": "2026-01-15T11:00:00.000000Z"
    }
  ]
}
```

Error responses:
- `400` — inventory cannot be validated (wrong status, uncounted lines, or missing proof media). Error messages:
  - `"N lines have not been counted yet"` — not all lines have `counted_quantity`
  - `"No proof attached"` — no media on inventory or lines
  - `"Cannot validate inventory with status: draft"` — wrong status

```json
{
  "message": "3 lines have not been counted yet",
  "error": "business_exception",
  "code": 400
}
```

- `401` — invalid token
- `404` — inventory not found

---

## Transfers

A **Transfer** is a treasury-account fund movement. Deducts from the source account balance and credits the destination. Automatically creates accounting book entries.

### `GET` `/2023-11/admin/transfers`

List treasury transfers.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Items per page |
| `sort` | string | No | — | Sort field: `reference`, `amount`, `created_at`, `updated_at` |
| `include` | string | No | — | Comma-separated: `user`, `bookEntries`, `company` |
| `filter[source_id]` | ULID | No | — | Exact match on source treasury account |
| `filter[destination_id]` | ULID | No | — | Exact match on destination treasury account |
| `filter[treasury_account_id]` | ULID | No | — | Returns transfers where this account is either source or destination |
| `filter[search]` | string | No | — | Search on account label/type/code, reference, and amount |

Request example:

```http
GET /2023-11/admin/transfers?per_page=10&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "data": [
    {
      "id": "01jz...",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "user_id": "01jq...",
      "reference": "TRFBANK_1001",
      "amount": 50000.00,
      "currency": "XAF",
      "reason": "Operating fund transfer",
      "status": "completed",
      "source_id": "01ja...",
      "destination_id": "01jb...",
      "metadata": null,
      "created_at": "2024-03-15T10:23:45.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "source": {
        "id": "01ja...",
        "label": "Main Cash Account",
        "type": "cash",
        "balance": 1250000.00
      },
      "destination": {
        "id": "01jb...",
        "label": "Branch Account",
        "type": "bank",
        "balance": 820000.00
      }
    }
  ],
  "pagination": {
    "total": 14,
    "count": 10,
    "per_page": 10,
    "current_page": 1,
    "total_pages": 2
  }
}
```

Error responses:
- `400` — missing `X-Company`
- `401` — invalid token

---

### `POST` `/2023-11/admin/transfers`

Create a treasury transfer. Immediately deducts from source and credits destination; fails if the source has insufficient balance.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `source` | ULID | Yes | exists:treasury_accounts | Source treasury account ID |
| `destination` | ULID | Yes | exists:treasury_accounts | Destination treasury account ID |
| `amount` | number | Yes | — | Amount to transfer |
| `currency` | string | No | — | Currency code (e.g. `XAF`) |
| `reason` | string | No | — | Purpose of the transfer |

Request example:

```http
POST /2023-11/admin/transfers HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "source": "01ja...",
  "destination": "01jb...",
  "amount": 50000,
  "currency": "XAF",
  "reason": "Operating fund transfer"
}
```

Success response `200 OK`:

```json
{
  "id": "01jz...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "TRFBANK_1001",
  "amount": 50000.00,
  "currency": "XAF",
  "reason": "Operating fund transfer",
  "status": "completed",
  "source_id": "01ja...",
  "destination_id": "01jb...",
  "metadata": [],
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "source": { "id": "01ja...", "label": "Main Cash Account" },
  "destination": { "id": "01jb...", "label": "Branch Account" }
}
```

Error responses:
- `400` — source balance insufficient (`"Insufficient balance"`)
- `401` — invalid token
- `404` — source or destination treasury account not found

---

### `GET` `/2023-11/admin/transfers/{transfer}`

Retrieve a single transfer.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `transfer` | ULID | Transfer ID |

Request example:

```http
GET /2023-11/admin/transfers/01jz... HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

> **Note:** The controller `show` method body is not yet implemented. The route exists but currently returns an empty response. When implemented, it will return a `TransferResource` object matching the shape shown in the `POST` success response.

Success response `200 OK` (anticipated once implemented):

```json
{
  "id": "01jz...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "TRFBANK_1001",
  "amount": 50000.00,
  "currency": "XAF",
  "reason": "Operating fund transfer",
  "status": "completed",
  "source_id": "01ja...",
  "destination_id": "01jb...",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "source": { "id": "01ja...", "label": "Main Cash Account", "type": "cash", "balance": 1200000.00 },
  "destination": { "id": "01jb...", "label": "Branch Account", "type": "bank", "balance": 870000.00 }
}
```

---

### `PUT` `/2023-11/admin/transfers/{transfer}`

Update a transfer.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `transfer` | ULID | Transfer ID |

- **Request body (anticipated):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `amount` | number | No | — | New transfer amount |
| `currency` | string | No | — | Currency code (e.g. `XAF`) |
| `reason` | string | No | — | Updated reason |

Request example:

```http
PUT /2023-11/admin/transfers/01jz... HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "reason": "Updated reason for transfer"
}
```

> **Note:** The controller `update` method body is not yet implemented. The route exists but currently returns an empty response.

Success response `200 OK` (anticipated once implemented):

```json
{
  "id": "01jz...",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "user_id": "01jq...",
  "reference": "TRFBANK_1001",
  "amount": 50000.00,
  "currency": "XAF",
  "reason": "Updated reason for transfer",
  "status": "completed",
  "source_id": "01ja...",
  "destination_id": "01jb...",
  "metadata": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "source": { "id": "01ja...", "label": "Main Cash Account" },
  "destination": { "id": "01jb...", "label": "Branch Account" }
}
```

---

### `DELETE` `/2023-11/admin/transfers/{transfer}`

Delete a transfer. Automatically reverses the source/destination balance changes and reverts all associated book entries.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `transfer` | ULID | Transfer ID |

Request example:

```http
DELETE /2023-11/admin/transfers/01jz... HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

> **Note:** The controller `destroy` method body is not yet implemented. The route exists but balance reversal logic is defined in the model's `deleting` observer.

Success response `200 OK` (anticipated once implemented):

```json
{ "message": "Transfer deleted successfully" }
```

---
