---
title: "Accounting & Finance"
description: "The Accounting & Finance domain covers the full double-entry bookkeeping stack: chart of accounts, accounting journals, book entries (transactions), fiscal year"
sidebarTitle: "Accounting & Finance"
---

The Accounting & Finance domain covers the full double-entry bookkeeping stack: chart of accounts, accounting journals, book entries (transactions), fiscal years and periods, reconciliation, treasury bank-statement import, tax returns, and supporting configuration (policies, document number formats, revenue types, taxes). It also includes a company wallet for WhatsApp message credits and treasury accounts (cash/bank drawers).

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · [Getting Started](01-getting-started.md)

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| GET | `/2023-11/admin/accounting/account_codes` | List all accounting account codes |
| GET | `/2023-11/admin/accounting/accounts` | List chart of accounts |
| POST | `/2023-11/admin/accounting/accounts` | Create an accounting account |
| GET | `/2023-11/admin/accounting/accounts-csv-template` | Download blank CSV import template |
| GET | `/2023-11/admin/accounting/accounts-export` | Export chart of accounts as CSV |
| GET | `/2023-11/admin/accounting/accounts-frameworks` | List available CoA frameworks |
| POST | `/2023-11/admin/accounting/accounts-import` | Import chart of accounts from CSV |
| GET | `/2023-11/admin/accounting/accounts/configuration` | Get account code configuration |
| PUT | `/2023-11/admin/accounting/accounts/configuration` | Update account code configuration |
| POST | `/2023-11/admin/accounting/accounts/{accountId}/lock` | Toggle account lock |
| GET | `/2023-11/admin/accounting/accounts/{account}` | Get a single account |
| PUT | `/2023-11/admin/accounting/accounts/{account}` | Update an account |
| DELETE | `/2023-11/admin/accounting/accounts/{account}` | Delete an account |
| POST | `/2023-11/admin/accounting/activation` | Trigger accounting activation (async) |
| GET | `/2023-11/admin/accounting/activation/status` | Get activation job status |
| GET | `/2023-11/admin/accounting/aged-balance` | Aged balance report (customers/suppliers) |
| GET | `/2023-11/admin/accounting/audit-log` | List accounting audit log entries |
| GET | `/2023-11/admin/accounting/audit-log/verify` | Verify audit log hash chain integrity |
| GET | `/2023-11/admin/accounting/balance-sheet` | Balance sheet report |
| GET | `/2023-11/admin/accounting/cash-flow` | Cash flow statement (alias) |
| GET | `/2023-11/admin/accounting/cash-flow-statement` | Cash flow statement |
| GET | `/2023-11/admin/accounting/dashboard` | Accounting dashboard KPIs |
| GET | `/2023-11/admin/accounting/entries` | List journal transactions |
| POST | `/2023-11/admin/accounting/entries` | Create a manual journal entry |
| POST | `/2023-11/admin/accounting/entries/batch-delete` | Batch-delete draft transactions |
| POST | `/2023-11/admin/accounting/entries/batch-post` | Batch-post draft transactions |
| GET | `/2023-11/admin/accounting/entries/{transaction}` | Get a transaction |
| PUT | `/2023-11/admin/accounting/entries/{transaction}` | Update a draft transaction |
| DELETE | `/2023-11/admin/accounting/entries/{transaction}` | Delete a draft transaction |
| POST | `/2023-11/admin/accounting/entries/{transaction}/post` | Post a draft transaction |
| POST | `/2023-11/admin/accounting/entries/{transaction}/reverse` | Reverse a posted transaction |
| GET | `/2023-11/admin/accounting/exports/audit-trail` | Export audit trail (CSV/XLSX) |
| GET | `/2023-11/admin/accounting/exports/available` | List available fiscal exports |
| GET | `/2023-11/admin/accounting/exports/fec` | Export FEC (France comptabilité) |
| GET | `/2023-11/admin/accounting/exports/{type}` | Generate a fiscal export |
| GET | `/2023-11/admin/accounting/exports/{type}/validate` | Validate export rules |
| GET | `/2023-11/admin/accounting/fiscal-years` | List fiscal years |
| POST | `/2023-11/admin/accounting/fiscal-years` | Create a fiscal year |
| GET | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}` | Get a fiscal year |
| PUT | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}` | Update a fiscal year |
| DELETE | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}` | Delete an open fiscal year |
| POST | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/close` | Close a fiscal year |
| POST | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/close-preview` | Preview year-end closing entries |
| POST | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/lock` | Lock a closed fiscal year |
| POST | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/pre-close-check` | Pre-close validation check |
| POST | `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/reopen` | Reopen a closed fiscal year |
| POST | `/2023-11/admin/accounting/imports/{type}` | Run a spreadsheet import |
| POST | `/2023-11/admin/accounting/imports/{type}/detect` | Detect column mapping from file |
| POST | `/2023-11/admin/accounting/imports/{type}/preview` | Dry-run import preview |
| GET | `/2023-11/admin/accounting/income-statement` | Income statement report |
| GET | `/2023-11/admin/accounting/journals` | List accounting journals |
| POST | `/2023-11/admin/accounting/journals` | Create a journal |
| GET | `/2023-11/admin/accounting/journals/{journal}` | Get a journal |
| PUT | `/2023-11/admin/accounting/journals/{journal}` | Update a journal |
| DELETE | `/2023-11/admin/accounting/journals/{journal}` | Delete a journal |
| GET | `/2023-11/admin/accounting/ledger-book` | Ledger book (general ledger) |
| GET | `/2023-11/admin/accounting/numbering-gaps` | Detect entry number gaps |
| GET | `/2023-11/admin/accounting/periods` | List accounting periods |
| GET | `/2023-11/admin/accounting/periods/{period}` | Get a period |
| POST | `/2023-11/admin/accounting/periods/{period}/close` | Close a period |
| POST | `/2023-11/admin/accounting/periods/{period}/lock` | Lock a period |
| POST | `/2023-11/admin/accounting/periods/{period}/reopen` | Reopen a period |
| GET | `/2023-11/admin/accounting/policies` | Get accounting policy |
| PUT | `/2023-11/admin/accounting/policies` | Update accounting policy |
| POST | `/2023-11/admin/accounting/reconcile` | Reconcile book entries |
| GET | `/2023-11/admin/accounting/reconciliations` | List reconciliations |
| GET | `/2023-11/admin/accounting/reconciliations/{reconciliation}` | Get a reconciliation |
| POST | `/2023-11/admin/accounting/reconstruction/generate` | Queue accounting reconstruction |
| POST | `/2023-11/admin/accounting/reconstruction/scan` | Scan historical data for reconstruction |
| GET | `/2023-11/admin/accounting/reconstruction/status` | Get reconstruction job status |
| POST | `/2023-11/admin/accounting/seed_accounts` | Seed chart of accounts from template |
| GET | `/2023-11/admin/accounting/setup` | Get setup wizard progress |
| POST | `/2023-11/admin/accounting/setup` | Save setup wizard progress |
| GET | `/2023-11/admin/accounting/tax-returns/calculate` | Preview tax return calculation |
| GET | `/2023-11/admin/accounting/tax-returns/dashboard` | Tax position dashboard |
| GET | `/2023-11/admin/accounting/tax-returns/declaration-calculate` | Calculate declaration form |
| GET | `/2023-11/admin/accounting/tax-returns/declaration-forms` | List available declaration forms |
| GET | `/2023-11/admin/accounting/tax-returns/detail-report` | Paginated tax account entries |
| POST | `/2023-11/admin/accounting/tax-returns/file` | File a tax return |
| GET | `/2023-11/admin/accounting/tax-returns/history` | List filed tax returns |
| GET | `/2023-11/admin/accounting/tax-returns/{taxReturn}` | Get a tax return |
| GET | `/2023-11/admin/accounting/third-parties` | List third parties with balances |
| GET | `/2023-11/admin/accounting/third-parties/{thirdParty}` | Get a third party |
| GET | `/2023-11/admin/accounting/third-parties/{thirdParty}/entries` | Third party ledger entries |
| GET | `/2023-11/admin/accounting/treasury-statements` | List treasury statements |
| POST | `/2023-11/admin/accounting/treasury-statements` | Import a treasury statement |
| POST | `/2023-11/admin/accounting/treasury-statements/preview` | Preview CSV before import |
| GET | `/2023-11/admin/accounting/treasury-statements/{statement}` | Get a statement with summary |
| DELETE | `/2023-11/admin/accounting/treasury-statements/{statement}` | Delete a draft statement |
| POST | `/2023-11/admin/accounting/treasury-statements/{statement}/auto-reconcile` | Auto-reconcile statement lines |
| GET | `/2023-11/admin/accounting/treasury-statements/{statement}/lines` | List statement lines |
| GET | `/2023-11/admin/accounting/treasury-statements/{statement}/lines/{line}/suggestions` | Get match suggestions for a line |
| POST | `/2023-11/admin/accounting/treasury-statements/{statement}/match` | Match line to book entry |
| GET | `/2023-11/admin/accounting/treasury-statements/{statement}/report` | Reconciliation report |
| POST | `/2023-11/admin/accounting/treasury-statements/{statement}/unmatch` | Unmatch a statement line |
| GET | `/2023-11/admin/accounting/trial-balance` | Trial balance report |
| GET | `/2023-11/admin/accounting/unmatched-entries` | List unreconciled book entries |
| POST | `/2023-11/admin/accounting/unreconcile/{reconciliation}` | Remove a reconciliation |
| GET | `/2023-11/admin/treasury-accounts` | List treasury accounts |
| POST | `/2023-11/admin/treasury-accounts` | Create treasury account |
| POST | `/2023-11/admin/treasury-accounts/export` | Export treasury transactions |
| GET | `/2023-11/admin/treasury-accounts/export/fields` | List exportable fields |
| GET | `/2023-11/admin/treasury-accounts/{treasuryAccount}` | Get treasury account |
| PUT | `/2023-11/admin/treasury-accounts/{treasuryAccount}` | Update treasury account |
| DELETE | `/2023-11/admin/treasury-accounts/{treasuryAccount}` | Delete treasury account |
| GET | `/2023-11/admin/treasuryAccounts` | List treasury accounts (legacy alias) |
| POST | `/2023-11/admin/treasuryAccounts` | Create treasury account (legacy alias) |
| POST | `/2023-11/admin/treasuryAccounts/export` | Export (legacy alias) |
| GET | `/2023-11/admin/treasuryAccounts/export/fields` | Export fields (legacy alias) |
| GET | `/2023-11/admin/treasuryAccounts/{treasuryAccount}` | Get treasury account (legacy alias) |
| PUT | `/2023-11/admin/treasuryAccounts/{treasuryAccount}` | Update treasury account (legacy alias) |
| DELETE | `/2023-11/admin/treasuryAccounts/{treasuryAccount}` | Delete treasury account (legacy alias) |
| GET | `/2023-11/admin/revenue-types` | List revenue types |
| POST | `/2023-11/admin/revenue-types` | Create a revenue type |
| PUT | `/2023-11/admin/revenue-types/{revenueType}` | Update a revenue type |
| DELETE | `/2023-11/admin/revenue-types/{revenueType}` | Delete a revenue type |
| GET | `/2023-11/admin/wallet` | Get wallet balance and recent transactions |
| POST | `/2023-11/admin/wallet/topup` | Initiate wallet top-up |
| GET | `/2023-11/admin/wallet/transactions` | List wallet transactions |
| GET | `/2023-11/admin/document_number_formats` | List document number formats |
| POST | `/2023-11/admin/document_number_formats` | Create document number format |
| PUT | `/2023-11/admin/document_number_formats/{documentNumberFormat}` | Update document number format |
| GET | `/2023-11/admin/taxes` | List taxes |
| POST | `/2023-11/admin/taxes` | Create a tax |
| GET | `/2023-11/admin/taxes/{tax}` | Get a tax |
| PUT | `/2023-11/admin/taxes/{tax}` | Update a tax |
| DELETE | `/2023-11/admin/taxes/{tax}` | Soft-delete a tax |
| POST | `/2023-11/admin/taxes/{tax}/restore` | Restore a soft-deleted tax |

---

## Chart of Accounts

### `GET` `/2023-11/admin/accounting/account_codes`

List all accounting account codes for the tenant. Supports date-range filtering on `updated_at`.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[updated_at][start]` | date | No | — | Filter accounts updated after this date |
| `filter[updated_at][end]` | date | No | — | Filter accounts updated before this date |

```http
GET /2023-11/admin/accounting/account_codes HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "code": "411",
    "name": "Clients",
    "account_type": "Asset",
    "normal_balance": "debit",
    "parent_account_code": "41",
    "parent_account_id": null,
    "is_reconcilable": true,
    "is_locked": false,
    "allow_manual_entries": true,
    "description": null,
    "created_at": "2024-03-15T10:30:00.000000Z",
    "updated_at": "2024-03-15T10:30:00.000000Z"
  }
]
```

**Error responses**
- `401` — Missing or invalid token.
- `403` — User lacks `manage_accounting` permission.

---

### `GET` `/2023-11/admin/accounting/accounts`

Paginated list of chart-of-accounts entries. Supports filtering, sorting, and optional tree includes.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[account_type]` | string | No | — | Exact match: `Asset`, `Liability`, `Equity`, `Revenue`, `Expense` |
| `filter[is_locked]` | boolean | No | — | Filter locked/unlocked accounts |
| `filter[is_reconcilable]` | boolean | No | — | Filter reconcilable accounts |
| `filter[parent_account_code]` | string | No | — | Filter by parent code |
| `filter[search]` | string | No | — | Partial search on `code` and `name` |
| `include` | string | No | — | `children` |
| `sort` | string | No | `code` | `code`, `name`, `account_type`, `created_at` (prefix `-` for DESC) |
| `per_page` | integer | No | `50` | Items per page |
| `paginate` | string | No | — | Set to `false` to return all without pagination |

```http
GET /2023-11/admin/accounting/accounts?filter[account_type]=Asset&sort=code HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — paginated wrapper with account objects:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "code": "411",
      "name": "Clients",
      "account_type": "Asset",
      "normal_balance": "debit",
      "parent_account_code": "41",
      "parent_account_id": "01hqydxwtxdj3kmzp3bz7jk000",
      "country_code": "CM",
      "is_locked": false,
      "is_reconcilable": true,
      "allow_manual_entries": false,
      "description": null,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 50, "total": 120 }
}
```

**Error responses**
- `401` Unauthenticated.
- `403` Missing `manage_accounting` permission.

---

### `POST` `/2023-11/admin/accounting/accounts`

Create a new account in the chart of accounts. Code must be unique per company.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `code` | string | Yes | max:20 | Account code (e.g. `411`) |
| `name` | string | Yes | max:255 | Account label |
| `account_type` | string | Yes | `Asset`,`Liability`,`Equity`,`Revenue`,`Expense` | Account classification |
| `normal_balance` | string | No | `debit` or `credit` | Defaults from account type if omitted |
| `parent_account_code` | string | No | max:20 | Parent account code |
| `is_reconcilable` | boolean | No | — | Whether entries can be reconciled |
| `allow_manual_entries` | boolean | No | — | Allow manual journal entries |
| `description` | string | No | — | Optional description |

```http
POST /2023-11/admin/accounting/accounts HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "code": "411",
  "name": "Clients",
  "account_type": "Asset",
  "parent_account_code": "41",
  "is_reconcilable": true,
  "allow_manual_entries": false
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "411",
  "name": "Clients",
  "account_type": "Asset",
  "normal_balance": "debit",
  "parent_account_code": "41",
  "parent_account_id": "01hqydxwtxdj3kmzp3bz7jk000",
  "is_reconcilable": true,
  "is_locked": false,
  "allow_manual_entries": false,
  "description": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `401` Unauthenticated.
- `403` Missing permission.
- `422` Validation failure or code already exists.

---

### `GET` `/2023-11/admin/accounting/accounts-csv-template`

Download a blank CSV file with headers for importing a chart of accounts.

```http
GET /2023-11/admin/accounting/accounts-csv-template HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
```

**Success response** (`200 OK`) — `text/csv` file download (`chart_of_accounts_template.csv`).

---

### `GET` `/2023-11/admin/accounting/accounts-export`

Export the company's current chart of accounts as a CSV file.

```http
GET /2023-11/admin/accounting/accounts-export HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
```

**Success response** (`200 OK`) — `text/csv` file download.

---

### `GET` `/2023-11/admin/accounting/accounts-frameworks`

Return the list of built-in chart of accounts frameworks (e.g. `syscohada`, `pcg`, `ifrs`).

```http
GET /2023-11/admin/accounting/accounts-frameworks HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
[
  { "key": "syscohada", "label": "SYSCOHADA (OHADA)" },
  { "key": "pcg", "label": "Plan Comptable Général (France)" }
]
```

---

### `POST` `/2023-11/admin/accounting/accounts-import`

Import (upsert) accounts from a CSV file. Existing accounts matched by code are updated; new ones are created.

**Request body** (`multipart/form-data`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | csv/txt, max 2 MB | CSV file to import |
| `country_code` | string | No | size:2 | Two-letter country code; defaults to `CM` |

```http
POST /2023-11/admin/accounting/accounts-import HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: multipart/form-data

file=@chart_of_accounts.csv
country_code=CM
```

**Success response** (`200 OK`):
```json
{
  "message": "4 accounts imported successfully.",
  "inserted": 4,
  "skipped": 1,
  "errors": []
}
```

**Error responses**
- `422` No rows could be imported; `errors` array contains per-row reasons.

---

### `GET` `/2023-11/admin/accounting/accounts/configuration`

Return the resolved account code mapping (system defaults + company overrides) used when auto-generating book entries.

```http
GET /2023-11/admin/accounting/accounts/configuration HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "customer_receivable": "411",
    "supplier_payable": "401",
    "vat_collected": "4431",
    "vat_deductible": "4452",
    "revenue_sales": "701",
    "cash": "571"
  }
}
```

---

### `PUT` `/2023-11/admin/accounting/accounts/configuration`

Override one or more account codes. Set a key to `null` or `""` to revert to the system default.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `accounts` | object | Yes | — | Key-value map of config key → account code |
| `accounts.*` | string\|null | No | max:20 | Account code or null to revert |

```http
PUT /2023-11/admin/accounting/accounts/configuration HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "accounts": {
    "customer_receivable": "4110",
    "revenue_sales": "7011"
  }
}
```

**Success response** (`200 OK`):
```json
{
  "message": "Account configuration updated.",
  "data": { "customer_receivable": "4110", "revenue_sales": "7011" }
}
```

---

### `POST` `/2023-11/admin/accounting/accounts/{accountId}/lock`

Toggle the `is_locked` flag on an account. Locked accounts cannot receive new manual entries. Accepts either an account ULID or account code.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `accountId` | string | Account ULID or code |

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/accounting/accounts/411/lock HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
```

**Success response** (`200 OK`) — returns the updated account object with the new `is_locked` value:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "411",
  "name": "Clients",
  "account_type": "Asset",
  "normal_balance": "debit",
  "parent_account_code": "41",
  "parent_account_id": "01hqydxwtxdj3kmzp3bz7jk000",
  "is_locked": true,
  "is_reconcilable": true,
  "allow_manual_entries": false,
  "description": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

---

### `GET` `/2023-11/admin/accounting/accounts/{account}`

Return a single account including entry count.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `account` | string | Account ULID |

```http
GET /2023-11/admin/accounting/accounts/01hqydxwtxdj3kmzp3bz7jk73g HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — account object with `entries_count`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "411",
  "name": "Clients",
  "account_type": "Asset",
  "normal_balance": "debit",
  "parent_account_code": "41",
  "parent_account_id": "01hqydxwtxdj3kmzp3bz7jk000",
  "country_code": "CM",
  "is_locked": false,
  "is_reconcilable": true,
  "allow_manual_entries": false,
  "description": null,
  "metadata": null,
  "entries_count": 42,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `404` Account not found.

---

### `PUT` `/2023-11/admin/accounting/accounts/{account}`

Update an account. The `code` field cannot be changed if the account already has book entries.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `account` | string | Account ULID |

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `code` | string | No | max:20 | Cannot change if entries exist |
| `name` | string | No | max:255 | — |
| `account_type` | string | No | enum | — |
| `normal_balance` | string | No | `debit`/`credit` | — |
| `parent_account_code` | string | No | max:20 | — |
| `is_reconcilable` | boolean | No | — | — |
| `allow_manual_entries` | boolean | No | — | — |
| `description` | string | No | — | — |

**Success response** (`200 OK`) — updated account object:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "411",
  "name": "Clients (updated)",
  "account_type": "Asset",
  "normal_balance": "debit",
  "parent_account_code": "41",
  "parent_account_id": "01hqydxwtxdj3kmzp3bz7jk000",
  "is_locked": false,
  "is_reconcilable": true,
  "allow_manual_entries": true,
  "description": "Updated description",
  "metadata": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-03-16T09:00:00.000000Z"
}
```

**Error responses**
- `422` Attempted to change code on an account with entries.

---

### `DELETE` `/2023-11/admin/accounting/accounts/{account}`

Delete an account. Fails if the account has any book entries.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `account` | string | Account ULID |

**Success response** (`200 OK`):
```json
{ "message": "Account deleted." }
```

**Error responses**
- `422` Account has entries and cannot be deleted.

---

## Accounting Activation & Setup

### `POST` `/2023-11/admin/accounting/activation`

Queue an async job to activate accounting by generating book entries from all existing invoices, bills, and payments. Returns `409` if a job is already running. Business data (treasury accounts, payments) is never modified.

**Request body:** None — no body required for this action endpoint.

```http
POST /2023-11/admin/accounting/activation HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
```

**Success response** (`202 Accepted`):
```json
{
  "message": "Activation queued.",
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "status": "queued",
    "options": { "mode": "activation" },
    "triggered_by": "01hqydxwtxdj3kmzp3bz000000",
    "created_at": "2024-03-15T10:30:00.000000Z"
  }
}
```

**Error responses**
- `403` Missing `manage_accounting` permission.
- `409` A job is already running.

---

### `GET` `/2023-11/admin/accounting/activation/status`

Poll the latest activation job status for the company.

```http
GET /2023-11/admin/accounting/activation/status HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "status": "completed",
    "progress": 100,
    "result": { "entries_created": 1240 },
    "updated_at": "2024-03-15T10:35:00.000000Z"
  }
}
```

---

### `GET` `/2023-11/admin/accounting/setup`

Return the saved accounting setup wizard progress stored in company metadata.

**Success response** (`200 OK`):
```json
{
  "data": {
    "formData": { "framework": { "framework": "syscohada", "fiscalYearStart": "01-01", "fiscalYearEnd": "12-31" } },
    "currentStep": 2,
    "completedSteps": [0, 1],
    "updatedAt": "2024-03-15T10:30:00.000000Z"
  }
}
```

---

### `POST` `/2023-11/admin/accounting/setup`

Save accounting setup wizard progress to company metadata.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `formData` | object | Yes | — | Full wizard form data |
| `currentStep` | integer | Yes | min:0 | Active wizard step index |
| `completedSteps` | array | Yes | — | Array of completed step indices |

**Success response** (`200 OK`):
```json
{
  "message": "Setup progress saved.",
  "data": { "currentStep": 2, "completedSteps": [0, 1] }
}
```

---

### `POST` `/2023-11/admin/accounting/seed_accounts`

Seed the chart of accounts from a built-in framework template. Only runs if no accounts exist (use `force=true` to re-seed). Called by the setup wizard.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `force` | boolean | No | — | Delete and re-seed even if accounts exist |

**Success response** (`200 OK`):
```json
{ "seeded": true, "count": 327 }
```

---

## Fiscal Years

### `GET` `/2023-11/admin/accounting/fiscal-years`

Paginated list of fiscal years.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[status]` | string | No | — | `open`, `closed`, `locked` |
| `filter[search]` | string | No | — | Search on name |
| `include` | string | No | — | `periods`, `closedByUser` |
| `sort` | string | No | `-start_date` | `start_date`, `end_date`, `name`, `created_at` |
| `per_page` | integer | No | 15 | — |
| `paginate` | string | No | — | `false` for all |

```http
GET /2023-11/admin/accounting/fiscal-years?filter[status]=open HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — paginated list of fiscal year objects:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "FY 2024",
      "start_date": "2024-01-01",
      "end_date": "2024-12-31",
      "status": "open",
      "closed_at": null,
      "closed_by": null,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 15, "total": 3 }
}
```

---

### `POST` `/2023-11/admin/accounting/fiscal-years`

Create a new fiscal year. Periods are auto-generated by the system observer.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | — | Display name (e.g. `FY 2024`) |
| `start_date` | date | Yes | — | Start of the fiscal year |
| `end_date` | date | Yes | after:start_date | End of the fiscal year |
| `metadata` | object | No | — | Arbitrary metadata |

```http
POST /2023-11/admin/accounting/fiscal-years HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "name": "FY 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-12-31"
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "FY 2024",
  "start_date": "2024-01-01T00:00:00.000000Z",
  "end_date": "2024-12-31T23:59:59.000000Z",
  "status": "open",
  "metadata": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "periods": [
    { "id": "...", "name": "January 2024", "period_number": 1, "start_date": "2024-01-01", "end_date": "2024-01-31", "status": "open" }
  ]
}
```

---

### `GET` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}`

Get a single fiscal year with period list and entry count.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `fiscalYear` | string | Fiscal year ULID |

**Success response** (`200 OK`) — fiscal year object with `periods`, `closed_by_user`, and `entries_count`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "FY 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-12-31",
  "status": "open",
  "closed_at": null,
  "closed_by": null,
  "metadata": null,
  "entries_count": 1240,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z",
  "periods": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk001",
      "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "period_number": 1,
      "name": "January 2024",
      "start_date": "2024-01-01",
      "end_date": "2024-01-31",
      "status": "open",
      "closed_at": null,
      "closed_by": null
    }
  ],
  "closed_by_user": null
}
```

---

### `PUT` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}`

Update a fiscal year. If dates change, periods are regenerated.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | — | — |
| `start_date` | date | No | — | Triggers period regeneration |
| `end_date` | date | No | — | Triggers period regeneration |
| `metadata` | object | No | — | — |

**Success response** (`200 OK`) — updated fiscal year with periods:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "FY 2024 (updated)",
  "start_date": "2024-01-01",
  "end_date": "2024-12-31",
  "status": "open",
  "closed_at": null,
  "closed_by": null,
  "metadata": null,
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "periods": []
}
```

---

### `DELETE` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}`

Delete a fiscal year. Only open years with no entries may be deleted.

**Success response** (`200 OK`):
```json
{ "message": "Fiscal year deleted." }
```

**Error responses**
- `422` Year is not open, or has entries.

---

### `POST` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/pre-close-check`

Run pre-close validation and return a report of issues and warnings without modifying data.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`):
```json
{
  "can_close": true,
  "issues": [],
  "warnings": [{ "code": "draft_entries", "count": 3, "message": "3 draft transactions remain." }]
}
```

---

### `POST` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/close-preview`

Dry-run: return the accounting entries that would be generated by a year-end close (result allocation + carry-forward).

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — array of preview entry objects:
```json
[
  {
    "journal_code": "OD",
    "description": "P&L allocation — FY 2024",
    "entry_date": "2024-12-31",
    "lines": [
      { "account_code": "130", "label": "Net result allocation", "debit": 5000000, "credit": 0 },
      { "account_code": "120", "label": "Retained earnings carry-forward", "debit": 0, "credit": 5000000 }
    ]
  }
]
```

---

### `POST` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/close`

Execute year-end close: allocate P&L result, generate carry-forward entry, lock period entries, and set status to `closed`.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`):
```json
{
  "status": "closed",
  "entries_created": 2,
  "carry_forward_amount": 1500000
}
```

**Error responses**
- `422` Pre-close checks failed.

---

### `POST` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/reopen`

Reverse a year-end close: delete carry-forward entries and set status back to `open`.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — fiscal year object with status `open`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "FY 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-12-31",
  "status": "open",
  "closed_at": null,
  "closed_by": null,
  "updated_at": "2024-03-16T09:00:00.000000Z"
}
```

---

### `POST` `/2023-11/admin/accounting/fiscal-years/{fiscalYear}/lock`

Permanently lock a closed fiscal year. The year must be in `closed` status first.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — fiscal year object with status `locked`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "FY 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-12-31",
  "status": "locked",
  "closed_at": "2025-01-15T08:00:00.000000Z",
  "closed_by": "01hqydxwtxdj3kmzp3bz000000",
  "updated_at": "2025-01-15T08:05:00.000000Z"
}
```

**Error responses**
- `422` Year is not in `closed` status.

---

## Accounting Periods

### `GET` `/2023-11/admin/accounting/periods`

List accounting periods (auto-generated monthly sub-divisions of fiscal years).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[fiscal_year_id]` | string | No | — | Filter by fiscal year |
| `filter[status]` | string | No | — | `open`, `closed`, `locked` |
| `include` | string | No | — | `fiscalYear`, `closedByUser` |
| `sort` | string | No | `period_number` | `period_number`, `start_date`, `end_date` |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`) — paginated period list:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk001",
      "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "period_number": 1,
      "name": "January 2024",
      "start_date": "2024-01-01",
      "end_date": "2024-01-31",
      "status": "open",
      "closed_at": null,
      "closed_by": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 15, "total": 12 }
}
```

---

### `GET` `/2023-11/admin/accounting/periods/{period}`

Get a single period with entry count.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `period` | string | Period ULID |

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "fiscal_year_id": "01hqydxwtxdj3kmzp3bz000000",
  "name": "January 2024",
  "period_number": 1,
  "start_date": "2024-01-01",
  "end_date": "2024-01-31",
  "status": "open",
  "closed_at": null,
  "entries_count": 42
}
```

---

### `POST` `/2023-11/admin/accounting/periods/{period}/close`

Close a period. The parent fiscal year must be open.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — period with status `closed` and `closed_at` timestamp:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "period_number": 1,
  "name": "January 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-01-31",
  "status": "closed",
  "closed_at": "2024-02-05T08:00:00.000000Z",
  "closed_by": "01hqydxwtxdj3kmzp3bz000000"
}
```

**Error responses**
- `422` Already closed, or fiscal year is not open.

---

### `POST` `/2023-11/admin/accounting/periods/{period}/reopen`

Reopen a closed period (not allowed if locked).

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — period with status `open`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "period_number": 1,
  "name": "January 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-01-31",
  "status": "open",
  "closed_at": null,
  "closed_by": null
}
```

---

### `POST` `/2023-11/admin/accounting/periods/{period}/lock`

Lock a period. Must be closed first.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — period with status `locked`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk001",
  "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "period_number": 1,
  "name": "January 2024",
  "start_date": "2024-01-01",
  "end_date": "2024-01-31",
  "status": "locked",
  "closed_at": "2024-02-05T08:00:00.000000Z",
  "closed_by": "01hqydxwtxdj3kmzp3bz000000"
}
```

---

## Accounting Journals

### `GET` `/2023-11/admin/accounting/journals`

List accounting journals with transaction counts.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[type]` | string | No | — | Journal type enum value |
| `filter[is_active]` | boolean | No | — | — |
| `filter[code]` | string | No | — | Exact match |
| `filter[search]` | string | No | — | Partial match on code and name |
| `sort` | string | No | `code` | `code`, `name`, `type`, `created_at` |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`) — paginated list with `transactions_count`:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "code": "VTE",
      "name": "Sales Journal",
      "type": "sales",
      "is_default": true,
      "is_active": true,
      "default_debit_account": "411",
      "default_credit_account": "701",
      "transactions_count": 248,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
    }
  ],
  "links": { "first": "...", "last": "...", "prev": null, "next": null },
  "meta": { "current_page": 1, "per_page": 15, "total": 6 }
}
```

---

### `POST` `/2023-11/admin/accounting/journals`

Create a journal.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `code` | string | Yes | max:10 | Unique journal code (e.g. `VTE`) |
| `name` | string | Yes | max:255 | Display name |
| `type` | string | Yes | JournalType enum | e.g. `sales`, `purchase`, `bank`, `cash`, `miscellaneous` |
| `is_active` | boolean | No | — | Defaults to true |
| `default_debit_account` | string | No | — | Default debit account code |
| `default_credit_account` | string | No | — | Default credit account code |

```http
POST /2023-11/admin/accounting/journals HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "code": "VTE",
  "name": "Sales Journal",
  "type": "sales",
  "is_active": true
}
```

**Success response** (`201 Created`) — journal object:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "VTE",
  "name": "Sales Journal",
  "type": "sales",
  "is_default": false,
  "is_active": true,
  "default_debit_account": null,
  "default_credit_account": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

---

### `GET` `/2023-11/admin/accounting/journals/{journal}`

Get a journal with transaction count.

**Success response** (`200 OK`) — journal object with `transactions_count`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "VTE",
  "name": "Sales Journal",
  "type": "sales",
  "is_default": true,
  "is_active": true,
  "default_debit_account": "411",
  "default_credit_account": "701",
  "transactions_count": 248,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z"
}
```

---

### `PUT` `/2023-11/admin/accounting/journals/{journal}`

Update a journal. Only `name`, `is_active`, and default accounts can be changed.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:255 | Display name |
| `is_active` | boolean | No | — | Enable or disable the journal |
| `default_debit_account` | string | No | max:20 | Default debit account code |
| `default_credit_account` | string | No | max:20 | Default credit account code |

**Success response** (`200 OK`) — updated journal object:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "VTE",
  "name": "Sales Journal (updated)",
  "type": "sales",
  "is_default": true,
  "is_active": true,
  "default_debit_account": "411",
  "default_credit_account": "7011",
  "updated_at": "2024-03-16T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/accounting/journals/{journal}`

Delete a journal.

**Success response** (`204 No Content`)

---

## Journal Entries (Transactions)

A **transaction** groups one or more double-entry **book entry lines**. Transactions are created in `draft` status and must be explicitly posted.

### `GET` `/2023-11/admin/accounting/entries`

Paginated list of accounting transactions with their entry lines.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[journal_code]` | string/array | No | — | Filter by journal code(s) |
| `filter[status]` | string | No | — | `draft`, `posted`, `locked` |
| `filter[fiscal_year_id]` | string | No | — | Filter by fiscal year |
| `filter[entry_date_from]` | date | No | — | Entries on or after this date |
| `filter[entry_date_to]` | date | No | — | Entries on or before this date |
| `filter[account_code]` | string | No | — | Filter transactions containing this account code |
| `filter[search]` | string | No | — | Search reference, description, entry_number |
| `sort` | string | No | `-entry_date` | `entry_date`, `entry_number`, `created_at`, `status` |
| `per_page` | integer | No | 20 | — |

```http
GET /2023-11/admin/accounting/entries?filter[status]=draft&filter[journal_code]=VTE HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — paginated list of transaction objects, each with `entries`, `journal`, `created_by_user`, `posted_by_user`:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "journal_id": "01hqydxwtxdj3kmzp3bz7jk100",
      "journal_code": "VTE",
      "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk200",
      "period_id": "01hqydxwtxdj3kmzp3bz7jk300",
      "entry_number": "VTE-2024-001",
      "entry_date": "2024-03-15",
      "description": "Invoice #001",
      "reference": "INV-001",
      "status": "posted",
      "source_type": "invoice",
      "source_id": "01hqydxwtxdj3kmzp3bz7jk400",
      "created_by": "01hqydxwtxdj3kmzp3bz000000",
      "posted_by": "01hqydxwtxdj3kmzp3bz000000",
      "posted_at": "2024-03-15T10:31:00.000000Z",
      "metadata": null,
      "created_at": "2024-03-15T10:30:00.000000Z",
      "updated_at": "2024-03-15T10:31:00.000000Z",
      "entries": [
        {
          "id": "01hqydxwtxdj3kmzp3bz7jk501",
          "transaction_id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "accounting_account_code": "411",
          "label": "Invoice #001 - ACME Corp",
          "debit": 590000,
          "credit": 0,
          "entry_date": "2024-03-15"
        },
        {
          "id": "01hqydxwtxdj3kmzp3bz7jk502",
          "transaction_id": "01hqydxwtxdj3kmzp3bz7jk73g",
          "accounting_account_code": "701",
          "label": "Sales revenue",
          "debit": 0,
          "credit": 500000,
          "entry_date": "2024-03-15"
        }
      ],
      "journal": { "id": "01hqydxwtxdj3kmzp3bz7jk100", "code": "VTE", "name": "Sales Journal", "type": "sales" }
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 20, "total": 156 }
}
```

---

### `POST` `/2023-11/admin/accounting/entries`

Create a manual journal entry in draft status.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `journal_code` | string | Yes | — | Journal to post to |
| `description` | string | Yes | — | Human-readable description |
| `entry_date` | date | Yes | — | Accounting date |
| `reference` | string | No | — | Optional external reference |
| `lines` | array | Yes | min:2 | Journal entry lines |
| `lines[].account_code` | string | Yes | — | Account code |
| `lines[].label` | string | Yes | — | Line description |
| `lines[].debit` | number | No | — | Debit amount (0 if credit line) |
| `lines[].credit` | number | No | — | Credit amount (0 if debit line) |

```http
POST /2023-11/admin/accounting/entries HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "journal_code": "OD",
  "description": "Office supplies purchase",
  "entry_date": "2024-03-15",
  "reference": "INV-2024-001",
  "lines": [
    { "account_code": "6064", "label": "Office supplies", "debit": 50000, "credit": 0 },
    { "account_code": "571",  "label": "Cash payment",    "debit": 0,     "credit": 50000 }
  ]
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "journal_code": "OD",
  "entry_number": null,
  "status": "draft",
  "description": "Office supplies purchase",
  "entry_date": "2024-03-15T00:00:00.000000Z",
  "reference": "INV-2024-001",
  "created_by": "01hqydxwtxdj3kmzp3bz000000",
  "entries": [
    { "id": "...", "accounting_account_code": "6064", "label": "Office supplies", "debit": 50000, "credit": 0 },
    { "id": "...", "accounting_account_code": "571",  "label": "Cash payment",    "debit": 0,     "credit": 50000 }
  ]
}
```

**Error responses**
- `422` Validation failure.

---

### `GET` `/2023-11/admin/accounting/entries/{transaction}`

Get a single transaction with all relations loaded.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `transaction` | string | Transaction ULID |

**Success response** (`200 OK`) — transaction with `entries`, `journal`, `fiscal_year`, `period`, `created_by_user`, `posted_by_user`, `source`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "journal_id": "01hqydxwtxdj3kmzp3bz7jk100",
  "journal_code": "VTE",
  "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk200",
  "period_id": "01hqydxwtxdj3kmzp3bz7jk300",
  "entry_number": "VTE-2024-001",
  "entry_date": "2024-03-15",
  "description": "Invoice #001",
  "reference": "INV-001",
  "status": "posted",
  "source_type": "invoice",
  "source_id": "01hqydxwtxdj3kmzp3bz7jk400",
  "created_by": "01hqydxwtxdj3kmzp3bz000000",
  "posted_by": "01hqydxwtxdj3kmzp3bz000000",
  "posted_at": "2024-03-15T10:31:00.000000Z",
  "metadata": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:31:00.000000Z",
  "entries": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk501", "accounting_account_code": "411", "label": "Invoice #001", "debit": 590000, "credit": 0, "entry_date": "2024-03-15" },
    { "id": "01hqydxwtxdj3kmzp3bz7jk502", "accounting_account_code": "4431", "label": "VAT collected", "debit": 0, "credit": 90000, "entry_date": "2024-03-15" },
    { "id": "01hqydxwtxdj3kmzp3bz7jk503", "accounting_account_code": "701", "label": "Sales revenue", "debit": 0, "credit": 500000, "entry_date": "2024-03-15" }
  ],
  "journal": { "id": "01hqydxwtxdj3kmzp3bz7jk100", "code": "VTE", "name": "Sales Journal", "type": "sales" },
  "fiscal_year": { "id": "01hqydxwtxdj3kmzp3bz7jk200", "name": "FY 2024", "status": "open" },
  "period": { "id": "01hqydxwtxdj3kmzp3bz7jk300", "name": "March 2024", "status": "open" },
  "created_by_user": { "id": "01hqydxwtxdj3kmzp3bz000000", "name": "Alice" },
  "posted_by_user": { "id": "01hqydxwtxdj3kmzp3bz000000", "name": "Alice" },
  "source": { "type": "invoice", "id": "01hqydxwtxdj3kmzp3bz7jk400" }
}
```

---

### `PUT` `/2023-11/admin/accounting/entries/{transaction}`

Update a draft transaction. Lines are fully replaced if provided.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `description` | string | No | — | — |
| `reference` | string | No | — | — |
| `entry_date` | date | No | — | — |
| `journal_code` | string | No | — | — |
| `lines` | array | No | — | Replaces all existing lines if provided |

**Success response** (`200 OK`) — updated transaction with entries:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "journal_code": "OD",
  "entry_number": null,
  "status": "draft",
  "description": "Updated description",
  "entry_date": "2024-03-15",
  "reference": "REF-002",
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "entries": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk501", "accounting_account_code": "6064", "label": "Office supplies", "debit": 50000, "credit": 0 },
    { "id": "01hqydxwtxdj3kmzp3bz7jk502", "accounting_account_code": "571",  "label": "Cash payment",    "debit": 0,     "credit": 50000 }
  ]
}
```

**Error responses**
- `422` Transaction is not in draft status.

---

### `DELETE` `/2023-11/admin/accounting/entries/{transaction}`

Delete a draft transaction and its lines.

**Success response** (`204 No Content`)

**Error responses**
- `422` Transaction is not in draft status.

---

### `POST` `/2023-11/admin/accounting/entries/{transaction}/post`

Post a draft transaction: assign a sequential entry number and set status to `posted`. The transaction must be balanced (debits = credits).

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — posted transaction with `entry_number` assigned:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "journal_code": "OD",
  "entry_number": "OD-2024-001",
  "status": "posted",
  "description": "Office supplies purchase",
  "entry_date": "2024-03-15",
  "posted_by": "01hqydxwtxdj3kmzp3bz000000",
  "posted_at": "2024-03-15T10:31:00.000000Z",
  "entries": [
    { "id": "01hqydxwtxdj3kmzp3bz7jk501", "accounting_account_code": "6064", "label": "Office supplies", "debit": 50000, "credit": 0 },
    { "id": "01hqydxwtxdj3kmzp3bz7jk502", "accounting_account_code": "571",  "label": "Cash payment",    "debit": 0,     "credit": 50000 }
  ]
}
```

**Error responses**
- `422` Already posted, locked, or unbalanced.

---

### `POST` `/2023-11/admin/accounting/entries/{transaction}/reverse`

Create a reversal transaction (swapped debits/credits) and post it immediately. The original must be in `posted` status and its period must be open.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `reason` | string | No | max:500 | Reason for reversal |

**Success response** (`201 Created`) — the new reversal transaction (posted):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk999",
  "journal_code": "OD",
  "entry_number": "OD-2024-002",
  "status": "posted",
  "description": "[Reversal] Office supplies purchase",
  "entry_date": "2024-03-16",
  "reference": "REV-OD-2024-001",
  "posted_at": "2024-03-16T09:00:00.000000Z",
  "entries": [
    { "id": "...", "accounting_account_code": "6064", "label": "Office supplies (reversal)", "debit": 0,     "credit": 50000 },
    { "id": "...", "accounting_account_code": "571",  "label": "Cash payment (reversal)",    "debit": 50000, "credit": 0 }
  ]
}
```

**Error responses**
- `422` Original is not posted, or period is closed.

---

### `POST` `/2023-11/admin/accounting/entries/batch-delete`

Delete up to 100 draft transactions in a single request.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `transaction_ids` | array | Yes | min:1 | Array of transaction ULIDs |

**Success response** (`204 No Content`) or `200 OK` with truncation notice if > 100 IDs supplied.

**Error responses**
- `422` Any transaction is not found or not in draft status.

---

### `POST` `/2023-11/admin/accounting/entries/batch-post`

Post up to 100 draft transactions atomically.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `transaction_ids` | array | Yes | min:1 | Array of transaction ULIDs |

**Success response** (`200 OK`):
```json
[
  { "id": "...", "status": "posted", "entry_number": "OD-2024-001", "entries": [] }
]
```

**Error responses**
- `422` Any transaction is not found, not draft, or unbalanced.

---

## Financial Reports

### `GET` `/2023-11/admin/accounting/balance-sheet`

Generate or download the balance sheet for a period or fiscal year.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | date | No | start of year | Period start (ignored if `fiscal_year_id` given) |
| `end_date` | date | No | end of year | Period end |
| `fiscal_year_id` | string | No | — | Use a specific fiscal year's dates |
| `compare` | integer | No | `0` | `1` to include N-1 comparison |
| `download` | boolean | No | — | Stream a file instead of JSON |
| `format` | string | No | `pdf` | `pdf`, `xlsx`, `csv` (only if `download=true`) |

```http
GET /2023-11/admin/accounting/balance-sheet?fiscal_year_id=01hqydxwtxdj3kmzp3bz7jk73g&compare=1 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "current": {
    "ASSETS": {
      "Current Assets": { "411 Clients": { "gross": 5000000, "amortization": null, "net": 5000000 } }
    },
    "LIABILITIES": {}
  },
  "previous": {}
}
```

---

### `GET` `/2023-11/admin/accounting/income-statement`

Income statement (compte de résultat). Same query parameters as balance sheet plus optional `compare`.

**Success response** (`200 OK`):
```json
{
  "revenue": { "701 Ventes produits": 12000000 },
  "expenses": { "601 Achats": 7000000 },
  "net_result": 5000000,
  "previous": null
}
```

---

### `GET` `/2023-11/admin/accounting/cash-flow-statement`

Cash flow statement (Tableau des flux de trésorerie). Same date/fiscal_year params; supports `download`.

**Success response** (`200 OK`) — structured cash flow data grouped by operating/investing/financing activities:
```json
{
  "operating": {
    "label": "Operating activities",
    "items": [
      { "label": "Net income", "amount": 5000000 },
      { "label": "Depreciation & amortisation", "amount": 120000 },
      { "label": "Change in trade receivables", "amount": -800000 },
      { "label": "Change in trade payables", "amount": 300000 }
    ],
    "total": 4620000
  },
  "investing": {
    "label": "Investing activities",
    "items": [
      { "label": "Purchase of equipment", "amount": -1200000 }
    ],
    "total": -1200000
  },
  "financing": {
    "label": "Financing activities",
    "items": [
      { "label": "Loan repayment", "amount": -500000 }
    ],
    "total": -500000
  },
  "net_change": 2920000,
  "opening_cash": 1000000,
  "closing_cash": 3920000,
  "period_start": "2024-01-01",
  "period_end": "2024-12-31"
}
```

The `/2023-11/admin/accounting/cash-flow` path is an alias for this endpoint.

---

### `GET` `/2023-11/admin/accounting/trial-balance`

Trial balance report. Cached per year unless `refresh=true`.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `year` | integer | Yes | — | Fiscal year (YYYY) |
| `refresh` | boolean | No | `false` | Force recalculation and cache invalidation |
| `detail` | boolean | No | `false` | Return individual account rows rather than root-level summary |
| `account_code` | string | No | — | Filter to a subtree of accounts |
| `download` | boolean | No | — | Download as file |
| `format` | string | No | `xlsx` | `xlsx`, `csv`, `pdf` |

**Success response** (`200 OK`):
```json
[
  { "code": "4", "name": "Tiers", "SID": 0, "SIC": 0, "MVTD": 5000000, "MVTC": 3000000, "SD": 2000000, "SC": 0, "SFD": 2000000, "SFC": 0, "year": 2024 },
  { "code": "", "name": "TOTAL", "SID": 0, "SIC": 0, "MVTD": 5000000, "MVTC": 5000000, "SD": 0, "SC": 0, "SFD": 0, "SFC": 0, "year": 2024 }
]
```

Column glossary: `SID`/`SIC` = initial debit/credit balances; `MVTD`/`MVTC` = period movements; `SD`/`SC` = period net debit/credit; `SFD`/`SFC` = final debit/credit balances.

---

### `GET` `/2023-11/admin/accounting/ledger-book`

General ledger. Returns paginated account summaries by default; use `account_code` to drill into individual entry lines.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `startDate` | date | No | start of year | — |
| `endDate` | date | No | end of year | — |
| `account_code` | string | No | — | Drill into a specific account |
| `per_page` | integer | No | 50 | max 200 |
| `download` | boolean | No | — | Trigger async export |
| `format` | string | No | `xlsx` | Export format |

```http
GET /2023-11/admin/accounting/ledger-book?startDate=2024-01-01&endDate=2024-12-31&account_code=411 HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — summary mode:
```json
{
  "data": [
    { "code": "411", "name": "Clients", "total_debit": 5000000, "total_credit": 3000000, "balance": 2000000, "entry_count": 14, "currency": "XAF" }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 50, "total": 8 }
}
```

Detail mode (when `account_code` is set):
```json
{
  "data": [
    { "id": "...", "label": "Invoice #001", "date": "2024-03-15", "debit": 500000, "credit": 0, "balance": 500000, "currency": "XAF" }
  ],
  "account": { "code": "411", "name": "Clients" },
  "opening_balance": 0,
  "currency": "XAF",
  "meta": { "current_page": 1, "per_page": 50, "total": 14 }
}
```

---

### `GET` `/2023-11/admin/accounting/dashboard`

All accounting KPIs in a single call: revenue/expense trends, cash position, receivables/payables aging, and action items.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `fiscal_year_id` | string | No | latest open year | Fiscal year to scope |

**Success response** (`200 OK`):
```json
{
  "revenue": { "current": 12000000, "previous": 10000000, "change_pct": 20.0 },
  "expenses": { "current": 7000000, "previous": 6500000, "change_pct": 7.7 },
  "net_profit": { "current": 5000000, "previous": 3500000, "change_pct": 42.9 },
  "cash_position": {
    "total": 3200000,
    "accounts": [{ "label": "Espèces", "balance": 1200000 }, { "label": "Banque", "balance": 2000000 }]
  },
  "receivables": {
    "total": 2000000,
    "overdue_90": 300000,
    "buckets": [
      { "label": "0-30", "amount": 1200000, "percentage": 60.0 },
      { "label": "31-60", "amount": 500000, "percentage": 25.0 },
      { "label": "61-90", "amount": 0, "percentage": 0 },
      { "label": "90+", "amount": 300000, "percentage": 15.0 }
    ]
  },
  "payables": { "total": 800000, "overdue_90": 0, "buckets": [] },
  "action_items": {
    "draft_entries": 3,
    "unreconciled_items": 12,
    "pending_statements": 1,
    "overdue_invoices": 5
  },
  "current_period": {
    "fiscal_year_name": "FY 2024",
    "fiscal_year_status": "open",
    "period_name": "March 2024",
    "period_status": "open",
    "days_until_close": 16
  }
}
```

---

## Reconciliation

### `GET` `/2023-11/admin/accounting/unmatched-entries`

List unreconciled book entries for a given account code prefix.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `account_code` | string | Yes | — | Account code prefix (e.g. `411`) |
| `start_date` | date | No | — | — |
| `end_date` | date | No | — | — |
| `per_page` | integer | No | 50 | — |

**Success response** (`200 OK`) — paginated book entries:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk501",
      "transaction_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "accounting_account_code": "411",
      "accounting_account_id": "01hqydxwtxdj3kmzp3bz7jk600",
      "label": "Invoice #001 - ACME Corp",
      "debit": 590000,
      "credit": 0,
      "entry_date": "2024-03-15",
      "journal_code": "VTE",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk200",
      "period_id": "01hqydxwtxdj3kmzp3bz7jk300",
      "metadata": null,
      "source": { "type": "invoice", "id": "01hqydxwtxdj3kmzp3bz7jk400" },
      "created_at": "2024-03-15T10:30:00.000000Z"
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 50, "total": 8 }
}
```

---

### `POST` `/2023-11/admin/accounting/reconcile`

Reconcile two or more book entries together. Auto-detects `full` vs `partial` status from balance.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `entry_ids` | array | Yes | min:2, must exist | ULIDs of book entries to reconcile |
| `status` | string | No | `full`/`partial` | Override auto-detection |

```http
POST /2023-11/admin/accounting/reconcile HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "entry_ids": ["01hqydxwtxdj3kmzp3bz7jk001", "01hqydxwtxdj3kmzp3bz7jk002"]
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "LET-001",
  "status": "full",
  "reconciled_at": "2024-03-15T10:30:00.000000Z",
  "lines": [
    { "id": "...", "book_entry_id": "01hqydxwtxdj3kmzp3bz7jk001", "amount": 500000 }
  ]
}
```

**Error responses**
- `422` Entry already fully reconciled, or `full` requested but entries are unbalanced.

---

### `GET` `/2023-11/admin/accounting/reconciliations`

Paginated list of reconciliations.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[status]` | string | No | — | `full`, `partial` |
| `filter[search]` | string | No | — | Search on code |
| `include` | string | No | — | `lines`, `lines.bookEntry`, `reconciledByUser` |
| `sort` | string | No | `-reconciled_at` | — |

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "code": "LET-001",
      "status": "full",
      "reconciled_at": "2024-03-15T10:30:00.000000Z",
      "reconciled_by": "01hqydxwtxdj3kmzp3bz000000",
      "lines": [
        { "id": "...", "book_entry_id": "01hqydxwtxdj3kmzp3bz7jk001", "amount": 500000 },
        { "id": "...", "book_entry_id": "01hqydxwtxdj3kmzp3bz7jk002", "amount": 500000 }
      ],
      "reconciled_by_user": { "id": "01hqydxwtxdj3kmzp3bz000000", "name": "Alice" }
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 15, "total": 32 }
}
```

---

### `GET` `/2023-11/admin/accounting/reconciliations/{reconciliation}`

Get a single reconciliation with its lines.

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "code": "LET-001",
  "status": "full",
  "reconciled_at": "2024-03-15T10:30:00.000000Z",
  "reconciled_by": "01hqydxwtxdj3kmzp3bz000000",
  "lines": [
    { "id": "...", "book_entry_id": "01hqydxwtxdj3kmzp3bz7jk001", "amount": 500000 },
    { "id": "...", "book_entry_id": "01hqydxwtxdj3kmzp3bz7jk002", "amount": 500000 }
  ],
  "reconciled_by_user": { "id": "01hqydxwtxdj3kmzp3bz000000", "name": "Alice" }
}
```

---

### `POST` `/2023-11/admin/accounting/unreconcile/{reconciliation}`

Remove a reconciliation and free its entries. Blocked if any entry is in a locked period.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `reconciliation` | string | Reconciliation ULID |

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`):
```json
{ "message": "Reconciliation removed." }
```

**Error responses**
- `422` Entries belong to a locked period.

---

## Treasury Statements (Bank Reconciliation)

### `GET` `/2023-11/admin/accounting/treasury-statements`

Paginated list of imported bank/treasury statements.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[treasury_account_id]` | string | No | — | — |
| `filter[status]` | string | No | — | `draft`, `in_progress`, `completed` |
| `include` | string | No | — | `treasuryAccount`, `importedByUser` |
| `sort` | string | No | `-created_at` | `period_start`, `period_end`, `status` |

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk100",
      "original_filename": "statement_march_2024.csv",
      "period_start": "2024-03-01",
      "period_end": "2024-03-31",
      "opening_balance": "1000000.00",
      "closing_balance": "1500000.00",
      "status": "in_progress",
      "reconciled_count": 20,
      "unreconciled_count": 8,
      "total_lines": 28,
      "imported_by": "01hqydxwtxdj3kmzp3bz000000",
      "created_at": "2024-03-16T10:00:00.000000Z",
      "treasury_account": {
        "id": "01hqydxwtxdj3kmzp3bz7jk100",
        "label": "Compte bancaire BNP",
        "type": "bank"
      }
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 15, "total": 5 }
}
```

---

### `POST` `/2023-11/admin/accounting/treasury-statements/preview`

Preview a CSV bank statement before import — returns parsed rows without saving.

**Request body** (`multipart/form-data`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | csv/txt, max 10 MB | — |
| `delimiter` | string | No | max:1 | CSV delimiter |

**Success response** (`200 OK`) — parsed row preview with detected columns:
```json
{
  "columns": ["Date", "Libellé", "Montant", "Solde"],
  "suggested_mapping": {
    "date": "Date",
    "label": "Libellé",
    "amount": "Montant",
    "running_balance": "Solde"
  },
  "sample_rows": [
    { "Date": "15/03/2024", "Libellé": "Virement client ACME", "Montant": "500000", "Solde": "1500000" },
    { "Date": "16/03/2024", "Libellé": "Frais bancaires", "Montant": "-2500", "Solde": "1497500" }
  ],
  "total_rows": 28
}
```

---

### `POST` `/2023-11/admin/accounting/treasury-statements`

Import a bank statement from CSV.

**Request body** (`multipart/form-data`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | csv/txt, max 10 MB | — |
| `treasury_account_id` | string | Yes | exists | Which treasury account |
| `column_mapping` | object | Yes | — | Maps CSV columns to fields |
| `column_mapping.date` | string | Yes | — | CSV column name for date |
| `column_mapping.label` | string | Yes | — | CSV column for label |
| `column_mapping.amount` | string | No | — | Single amount column |
| `column_mapping.debit` | string | No | — | Debit column (alternative to amount) |
| `column_mapping.credit` | string | No | — | Credit column |
| `column_mapping.reference` | string | No | — | Reference column |
| `column_mapping.value_date` | string | No | — | Value date column |
| `column_mapping.running_balance` | string | No | — | Running balance column |
| `delimiter` | string | No | — | CSV delimiter |
| `date_format` | string | No | — | Date format string |
| `opening_balance` | number | No | — | Opening balance for reconciliation check |
| `closing_balance` | number | No | — | Expected closing balance |

**Success response** (`201 Created`) — statement object with `treasury_account`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "treasury_account_id": "01hqydxwtxdj3kmzp3bz7jk100",
  "original_filename": "statement_march_2024.csv",
  "period_start": "2024-03-01",
  "period_end": "2024-03-31",
  "opening_balance": "1000000.00",
  "closing_balance": "1500000.00",
  "status": "draft",
  "reconciled_count": 0,
  "unreconciled_count": 28,
  "total_lines": 28,
  "import_format": "csv",
  "imported_by": "01hqydxwtxdj3kmzp3bz000000",
  "column_mapping": { "date": "Date", "label": "Libellé", "amount": "Montant" },
  "metadata": null,
  "created_at": "2024-03-16T10:00:00.000000Z",
  "updated_at": "2024-03-16T10:00:00.000000Z",
  "treasury_account": {
    "id": "01hqydxwtxdj3kmzp3bz7jk100",
    "label": "Compte bancaire BNP",
    "type": "bank",
    "accounting_account_code": "512"
  }
}
```

---

### `GET` `/2023-11/admin/accounting/treasury-statements/{statement}`

Get a statement with reconciliation summary.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `statement` | string | Statement ULID |

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "treasury_account_id": "...",
  "original_filename": "statement_march_2024.csv",
  "period_start": "2024-03-01",
  "period_end": "2024-03-31",
  "opening_balance": 1000000,
  "closing_balance": 1500000,
  "status": "in_progress",
  "summary": {
    "total_lines": 28,
    "reconciled_lines": 20,
    "unreconciled_lines": 8,
    "reconciliation_rate": 71.4
  }
}
```

---

### `DELETE` `/2023-11/admin/accounting/treasury-statements/{statement}`

Delete a draft or in-progress statement.

**Success response** (`200 OK`):
```json
{ "message": "Statement deleted." }
```

**Error responses**
- `422` Statement is completed and cannot be deleted.

---

### `GET` `/2023-11/admin/accounting/treasury-statements/{statement}/lines`

Paginated list of statement lines.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[is_reconciled]` | boolean | No | — | — |
| `include` | string | No | — | `bookEntry`, `bookEntry.accountingAccount`, `reconciledByUser` |
| `sort` | string | No | `line_date` | `line_date`, `amount`, `is_reconciled` |
| `per_page` | integer | No | 50 | — |

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk601",
      "treasury_statement_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "line_date": "2024-03-15",
      "value_date": "2024-03-15",
      "label": "Virement client ACME",
      "reference": "VIR-001",
      "amount": "500000.00",
      "running_balance": "1500000.00",
      "is_reconciled": false,
      "book_entry_id": null,
      "reconciled_at": null,
      "reconciled_by": null,
      "metadata": null,
      "created_at": "2024-03-16T10:00:00.000000Z"
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 50, "total": 28 }
}
```

---

### `GET` `/2023-11/admin/accounting/treasury-statements/{statement}/lines/{line}/suggestions`

Get up to 10 auto-match suggestions for a statement line.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `statement` | string | Statement ULID |
| `line` | string | Line ULID |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `limit` | integer | No | 5 | Max suggestions (capped at 10) |

**Success response** (`200 OK`) — array of suggested book entries with match score:
```json
[
  {
    "score": 95,
    "book_entry": {
      "id": "01hqydxwtxdj3kmzp3bz7jk501",
      "accounting_account_code": "512",
      "label": "Virement client ACME",
      "debit": 500000,
      "credit": 0,
      "entry_date": "2024-03-15",
      "journal_code": "BNQ",
      "source": { "type": "invoice", "id": "01hqydxwtxdj3kmzp3bz7jk400" }
    }
  },
  {
    "score": 72,
    "book_entry": {
      "id": "01hqydxwtxdj3kmzp3bz7jk502",
      "accounting_account_code": "512",
      "label": "Paiement facture #002",
      "debit": 500000,
      "credit": 0,
      "entry_date": "2024-03-14",
      "journal_code": "BNQ",
      "source": null
    }
  }
]
```

---

### `POST` `/2023-11/admin/accounting/treasury-statements/{statement}/match`

Match a statement line to a book entry.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `line_id` | string | Yes | exists | Statement line ULID |
| `book_entry_id` | string | Yes | exists | Book entry ULID to match |

**Success response** (`200 OK`) — updated line with `book_entry` loaded:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk601",
  "treasury_statement_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "line_date": "2024-03-15",
  "value_date": "2024-03-15",
  "label": "Virement client ACME",
  "reference": "VIR-001",
  "amount": "500000.00",
  "running_balance": "1500000.00",
  "is_reconciled": true,
  "book_entry_id": "01hqydxwtxdj3kmzp3bz7jk501",
  "reconciled_at": "2024-03-16T10:05:00.000000Z",
  "reconciled_by": "01hqydxwtxdj3kmzp3bz000000",
  "metadata": null,
  "book_entry": {
    "id": "01hqydxwtxdj3kmzp3bz7jk501",
    "accounting_account_code": "512",
    "label": "Virement client ACME",
    "debit": 500000,
    "credit": 0,
    "entry_date": "2024-03-15"
  }
}
```

---

### `POST` `/2023-11/admin/accounting/treasury-statements/{statement}/unmatch`

Remove a match from a statement line.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `line_id` | string | Yes | exists | Statement line ULID |

**Success response** (`200 OK`) — updated line with `is_reconciled: false`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk601",
  "treasury_statement_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "line_date": "2024-03-15",
  "label": "Virement client ACME",
  "amount": "500000.00",
  "is_reconciled": false,
  "book_entry_id": null,
  "reconciled_at": null,
  "reconciled_by": null
}
```

---

### `POST` `/2023-11/admin/accounting/treasury-statements/{statement}/auto-reconcile`

Run auto-reconciliation on all unreconciled lines using fuzzy matching.

**Query parameters / Request body**

| Field | Type | Required | Default | Description |
|---|---|---|---|---|
| `threshold` | integer | No | 70 | Minimum match score (50–100) |

**Success response** (`200 OK`):
```json
{
  "matched": 18,
  "skipped": 3,
  "statement": { "id": "...", "status": "in_progress" }
}
```

---

### `GET` `/2023-11/admin/accounting/treasury-statements/{statement}/report`

Reconciliation completion report: summary + unreconciled lines grouped by date.

**Success response** (`200 OK`):
```json
{
  "summary": { "total_lines": 28, "reconciled_lines": 25, "reconciliation_rate": 89.3 },
  "unreconciled_by_date": [
    { "line_date": "2024-03-14", "count": 1, "total_amount": -50000 }
  ]
}
```

---

## Third Parties

### `GET` `/2023-11/admin/accounting/third-parties`

List third parties (customers/suppliers derived from accounting auxiliary codes) with computed balances.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[type]` | string | No | — | `customer` or `supplier` |
| `filter[search]` | string | No | — | Search on name and auxiliary code |
| `start_date` | date | No | start of year | Balance computation start |
| `end_date` | date | No | end of year | Balance computation end |
| `include` | string | No | — | `entity` |
| `sort` | string | No | `auxiliary_code` | `name`, `auxiliary_code`, `created_at` |

**Success response** (`200 OK`) — paginated list with `debit`, `credit`, `balance`, `unreconciled` per third party:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "ACME Corp",
      "type": "customer",
      "auxiliary_code": "41100001",
      "account_code": "41100001",
      "debit": 2500000,
      "credit": 1000000,
      "balance": 1500000,
      "unreconciled": 800000
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 20, "total": 45 }
}
```

---

### `GET` `/2023-11/admin/accounting/third-parties/{thirdParty}`

Get a single third party with total balance and unreconciled amount.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `thirdParty` | string | Third party ULID |

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "ACME Corp",
  "type": "customer",
  "auxiliary_code": "41100001",
  "account_code": "41100001",
  "balance": 2500000,
  "unreconciled": 1200000
}
```

---

### `GET` `/2023-11/admin/accounting/third-parties/{thirdParty}/entries`

List book entries for a third party with reconciliation letter assigned.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `start_date` | date | No | start of year | — |
| `end_date` | date | No | end of year | — |
| `reconciled` | integer | No | — | `0` unreconciled only, `1` reconciled only |

**Success response** (`200 OK`):
```json
[
  {
    "id": "...",
    "date": "2024-03-15",
    "reference": "INV-001",
    "label": "Invoice #001",
    "debit": 500000,
    "credit": 0,
    "letter": "A",
    "source": "invoice"
  }
]
```

---

### `GET` `/2023-11/admin/accounting/aged-balance`

Aged balance report for customers or suppliers bucketed into 0-30, 31-60, 61-90, and 90+ days.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `type` | string | Yes | — | `customer` or `supplier` |
| `as_of_date` | date | No | today | Reference date for aging |

**Success response** (`200 OK`):
```json
{
  "as_of_date": "2024-03-31",
  "type": "customer",
  "data": [
    {
      "third_party": { "id": "...", "name": "ACME Corp" },
      "current": 1000000,
      "31_60": 500000,
      "61_90": 0,
      "over_90": 200000,
      "total": 1700000
    }
  ],
  "totals": { "current": 1000000, "31_60": 500000, "61_90": 0, "over_90": 200000, "total": 1700000 }
}
```

---

## Accounting Policy

### `GET` `/2023-11/admin/accounting/policies`

Return the accounting policy for the current company (auto-created with defaults if absent).

**Success response** (`200 OK`):
```json
{
  "data": {
    "id": "...",
    "company_id": "...",
    "execution_trigger": "on_invoice",
    "allow_manual_override": true,
    "draft_review_mandatory": false,
    "allow_posting_before_mapping": false,
    "stock_recognition": "on_delivery",
    "payment_accounting": "on_payment"
  }
}
```

---

### `PUT` `/2023-11/admin/accounting/policies`

Update the accounting policy.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `execution_trigger` | string | No | ExecutionTrigger enum | When book entries are auto-generated |
| `allow_manual_override` | boolean | No | — | — |
| `draft_review_mandatory` | boolean | No | — | — |
| `allow_posting_before_mapping` | boolean | No | — | — |
| `stock_recognition` | string | No | StockRecognition enum | — |
| `payment_accounting` | string | No | PaymentAccountingMode enum | — |

**Success response** (`200 OK`) — updated policy in `data` envelope:
```json
{
  "data": {
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "execution_trigger": "on_invoice",
    "allow_manual_override": true,
    "draft_review_mandatory": false,
    "allow_posting_before_mapping": false,
    "stock_recognition": "on_delivery",
    "payment_accounting": "on_payment",
    "updated_at": "2024-03-16T09:00:00.000000Z"
  }
}
```

---

## Audit Log

### `GET` `/2023-11/admin/accounting/audit-log`

Paginated accounting audit trail with tamper-evident hash chain.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[auditable_type]` | string | No | — | Model class (e.g. `AccountingTransaction`) |
| `filter[auditable_id]` | string | No | — | Model ULID |
| `filter[action]` | string | No | — | Action name (e.g. `entry_posted`, `entry_reversed`) |
| `filter[user_id]` | string | No | — | Filter by acting user |
| `filter[from]` | datetime | No | — | Entries created at or after |
| `filter[to]` | datetime | No | — | Entries created at or before |
| `sort` | string | No | `-created_at` | `created_at`, `action` |
| `per_page` | integer | No | 20 | — |

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "...",
      "company_id": "...",
      "action": "entry_posted",
      "auditable_type": "AccountingTransaction",
      "auditable_id": "...",
      "user_id": "...",
      "before": { "status": "draft" },
      "after": { "status": "posted" },
      "hash": "abc123...",
      "created_at": "2024-03-15T10:30:00.000000Z",
      "user": { "id": "...", "name": "Alice" }
    }
  ]
}
```

---

### `GET` `/2023-11/admin/accounting/audit-log/verify`

Verify the hash chain integrity of the audit log.

**Success response** (`200 OK`):
```json
{
  "valid": true,
  "entries_checked": 152,
  "first_broken_at": null
}
```

---

## Fiscal Exports

### `GET` `/2023-11/admin/accounting/exports/available`

Return export types available for the company's country.

**Success response** (`200 OK`):
```json
{
  "country_code": "FR",
  "exports": [
    { "type": "fec", "label": "Fichier des Écritures Comptables (FEC)", "format": "txt" },
    { "type": "audit_trail", "label": "Audit Trail", "format": "csv" }
  ]
}
```

---

### `GET` `/2023-11/admin/accounting/exports/{type}`

Generate and stream a country-specific fiscal export file.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `type` | string | Export type (e.g. `fec`, `audit_trail`) |

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `fiscal_year_id` | string | Yes | — | Fiscal year to export |

**Success response** (`200 OK`) — file download (format depends on export type).

---

### `GET` `/2023-11/admin/accounting/exports/{type}/validate`

Run country-specific validation rules and return a pass/fail report before exporting.

**Query parameters** — same as generate export.

**Success response** (`200 OK`):
```json
{
  "all_passed": true,
  "rules": [
    { "rule": "all_entries_posted", "passed": true, "message": null },
    { "rule": "no_numbering_gaps", "passed": true, "message": null }
  ]
}
```

---

### `GET` `/2023-11/admin/accounting/exports/fec`

Backward-compatible alias — delegates to `exports/{type}` with `type=fec`. Accepts the same `fiscal_year_id` query parameter.

**Success response** (`200 OK`) — `text/plain` (pipe-delimited FEC file). `Content-Disposition: attachment; filename="FEC_{SIREN}_{YYYYMMDD}.txt"`.

---

### `GET` `/2023-11/admin/accounting/exports/audit-trail`

Export a full audit trail.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `fiscal_year_id` | string | Yes | — | — |
| `format` | string | No | `csv` | `csv` or `xlsx` |

**Success response** (`200 OK`) — binary file download. `Content-Type` is `text/csv` or `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` depending on `format`. `Content-Disposition: attachment; filename="audit_trail_{fiscal_year}.{ext}"`.

---

## Numbering Gaps

### `GET` `/2023-11/admin/accounting/numbering-gaps`

Detect gaps in sequential `entry_number` per journal using SQL window functions.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `fiscal_year_id` | string | No | — | Scope to a fiscal year |

**Success response** (`200 OK`):
```json
{
  "gaps": [
    { "journal_code": "VTE", "gap_start": 15, "gap_end": 17, "count": 3 }
  ],
  "total_gaps": 1
}
```

---

## Tax Returns

### `GET` `/2023-11/admin/accounting/tax-returns/calculate`

Preview a tax return calculation for a period without persisting.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `period_start` | date | Yes | — | — |
| `period_end` | date | Yes | after period_start | — |
| `tax_type` | string | No | — | Filter to a specific tax type |

**Success response** (`200 OK`):
```json
{
  "collected": 1500000,
  "deductible": 300000,
  "net": 1200000,
  "is_due": true,
  "is_credit": false,
  "details": {}
}
```

---

### `POST` `/2023-11/admin/accounting/tax-returns/file`

File a tax return: creates a `TaxReturn` record and a settlement accounting entry.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `tax_type` | string | Yes | — | e.g. `vat`, `income_tax` |
| `period_start` | date | Yes | — | — |
| `period_end` | date | Yes | after period_start | — |
| `filing_reference` | string | No | — | External filing reference number |

```http
POST /2023-11/admin/accounting/tax-returns/file HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "tax_type": "vat",
  "period_start": "2024-03-01",
  "period_end": "2024-03-31",
  "filing_reference": "DGI-2024-03-001"
}
```

**Success response** (`201 Created`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "tax_type": "vat",
  "period_start": "2024-03-01",
  "period_end": "2024-03-31",
  "collected_amount": 1500000,
  "deductible_amount": 300000,
  "net_amount": 1200000,
  "is_due": true,
  "is_credit": false,
  "status": "filed",
  "return_number": "VAT-2024-003",
  "filing_reference": "DGI-2024-03-001",
  "filed_at": "2024-04-01T10:00:00.000000Z"
}
```

---

### `GET` `/2023-11/admin/accounting/tax-returns/history`

Paginated list of filed tax returns.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[fiscal_year_id]` | string | No | — | — |
| `filter[tax_type]` | string | No | — | — |
| `filter[status]` | string | No | — | `filed`, `paid`, `cancelled` |
| `filter[search]` | string | No | — | Search return_number, filing_reference |
| `sort` | string | No | `-period_start` | `period_start`, `period_end`, `filed_at`, `net_amount` |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk200",
      "tax_type": "vat",
      "period_start": "2024-03-01",
      "period_end": "2024-03-31",
      "collected_amount": "1500000.00",
      "deductible_amount": "300000.00",
      "net_amount": "1200000.00",
      "status": "filed",
      "return_number": "VAT-2024-003",
      "filing_reference": "DGI-2024-03-001",
      "filed_at": "2024-04-01T10:00:00.000000Z"
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 15, "total": 3 }
}
```

---

### `GET` `/2023-11/admin/accounting/tax-returns/{taxReturn}`

Get a single tax return with settlement transaction entries.

**Success response** (`200 OK`) — TaxReturn resource with `fiscal_year`, `settlement_transaction.entries`, `filed_by_user`:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "fiscal_year_id": "01hqydxwtxdj3kmzp3bz7jk200",
  "period_id": "01hqydxwtxdj3kmzp3bz7jk300",
  "tax_type": "vat",
  "period_start": "2024-03-01",
  "period_end": "2024-03-31",
  "collected_amount": "1500000.00",
  "deductible_amount": "300000.00",
  "net_amount": "1200000.00",
  "status": "filed",
  "return_number": "VAT-2024-003",
  "filing_reference": "DGI-2024-03-001",
  "settlement_transaction_id": "01hqydxwtxdj3kmzp3bz7jk800",
  "filed_by": "01hqydxwtxdj3kmzp3bz000000",
  "filed_at": "2024-04-01T10:00:00.000000Z",
  "details": {},
  "metadata": null,
  "fiscal_year": { "id": "01hqydxwtxdj3kmzp3bz7jk200", "name": "FY 2024", "status": "open" },
  "settlement_transaction": {
    "id": "01hqydxwtxdj3kmzp3bz7jk800",
    "entry_number": "OD-2024-005",
    "status": "posted",
    "entries": [
      { "id": "...", "accounting_account_code": "4431", "label": "VAT collected", "debit": 1500000, "credit": 0 },
      { "id": "...", "accounting_account_code": "4452", "label": "VAT deductible", "debit": 0, "credit": 300000 },
      { "id": "...", "accounting_account_code": "4441", "label": "VAT payable",    "debit": 0, "credit": 1200000 }
    ]
  },
  "filed_by_user": { "id": "01hqydxwtxdj3kmzp3bz000000", "name": "Alice" }
}
```

---

### `GET` `/2023-11/admin/accounting/tax-returns/dashboard`

Tax position summary: current month position + filed returns summary for the year.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `fiscal_year_id` | string | No | — | Filter filed returns |

**Success response** (`200 OK`):
```json
{
  "current_position": { "period_start": "2024-03-01", "period_end": "2024-03-31", "collected": 1500000, "deductible": 300000, "net": 1200000, "is_due": true, "is_credit": false },
  "filed_summary": { "total_filed": 3, "total_paid": 2, "total_pending": 1, "total_collected": 4500000, "total_deductible": 900000, "total_net": 3600000 },
  "latest_return": { "id": "...", "return_number": "VAT-2024-003", "net_amount": 1200000, "status": "filed", "filed_at": "2024-04-01T10:00:00.000000Z" }
}
```

---

### `GET` `/2023-11/admin/accounting/tax-returns/declaration-forms`

List country-specific declaration form templates available.

**Success response** (`200 OK`) — array of form descriptors with fields:
```json
[
  {
    "key": "cm_dsce",
    "label": "Déclaration statistique et comptable (DSCE)",
    "country_code": "CM",
    "lines": [
      { "key": "ca_ht", "label": "Chiffre d'affaires HT", "account_prefix": "70", "operation": "credit" },
      { "key": "tva_collectee", "label": "TVA collectée", "account_prefix": "4431", "operation": "credit" },
      { "key": "tva_deductible", "label": "TVA déductible", "account_prefix": "4452", "operation": "debit" }
    ]
  }
]
```

---

### `GET` `/2023-11/admin/accounting/tax-returns/declaration-calculate`

Calculate amounts for a specific declaration form.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `form` | string | Yes | — | Form key (from declaration-forms) |
| `period_start` | date | Yes | — | — |
| `period_end` | date | Yes | — | — |

**Success response** (`200 OK`) — form with computed line amounts:
```json
{
  "form": "cm_dsce",
  "label": "Déclaration statistique et comptable (DSCE)",
  "period_start": "2024-03-01",
  "period_end": "2024-03-31",
  "lines": [
    { "key": "ca_ht", "label": "Chiffre d'affaires HT", "amount": 5000000 },
    { "key": "tva_collectee", "label": "TVA collectée", "amount": 962500 },
    { "key": "tva_deductible", "label": "TVA déductible", "amount": 150000 }
  ],
  "totals": {
    "tva_net": 812500,
    "is_due": true
  }
}
```

---

### `GET` `/2023-11/admin/accounting/tax-returns/detail-report`

Paginated list of individual book entries on tax accounts for a period.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `period_start` | date | Yes | — | — |
| `period_end` | date | Yes | — | — |
| `account` | string | No | — | Filter to specific account |
| `page` | integer | No | 1 | — |
| `per_page` | integer | No | 50 | max 200 |

**Success response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk501",
      "transaction_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "accounting_account_code": "4431",
      "label": "TVA sur vente - Invoice #001",
      "debit": 0,
      "credit": 962500,
      "entry_date": "2024-03-15",
      "journal_code": "VTE",
      "source": { "type": "invoice", "id": "01hqydxwtxdj3kmzp3bz7jk400" }
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 50, "total": 42 }
}
```

---

## Reconstruction

### `POST` `/2023-11/admin/accounting/reconstruction/scan`

Analyze historical business data and return a quality report before generating accounting entries.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`):
```json
{
  "data": {
    "periodStart": "2023-01-01",
    "periodEnd": "2024-03-15",
    "totalOperations": 1450,
    "estimatedEntries": 3200,
    "qualityScore": "medium",
    "details": { "orders": 800, "bills": 150, "invoices": 400, "payments": 100 },
    "issues": [
      { "severity": "warning", "category": "Orphan payments", "affectedRecords": 5, "blocking": false }
    ]
  }
}
```

---

### `POST` `/2023-11/admin/accounting/reconstruction/generate`

Queue an async job to generate draft accounting entries from all historical business data. Returns `409` if a job is already running.

**Request body:** None — no body required for this action endpoint.

**Success response** (`202 Accepted`):
```json
{
  "message": "Generation queued.",
  "data": { "id": "...", "status": "queued" }
}
```

---

### `GET` `/2023-11/admin/accounting/reconstruction/status`

Poll the latest reconstruction job status.

**Success response** (`200 OK`):
```json
{
  "data": {
    "id": "...",
    "status": "completed",
    "progress": 100,
    "result": { "entries_created": 3200, "skipped": 12 }
  }
}
```

---

## Accounting Imports

### `POST` `/2023-11/admin/accounting/imports/{type}/detect`

Phase 1: upload a spreadsheet and receive a suggested column mapping.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `type` | string | Import type. Currently only `chart-of-accounts` is supported. |

**Request body** (`multipart/form-data`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | csv/txt/xlsx/xls/ods, max 10 MB | — |

**Success response** (`200 OK`) — suggested `column_mapping` and sample rows:
```json
{
  "columns": ["Date", "Libellé", "Débit", "Crédit", "Solde"],
  "suggested_mapping": {
    "date": "Date",
    "label": "Libellé",
    "debit": "Débit",
    "credit": "Crédit",
    "running_balance": "Solde"
  },
  "sample_rows": [
    { "Date": "01/01/2024", "Libellé": "Ouverture", "Débit": "", "Crédit": "500000", "Solde": "500000" }
  ],
  "total_rows": 42
}
```

---

### `POST` `/2023-11/admin/accounting/imports/{type}/preview`

Phase 2: dry-run import with a confirmed column mapping. Returns parsed/validated rows without persisting.

**Request body** (`multipart/form-data`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | — | — |
| `column_mapping` | object | No | — | Confirmed column mapping |

**Success response** (`200 OK`) — preview with `valid`, `invalid`, and `errors` arrays:
```json
{
  "valid": [
    { "row": 2, "code": "411", "name": "Clients", "account_type": "Asset", "parent_account_code": "41" },
    { "row": 3, "code": "571", "name": "Caisse", "account_type": "Asset", "parent_account_code": "57" }
  ],
  "invalid": [
    { "row": 5, "errors": ["The code field is required."] }
  ],
  "total": 3,
  "valid_count": 2,
  "invalid_count": 1
}
```

---

### `POST` `/2023-11/admin/accounting/imports/{type}`

Phase 3: parse, validate, and persist.

**Request body** (`multipart/form-data`)

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `file` | file | Yes | csv/txt/xlsx/xls/ods, max 10 MB | Spreadsheet to import |
| `column_mapping` | object | No | — | Confirmed column mapping from detect phase; keys are field names, values are CSV header names |
| `country_code` | string | No | size:2 | Two-letter country code; defaults to `CM` |

**Success response** (`200 OK`):
```json
{ "inserted": 280, "updated": 15, "skipped": 2, "errors": [] }
```

---

## Treasury Accounts

The `/2023-11/admin/treasury-accounts` and `/2023-11/admin/treasuryAccounts` paths are functionally identical (the camelCase path is a legacy alias). Use `treasury-accounts` for new integrations.

### `GET` `/2023-11/admin/treasury-accounts`

Paginated list of treasury accounts (cash registers, bank accounts).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[label]` | string | No | — | Partial label match |
| `filter[type]` | string | No | — | e.g. `cash`, `bank` |
| `filter[shop_id]` | string | No | — | Filter by shop (null entries always included) |
| `filter[accounting_account_code]` | string | No | — | Partial match |
| `filter[search]` | string | No | — | Search label, type, account code |
| `include` | string | No | — | `shop`, `company`, `paymentsCount` |
| `sort` | string | No | — | `created_at`, `balance`, `label`, `type` |
| `per_page` | integer | No | 15 | — |
| `paginate` | string | No | — | `false` for all |

```http
GET /2023-11/admin/treasury-accounts HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`) — paginated list of treasury account resources with `medias`, `shop`:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "shop_id": null,
      "label": "Caisse principale",
      "type": "cash",
      "accounting_account_code": "571",
      "accounting_account_id": "01hqydxwtxdj3kmzp3bz7jk100",
      "journal_id": null,
      "balance": "50000.00",
      "logoUrl": null,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-03-15T10:30:00.000000Z",
      "medias": [],
      "shop": null
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 15, "total": 3 }
}
```

---

### `POST` `/2023-11/admin/treasury-accounts`

Create a treasury account.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `label` | string | Yes | max:255 | Display name |
| `type` | string | Yes | max:255 | e.g. `cash`, `bank`, `mobile_money` |
| `accounting_account_code` | string | Yes | max:255 | Linked chart of accounts code |
| `accounting_account_id` | string | No | exists | Optional explicit account FK |
| `balance` | number | Yes | numeric | Initial balance |
| `shop_id` | string | No | exists | Scope to a shop |
| `medias` | array | No | — | Array of `{link}` objects for logo |
| `uploadImageUrl` | string | No | — | Logo URL |
| `uploadImageBase64` | string | No | — | Logo as base64 |

```http
POST /2023-11/admin/treasury-accounts HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "label": "Caisse principale",
  "type": "cash",
  "accounting_account_code": "571",
  "balance": 50000
}
```

**Success response** (`200 OK`) — TreasuryAccountResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "label": "Caisse principale",
  "type": "cash",
  "accounting_account_code": "571",
  "accounting_account_id": "01hqydxwtxdj3kmzp3bz7jk100",
  "journal_id": null,
  "balance": "50000.00",
  "logoUrl": null,
  "metadata": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "medias": [],
  "shop": null
}
```

---

### `GET` `/2023-11/admin/treasury-accounts/{treasuryAccount}`

Get a single treasury account.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `treasuryAccount` | string | Treasury account ULID |

**Success response** (`200 OK`):
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "label": "Caisse principale",
  "type": "cash",
  "accounting_account_code": "571",
  "balance": 50000,
  "metadata": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "medias": [],
  "shop": null
}
```

---

### `PUT` `/2023-11/admin/treasury-accounts/{treasuryAccount}`

Update a treasury account. Only admin/superadmin users can modify `balance`.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `label` | string | No | max:255 | — |
| `type` | string | No | max:255 | — |
| `accounting_account_code` | string | No | max:255 | — |
| `accounting_account_id` | string | No | exists | — |
| `balance` | number | No | — | Admin-only field |

**Success response** (`200 OK`) — updated TreasuryAccountResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "shop_id": null,
  "label": "Caisse principale (updated)",
  "type": "cash",
  "accounting_account_code": "571",
  "accounting_account_id": "01hqydxwtxdj3kmzp3bz7jk100",
  "balance": "75000.00",
  "logoUrl": null,
  "metadata": null,
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "medias": [],
  "shop": null
}
```

**Error responses**
- `403` Non-admin attempted to change balance.

---

### `DELETE` `/2023-11/admin/treasury-accounts/{treasuryAccount}`

Delete a treasury account.

**Success response** (`200 OK`):
```json
{ "message": "Treasury account deleted." }
```

---

### `GET` `/2023-11/admin/treasury-accounts/export/fields`

Return the list of columns available for export.

**Success response** (`200 OK`):
```json
{ "columns": ["id", "label", "type", "balance", "accounting_account_code", "created_at"] }
```

---

### `POST` `/2023-11/admin/treasury-accounts/export`

Trigger an async export of treasury transactions.

**Request body**

| Field | Type | Required | Default | Description |
|---|---|---|---|---|
| `format` | string | No | `xlsx` | `xlsx`, `csv` |
| `columns` | array | No | all | Column subset from export/fields |
| Other filter params | — | No | — | Same filters as index |

**Success response** — async export job response (see export conventions).

---

## Revenue Types

### `GET` `/2023-11/admin/revenue-types`

Paginated list of revenue types (accounting mappings for order revenue recognition).

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search on name |
| `sort` | string | No | — | `created_at`, `updated_at`, `name` |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`) — paginated list of revenue type resources:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "name": "Ventes produits",
      "handle": "ventes-produits",
      "description": "Revenue from product sales",
      "debit_code": "411",
      "credit_code": "701",
      "metadata": null,
      "icon": "Tag",
      "created_at": "2024-01-01T00:00:00.000000Z",
      "updated_at": "2024-01-01T00:00:00.000000Z"
    }
  ],
  "links": {},
  "meta": { "current_page": 1, "per_page": 15, "total": 4 }
}
```

---

### `POST` `/2023-11/admin/revenue-types`

Create a revenue type.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | — | Display name |
| `handle` | string | Yes | unique per company | Slug identifier |
| `debit_code` | string | No | — | Accounting debit account code |
| `credit_code` | string | No | different from debit_code | Accounting credit account code |
| `description` | string | No | — | — |
| `metadata` | object | No | — | — |
| `icon` | string/file | No | — | Icon URL or uploaded file |

**Success response** (`200 OK`) — RevenueTypeResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Ventes produits",
  "handle": "ventes-produits",
  "description": "Revenue from product sales",
  "debit_code": "411",
  "credit_code": "701",
  "metadata": null,
  "icon": "Tag",
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `400` `credit_code` equals `debit_code`, or handle already exists.

---

### `PUT` `/2023-11/admin/revenue-types/{revenueType}`

Update a revenue type. `handle` cannot be changed after creation.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | — | Display name |
| `debit_code` | string | No | — | Accounting debit account code |
| `credit_code` | string | No | different from debit_code | Accounting credit account code |
| `description` | string | No | — | Optional description |
| `metadata` | object | No | — | Arbitrary metadata |
| `icon` | string/file | No | — | Icon name, URL, or uploaded file |

**Success response** (`200 OK`) — updated RevenueTypeResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "Ventes produits (updated)",
  "handle": "ventes-produits",
  "description": "Updated description",
  "debit_code": "411",
  "credit_code": "7011",
  "metadata": null,
  "icon": "ShoppingCart",
  "updated_at": "2024-03-16T09:00:00.000000Z"
}
```

---

### `DELETE` `/2023-11/admin/revenue-types/{revenueType}`

Delete a revenue type.

**Success response** (`204 No Content`)

---

## Wallet (WhatsApp Message Credits)

### `GET` `/2023-11/admin/wallet`

Get the company's current message credit balance and recent transactions.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `limit` | integer | No | 10 | Number of recent transactions to include |

```http
GET /2023-11/admin/wallet HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response** (`200 OK`):
```json
{
  "balance": 850.0,
  "transactions": [
    { "id": "...", "type": "credit", "amount": 1000, "balance_after": 1000, "reference": null, "created_at": "2024-03-10T08:00:00.000000Z" },
    { "id": "...", "type": "debit",  "amount": 150,  "balance_after": 850,  "reference": null, "created_at": "2024-03-12T14:00:00.000000Z" }
  ]
}
```

---

### `GET` `/2023-11/admin/wallet/transactions`

Paginated list of all wallet transactions.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `type` | string | No | — | `credit` or `debit` |
| `from` | date | No | — | Filter created_at >= date |
| `to` | date | No | — | Filter created_at <= date |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`) — paginated list:
```json
{
  "data": [
    { "id": "...", "type": "credit", "amount": 1000, "balance_after": 1000, "reference": null, "created_at": "2024-03-10T08:00:00.000000Z" }
  ],
  "meta": { "current_page": 1, "total": 5 }
}
```

---

### `POST` `/2023-11/admin/wallet/topup`

Initiate a wallet top-up. Creates a pending wallet transaction and returns a payment URL. The balance is credited once payment is confirmed via webhook.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `price` | number | Yes | min:0.01 | Purchase price in fiat |
| `variant_id` | string | Yes | — | Product variant ULID containing `messages` in metadata |
| `currency` | string | Yes | max:3 | ISO 4217 currency code (e.g. `XAF`) |
| `order_id` | string | No | — | Associated order ULID |
| `lang` | string | No | app locale | Payment page language |

```http
POST /2023-11/admin/wallet/topup HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "price": 5000,
  "variant_id": "01hqydxwtxdj3kmzp3bz7jk001",
  "currency": "XAF"
}
```

**Success response** (`201 Created`) — payment gateway response with checkout URL:
```json
{
  "payment_url": "https://pay.pawapay.io/checkout/abc123",
  "transaction_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "amount": 5000,
  "currency": "XAF",
  "messages": 500,
  "status": "pending"
}
```

**Error responses**
- `422` Unsupported currency or invalid product variant.
- `500` Payment gateway creation failed.

---

## Document Number Formats

### `GET` `/2023-11/admin/document_number_formats`

Paginated list of document number format rules.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[search]` | string | No | — | Search on document_type, pattern |
| `sort` | string | No | — | `created_at`, `document_type`, `pattern` |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`) — paginated list:
```json
{
  "data": [
    {
      "id": "...",
      "company_id": "...",
      "document_type": "invoice",
      "pattern": "INV-{YYYY}-{NNN}",
      "counter_digits": 3,
      "start_from": 1,
      "reset_yearly": true,
      "reset_monthly": false
    }
  ]
}
```

---

### `POST` `/2023-11/admin/document_number_formats`

Create a document number format. Only one format per `document_type` is allowed.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `document_type` | string | Yes | unique per company | e.g. `invoice`, `order`, `bill` |
| `pattern` | string | Yes | — | Pattern string with tokens like `{YYYY}`, `{MM}`, `{NNN}` |
| `counter_digits` | integer | Yes | min:1, max:10 | Width of the sequential counter |
| `start_from` | integer | Yes | min:1 | Initial counter value |
| `reset_yearly` | boolean | No | — | Reset counter each year |
| `reset_monthly` | boolean | No | — | Reset counter each month |

```http
POST /2023-11/admin/document_number_formats HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "document_type": "invoice",
  "pattern": "INV-{YYYY}-{NNN}",
  "counter_digits": 5,
  "start_from": 1,
  "reset_yearly": true,
  "reset_monthly": false
}
```

**Success response** (`201 Created`) — DocumentNumberFormatResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "document_type": "invoice",
  "pattern": "INV-{YYYY}-{NNN}",
  "counter_digits": 5,
  "start_from": 1,
  "reset_yearly": true,
  "reset_monthly": false,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z"
}
```

**Error responses**
- `422` Format for this document_type already exists.

---

### `PUT` `/2023-11/admin/document_number_formats/{documentNumberFormat}`

Update a document number format. `document_type` cannot be changed.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `pattern` | string | Yes | — | — |
| `counter_digits` | number | Yes | min:1, max:10 | — |
| `start_from` | number | Yes | min:1 | — |
| `reset_yearly` | boolean | No | — | — |
| `reset_monthly` | boolean | No | — | — |

**Success response** (`200 OK`) — updated DocumentNumberFormatResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "document_type": "invoice",
  "pattern": "INV-{YYYY}-{MM}-{NNNNN}",
  "counter_digits": 5,
  "start_from": 1,
  "reset_yearly": true,
  "reset_monthly": true,
  "updated_at": "2024-03-16T09:00:00.000000Z"
}
```

---

## Taxes

### `GET` `/2023-11/admin/taxes`

Paginated list of taxes.

**Query parameters**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `filter[name]` | string | No | — | Partial match |
| `filter[code]` | string | No | — | Partial match |
| `filter[search]` | string | No | — | Search on code and name |
| `filter[deleted]` | integer | No | — | `0` active only, `1` deleted only |
| `include` | string | No | — | `orders`, `bills` |
| `sort` | string | No | — | `created_at`, `rate`, `name`, `code` |
| `per_page` | integer | No | 15 | — |

**Success response** (`200 OK`) — paginated TaxResource list:
```json
{
  "data": [
    {
      "id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "company_id": "...",
      "name": "TVA 19.25%",
      "code": "TVA",
      "rate": 19.25,
      "fixed_amount": 0,
      "is_withholding": false,
      "include_in_base_for_subsequent_taxes": false,
      "tax_order": 1,
      "metadata": null,
      "created_at": "2024-01-01T00:00:00.000000Z"
    }
  ]
}
```

---

### `POST` `/2023-11/admin/taxes`

Create a tax.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | Yes | — | Display name |
| `code` | string | Yes | — | Short code (e.g. `TVA`) |
| `rate` | number | Yes | — | Percentage rate (e.g. `19.25`) |
| `fixed_amount` | number | No | — | Fixed amount (alternative to rate) |
| `is_withholding` | boolean | No | — | Whether it is a withholding tax |
| `include_in_base_for_subsequent_taxes` | boolean | No | — | Include in base for cascaded taxes |
| `tax_order` | integer | No | — | Calculation order; defaults to max+1 |
| `metadata` | object | No | — | — |

```http
POST /2023-11/admin/taxes HTTP/1.1
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Content-Type: application/json

{
  "name": "TVA 19.25%",
  "code": "TVA",
  "rate": 19.25,
  "fixed_amount": 0,
  "is_withholding": false,
  "tax_order": 1
}
```

**Success response** (`200 OK`) — TaxResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "TVA 19.25%",
  "code": "TVA",
  "rate": 19.25,
  "fixed_amount": 0,
  "is_withholding": false,
  "is_default_for_products": false,
  "is_active": true,
  "include_in_base_for_subsequent_taxes": false,
  "tax_order": 1,
  "collected_account": null,
  "deductible_account": null,
  "settlement_account": null,
  "metadata": null,
  "created_at": "2024-03-15T10:30:00.000000Z",
  "updated_at": "2024-03-15T10:30:00.000000Z",
  "deleted_at": null
}
```

---

### `GET` `/2023-11/admin/taxes/{tax}`

Get a single tax.

**Path parameters**

| Name | Type | Description |
|---|---|---|
| `tax` | string | Tax ULID |

**Success response** (`200 OK`) — TaxResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "TVA 19.25%",
  "code": "TVA",
  "rate": 19.25,
  "fixed_amount": 0,
  "is_withholding": false,
  "is_default_for_products": false,
  "is_active": true,
  "include_in_base_for_subsequent_taxes": false,
  "tax_order": 1,
  "collected_account": "4431",
  "deductible_account": "4452",
  "settlement_account": "4441",
  "metadata": null,
  "created_at": "2024-01-01T00:00:00.000000Z",
  "updated_at": "2024-01-01T00:00:00.000000Z",
  "deleted_at": null
}
```

---

### `PUT` `/2023-11/admin/taxes/{tax}`

Update a tax.

**Request body**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `name` | string | No | max:255 | Display name |
| `code` | string | Yes | max:255, unique per company | Short code (e.g. `TVA`); must be unique for this company's active taxes |
| `rate` | number | No | between:-100,100; required if no fixed_amount | Percentage rate |
| `fixed_amount` | number | No | numeric; required if no rate | Fixed amount per unit |
| `is_withholding` | boolean | No | — | Whether it is a withholding tax |
| `include_in_base_for_subsequent_taxes` | boolean | No | — | Include in base for cascaded tax calculation |
| `tax_order` | integer | No | — | Calculation order |
| `metadata` | object | No | — | Arbitrary metadata |

**Success response** (`200 OK`) — updated TaxResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "TVA 19.25%",
  "code": "TVA",
  "rate": 19.25,
  "fixed_amount": 0,
  "is_withholding": false,
  "is_default_for_products": true,
  "is_active": true,
  "include_in_base_for_subsequent_taxes": false,
  "tax_order": 1,
  "collected_account": "4431",
  "deductible_account": "4452",
  "settlement_account": "4441",
  "metadata": null,
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "deleted_at": null
}
```

---

### `DELETE` `/2023-11/admin/taxes/{tax}`

Soft-delete a tax.

**Success response** (`200 OK`):
```json
{ "message": "Tax deleted" }
```

**Error responses**
- `404` Tax not found.

---

### `POST` `/2023-11/admin/taxes/{tax}/restore`

Restore a soft-deleted tax.

**Request body:** None — no body required for this action endpoint.

**Success response** (`200 OK`) — restored TaxResource:
```json
{
  "id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
  "name": "TVA 19.25%",
  "code": "TVA",
  "rate": 19.25,
  "fixed_amount": 0,
  "is_withholding": false,
  "is_default_for_products": false,
  "is_active": true,
  "include_in_base_for_subsequent_taxes": false,
  "tax_order": 1,
  "collected_account": "4431",
  "deductible_account": "4452",
  "settlement_account": "4441",
  "metadata": null,
  "updated_at": "2024-03-16T09:00:00.000000Z",
  "deleted_at": null
}
```

**Error responses**
- `404` Tax not found.
