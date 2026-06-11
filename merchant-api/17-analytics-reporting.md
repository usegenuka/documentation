---
title: "Analytics & Reporting"
description: "The Analytics & Reporting domain provides time-series metrics, KPI snapshots, and structured reports for a company's sales, expenses, orders, customers, WhatsAp"
sidebarTitle: "Analytics & Reporting"
---

The Analytics & Reporting domain provides time-series metrics, KPI snapshots, and structured reports for a company's sales, expenses, orders, customers, WhatsApp campaigns, and supplier/user performance. Endpoints are read-only aggregations; they do not mutate business data. The **Wrapped** sub-group produces annual "Year in Review" metrics. The **Reports** sub-group generates, stores, and exports structured multi-section report documents. The **Recap** sub-group manages auto-generated monthly recap cards shared publicly.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/analytics/accounting/stats` | Yearly accounting stats (sales, expenses, net profit) |
| GET | `/2023-11/admin/analytics/accounting/stats/expenses` | Expenses time-series for a period |
| GET | `/2023-11/admin/analytics/accounting/stats/revenues` | Revenue time-series for a period |
| GET | `/2023-11/admin/analytics/accounting/stats/revenues/breakdown` | Revenue breakdown by adjustment type |
| GET | `/2023-11/admin/analytics/accounting/stats/revenues/settings` | Revenue calculation settings for the company |
| GET | `/2023-11/admin/analytics/avg-bill-amount` | Average bill (expense) amount time-series |
| GET | `/2023-11/admin/analytics/avg-order-amount` | Average order value time-series |
| GET | `/2023-11/admin/analytics/discounts/stats` | Discount usage stats time-series |
| GET | `/2023-11/admin/analytics/expenses-by-category` | Expenses grouped by category |
| GET | `/2023-11/admin/analytics/expenses-by-category-monthly` | Expenses by category with monthly breakdown matrix |
| GET | `/2023-11/admin/analytics/orders/stats` | Orders report: products sold, total sales, profit |
| GET | `/2023-11/admin/analytics/payments/stats` | Payments report: in, out, balance |
| GET | `/2023-11/admin/analytics/recurring-customers` | Recurring customer rate |
| GET | `/2023-11/admin/analytics/stats` | Monthly stats overview (sales, orders, customers) |
| GET | `/2023-11/admin/analytics/stats/orders/group-by` | Orders totals grouped by a chosen field |
| GET | `/2023-11/admin/analytics/suppliers/stats/daily-sales` | Supplier daily sales revenue time-series |
| GET | `/2023-11/admin/analytics/suppliers/stats/daily-volume` | Supplier daily sales volume time-series |
| GET | `/2023-11/admin/analytics/suppliers/stats/market-share` | Market share by supplier |
| GET | `/2023-11/admin/analytics/suppliers/stats/profitability-share` | Profitability share by supplier |
| GET | `/2023-11/admin/analytics/suppliers/stats/top-products` | Top selling products per supplier |
| GET | `/2023-11/admin/analytics/suppliers/stats/top-suppliers-by-revenue` | Best suppliers ranked by revenue |
| GET | `/2023-11/admin/analytics/top-customers` | Top customers by spend or order count |
| GET | `/2023-11/admin/analytics/top-products` | Top products by quantity or revenue |
| GET | `/2023-11/admin/analytics/top-suppliers-expenses` | Top suppliers by total expense amount |
| GET | `/2023-11/admin/analytics/total-bills` | Total bills (expenses) count time-series |
| GET | `/2023-11/admin/analytics/total-expenses` | Total expenses amount time-series |
| GET | `/2023-11/admin/analytics/total-expenses-by-shop` | Total expenses grouped by shop |
| GET | `/2023-11/admin/analytics/total-orders` | Total orders count time-series |
| GET | `/2023-11/admin/analytics/total-sales` | Total sales amount time-series |
| GET | `/2023-11/admin/analytics/total-sales-by-shop` | Total sales grouped by shop |
| GET | `/2023-11/admin/analytics/users/stats/daily-sales` | User daily sales revenue time-series |
| GET | `/2023-11/admin/analytics/users/stats/daily-volume` | User daily sales volume time-series |
| GET | `/2023-11/admin/analytics/users/stats/market-share` | Market share by user |
| GET | `/2023-11/admin/analytics/users/stats/profitability-share` | Profitability share by user |
| GET | `/2023-11/admin/analytics/users/stats/top-products` | Top selling products per user |
| GET | `/2023-11/admin/analytics/users/stats/top-users-by-revenue` | Best users ranked by revenue |
| GET | `/2023-11/admin/analytics/whatsapp/stats` | WhatsApp campaign usage stats |
| GET | `/2023-11/admin/analytics/whatsapp/templates-stats` | WhatsApp template approval stats |
| GET | `/2023-11/admin/analytics/whatsapp/top-customers` | Top customers attributed to WhatsApp campaigns |
| POST | `/2023-11/admin/reports/reports` | Generate and save a report |
| GET | `/2023-11/admin/reports/reports` | List saved reports for a company |
| POST | `/2023-11/admin/reports/reports/preview` | Preview a report without saving |
| GET | `/2023-11/admin/reports/reports/{id}` | Get a specific saved report |
| DELETE | `/2023-11/admin/reports/reports/{id}` | Delete a saved report |
| GET | `/2023-11/admin/reports/reports/{id}/pdf` | Download a report as PDF |
| GET | `/2023-11/admin/reports/reports/{id}/whatsapp` | Get WhatsApp-formatted text for a report |
| POST | `/2023-11/reports/whatsapp/response` | Handle WhatsApp button reply for a report (webhook) |
| GET | `/2023-11/reports/{companyId}/{reportId}/view` | View a report PDF via public tokenized URL |
| GET | `/2023-11/recap/latest` | Get the latest monthly recap for a company |
| GET | `/2023-11/recap/{recap}` | Get a specific recap by ID or slug |
| POST | `/2023-11/recap/{recap}/feedback` | Submit feedback rating for a recap |
| POST | `/2023-11/recap/{recap}/track-share` | Increment recap share count |
| POST | `/2023-11/recap/{recap}/track-view` | Increment recap view count |
| GET | `/2023-11/admin/wrapped/annual-revenue` | Annual revenue summary |
| GET | `/2023-11/admin/wrapped/average-basket` | Average basket (order value) for the year |
| GET | `/2023-11/admin/wrapped/average-customer-basket` | Average basket per customer for the year |
| GET | `/2023-11/admin/wrapped/badges/earned` | Earned gamification badges |
| GET | `/2023-11/admin/wrapped/badges/evaluate` | Evaluate all badges for the company |
| GET | `/2023-11/admin/wrapped/badges/progress` | Badge progress and missed badges |
| GET | `/2023-11/admin/wrapped/best-monthly-revenue` | Best revenue month of the year |
| GET | `/2023-11/admin/wrapped/cash-vs-credit-sales` | Cash vs credit sales breakdown |
| GET | `/2023-11/admin/wrapped/genuka-management-score` | Genuka management score (0–100) |
| GET | `/2023-11/admin/wrapped/inactive-customers` | Customers who ordered this year but not in last 90 days |
| GET | `/2023-11/admin/wrapped/monthly-average-revenue` | Monthly average revenue for the year |
| GET | `/2023-11/admin/wrapped/monthly-sales-evolution` | Month-by-month sales evolution |
| GET | `/2023-11/admin/wrapped/most-productive-day` | Single most productive trading day |
| GET | `/2023-11/admin/wrapped/most-profitable-product` | Most profitable product by margin |
| GET | `/2023-11/admin/wrapped/most-sold-products` | Most sold products by quantity |
| GET | `/2023-11/admin/wrapped/never-sold-products` | Products with zero sales in the year |
| GET | `/2023-11/admin/wrapped/organization-stats` | Organization & discipline statistics |
| GET | `/2023-11/admin/wrapped/regular-customers` | Regular customers (more than 1 order) |
| GET | `/2023-11/admin/wrapped/top-expenses` | Top 3 largest expense bills |
| GET | `/2023-11/admin/wrapped/top-products` | Top 5 products by revenue |
| GET | `/2023-11/admin/wrapped/total-expenses` | Total expenses amount for the year |
| GET | `/2023-11/local-test/broadcast` | Local test: broadcast event (dev only) |
| GET | `/2023-11/local-test/broadcast/{orderId}` | Local test: broadcast order event (dev only) |
| GET | `/2023-11/local-test/clean` | Local test: clean duplicate products/addresses (dev only) |
| GET | `/2023-11/local-test/clean-accounting-codes` | Local test: clean accounting codes (dev only) |
| POST | `/2023-11/local-test/meets` | Local test: create meets (dev only) |
| GET | `/2023-11/local-test/merge-variants` | Local test: merge product variants (dev only) |
| GET | `/2023-11/local-test/notify` | Local test: send notification (dev only) |
| GET | `/2023-11/local-test/redis-test` | Local test: Redis connection test (dev only) |
| GET | `/2023-11/local-test/sync/permissions` | Local test: sync permissions (dev only) |
| GET | `/2023-11/local-test/test-mail` | Local test: send test email (dev only) |
| GET | `/2023-11/local-test/{company}/orders/email` | Local test: check order emails (dev only) |

---

## Dashboard Analytics

### Common query parameters

Most analytics endpoints accept the following date and filter parameters:

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start. Also accepted as `startDate`. ISO 8601 or `YYYY-MM-DD`. |
| `end_date` | string (date) | No | End of current month | Period end. Also accepted as `endDate`. |
| `compare_start_date` | string (date) | No | Start of previous month | Comparison period start. Also accepted as `compareStartDate` or `pastStartDate`. |
| `compare_end_date` | string (date) | No | End of previous month | Comparison period end. Also accepted as `compareEndDate` or `pastEndDate`. |
| `revenueType` | string | No | All types | Comma-separated revenue type handles to filter by. |
| `shopId` | string | No | All accessible shops | Comma-separated shop IDs to filter by. Can also be sent as `X-ShopId` or `X-Shop` header. |
| `paid_only` | string | No | `false` | Set to `"true"` to count only paid orders. |

---

### `GET` `/2023-11/admin/analytics/accounting/stats`

Yearly accounting stats comparing current year to previous year: total sales, total expenses, and net profit as daily arrays.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string (date) | No | Jan 1 of current year | Period start |
| `endDate` | string (date) | No | Dec 31 of current year | Period end |
| `pastStartDate` | string (date) | No | Jan 1 of previous year | Comparison period start |
| `pastEndDate` | string (date) | No | Dec 31 of previous year | Comparison period end |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |
| `paid_only` | string | No | `false` | `"true"` for paid orders only |

```http
GET /2023-11/admin/analytics/accounting/stats?startDate=2024-01-01&endDate=2024-12-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Total Sales",
    "data": [0, 15000, 32000, 0, 0],
    "percentage": 12.5,
    "previous": 280000,
    "total": 315000
  },
  {
    "label": "Total Expenses",
    "data": [0, 5000, 10000, 0, 0],
    "percentage": -3.2,
    "previous": 95000,
    "total": 92000
  },
  {
    "label": "Net Profit",
    "data": [0, 10000, 22000, 0, 0],
    "percentage": 8.3,
    "previous": 185000
  }
]
```

**Error responses**
- `401` — Missing or invalid token.
- `403` — Authenticated user does not belong to the requested company.

---

### `GET` `/2023-11/admin/analytics/accounting/stats/expenses`

Expenses time-series for a period. Supports optional `group_by` to aggregate by a bill column (e.g. `expense_type`).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string (date) | No | Jan 1 of current year | Period start |
| `endDate` | string (date) | No | Dec 31 of current year | Period end |
| `group_by` | string | No | — | Group results by a bill column (e.g. `expense_type`). When set, returns aggregated totals instead of daily array. |

```http
GET /2023-11/admin/analytics/accounting/stats/expenses?startDate=2024-01-01&endDate=2024-12-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — without `group_by`:
```json
{
  "data": [0, 5000, 10000, 8000, 0],
  "total": 23000
}
```

**Success response** (`200 OK`) — with `group_by=expense_type`:
```json
[
  { "expense_type": "rent", "total_amount": 120000, "label": "Rent" },
  { "expense_type": "salaries", "total_amount": 450000, "label": "Salaries" }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/accounting/stats/revenues`

Revenue time-series for a period. Supports `group_by` to aggregate by an order column (e.g. `revenue_type`).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string (date) | No | Jan 1 of current year | Period start |
| `endDate` | string (date) | No | Dec 31 of current year | Period end |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |
| `paid_only` | string | No | `false` | `"true"` for paid orders only |
| `group_by` | string | No | — | Group results by an order column (e.g. `revenue_type`). When set, returns aggregated totals. |

```http
GET /2023-11/admin/analytics/accounting/stats/revenues?startDate=2024-01-01&endDate=2024-12-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — without `group_by`:
```json
{
  "data": [0, 15000, 32000, 0, 0],
  "total": 315000
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/accounting/stats/revenues/breakdown`

Revenue breakdown by adjustment type (e.g. shipping fees, discounts, taxes) for a period.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | string (date) | No | Jan 1 of current year | Period start |
| `endDate` | string (date) | No | Dec 31 of current year | Period end |
| `paid_only` | string | No | `false` | `"true"` for paid orders only |
| `shopId` | string | No | All accessible | Comma-separated shop IDs |

```http
GET /2023-11/admin/analytics/accounting/stats/revenues/breakdown?startDate=2024-01-01&endDate=2024-12-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "gross_revenue": 315000,
  "net_revenue": 298000,
  "adjustments": [
    { "type": "shipping", "label": "Shipping Fees", "total": 8000 },
    { "type": "discount", "label": "Discounts", "total": -5000 },
    { "type": "tax", "label": "Taxes", "total": -20000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/accounting/stats/revenues/settings`

Returns the revenue calculation settings (adjustment types and their configuration) for the company.

```http
GET /2023-11/admin/analytics/accounting/stats/revenues/settings HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "adjustments": [
    { "type": "shipping", "label": "Shipping", "included": true },
    { "type": "discount", "label": "Discounts", "included": false }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/avg-bill-amount`

Average bill (expense) amount per day for the period, compared to a previous period.

**Query parameters** — see [Common query parameters](#common-query-parameters). Also accepts `expenseType` (comma-separated expense type handles).

```http
GET /2023-11/admin/analytics/avg-bill-amount?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Average Bill Amount",
  "percentage": 5.2,
  "data": [0, 12500, 8000, 15000],
  "dataCount": 11833,
  "compare": 9500,
  "compareCount": [0, 9000, 10000, 9500]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/avg-order-amount`

Average order value per day, compared to the previous period.

**Query parameters** — see [Common query parameters](#common-query-parameters).

```http
GET /2023-11/admin/analytics/avg-order-amount?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Average Order Value",
  "percentage": 8.4,
  "data": [0, 25000, 18000, 30000],
  "dataCount": 24333,
  "compare": 22500,
  "compareCount": [0, 20000, 25000, 22500]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/discounts/stats`

Discount usage statistics for a period: total discounts used (count), total amount applied, and proportion of discounted sales.

**Query parameters** — see [Common query parameters](#common-query-parameters).

```http
GET /2023-11/admin/analytics/discounts/stats?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Total discounts used",
    "data": [0, 5, 3, 8],
    "percentage": 15.0,
    "previous": 14,
    "total": 16,
    "type": "number"
  },
  {
    "label": "Total amount applied",
    "data": [0, 2500, 1500, 4000],
    "percentage": 20.0,
    "previous": 6500,
    "total": 8000,
    "type": "currency"
  },
  {
    "label": "Proportion of sales with discounts",
    "data": [0, 25.0, 15.0, 30.0],
    "percentage": 5.0,
    "previous": 20.0,
    "total": 23.3,
    "type": "percentage"
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/expenses-by-category`

Total expenses grouped by expense category/type for the period.

**Query parameters** — see [Common query parameters](#common-query-parameters) (date params only; no `revenueType`).

```http
GET /2023-11/admin/analytics/expenses-by-category?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Expenses By Category",
  "data": [
    { "expense_type": "rent", "total": 120000, "label": "Rent" },
    { "expense_type": "salaries", "total": 450000, "label": "Salaries" }
  ],
  "total": 570000
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/expenses-by-category-monthly`

Expenses by category with a monthly breakdown matrix. Returns categories as rows and months as columns, along with month totals.

**Query parameters** — see [Common query parameters](#common-query-parameters) (date params only).

```http
GET /2023-11/admin/analytics/expenses-by-category-monthly?start_date=2024-01-01&end_date=2024-06-30 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Expenses By Category Monthly",
  "data": [
    {
      "expense_type": "salaries",
      "label": "Salaries",
      "months": { "2024-01": 150000, "2024-02": 150000, "2024-03": 150000 },
      "total": 450000
    },
    {
      "expense_type": "rent",
      "label": "Rent",
      "months": { "2024-01": 40000, "2024-02": 40000, "2024-03": 40000 },
      "total": 120000
    }
  ],
  "months": ["2024-01", "2024-02", "2024-03"],
  "monthTotals": { "2024-01": 190000, "2024-02": 190000, "2024-03": 190000 },
  "grandTotal": 570000
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/orders/stats`

Orders report for a period: products sold, total sales, and profit — each with a daily data array and period-over-period percentage change.

**Query parameters** — see [Common query parameters](#common-query-parameters).

```http
GET /2023-11/admin/analytics/orders/stats?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Products sold",
    "data": [0, 10, 25, 15],
    "percentage": 8.5,
    "previous": 46,
    "total": 50,
    "type": "number"
  },
  {
    "label": "Total sales",
    "data": [0, 50000, 120000, 75000],
    "percentage": 12.0,
    "previous": 215000,
    "total": 245000,
    "type": "currency"
  },
  {
    "label": "Profit",
    "data": [0, 20000, 48000, 30000],
    "percentage": 9.0,
    "previous": 90000,
    "total": 98000,
    "type": "currency"
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/payments/stats`

Payments report: inbound payments, outbound payments, and balance — each as daily arrays with period-over-period percentage change.

**Query parameters** — see [Common query parameters](#common-query-parameters) (date params only).

```http
GET /2023-11/admin/analytics/payments/stats?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Payments in",
    "data": [0, 80000, 120000, 60000],
    "percentage": 10.0,
    "previous": 240000,
    "total": 260000,
    "type": "currency"
  },
  {
    "label": "Payments out",
    "data": [0, 30000, 40000, 20000],
    "percentage": -5.0,
    "previous": 95000,
    "total": 90000,
    "type": "currency"
  },
  {
    "label": "Balance",
    "data": [0, 50000, 80000, 40000],
    "percentage": 18.0,
    "previous": 145000,
    "total": 170000,
    "type": "currency"
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/recurring-customers`

Recurring customer rate for a period: customers with exactly 1 order vs. more than 1 order, daily.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |

```http
GET /2023-11/admin/analytics/recurring-customers?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Recurring Customer Rate",
  "percentage": 32.5,
  "totalCustomerWithMoreThanOneOrder": 13,
  "totalCustomer": 40,
  "data": {
    "customersWithOneOrder": [0, 2, 5, 3],
    "customersWithMoreThanOneOrder": [0, 1, 2, 1]
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.
- `422` — `end_date` is before `start_date`.

---

### `GET` `/2023-11/admin/analytics/stats`

Monthly stats overview: total sales, new orders, and new customers — each with a daily array and period comparison.

**Query parameters** — see [Common query parameters](#common-query-parameters).

```http
GET /2023-11/admin/analytics/stats?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Total sales",
    "data": [0, 50000, 120000, 75000],
    "percentage": 12.0,
    "previous": 215000
  },
  {
    "label": "New orders",
    "data": [0, 5, 12, 8],
    "percentage": 8.0,
    "previous": 23
  },
  {
    "label": "New customers",
    "data": [0, 3, 7, 4],
    "percentage": 16.7,
    "previous": 12
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/stats/orders/group-by`

Total orders revenue grouped by any order column (e.g. `state`, `shop_id`, `revenue_type`).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `field` | string | Yes | — | Order column to group by (e.g. `state`, `shop_id`, `revenue_type`) |
| `startDate` | string (date) | No | Start of current month | Period start |
| `endDate` | string (date) | No | End of current month | Period end |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |

```http
GET /2023-11/admin/analytics/stats/orders/group-by?field=state&startDate=2024-01-01&endDate=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  { "state": "invoice", "total": 280000 },
  { "state": "quotation", "total": 45000 }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/top-customers`

Top customers ranked by total spend (default) or order count.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `limit` | integer | No | `5` | Number of customers to return |
| `per_orders_count` | any | No | — | When present, rank by order count instead of spend |
| `shopId` | string | No | — | Filter by shop ID |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |

```http
GET /2023-11/admin/analytics/top-customers?start_date=2024-01-01&end_date=2024-01-31&limit=5 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — array of `CustomerResource`:
```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "first_name": "Alice",
    "last_name": "Mballa",
    "email": "alice@example.com",
    "phone": "+237600000001",
    "orders_count": 12,
    "orders_sum_amount": 350000
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/top-products`

Top products ranked by quantity sold (default) or total revenue.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `limit` | integer | No | `5` | Number of products to return |
| `per_sale` | any | No | — | When present, rank by revenue instead of quantity |
| `shopId` | string | No | — | Filter by shop ID |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |

```http
GET /2023-11/admin/analytics/top-products?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "title": "Premium Widget",
    "quantity_sum": 150,
    "total_ordered_amount": 750000,
    "medias": []
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/top-suppliers-expenses`

Top suppliers ranked by total expense amount (bills) or bill count.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `limit` | integer | No | `5` | Number of suppliers to return |
| `per_bills_count` | any | No | — | When present, rank by bill count instead of amount |
| `expenseType` | string | No | All types | Comma-separated expense type handles |

```http
GET /2023-11/admin/analytics/top-suppliers-expenses?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "first_name": "Jean",
    "last_name": "Dupont",
    "company_name": "Fournitures Pro",
    "email": "jean@fournitures.com",
    "phone": "+33600000001",
    "bills_count": 8,
    "bills_sum_amount": 480000,
    "medias": []
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/total-bills`

Total number of bills (expenses) per day for the period, compared to a previous period.

**Query parameters** — see [Common query parameters](#common-query-parameters). Also accepts `expenseType` (comma-separated).

```http
GET /2023-11/admin/analytics/total-bills?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Total Bills",
  "percentage": 5.0,
  "data": [0, 2, 4, 3],
  "dataCount": 9,
  "compare": 8,
  "compareCount": [0, 2, 3, 3]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/total-expenses`

Total expenses amount per day for the period, compared to a previous period.

**Query parameters** — see [Common query parameters](#common-query-parameters). Also accepts `expenseType` (comma-separated).

```http
GET /2023-11/admin/analytics/total-expenses?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Total Expenses",
  "percentage": -3.5,
  "data": [0, 25000, 40000, 20000],
  "dataCount": 85000,
  "compare": 88000,
  "compareCount": [0, 28000, 42000, 18000]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/total-expenses-by-shop`

Total expenses grouped by shop for the period.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `expenseType` | string | No | All types | Comma-separated expense type handles |

```http
GET /2023-11/admin/analytics/total-expenses-by-shop?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Total Expenses By Shop",
  "data": [
    { "shop_id": "01hqydxwtxdj3kmzp3bz7jk73g", "total": 45000 },
    { "shop_id": "01hqydxwtxdj3kmzp3bz7jk73h", "total": 32000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/total-orders`

Total order count per day for the period, compared to a previous period.

**Query parameters** — see [Common query parameters](#common-query-parameters).

```http
GET /2023-11/admin/analytics/total-orders?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Total Orders",
  "percentage": 10.0,
  "data": [0, 5, 12, 8],
  "dataCount": 25,
  "compare": 22,
  "compareCount": [0, 4, 11, 7]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/total-sales`

Total sales revenue per day for the period, compared to a previous period.

**Query parameters** — see [Common query parameters](#common-query-parameters).

```http
GET /2023-11/admin/analytics/total-sales?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Total Sales",
  "percentage": 12.5,
  "data": [0, 50000, 120000, 75000],
  "dataCount": 245000,
  "compare": 215000,
  "compareCount": [0, 45000, 110000, 60000]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/total-sales-by-shop`

Total sales revenue grouped by shop for the period.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `revenueType` | string | No | All types | Comma-separated revenue type handles |

```http
GET /2023-11/admin/analytics/total-sales-by-shop?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "label": "Total Sales By Shop",
  "data": [
    { "shop_id": "01hqydxwtxdj3kmzp3bz7jk73g", "total": 180000 },
    { "shop_id": "01hqydxwtxdj3kmzp3bz7jk73h", "total": 65000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

## Supplier Analytics

All supplier analytics endpoints share the same date and optional `supplier_id` / `limit` query parameters.

**Common query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `supplier_id` | string (ULID) | No | — | Filter to a specific supplier |
| `limit` | integer | No | — | Limit number of results |

### `GET` `/2023-11/admin/analytics/suppliers/stats/daily-sales`

Daily sales revenue attributed to suppliers for the period.

```http
GET /2023-11/admin/analytics/suppliers/stats/daily-sales?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [0, 25000, 48000, 30000],
  "label": "Total Sales",
  "dataCount": 103000
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/suppliers/stats/daily-volume`

Daily sales volume (units) attributed to suppliers for the period.

```http
GET /2023-11/admin/analytics/suppliers/stats/daily-volume?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [0, 10, 25, 15],
  "label": "Sales Volume",
  "dataCount": 50
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/suppliers/stats/market-share`

Market share (by revenue) for each supplier in the period.

```http
GET /2023-11/admin/analytics/suppliers/stats/market-share?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "supplier_id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Fournitures Pro", "revenue": 280000, "share": 54.5 },
    { "supplier_id": "01hqydxwtxdj3kmzp3bz7jk73h", "name": "Global Supply", "revenue": 120000, "share": 23.3 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/suppliers/stats/profitability-share`

Profitability share (by margin) for each supplier in the period.

```http
GET /2023-11/admin/analytics/suppliers/stats/profitability-share?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "supplier_id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Fournitures Pro", "profit": 95000, "share": 62.0 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/suppliers/stats/top-products`

Top selling products linked to a supplier for the period.

```http
GET /2023-11/admin/analytics/suppliers/stats/top-products?start_date=2024-01-01&end_date=2024-01-31&supplier_id=01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "product_id": "01hqydxwtxdj3kmzp3bz7jk73g", "title": "Widget A", "quantity_sum": 120, "revenue": 360000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/suppliers/stats/top-suppliers-by-revenue`

Best suppliers ranked by total revenue generated for the period.

```http
GET /2023-11/admin/analytics/suppliers/stats/top-suppliers-by-revenue?start_date=2024-01-01&end_date=2024-01-31&limit=5 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "supplier_id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Fournitures Pro", "revenue": 280000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

## User (Sales Rep) Analytics

All user analytics endpoints share the same common query parameters as supplier analytics, with `user_id` instead of `supplier_id`.

**Common query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | string (date) | No | Start of current month | Period start |
| `end_date` | string (date) | No | End of current month | Period end |
| `user_id` | string (ULID) | No | — | Filter to a specific user |
| `limit` | integer | No | — | Limit number of results |

### `GET` `/2023-11/admin/analytics/users/stats/daily-sales`

Daily sales revenue attributed to users (sales reps) for the period.

```http
GET /2023-11/admin/analytics/users/stats/daily-sales?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [0, 30000, 55000, 25000],
  "label": "Total Sales",
  "dataCount": 110000
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/users/stats/daily-volume`

Daily sales volume (units) attributed to users for the period.

```http
GET /2023-11/admin/analytics/users/stats/daily-volume?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [0, 12, 28, 18],
  "label": "Sales Volume",
  "dataCount": 58
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/users/stats/market-share`

Market share (by revenue) for each user in the period.

```http
GET /2023-11/admin/analytics/users/stats/market-share?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "user_id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "John Doe", "revenue": 150000, "share": 58.0 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/users/stats/profitability-share`

Profitability share by user for the period.

```http
GET /2023-11/admin/analytics/users/stats/profitability-share?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "user_id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "John Doe", "profit": 60000, "share": 65.0 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/users/stats/top-products`

Top selling products attributed to a user for the period.

```http
GET /2023-11/admin/analytics/users/stats/top-products?start_date=2024-01-01&end_date=2024-01-31&user_id=01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "product_id": "01hqydxwtxdj3kmzp3bz7jk73g", "title": "Widget A", "quantity_sum": 80, "revenue": 240000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/users/stats/top-users-by-revenue`

Best users ranked by revenue generated for the period.

```http
GET /2023-11/admin/analytics/users/stats/top-users-by-revenue?start_date=2024-01-01&end_date=2024-01-31&limit=5 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "status": "success",
  "data": [
    { "user_id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "John Doe", "revenue": 150000 }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

## WhatsApp Analytics

### `GET` `/2023-11/admin/analytics/whatsapp/stats`

WhatsApp campaign usage: messages sent (with delivery rate), messages read (with read rate), and attributed revenue for the period.

**Query parameters** — see [Common query parameters](#common-query-parameters) (date params only).

```http
GET /2023-11/admin/analytics/whatsapp/stats?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Messages sent",
    "data": [0, 150, 220, 180],
    "percentage": 15.0,
    "previous": 480,
    "total": 550,
    "type": "number",
    "subtitle": "88.0% delivery rate"
  },
  {
    "label": "Messages read",
    "data": [0, 90, 140, 110],
    "percentage": 12.0,
    "previous": 300,
    "total": 340,
    "type": "number",
    "subtitle": "70.4% read rate"
  },
  {
    "label": "WhatsApp revenue",
    "data": [0, 30000, 55000, 40000],
    "percentage": 18.0,
    "previous": 105000,
    "total": 125000,
    "type": "currency"
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/whatsapp/templates-stats`

WhatsApp template approval statistics: approved, pending, and rejected templates.

**Query parameters** — see [Common query parameters](#common-query-parameters) (date params only).

```http
GET /2023-11/admin/analytics/whatsapp/templates-stats?start_date=2024-01-01&end_date=2024-01-31 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "label": "Approved templates",
    "data": [0, 2, 1, 3],
    "percentage": 20.0,
    "previous": 5,
    "total": 12,
    "type": "number"
  },
  {
    "label": "Pending templates",
    "data": [0, 1, 0, 1],
    "percentage": 0.0,
    "previous": 2,
    "total": 2,
    "type": "number"
  },
  {
    "label": "Rejected templates",
    "data": [0, 0, 1, 0],
    "percentage": -50.0,
    "previous": 2,
    "total": 1,
    "type": "number"
  }
]
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/analytics/whatsapp/top-customers`

Top customers attributed to WhatsApp campaigns (orders placed within 7 days of receiving a campaign message).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `limit` | integer | No | `5` | Number of customers to return |

```http
GET /2023-11/admin/analytics/whatsapp/top-customers?limit=5 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "first_name": "Alice",
      "last_name": "Mballa",
      "company_name": null,
      "email": "alice@example.com",
      "phone": "+237600000001",
      "orders_count": 3,
      "total_spent": 85000
    }
  ]
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

## Reports

Reports are saved `ReportRun` records containing a structured data payload, optional PDF, and WhatsApp preview text.

### `POST` `/2023-11/admin/reports/reports`

Generate and save a report for a company. Optionally generates a PDF.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_id` | string (ULID) | Yes | `exists:companies,id` | Target company |
| `period.type` | string | Yes | `in:day,week,month,custom` | Period granularity |
| `period.start_date` | string (date) | Conditional | Required when `period.type=custom` | Start of custom period |
| `period.end_date` | string (date) | Conditional | Required when `period.type=custom`, after or equal to start | End of custom period |
| `period.date` | string (date) | Conditional | Required when `period.type=day` | The specific day |
| `generate_pdf` | boolean | No | — | Whether to generate a PDF (default `true`) |
| `config` | object | No | — | Optional report configuration overrides |

```http
POST /2023-11/admin/reports/reports HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "period": {
    "type": "month",
    "start_date": "2024-01-01",
    "end_date": "2024-01-31"
  },
  "generate_pdf": true,
  "config": {}
}
```

**Success response** (`201 Created`):
```json
{
  "message": "Rapport généré avec succès",
  "report": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "period_type": "month",
    "start_date": "2024-01-01T00:00:00.000000Z",
    "end_date": "2024-01-31T23:59:59.000000Z",
    "status": "completed",
    "data_quality": "ok",
    "pdf_path": "reports/rapport-01hqydxwtxdj3kmzp3bz7jk73g.pdf",
    "payload": { "..." : "..." },
    "created_at": "2024-02-01T08:00:00.000000Z",
    "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "My Store" }
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Authenticated user does not belong to the requested company.
- `404` — Company not found.
- `422` — Validation error (missing required fields).

---

### `GET` `/2023-11/admin/reports/reports`

List saved reports for a company, paginated and optionally filtered.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `company_id` | string (ULID) | Yes | — | Company to list reports for |
| `period_type` | string | No | — | Filter by period type: `day`, `week`, `month`, `custom` |
| `start_date` | string (date) | No | — | Filter reports whose `start_date` falls within this range |
| `end_date` | string (date) | No | — | Range end (requires `start_date`) |
| `per_page` | integer | No | `15` | Number of results per page (max 100) |

```http
GET /2023-11/admin/reports/reports?company_id=01hqydxwtxdj3kmzp3bz7jk73g&period_type=month&per_page=10 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — paginated wrapper:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "period_type": "month",
      "start_date": "2024-01-01T00:00:00.000000Z",
      "end_date": "2024-01-31T23:59:59.000000Z",
      "status": "completed",
      "data_quality": "ok",
      "created_at": "2024-02-01T08:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 10, "total": 3 }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.
- `422` — `company_id` missing or invalid.

---

### `POST` `/2023-11/admin/reports/reports/preview`

Preview a report without saving it to the database. Returns the full payload.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_id` | string (ULID) | Yes | `exists:companies,id` | Target company |
| `period.type` | string | Yes | `in:day,week,month,custom` | Period granularity |
| `period.start_date` | string (date) | Conditional | Required when `period.type=custom` | Start of custom period |
| `period.end_date` | string (date) | Conditional | Required when `period.type=custom`, after or equal to start | End of custom period |
| `period.date` | string (date) | Conditional | Required when `period.type=day` | The specific day |
| `config` | object | No | — | Optional report configuration overrides |

```http
POST /2023-11/admin/reports/reports/preview HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "period": {
    "type": "custom",
    "start_date": "2024-01-01",
    "end_date": "2024-01-31"
  }
}
```

**Success response** (`200 OK`):
```json
{
  "period": { "type": "custom", "label": "January 2024", "start_date": "2024-01-01", "end_date": "2024-01-31" },
  "kpis": { "total_revenue": 245000, "total_orders": 25, "total_customers": 18 },
  "whatsapp_preview": { "lines": ["*January 2024 Report*", "Revenue: 2 450.00", "Orders: 25"] }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.
- `404` — Company not found.
- `422` — Validation error.

---

### `GET` `/2023-11/admin/reports/reports/{id}`

Retrieve a specific saved report by ID.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `id` | string (ULID) | Report run ID |

```http
GET /2023-11/admin/reports/reports/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "period_type": "month",
  "start_date": "2024-01-01T00:00:00.000000Z",
  "end_date": "2024-01-31T23:59:59.000000Z",
  "status": "completed",
  "data_quality": "ok",
  "payload": { "..." : "..." },
  "pdf_path": "reports/rapport-01hqydxwtxdj3kmzp3bz7jk73g.pdf",
  "created_at": "2024-02-01T08:00:00.000000Z",
  "company": { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "My Store" }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized (report belongs to another company).
- `404` — Report not found.

---

### `DELETE` `/2023-11/admin/reports/reports/{id}`

Delete a saved report and its associated PDF file.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `id` | string (ULID) | Report run ID |

```http
DELETE /2023-11/admin/reports/reports/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "message": "Rapport supprimé avec succès" }
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.
- `404` — Report not found.

---

### `GET` `/2023-11/admin/reports/reports/{id}/pdf`

Download a report as a PDF file. Generates the PDF on the fly if it does not already exist.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `id` | string (ULID) | Report run ID |

```http
GET /2023-11/admin/reports/reports/01hqydxwtxdj3kmzp3bz7jk73g/pdf HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`): Binary PDF stream with `Content-Type: application/pdf` and `Content-Disposition: attachment`.

**Error responses**
- `400` — Report is not yet completed.
- `401` — Unauthenticated.
- `403` — Unauthorized.
- `404` — Report not found.

---

### `GET` `/2023-11/admin/reports/reports/{id}/whatsapp`

Get the WhatsApp-formatted text summary of a completed report.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `id` | string (ULID) | Report run ID |

```http
GET /2023-11/admin/reports/reports/01hqydxwtxdj3kmzp3bz7jk73g/whatsapp HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "text": "*January 2024 Report*\nRevenue: 2 450.00\nOrders: 25\nCustomers: 18",
  "lines": [
    "*January 2024 Report*",
    "Revenue: 2 450.00",
    "Orders: 25",
    "Customers: 18"
  ]
}
```

**Error responses**
- `400` — Report not completed or payload missing.
- `401` — Unauthenticated.
- `403` — Unauthorized.
- `404` — Report not found.

---

### `POST` `/2023-11/reports/whatsapp/response`

Webhook endpoint for handling WhatsApp button replies to report notifications. Called by the WhatsApp webhook forwarder. Sends a detailed report back via WhatsApp when the user responds "yes".

> This endpoint is intended for internal webhook use by the WhatsApp Business API, not for client-side calls.

**Request body** — standard WhatsApp Cloud API webhook payload (button reply). Sent by the WhatsApp Business Platform; no `Authorization` or `X-Company` header required.

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `entry` | array | Yes | Non-empty array | Top-level WhatsApp webhook entries |
| `entry[].changes` | array | Yes | Non-empty array | List of changes within an entry |
| `entry[].changes[].value.metadata.phone_number_id` | string | Yes | — | WhatsApp Business phone number ID that received the reply |
| `entry[].changes[].value.messages` | array | Yes | — | Array of incoming messages (only first element is processed) |
| `entry[].changes[].value.messages[].type` | string | Yes | Must be `"button"` | Message type. Non-button types are ignored with `status: ignored` |
| `entry[].changes[].value.messages[].from` | string | Yes | — | Sender's phone number (without leading `+`) |
| `entry[].changes[].value.messages[].button.payload` | string | Yes | Format: `report_(yes\|no)_{reportId}_{userId}` | Button reply payload identifying the action (yes/no), the report, and the user |

```json
{
  "entry": [{
    "changes": [{
      "value": {
        "metadata": { "phone_number_id": "12345" },
        "messages": [{
          "type": "button",
          "from": "237600000001",
          "button": { "payload": "report_yes_01hqydxwtxdj3kmzp3bz7jk73g_01hqydxwtxdj3kmzp3bz7jk73h" }
        }]
      }
    }]
  }]
}
```

**Success response** (`200 OK`):
```json
{ "status": "success", "action": "sent_detailed_report" }
```

**Error responses**
- `200` with `"status": "ignored"` — no messages, wrong message type, or invalid payload format.
- `200` with `"status": "error"` — report or user not found, phone mismatch.
- `500` — Internal error.

---

### `GET` `/2023-11/reports/{companyId}/{reportId}/view`

View a report as a PDF via a public, tokenized, non-indexable URL. No authentication header required; access is controlled by the `token` query parameter.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `companyId` | string (ULID) | Company ID |
| `reportId` | string (ULID) | Report run ID |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `token` | string | Yes | — | HMAC-SHA256 view token (generated by `ReportRun::generateViewToken()`) |

```http
GET /2023-11/reports/01hqydxwtxdj3kmzp3bz7jk73g/01hqydxwtxdj3kmzp3bz7jk73h/view?token=abc123def456 HTTP/1.1
Accept: application/pdf
```

**Success response** (`200 OK`): Inline PDF stream with `Content-Type: application/pdf`, `X-Robots-Tag: noindex`, `Cache-Control: no-cache`.

**Error responses**
- `403` — Token missing or invalid.
- `404` — Report not found, not completed, or company mismatch.

---

## Monthly Recap

Recaps are auto-generated monthly performance cards identified by ULID or slug. Some fields (revenue, basket) are hidden for non-owners.

### `GET` `/2023-11/recap/latest`

Get the latest recap for a company. The company must be passed via `X-Company` header or query parameter.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `companyId` | string (ULID) | No | From `X-Company` header | Target company ID |

```http
GET /2023-11/recap/latest HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — `RecapResource`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "slug": "my-store-january-2024",
  "is_owner": true,
  "store": {
    "name": "My Store",
    "slug": "my-store",
    "logo_url": "https://cdn.genuka.com/logos/mystore.png",
    "initials": "MS",
    "currency": "XAF"
  },
  "period": { "year": 2024, "month": 1, "label": "January 2024", "label_fr": "Janvier 2024" },
  "kpis": {
    "total_orders": 25,
    "total_revenue": 245000,
    "total_customers": 18,
    "average_basket": 9800,
    "trends": { "orders": 8.0, "revenue": 12.5, "customers": 16.7, "basket": 3.5 }
  },
  "charts": {
    "daily_orders": [0, 1, 2, 3],
    "weekly_orders": [5, 8, 7, 5]
  },
  "insights": {
    "best_day": "2024-01-15",
    "avg_daily_orders": 0.81,
    "weekly_growth": 4.5
  },
  "top_products": [
    { "rank": 1, "name": "Premium Widget", "units_sold": 15, "percentage": 30.0, "revenue": 75000 }
  ],
  "top_customers": [
    { "rank": 1, "name": "Alice Mballa", "initials": "AM", "orders_count": 5, "products_bought": 12, "total_spent": 49000, "since": "2023-05-01" }
  ],
  "customer_metrics": {
    "retention_rate": 45.0,
    "avg_orders_per_customer": 1.4,
    "avg_products_per_customer": 2.8,
    "new_customers": 6
  },
  "badges": {
    "earned": [{ "slug": "premiere-vente", "name": "First Sale", "icon": "🎉", "description": "Made your first sale!" }],
    "locked": [],
    "progress": {}
  },
  "headline": { "emoji": "🚀", "text": "Your best January yet!" },
  "ranking": { "revenue_percentile": 78.5 },
  "feedback": { "submitted": false, "rating": null, "text": null },
  "meta": {
    "view_count": 12,
    "shared_count": 3,
    "created_at": "2024-02-01T06:00:00.000000Z",
    "generated_at": "2024-02-01T06:00:00.000000Z",
    "share_url": "https://app.genuka.com/recap/my-store-january-2024",
    "og_title": "My Store had an incredible month 🚀",
    "og_description": "25 orders in January 2024 — Discover the recap. Powered by Genuka.",
    "og_image": "https://cdn.genuka.com/logos/mystore.png"
  }
}
```

**Error responses**
- `404` — No recap found for the company.
- `422` — No company ID provided.

---

### `GET` `/2023-11/recap/{recap}`

Get a specific recap by ULID or slug.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `recap` | string | Recap ULID or slug |

```http
GET /2023-11/recap/my-store-january-2024 HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`) — `RecapResource`. Revenue and basket fields (`kpis.total_revenue`, `kpis.average_basket`, `kpis.trends.revenue`, `kpis.trends.basket`) are `0` when the requester is not the company owner.

```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "slug": "my-store-january-2024",
  "is_owner": false,
  "store": {
    "name": "My Store",
    "slug": "my-store",
    "logo_url": "https://cdn.genuka.com/logos/mystore.png",
    "initials": "MS",
    "currency": "XAF"
  },
  "period": { "year": 2024, "month": 1, "label": "January 2024", "label_fr": "Janvier 2024" },
  "kpis": {
    "total_orders": 25,
    "total_revenue": 0,
    "total_customers": 18,
    "average_basket": 0,
    "trends": { "orders": 8.0, "revenue": 0, "customers": 16.7, "basket": 0 }
  },
  "charts": {
    "daily_orders": [0, 1, 2, 3],
    "weekly_orders": [5, 8, 7, 5]
  },
  "insights": {
    "best_day": "2024-01-15",
    "avg_daily_orders": 0.81,
    "weekly_growth": 4.5
  },
  "top_products": [
    { "rank": 1, "name": "Premium Widget", "units_sold": 15, "percentage": 30.0, "revenue": 75000 }
  ],
  "top_customers": [
    { "rank": 1, "name": "Alice Mballa", "initials": "AM", "orders_count": 5, "products_bought": 12, "total_spent": 49000, "since": "2023-05-01" }
  ],
  "customer_metrics": {
    "retention_rate": 45.0,
    "avg_orders_per_customer": 1.4,
    "avg_products_per_customer": 2.8,
    "new_customers": 6
  },
  "badges": {
    "earned": [{ "slug": "premiere-vente", "name": "First Sale", "icon": "🎉", "description": "Made your first sale!" }],
    "locked": [],
    "progress": {}
  },
  "headline": { "emoji": "🚀", "text": "Your best January yet!" },
  "ranking": { "revenue_percentile": 78.5 },
  "feedback": { "submitted": false, "rating": null, "text": null },
  "meta": {
    "view_count": 12,
    "shared_count": 3,
    "created_at": "2024-02-01T06:00:00.000000Z",
    "generated_at": "2024-02-01T06:00:00.000000Z",
    "share_url": "https://app.genuka.com/recap/my-store-january-2024",
    "og_title": "My Store had an incredible month 🚀",
    "og_description": "25 orders in January 2024 — Discover the recap. Powered by Genuka.",
    "og_image": "https://cdn.genuka.com/logos/mystore.png"
  }
}
```

**Error responses**
- `404` — `{ "message": "Recap not found." }`

---

### `POST` `/2023-11/recap/{recap}/feedback`

Submit feedback (rating + optional text) for a recap.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `recap` | string | Recap ULID or slug |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `rating` | string | Yes | `in:love,good,ok,improve,bad` | Feedback rating |
| `text` | string | No | `max:2000` | Optional feedback comment |

```http
POST /2023-11/recap/my-store-january-2024/feedback HTTP/1.1
Accept: application/json
Content-Type: application/json

{
  "rating": "love",
  "text": "Very insightful recap!"
}
```

**Success response** (`200 OK`):
```json
{ "success": true }
```

**Error responses**
- `404` — `{ "message": "Recap not found." }`
- `422` — `rating` is missing or invalid.

---

### `POST` `/2023-11/recap/{recap}/track-share`

Increment the share count for a recap (call when a user shares the recap link).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `recap` | string | Recap ULID or slug |

**Request body** — None. This endpoint accepts no body.

```http
POST /2023-11/recap/my-store-january-2024/track-share HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "success": true }
```

**Error responses**
- `404` — `{ "message": "Recap not found." }`

---

### `POST` `/2023-11/recap/{recap}/track-view`

Increment the view count for a recap (call on page load).

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `recap` | string | Recap ULID or slug |

**Request body** — None. This endpoint accepts no body.

```http
POST /2023-11/recap/my-store-january-2024/track-view HTTP/1.1
Accept: application/json
```

**Success response** (`200 OK`):
```json
{ "success": true }
```

**Error responses**
- `404` — `{ "message": "Recap not found." }`

---

## Year Wrapped

Annual "Year in Review" metrics for a company. All endpoints accept a `year` query parameter and require the `X-Company` header.

**Common query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `year` | integer | No | Current year | The year to compute metrics for (e.g. `2024`) |

---

### `GET` `/2023-11/admin/wrapped/annual-revenue`

Annual revenue summary with monthly average and best month.

```http
GET /2023-11/admin/wrapped/annual-revenue?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "annual_revenue": 3150000,
    "avg_monthly_revenue": 262500,
    "best_monthly_revenue": 420000,
    "ranking": { "percentile": 72, "label": "Top 28%" }
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/average-basket`

Average basket value (order amount) across all non-cancelled orders for the year.

```http
GET /2023-11/admin/wrapped/average-basket?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "average_basket": 12600,
    "total_orders": 250,
    "ranking": { "percentile": 65 }
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/average-customer-basket`

Average basket per customer (mean of per-customer average order value) for the year.

```http
GET /2023-11/admin/wrapped/average-customer-basket?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "average_customer_basket": 14200.50,
    "median_customer_basket": 11000.00,
    "total_customers_with_orders": 85
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `400` — `companyId` not provided.

---

### `GET` `/2023-11/admin/wrapped/badges/earned`

Earned gamification badges for the company in the given year, including counts by level.

**Query parameters** — `year` (see above).

```http
GET /2023-11/admin/wrapped/badges/earned?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "year": 2024,
    "total_badges_earned": 3,
    "earned_badges": [
      {
        "type": "revenue",
        "emoji": "🔥",
        "label": "Roi du chiffre",
        "level": "gold",
        "description": "Achieved gold revenue milestone",
        "progress": 100,
        "next_milestone": null
      }
    ],
    "gold_count": 1,
    "silver_count": 2,
    "bronze_count": 0
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.
- `403` — Company not eligible for badges.

---

### `GET` `/2023-11/admin/wrapped/badges/evaluate`

Evaluate and return all badge statuses for the company in the given year.

**Query parameters** — `year` (see above).

```http
GET /2023-11/admin/wrapped/badges/evaluate?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "year": 2024,
    "eligible": true,
    "earned_badges": [ { "type": "revenue", "level": "gold", "..." : "..." } ],
    "all_badges": { "revenue": { "..." : "..." }, "regularity": null }
  },
  "message": "Badges evaluated successfully"
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.
- `403` — Company not eligible.

---

### `GET` `/2023-11/admin/wrapped/badges/progress`

Badge progress report: which badges were missed and what the next targets are.

**Query parameters** — `year` (see above).

```http
GET /2023-11/admin/wrapped/badges/progress?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "year": 2024,
    "earned_badges_count": 3,
    "available_badges_count": 5,
    "earned_badge_types": ["revenue", "regularity", "loyalty"],
    "missed_badges": [
      { "type": "growth", "emoji": "🚀", "label": "Business en croissance", "next_milestone": "Poursuivez vos efforts pour débloquer ce badge!" }
    ],
    "next_targets": [
      { "type": "revenue", "emoji": "🔥", "current_level": "silver", "target": "Reach 1M in annual revenue for gold" }
    ]
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/best-monthly-revenue`

The best revenue month in the year.

```http
GET /2023-11/admin/wrapped/best-monthly-revenue?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "best_monthly_mevenue": {
      "month": 11,
      "total_amount": 420000
    }
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/cash-vs-credit-sales`

Breakdown of cash (fully paid) vs credit (unpaid or partially paid) orders for the year.

```http
GET /2023-11/admin/wrapped/cash-vs-credit-sales?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "total_orders": 250,
    "cash_sales": 180,
    "credit_sales": 55,
    "cash_percentage": 72.0,
    "credit_percentage": 22.0,
    "ranking": { "percentile": 80 }
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/genuka-management-score`

Genuka management score (0–100) computed from invoicing rate, expense tracking, stock management, regularity, and data discipline.

```http
GET /2023-11/admin/wrapped/genuka-management-score?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "genuka_management_score": 74,
    "breakdown": {
      "invoicing": { "score": 85.0, "weight": 30, "contribution": 25.5, "details": { "total_sales": 250, "invoiced_sales": 213, "rate": "85.20%" } },
      "expenses": { "score": 75.0, "weight": 20, "contribution": 15.0, "details": { "active_months": 12, "months_with_expenses": 9, "rate": "75.00%" } },
      "stock": { "score": 60.0, "weight": 20, "contribution": 12.0, "details": { "total_products": 50, "products_with_sales": 30, "rate": "60.00%" } },
      "regularity": { "score": 100.0, "weight": 15, "contribution": 15.0, "details": { "active_months": 12, "total_months": 12 } },
      "discipline": { "score": 65.0, "weight": 15, "contribution": 9.75, "details": { "days_with_activity": 195, "total_days": 300, "rate": "65.00%" } }
    },
    "ranking": { "percentile": 68 }
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/inactive-customers`

Customers who placed at least one order during the year but have not ordered in the last 90 days.

```http
GET /2023-11/admin/wrapped/inactive-customers?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "inactive_customers_count": 12,
    "inactive_customers": [
      {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "first_name": "Bob",
        "last_name": "Nkomo",
        "email": "bob@example.com",
        "orders": [{ "id": "...", "created_at": "2024-08-15T10:00:00.000000Z" }]
      }
    ],
    "inactive_since_days": 90
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/monthly-average-revenue`

Average monthly revenue across all months with orders in the year.

```http
GET /2023-11/admin/wrapped/monthly-average-revenue?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "monthly_average_revenue": 262500
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/monthly-sales-evolution`

Month-by-month sales evolution for all 12 months of the year, with the best month identified.

**Query parameters** — `year`, `locale` (e.g. `fr`, `en`; default `fr`).

```http
GET /2023-11/admin/wrapped/monthly-sales-evolution?year=2024&locale=en HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "monthly_sales_evolution": [
    { "month": "January", "total_amount": 210000 },
    { "month": "February", "total_amount": 195000 },
    { "month": "March", "total_amount": 310000 }
  ],
  "best_month": { "month": "March", "total_amount": 310000 }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/most-productive-day`

The single day with the highest revenue in the year.

```http
GET /2023-11/admin/wrapped/most-productive-day?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "most_productive_day": {
    "date": "2024-11-29",
    "total_amount": 85000
  }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/most-profitable-product`

The most profitable product by gross margin (sale price minus cost) for the year.

```http
GET /2023-11/admin/wrapped/most-profitable-product?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — `ProductDetailResource`:
```json
{
  "most_profitable_product": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "title": "Premium Widget",
    "total_profit": 125000
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.
- `404` — No matching product found.

---

### `GET` `/2023-11/admin/wrapped/most-sold-products`

Products ranked by units sold in the year (only products with more than 2 units sold).

```http
GET /2023-11/admin/wrapped/most-sold-products?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "most_sold_products": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "title": "Widget A", "quantity_sum": 320 }
  ],
  "top5_most_sold_products": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "title": "Widget A", "quantity_sum": 320 }
  ]
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/never-sold-products`

Products that had zero sales during the year.

```http
GET /2023-11/admin/wrapped/never-sold-products?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — items are `ProductDetailResource`:
```json
{
  "data": {
    "never_sold_products_count": 8,
    "never_sold_products": [
      { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "title": "Slow Mover" }
    ]
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/organization-stats`

Organization and discipline statistics: invoices issued, non-invoiced sales rate, expenses recorded, estimated profit, and best expense control month.

**Query parameters** — `year`, `locale` (default `fr`).

```http
GET /2023-11/admin/wrapped/organization-stats?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "invoices_count": 213,
    "total_sales": 250,
    "non_invoiced_sales": 37,
    "non_invoiced_percentage": 14.8,
    "expenses_recorded": 96,
    "estimated_profit": 2580000,
    "best_expense_control_month": "March",
    "best_expense_control_ratio": 18.5
  }
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/regular-customers`

Customers who placed more than one order during the year, with a count and top 5 list.

```http
GET /2023-11/admin/wrapped/regular-customers?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "regular_customers_count": 45,
  "top_regular_customers": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "first_name": "Alice", "last_name": "Mballa", "orders_count": 12 }
  ],
  "ranking": { "percentile": 60 }
}
```

**Error responses**
- `401` — Unauthenticated.
- `403` — Unauthorized.

---

### `GET` `/2023-11/admin/wrapped/top-expenses`

Top 3 highest individual expense bills for the year.

```http
GET /2023-11/admin/wrapped/top-expenses?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "top_expenses": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "amount": 350000, "expense_type": "equipment", "created_at": "2024-03-15T00:00:00.000000Z" },
    { "id": "01hqydxwtxdj3kmzp3bz7jk73h", "amount": 280000, "expense_type": "rent", "created_at": "2024-01-01T00:00:00.000000Z" }
  ]
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/top-products`

Top 5 products ranked by total revenue for the year.

```http
GET /2023-11/admin/wrapped/top-products?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — items are `ProductDetailResource`:
```json
{
  "top_products": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "title": "Premium Widget", "quantity_sum": 320, "total_revenue": 960000 }
  ]
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

### `GET` `/2023-11/admin/wrapped/total-expenses`

Total expenses amount (sum of all bills) for the year.

```http
GET /2023-11/admin/wrapped/total-expenses?year=2024 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "total_expenses": 920000
}
```

**Error responses**
- `400` — `companyId` not provided.
- `401` — Unauthenticated.

---

## Local Test Endpoints

> **These endpoints are for development/testing purposes only.** They should not be called in production. They are implemented as route closures and may perform destructive operations (data cleanup, test broadcasts, etc.).

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/local-test/broadcast` | Fires a test broadcast event |
| GET | `/2023-11/local-test/broadcast/{orderId}` | Fires a test broadcast for a specific order |
| GET | `/2023-11/local-test/clean` | Cleans duplicate products, variants, and addresses |
| GET | `/2023-11/local-test/clean-accounting-codes` | Cleans duplicate accounting codes |
| POST | `/2023-11/local-test/meets` | Creates a test meets entry |
| GET | `/2023-11/local-test/merge-variants` | Merges duplicate product variants |
| GET | `/2023-11/local-test/notify` | Sends a test notification |
| GET | `/2023-11/local-test/redis-test` | Tests Redis connectivity |
| GET | `/2023-11/local-test/sync/permissions` | Syncs permission seeds |
| GET | `/2023-11/local-test/test-mail` | Sends a test email |
| GET | `/2023-11/local-test/{company}/orders/email` | Checks order email rendering for a company |
