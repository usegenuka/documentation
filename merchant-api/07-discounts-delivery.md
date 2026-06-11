---
title: "Discounts, Delivery & Fulfillment"
description: "This domain covers promotional discounts (coupon codes and automatic rules), order delivery tracking, product returns and their audit trail, pickup locations fo"
sidebarTitle: "Discounts & Delivery"
---

This domain covers promotional discounts (coupon codes and automatic rules), order delivery tracking, product returns and their audit trail, pickup locations for click-and-collect, and shipping fee configurations. Admin routes require authentication; public routes (`/2023-11/discounts`, `/2023-11/deliveries`, `/2023-11/delivery`, `/2023-11/shipping-fees`, `/2023-11/pickup-locations`) are available to storefront clients with only the `X-Company` header.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/discounts` | List discounts (admin) |
| POST | `/2023-11/admin/discounts` | Create a discount |
| GET | `/2023-11/admin/discounts/{discount}` | Get a discount |
| PUT | `/2023-11/admin/discounts/{discount}` | Update a discount |
| DELETE | `/2023-11/admin/discounts/{discount}` | Delete a discount |
| GET | `/2023-11/discounts` | Look up a discount by code (public) |
| POST | `/2023-11/discounts/automatic` | List applicable automatic discounts (public) |
| GET | `/2023-11/admin/deliveries` | List deliveries (admin) |
| POST | `/2023-11/admin/deliveries` | Create a delivery |
| POST | `/2023-11/admin/deliveries/update-position` | Push a GPS position update for a delivery |
| GET | `/2023-11/admin/deliveries/{delivery}` | Get a delivery (admin) |
| PUT | `/2023-11/admin/deliveries/{delivery}` | Update a delivery |
| DELETE | `/2023-11/admin/deliveries/{delivery}` | Delete a delivery |
| GET | `/2023-11/deliveries` | List deliveries (public) |
| GET | `/2023-11/deliveries/{delivery}` | Get a delivery (public) |
| GET | `/2023-11/deliveries/{delivery}/last_position` | Get the latest GPS position of a delivery (public) |
| GET | `/2023-11/delivery` | List deliveries — alternate public alias |
| GET | `/2023-11/delivery/{delivery}` | Get a delivery — alternate public alias |
| GET | `/2023-11/delivery/{delivery}/last_position` | Latest GPS position — alternate public alias |
| GET | `/2023-11/admin/returns` | List product returns |
| POST | `/2023-11/admin/returns` | Create a product return |
| GET | `/2023-11/admin/returns/audit/report` | Aggregate audit report for returns |
| GET | `/2023-11/admin/returns/audit/trail` | Full chronological audit trail |
| GET | `/2023-11/admin/returns/{productReturn}` | Get a return |
| PUT | `/2023-11/admin/returns/{productReturn}` | Update a return |
| PATCH | `/2023-11/admin/returns/{productReturn}` | Update return status |
| DELETE | `/2023-11/admin/returns/{productReturn}` | Delete a return |
| PATCH | `/2023-11/admin/returns/{productReturn}/approve` | Approve a return |
| GET | `/2023-11/admin/returns/{productReturn}/audit` | Audit detail for one return |
| GET | `/2023-11/pickup-locations` | List pickup locations (public) |
| GET | `/2023-11/admin/pickup_locations` | List pickup locations (admin) |
| POST | `/2023-11/admin/pickup_locations` | Create a pickup location |
| GET | `/2023-11/admin/pickup_locations/{pickupLocation}` | Get a pickup location |
| PUT | `/2023-11/admin/pickup_locations/{pickupLocation}` | Update a pickup location |
| DELETE | `/2023-11/admin/pickup_locations/{pickupLocation}` | Delete a pickup location |
| GET | `/2023-11/shipping-fees` | List shipping fees (public) |
| GET | `/2023-11/admin/shipping_fees` | List shipping fees (admin) |
| POST | `/2023-11/admin/shipping_fees` | Create a shipping fee |
| GET | `/2023-11/admin/shipping_fees/{shippingFee}` | Get a shipping fee |
| PUT | `/2023-11/admin/shipping_fees/{shippingFee}` | Update a shipping fee |
| DELETE | `/2023-11/admin/shipping_fees/{shippingFee}` | Delete a shipping fee |

---

## Discounts

### `GET` `/2023-11/admin/discounts`
List all discounts for the company, with pagination and optional filtering.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page (max 100) |
| `filter[search]` | string | No | — | Partial match on `code` |
| `filter[active]` | boolean | No | — | Filter by active flag |
| `filter[valid]` | boolean | No | — | Only return active discounts whose date window includes now |
| `filter[shop_id]` | ULID | No | — | Discounts linked to a specific shop, or company-wide discounts |
| `filter[created_at][start]` | date | No | — | Created-at lower bound |
| `filter[created_at][end]` | date | No | — | Created-at upper bound |
| `filter[updated_at][start]` | date | No | — | Updated-at lower bound |
| `filter[updated_at][end]` | date | No | — | Updated-at upper bound |
| `sort` | string | No | — | One of `created_at`, `updated_at`, `code`, `orders_count`, `application_strategy`. Prefix with `-` for descending. |
| `include` | string | No | — | Comma-separated eager loads: `orders` |
| `all` | boolean | No | — | Include soft-deleted records when `true` |

```http
GET /2023-11/admin/discounts?filter[active]=true&per_page=20 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "data": [
    {
      "id": "01jjks25r1ef7cgej7bgwyhy35",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "code": "SUMMER25",
      "title": "Summer Sale 25%",
      "description": null,
      "discount_type": "percentage",
      "discount_value": 25.00,
      "buy_quantity": null,
      "get_quantity": null,
      "get_reward_type": null,
      "get_reward_value": null,
      "buy_variants": [],
      "get_variants": [],
      "amount": null,
      "requirement_type": "min_cart_amount",
      "requirement_value": 5000.00,
      "usage_limit_global": 100,
      "usage_limit_per_customer": 1,
      "start_date": "2024-06-01T00:00:00.000000Z",
      "end_date": "2024-08-31T23:59:59.000000Z",
      "active": true,
      "application_strategy": "manual",
      "customers": [],
      "products": [],
      "shops": [],
      "created_at": "2024-05-15T10:00:00.000000Z",
      "updated_at": "2024-05-15T10:00:00.000000Z",
      "deleted_at": null
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

**Error responses:**
- `401` — missing or invalid token
- `400` — missing `X-Company` header

---

### `POST` `/2023-11/admin/discounts`
Create a new discount.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_id` | ULID | Yes | exists:companies | Owning company |
| `title` | string | Yes | max:255 | Display title |
| `code` | string | Yes | max:255, unique per company | Coupon code (case-sensitive) |
| `description` | string | No | — | Optional description |
| `discount_type` | string | Yes | `fixed`, `percentage`, `free_shipping`, `buy_x_get_y` | Type of discount |
| `discount_value` | number | No | numeric | Monetary (fixed) or percent value |
| `application_strategy` | string | Yes | `automatic`, `manual` | How the discount is applied |
| `start_date` | date | Yes | — | When discount becomes active |
| `end_date` | date | No | after_or_equal:start_date | When discount expires |
| `active` | boolean | No | — | Whether the discount is enabled |
| `requirement_type` | string | No | `none`, `min_cart_amount`, `min_cart_quantity` | Cart minimum requirement |
| `requirement_value` | number | Conditional | required when requirement_type is not none | Cart minimum value |
| `usage_limit_global` | integer | No | — | Max total redemptions |
| `usage_limit_per_customer` | integer | No | — | Max redemptions per customer |
| `product_ids` | ULID[] | No | exists:products | Restrict to these products |
| `customer_ids` | ULID[] | No | exists:customers | Restrict to these customers |
| `shop_ids` | ULID[] | No | exists:shops | Restrict to these shops |
| `buy_quantity` | integer | No | min:1 | Default quantity to buy (buy X get Y) |
| `get_quantity` | integer | No | min:1 | Default quantity to get (buy X get Y) |
| `get_reward_type` | string | Conditional | required for buy_x_get_y; `free`, `percentage`, `fixed` | Reward type |
| `get_reward_value` | number | Conditional | required when get_reward_type is percentage or fixed | Reward value |
| `buy_variants` | array | Conditional | required for buy_x_get_y | Per-variant buy rules: `[{variant_id, quantity?}]` |
| `get_variants` | array | Conditional | required for buy_x_get_y | Per-variant get rules: `[{variant_id, quantity?}]` |

> **Automatic discount scoping rule:** When `application_strategy` is `automatic` and `discount_type` is not `buy_x_get_y`, the request must provide at least one of: `product_ids`, `shop_ids`, `customer_ids`, or a non-`none` `requirement_type`.

```http
POST /2023-11/admin/discounts HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "title": "25% Off Selected Products",
  "code": "PROMO25",
  "discount_type": "percentage",
  "discount_value": 25,
  "requirement_type": "min_cart_quantity",
  "requirement_value": 5,
  "usage_limit_global": 100,
  "usage_limit_per_customer": 1,
  "active": true,
  "start_date": "2025-01-20 11:49:47",
  "application_strategy": "automatic",
  "product_ids": ["01jjxv6cmncg0m9es0wyy7s77t", "01jjxv67q6aejgkv6s4fgj5vw2"],
  "shop_ids": ["01jjxv5jgbqdph01twqv113vd8"]
}
```

**201 Created**
```json
{
  "id": "01jjxw2janrbwrkt52bqmr60nn",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "PROMO25",
  "title": "25% Off Selected Products",
  "description": null,
  "discount_type": "percentage",
  "discount_value": 25.00,
  "buy_quantity": null,
  "get_quantity": null,
  "get_reward_type": null,
  "get_reward_value": null,
  "buy_variants": [],
  "get_variants": [],
  "amount": null,
  "requirement_type": "min_cart_quantity",
  "requirement_value": 5.00,
  "usage_limit_global": 100,
  "usage_limit_per_customer": 1,
  "start_date": "2025-01-20T11:49:47.000000Z",
  "end_date": null,
  "active": true,
  "application_strategy": "automatic",
  "customers": [],
  "products": [{ "id": "01jjxv6cmncg0m9es0wyy7s77t" }],
  "shops": [],
  "created_at": "2025-01-20T11:49:47.000000Z",
  "updated_at": "2025-01-20T11:49:47.000000Z",
  "deleted_at": null
}
```

**Error responses:**
- `401` — invalid token
- `422` — validation failed, e.g.:
```json
{
  "message": "The code has already been taken.",
  "errors": {
    "code": ["The code has already been taken."]
  }
}
```

---

### `GET` `/2023-11/admin/discounts/{discount}`
Retrieve a single discount with its related products, customers, shops, and buy/get variants.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `discount` | ULID | Discount ID |

```http
GET /2023-11/admin/discounts/01jjks25r1ef7cgej7bgwyhy35 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01jjks25r1ef7cgej7bgwyhy35",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "SUMMER25",
  "title": "Summer Sale 25%",
  "description": null,
  "discount_type": "percentage",
  "discount_value": 25.00,
  "buy_quantity": null,
  "get_quantity": null,
  "get_reward_type": null,
  "get_reward_value": null,
  "buy_variants": [],
  "get_variants": [],
  "amount": null,
  "requirement_type": "min_cart_amount",
  "requirement_value": 5000.00,
  "usage_limit_global": 100,
  "usage_limit_per_customer": 1,
  "start_date": "2024-06-01T00:00:00.000000Z",
  "end_date": "2024-08-31T23:59:59.000000Z",
  "active": true,
  "application_strategy": "manual",
  "customers": [],
  "products": [{ "id": "01jjxv6cmncg0m9es0wyy7s77t" }],
  "shops": [],
  "created_at": "2024-05-15T10:00:00.000000Z",
  "updated_at": "2024-05-15T10:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses:**
- `401` — invalid token
- `404` — discount not found

---

### `PUT` `/2023-11/admin/discounts/{discount}`
Update an existing discount. All fields follow the same validation rules as creation; `code` must remain unique.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `discount` | ULID | Discount ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_id` | ULID | No | exists:companies | Owning company |
| `title` | string | No | max:255 | Display title |
| `code` | string | No | max:255, unique per company | Coupon code (case-sensitive) |
| `description` | string | No | — | Optional description |
| `discount_type` | string | No | `fixed`, `percentage`, `free_shipping`, `buy_x_get_y` | Type of discount |
| `discount_value` | number | No | numeric | Monetary (fixed) or percent value |
| `application_strategy` | string | No | `automatic`, `manual` | How the discount is applied |
| `start_date` | date | No | — | When discount becomes active |
| `end_date` | date | No | after_or_equal:start_date | When discount expires |
| `active` | boolean | No | — | Whether the discount is enabled |
| `requirement_type` | string | No | `none`, `min_cart_amount`, `min_cart_quantity` | Cart minimum requirement |
| `requirement_value` | number | Conditional | required when requirement_type is not none | Cart minimum value |
| `usage_limit_global` | integer | No | — | Max total redemptions |
| `usage_limit_per_customer` | integer | No | — | Max redemptions per customer |
| `product_ids` | ULID[] | No | exists:products | Restrict to these products |
| `customer_ids` | ULID[] | No | exists:customers | Restrict to these customers |
| `shop_ids` | ULID[] | No | exists:shops | Restrict to these shops |
| `buy_quantity` | integer | No | min:1 | Default quantity to buy (buy X get Y) |
| `get_quantity` | integer | No | min:1 | Default quantity to get (buy X get Y) |
| `get_reward_type` | string | No | `free`, `percentage`, `fixed` | Reward type (required for buy_x_get_y) |
| `get_reward_value` | number | Conditional | required when get_reward_type is percentage or fixed | Reward value |
| `buy_variants` | array | No | — | Per-variant buy rules: `[{variant_id, quantity?}]` (required for buy_x_get_y) |
| `get_variants` | array | No | — | Per-variant get rules: `[{variant_id, quantity?}]` (required for buy_x_get_y) |

```http
PUT /2023-11/admin/discounts/01jjxw2janrbwrkt52bqmr60nn HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "title": "Fixed 5000 Off",
  "code": "ZBGEH",
  "discount_type": "fixed",
  "discount_value": 5000,
  "requirement_type": "min_cart_quantity",
  "requirement_value": 5,
  "active": true,
  "start_date": "2025-01-31 11:49:47",
  "application_strategy": "automatic",
  "product_ids": ["01jjxv6cmncg0m9es0wyy7s77t"],
  "shop_ids": ["01jjxv5jgbqdph01twqv113vd8"]
}
```

**200 OK**
```json
{
  "id": "01jjxw2janrbwrkt52bqmr60nn",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "ZBGEH",
  "title": "Fixed 5000 Off",
  "description": null,
  "discount_type": "fixed",
  "discount_value": 5000.00,
  "buy_quantity": null,
  "get_quantity": null,
  "get_reward_type": null,
  "get_reward_value": null,
  "buy_variants": [],
  "get_variants": [],
  "amount": null,
  "requirement_type": "min_cart_quantity",
  "requirement_value": 5.00,
  "usage_limit_global": null,
  "usage_limit_per_customer": null,
  "start_date": "2025-01-31T11:49:47.000000Z",
  "end_date": null,
  "active": true,
  "application_strategy": "automatic",
  "customers": [],
  "products": [{ "id": "01jjxv6cmncg0m9es0wyy7s77t" }],
  "shops": [],
  "created_at": "2025-01-20T11:49:47.000000Z",
  "updated_at": "2025-01-31T12:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses:**
- `401` — invalid token
- `404` — discount not found
- `422` — validation failed

---

### `DELETE` `/2023-11/admin/discounts/{discount}`
Soft-delete a discount.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `discount` | ULID | Discount ID |

```http
DELETE /2023-11/admin/discounts/01jjks25r1ef7cgej7bgwyhy35 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{ "message": "Discount deleted" }
```

**Error responses:**
- `401` — invalid token
- `404` — discount not found

---

### `GET` `/2023-11/discounts`
Public endpoint. Look up a discount by coupon code.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `code` | string | Yes | — | Coupon code to look up |

```http
GET /2023-11/discounts?code=SUMMER25 HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01jjks25r1ef7cgej7bgwyhy35",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "SUMMER25",
  "title": "Summer Sale 25%",
  "description": null,
  "discount_type": "percentage",
  "discount_value": 25.00,
  "buy_quantity": null,
  "get_quantity": null,
  "get_reward_type": null,
  "get_reward_value": null,
  "buy_variants": [],
  "get_variants": [],
  "amount": null,
  "requirement_type": "min_cart_amount",
  "requirement_value": 5000.00,
  "usage_limit_global": 100,
  "usage_limit_per_customer": 1,
  "start_date": "2024-06-01T00:00:00.000000Z",
  "end_date": "2024-08-31T23:59:59.000000Z",
  "active": true,
  "application_strategy": "manual",
  "customers": [],
  "products": [],
  "shops": [],
  "created_at": "2024-05-15T10:00:00.000000Z",
  "updated_at": "2024-05-15T10:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses:**
- `404` — no discount found with that code

---

### `POST` `/2023-11/discounts/automatic`
Public endpoint. Given a cart context, return all automatic discounts that are currently valid and applicable.

- **Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `customer` | object | No | Customer context: `{id}` or `{phone, email}` |
| `customer.id` | ULID | No | Customer ID (takes precedence) |
| `customer.phone` | string | No | Customer phone (used to look up customer) |
| `customer.email` | string | No | Customer email (used to look up customer) |

Additional cart fields required by `DiscountService::validateDiscount` (e.g. `cart_amount`, `cart_quantity`) should be passed in the body as needed by your cart logic.

```http
POST /2023-11/discounts/automatic HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "customer": { "phone": "+237612345678" },
  "cart_amount": 15000,
  "cart_quantity": 6
}
```

**200 OK** — Array of matching discounts (public resource shape: subset of fields).
```json
[
  {
    "id": "01jjxw2janrbwrkt52bqmr60nn",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "code": "PROMO25",
    "title": "25% Off Selected Products",
    "discount_type": "percentage",
    "discount_value": 25.00,
    "buy_quantity": null,
    "get_quantity": null,
    "get_reward_type": null,
    "get_reward_value": null
  }
]
```

**Error responses:**
- `400` — missing `X-Company` header

---

## Deliveries

Deliveries track the fulfillment of order items from warehouse to customer. A delivery belongs to an order and carries per-item quantities. The `status` lifecycle is: `idle` → `taken_in_charge` → `partial_delivery` | `delivered` | `cancelled`.

### `GET` `/2023-11/admin/deliveries`
List deliveries for the company.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page (max 100) |
| `filter[search]` | string | No | — | Searches tracking_number, customer name/email/phone, order reference/amount, shop name, address fields |
| `filter[tracking_number]` | string | No | — | Partial match on tracking number |
| `filter[order_id]` | ULID | No | — | Exact match on order |
| `filter[status]` | string | No | — | Partial match on status |
| `filter[mode]` | string | No | — | Partial match on mode (e.g. `car`, `bike`) |
| `filter[shop_id]` | ULID | No | — | Filter by the order's shop |
| `filter[ids]` | string | No | — | Comma-separated list of delivery IDs |
| `filter[scheduled_at]` | date | No | — | Exact date (returns deliveries scheduled that day) |
| `filter[updated_at][start]` | date | No | — | Updated-at lower bound |
| `filter[updated_at][end]` | date | No | — | Updated-at upper bound |
| `sort` | string | No | — | `status`, `mode`, `starts_at`, `ends_at`, `scheduled_at`, `created_at`, `updated_at` |
| `include` | string | No | — | `address`, `user`, `order`, `order.company`, `order.shop`, `order.customer`, `order.customer.address`, `order.products` |
| `all` | boolean | No | — | Include soft-deleted records |

```http
GET /2023-11/admin/deliveries?filter[status]=taken_in_charge&per_page=20 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "data": [
    {
      "id": "01hkxbwgh3z1bx4hyz87gbmdjx",
      "tracking_number": "TRK-20240315-001",
      "tracking_url": "https://tracking.example.com/TRK-20240315-001",
      "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
      "invoice_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "address_id": "01hkxbwgh3z1bx4hyz87gbmdja",
      "user_id": "01hkxbwgh3z1bx4hyz87gbmdjb",
      "status": "taken_in_charge",
      "mode": "car",
      "metadata": {},
      "scheduled_at": "2024-03-15T10:00:00.000000Z",
      "starts_at": null,
      "ends_at": null,
      "created_at": "2024-03-15T08:00:00.000000Z",
      "updated_at": "2024-03-15T09:00:00.000000Z",
      "address": { "id": "01hkxbwgh3z1bx4hyz87gbmdja", "line1": "123 Main St", "city": "Douala", "country": "Cameroon" },
      "products": [],
      "invoice": null,
      "order": null,
      "user": null,
      "sequence": 1,
      "delivery_status": {
        "is_complete": false,
        "is_partial": false,
        "is_sold_out": false,
        "sequence": 1,
        "is_first": true,
        "delivered_quantity": 0
      }
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

**Error responses:**
- `401` — invalid token
- `400` — missing `X-Company` header

---

### `POST` `/2023-11/admin/deliveries`
Create a new delivery for an order. If `products` is omitted, all undelivered order items are auto-included. Cannot deliver more than the remaining unfulfilled quantity per item.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | ULID | Yes | exists:orders | Order to fulfill |
| `company_id` / `companyId` | ULID | No | exists:companies | Defaults to authenticated user's company |
| `tracking_number` | string | No | unique per company | External carrier tracking number |
| `tracking_url` | string | No | valid URL | Carrier tracking URL |
| `metadata` | object | No | — | Arbitrary key-value data |
| `address_id` | ULID | No | exists:addresses | Delivery address (defaults to order shipping address) |
| `mode` | string | No | — | Transport mode, e.g. `car`, `bike`, `foot` |
| `scheduled_at` | datetime | No | valid date | Planned delivery time |
| `products` | array | Yes | — | Items to deliver |
| `products[].order_product_id` | ULID | Yes | exists:order_product | Order product line ID |
| `products[].quantity` | integer | Yes | min:1 | Quantity to deliver (≤ remaining unfulfilled quantity) |

```http
POST /2023-11/admin/deliveries HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
  "tracking_number": "TRK-20240315-002",
  "tracking_url": "https://tracking.example.com/TRK-20240315-002",
  "mode": "car",
  "scheduled_at": "2024-03-16T09:00:00Z",
  "products": [
    { "order_product_id": "01hkx8hjdwx2e94vxzxez3spr9", "quantity": 2 }
  ]
}
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmdj2",
  "tracking_number": "TRK-20240316-002",
  "tracking_url": "https://tracking.example.com/TRK-20240316-002",
  "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
  "invoice_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address_id": "01hkxbwgh3z1bx4hyz87gbmdja",
  "user_id": null,
  "status": "idle",
  "mode": "car",
  "metadata": {},
  "scheduled_at": "2024-03-16T09:00:00.000000Z",
  "starts_at": null,
  "ends_at": null,
  "created_at": "2024-03-15T11:00:00.000000Z",
  "updated_at": "2024-03-15T11:00:00.000000Z",
  "address": { "id": "01hkxbwgh3z1bx4hyz87gbmdja", "line1": "123 Main St", "city": "Douala", "country": "Cameroon" },
  "products": [
    {
      "id": "01hkx8hjdwx2e94vxzxez3spr9",
      "order_product_id": "01hkx8hjdwx2e94vxzxez3spr9",
      "quantity": 2,
      "title": "Blue T-Shirt XL"
    }
  ],
  "invoice": null,
  "order": null,
  "user": null,
  "sequence": 1,
  "delivery_status": {
    "is_complete": false,
    "is_partial": false,
    "is_sold_out": false,
    "sequence": 1,
    "is_first": true,
    "delivered_quantity": 2
  }
}
```

**Error responses:**
- `401` — invalid token
- `422` — validation failed (e.g. quantity exceeds remaining)
- `500` — all items already delivered or business rule violation

---

### `POST` `/2023-11/admin/deliveries/update-position`
Push a real-time GPS position for a delivery. Only the assigned courier or an admin/manager can submit positions. Broadcasts a `DeliveryPositionUpdated` event via Laravel Reverb.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `delivery_id` | ULID | Yes | exists:deliveries | Target delivery |
| `latitude` | number | Yes | between:-90,90 | GPS latitude |
| `longitude` | number | Yes | between:-180,180 | GPS longitude |
| `accuracy` | number | No | numeric | Position accuracy in metres |
| `heading` | number | No | numeric | Bearing in degrees (0–360) |
| `speed` | number | No | numeric | Speed in m/s |

```http
POST /2023-11/admin/deliveries/update-position HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "delivery_id": "01hkxbwgh3z1bx4hyz87gbmdjx",
  "latitude": 3.8480,
  "longitude": 11.5021,
  "accuracy": 10.5,
  "heading": 90.0,
  "speed": 12.0
}
```

**200 OK**
```json
{
  "success": true,
  "data": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdjz",
    "delivery_id": "01hkxbwgh3z1bx4hyz87gbmdjx",
    "latitude": 3.8480,
    "longitude": 11.5021,
    "accuracy": 10.5,
    "heading": 90.0,
    "speed": 12.0,
    "recorded_at": "2024-03-15T10:23:45.000000Z"
  }
}
```

**Error responses:**
- `401` — invalid token
- `403` — caller is not the assigned courier or an admin/manager
- `422` — validation failed

---

### `GET` `/2023-11/admin/deliveries/{delivery}`
Get a single delivery with full related data loaded by default (address, user, order, order products, invoices).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `delivery` | ULID | Delivery ID |

- **Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `include` | string | No | Comma-separated: `address`, `user`, `order`, `order.shop`, `order.shop.address`, `order.customer`, `order.customer.address`, `order.user`, `order.products`, `order.invoices`, `orderProducts`, `orderProducts.orderProduct` |

```http
GET /2023-11/admin/deliveries/01hkxbwgh3z1bx4hyz87gbmdjx HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmdjx",
  "tracking_number": "TRK-20240315-001",
  "tracking_url": "https://tracking.example.com/TRK-20240315-001",
  "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
  "invoice_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address_id": "01hkxbwgh3z1bx4hyz87gbmdja",
  "user_id": "01hkxbwgh3z1bx4hyz87gbmdj1",
  "status": "taken_in_charge",
  "mode": "car",
  "metadata": {},
  "scheduled_at": "2024-03-15T10:00:00.000000Z",
  "starts_at": null,
  "ends_at": null,
  "created_at": "2024-03-15T08:00:00.000000Z",
  "updated_at": "2024-03-15T09:00:00.000000Z",
  "address": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdja",
    "line1": "123 Main St",
    "city": "Douala",
    "country": "Cameroon"
  },
  "products": [
    {
      "id": "01hkx8hjdwx2e94vxzxez3spr9",
      "order_product_id": "01hkx8hjdwx2e94vxzxez3spr9",
      "quantity": 2,
      "title": "Blue T-Shirt XL"
    }
  ],
  "invoice": null,
  "order": {
    "id": "01hkx8hjdwx2e94vxzxez3spr8",
    "reference": "#ORDER_1001",
    "amount": 25000.00,
    "shop": { "id": "01jjxv5jgbqdph01twqv113vd8", "name": "Boutique Principale" },
    "customer": {
      "id": "01jxd8qqzxtwjzgs3629qv9jt6",
      "first_name": "Alice",
      "last_name": "Dupont",
      "email": "alice@example.com"
    },
    "products": [
      {
        "id": "01hkx8hjdwx2e94vxzxez3spr9",
        "title": "Blue T-Shirt XL",
        "quantity": 2,
        "price": 12500.00
      }
    ]
  },
  "user": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdj1",
    "name": "Bob Courier",
    "email": "bob@example.com",
    "totalDeliveries": 45
  },
  "sequence": 1,
  "delivery_status": {
    "is_complete": true,
    "is_partial": false,
    "is_sold_out": false,
    "sequence": 1,
    "is_first": true,
    "delivered_quantity": 2
  }
}
```

**Error responses:**
- `401` — invalid token
- `404` — delivery not found

---

### `PUT` `/2023-11/admin/deliveries/{delivery}`
Update a delivery's status or assign a courier. Assigning a user (`user_id`) automatically transitions the status to `taken_in_charge` unless already `delivered`. A non-admin courier can only update their own delivery's status.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `delivery` | ULID | Delivery ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `user_id` | ULID | No | exists:users | Assign a courier |
| `status` | string | No | `idle`, `pending`, `taken_in_charge`, `partial_delivery`, `delivered`, `cancelled` | New status |
| `tracking_number` | string | No | unique per company | Update tracking number |
| `tracking_url` | string | No | valid URL | Update tracking URL |
| `metadata` | object | No | — | Arbitrary metadata |
| `mode` | string | No | — | Transport mode |
| `scheduled_at` | datetime | No | valid date | Rescheduled delivery time |

```http
PUT /2023-11/admin/deliveries/01hkxbwgh3z1bx4hyz87gbmdjx HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "status": "taken_in_charge",
  "user_id": "01hkxbwgh3z1bx4hyz87gbmdj1",
  "mode": "car",
  "tracking_number": "TRK-001",
  "tracking_url": "https://tracking.example.com/TRK-001"
}
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmdjx",
  "tracking_number": "TRK-001",
  "tracking_url": "https://tracking.example.com/TRK-001",
  "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
  "invoice_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address_id": "01hkxbwgh3z1bx4hyz87gbmdja",
  "user_id": "01hkxbwgh3z1bx4hyz87gbmdj1",
  "status": "taken_in_charge",
  "mode": "car",
  "metadata": {},
  "scheduled_at": "2024-03-15T10:00:00.000000Z",
  "starts_at": null,
  "ends_at": null,
  "created_at": "2024-03-15T08:00:00.000000Z",
  "updated_at": "2024-03-15T09:30:00.000000Z",
  "address": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdja",
    "line1": "123 Main St",
    "city": "Douala",
    "country": "Cameroon"
  },
  "products": [
    {
      "id": "01hkx8hjdwx2e94vxzxez3spr9",
      "order_product_id": "01hkx8hjdwx2e94vxzxez3spr9",
      "quantity": 2,
      "title": "Blue T-Shirt XL"
    }
  ],
  "invoice": null,
  "order": {
    "id": "01hkx8hjdwx2e94vxzxez3spr8",
    "reference": "#ORDER_1001",
    "amount": 25000.00,
    "shop": { "id": "01jjxv5jgbqdph01twqv113vd8", "name": "Boutique Principale" },
    "customer": {
      "id": "01jxd8qqzxtwjzgs3629qv9jt6",
      "first_name": "Alice",
      "last_name": "Dupont",
      "email": "alice@example.com"
    }
  },
  "user": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdj1",
    "name": "Bob Courier",
    "email": "bob@example.com",
    "totalDeliveries": 45
  },
  "sequence": 1,
  "delivery_status": {
    "is_complete": false,
    "is_partial": false,
    "is_sold_out": false,
    "sequence": 1,
    "is_first": true,
    "delivered_quantity": 2
  }
}
```

**Error responses:**
- `401` — invalid token
- `403` — courier attempting to update a delivery they are not assigned to
- `404` — delivery not found
- `422` — validation failed

---

### `DELETE` `/2023-11/admin/deliveries/{delivery}`
Delete a delivery and its associated order product lines.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `delivery` | ULID | Delivery ID |

```http
DELETE /2023-11/admin/deliveries/01hkx8hjdwx2e94vxzxez3spr8 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**204 No Content**

**Error responses:**
- `401` — invalid token
- `404` — delivery not found

---

### `GET` `/2023-11/deliveries` · `GET` `/2023-11/delivery`
Public versions of the list endpoint. Identical behaviour to `GET /2023-11/admin/deliveries` but accessible without a bearer token (requires only `X-Company`). Both paths resolve to the same controller method and support the same filters.

```http
GET /2023-11/deliveries?filter[status]=idle HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "data": [
    {
      "id": "01hkxbwgh3z1bx4hyz87gbmdjx",
      "tracking_number": "TRK-20240315-001",
      "tracking_url": "https://tracking.example.com/TRK-20240315-001",
      "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
      "invoice_id": null,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "address_id": "01hkxbwgh3z1bx4hyz87gbmdja",
      "user_id": null,
      "status": "idle",
      "mode": "car",
      "metadata": {},
      "scheduled_at": "2024-03-15T10:00:00.000000Z",
      "starts_at": null,
      "ends_at": null,
      "created_at": "2024-03-15T08:00:00.000000Z",
      "updated_at": "2024-03-15T08:00:00.000000Z",
      "address": { "id": "01hkxbwgh3z1bx4hyz87gbmdja", "line1": "123 Main St", "city": "Douala", "country": "Cameroon" },
      "products": [],
      "invoice": null,
      "order": null,
      "user": null,
      "sequence": 1,
      "delivery_status": {
        "is_complete": false,
        "is_partial": false,
        "is_sold_out": false,
        "sequence": 1,
        "is_first": true,
        "delivered_quantity": 0
      }
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

### `GET` `/2023-11/deliveries/{delivery}` · `GET` `/2023-11/delivery/{delivery}`
Public versions of the single-delivery endpoint. Both paths behave identically to `GET /2023-11/admin/deliveries/{delivery}`.

```http
GET /2023-11/deliveries/01hkxbwgh3z1bx4hyz87gbmdjx HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmdjx",
  "tracking_number": "TRK-20240315-001",
  "tracking_url": "https://tracking.example.com/TRK-20240315-001",
  "order_id": "01hkx8hjdwx2e94vxzxez3spr8",
  "invoice_id": null,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "address_id": "01hkxbwgh3z1bx4hyz87gbmdja",
  "user_id": null,
  "status": "idle",
  "mode": "car",
  "metadata": {},
  "scheduled_at": "2024-03-15T10:00:00.000000Z",
  "starts_at": null,
  "ends_at": null,
  "created_at": "2024-03-15T08:00:00.000000Z",
  "updated_at": "2024-03-15T08:00:00.000000Z",
  "address": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdja",
    "line1": "123 Main St",
    "city": "Douala",
    "country": "Cameroon"
  },
  "products": [],
  "invoice": null,
  "order": null,
  "user": null,
  "sequence": 1,
  "delivery_status": {
    "is_complete": false,
    "is_partial": false,
    "is_sold_out": false,
    "sequence": 1,
    "is_first": true,
    "delivered_quantity": 0
  }
}
```

---

### `GET` `/2023-11/deliveries/{delivery}/last_position` · `GET` `/2023-11/delivery/{delivery}/last_position`
Get the most recent GPS tracking position for a delivery.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `delivery` | ULID | Delivery ID |

```http
GET /2023-11/deliveries/01hkxbwgh3z1bx4hyz87gbmdjx/last_position HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "delivery": {
    "id": "01hkxbwgh3z1bx4hyz87gbmdjx",
    "status": "taken_in_charge",
    "order_id": "01hkx8hjdwx2e94vxzxez3spr8"
  },
  "position": {
    "latitude": 3.8480,
    "longitude": 11.5021,
    "accuracy": 10.5,
    "heading": 90.0,
    "speed": 12.0,
    "updated_at": "2024-03-15T10:23:45.000000Z"
  }
}
```

When no position has been recorded yet, `position` is `null` and a `message` field is included.

**Error responses:**
- `404` — delivery not found

---

## Product Returns

Returns capture customer return requests for order items. The status flow is: `draft` → `waiting_approval` → `confirmed` (via approve) or `cancelled`. The `done` status marks physical receipt. Returns in `confirmed`, `cancelled`, or `done` status cannot be edited.

### `GET` `/2023-11/admin/returns`
List product returns. Can be scoped to a specific shop via `X-Shop` header or `shopId` query/body parameter.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[status]` | string | No | — | Exact status: `draft`, `waiting_approval`, `confirmed`, `done`, `cancelled` |
| `filter[order_id]` | ULID | No | — | Filter by order |
| `filter[customer_id]` | ULID | No | — | Filter by customer |
| `filter[search]` | string | No | — | Searches status, source, order reference, customer first_name/email |
| `sort` | string | No | — | `status`, `order_id`, `customer_id`, `return_date`, `created_at` |

```http
GET /2023-11/admin/returns?filter[status]=waiting_approval HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "data": [
    {
      "id": "01jxk51r11tvfe62esnx18hvpz",
      "return_date": "2025-06-11T23:00:00.000000Z",
      "status": "waiting_approval",
      "reference": "RET-0001",
      "source": null,
      "comment": "Items do not match description.",
      "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
      "warehouse_id": null,
      "metadata": null,
      "credit_notes": [],
      "warehouse": null,
      "order": { "id": "01jxd8xfp2256xf7mjt4fpsd9z", "reference": "#ORDER_1336" },
      "customer": { "id": "01jxd8qqzxtwjzgs3629qv9jt6", "first_name": "Margie", "last_name": "Zulauf" },
      "lines": [
        {
          "id": "01jxk51r20g5yxx2cdwzftyxen",
          "return_id": "01jxk51r11tvfe62esnx18hvpz",
          "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
          "variant": { "id": "01jxd8qxgk36sdxsv2veqs00m5", "title": "Default Title" },
          "order_product": null,
          "quantity": 2,
          "reason": "defective",
          "comment": null,
          "created_at": "2025-06-12T22:44:25.000000Z",
          "updated_at": "2025-06-12T22:44:25.000000Z"
        }
      ],
      "created_at": "2025-06-12T22:44:25.000000Z",
      "updated_at": "2025-06-12T22:44:25.000000Z"
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

### `POST` `/2023-11/admin/returns`
Create a new product return for an order.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | ULID | Yes | exists:orders | Order being returned |
| `status` | string | Yes | `draft`, `waiting_approval`, `confirmed`, `done`, `cancelled` | Initial status |
| `lines` | array | Yes | min:1 | Return line items |
| `lines[].variant_id` | ULID | Yes | exists:product_variants | Variant being returned |
| `lines[].quantity` | integer | Yes | min:1 | Quantity to return |
| `lines[].reason` | string | Yes | `defective`, `nonconforming`, `delivery`, `dissatisfaction` | Return reason |
| `lines[].comment` | string | No | max:500 | Optional line comment |
| `comment` | string | No | max:500 | Overall return comment |
| `customer_id` | ULID | No | exists:customers | Defaults to order's customer |
| `warehouse_id` | ULID | No | exists:warehouses | Target restock warehouse |
| `return_date` | datetime | No | — | Defaults to now |

```http
POST /2023-11/admin/returns HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "return_date": "2025-06-11T23:00:00.000000Z",
  "status": "waiting_approval",
  "lines": [
    {
      "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
      "quantity": 2,
      "reason": "defective",
      "comment": null
    }
  ],
  "comment": "Items do not match description."
}
```

**200 OK**
```json
{
  "id": "01jxk51r11tvfe62esnx18hvpz",
  "return_date": "2025-06-11T23:00:00.000000Z",
  "status": "waiting_approval",
  "reference": "RET-0001",
  "source": null,
  "comment": "Items do not match description.",
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "warehouse_id": null,
  "metadata": null,
  "credit_notes": [],
  "warehouse": null,
  "order": { "id": "01jxd8xfp2256xf7mjt4fpsd9z", "reference": "#ORDER_1336" },
  "customer": { "id": "01jxd8qqzxtwjzgs3629qv9jt6", "first_name": "Margie", "last_name": "Zulauf" },
  "lines": [
    {
      "id": "01jxk51r20g5yxx2cdwzftyxen",
      "return_id": "01jxk51r11tvfe62esnx18hvpz",
      "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
      "variant": { "id": "01jxd8qxgk36sdxsv2veqs00m5", "title": "Default Title" },
      "order_product": null,
      "quantity": 2,
      "reason": "defective",
      "comment": null,
      "created_at": "2025-06-12T22:44:25.000000Z",
      "updated_at": "2025-06-12T22:44:25.000000Z"
    }
  ],
  "created_at": "2025-06-12T22:44:25.000000Z",
  "updated_at": "2025-06-12T22:44:25.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `422` — validation failed
- `500` — variant not in order, quantity exceeded, or other business rule violation

---

### `GET` `/2023-11/admin/returns/audit/report`
Generate an aggregate fiscal audit report covering a date range.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | date | No | Start of current month | Lower bound |
| `end_date` | date | No | End of current month | Upper bound (must be >= start_date) |
| `period` | string | No | — | Shorthand: `month`, `quarter`, `year` (overrides start/end_date) |

```http
GET /2023-11/admin/returns/audit/report?period=quarter HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "period": { "start": "2024-01-01", "end": "2024-03-31" },
  "summary": {
    "total_returns": 42,
    "total_amount": 1250000.00,
    "confirmed_returns": 30,
    "pending_approval": 8,
    "average_processing_time": 12.5
  },
  "returns_by_status": [
    { "status": "confirmed", "count": 30 },
    { "status": "waiting_approval", "count": 8 }
  ],
  "returns_by_reason": [
    { "reason": "defective", "count": 20, "total_quantity": 35 }
  ],
  "approval_statistics": {
    "total_approved": 30,
    "approvers": [{ "user_name": "Alice Manager", "count": 30 }],
    "average_approval_time": 8.2
  },
  "monthly_trend": [
    { "month": "2024-01", "count": 15, "confirmed_count": 12 }
  ],
  "credit_notes_issued": [
    { "status": "issued", "count": 28, "total_amount": 950000.00 }
  ]
}
```

**Error responses:**
- `401` — invalid token (requires `VIEW_RETURNS` permission)
- `422` — invalid date range

---

### `GET` `/2023-11/admin/returns/audit/trail`
Full chronological audit trail across all returns.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | date | No | — | Filter by created_at >= |
| `end_date` | date | No | — | Filter by created_at <= |
| `user_id` | ULID | No | — | Filter trail entries by user |
| `action` | string | No | — | Filter by action: `created`, `status_changed`, `approved`, `deleted` |

```http
GET /2023-11/admin/returns/audit/trail?action=approved HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "total_entries": 2,
  "entries": [
    {
      "return_id": "01jxk51r11tvfe62esnx18hvpz",
      "return_reference": "RET-0001",
      "order_reference": "#ORDER_1336",
      "action": "approved",
      "user_name": "Alice Manager",
      "timestamp": "2025-06-13T10:00:00.000000Z",
      "details": { "approval_comment": "OK" },
      "ip_address": "192.168.1.1"
    }
  ]
}
```

**Error responses:**
- `401` — invalid token (requires `VIEW_RETURNS` permission)
- `422` — invalid date or action value

---

### `GET` `/2023-11/admin/returns/{productReturn}`
Retrieve a single return with order, customer, lines (with variant and order product), warehouse, and credit notes loaded.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productReturn` | ULID | Return ID |

```http
GET /2023-11/admin/returns/01jxk51r11tvfe62esnx18hvpz HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01jxk51r11tvfe62esnx18hvpz",
  "return_date": "2025-06-11T23:00:00.000000Z",
  "status": "waiting_approval",
  "reference": "RET-0001",
  "source": null,
  "comment": "Items do not match description.",
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "warehouse_id": null,
  "metadata": null,
  "credit_notes": [],
  "warehouse": null,
  "order": { "id": "01jxd8xfp2256xf7mjt4fpsd9z", "reference": "#ORDER_1336" },
  "customer": { "id": "01jxd8qqzxtwjzgs3629qv9jt6", "first_name": "Margie", "last_name": "Zulauf" },
  "lines": [
    {
      "id": "01jxk51r20g5yxx2cdwzftyxen",
      "return_id": "01jxk51r11tvfe62esnx18hvpz",
      "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
      "variant": { "id": "01jxd8qxgk36sdxsv2veqs00m5", "title": "Default Title" },
      "order_product": null,
      "quantity": 2,
      "reason": "defective",
      "comment": null,
      "created_at": "2025-06-12T22:44:25.000000Z",
      "updated_at": "2025-06-12T22:44:25.000000Z"
    }
  ],
  "created_at": "2025-06-12T22:44:25.000000Z",
  "updated_at": "2025-06-12T22:44:25.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `404` — return not found

---

### `PUT` `/2023-11/admin/returns/{productReturn}`
Replace a return's content (lines are fully replaced). Returns in `confirmed`, `cancelled`, or `done` status cannot be updated.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productReturn` | ULID | Return ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `order_id` | ULID | Yes | exists:orders | Order being returned |
| `status` | string | Yes | `draft`, `waiting_approval`, `confirmed`, `done`, `cancelled` | New status |
| `lines` | array | Yes | min:1 | Return line items (fully replaces existing lines) |
| `lines[].variant_id` | ULID | Yes | exists:product_variants | Variant being returned |
| `lines[].quantity` | integer | Yes | min:1 | Quantity to return |
| `lines[].reason` | string | Yes | `defective`, `nonconforming`, `delivery`, `dissatisfaction` | Return reason |
| `lines[].comment` | string | No | max:500 | Optional line comment |
| `comment` | string | No | max:500 | Overall return comment |
| `customer_id` | ULID | No | exists:customers | Override return customer |
| `warehouse_id` | ULID | No | exists:warehouses | Target restock warehouse |
| `return_date` | datetime | No | — | Return date (defaults to existing value) |

```http
PUT /2023-11/admin/returns/01jxk51r11tvfe62esnx18hvpz HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "status": "waiting_approval",
  "lines": [
    { "variant_id": "01jxd8qxgk36sdxsv2veqs00m5", "quantity": 1, "reason": "defective" }
  ]
}
```

**200 OK**
```json
{
  "id": "01jxk51r11tvfe62esnx18hvpz",
  "return_date": "2025-06-11T23:00:00.000000Z",
  "status": "waiting_approval",
  "reference": "RET-0001",
  "source": null,
  "comment": null,
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "warehouse_id": null,
  "metadata": null,
  "credit_notes": [],
  "warehouse": null,
  "order": { "id": "01jxd8xfp2256xf7mjt4fpsd9z", "reference": "#ORDER_1336" },
  "customer": { "id": "01jxd8qqzxtwjzgs3629qv9jt6", "first_name": "Margie", "last_name": "Zulauf" },
  "lines": [
    {
      "id": "01jxk51r20g5yxx2cdwzftyxen",
      "return_id": "01jxk51r11tvfe62esnx18hvpz",
      "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
      "variant": { "id": "01jxd8qxgk36sdxsv2veqs00m5", "title": "Default Title" },
      "order_product": null,
      "quantity": 1,
      "reason": "defective",
      "comment": null,
      "created_at": "2025-06-12T22:44:25.000000Z",
      "updated_at": "2025-06-12T23:10:00.000000Z"
    }
  ],
  "created_at": "2025-06-12T22:44:25.000000Z",
  "updated_at": "2025-06-12T23:10:00.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `404` — return not found
- `422` — validation failed
- `500` — return is in a terminal status

---

### `PATCH` `/2023-11/admin/returns/{productReturn}`
Update only the status of a return. Cannot transition directly to `confirmed` or `done` via this endpoint (use the `/approve` endpoint for confirmation).

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productReturn` | ULID | Return ID |

- **Query parameters (or body):**

| Name | Type | Required | Description |
|---|---|---|---|
| `status` | string | Yes | `draft`, `waiting_approval`, `cancelled` (not `confirmed` or `done`) |

```http
PATCH /2023-11/admin/returns/01jy18appsb2ym6pqxwqk6zxg4 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{ "status": "waiting_approval" }
```

**200 OK**
```json
{
  "id": "01jy18appsb2ym6pqxwqk6zxg4",
  "return_date": "2025-06-11T23:00:00.000000Z",
  "status": "waiting_approval",
  "reference": "RET-0002",
  "source": null,
  "comment": null,
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "warehouse_id": null,
  "metadata": null,
  "credit_notes": [],
  "warehouse": null,
  "order": { "id": "01jxd8xfp2256xf7mjt4fpsd9z", "reference": "#ORDER_1336" },
  "customer": { "id": "01jxd8qqzxtwjzgs3629qv9jt6", "first_name": "Margie", "last_name": "Zulauf" },
  "lines": [
    {
      "id": "01jy18appsb2ym6pqxwqk6zxg5",
      "return_id": "01jy18appsb2ym6pqxwqk6zxg4",
      "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
      "variant": { "id": "01jxd8qxgk36sdxsv2veqs00m5", "title": "Default Title" },
      "order_product": null,
      "quantity": 1,
      "reason": "defective",
      "comment": null,
      "created_at": "2025-06-12T22:44:25.000000Z",
      "updated_at": "2025-06-12T23:15:00.000000Z"
    }
  ],
  "created_at": "2025-06-12T22:44:25.000000Z",
  "updated_at": "2025-06-12T23:15:00.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `404` — return not found
- `422` — invalid status value, or attempting to set `confirmed`/`done`
- `500` — return already in terminal status

---

### `DELETE` `/2023-11/admin/returns/{productReturn}`
Soft-delete a return.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productReturn` | ULID | Return ID |

```http
DELETE /2023-11/admin/returns/01jxw0bgeza8kg5s619zdp4jrm HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**204 No Content**

**Error responses:**
- `401` — invalid token
- `404` — return not found

---

### `PATCH` `/2023-11/admin/returns/{productReturn}/approve`
Approve a return. Only returns with status `waiting_approval` can be approved. Transitions status to `confirmed`, restocks inventory according to each line's reason actions, and recalculates order billing. Requires `APPROVE_RETURNS` permission.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productReturn` | ULID | Return ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `approval_comment` | string | No | max:500 | Approval note stored in metadata |

```http
PATCH /2023-11/admin/returns/01jxk51r11tvfe62esnx18hvpz/approve HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{ "approval_comment": "Verified defective, approved for full restock." }
```

**200 OK**
```json
{
  "id": "01jxk51r11tvfe62esnx18hvpz",
  "return_date": "2025-06-11T23:00:00.000000Z",
  "status": "confirmed",
  "reference": "RET-0001",
  "source": null,
  "comment": "Items do not match description.",
  "order_id": "01jxd8xfp2256xf7mjt4fpsd9z",
  "warehouse_id": null,
  "metadata": { "approval_comment": "Verified defective, approved for full restock." },
  "credit_notes": [],
  "warehouse": null,
  "order": { "id": "01jxd8xfp2256xf7mjt4fpsd9z", "reference": "#ORDER_1336" },
  "customer": { "id": "01jxd8qqzxtwjzgs3629qv9jt6", "first_name": "Margie", "last_name": "Zulauf" },
  "lines": [
    {
      "id": "01jxk51r20g5yxx2cdwzftyxen",
      "return_id": "01jxk51r11tvfe62esnx18hvpz",
      "variant_id": "01jxd8qxgk36sdxsv2veqs00m5",
      "variant": { "id": "01jxd8qxgk36sdxsv2veqs00m5", "title": "Default Title" },
      "order_product": null,
      "quantity": 2,
      "reason": "defective",
      "comment": null,
      "created_at": "2025-06-12T22:44:25.000000Z",
      "updated_at": "2025-06-13T10:00:00.000000Z"
    }
  ],
  "created_at": "2025-06-12T22:44:25.000000Z",
  "updated_at": "2025-06-13T10:00:00.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `403` — missing `APPROVE_RETURNS` permission
- `404` — return not found
- `500` — return is not in `waiting_approval` status

---

### `GET` `/2023-11/admin/returns/{productReturn}/audit`
Detailed audit information for a single return, including audit trail, status history, and financial impact.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `productReturn` | ULID | Return ID (bound as `return` in the route) |

```http
GET /2023-11/admin/returns/01jxk51r11tvfe62esnx18hvpz/audit HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "return": { "id": "01jxk51r11tvfe62esnx18hvpz", "status": "confirmed" },
  "audit_trail": [
    { "action": "created", "user_name": "Bob", "timestamp": "2025-06-12T22:44:25.000000Z", "details": {} }
  ],
  "status_history": [
    { "from": "draft", "to": "waiting_approval", "at": "2025-06-12T22:45:00.000000Z" }
  ],
  "financial_impact": {
    "return_amount": 3279800.00,
    "credit_notes_total": 3279800.00,
    "refunds_total": 0.00
  }
}
```

**Error responses:**
- `401` — invalid token (requires `VIEW_RETURNS` permission)
- `404` — return not found

---

## Pickup Locations

Pickup locations are physical in-store or click-and-collect points. They can be linked to warehouses (for stock visibility) and carry a postal address.

### `GET` `/2023-11/pickup-locations` · `GET` `/2023-11/admin/pickup_locations`
List pickup locations. Both paths resolve to the same controller method; the public route requires only `X-Company`.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[search]` | string | No | — | Partial match on `name` |
| `filter[active]` | boolean | No | — | Filter by active status |
| `filter[shop]` | ULID | No | — | Filter by shop |
| `filter[created_at][start]` | date | No | — | Created-at lower bound |
| `filter[created_at][end]` | date | No | — | Created-at upper bound |
| `filter[updated_at][start]` | date | No | — | Updated-at lower bound |
| `filter[updated_at][end]` | date | No | — | Updated-at upper bound |
| `sort` | string | No | — | `created_at`, `updated_at`, `name`, `orders_count` |
| `include` | string | No | — | `orders` |

```http
GET /2023-11/admin/pickup_locations?filter[active]=true HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "data": [
    {
      "id": "01hkxbwgh3z1bx4hyz87gbmda1",
      "name": "Main Store Douala",
      "amount": 0.00,
      "active": true,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shop_id": "01jjxv5jgbqdph01twqv113vd8",
      "metadata": null,
      "shop": { "id": "01jjxv5jgbqdph01twqv113vd8", "name": "Boutique Principale" },
      "warehouses": [{ "id": "01hkxbwgh3z1bx4hyz87gbmdw1", "name": "Main Warehouse" }],
      "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"],
      "address": { "line1": "123 Rue de la Paix", "city": "Douala", "country": "Cameroon" },
      "full_address": "123 Rue de la Paix, Douala, Cameroon",
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
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

### `POST` `/2023-11/admin/pickup_locations`
Create a new pickup location.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:255, unique per company | Location name |
| `amount` | number | No | — | Pickup fee amount |
| `metadata` | object | No | — | Arbitrary data |
| `company_id` / `companyId` | ULID | No | — | Defaults to auth company |
| `shop_id` / `shopId` | ULID | No | — | Associated shop |
| `address` | object | No | — | Postal address |
| `address.line1` | string | Conditional | required with address | Street address |
| `address.city` | string | Conditional | required with address | City |
| `address.country` | string | Conditional | required with address | Country |
| `address.phone` | string | No | — | Contact phone |
| `address.latitude` | number | No | — | GPS latitude |
| `address.longitude` | number | No | — | GPS longitude |
| `warehouse_ids` | ULID[] | No | exists:warehouses | Link to warehouses |

```http
POST /2023-11/admin/pickup_locations HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Akwa Store",
  "amount": 0,
  "shop_id": "01jjxv5jgbqdph01twqv113vd8",
  "address": {
    "line1": "Rue Joss, Akwa",
    "city": "Douala",
    "country": "Cameroon",
    "latitude": 4.0511,
    "longitude": 9.7085
  },
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"]
}
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmda2",
  "name": "Akwa Store",
  "amount": 0.00,
  "active": true,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01jjxv5jgbqdph01twqv113vd8",
  "metadata": null,
  "shop": { "id": "01jjxv5jgbqdph01twqv113vd8", "name": "Boutique Principale" },
  "warehouses": [{ "id": "01hkxbwgh3z1bx4hyz87gbmdw1", "name": "Main Warehouse" }],
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"],
  "address": {
    "line1": "Rue Joss, Akwa",
    "city": "Douala",
    "country": "Cameroon",
    "latitude": 4.0511,
    "longitude": 9.7085
  },
  "full_address": "Rue Joss, Akwa, Douala, Cameroon",
  "created_at": "2024-06-01T10:00:00.000000Z",
  "updated_at": "2024-06-01T10:00:00.000000Z"
}
```

**Error responses:**
- `400` — name not unique within company (returns `errors` object)
- `401` — invalid token

---

### `GET` `/2023-11/admin/pickup_locations/{pickupLocation}`
Get a single pickup location.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pickupLocation` | ULID | Pickup location ID |

```http
GET /2023-11/admin/pickup_locations/01hkxbwgh3z1bx4hyz87gbmda1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmda1",
  "name": "Main Store Douala",
  "amount": 0.00,
  "active": true,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01jjxv5jgbqdph01twqv113vd8",
  "metadata": null,
  "shop": { "id": "01jjxv5jgbqdph01twqv113vd8", "name": "Boutique Principale" },
  "warehouses": [{ "id": "01hkxbwgh3z1bx4hyz87gbmdw1", "name": "Main Warehouse" }],
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"],
  "address": { "line1": "123 Rue de la Paix", "city": "Douala", "country": "Cameroon" },
  "full_address": "123 Rue de la Paix, Douala, Cameroon",
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `404` — not found

---

### `PUT` `/2023-11/admin/pickup_locations/{pickupLocation}`
Update a pickup location.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pickupLocation` | ULID | Pickup location ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:255, unique per company | Location name |
| `amount` | number | No | — | Pickup fee amount |
| `active` | boolean | No | — | Whether the location is active |
| `metadata` | object | No | — | Arbitrary data |
| `company_id` / `companyId` | ULID | No | — | Defaults to auth company |
| `shop_id` / `shopId` | ULID | No | — | Associated shop |
| `address` | object | No | — | Postal address |
| `address.line1` | string | Conditional | required with address | Street address |
| `address.city` | string | Conditional | required with address | City |
| `address.country` | string | Conditional | required with address | Country |
| `address.phone` | string | No | — | Contact phone |
| `address.latitude` | number | No | — | GPS latitude |
| `address.longitude` | number | No | — | GPS longitude |
| `warehouse_ids` | ULID[] | No | exists:warehouses | Link to warehouses |

```http
PUT /2023-11/admin/pickup_locations/01hkxbwgh3z1bx4hyz87gbmda1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Akwa Store Updated",
  "amount": 500,
  "active": true,
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"]
}
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmda1",
  "name": "Akwa Store Updated",
  "amount": 500.00,
  "active": true,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": "01jjxv5jgbqdph01twqv113vd8",
  "metadata": null,
  "shop": { "id": "01jjxv5jgbqdph01twqv113vd8", "name": "Boutique Principale" },
  "warehouses": [{ "id": "01hkxbwgh3z1bx4hyz87gbmdw1", "name": "Main Warehouse" }],
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"],
  "address": { "line1": "123 Rue de la Paix", "city": "Douala", "country": "Cameroon" },
  "full_address": "123 Rue de la Paix, Douala, Cameroon",
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-06-15T09:00:00.000000Z"
}
```

**Error responses:**
- `400` — name conflict
- `401` — invalid token
- `404` — not found

---

### `DELETE` `/2023-11/admin/pickup_locations/{pickupLocation}`
Soft-delete a pickup location. Pass `force=true` to hard-delete.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `pickupLocation` | ULID | Pickup location ID |

- **Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `force` | boolean | No | Hard-delete when `true` |

```http
DELETE /2023-11/admin/pickup_locations/01hkxbwgh3z1bx4hyz87gbmda1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{ "message": "Pickup location deleted" }
```

**Error responses:**
- `401` — invalid token
- `404` — not found

---

## Shipping Fees

Shipping fee configurations define cost rules for delivery methods. Currently only `fixed` type is supported. Like pickup locations, shipping fees can be linked to warehouses.

### `GET` `/2023-11/shipping-fees` · `GET` `/2023-11/admin/shipping_fees`
List shipping fees. Both paths resolve to the same controller method; the public route requires only `X-Company`.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |
| `filter[search]` | string | No | — | Partial match on `name` |
| `filter[active]` | boolean | No | — | Filter by active flag |
| `filter[created_at][start]` | date | No | — | Created-at lower bound |
| `filter[created_at][end]` | date | No | — | Created-at upper bound |
| `filter[updated_at][start]` | date | No | — | Updated-at lower bound |
| `filter[updated_at][end]` | date | No | — | Updated-at upper bound |
| `sort` | string | No | — | `created_at`, `updated_at`, `name`, `orders_count` |
| `include` | string | No | — | `orders` |

```http
GET /2023-11/admin/shipping_fees?filter[active]=true HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "data": [
    {
      "id": "01hkxbwgh3z1bx4hyz87gbmds1",
      "name": "Standard Delivery",
      "type": "fixed",
      "amount": 1500.00,
      "active": true,
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shop_id": null,
      "metadata": null,
      "shop": null,
      "warehouses": [],
      "warehouse_ids": [],
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
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

### `POST` `/2023-11/admin/shipping_fees`
Create a new shipping fee configuration.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | max:255, unique per company | Fee name |
| `type` | string | Yes | `fixed` | Fee type (currently only `fixed`) |
| `amount` | number | No | — | Fee amount |
| `metadata` | object | No | — | Arbitrary data |
| `company_id` / `companyId` | ULID | No | — | Defaults to auth company |
| `shop_id` / `shopId` | ULID | No | — | Scope to a specific shop |
| `warehouse_ids` | ULID[] | No | exists:warehouses | Link to warehouses |

```http
POST /2023-11/admin/shipping_fees HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Express Delivery",
  "type": "fixed",
  "amount": 3000,
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"]
}
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmds2",
  "name": "Express Delivery",
  "type": "fixed",
  "amount": 3000.00,
  "active": true,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "metadata": null,
  "shop": null,
  "warehouses": [{ "id": "01hkxbwgh3z1bx4hyz87gbmdw1", "name": "Main Warehouse" }],
  "warehouse_ids": ["01hkxbwgh3z1bx4hyz87gbmdw1"],
  "created_at": "2024-06-01T10:00:00.000000Z",
  "updated_at": "2024-06-01T10:00:00.000000Z"
}
```

**Error responses:**
- `400` — name not unique within company, or invalid `type` value
- `401` — invalid token

---

### `GET` `/2023-11/admin/shipping_fees/{shippingFee}`
Get a single shipping fee.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `shippingFee` | ULID | Shipping fee ID |

```http
GET /2023-11/admin/shipping_fees/01hkxbwgh3z1bx4hyz87gbmds1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmds1",
  "name": "Standard Delivery",
  "type": "fixed",
  "amount": 1500.00,
  "active": true,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "metadata": null,
  "shop": null,
  "warehouses": [],
  "warehouse_ids": [],
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z"
}
```

**Error responses:**
- `401` — invalid token
- `404` — not found

---

### `PUT` `/2023-11/admin/shipping_fees/{shippingFee}`
Update a shipping fee.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `shippingFee` | ULID | Shipping fee ID |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:255, unique per company | Fee name |
| `type` | string | No | `fixed` | Fee type (currently only `fixed`) |
| `amount` | number | No | — | Fee amount |
| `active` | boolean | No | — | Whether the fee is active |
| `metadata` | object | No | — | Arbitrary data |
| `company_id` / `companyId` | ULID | No | — | Defaults to auth company |
| `shop_id` / `shopId` | ULID | No | — | Scope to a specific shop |
| `warehouse_ids` | ULID[] | No | exists:warehouses | Link to warehouses |

```http
PUT /2023-11/admin/shipping_fees/01hkxbwgh3z1bx4hyz87gbmds1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "name": "Express Delivery",
  "type": "fixed",
  "amount": 3500,
  "active": true
}
```

**200 OK**
```json
{
  "id": "01hkxbwgh3z1bx4hyz87gbmds1",
  "name": "Express Delivery",
  "type": "fixed",
  "amount": 3500.00,
  "active": true,
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "metadata": null,
  "shop": null,
  "warehouses": [],
  "warehouse_ids": [],
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-06-15T11:00:00.000000Z"
}
```

**Error responses:**
- `400` — name conflict
- `401` — invalid token
- `404` — not found

---

### `DELETE` `/2023-11/admin/shipping_fees/{shippingFee}`
Soft-delete a shipping fee. Pass `force=true` to hard-delete.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `shippingFee` | ULID | Shipping fee ID |

- **Query parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `force` | boolean | No | Hard-delete when `true` |

```http
DELETE /2023-11/admin/shipping_fees/01hkxbwgh3z1bx4hyz87gbmds1 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <plainTextToken>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**200 OK**
```json
{ "message": "Shipping Fee deleted" }
```

**Error responses:**
- `401` — invalid token
- `404` — not found
