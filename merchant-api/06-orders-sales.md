---
title: "Orders & Sales"
description: "The Orders & Sales domain is the transactional core of the Genuka platform. It manages the full lifecycle of a sale: from order creation through state transitio"
sidebarTitle: "Orders & Sales"
---

The Orders & Sales domain is the transactional core of the Genuka platform. It manages the full lifecycle of a sale: from order creation through state transitions (quotation → sales order → invoice), payment tracking, invoice generation, and post-sale documents such as credit notes. Related sub-resources include order products, order activities, order subscriptions, and per-order payments.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/orders` | List orders (paginated, filterable) |
| POST | `/2023-11/admin/orders` | Create a new order |
| POST | `/2023-11/admin/orders/export` | Export orders to XLSX/CSV (async) |
| GET | `/2023-11/admin/orders/export/fields` | List exportable column names |
| POST | `/2023-11/admin/orders/import` | Import orders from an XLSX/XLS/CSV file |
| POST | `/2023-11/admin/orders/import-chunk` | Upload a single chunk for chunked import |
| POST | `/2023-11/admin/orders/merge-chunk` | Merge uploaded chunks and run import |
| GET | `/2023-11/admin/orders/overview` | Orders KPI overview (counts, revenue, unpaid) |
| PUT | `/2023-11/admin/orders/status/bulk-update` | Bulk-update shipping/billing status on multiple orders |
| GET | `/2023-11/admin/orders/{orderId}` | Get order details |
| PUT | `/2023-11/admin/orders/{orderId}` | Update an order |
| DELETE | `/2023-11/admin/orders/{orderId}` | Cancel (soft-delete) an order |
| GET | `/2023-11/admin/orders/{orderId}/activities` | List order activity log |
| GET | `/2023-11/admin/orders/{orderId}/products` | List order product lines |
| PATCH | `/2023-11/admin/orders/{orderId}/products/{orderProductId}/status` | Update a single order product line status |
| POST | `/2023-11/admin/orders/{orderId}/restore` | Restore a cancelled order as a new duplicate |
| PUT | `/2023-11/admin/orders/{orderId}/state` | Transition order state (quotation / sales order / invoice) |
| POST | `/2023-11/admin/orders/{order}/notify` | Send order notification to customer and shop users |
| POST | `/2023-11/admin/orders/{order}/payments` | Add a payment to an order |
| PUT | `/2023-11/admin/orders/{order}/payments/{payment}` | Update a payment |
| DELETE | `/2023-11/admin/orders/{order}/payments/{payment}` | Delete a payment |
| POST | `/2023-11/admin/orders/{order}/payments/{payment}/restore` | Restore a deleted payment |
| PUT | `/2023-11/admin/orders/{order}/status` | Update shipping and/or billing status |
| GET | `/2023-11/admin/order_subscriptions` | List order-subscription links |
| GET | `/2023-11/admin/invoices` | List invoices (paginated, filterable) |
| POST | `/2023-11/admin/invoices` | Create an invoice from an order or deliveries |
| GET | `/2023-11/admin/invoices/{invoice}` | Get invoice details |
| PUT | `/2023-11/admin/invoices/{invoice}` | Update invoice status |
| DELETE | `/2023-11/admin/invoices/{invoice}` | Delete a pending invoice |
| DELETE | `/2023-11/admin/invoices/{invoice}/payments/{payment}` | Remove a payment from an invoice |
| GET | `/2023-11/invoices/{orderId}` | Render invoice as HTML view (public) |
| GET | `/2023-11/invoices/{orderId}/invoice` | Render or download invoice as PDF (public) |
| GET | `/2023-11/invoice/{orderId}` | Get order detail by order ID (public alias) |
| PUT | `/2023-11/invoice/{order}/signature` | Attach a signature to an order |
| POST | `/2023-11/invoice-generator/prepare` | Store transient invoice data, return a token |
| GET | `/2023-11/invoice-generator/{token}` | Render a tokenized invoice as HTML (auto-prints) |
| GET | `/2023-11/admin/credit-notes` | List credit notes |
| GET | `/2023-11/admin/credit-notes/{creditNote}` | Get credit note details |
| GET | `/2023-11/checkout` | Render the Mamoni checkout payment page (public) |

---

## Orders

### `GET` `/2023-11/admin/orders`
List orders with rich filtering, sorting, and optional relationship includes.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | no | 1 | Page number |
| `per_page` | integer | no | 15 | Results per page (max 100) |
| `simple_paginate` | boolean | no | false | Use simple (cursor-style) pagination — response omits total counts |
| `include` | string | no | — | Comma-separated: `company`, `shop`, `customer`, `products`, `products_count`, `taxes`, `discounts`, `shipping_fee`, `pickup_location`, `delivery`, `deliveries`, `adjustments` |
| `sort` | string | no | — | Prefix with `-` for descending. Allowed: `reference`, `state`, `amount`, `source`, `due_at`, `created_at`, `updated_at`, `products_count`, `scheduled_at`, `shop_name`, `customer_first_name`, `customer_last_name` |
| `filter[reference]` | string | no | — | Partial match on order reference |
| `filter[state]` | integer | no | — | Exact match on order state integer |
| `filter[active]` | boolean | no | — | `true` = non-deleted, non-cancelled; `false` = deleted orders only |
| `filter[billing_status]` | string | no | — | `pending`, `paid`, `partially_paid`, `unpaid`, `canceled` |
| `filter[shipping->status]` | string | no | — | Partial match on shipping status |
| `filter[customer_id]` | string (ULID) | no | — | Exact match on customer ID |
| `filter[customer.first_name]` | string | no | — | Partial match |
| `filter[customer.last_name]` | string | no | — | Partial match |
| `filter[customer.email]` | string | no | — | Partial match |
| `filter[customer.phone]` | string | no | — | Partial match |
| `filter[shop.id]` | string | no | — | Partial match on shop ID |
| `filter[shop.name]` | string | no | — | Partial match on shop name |
| `filter[created_by]` | array | no | — | Filter by creator user IDs |
| `filter[supplier_id]` | string | no | — | Orders containing products from this supplier |
| `filter[tax_id]` | string | no | — | Orders that include this tax |
| `filter[discount_id]` | string | no | — | Orders that include this discount |
| `filter[shipping_fee_id]` | string | no | — | Orders using this shipping fee |
| `filter[pickup_location_id]` | string | no | — | Orders with this pickup location |
| `filter[has_subscription]` | boolean | no | — | Filter by whether the order is linked to a subscription |
| `filter[unpaid]` | boolean | no | — | Invoiced orders with unpaid/pending/partially-paid billing |
| `filter[late]` | boolean | no | — | Invoiced orders past their due date with pending shipping |
| `filter[created_at][start]` | date | no | — | ISO 8601 or `Y-m-d` lower bound on creation date |
| `filter[created_at][end]` | date | no | — | ISO 8601 or `Y-m-d` upper bound on creation date |
| `filter[updated_at][start]` | date | no | — | Lower bound on last update date |
| `filter[updated_at][end]` | date | no | — | Upper bound on last update date |
| `filter[scheduled_at][start]` | date | no | — | Lower bound on delivery scheduled date |
| `filter[scheduled_at][end]` | date | no | — | Upper bound on delivery scheduled date |
| `filter[created_period][start]` | date | no | — | Invoiced orders created within a period (no TZ shift) |
| `filter[created_period][end]` | date | no | — | Upper bound for `created_period` |
| `filter[paid_at][start]` | date | no | — | Orders with inbound paid payments within this period |
| `filter[paid_at][end]` | date | no | — | Upper bound for `paid_at` |
| `filter[search]` | string | no | — | Full-text search across customer fields, product title, reference, source, metadata |
| `anonymous` | boolean | no | — | When `false`, excludes anonymous/ghost orders |
| `whereColumn` | string | no | — | Low-level column filter; combine with `whereOperator` and `whereValue` |
| `whereOperator` | string | no | — | `=`, `>`, `<`, `in`, etc. |
| `whereValue` | string | no | — | Value(s) for `whereColumn` filter |

Request example:

```http
GET /2023-11/admin/orders?page=1&per_page=5&include=products,delivery,customer&filter[billing_status]=paid&sort=-created_at HTTP/1.1
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
      "id": "01k76xt7z87katy392s6w335cp",
      "reference": "#ORDER_1174",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
      "user_id": "01k1amyqgjje8sknkzpm78qv6x",
      "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
      "amount": 15000.00,
      "amount_due": 15000.00,
      "currency": "XAF",
      "source": "EXCEL",
      "state": 1,
      "status": "pending",
      "billing": {
        "total": 15000.00,
        "amount": 14000.00,
        "method": "pawapay",
        "status": "pending",
        "subtotal": 15000.00,
        "net_to_pay": 15000.00,
        "discount": 0,
        "shipping": 0,
        "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
        "scheduled_at": "18/01/2024"
      },
      "shipping": {
        "mode": "delivery",
        "amount": 0,
        "status": "pending",
        "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
        "scheduled_at": "2024-01-18"
      },
      "due_at": null,
      "discounts": [],
      "customer": { "...": "CustomerResource" },
      "created_at": "2025-09-18T00:00:00.000000Z",
      "updated_at": "2025-10-10T11:24:05.000000Z",
      "deleted_at": null
    }
  ],
  "pagination": {
    "total": 284,
    "count": 5,
    "per_page": 5,
    "current_page": 1,
    "total_pages": 57
  }
}
```

Error responses:
- `400` Missing `X-Company` header: `{"message":"Company id required","error":"company_id_required","code":400}`
- `401` Invalid or missing token.
- `403` Token lacks order read permission.

---

### `POST` `/2023-11/admin/orders`
Create a new order. Triggers `UpsertOrderProcess` which handles customer upsert, product creation, address linking, and—if billing status is already `paid`—runs payment processing automatically.

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `source` | string | no | Order source: `EXCEL`, `WHATSAPP`, `WEB`, `POS`, etc. |
| `currency` | string | no | ISO 4217 currency code (e.g. `XAF`) |
| `customer` | object | yes | Customer information (see below) |
| `customer.id` | string (ULID) | no | Existing customer ULID — updates if found |
| `customer.first_name` | string | no | |
| `customer.last_name` | string | no | |
| `customer.email` | string | no | |
| `customer.phone` | string | no | |
| `customer.city` | string | no | |
| `customer.line1` | string | no | Billing/default address line 1 |
| `customer.force` | boolean | no | Force-create even if a duplicate is detected |
| `products` | array | yes | Array of product lines |
| `products[].title` | string | yes | Product name |
| `products[].price` | number | yes | Unit price (decimal) |
| `products[].quantity` | integer | yes | Ordered quantity |
| `products[].cost` | number | no | Cost price |
| `products[].product_id` | string (ULID) | no | Link to an existing product |
| `products[].variant_id` | string (ULID) | no | Link to a specific variant |
| `shipping` | object | no | Shipping information |
| `shipping.mode` | string | no | `delivery` or `pickup` |
| `shipping.address_id` | string (ULID) | no | Existing address ID |
| `shipping.address` | object | no | Inline address (country, line1, city, …) |
| `shipping.scheduled_at` | date | no | Scheduled delivery date (`Y-m-d`) |
| `shipping.shipping_fee_id` | string (ULID) | no | |
| `billing` | object | no | Billing information |
| `billing.amount` | number | no | Amount paid (for partial/full payment) |
| `billing.method` | string | no | Payment method label (e.g. `cash`, `notchpay`, `pawapay`) |
| `billing.scheduled_at` | string | no | Billing scheduled date |
| `billing.address` | object | no | Inline billing address |
| `billing.treasury_account_id` | string (ULID) | no | Treasury account to record payment in |
| `created_at` | ISO 8601 | no | Override the creation timestamp |
| `shop_id` | string (ULID) | no | Target shop; defaults to the company's default shop |

Request example:

```http
POST /2023-11/admin/orders HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "source": "EXCEL",
  "currency": "XAF",
  "customer": {
    "last_name": "Gaëlle",
    "phone": "697120598",
    "city": "Douala",
    "line1": "Bali DHL"
  },
  "products": [
    {
      "title": "Sac bleu",
      "price": 15000,
      "quantity": 1,
      "cost": 3800
    }
  ],
  "shipping": {
    "mode": "delivery",
    "scheduled_at": "2024-01-18",
    "address": {
      "country": "Cameroon",
      "line1": "Bali DHL",
      "city": "Douala"
    }
  },
  "billing": {
    "amount": 14000,
    "method": "pawapay",
    "scheduled_at": "18/01/2024"
  },
  "created_at": "2025-09-18T00:00:00.000Z"
}
```

Success response `200 OK` — returns an `OrderDetailResource`:

```json
{
  "id": "01k76xt7z87katy392s6w335cp",
  "reference": "#ORDER_1175",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "calendar_event_id": null,
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "source": "EXCEL",
  "due_at": null,
  "expires_at": null,
  "revenue_type": "PRODUCTS",
  "credit_code": "701",
  "debit_code": "4111",
  "shipping_fee_id": null,
  "pickup_location_id": null,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": { "...": "AddressResource" },
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "order_products": [ { "...": "OrderProduct pivot rows" } ],
  "customer": { "...": "CustomerResource" },
  "company": { "...": "CompanyResource" },
  "shop": { "...": "ShopResource" },
  "user": { "...": "UserResource" },
  "payments": [],
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "adjustments": [],
  "discounts": [],
  "taxes": [],
  "addresses": [ { "...": "AddressResource" } ],
  "custom_fields": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-09-18T00:00:01.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Missing `X-Company` header.
- `401` Unauthorized.
- `500` Internal error with `message` describing the failure.

---

### `POST` `/2023-11/admin/orders/export`
Enqueue an asynchronous export of the current order query. Accepts the same filter and `include` parameters as the list endpoint, plus:

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `format` | string | no | `xlsx` (default), `csv`, or `pdf` |
| `columns` | array | no | Column names to include (see `GET /export/fields`) |
| _(all list filters)_ | — | no | Any `filter[*]` query parameter is forwarded to scope the export |

Request example:

```http
POST /2023-11/admin/orders/export HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "format": "xlsx",
  "columns": ["reference", "customer", "amount", "status", "created_at"]
}
```

Success response — two possible shapes depending on the number of matching orders:

**Sync (< 500 orders): `200 OK`** — binary file download. `Content-Type` is `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` for XLSX, `text/csv` for CSV, or `application/pdf` for PDF. No JSON body.

**Async (≥ 500 orders): `202 Accepted`** — the export is queued; the user will receive an email with the download link.

```json
{
  "status": "queued",
  "message": "Your export is being generated. You will receive an email with the download link once it is ready."
}
```

Error responses:
- `401` User not authenticated.
- `422` Unsupported format: `{"message":"The given data was invalid.","errors":{"format":["Unsupported export format."]}}`
- `500` Export failure.

---

### `GET` `/2023-11/admin/orders/export/fields`
Return the list of column names available for order exports.

Request example:

```http
GET /2023-11/admin/orders/export/fields HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "columns": [
    "reference",
    "customer",
    "amount",
    "status",
    "source",
    "created_at",
    "..."
  ]
}
```

---

### `POST` `/2023-11/admin/orders/import`
Import orders from a spreadsheet file (single-request upload).

- **Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `import_file` | file | yes | `mimes:xlsx,xls,csv` | The spreadsheet to import |

Request example:

```http
POST /2023-11/admin/orders/import HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: multipart/form-data

import_file=<file>
```

Success response `200 OK`:

```json
{
  "message": "Orders imported successfully"
}
```

Error responses:
- `422` Validation failure — file missing or wrong MIME type.
- `500` Import processing error.

---

### `POST` `/2023-11/admin/orders/import-chunk`
Upload a single chunk of a large import file (for chunked/resumable uploads). All chunks for a single upload share the same `uploadId`.

- **Request body (multipart/form-data):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `chunk` | file | yes | `required\|file` | The binary chunk |
| `chunkIndex` | integer | yes | `required\|integer` | Zero-based chunk index |
| `uploadId` | string | yes | `required\|string` | Unique ID shared by all chunks of one upload |

Success response `200 OK`:

```json
{
  "success": true,
  "message": "Chunk 0 uploaded successfully",
  "chunkPath": "imports/tmp/abc123/chunk_0"
}
```

Error responses:
- `400` File not found or file is empty.
- `422` Validation failure.

---

### `POST` `/2023-11/admin/orders/merge-chunk`
Merge previously uploaded chunks into a single file and run the import.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `uploadId` | string | yes | `required\|string` | Same ID used during chunk uploads |
| `totalChunks` | integer | yes | `required\|integer` | Total number of chunks to merge |
| `fileName` | string | yes | `required\|string` | Original file name (used to name the merged file) |

Success response `200 OK`:

```json
{
  "success": true,
  "message": "Import completed successfully",
  "filePath": "imports/abc123_orders.xlsx",
  "fileSize": 102400
}
```

Error responses:
- `422` Validation failure.
- `500` Merge or import error with `message`.

---

### `GET` `/2023-11/admin/orders/overview`
Return order KPI metrics for the given date range — used to populate the orders dashboard overview.

- **Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `start_date` | date | no | Range start (ISO 8601 or `Y-m-d`) |
| `end_date` | date | no | Range end (ISO 8601 or `Y-m-d`) |

Request example:

```http
GET /2023-11/admin/orders/overview?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "orders_count": 148,
  "revenue": 2450000.00,
  "unpaid": 12,
  "late": 3,
  "average_order_value": 16554.05
}
```

Error responses:
- `422` Invalid date format.

---

### `PUT` `/2023-11/admin/orders/status/bulk-update`
Update the shipping and/or billing status for multiple orders in one call. For billing status transitions to `paid`, the unpaid remainder is resolved automatically.

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `order_ids` | array of strings | yes | ULIDs of the orders to update |
| `shipping` | object | no | Set `shipping.status` (e.g. `pending`, `taken_in_charge`, `delivered`) |
| `billing` | object | no | Set `billing.status` (e.g. `paid`, `pending`) |
| `metadata` | object | no | Key-value pairs to merge into each order's metadata |

Request example:

```http
PUT /2023-11/admin/orders/status/bulk-update HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "order_ids": [
    "01k76xt7z87katy392s6w335cp",
    "01k76xt7z87katy392s6w335cq"
  ],
  "shipping": {
    "status": "delivered"
  }
}
```

Success response `200 OK`:

```json
{
  "message": "Orders status updated"
}
```

Error responses:
- `400` Ghost order detected (`Cannot update a ghost order`).
- `500` Business exception during processing.

---

### `GET` `/2023-11/admin/orders/{orderId}`
Retrieve full details of a single order, including all relationships.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

Request example:

```http
GET /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "calendar_event_id": null,
  "reference": "#ORDER_1354",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "source": "WEB",
  "due_at": null,
  "expires_at": null,
  "revenue_type": "PRODUCTS",
  "credit_code": "701",
  "debit_code": "4111",
  "shipping_fee_id": null,
  "pickup_location_id": null,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": { "...": "AddressResource" },
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "order_products": [ { "...": "OrderProduct pivot rows" } ],
  "customer": { "...": "CustomerResource" },
  "company": { "...": "CompanyResource" },
  "shop": { "...": "ShopResource" },
  "user": { "...": "UserResource" },
  "payments": [ { "...": "Payment models (inbound)" } ],
  "delivery": { "...": "DeliveryResource (first delivery)" },
  "deliveries": [ { "...": "DeliveryResource" } ],
  "invoices": [ { "...": "Invoice models" } ],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "adjustments": [],
  "discounts": [],
  "taxes": [],
  "addresses": [ { "...": "AddressResource" } ],
  "custom_fields": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:24:05.000000Z",
  "deleted_at": null
}
```

Error responses:
- `404` Order not found: `{"status":"error","code":404,"data":null,"message":"The order does not exist"}`
- `401` Unauthorized.

---

### `PUT` `/2023-11/admin/orders/{orderId}`
Update an existing order. Runs the same `UpsertOrderProcess` as creation. Cannot remove products or decrease quantities if a final paid invoice exists.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order to update |

- **Request body:** All fields are optional (partial update). Pass `"type": "simple"` with `"metadata"` to perform a lightweight metadata-only patch without full order reprocessing.

| Field | Type | Required | Description |
|---|---|---|---|
| `type` | string | no | Set to `"simple"` for a metadata-only patch (skips full reprocessing) |
| `source` | string | no | Order source: `EXCEL`, `WHATSAPP`, `WEB`, `POS`, etc. |
| `currency` | string | no | ISO 4217 currency code (e.g. `XAF`) |
| `state` | integer | no | Order state: `1` = invoice, `2` = quotation, `3` = sales order |
| `revenue_type` | string | no | Revenue type (e.g. `PRODUCTS`, `SERVICES`) |
| `due_at` | date | no | Payment due date |
| `created_at` | ISO 8601 | no | Override the creation timestamp |
| `user_id` | string (ULID) | no | Assign the order to a specific staff user (requires `CHANGE_ORDER_USER` permission) |
| `credit_code` | string | no | Accounting credit code (e.g. `701`) |
| `debit_code` | string | no | Accounting debit code (e.g. `4111`) |
| `metadata` | object | no | Arbitrary metadata merged with existing order metadata |
| `customer` | object | no | Customer information (upsert) |
| `customer.id` | string (ULID) | no | Existing customer ULID — updates if found |
| `customer.first_name` | string | no | |
| `customer.last_name` | string | no | |
| `customer.email` | string | no | |
| `customer.phone` | string | no | |
| `customer.city` | string | no | |
| `customer.line1` | string | no | Default address line 1 |
| `customer.force` | boolean | no | Force-create even if a duplicate is detected |
| `products` | array | no | Replacement product line list; replaces all existing lines |
| `products[].product_id` | string (ULID) | no | Link to an existing product |
| `products[].variant_id` | string (ULID) | no | Link to a specific variant |
| `products[].order_product_id` | integer | no | ID of the existing order product line to update in place |
| `products[].title` | string | no | Product name (used when creating an ad-hoc product) |
| `products[].price` | number | no | Unit price (decimal) |
| `products[].quantity` | integer | no | Ordered quantity |
| `products[].cost` | number | no | Cost price |
| `products[].service_id` | string (ULID) | no | Link to a service |
| `products[].status` | string | no | Line status (company-defined) |
| `products[].account_code` | string | no | Per-line accounting code override |
| `products[].discounts` | array | no | Per-line discount objects (`discount_id`, `discount_type`, `discount_value`) |
| `products[].metadata` | object | no | Per-line metadata (supports `preferred_warehouse_id`, `taxes`, etc.) |
| `discounts` | array | no | Order-level discounts (`discount_id`, `code`) |
| `shipping` | object | no | Shipping information |
| `shipping.mode` | string | no | `delivery` or `pickup` |
| `shipping.address_id` | string (ULID) | no | Existing address ID |
| `shipping.address` | object | no | Inline address (`line1`, `city`, `country` required if provided) |
| `shipping.scheduled_at` | date | no | Scheduled delivery date (`Y-m-d`) |
| `shipping.status` | string | no | Shipping status |
| `shipping.amount` | number | no | Shipping cost |
| `shipping_fee_id` | string (ULID) | no | Shipping fee rule ID |
| `pickup_location_id` | string (ULID) | no | Pickup location ID |
| `billing` | object | no | Billing information |
| `billing.method` | string | no | Payment method label (e.g. `cash`, `notchpay`, `pawapay`) |
| `billing.amount` | number | no | Amount paid or to be paid |
| `billing.scheduled_at` | string | no | Billing scheduled date |
| `billing.address` | object | no | Inline billing address |
| `billing.treasury_account_id` | string (ULID) | no | Treasury account to record payment in |
| `billing.status` | string | no | Billing status |
| `shop_id` | string (ULID) | no | Target shop |
| `force` | boolean | no | Set to `true` to bypass stock availability checks |
| `warehouse_ids` | array | no | Preferred warehouse IDs for stock deduction |

Request example:

```http
PUT /2023-11/admin/orders/01k76xt7z87katy392s6w335cp HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "currency": "XAF",
  "products": [
    {
      "title": "Sac bleu",
      "price": 15000,
      "quantity": 2
    }
  ],
  "billing": {
    "method": "pawapay"
  }
}
```

Success response `200 OK` — returns full `OrderDetailResource`:

```json
{
  "id": "01k76xt7z87katy392s6w335cp",
  "reference": "#ORDER_1174",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "calendar_event_id": null,
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 30000.00,
  "amount_due": 30000.00,
  "source": "EXCEL",
  "due_at": null,
  "expires_at": null,
  "revenue_type": "PRODUCTS",
  "credit_code": "701",
  "debit_code": "4111",
  "shipping_fee_id": null,
  "pickup_location_id": null,
  "billing": {
    "total": 30000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 30000.00,
    "net_to_pay": 30000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 30000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": { "...": "AddressResource" },
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "order_products": [ { "...": "OrderProduct pivot rows" } ],
  "customer": { "...": "CustomerResource" },
  "company": { "...": "CompanyResource" },
  "shop": { "...": "ShopResource" },
  "user": { "...": "UserResource" },
  "payments": [],
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "adjustments": [],
  "discounts": [],
  "taxes": [],
  "addresses": [ { "...": "AddressResource" } ],
  "custom_fields": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:30:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Cannot update an invoiced order that has a final paid invoice if products are being removed or quantities decreased.
- `404` Order not found.

---

### `DELETE` `/2023-11/admin/orders/{orderId}`
Cancel (soft-delete) an order. Orders with invoices or payments are transitioned to `CANCELLED` state; their invoices are undone. Pass `force=true` to permanently delete an order with no invoices, payments, or returns.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order to delete |

- **Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `force` | boolean | no | Permanently delete (hard-delete) the order if no related documents exist |

Request example:

```http
DELETE /2023-11/admin/orders/01k76xt7z87katy392s6w335cp HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — soft-delete returns `OrderDetailResource` with `deleted_at` populated:

```json
{
  "id": "01k76xt7z87katy392s6w335cp",
  "reference": "#ORDER_1174",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "customer": { "...": "CustomerResource" },
  "payments": [],
  "invoices_count": 0,
  "returns_count": 0,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T12:20:00.000000Z",
  "deleted_at": "2025-10-10T12:20:00.000000Z"
}
```

For `?force=true` (permanent delete with no related documents), returns:

```json
{
  "message": "Order deleted"
}
```

Error responses:
- `404` Order not found.
- `500` Processing error.

---

### `GET` `/2023-11/admin/orders/{orderId}/activities`
List the activity log for an order (paginated).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | no | 6 | Activities per page |

Request example:

```http
GET /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/activities HTTP/1.1
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
      "id": 1,
      "log_name": "default",
      "description": "Order status changed",
      "subject_id": "01k9ey2e5xpnq89y8m8w10senj",
      "subject_type": "App\\Models\\Order",
      "causer_id": "01k1amyqgjje8sknkzpm78qv6x",
      "causer_type": "App\\Models\\User",
      "properties": { "old": "pending", "new": "paid" },
      "username": "John Doe",
      "user": { "...": "User model" },
      "created_at": "2025-10-10T11:24:05.000000Z",
      "updated_at": "2025-10-10T11:24:05.000000Z"
    }
  ],
  "pagination": { "..." : "..." }
}
```

Error responses:
- `404` Order not found.

---

### `GET` `/2023-11/admin/orders/{orderId}/products`
Return all order product lines for an order, including their pivot metadata and linked product records.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

Request example:

```http
GET /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/products HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — raw array of `OrderProduct` models with eager-loaded `product`:

```json
[
  {
    "id": 4839,
    "order_id": "01k9ey2e5xpnq89y8m8w10senj",
    "product_id": "01k76xt7vxsnae0vz36ddgt4bz",
    "variant_id": "01k76xt7x2zgs1gfnt0fwcmrsy",
    "service_id": null,
    "price": 15000.00,
    "quantity": 1,
    "status": null,
    "metadata": {
      "item_title": "Sac bleu",
      "variant_title": "Default Title",
      "product_title": "Sac bleu"
    },
    "created_at": "2025-10-10T11:24:04.000000Z",
    "updated_at": "2025-10-10T11:24:04.000000Z",
    "product": { "...": "Product model" }
  }
]
```

Error responses:
- `404` Order not found.
- `500` Internal error.

---

### `PATCH` `/2023-11/admin/orders/{orderId}/products/{orderProductId}/status`
Update the fulfillment status of a single order product line.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |
| `orderProductId` | integer | The `order_products` table row ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `status` | string | no | `nullable\|string\|max:255` | New status value (e.g. `picked`, `packed`, `shipped`) |

Request example:

```http
PATCH /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/products/4839/status HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "status": "packed"
}
```

Success response `200 OK`:

```json
{
  "status": "success",
  "data": {
    "id": 4839,
    "order_id": "01k9ey2e5xpnq89y8m8w10senj",
    "product_id": "01k76xt7vxsnae0vz36ddgt4bz",
    "variant_id": "01k76xt7x2zgs1gfnt0fwcmrsy",
    "price": 15000.00,
    "quantity": 1,
    "status": "packed",
    "metadata": { "...": "..." },
    "created_at": "2025-10-10T11:24:04.000000Z",
    "updated_at": "2025-10-10T11:30:00.000000Z"
  }
}
```

Error responses:
- `404` Order or order product not found.
- `500` Internal error.

---

### `POST` `/2023-11/admin/orders/{orderId}/restore`
Restore a cancelled order by duplicating it as a new active order with a fresh reference. Billing/shipping statuses are reset to `pending`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The cancelled order ID |

Request example:

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/orders/01k76xt7z87katy392s6w335cp/restore HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — returns `OrderDetailResource` of the newly created order:

```json
{
  "id": "01k9fz3r1bpnq89y8m8w20tenk",
  "reference": "#ORDER_1355",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "calendar_event_id": null,
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "source": "EXCEL",
  "due_at": null,
  "expires_at": null,
  "revenue_type": "PRODUCTS",
  "credit_code": "701",
  "debit_code": "4111",
  "shipping_fee_id": null,
  "pickup_location_id": null,
  "billing": {
    "total": 15000.00,
    "amount": 0,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": null,
    "treasury_account_label": null,
    "treasury_account": null,
    "address": null,
    "scheduled_at": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": null
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "order_products": [ { "...": "OrderProduct pivot rows" } ],
  "customer": { "...": "CustomerResource" },
  "company": { "...": "CompanyResource" },
  "shop": { "...": "ShopResource" },
  "user": { "...": "UserResource" },
  "payments": [],
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "adjustments": [],
  "discounts": [],
  "taxes": [],
  "addresses": [ { "...": "AddressResource" } ],
  "custom_fields": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-10-11T09:00:00.000000Z",
  "updated_at": "2025-10-11T09:00:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Order is not cancelled.
- `404` Order not found.
- `500` Internal error.

---

### `PUT` `/2023-11/admin/orders/{orderId}/state`
Transition an order to a different document state. Valid states: `1` (Invoice), `2` (Quotation), `3` (Sales Order). Converting a Sales Order to an Invoice creates a new linked invoice-state order. Converting to Invoice also deducts stock if stock tracking is enabled.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `state` | integer | yes | Target state value (must be a valid `OrderState` integer) |

Request example:

```http
PUT /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/state HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "state": 1
}
```

Success response `200 OK` — returns `OrderDetailResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "calendar_event_id": null,
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "source": "WEB",
  "due_at": null,
  "expires_at": null,
  "revenue_type": "PRODUCTS",
  "credit_code": "701",
  "debit_code": "4111",
  "shipping_fee_id": null,
  "pickup_location_id": null,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": { "...": "AddressResource" },
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "order_products": [ { "...": "OrderProduct pivot rows" } ],
  "customer": { "...": "CustomerResource" },
  "company": { "...": "CompanyResource" },
  "shop": { "...": "ShopResource" },
  "user": { "...": "UserResource" },
  "payments": [],
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "adjustments": [],
  "discounts": [],
  "taxes": [],
  "addresses": [ { "...": "AddressResource" } ],
  "custom_fields": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:25:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Invalid state value, or insufficient stock when transitioning to Invoice state (includes descriptive message about which product/warehouse).
- `404` Order not found.

---

### `POST` `/2023-11/admin/orders/{order}/notify`
Send an order confirmation notification to the customer (email) and to all shop users who have the `VIEW_ORDERS` permission.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |

- **Query parameters / request body (optional):**

| Field | Type | Description |
|---|---|---|
| `lang` / `locale` | string | Notification locale (`en`, `fr`). Defaults to `Accept-Language` header. |

Request example:

```http
POST /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/notify HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "message": "Notification sent"
}
```

Error responses:
- `500` Notification delivery error with `message`.

---

### `POST` `/2023-11/admin/orders/{order}/payments`
Record a payment against an order. Runs `PaidOrderProcess` which updates the billing status, creates accounting book entries, and optionally generates a credit note.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `amount` | number | yes | Payment amount |
| `method` | string | yes | Payment method label (e.g. `bank`, `cash`, `notchpay`) |
| `treasury_account_id` | string (ULID) | yes | Treasury account to record the payment in |
| `date` | date | no | Payment date (`Y-m-d`); defaults to today |
| `invoice_id` | string (ULID) | no | If provided, returns the updated invoice instead of a status message |

Request example:

```http
POST /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/payments HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "amount": 15000,
  "method": "bank",
  "treasury_account_id": "01k1amyq08htzkeg79xd7f9pct"
}
```

Success response `200 OK`:

```json
{
  "status": "success",
  "code": 200,
  "message": "Payment added successfully"
}
```

Error responses:
- `500` Payment processing error with `message`.

---

### `PUT` `/2023-11/admin/orders/{order}/payments/{payment}`
Update an existing payment on an order (e.g. correct amount, method, or date). Runs `UpdatePaymentProcess` and logs an activity entry.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |
| `payment` | string (ULID) | The payment ID (supports soft-deleted) |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `amount` | number | no | `numeric\|min:0.01` | New payment amount |
| `method` | string | no | `string` | Payment method label (e.g. `cash`, `bank`, `pawapay`) |
| `date` | date | no | `date` | Payment date (`Y-m-d`); updates `paid_at` and `due_date` |
| `treasury_account_id` | string (ULID) | no | `exists:treasury_accounts,id` | New treasury account for the payment |
| `status` | string | no | `string` | Payment status override |

Request example:

```http
PUT /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/payments/01k1amyq08htzkeg79xd7f9pcd HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "amount": 12000,
  "method": "cash"
}
```

Success response `200 OK` — returns `OrderDetailResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 3000.00,
  "source": "WEB",
  "billing": {
    "total": 15000.00,
    "amount": 12000.00,
    "method": "cash",
    "status": "partially_paid",
    "subtotal": 15000.00,
    "net_to_pay": 3000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
    "treasury_account_label": "Cash",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "customer": { "...": "CustomerResource" },
  "payments": [
    {
      "id": "01k1amyq08htzkeg79xd7f9pcd",
      "amount": 12000.00,
      "method": "cash",
      "type": "inbound",
      "status": "paid",
      "paid_at": "2025-10-10",
      "due_date": "2025-10-10",
      "treasury_account_id": "01k1amypn6xycwe7w948jp82zm",
      "created_at": "2025-10-10T11:24:05.000000Z",
      "updated_at": "2025-10-10T11:35:00.000000Z",
      "deleted_at": null
    }
  ],
  "invoices_count": 0,
  "returns_count": 0,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:35:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Processing error with `message`.

---

### `DELETE` `/2023-11/admin/orders/{order}/payments/{payment}`
Soft-delete a payment from an order. Runs `DeletePaymentProcess` which reverses billing status changes and logs an activity entry.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |
| `payment` | string (ULID) | The payment ID (supports soft-deleted) |

Request example:

```http
DELETE /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/payments/01k1amyq08htzkeg79xd7f9pcd HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — returns `OrderDetailResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "billing": {
    "total": 15000.00,
    "amount": 0,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "customer": { "...": "CustomerResource" },
  "payments": [],
  "invoices_count": 0,
  "returns_count": 0,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:40:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Processing error with `message`.

---

### `POST` `/2023-11/admin/orders/{order}/payments/{payment}/restore`
Restore a previously soft-deleted payment. Runs `RestorePaymentProcess` and logs an activity entry.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |
| `payment` | string (ULID) | The soft-deleted payment ID |

Request example:

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/payments/01k1amyq08htzkeg79xd7f9pcd/restore HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — returns `OrderDetailResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 0.00,
  "billing": {
    "total": 15000.00,
    "amount": 15000.00,
    "method": "pawapay",
    "status": "paid",
    "subtotal": 15000.00,
    "net_to_pay": 0.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "customer": { "...": "CustomerResource" },
  "payments": [
    {
      "id": "01k1amyq08htzkeg79xd7f9pcd",
      "amount": 15000.00,
      "method": "pawapay",
      "type": "inbound",
      "status": "paid",
      "paid_at": "2025-10-10",
      "due_date": "2025-10-10",
      "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
      "created_at": "2025-10-10T11:24:05.000000Z",
      "updated_at": "2025-10-10T11:45:00.000000Z",
      "deleted_at": null
    }
  ],
  "invoices_count": 0,
  "returns_count": 0,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:45:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Processing error with `message`.

---

### `PUT` `/2023-11/admin/orders/{order}/status`
Update shipping and/or billing status for a single order. Billing status changes to `pending` automatically create a credit note reversing existing inbound payments.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `shipping` | object | no | `{ "status": "<DeliveryStatus>" }` |
| `billing` | object | no | `{ "status": "<BillingStatus>", ... }` |
| `metadata` | object | no | Key-value metadata to merge into the order |

Allowed shipping statuses: `pending`, `taken_in_charge`, `delivered`, `partial_delivery`.
Allowed billing statuses: `pending`, `paid`, `partially_paid`, `unpaid`, `canceled`.

Request example:

```http
PUT /2023-11/admin/orders/01k9ey2e5xpnq89y8m8w10senj/status HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "shipping": {
    "status": "delivered"
  }
}
```

Success response `200 OK` — returns `OrderDetailResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": null
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "delivered",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "customer": { "...": "CustomerResource" },
  "payments": [],
  "invoices_count": 0,
  "returns_count": 0,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:50:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Invalid shipping status or business rule violation.
- `500` `BusinessException` with message.

---

## Order Subscriptions

### `GET` `/2023-11/admin/order_subscriptions`
List order-to-subscription link records (paginated).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | no | 15 | Results per page |
| `sort` | string | no | — | `created_at`, `updated_at` (prefix `-` for descending) |
| `filter[subscription_id]` | string (ULID) | no | — | Exact match |
| `filter[order_id]` | string (ULID) | no | — | Exact match |

Request example:

```http
GET /2023-11/admin/order_subscriptions?filter[order_id]=01k9ey2e5xpnq89y8m8w10senj HTTP/1.1
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
      "id": 12,
      "subscription_id": "01k1amypzhgs7fngfx1r9d37pn",
      "order_id": "01k9ey2e5xpnq89y8m8w10senj",
      "status": "active",
      "subscription": { "...": "Subscription model" },
      "order": { "...": "Order model" },
      "created_at": "2025-09-18T00:00:00.000000Z",
      "updated_at": "2025-09-18T00:00:00.000000Z"
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

---

## Invoices

### `GET` `/2023-11/admin/invoices`
List invoices with filtering and sorting (paginated).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | no | 15 | Results per page |
| `include` | string | no | — | Comma-separated: `company`, `payments`, `creditNotes`, `order`, `order.company`, `order.shop`, `order.customer`, `order.customer.address`, `order.products` |
| `sort` | string | no | — | `status`, `is_final`, `is_deposit`, `created_at`, `updated_at`, `shop.name` |
| `filter[reference]` | string | no | — | Partial match on invoice reference |
| `filter[order_id]` | string (ULID) | no | — | Exact match on linked order ID |
| `filter[is_final]` | boolean | no | — | Filter by final invoice flag |
| `filter[is_deposit]` | boolean | no | — | Filter by deposit invoice flag |
| `filter[status]` | string | no | — | Partial match: `pending`, `paid`, `partially_paid`, `cancelled` |
| `filter[shop_id]` | string (ULID) | no | — | Invoices linked to orders from this shop |
| `filter[search]` | string | no | — | Full-text search on reference, customer name, phone, email, order reference, amount |
| `filter[updated_at][start]` | date | no | — | Lower bound on last update date |
| `filter[updated_at][end]` | date | no | — | Upper bound on last update date |
| `all` | boolean | no | — | Include soft-deleted (trashed) invoices |

Request example:

```http
GET /2023-11/admin/invoices?filter[status]=pending&include=order.customer&sort=-created_at HTTP/1.1
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
      "id": "01k9ey2e5xpnq89y8m8w10sen1",
      "reference": "FAC-0042",
      "order_id": "01k9ey2e5xpnq89y8m8w10senj",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "status": "pending",
      "is_final": false,
      "is_deposit": false,
      "amount": 15000.00,
      "currency_code": "XAF",
      "metadata": null,
      "order": { "...": "Order model" },
      "payments": [],
      "lines": [],
      "created_at": "2025-10-10T11:24:05.000000Z",
      "updated_at": "2025-10-10T11:24:05.000000Z",
      "deleted_at": null
    }
  ],
  "pagination": { "...": "..." }
}
```

---

### `POST` `/2023-11/admin/invoices`
Create an invoice from an existing order or from a set of delivery IDs. The order must have at least one product, and the deliveries must contain at least one item.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | string (ULID) | cond. | Required if `delivery_ids` absent | Generate invoice from this order |
| `delivery_ids` | array | cond. | Required if `order_id` absent | Generate invoice from these delivery IDs |

Request example:

```http
POST /2023-11/admin/invoices HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "order_id": "01k9ey2e5xpnq89y8m8w10senj"
}
```

Success response `200 OK` — returns full `InvoiceResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10sen1",
  "reference": "FAC-0043",
  "order_id": "01k9ey2e5xpnq89y8m8w10senj",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "pending",
  "is_final": false,
  "is_deposit": false,
  "amount": 15000.00,
  "currency_code": "XAF",
  "metadata": null,
  "order": {
    "id": "01k9ey2e5xpnq89y8m8w10senj",
    "shop": { "...": "ShopResource" },
    "customer": { "...": "CustomerResource" }
  },
  "payments": [],
  "lines": [
    {
      "id": 2,
      "invoice_id": "01k9ey2e5xpnq89y8m8w10sen1",
      "order_product_id": 4839,
      "description": "Sac bleu",
      "quantity": 1,
      "unit_price": 15000.00,
      "total": 15000.00,
      "tax_id": null,
      "discount_id": null,
      "tax": null,
      "discount": null,
      "order_product": { "...": "OrderProduct model" }
    }
  ],
  "created_at": "2025-10-10T12:00:00.000000Z",
  "updated_at": "2025-10-10T12:00:00.000000Z",
  "deleted_at": null
}
```

Error responses:
- `400` Neither `order_id` nor `delivery_ids` provided.
- `404` Order not found.
- `422` Order has no products, or deliveries contain no items.
- `500` Internal error.

---

### `GET` `/2023-11/admin/invoices/{invoice}`
Retrieve a single invoice with its company, order, order shop/customer, lines, and credit notes.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `invoice` | string (ULID) | The invoice ID |

Request example:

```http
GET /2023-11/admin/invoices/01k9ey2e5xpnq89y8m8w10sen1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10sen1",
  "reference": "FAC-0042",
  "order_id": "01k9ey2e5xpnq89y8m8w10senj",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "pending",
  "is_final": false,
  "is_deposit": false,
  "amount": 15000.00,
  "currency_code": "XAF",
  "metadata": null,
  "order": {
    "id": "01k9ey2e5xpnq89y8m8w10senj",
    "shop": { "...": "ShopResource" },
    "customer": { "...": "CustomerResource" }
  },
  "payments": [],
  "lines": [
    {
      "id": 1,
      "invoice_id": "01k9ey2e5xpnq89y8m8w10sen1",
      "order_product_id": 4839,
      "description": "Sac bleu",
      "quantity": 1,
      "unit_price": 15000.00,
      "total": 15000.00,
      "tax_id": null,
      "discount_id": null,
      "tax": null,
      "discount": null,
      "order_product": { "...": "OrderProduct model" }
    }
  ],
  "created_at": "2025-10-10T11:24:05.000000Z",
  "updated_at": "2025-10-10T11:24:05.000000Z",
  "deleted_at": null
}
```

Error responses:
- `404` Invoice not found.

---

### `PUT` `/2023-11/admin/invoices/{invoice}`
Update invoice status.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `invoice` | string (ULID) | The invoice ID |

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `status` | string | no | New status: `pending`, `paid`, `partially_paid`, `cancelled` |

Request example:

```http
PUT /2023-11/admin/invoices/01k9ey2e5xpnq89y8m8w10sen1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "status": "paid"
}
```

Success response `200 OK` — returns full `InvoiceResource`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10sen1",
  "reference": "FAC-0042",
  "order_id": "01k9ey2e5xpnq89y8m8w10senj",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "status": "paid",
  "is_final": true,
  "is_deposit": false,
  "amount": 15000.00,
  "currency_code": "XAF",
  "metadata": null,
  "order": {
    "id": "01k9ey2e5xpnq89y8m8w10senj",
    "shop": { "...": "ShopResource" },
    "customer": { "...": "CustomerResource" }
  },
  "payments": [
    {
      "id": "01k1amyq08htzkeg79xd7f9pcd",
      "amount": 15000.00,
      "method": "bank",
      "type": "inbound",
      "status": "paid",
      "paid_at": "2025-10-10",
      "due_date": "2025-10-10",
      "treasury_account_id": "01k1amyq08htzkeg79xd7f9pct",
      "created_at": "2025-10-10T12:00:00.000000Z",
      "updated_at": "2025-10-10T12:00:00.000000Z",
      "deleted_at": null
    }
  ],
  "lines": [
    {
      "id": 1,
      "invoice_id": "01k9ey2e5xpnq89y8m8w10sen1",
      "order_product_id": 4839,
      "description": "Sac bleu",
      "quantity": 1,
      "unit_price": 15000.00,
      "total": 15000.00,
      "tax_id": null,
      "discount_id": null,
      "tax": null,
      "discount": null,
      "order_product": { "...": "OrderProduct model" }
    }
  ],
  "created_at": "2025-10-10T11:24:05.000000Z",
  "updated_at": "2025-10-10T12:05:00.000000Z",
  "deleted_at": null
}
```

---

### `DELETE` `/2023-11/admin/invoices/{invoice}`
Delete a pending invoice (soft-delete). Only invoices with `status = pending` can be deleted.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `invoice` | string (ULID) | The invoice ID |

Request example:

```http
DELETE /2023-11/admin/invoices/01k9ey2e5xpnq89y8m8w10sen1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `204 No Content`.

Error responses:
- `403` Invoice is not in `pending` status: `{"message":"You can only delete pending invoices."}`

---

### `DELETE` `/2023-11/admin/invoices/{invoice}/payments/{payment}`
Remove a payment from an invoice. Runs `DeletePaymentProcess`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `invoice` | string (ULID) | The invoice ID |
| `payment` | string (ULID) | The payment ID |

Request example:

```http
DELETE /2023-11/admin/invoices/01k9ey2e5xpnq89y8m8w10sen1/payments/01k1amyq08htzkeg79xd7f9pcd HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `204 No Content`.

Error responses:
- `404` Invoice or payment not found.

---

## Invoice Rendering (Public)

These endpoints are publicly accessible (no `Authorization` header required) and return HTML views or PDF files rather than JSON.

### `GET` `/2023-11/invoices/{orderId}`
Render the order invoice as an HTML page. Supports `?receipt=1` to switch to a compact receipt view with a QR code.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

- **Query parameters:**

| Name | Type | Description |
|---|---|---|
| `receipt` | any | If present, render as a receipt view with QR code |
| `stock` | boolean | Show stock information on the invoice |
| `warehouse_name` | boolean | Show warehouse name |
| `batch_number` | boolean | Show batch number |
| `expiration_date` | boolean | Show expiration date |
| `product_images` | boolean | Show product images |
| `description` | boolean | Show product description |

Success response `200 OK` — HTML content (`text/html`).

---

### `GET` `/2023-11/invoices/{orderId}/invoice`
Render the order invoice as a downloadable PDF (A4 format). Supports `?receipt=1` for a receipt HTML view instead.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

Same query parameters as the HTML view above.

Success response `200 OK` — PDF binary (`application/pdf`) or HTML if `?receipt` is present.

---

### `GET` `/2023-11/invoice/{orderId}`
Public alias for `GET /admin/orders/{orderId}` — returns the same `OrderDetailResource` JSON. Used by external pages (e.g. customer-facing invoice portal).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `orderId` | string (ULID) | The order ID |

Request example:

```http
GET /2023-11/invoice/01k9ey2e5xpnq89y8m8w10senj HTTP/1.1
Host: api.genuka.com
Accept: application/json
```

Success response `200 OK` — same shape as `GET /2023-11/admin/orders/{orderId}`:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "source": "WEB",
  "due_at": null,
  "expires_at": null,
  "revenue_type": "PRODUCTS",
  "credit_code": "701",
  "debit_code": "4111",
  "shipping_fee_id": null,
  "pickup_location_id": null,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "order_products": [ { "...": "OrderProduct pivot rows" } ],
  "customer": { "...": "CustomerResource" },
  "company": { "...": "CompanyResource" },
  "shop": { "...": "ShopResource" },
  "user": { "...": "UserResource" },
  "payments": [],
  "delivery": null,
  "deliveries": [],
  "invoices": [],
  "invoices_count": 0,
  "returns": [],
  "returns_count": 0,
  "credit_notes": [],
  "adjustments": [],
  "discounts": [],
  "taxes": [],
  "addresses": [ { "...": "AddressResource" } ],
  "custom_fields": [],
  "shipping_fee": null,
  "pickup_location": null,
  "medias": [],
  "private_medias": [],
  "signature": null,
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T11:24:05.000000Z",
  "deleted_at": null
}
```

Error responses:
- `404` Order not found.

---

### `PUT` `/2023-11/invoice/{order}/signature`
Attach a base64-encoded signature image to an order. The signature is stored in the `signature` Spatie media collection and metadata is recorded on the order.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `order` | string (ULID) | The order ID |

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `signature.base64` | string | yes | Base64-encoded image of the signature |
| `signature.information` | any | no | Additional metadata (signer name, date, etc.) |

Request example:

```http
PUT /2023-11/invoice/01k9ey2e5xpnq89y8m8w10senj/signature HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json
Accept: application/json

{
  "signature": {
    "base64": "data:image/png;base64,iVBORw0KGgo...",
    "information": {
      "signer": "John Doe",
      "signed_at": "2025-10-10"
    }
  }
}
```

Success response `200 OK` — returns full `OrderDetailResource` with `signature` populated:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senj",
  "reference": "#ORDER_1354",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01k1amypzhgs7fngfx1r9d37pn",
  "user_id": "01k1amyqgjje8sknkzpm78qv6x",
  "customer_id": "01k76xt7v9aq02r798c0c9nqhf",
  "status": "pending",
  "state": 1,
  "currency": "XAF",
  "amount": 15000.00,
  "amount_due": 15000.00,
  "billing": {
    "total": 15000.00,
    "amount": 14000.00,
    "method": "pawapay",
    "status": "pending",
    "subtotal": 15000.00,
    "net_to_pay": 15000.00,
    "discount": 0,
    "shipping": 0,
    "out_of_tax_total": 15000.00,
    "sum_positive_taxes": 0,
    "sum_negative_taxes": 0,
    "line_discounts_total": 0,
    "order_discounts_total": 0,
    "treasury_account_id": "01k1d8vxr0xdthhtb6zy1e97dt",
    "treasury_account_label": "PawaPay",
    "treasury_account": { "...": "TreasuryAccountResource" },
    "address": null,
    "scheduled_at": "18/01/2024"
  },
  "shipping": {
    "mode": "delivery",
    "amount": 0,
    "status": "pending",
    "address_id": "01k76xt7zgpvv1pfe177sqcsr4",
    "address": { "...": "AddressResource" },
    "scheduled_at": "2024-01-18"
  },
  "metadata": {},
  "products": [ { "...": "OrderProductResource" } ],
  "customer": { "...": "CustomerResource" },
  "payments": [],
  "invoices_count": 0,
  "returns_count": 0,
  "medias": [],
  "private_medias": [
    {
      "id": "01k9ez1r2bpnq89y8m8w10sign",
      "collection_name": "signature",
      "file_name": "signature.png",
      "mime_type": "image/png",
      "size": 48200,
      "url": "https://cdn.genuka.com/signatures/signature.png"
    }
  ],
  "signature": {
    "base64": null,
    "information": {
      "signer": "John Doe",
      "signed_at": "2025-10-10"
    },
    "media": { "...": "PrivateMediaResource" }
  },
  "bills": [],
  "created_at": "2025-09-18T00:00:00.000000Z",
  "updated_at": "2025-10-10T12:10:00.000000Z",
  "deleted_at": null
}
```

---

## Invoice Generator (Public)

A lightweight stateless invoice generator that accepts arbitrary data, caches it for 30 minutes behind a token, and renders a printable HTML page.

### `POST` `/2023-11/invoice-generator/prepare`
Store invoice data in cache and return a short-lived token and URL.

- **Request body:** Any JSON object. Recognized top-level keys:

| Field | Type | Description |
|---|---|---|
| `company` | object | Company info (name, address, currency, logo…) |
| `client` | object | Client/customer info |
| `items` | array | Line items: `{ title, quantity, price }` |
| `invoiceNumber` | string | Invoice reference (default: `FAC-000`) |
| `invoiceDate` | string | Invoice date (default: today `d/m/Y`) |
| `shipping` | number | Shipping amount |
| `discount` | number | Discount amount |
| `taxes` | array | Tax lines: `{ label, amount }` |
| `note` | string | Private note |
| `publicNote` | string | Public note shown on invoice |

Request example:

```http
POST /2023-11/invoice-generator/prepare HTTP/1.1
Host: api.genuka.com
Content-Type: application/json
Accept: application/json

{
  "company": { "name": "Acme Corp", "currency": "XAF" },
  "client": { "name": "John Doe" },
  "invoiceNumber": "FAC-0099",
  "invoiceDate": "09/06/2026",
  "items": [
    { "title": "Consulting", "quantity": 2, "price": 50000 }
  ],
  "taxes": [
    { "label": "TVA 19.25%", "amount": 19250 }
  ]
}
```

Success response `200 OK`:

```json
{
  "token": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "url": "https://api.genuka.com/2023-11/invoice-generator/a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "expires_in": 1800
}
```

---

### `GET` `/2023-11/invoice-generator/{token}`
Render the previously prepared invoice as a printable HTML page (auto-prints via JavaScript). The token expires after 30 minutes.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `token` | string | 32-character random token returned by `prepare` |

Success response `200 OK` — HTML content (`text/html`).

Error responses:
- `404` Token not found or expired.

---

## Credit Notes

### `GET` `/2023-11/admin/credit-notes`
List credit notes (paginated), with filtering and sorting.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `per_page` | integer | no | 15 | Results per page |
| `sort` | string | no | `-created_at` | `amount`, `reference`, `status`, `beneficiary.name`, `issued_at` |
| `filter[source_id]` | string | no | — | Exact match on source entity ID |
| `filter[source_type]` | string | no | — | Exact match on source type (e.g. `App\Models\Order`) |
| `filter[company_id]` | string (ULID) | no | — | Exact match |
| `filter[status]` | string | no | — | Exact match: `pending`, `refunded`, `cancelled` |
| `filter[reference]` | string | no | — | Partial match |
| `filter[order_id]` | string (ULID) | no | — | Shorthand: credit notes whose source is this order |
| `filter[search]` | string | no | — | Full-text search on reference, status, beneficiary name |

Request example:

```http
GET /2023-11/admin/credit-notes?filter[order_id]=01k9ey2e5xpnq89y8m8w10senj HTTP/1.1
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
      "id": "01k9ey2e5xpnq89y8m8w10senz",
      "reference": "CN-0001",
      "amount": 15000.00,
      "status": "pending",
      "reason": "Automatic payment reversal due to billing status change to pending",
      "issued_at": "2025-10-10T11:24:05.000000Z",
      "refunded_at": null,
      "cancelled_at": null,
      "metadata": {},
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "creditable_id": null,
      "creditable_type": null,
      "creditable": null,
      "beneficiary_id": "01k76xt7v9aq02r798c0c9nqhf",
      "beneficiary_type": "Customer",
      "beneficiary": { "...": "CustomerResource" },
      "source_id": "01k9ey2e5xpnq89y8m8w10senj",
      "source_type": "Order",
      "source": { "...": "OrderDetailResource" },
      "lines": [],
      "taxes": [],
      "book_entries": [],
      "payments": [],
      "refund_amount": 0.00,
      "created_at": "2025-10-10T11:24:05.000000Z",
      "updated_at": "2025-10-10T11:24:05.000000Z"
    }
  ],
  "pagination": { "...": "..." }
}
```

---

### `GET` `/2023-11/admin/credit-notes/{creditNote}`
Retrieve full details of a single credit note, including lines, taxes, book entries, and refund payments.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `creditNote` | string (ULID) | The credit note ID |

Request example:

```http
GET /2023-11/admin/credit-notes/01k9ey2e5xpnq89y8m8w10senz HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

Success response `200 OK` — same shape as the list item above, with `lines` fully populated:

```json
{
  "id": "01k9ey2e5xpnq89y8m8w10senz",
  "reference": "CN-0001",
  "amount": 15000.00,
  "status": "pending",
  "reason": "...",
  "issued_at": "2025-10-10T11:24:05.000000Z",
  "refunded_at": null,
  "cancelled_at": null,
  "metadata": {},
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "creditable_id": null,
  "creditable_type": null,
  "creditable": null,
  "beneficiary_id": "01k76xt7v9aq02r798c0c9nqhf",
  "beneficiary_type": "Customer",
  "beneficiary": { "...": "CustomerResource" },
  "source_id": "01k9ey2e5xpnq89y8m8w10senj",
  "source_type": "Order",
  "source": { "...": "OrderDetailResource" },
  "lines": [
    {
      "id": 1,
      "credit_note_id": "01k9ey2e5xpnq89y8m8w10senz",
      "description": "Sac bleu",
      "quantity": 1,
      "unit_price": 15000.00,
      "total": 15000.00,
      "variant_id": null,
      "variant": null,
      "tax_id": null,
      "tax": null,
      "discount_id": null,
      "discount": null
    }
  ],
  "taxes": [],
  "book_entries": [],
  "payments": [],
  "refund_amount": 0.00,
  "created_at": "2025-10-10T11:24:05.000000Z",
  "updated_at": "2025-10-10T11:24:05.000000Z"
}
```

Error responses:
- `404` Credit note not found.

---

## Checkout

### `GET` `/2023-11/checkout`
Render the Mamoni payment checkout HTML page for a given order. This is a public page loaded in a browser; it is not a JSON API endpoint.

- **Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `order_id` | string (ULID) | yes | The order to pay |
| `company_id` | string (ULID) | yes | The company that owns the order |
| `amount` | number | no | Override the amount to charge (defaults to `order.amount`) |
| `phone` | string | no | Pre-fill the payer's phone number |
| `success_url` | string | no | Redirect URL on successful payment |
| `cancel_url` | string | no | Redirect URL on cancelled payment |

Success response `200 OK` — HTML content (`text/html`), renders the Mamoni payment form.

Error responses:
- `404` Order not found or order does not belong to the specified company.
