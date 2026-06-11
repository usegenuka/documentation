---
title: "Suppliers & Procurement"
description: "This domain covers the full purchasing cycle: managing **Suppliers** (vendors who provide goods and services), **Supplier Products** (items purchased from suppl"
sidebarTitle: "Suppliers & Procurement"
---

This domain covers the full purchasing cycle: managing **Suppliers** (vendors who provide goods and services), **Supplier Products** (items purchased from suppliers), **Bills** (purchase invoices and purchase orders with payment tracking), **Bills of Materials** (production recipes linking raw-material stocks to finished-product stocks), **Production Orders** (executions of a bill of materials that move stock), and **Expense Types** (accounting categories for bill classification).

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/admin/suppliers` | List suppliers |
| `POST` | `/2023-11/admin/suppliers` | Create a supplier |
| `POST` | `/2023-11/admin/suppliers/export` | Export suppliers to CSV |
| `GET` | `/2023-11/admin/suppliers/{supplier}` | Get supplier details with stats |
| `PUT` | `/2023-11/admin/suppliers/{supplier}` | Update a supplier |
| `DELETE` | `/2023-11/admin/suppliers/{supplier}` | Soft-delete (or force-delete) a supplier |
| `POST` | `/2023-11/admin/suppliers/{supplier}/products/link` | Link catalog products to a supplier |
| `GET` | `/2023-11/admin/supplierProducts` | List supplier products |
| `POST` | `/2023-11/admin/supplierProducts` | Create a supplier product |
| `GET` | `/2023-11/admin/supplierProducts/handle/{supplierproduct}` | Get supplier product by handle |
| `GET` | `/2023-11/admin/supplierProducts/{supplierproduct}` | Get supplier product by ID |
| `PUT` | `/2023-11/admin/supplierProducts/{supplierproduct}` | Update a supplier product |
| `DELETE` | `/2023-11/admin/supplierProducts/{supplierproduct}` | Delete a supplier product |
| `PUT` | `/2023-11/admin/supplierProducts/{supplierproduct}/{field}` | Update a single field on a supplier product |
| `GET` | `/2023-11/admin/bills` | List bills |
| `POST` | `/2023-11/admin/bills` | Create a bill |
| `POST` | `/2023-11/admin/bills/export` | Async-export bills (xlsx/csv) |
| `GET` | `/2023-11/admin/bills/export/fields` | List exportable column names |
| `POST` | `/2023-11/admin/bills/import` | Import bills from xlsx/csv |
| `POST` | `/2023-11/admin/bills/purchase-order` | Create a bill as a purchase order |
| `GET` | `/2023-11/admin/bills/{billId}` | Get bill details |
| `GET` | `/2023-11/admin/bills/{billId}/products` | Get line-item supplier products for a bill |
| `PUT` | `/2023-11/admin/bills/{bill}` | Update a bill |
| `DELETE` | `/2023-11/admin/bills/{bill}` | Soft-delete (or force-delete) a bill |
| `POST` | `/2023-11/admin/bills/{bill}/payments` | Add a payment to a bill |
| `PUT` | `/2023-11/admin/bills/{bill}/payments/{payment}` | Update a bill payment |
| `DELETE` | `/2023-11/admin/bills/{bill}/payments/{payment}` | Delete a bill payment |
| `POST` | `/2023-11/admin/bills/{bill}/payments/{payment}/restore` | Restore a deleted bill payment |
| `PUT` | `/2023-11/admin/bills/{bill}/state` | Transition bill state (e.g. purchase order → bill) |
| `PUT` | `/2023-11/admin/bills/{bill}/status` | Update bill shipping/billing status |
| `GET` | `/2023-11/admin/bill-of-materials` | List bills of materials |
| `POST` | `/2023-11/admin/bill-of-materials` | Create a bill of materials |
| `GET` | `/2023-11/admin/bill-of-materials/reference/{billOfMaterial}` | Get bill of materials by reference |
| `GET` | `/2023-11/admin/bill-of-materials/{billOfMaterial}` | Get bill of materials by ID |
| `PUT` | `/2023-11/admin/bill-of-materials/{billOfMaterial}` | Update a bill of materials |
| `DELETE` | `/2023-11/admin/bill-of-materials/{billOfMaterial}` | Delete a bill of materials |
| `GET` | `/2023-11/admin/production-orders` | List production orders |
| `POST` | `/2023-11/admin/production-orders` | Create a production order |
| `GET` | `/2023-11/admin/production-orders/reference/{productionOrder}` | Get production order by reference |
| `GET` | `/2023-11/admin/production-orders/{productionOrder}` | Get production order by ID |
| `PUT` | `/2023-11/admin/production-orders/{productionOrder}` | Update a production order (stub — not yet implemented) |
| `DELETE` | `/2023-11/admin/production-orders/{productionOrder}` | Delete a production order |
| `GET` | `/2023-11/admin/expense-types` | List expense types |
| `POST` | `/2023-11/admin/expense-types` | Create an expense type |
| `PUT` | `/2023-11/admin/expense-types/{expenseType}` | Update an expense type |
| `DELETE` | `/2023-11/admin/expense-types/{expenseType}` | Delete an expense type |

---

## Suppliers

### `GET` `/2023-11/admin/suppliers`
List all suppliers for the authenticated company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page (max 100) |
| `include` | string | No | — | Comma-separated relations: `shops`, `addresses`, `tags`, `bills`, `products`, `bills_sum_amount`, `products_sold_count` |
| `filter[id]` | string | No | — | Exact match on supplier ID |
| `filter[company_name]` | string | No | — | Partial match on company name |
| `filter[first_name]` | string | No | — | Partial match on first name |
| `filter[last_name]` | string | No | — | Partial match on last name |
| `filter[email]` | string | No | — | Partial match on email |
| `filter[phone]` | string | No | — | Partial match on phone |
| `filter[search]` | string | No | — | Full-text search across name, email, phone, addresses, and custom fields |
| `filter[created_at][start]` | string | No | — | ISO 8601 date — lower bound |
| `filter[created_at][end]` | string | No | — | ISO 8601 date — upper bound |
| `filter[deleted]` | integer | No | — | `0` = active only, `1` = deleted only, any other value = all |
| `sort` | string | No | — | Sortable fields: `created_at`, `updated_at`, `company_name`, `first_name`, `last_name`, `phone`, `email`, `bills_count`, `bills_sum_amount`, `products_count`, `products_sum_amount`, `total_sales`. Prefix with `-` for descending. |

Request example:

```http
GET /2023-11/admin/suppliers?include=bills_sum_amount,billsCount&sort=-bills_count,-bills_sum_amount&per_page=15 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "data": [
    {
      "id": "01hn5mrmrbssrkaehtze05nzs8",
      "first_name": "Wilfried",
      "last_name": "Djopa",
      "company_name": "Ma Team",
      "email": "wdjopa@lamater.tech",
      "phone": "+33613202181",
      "metadata": { "custom_fields": [] },
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "total_sales": "48000.00",
      "orders_count": 12,
      "custom_fields": [],
      "default_address": null,
      "billing_address": null,
      "shipping_address": null
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

Error responses:
- `400 Bad Request` — missing `X-Company` header (`company_id_required`)
- `401 Unauthorized` — invalid or expired token

---

### `POST` `/2023-11/admin/suppliers`
Create a new supplier. Either `first_name` or `last_name` must be provided. Either `email` or `phone` must be provided. The combination of name + email (or name + phone) must be unique within the company.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `first_name` | string | Conditional | Required without `last_name` | Supplier first name |
| `last_name` | string | Conditional | Required without `first_name` | Supplier last name |
| `email` | string | Conditional | Required without `phone`; unique per company+name | Email address |
| `phone` | string | Conditional | Required without `email`; unique per company+name | Phone number |
| `company_name` | string | No | — | Supplier company / organisation name |
| `metadata` | object | No | — | Arbitrary key-value store; `custom_fields` sub-key is surfaced in the response |
| `shop_id` | string (ULID) | No | — | Attach supplier to a specific storefront |
| `tags` | array of strings | No | — | Tag names to attach |
| `addresses` | array of objects | No | — | Address objects to create for the supplier |
| `address` | object | No | — | Single address object to create |

Request example:

```http
POST /2023-11/admin/suppliers HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "email": "wdjopa@lamater.tech",
  "phone": "+33613202181",
  "company_name": "Ma Team"
}
```

Success response (`200 OK`):

```json
{
  "id": "01hn5mrmrbssrkaehtze05nzs8",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company_name": "Ma Team",
  "email": "wdjopa@lamater.tech",
  "phone": "+33613202181",
  "metadata": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "custom_fields": [],
  "default_address": null,
  "billing_address": null,
  "shipping_address": null
}
```

Error responses:
- `400 Bad Request` — validation failed (e.g. duplicate email+name within the company):
  ```json
  {
    "message": "validation.failed",
    "errors": { "email": ["The email has already been taken."] }
  }
  ```
- `500 Internal Server Error` — unexpected database error

---

### `POST` `/2023-11/admin/suppliers/export`
Export all suppliers for the company (or a specific shop) as a CSV file. The response is a binary CSV download.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `shop_id` | string (ULID) | No | — | Scope export to a specific shop |

Request example:

```http
POST /2023-11/admin/suppliers/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{}
```

Success response (`200 OK`): Binary CSV file (`Content-Disposition: attachment; filename="suppliers_<name>.csv"`).

Error responses:
- `401 Unauthorized` — invalid token

---

### `GET` `/2023-11/admin/suppliers/{supplier}`
Get full details for a single supplier including aggregated stats (total spent, total sold, top products, bills by day).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplier` | string (ULID) | Supplier ID |

Request example:

```http
GET /2023-11/admin/suppliers/01hn5mrmrbssrkaehtze05nzs8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01hn5mrmrbssrkaehtze05nzs8",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company_name": "Ma Team",
  "email": "wdjopa@lamater.tech",
  "phone": "+33613202181",
  "metadata": { "genukaId": "id", "custom_fields": [] },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "custom_fields": [],
  "default_address": null,
  "billing_address": null,
  "shipping_address": null,
  "shops": [],
  "addresses": [],
  "tags": [],
  "stats": {
    "order_sources": {},
    "total_spent": 125000.00,
    "total_sold": 48000.00,
    "potential_spent": 175000.00,
    "potential_sold": 60000.00,
    "bills_count": 14,
    "orders_count": 12,
    "top_orders_products": [],
    "top_bills_products": [],
    "bills_count_by_day": {}
  }
}
```

Error responses:
- `404 Not Found` — supplier does not exist or belongs to a different company

---

### `PUT` `/2023-11/admin/suppliers/{supplier}`
Update a supplier. All fields are optional; only provided fields are changed. Email and phone uniqueness is validated ignoring the current supplier's own values.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplier` | string (ULID) | Supplier ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `first_name` | string | No | — | First name |
| `last_name` | string | No | — | Last name |
| `email` | string | No | Unique per company+name (ignoring self) | Email address |
| `phone` | string | No | Unique per company+name (ignoring self) | Phone number |
| `company_name` | string | No | — | Supplier company name |
| `metadata` | object | No | — | Replaces the entire metadata object |
| `shop_id` | string (ULID) | No | — | Attach/sync to a storefront |
| `tags` | array | No | — | Tag names to attach (existing tags by ID are skipped) |

Request example:

```http
PUT /2023-11/admin/suppliers/01hn5mrmrbssrkaehtze05nzs8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "company_name": "LM J.",
  "metadata": { "genukaId": "id" }
}
```

Success response (`200 OK`):

```json
{
  "id": "01hn5mrmrbssrkaehtze05nzs8",
  "first_name": "Wilfried",
  "last_name": "Djopa",
  "company_name": "LM J.",
  "email": "wdjopa@lamater.tech",
  "phone": "+33613202181",
  "metadata": { "genukaId": "id" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "deleted_at": null,
  "custom_fields": [],
  "default_address": null,
  "billing_address": null,
  "shipping_address": null
}
```

Error responses:
- `400 Bad Request` — duplicate email/phone within the company
- `404 Not Found` — supplier not found

---

### `DELETE` `/2023-11/admin/suppliers/{supplier}`
Soft-delete a supplier. Pass `force=true` to permanently delete.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplier` | string (ULID) | Supplier ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `force` | boolean | No | false | Permanently delete the supplier |

Request example:

```http
DELETE /2023-11/admin/suppliers/01hn5mrmrbssrkaehtze05nzs8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{ "message": "Supplier deleted" }
```

Error responses:
- `404 Not Found` — supplier not found

---

### `POST` `/2023-11/admin/suppliers/{supplier}/products/link`
Bulk-assign catalog `Product` records to a supplier by setting `supplier_id` on each product.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplier` | string (ULID) | Supplier ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `productIds` | array of strings | Yes | — | Array of catalog product IDs to link |

Request example:

```http
POST /2023-11/admin/suppliers/01hn5mrmrbssrkaehtze05nzs8/products/link HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "productIds": [
    "01jgwvrk88778p7yc4ge8mece3",
    "01jsm6nb4vawegmrzdnmkwdt4j"
  ]
}
```

Success response (`200 OK`):

```json
{
  "status": "terminated",
  "errors": []
}
```

The `errors` array contains human-readable messages for any products that failed to link.

Error responses:
- `404 Not Found` — supplier not found

---

## Supplier Products

Supplier products (`supplierproducts`) are purchasable items linked to bills. They may optionally reference a catalog `ProductVariant`.

### `GET` `/2023-11/admin/supplierProducts`
List all supplier products for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `include` | string | No | — | Comma-separated: `shops`, `bills`, `stocks`, `total_billed_quantity`, `total_billed_amount` |
| `filter[published]` | boolean | No | — | Exact match on published flag |
| `filter[collections]` | string | No | — | Comma-separated collection IDs |
| `filter[search]` | string | No | — | Full-text search on `title` and `handle` |
| `filter[created_at][start]` | string | No | — | ISO 8601 lower bound |
| `filter[created_at][end]` | string | No | — | ISO 8601 upper bound |
| `sort` | string | No | `title` | Sortable: `title`, `price`, `handle`, `billsCount`, `created_at`, `updated_at`, `total_bills`. Prefix `-` for descending. |

Request example:

```http
GET /2023-11/admin/supplierProducts?per_page=15&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "data": [
    {
      "id": "01hn5vwx124rn7adgss5rkc9nd",
      "title": "Farine de blé",
      "handle": "farine-de-ble",
      "price": 6500.00,
      "sku": null,
      "barcode": null,
      "content": "",
      "metadata": null,
      "product_variant_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-03-15T10:23:45.000000Z",
      "deleted_at": null,
      "medias": [],
      "tags": [],
      "supplier": null
    }
  ],
  "pagination": {
    "total": 42,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 3
  }
}
```

---

### `POST` `/2023-11/admin/supplierProducts`
Create a new supplier product. The `handle` is auto-generated from the title (slugified); if a collision exists, a random 8-character suffix is appended.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | — | Product name |
| `content` | string | No | — | Description / body HTML |
| `price` | number | No | — | Purchase unit price |
| `sku` | string | No | — | Stock-keeping unit |
| `barcode` | string | No | — | Barcode |
| `metadata` | object | No | — | Arbitrary metadata |
| `medias` | array | No | — | Media objects to attach |
| `tags` | array of strings | No | — | Tag names to attach |
| `shop_ids` | array of strings | No | — | Storefront IDs to scope this product to; defaults to all company shops |

Request example:

```http
POST /2023-11/admin/supplierProducts HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Farine de blé",
  "content": "",
  "price": 6500,
  "medias": []
}
```

Success response (`200 OK`):

```json
{
  "id": "01hn5vwx124rn7adgss5rkc9nd",
  "title": "Farine de blé",
  "handle": "farine-de-ble",
  "price": 6500.00,
  "sku": null,
  "barcode": null,
  "content": "",
  "metadata": null,
  "product_variant_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": null,
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "medias": [],
  "tags": [],
  "supplier": null
}
```

---

### `GET` `/2023-11/admin/supplierProducts/handle/{supplierproduct}`
Retrieve a supplier product by its handle slug. Same response shape as the ID-based show endpoint.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplierproduct` | string | URL-friendly handle (e.g. `farine-de-ble`) |

- **Query parameters:** Supports the same `include` values as the list endpoint.

Request example:

```http
GET /2023-11/admin/supplierProducts/handle/farine-de-ble HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01hn5vwx124rn7adgss5rkc9nd",
  "title": "Farine de blé",
  "handle": "farine-de-ble",
  "price": 6500.00,
  "sku": null,
  "barcode": null,
  "content": "",
  "metadata": null,
  "product_variant_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "medias": [],
  "tags": [],
  "supplier": null
}
```

---

### `GET` `/2023-11/admin/supplierProducts/{supplierproduct}`
Get a single supplier product by ID. Passing `analytics=1` adds detailed analytics data (top suppliers, total bills, total sales, related products).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplierproduct` | string (ULID) | Supplier product ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `include` | string | No | — | `shops`, `bills`, `total_billed_quantity`, `total_billed_amount` |
| `analytics` | integer | No | — | Set to `1` to include analytics payload |
| `limit` | integer | No | 5 | Number of top items returned in analytics |

Request example:

```http
GET /2023-11/admin/supplierProducts/01hn5vwx124rn7adgss5rkc9nd HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01hn5vwx124rn7adgss5rkc9nd",
  "title": "Farine de blé",
  "handle": "farine-de-ble",
  "price": 6500.00,
  "sku": null,
  "barcode": null,
  "content": "",
  "metadata": null,
  "product_variant_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "deleted_at": null,
  "medias": [],
  "tags": [],
  "supplier": { "id": "01hn5mrmrbssrkaehtze05nzs8", "first_name": "Wilfried", "..." }
}
```

Error responses:
- `404 Not Found` — supplier product not found

---

### `PUT` `/2023-11/admin/supplierProducts/{supplierproduct}`
Update a supplier product. The handle is regenerated from the new title.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplierproduct` | string (ULID) | Supplier product ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | — | Product name (handle is regenerated from this) |
| `content` | string | No | — | Description / body HTML |
| `price` | number | No | — | Purchase unit price |
| `sku` | string | No | — | Stock-keeping unit |
| `barcode` | string | No | — | Barcode |
| `metadata` | object | No | — | Arbitrary metadata |
| `medias` | array | No | — | Media objects to attach |
| `tags` | array of strings | No | — | Tag names to attach |
| `shop_ids` | array of strings | No | — | Storefront IDs to scope this product to |

Request example:

```http
PUT /2023-11/admin/supplierProducts/01hn5vwx124rn7adgss5rkc9nd HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Farine de blé premium",
  "price": 7000,
  "content": "High-quality wheat flour"
}
```

Success response (`200 OK`):

```json
{
  "id": "01hn5vwx124rn7adgss5rkc9nd",
  "title": "Farine de blé premium",
  "handle": "farine-de-ble-premium",
  "price": 7000.00,
  "sku": null,
  "barcode": null,
  "content": "High-quality wheat flour",
  "metadata": null,
  "product_variant_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "deleted_at": null,
  "medias": [],
  "tags": [],
  "supplier": {
    "id": "01hn5mrmrbssrkaehtze05nzs8",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "company_name": "Ma Team",
    "email": "wdjopa@lamater.tech",
    "phone": "+33613202181",
    "metadata": null,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "created_at": "2024-01-10T08:00:00.000000Z",
    "updated_at": "2024-03-15T10:23:45.000000Z",
    "deleted_at": null,
    "custom_fields": [],
    "default_address": null,
    "billing_address": null,
    "shipping_address": null
  }
}
```

---

### `DELETE` `/2023-11/admin/supplierProducts/{supplierproduct}`
Soft-delete a supplier product.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplierproduct` | string (ULID) | Supplier product ID |

Request example:

```http
DELETE /2023-11/admin/supplierProducts/01hn5vwx124rn7adgss5rkc9nd HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{ "message": "Supplierproduct deleted successfully" }
```

---

### `PUT` `/2023-11/admin/supplierProducts/{supplierproduct}/{field}`
Update a single named field on a supplier product. Supported fields include any model attribute, plus the special value `shops` which replaces the many-to-many shop associations.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `supplierproduct` | string (ULID) | Supplier product ID |
| `field` | string | Field name to update (e.g. `price`, `published`, `shops`) |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `value` | mixed | Yes | — | New value. For `shops` this should be an array of shop IDs (replaces existing). |

Request example:

```http
PUT /2023-11/admin/supplierProducts/01hn5vwx124rn7adgss5rkc9nd/price HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "value": 7500
}
```

Success response (`200 OK`):

```json
{
  "id": "01hn5vwx124rn7adgss5rkc9nd",
  "title": "Farine de blé",
  "handle": "farine-de-ble",
  "price": 7500.00,
  "sku": null,
  "barcode": null,
  "content": "",
  "metadata": null,
  "product_variant_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-16T10:00:00.000000Z",
  "deleted_at": null,
  "medias": [],
  "tags": [],
  "supplier": null
}
```

---

## Bills

A Bill represents a purchase invoice from a supplier. It tracks line items (supplier products), billing information, payment status, and optionally shipping. Bills can also exist in a `purchase_order` state before being converted to a formal bill.

### `GET` `/2023-11/admin/bills`
List all bills for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `include` | string | No | — | `company`, `shop`, `supplier`, `supplierproducts` |
| `filter[state]` | integer | No | — | Exact match on bill state (1 = bill, 2 = purchase order) |
| `filter[expense_type]` | string | No | — | Exact match on expense type handle |
| `filter[reference]` | string | No | — | Partial match on reference number |
| `filter[amount]` | string | No | — | Partial match on amount |
| `filter[billing->status]` | string | No | — | Partial match on billing status |
| `filter[shipping->status]` | string | No | — | Partial match on shipping status |
| `filter[supplier_id]` | string (ULID) | No | — | Filter by supplier ID |
| `filter[created_by]` | array | No | — | Array of user IDs |
| `filter[search]` | string | No | — | Full-text search across supplier, shop, products, reference, and metadata |
| `sort` | string | No | — | Sortable: `reference`, `state`, `amount`, `source`, `created_at`, `updated_at`, `supplierproducts_count`, `shop_name`, `supplier_first_name`, `supplier_last_name`, `supplier_company_name`. Prefix `-` for descending. |

Request example:

```http
GET /2023-11/admin/bills?filter[state]=1&sort=-created_at&per_page=15 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "data": [
    {
      "id": "01k2hasmw27x5r9202h4wyc7r4",
      "reference": "BILL-1042",
      "amount": 5000.00,
      "state": 1,
      "expense_type": "RAW_MATERIALS",
      "source": "DASHBOARD",
      "currency": "XAF",
      "due_date": "2025-10-13T16:11:33.000000Z",
      "paid_at": null,
      "billing": {
        "address_id": null,
        "method": "01k1amypn6xycwe7w948jp82zm",
        "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
        "treasury_account_label": "Cash",
        "status": "pending",
        "subtotal": 5000.00,
        "total": 5000.00
      },
      "shipping": null,
      "metadata": { "note": "" },
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shop_id": null,
      "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
      "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2025-10-13T16:11:33.000000Z",
      "updated_at": "2025-10-13T16:11:33.000000Z",
      "deleted_at": null,
      "supplier": { "id": "01hn5mrmrbssrkaehtze05nzs8", "first_name": "Wilfried", "..." }
    }
  ],
  "pagination": {
    "total": 156,
    "count": 15,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 11
  }
}
```

---

### `POST` `/2023-11/admin/bills`
Create a new bill (purchase invoice). Products are linked via `supplier_product_id` on each line item. The reference number is auto-generated. Billing totals are derived from the `billing.total` value.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `state` | integer | No | 1 | `1` = bill, `2` = purchase order |
| `expense_type` | string | No | — | Expense type handle (e.g. `RAW_MATERIALS`) |
| `source` | string | No | `Dashboard` | Source label (uppercased automatically) |
| `due_date` | string (ISO 8601) | No | now | Payment due date |
| `created_at` | string (ISO 8601) | No | now | Override creation timestamp |
| `metadata` | object | No | — | Arbitrary metadata; `note` sub-key is common |
| `products` | array | Yes | — | Array of line-item objects (see below) |
| `products[].supplier_product_id` | string (ULID) | Yes | Exists in `supplierproducts` | Supplier product ID |
| `products[].title` | string | Yes | — | Line item display title |
| `products[].price` | number | Yes | — | Unit price |
| `products[].quantity` | number | Yes | — | Quantity |
| `products[].metadata` | object | No | — | Per-line metadata |
| `billing` | object | Yes | — | Payment details (see below) |
| `billing.method` | string | Yes | — | Treasury account ID used as method |
| `billing.treasury_account_id` | string (ULID) | Yes | Exists | Treasury account to debit |
| `billing.status` | string | Yes | `pending` or `paid` | Initial billing status |
| `billing.subtotal` | number | No | — | Pre-tax total |
| `billing.total` | number | Yes | — | Total amount (sets `bill.amount`) |
| `billing.address_id` | string (ULID) | No | — | Billing address ID |
| `supplier_id` | string (ULID) | No | — | Supplier ID (resolved by the process pipeline) |
| `shop_id` | string (ULID) | No | — | Shop to scope the bill to |
| `order_id` | string (ULID) | No | — | Link this bill to a sales order |
| `credit_code` | string | No | — | Accounting credit account code |
| `debit_code` | string | No | — | Accounting debit account code |

Request example:

```http
POST /2023-11/admin/bills HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "state": 1,
  "expense_type": "RAW_MATERIALS",
  "source": null,
  "products": [
    {
      "supplier_product_id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Abonnement",
      "price": 5000,
      "quantity": 1
    }
  ],
  "metadata": { "note": "" },
  "created_at": "2025-10-13T16:11:33.698Z",
  "due_date": "2025-10-13T16:11:33.698Z",
  "billing": {
    "address_id": null,
    "method": "01k1amypn6xycwe7w948jp82zm",
    "status": "pending",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "total": 5000,
    "subtotal": 5000
  }
}
```

Success response (`200 OK`):

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1043",
  "amount": 5000.00,
  "state": 1,
  "expense_type": "RAW_MATERIALS",
  "source": "DASHBOARD",
  "currency": "XAF",
  "due_date": "2025-10-13T16:11:33.000000Z",
  "paid_at": null,
  "billing": {
    "address_id": null,
    "method": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "status": "pending",
    "subtotal": 5000.00,
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 250000.00 }
  },
  "shipping": null,
  "metadata": { "note": "" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-10-13T16:11:33.000000Z",
  "updated_at": "2025-10-13T16:11:33.000000Z",
  "deleted_at": null,
  "supplier": {
    "id": "01hn5mrmrbssrkaehtze05nzs8",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "company_name": "Ma Team",
    "email": "wdjopa@lamater.tech",
    "phone": "+33613202181",
    "metadata": null,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "created_at": "2024-01-10T08:00:00.000000Z",
    "updated_at": "2024-03-15T10:23:45.000000Z",
    "deleted_at": null,
    "custom_fields": [],
    "default_address": null,
    "billing_address": null,
    "shipping_address": null
  },
  "addresses": [],
  "shop": null,
  "custom_fields": [],
  "user": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Wilfried Djopa", "email": "wdjopa@lamater.tech" },
  "subscriptions": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Ma Team" },
  "delivery": null,
  "payments": [],
  "products": [
    {
      "id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Abonnement",
      "handle": "abonnement",
      "price": 5000.00,
      "sku": null,
      "barcode": null,
      "content": "",
      "metadata": null,
      "product_variant_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
      "created_at": "2025-01-10T08:00:00.000000Z",
      "updated_at": "2025-01-10T08:00:00.000000Z",
      "deleted_at": null,
      "medias": [],
      "tags": [],
      "supplier": null,
      "pivot": { "quantity": 1, "price": 5000.00 }
    }
  ],
  "medias": [],
  "orders": [],
  "credit_notes": []
}
```

Error responses:
- `422 Unprocessable Entity` — validation errors from nested process tasks
- `500 Internal Server Error` — database error (response includes `message`)

---

### `POST` `/2023-11/admin/bills/export`
Trigger an async export of bills. The export is queued and delivered via the company's notification channel. Returns a job acknowledgment immediately.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `format` | string | No | `xlsx` | Export format: `xlsx` or `csv` |
| `columns` | array of strings | No | — | Specific column names to include |
| `supplier` | boolean | No | false | Include supplier columns |
| Any `filter[*]` param | — | No | — | Filters are forwarded from the request |

Request example:

```http
POST /2023-11/admin/bills/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "format": "xlsx",
  "supplier": true
}
```

Success response — two possible shapes depending on dataset size:

- **Small dataset (< 500 rows)** — `200 OK`: Binary file download with `Content-Disposition: attachment; filename="bills.<format>"` and appropriate `Content-Type` (e.g. `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` for xlsx).
- **Large dataset (≥ 500 rows)** — `202 Accepted`: Job queued; the file link is delivered by email.

```json
{
  "status": "queued",
  "message": "Votre export est en cours de generation. Vous recevrez un email avec le lien de telechargement des qu'il sera pret."
}
```

Error responses:
- `401 Unauthorized` — user not authenticated
- `422 Unprocessable Entity` — unsupported format value

---

### `GET` `/2023-11/admin/bills/export/fields`
Return the list of available column names for bill exports.

Request example:

```http
GET /2023-11/admin/bills/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "columns": ["id", "reference", "amount", "state", "expense_type", "created_at", "..."]
}
```

---

### `POST` `/2023-11/admin/bills/import`
Import bills from an xlsx, xls, or csv file.

- **Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `import_file` | file | Yes | `mimes:xlsx,xls,csv` | The spreadsheet to import |

Request example:

```http
POST /2023-11/admin/bills/import HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: multipart/form-data

import_file=<binary spreadsheet>
```

Success response (`200 OK`):

```json
{ "message": "Bills imported successfully" }
```

Error responses:
- `422 Unprocessable Entity` — file validation failed
- `500 Internal Server Error` — parsing/import error

---

### `POST` `/2023-11/admin/bills/purchase-order`
Create a bill in the **purchase order** state (state = 2). If the expense type is not `SUPPLIER`, the bill is immediately marked as paid via `PaidBillProcess` and stock movements are applied.

- **Request body:** Same fields as `POST /2023-11/admin/bills`. Key fields are listed below. Set `state: 2` (or omit — the process will enforce state 2).

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `state` | integer | No | — | Will be enforced as `2` (purchase order) by this endpoint |
| `expense_type` | string | No | — | Expense type handle. If not `SUPPLIER`, bill is auto-paid and stock is moved |
| `products` | array | Yes | — | Array of line-item objects (same as `POST /2023-11/admin/bills`) |
| `products[].supplier_product_id` | string (ULID) | Yes | Exists in `supplierproducts` | Supplier product ID |
| `products[].title` | string | Yes | — | Line item display title |
| `products[].price` | number | Yes | — | Unit price |
| `products[].quantity` | number | Yes | — | Quantity |
| `billing` | object | Yes | — | Billing details (same as `POST /2023-11/admin/bills`) |
| `billing.method` | string | Yes | — | Treasury account ID used as payment method |
| `billing.treasury_account_id` | string (ULID) | Yes | Exists | Treasury account to debit |
| `billing.status` | string | Yes | `pending` or `paid` | Initial billing status |
| `billing.total` | number | Yes | — | Total amount (sets `bill.amount`) |
| `due_date` | string (ISO 8601) | No | now | Payment due date |
| `metadata` | object | No | — | Arbitrary metadata; `note` sub-key is common |
| `supplier_id` | string (ULID) | No | — | Supplier ID |
| `shop_id` | string (ULID) | No | — | Shop to scope the bill to |

Request example:

```http
POST /2023-11/admin/bills/purchase-order HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "expense_type": "RAW_MATERIALS",
  "products": [
    {
      "supplier_product_id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Farine",
      "price": 5000,
      "quantity": 10
    }
  ],
  "billing": {
    "method": "01k1amypn6xycwe7w948jp82zm",
    "status": "pending",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "total": 50000
  }
}
```

Success response (`200 OK`):

```json
{
  "id": "01k2hasmw27x5r9202h4wyc8p5",
  "reference": "PO-2025-002",
  "amount": 50000.00,
  "state": 2,
  "expense_type": "RAW_MATERIALS",
  "source": "DASHBOARD",
  "currency": "XAF",
  "due_date": "2025-10-13T16:11:33.000000Z",
  "paid_at": "2025-10-13T16:11:33.000000Z",
  "billing": {
    "address_id": null,
    "method": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "status": "paid",
    "subtotal": 50000.00,
    "total": 50000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 200000.00 }
  },
  "shipping": null,
  "metadata": { "note": "" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "supplier_id": null,
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-10-13T16:11:33.000000Z",
  "updated_at": "2025-10-13T16:11:33.000000Z",
  "deleted_at": null,
  "supplier": null,
  "addresses": null,
  "shop": null,
  "custom_fields": [],
  "user": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Wilfried Djopa", "email": "wdjopa@lamater.tech" },
  "subscriptions": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Ma Team" },
  "delivery": null,
  "payments": [
    {
      "id": "01k3pqr00000000000000000ab",
      "amount": 50000.00,
      "method": "01k1amypn6xycwe7w948jp82zm",
      "type": "OUTBOUND",
      "paid_at": "2025-10-13T16:11:33.000000Z",
      "deleted_at": null
    }
  ],
  "products": [
    {
      "id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Farine",
      "handle": "farine",
      "price": 5000.00,
      "sku": null,
      "barcode": null,
      "content": "",
      "metadata": null,
      "product_variant_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": null,
      "created_at": "2025-01-10T08:00:00.000000Z",
      "updated_at": "2025-01-10T08:00:00.000000Z",
      "deleted_at": null,
      "medias": [],
      "tags": [],
      "supplier": null,
      "pivot": { "quantity": 10, "price": 5000.00 }
    }
  ],
  "medias": [],
  "orders": [],
  "credit_notes": []
}
```

---

### `GET` `/2023-11/admin/bills/{billId}`
Get full details for a single bill including supplier, products, payments, credit notes, and accounting info.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `billId` | string (ULID) | Bill ID |

Request example:

```http
GET /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 5000.00,
  "state": 1,
  "expense_type": "RAW_MATERIALS",
  "source": "DASHBOARD",
  "currency": "XAF",
  "due_date": "2025-10-13T16:11:33.000000Z",
  "paid_at": null,
  "billing": {
    "address_id": null,
    "method": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "status": "pending",
    "subtotal": 5000.00,
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "..." }
  },
  "shipping": null,
  "metadata": { "note": "" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-10-13T16:11:33.000000Z",
  "updated_at": "2025-10-13T16:11:33.000000Z",
  "deleted_at": null,
  "supplier": { "id": "01hn5mrmrbssrkaehtze05nzs8", "first_name": "Wilfried", "..." },
  "addresses": [],
  "shop": null,
  "custom_fields": [],
  "user": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "..." },
  "subscriptions": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "..." },
  "delivery": null,
  "payments": [],
  "products": [
    {
      "id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Abonnement",
      "price": 5000.00,
      "pivot": { "quantity": 1, "price": 5000.00 },
      "..."
    }
  ],
  "medias": [],
  "orders": [],
  "credit_notes": []
}
```

Error responses:
- `404 Not Found` — bill not found (via `firstOrFail`)

---

### `GET` `/2023-11/admin/bills/{billId}/products`
Return the raw list of supplier products attached to a bill (without full `BillDetailResource` wrapping).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `billId` | string (ULID) | Bill ID |

Request example:

```http
GET /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/products HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
[
  {
    "id": "01k2hasmw27x5r9202h4wyc7r3",
    "title": "Abonnement",
    "handle": "abonnement",
    "price": 5000.00,
    "sku": null,
    "barcode": null,
    "content": "",
    "metadata": null,
    "product_variant_id": null,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
    "created_at": "2025-01-10T08:00:00.000000Z",
    "updated_at": "2025-01-10T08:00:00.000000Z",
    "deleted_at": null,
    "pivot": {
      "bill_id": "01k2hasmw27x5r9202h4wyc7r4",
      "supplierproduct_id": "01k2hasmw27x5r9202h4wyc7r3",
      "quantity": 1,
      "price": 5000.00,
      "metadata": null
    }
  }
]
```

Error responses:
- `404 Not Found` — bill not found

---

### `PUT` `/2023-11/admin/bills/{bill}`
Update an existing bill. All product lines are re-synced (existing pivot rows are replaced). Existing accounting book entries are reverted and recreated.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |

- **Request body:** Same fields as `POST /2023-11/admin/bills`. Key fields are listed below.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `state` | integer | No | `1` or `2` | Bill state: `1` = bill, `2` = purchase order |
| `expense_type` | string | No | — | Expense type handle |
| `source` | string | No | — | Source label |
| `due_date` | string (ISO 8601) | No | — | Payment due date |
| `created_at` | string (ISO 8601) | No | — | Override creation timestamp |
| `metadata` | object | No | — | Arbitrary metadata; `note` sub-key is common |
| `products` | array | No | — | Array of line-item objects; replaces all existing lines |
| `products[].supplier_product_id` | string (ULID) | Yes (per item) | Exists in `supplierproducts` | Supplier product ID |
| `products[].title` | string | Yes (per item) | — | Line item display title |
| `products[].price` | number | Yes (per item) | — | Unit price |
| `products[].quantity` | number | Yes (per item) | — | Quantity |
| `billing` | object | No | — | Billing details to update |
| `billing.method` | string | No | — | Treasury account ID used as payment method |
| `billing.treasury_account_id` | string (ULID) | No | Exists | Treasury account |
| `billing.status` | string | No | — | Billing status |
| `billing.total` | number | No | — | Total amount (updates `bill.amount`) |
| `supplier_id` | string (ULID) | No | — | Supplier ID |
| `shop_id` | string (ULID) | No | — | Shop to scope the bill to |

Request example:

```http
PUT /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "products": [
    {
      "supplier_product_id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Abonnement",
      "price": 6000,
      "quantity": 1
    }
  ],
  "billing": {
    "method": "01k1amypn6xycwe7w948jp82zm",
    "status": "pending",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "total": 6000
  }
}
```

Success response (`200 OK`):

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 6000.00,
  "state": 1,
  "expense_type": "RAW_MATERIALS",
  "source": "DASHBOARD",
  "currency": "XAF",
  "due_date": "2025-10-13T16:11:33.000000Z",
  "paid_at": null,
  "billing": {
    "address_id": null,
    "method": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "status": "pending",
    "subtotal": 6000.00,
    "total": 6000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 250000.00 }
  },
  "shipping": null,
  "metadata": { "note": "" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-10-13T16:11:33.000000Z",
  "updated_at": "2025-10-14T09:00:00.000000Z",
  "deleted_at": null,
  "supplier": {
    "id": "01hn5mrmrbssrkaehtze05nzs8",
    "first_name": "Wilfried",
    "last_name": "Djopa",
    "company_name": "Ma Team",
    "email": "wdjopa@lamater.tech",
    "phone": "+33613202181",
    "metadata": null,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "created_at": "2024-01-10T08:00:00.000000Z",
    "updated_at": "2024-03-15T10:23:45.000000Z",
    "deleted_at": null,
    "custom_fields": [],
    "default_address": null,
    "billing_address": null,
    "shipping_address": null
  },
  "addresses": [],
  "shop": null,
  "custom_fields": [],
  "user": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Wilfried Djopa", "email": "wdjopa@lamater.tech" },
  "subscriptions": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Ma Team" },
  "delivery": null,
  "payments": [],
  "products": [
    {
      "id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Abonnement",
      "handle": "abonnement",
      "price": 6000.00,
      "sku": null,
      "barcode": null,
      "content": "",
      "metadata": null,
      "product_variant_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
      "created_at": "2025-01-10T08:00:00.000000Z",
      "updated_at": "2025-01-10T08:00:00.000000Z",
      "deleted_at": null,
      "medias": [],
      "tags": [],
      "supplier": null,
      "pivot": { "quantity": 1, "price": 6000.00 }
    }
  ],
  "medias": [],
  "orders": [],
  "credit_notes": []
}
```

Error responses:
- `500 Internal Server Error` — update failed (includes `message`)

---

### `DELETE` `/2023-11/admin/bills/{bill}`
Soft-delete a bill. Pass `force=true` to permanently delete.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `force` | boolean | No | false | Permanently delete the bill |

Request example:

```http
DELETE /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response: On soft-delete (`200 OK`): full `BillDetailResource` with `deleted_at` set. On force-delete (`200 OK`): `{"message":"Bill deleted"}`.

Error responses:
- `404 Not Found` — bill not found

---

### `POST` `/2023-11/admin/bills/{bill}/payments`
Add a payment to a bill. Creates a `Payment` record (type = `OUTBOUND`), updates the bill's billing status (`pending` → `partially_paid` → `paid` based on totals).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `amount` | number | Yes | — | Payment amount |
| `treasury_account_id` | string (ULID) | Conditional | Required unless `billing.treasury_account_id` is set | Treasury account to debit |
| `billing.treasury_account_id` | string (ULID) | Conditional | — | Alternative way to pass treasury account |
| `date` | string (ISO 8601) | No | now | Date the payment was made |
| `no_response` | boolean | No | false | Return only `{"message":"Bill status updated"}` instead of full bill |

Request example:

```http
POST /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/payments HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "amount": 5000,
  "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
  "date": "2025-10-13T16:11:33.000Z"
}
```

Success response (`200 OK`): Full `BillDetailResource` with updated `payments` array and `billing.status`. The `payments` array will contain the newly added payment:

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 5000.00,
  "state": 1,
  "expense_type": "RAW_MATERIALS",
  "source": "DASHBOARD",
  "currency": "XAF",
  "due_date": "2025-10-13T16:11:33.000000Z",
  "paid_at": "2025-10-13T16:15:00.000000Z",
  "billing": {
    "address_id": null,
    "method": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "status": "paid",
    "subtotal": 5000.00,
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 245000.00 }
  },
  "shipping": null,
  "metadata": { "note": "" },
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-10-13T16:11:33.000000Z",
  "updated_at": "2025-10-13T16:15:00.000000Z",
  "deleted_at": null,
  "supplier": { "id": "01hn5mrmrbssrkaehtze05nzs8", "first_name": "Wilfried", "last_name": "Djopa", "company_name": "Ma Team", "email": "wdjopa@lamater.tech", "phone": "+33613202181", "metadata": null, "company_id": "01hqydxwtxdj3kmzp3bz7jk73g", "created_at": "2024-01-10T08:00:00.000000Z", "updated_at": "2024-03-15T10:23:45.000000Z", "deleted_at": null, "custom_fields": [], "default_address": null, "billing_address": null, "shipping_address": null },
  "addresses": [],
  "shop": null,
  "custom_fields": [],
  "user": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Wilfried Djopa", "email": "wdjopa@lamater.tech" },
  "subscriptions": [],
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Ma Team" },
  "delivery": null,
  "payments": [
    {
      "id": "01k3xyzmw27x5r9202h4wyc7r9",
      "amount": 5000.00,
      "method": "01k1amypn6xycwe7w948jp82zm",
      "type": "OUTBOUND",
      "paid_at": "2025-10-13T16:11:33.000000Z",
      "deleted_at": null
    }
  ],
  "products": [
    {
      "id": "01k2hasmw27x5r9202h4wyc7r3",
      "title": "Abonnement",
      "handle": "abonnement",
      "price": 5000.00,
      "sku": null,
      "barcode": null,
      "content": "",
      "metadata": null,
      "product_variant_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "supplier_id": "01hn5mrmrbssrkaehtze05nzs8",
      "created_at": "2025-01-10T08:00:00.000000Z",
      "updated_at": "2025-01-10T08:00:00.000000Z",
      "deleted_at": null,
      "medias": [],
      "tags": [],
      "supplier": null,
      "pivot": { "quantity": 1, "price": 5000.00 }
    }
  ],
  "medias": [],
  "orders": [],
  "credit_notes": []
}
```

Error responses:
- `500 Internal Server Error` — payment creation failed

---

### `PUT` `/2023-11/admin/bills/{bill}/payments/{payment}`
Update an existing payment on a bill (via `UpdatePaymentProcess`).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |
| `payment` | string (ULID) | Payment ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `amount` | number | No | — | New payment amount |
| `date` | string (ISO 8601) | No | — | New payment date |
| `treasury_account_id` | string (ULID) | No | — | New treasury account |

Request example:

```http
PUT /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/payments/01k3xyzmw27x5r9202h4wyc7r9 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "amount": 2500
}
```

Success response (`200 OK`): Full `BillDetailResource` with the updated payment reflected in `payments[]`. Same shape as `GET /2023-11/admin/bills/{billId}`.

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 5000.00,
  "state": 1,
  "billing": {
    "status": "partially_paid",
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 247500.00 }
  },
  "payments": [
    {
      "id": "01k3xyzmw27x5r9202h4wyc7r9",
      "amount": 2500.00,
      "method": "01k1amypn6xycwe7w948jp82zm",
      "type": "OUTBOUND",
      "paid_at": "2025-10-13T16:11:33.000000Z",
      "deleted_at": null
    }
  ],
  "..."
}
```

Error responses:
- `400 Bad Request` — update error (via `BusinessException`)

---

### `DELETE` `/2023-11/admin/bills/{bill}/payments/{payment}`
Delete a payment from a bill. The bill's billing status is recalculated. Activity is logged.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |
| `payment` | string (ULID) | Payment ID |

Request example:

```http
DELETE /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/payments/01k3xyzmw27x5r9202h4wyc7r9 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`): Full `BillDetailResource` with the deleted payment removed from `payments[]` and `billing.status` recalculated. Same shape as `GET /2023-11/admin/bills/{billId}`.

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 5000.00,
  "state": 1,
  "billing": {
    "status": "pending",
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 250000.00 }
  },
  "payments": [],
  "..."
}
```

Error responses:
- `400 Bad Request` — deletion error

---

### `POST` `/2023-11/admin/bills/{bill}/payments/{payment}/restore`
Restore a soft-deleted payment. Activity is logged.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |
| `payment` | string (ULID) | Payment ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `no_response` | boolean | No | false | Return only `{"message":"Bill payment restored"}` |

Request example:

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/payments/01k3xyzmw27x5r9202h4wyc7r9/restore HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`): Full `BillDetailResource` with the restored payment back in `payments[]` and `billing.status` recalculated. Same shape as `GET /2023-11/admin/bills/{billId}`.

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 5000.00,
  "state": 1,
  "billing": {
    "status": "paid",
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 245000.00 }
  },
  "payments": [
    {
      "id": "01k3xyzmw27x5r9202h4wyc7r9",
      "amount": 5000.00,
      "method": "01k1amypn6xycwe7w948jp82zm",
      "type": "OUTBOUND",
      "paid_at": "2025-10-13T16:11:33.000000Z",
      "deleted_at": null
    }
  ],
  "..."
}
```

Error responses:
- `400 Bad Request` — restore error

---

### `PUT` `/2023-11/admin/bills/{bill}/state`
Transition the bill's state. Currently supports transitioning from `purchase_order` (2) to `bill` (1), which generates a new reference number and stores the original purchase order ID in metadata.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `state` | integer | Yes | One of the valid `BillState` values | Target state. `1` = bill, `2` = purchase order |

Request example:

```http
PUT /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/state HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "state": 1
}
```

Success response (`200 OK`): Full `BillDetailResource` with the updated `state` and new `reference`. Same shape as `GET /2023-11/admin/bills/{billId}`.

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1044",
  "amount": 5000.00,
  "state": 1,
  "expense_type": "RAW_MATERIALS",
  "source": "DASHBOARD",
  "currency": "XAF",
  "due_date": "2025-10-13T16:11:33.000000Z",
  "paid_at": null,
  "metadata": { "note": "", "purchase_bill_id": "01k2hasmw27x5r9202h4wyc7r4" },
  "billing": {
    "status": "pending",
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 250000.00 }
  },
  "payments": [],
  "..."
}
```

Error responses:
- `400 Bad Request` — invalid state value

---

### `PUT` `/2023-11/admin/bills/{bill}/status`
Update the bill's shipping and/or billing status. Can also update metadata. If `billing` is present, a payment is processed via `PaidBillProcess`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `bill` | string (ULID) | Bill ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `shipping` | object | No | — | Object with `status` key to update shipping status |
| `billing` | object | No | — | Billing update (triggers `PaidBillProcess`; same shape as billing on bill create) |
| `metadata` | object | No | — | Replace entire metadata |
| `no_response` | boolean | No | false | Return `{"message":"Bill status updated"}` instead of full bill |

Request example:

```http
PUT /2023-11/admin/bills/01k2hasmw27x5r9202h4wyc7r4/status HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "shipping": { "status": "delivered" }
}
```

Success response (`200 OK`): Full `BillDetailResource` with the updated shipping/billing status. Same shape as `GET /2023-11/admin/bills/{billId}`.

```json
{
  "id": "01k2hasmw27x5r9202h4wyc7r4",
  "reference": "BILL-1042",
  "amount": 5000.00,
  "state": 1,
  "billing": {
    "status": "pending",
    "total": 5000.00,
    "treasury_account": { "id": "01k1amypn6xycwe7w948jp82zm", "label": "Cash", "balance": 250000.00 }
  },
  "shipping": {
    "status": "delivered"
  },
  "payments": [],
  "..."
}
```

When `no_response: true` is passed:

```json
{ "message": "Bill status updated" }
```

---

## Bills of Materials

A Bill of Materials (BoM) defines a production recipe: it links **input** stocks (raw materials consumed) to **output** stocks (finished goods produced). Quantities are stored on the pivot.

### `GET` `/2023-11/admin/bill-of-materials`
List all bills of materials for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `include` | string | No | — | `company`, `inputs`, `outputs` |
| `filter[title]` | string | No | — | Partial match on title |
| `filter[search]` | string | No | — | Full-text search on title |
| `sort` | string | No | — | `title`, `created_at`, `updated_at`. Prefix `-` for descending. |

Request example:

```http
GET /2023-11/admin/bill-of-materials?include=inputs,outputs HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "data": [
    {
      "id": "01jsmeejsgewcadygdrxqcsett",
      "title": "Fabrication de Pain",
      "description": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "metadata": null,
      "created_at": "2025-04-10T08:00:00.000000Z",
      "updated_at": "2025-04-10T08:00:00.000000Z",
      "deleted_at": null,
      "inputs": [
        { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.35, "stock_quantity": 100 }
      ],
      "outputs": [
        { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 0 }
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

---

### `POST` `/2023-11/admin/bill-of-materials`
Create a bill of materials with input and output stock references. Inputs and outputs are synced via pivot tables; existing associations are replaced.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | Yes | — | BoM name |
| `description` | string | No | — | Description |
| `inputs` | array | Yes | — | Array of input objects |
| `inputs[].id` | string (ULID) | Yes | Exists in `stocks` | Stock ID of raw material |
| `inputs[].quantity` | number | Yes | — | Quantity consumed per cycle |
| `outputs` | array | Yes | — | Array of output objects |
| `outputs[].id` | string (ULID) | Yes | Exists in `stocks` | Stock ID of finished good |
| `outputs[].quantity` | number | Yes | — | Quantity produced per cycle |

Request example:

```http
POST /2023-11/admin/bill-of-materials HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Fabrication de Pain",
  "inputs": [
    { "id": "01jgwvrk88778p7yc4ge8mece3", "quantity": 0.35 },
    { "id": "01jsm6nb4vawegmrzdnmkwdt4j", "quantity": 0.15 }
  ],
  "outputs": [
    { "id": "01jsm6stzjy5ett1jefnghm7ad", "quantity": 1 }
  ]
}
```

Success response (`201 Created`):

```json
{
  "id": "01jsmeejsgewcadygdrxqcsett",
  "title": "Fabrication de Pain",
  "description": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "metadata": null,
  "created_at": "2025-04-10T08:00:00.000000Z",
  "updated_at": "2025-04-10T08:00:00.000000Z",
  "deleted_at": null,
  "inputs": [
    { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.35, "stock_quantity": 100 },
    { "id": "01jsm6nb4vawegmrzdnmkwdt4j", "title": "Levure", "quantity": 0.15, "stock_quantity": 50 }
  ],
  "outputs": [
    { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 0 }
  ],
  "medias": []
}
```

Error responses:
- `422 Unprocessable Entity` — `inputs` or `outputs` missing
- `404 Not Found` — a stock ID in inputs or outputs does not exist

---

### `GET` `/2023-11/admin/bill-of-materials/reference/{billOfMaterial}`
Retrieve a bill of materials by its reference value (route-model-bound by the `billOfMaterial` parameter slug). Response shape is identical to the ID-based show endpoint.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `billOfMaterial` | string | BoM reference value |

Request example:

```http
GET /2023-11/admin/bill-of-materials/reference/PAIN-001 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01jsmeejsgewcadygdrxqcsett",
  "title": "Fabrication de Pain",
  "description": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "metadata": null,
  "created_at": "2025-04-10T08:00:00.000000Z",
  "updated_at": "2025-04-10T08:00:00.000000Z",
  "deleted_at": null,
  "inputs": [
    { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.35, "stock_quantity": 100 }
  ],
  "outputs": [
    { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 0 }
  ],
  "medias": []
}
```

---

### `GET` `/2023-11/admin/bill-of-materials/{billOfMaterial}`
Get a single bill of materials by ID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `billOfMaterial` | string (ULID) | BoM ID |

Request example:

```http
GET /2023-11/admin/bill-of-materials/01jsmeejsgewcadygdrxqcsett HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01jsmeejsgewcadygdrxqcsett",
  "title": "Fabrication de Pain",
  "description": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "metadata": null,
  "created_at": "2025-04-10T08:00:00.000000Z",
  "updated_at": "2025-04-10T08:00:00.000000Z",
  "deleted_at": null,
  "inputs": [
    { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.35, "stock_quantity": 100 }
  ],
  "outputs": [
    { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 0 }
  ],
  "medias": []
}
```

---

### `PUT` `/2023-11/admin/bill-of-materials/{billOfMaterial}`
Update a bill of materials. Inputs and outputs are fully re-synced (replacing existing pivot rows).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `billOfMaterial` | string (ULID) | BoM ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | — | BoM name |
| `description` | string | No | — | Description |
| `inputs` | array | No | — | Array of input objects; replaces all existing inputs |
| `inputs[].id` | string (ULID) | Yes (per item) | Exists in `stocks` | Stock ID of raw material |
| `inputs[].quantity` | number | Yes (per item) | — | Quantity consumed per cycle |
| `outputs` | array | No | — | Array of output objects; replaces all existing outputs |
| `outputs[].id` | string (ULID) | Yes (per item) | Exists in `stocks` | Stock ID of finished good |
| `outputs[].quantity` | number | Yes (per item) | — | Quantity produced per cycle |

Request example:

```http
PUT /2023-11/admin/bill-of-materials/01jsmeejsgewcadygdrxqcsett HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "title": "Fabrication de Pain Complet",
  "inputs": [
    { "id": "01jgwvrk88778p7yc4ge8mece3", "quantity": 0.40 }
  ],
  "outputs": [
    { "id": "01jsm6stzjy5ett1jefnghm7ad", "quantity": 1 }
  ]
}
```

Success response (`200 OK`):

```json
{
  "id": "01jsmeejsgewcadygdrxqcsett",
  "title": "Fabrication de Pain Complet",
  "description": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "metadata": null,
  "created_at": "2025-04-10T08:00:00.000000Z",
  "updated_at": "2025-04-15T10:00:00.000000Z",
  "deleted_at": null,
  "inputs": [
    { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.40, "stock_quantity": 100 }
  ],
  "outputs": [
    { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 0 }
  ],
  "medias": []
}
```

---

### `DELETE` `/2023-11/admin/bill-of-materials/{billOfMaterial}`
Soft-delete a bill of materials.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `billOfMaterial` | string (ULID) | BoM ID |

Request example:

```http
DELETE /2023-11/admin/bill-of-materials/01jsmeejsgewcadygdrxqcsett HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{ "message": "Bill of material deleted successfully" }
```

---

## Production Orders

A Production Order executes a Bill of Materials for a given number of cycles. On creation, the system checks stock availability, deducts input stocks (FIFO by warehouse), and adds the produced output stocks to the target warehouse. Deleting a production order reverses these stock movements.

### `GET` `/2023-11/admin/production-orders`
List all production orders for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `include` | string | No | — | `company`, `billOfMaterial`, `productionWarehouse` |
| `filter[status]` | string | No | — | Exact match on status |
| `filter[production_warehouse_id]` | string (ULID) | No | — | Exact match on warehouse |
| `filter[reference]` | string | No | — | Partial match on reference |
| `filter[created_by]` | array | No | — | Array of user IDs |
| `filter[search]` | string | No | — | Full-text search across user, BoM title, reference, metadata |
| `filter[created_at][start]` | string | No | — | ISO 8601 lower bound |
| `filter[created_at][end]` | string | No | — | ISO 8601 upper bound |
| `filter[scheduled_at][start]` | string | No | — | ISO 8601 lower bound |
| `filter[scheduled_at][end]` | string | No | — | ISO 8601 upper bound |
| `sort` | string | No | — | `reference`, `status`, `created_at`, `updated_at`. Prefix `-` for descending. |

Request example:

```http
GET /2023-11/admin/production-orders?filter[status]=pending&sort=-created_at HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "data": [
    {
      "id": "01jt2xyzabc78p7yc4ge8mecf1",
      "reference": "PO-2025-001",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "bill_of_material_id": "01jsmeejsgewcadygdrxqcsett",
      "production_warehouse_id": null,
      "cycle": 5,
      "batch_number": null,
      "note": null,
      "status": "pending",
      "production_date": null,
      "expiration_date": null,
      "scheduled_at": null,
      "end_at": null,
      "metadata": null,
      "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2025-05-10T08:00:00.000000Z",
      "updated_at": "2025-05-10T08:00:00.000000Z",
      "deleted_at": null,
      "bill_of_material": { "id": "01jsmeejsgewcadygdrxqcsett", "title": "Fabrication de Pain", "..." },
      "production_warehouse": null
    }
  ],
  "pagination": {
    "total": 12,
    "count": 12,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

---

### `POST` `/2023-11/admin/production-orders`
Create a production order. The system validates that enough stock is available for the requested `cycle` count, then deducts raw-material stock (FIFO) and produces finished-good stock in the target warehouse.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `bill_of_material_id` | string (ULID) | Yes | Exists in `bill_of_materials` | BoM to execute |
| `cycle` | integer | No | 1 | Number of production cycles to run |
| `reference` | string | No | Auto-generated | Production order reference; auto-generated if omitted |
| `production_warehouse_id` | string (ULID) | No | First available warehouse | Target warehouse for output stocks |
| `status` | string | No | — | Initial status (e.g. `pending`, `completed`) |
| `production_date` | string (ISO 8601 date) | No | — | Date of production |
| `expiration_date` | string (ISO 8601 date) | No | — | Expiration date for produced stocks |
| `scheduled_at` | string (ISO 8601 date) | No | — | Scheduled production date |
| `end_at` | string (ISO 8601 date) | No | — | Actual end date |
| `batch_number` | string | No | — | Batch number for traceability |
| `note` | string | No | — | Internal notes |
| `metadata` | object | No | — | Arbitrary metadata |

Request example:

```http
POST /2023-11/admin/production-orders HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "bill_of_material_id": "01jsmeejsgewcadygdrxqcsett",
  "cycle": 5,
  "production_date": "2025-06-15",
  "expiration_date": "2025-12-31",
  "note": "Morning batch"
}
```

Success response (`201 Created`):

```json
{
  "id": "01jt2xyzabc78p7yc4ge8mecf1",
  "reference": "PO-2025-001",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "bill_of_material_id": "01jsmeejsgewcadygdrxqcsett",
  "production_warehouse_id": null,
  "cycle": 5,
  "batch_number": null,
  "note": "Morning batch",
  "status": null,
  "production_date": "2025-06-15T00:00:00.000000Z",
  "expiration_date": "2025-12-31T00:00:00.000000Z",
  "scheduled_at": null,
  "end_at": null,
  "metadata": null,
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z",
  "deleted_at": null,
  "bill_of_material": { "id": "01jsmeejsgewcadygdrxqcsett", "..." },
  "production_warehouse": null
}
```

Error responses:
- `404 Not Found` — bill of materials not found
- `422 Unprocessable Entity` — insufficient stock for the requested cycle count:
  ```json
  {
    "status": "error",
    "code": 422,
    "data": null,
    "message": "The production order cannot be produced with the current stock"
  }
  ```
- `500 Internal Server Error` — stock movement failed

---

### `GET` `/2023-11/admin/production-orders/reference/{productionOrder}`
Retrieve a production order by its reference. Response shape is identical to the ID-based show endpoint.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productionOrder` | string | Production order reference (e.g. `PO-2025-001`) |

Request example:

```http
GET /2023-11/admin/production-orders/reference/PO-2025-001 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01jt2xyzabc78p7yc4ge8mecf1",
  "reference": "PO-2025-001",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "bill_of_material_id": "01jsmeejsgewcadygdrxqcsett",
  "production_warehouse_id": null,
  "cycle": 5,
  "batch_number": null,
  "note": "Morning batch",
  "status": null,
  "production_date": "2025-06-15T00:00:00.000000Z",
  "expiration_date": "2025-12-31T00:00:00.000000Z",
  "scheduled_at": null,
  "end_at": null,
  "metadata": null,
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z",
  "deleted_at": null,
  "bill_of_material": {
    "id": "01jsmeejsgewcadygdrxqcsett",
    "title": "Fabrication de Pain",
    "description": null,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "metadata": null,
    "created_at": "2025-04-10T08:00:00.000000Z",
    "updated_at": "2025-04-10T08:00:00.000000Z",
    "deleted_at": null,
    "inputs": [
      { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.35, "stock_quantity": 98.25 }
    ],
    "outputs": [
      { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 5 }
    ],
    "medias": []
  },
  "production_warehouse": null
}
```

---

### `GET` `/2023-11/admin/production-orders/{productionOrder}`
Get a single production order by ID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productionOrder` | string (ULID) | Production order ID |

Request example:

```http
GET /2023-11/admin/production-orders/01jt2xyzabc78p7yc4ge8mecf1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "id": "01jt2xyzabc78p7yc4ge8mecf1",
  "reference": "PO-2025-001",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "bill_of_material_id": "01jsmeejsgewcadygdrxqcsett",
  "production_warehouse_id": null,
  "cycle": 5,
  "batch_number": null,
  "note": "Morning batch",
  "status": null,
  "production_date": "2025-06-15T00:00:00.000000Z",
  "expiration_date": "2025-12-31T00:00:00.000000Z",
  "scheduled_at": null,
  "end_at": null,
  "metadata": null,
  "user_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2025-06-09T10:00:00.000000Z",
  "updated_at": "2025-06-09T10:00:00.000000Z",
  "deleted_at": null,
  "bill_of_material": {
    "id": "01jsmeejsgewcadygdrxqcsett",
    "title": "Fabrication de Pain",
    "description": null,
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "metadata": null,
    "created_at": "2025-04-10T08:00:00.000000Z",
    "updated_at": "2025-04-10T08:00:00.000000Z",
    "deleted_at": null,
    "inputs": [
      { "id": "01jgwvrk88778p7yc4ge8mece3", "title": "Farine", "quantity": 0.35, "stock_quantity": 98.25 }
    ],
    "outputs": [
      { "id": "01jsm6stzjy5ett1jefnghm7ad", "title": "Pain", "quantity": 1, "stock_quantity": 5 }
    ],
    "medias": []
  },
  "production_warehouse": null
}
```

Error responses:
- `404 Not Found` — production order not found

---

### `PUT` `/2023-11/admin/production-orders/{productionOrder}`
Update a production order.

> **Note:** This endpoint is declared in the route table but the controller method body is currently empty (not yet implemented). It returns an empty `200 OK` response.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productionOrder` | string (ULID) | Production order ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `cycle` | integer | No | — | Number of production cycles |
| `reference` | string | No | — | Production order reference |
| `production_warehouse_id` | string (ULID) | No | Exists in `warehouses` | Target warehouse for output stocks |
| `status` | string | No | — | Status (e.g. `pending`, `completed`) |
| `production_date` | string (ISO 8601 date) | No | — | Date of production |
| `expiration_date` | string (ISO 8601 date) | No | — | Expiration date for produced stocks |
| `scheduled_at` | string (ISO 8601 date) | No | — | Scheduled production date |
| `end_at` | string (ISO 8601 date) | No | — | Actual end date |
| `batch_number` | string | No | — | Batch number for traceability |
| `note` | string | No | — | Internal notes |
| `metadata` | object | No | — | Arbitrary metadata |

**Success response (`200 OK`):** Empty body — the controller implementation is pending.

```json
{}
```

---

### `DELETE` `/2023-11/admin/production-orders/{productionOrder}`
Delete a production order. **Side effect:** all associated stock movements are reversed (input stocks are restored, output stocks are decremented). Pass `force=true` to permanently delete.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productionOrder` | string (ULID) | Production order ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `force` | boolean | No | false | Permanently delete instead of soft-delete |

Request example:

```http
DELETE /2023-11/admin/production-orders/01jt2xyzabc78p7yc4ge8mecf1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{ "message": "Production order deleted" }
```

Error responses:
- `404 Not Found` — production order not found

---

## Expense Types

Expense types are accounting categories used to classify bills. Each type has a debit and credit account code pair, and must be unique by handle within the company.

### `GET` `/2023-11/admin/expense-types`
List all expense types for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[search]` | string | No | — | Full-text search on `name` |
| `filter[created_at][start]` | string | No | — | ISO 8601 lower bound |
| `filter[created_at][end]` | string | No | — | ISO 8601 upper bound |
| `sort` | string | No | — | `name`, `created_at`, `updated_at`. Prefix `-` for descending. |

Request example:

```http
GET /2023-11/admin/expense-types?sort=name HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`200 OK`):

```json
{
  "data": [
    {
      "id": "01k1amypn6xycwe7w948jp82zn",
      "name": "Raw Materials",
      "handle": "RAW_MATERIALS",
      "description": "Purchase of raw materials for production",
      "debit_code": "601",
      "credit_code": "401",
      "metadata": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "created_at": "2024-01-10T08:00:00.000000Z",
      "updated_at": "2024-01-10T08:00:00.000000Z",
      "icon": "https://cdn.genuka.com/icon.webp"
    }
  ],
  "pagination": {
    "total": 4,
    "count": 4,
    "per_page": 15,
    "current_page": 1,
    "total_pages": 1
  }
}
```

---

### `POST` `/2023-11/admin/expense-types`
Create an expense type. The `handle` must be unique within the company. `credit_code` and `debit_code` must differ.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `handle` | string | Yes | Unique per company; max 255 chars | Machine-readable identifier (e.g. `RAW_MATERIALS`) |
| `name` | string | No | — | Human-readable display name |
| `description` | string | No | — | Description |
| `debit_code` | string | No | Must differ from `credit_code` | Accounting debit account code |
| `credit_code` | string | No | Must differ from `debit_code` | Accounting credit account code |
| `metadata` | object | No | — | Arbitrary metadata |
| `icon` | file or string | No | — | Icon: either an uploaded file (stored in media collection `icon`) or a string name (stored in `metadata.icon`) |

Request example:

```http
POST /2023-11/admin/expense-types HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "handle": "RAW_MATERIALS",
  "name": "Raw Materials",
  "description": "Purchase of raw materials for production",
  "debit_code": "601",
  "credit_code": "401"
}
```

Success response (`200 OK`):

```json
{
  "id": "01k1amypn6xycwe7w948jp82zn",
  "name": "Raw Materials",
  "handle": "RAW_MATERIALS",
  "description": "Purchase of raw materials for production",
  "debit_code": "601",
  "credit_code": "401",
  "metadata": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-03-15T10:23:45.000000Z",
  "updated_at": "2024-03-15T10:23:45.000000Z",
  "icon": "Tag"
}
```

Error responses:
- `400 Bad Request` — `credit_code` equals `debit_code`:
  ```json
  {
    "message": "validation.failed",
    "errors": { "credit_code": ["Credit code and debit code must be different"] }
  }
  ```
- `422 Unprocessable Entity` — `handle` not unique in company:
  ```json
  {
    "message": "The handle has already been taken.",
    "errors": { "handle": ["The handle has already been taken."] }
  }
  ```

---

### `PUT` `/2023-11/admin/expense-types/{expenseType}`
Update an expense type. All fields are optional. `credit_code` and `debit_code` must still differ if both are provided.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `expenseType` | string (ULID) | Expense type ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `handle` | string | No | Unique per company; max 255 chars | Machine-readable identifier (e.g. `RAW_MATERIALS`) |
| `name` | string | No | — | Human-readable display name |
| `description` | string | No | — | Description |
| `debit_code` | string | No | Must differ from `credit_code` | Accounting debit account code |
| `credit_code` | string | No | Must differ from `debit_code` | Accounting credit account code |
| `metadata` | object | No | — | Arbitrary metadata |
| `icon` | file or string | No | — | Icon: either an uploaded file (stored in media collection `icon`) or a string name (stored in `metadata.icon`) |

Request example:

```http
PUT /2023-11/admin/expense-types/01k1amypn6xycwe7w948jp82zn HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Raw Materials (Updated)",
  "debit_code": "602",
  "credit_code": "401"
}
```

Success response (`200 OK`):

```json
{
  "id": "01k1amypn6xycwe7w948jp82zn",
  "name": "Raw Materials (Updated)",
  "handle": "RAW_MATERIALS",
  "description": "Purchase of raw materials for production",
  "debit_code": "602",
  "credit_code": "401",
  "metadata": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "created_at": "2024-01-10T08:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "icon": "Tag"
}
```

Error responses:
- `400 Bad Request` — `credit_code` equals `debit_code`

---

### `DELETE` `/2023-11/admin/expense-types/{expenseType}`
Delete an expense type. All bills that referenced this expense type are reassigned to the default expense type (the one with `handle = null`, or the first available).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `expenseType` | string (ULID) | Expense type ID |

Request example:

```http
DELETE /2023-11/admin/expense-types/01k1amypn6xycwe7w948jp82zn HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response (`204 No Content`): Empty body.
