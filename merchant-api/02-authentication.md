---
title: "Authentication & Identity"
description: "This domain covers everything needed to establish and maintain a session with the Genuka API. It includes user registration (with company creation), email/passw"
sidebarTitle: "Authentication"
---

This domain covers everything needed to establish and maintain a session with the Genuka API. It includes user registration (with company creation), email/password login, PIN-based login, password reset via OTP, email verification, and multi-company management. After a successful login or registration, the server returns a Passport `accessToken` (plain-text bearer token) and the resolved `companyId` that must be sent as the `X-Company` header on all subsequent requests.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

---

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/auth/login` | Redirect to the frontend login page (not an API endpoint) |
| `POST` | `/2023-11/auth/login` | Authenticate with email and password |
| `GET` | `/2023-11/auth/register` | Redirect to the frontend register page (not an API endpoint) |
| `POST` | `/2023-11/auth/register` | Register a new company and admin user |
| `POST` | `/2023-11/auth/register-additional-company` | Add a second company to an existing authenticated user |
| `POST` | `/2023-11/auth/login-with-pin` | Authenticate using a company code and PIN |
| `POST` | `/2023-11/auth/verify-company-code` | Look up a company by its short code (first step of PIN login) |
| `POST` | `/2023-11/auth/logout` | Revoke the current access token |
| `POST` | `/2023-11/auth/password/email` | Request a password-reset OTP via email |
| `POST` | `/2023-11/auth/password/reset` | Reset password using the OTP |
| `POST` | `/2023-11/auth/email/send-verification` | Resend the email-verification OTP |
| `POST` | `/2023-11/auth/email/verify` | Verify email address using the OTP |
| `GET` | `/2023-11/user` | Return the authenticated user's profile |
| `GET` | `/2023-11/user/companies` | List all companies the authenticated user belongs to |
| `PUT` | `/2023-11/user/metadata` | Merge arbitrary key/value pairs into the user's metadata |
| `POST` | `/2023-11/user/set-pin` | Set or update the user's numeric PIN |
| `POST` | `/2023-11/user/switch-company` | Switch the active company and receive a fresh token |
| `POST` | `/2023-11/user/refresh-token` | Re-issue the current token with up-to-date permissions |
| `GET` | `/2023-11/invitations` | Get invitation details by token (public) |
| `POST` | `/2023-11/invitations/accept` | Accept a company invitation |
| `POST` | `/2023-11/invitations/decline` | Decline a company invitation |

---

## Authentication

### `GET` `/2023-11/auth/login`
Redirects the browser to the configured frontend login page. This is a convenience redirect for web flows and **does not return JSON**. Clients integrating the API directly should use `POST /2023-11/auth/login`.

---

### `POST` `/2023-11/auth/login`
Authenticate a user with their email and password. Returns a Passport bearer token and basic company context.

- **Query parameters:** none
- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `email` | string | yes | valid email | The user's email address |
| `password` | string | yes | non-empty | The user's plaintext password |

**Request example**

```http
POST /2023-11/auth/login HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "email": "demo@genuka.com",
    "password": "password"
}
```

**Success response — `200 OK`**

```json
{
    "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
    "token": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Jean ONANA",
        "scopes": ["products.*", "orders.*"],
        "expires_at": null
    },
    "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_code": "PETC001",
    "companies": [
        {
            "id": "01hqydxwtxdj3kmzp3bz7jk73g",
            "name": "Pet Clinic",
            "logoUrl": "https://cdn.genuka.com/media/logo.png",
            "company_code": "PETC001",
            "role": "admin",
            "last_used_at": "2024-03-15T10:23:45.000000Z"
        }
    ]
}
```

> The `companies` array is only present when the user belongs to more than one company.

**Error responses**

- `401 Unauthorized` — invalid email/password combination
  ```json
  { "message": "Invalid email or password.", "code": 401 }
  ```

---

### `GET` `/2023-11/auth/register`
Redirects the browser to the configured frontend registration page. Not a JSON API endpoint.

---

### `POST` `/2023-11/auth/register`
Register a new company and its first admin user in a single request. Creates the company, creates the user (role `admin`), queues a welcome notification, creates a trial subscription, and returns a bearer token.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company` | object | yes | — | Company to create |
| `company.name` | string | yes | non-empty | Company display name |
| `company.currency_code` | string | yes | non-empty | ISO 4217 currency code (e.g. `XAF`, `USD`) |
| `company.currency_name` | string | no | — | Human-readable currency name |
| `company.description` | string | no | — | Company description |
| `company.metadata` | object | no | — | Arbitrary metadata including `rccm`, `fiscalID` |
| `company.logoUrl` | string | no | valid URL | URL to fetch and attach as company logo |
| `user` | object | yes | — | Admin user to create |
| `user.first_name` | string | yes | non-empty | First name |
| `user.last_name` | string | yes | non-empty | Last name |
| `user.email` | string | yes | valid email, unique | Email address (becomes the login) |
| `user.password` | string | yes | non-empty | Plaintext password (hashed on save) |
| `user.phone` | string | no | — | Phone number, may be `null` |

**Request example**

```http
POST /2023-11/auth/register HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "company": {
        "name": "Pet Clinic",
        "currency_code": "XAF"
    },
    "user": {
        "first_name": "Jean",
        "last_name": "ONANA",
        "email": "jonana@genuka.com",
        "password": "password",
        "phone": null
    }
}
```

**Success response — `200 OK`**

Same shape as `POST /2023-11/auth/login`:

```json
{
    "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
    "token": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Jean ONANA",
        "scopes": ["products.*", "orders.*"],
        "expires_at": null
    },
    "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_code": "PETC001"
}
```

**Error responses**

- `400 Bad Request` — missing `company` or `user` payload, or missing `currency_code`
  ```json
  { "message": "Company payload is required" }
  ```
- `409` (surfaced as a `BusinessException`) — email already registered; returns the list of companies the existing user belongs to
  ```json
  {
      "message": "A user with this email already exists.",
      "companies": [
          { "id": "01hqydxwtxdj3kmzp3bz7jk73g", "name": "Pet Clinic", "logoUrl": null }
      ]
  }
  ```

---

### `POST` `/2023-11/auth/register-additional-company`
Add a new company to an **existing authenticated user**. The user must already have a valid token. Creates the company, attaches the user as admin, creates a trial subscription, and issues a new token scoped to the new company.

Rate-limited to **5 requests per minute**.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company` | object | yes | — | Company to create |
| `company.name` | string | yes | non-empty | Company display name |
| `company.currency_code` | string | yes | non-empty | ISO 4217 currency code (e.g. `XAF`, `USD`) |
| `company.currency_name` | string | no | — | Human-readable currency name |
| `company.description` | string | no | — | Company description |
| `company.metadata` | object | no | — | Arbitrary metadata (e.g. `rccm`, `fiscalID`) |
| `company.logoUrl` | string | no | valid URL | URL to fetch and attach as company logo |

> The `user` object (if sent) is silently ignored — the company is attached to the currently authenticated user.

**Request example**

```http
POST /2023-11/auth/register-additional-company HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
    "company": {
        "name": "Second Store",
        "currency_code": "USD"
    }
}
```

**Success response — `200 OK`**

```json
{
    "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
    "token": {
        "id": "01j3kdxwtxdj3kmzp3bz7jk99f",
        "name": "Jean ONANA",
        "scopes": ["products.*", "orders.*"],
        "expires_at": null
    },
    "companyId": "01j3kdxwtxdj3kmzp3bz7jk99f",
    "company_code": "SCND002"
}
```

> Use the returned `accessToken` and `companyId` for all subsequent requests scoped to the new company.

**Error responses**

- `400 Bad Request` — missing `company` payload or `currency_code`
- `401 Unauthorized` — no valid token provided

---

### `POST` `/2023-11/auth/login-with-pin`
Authenticate using a company short code and numeric PIN. Tokens issued by this endpoint expire after 24 hours. Intended for point-of-sale and kiosk flows.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_code` | string | yes | max 16 chars, must exist in `companies` table | The company's short identifier (see `POST /2023-11/auth/verify-company-code`) |
| `pin` | string | yes | non-empty | The user's numeric PIN |

**Request example**

```http
POST /2023-11/auth/login-with-pin HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "company_code": "PETC001",
    "pin": "1234"
}
```

**Success response — `200 OK`**

```json
{
    "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
    "token": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Jean ONANA",
        "scopes": ["products.*", "orders.*"],
        "expires_at": "2024-06-10T08:00:00.000000Z"
    },
    "companyId": "01hqydxwtxdj3kmzp3bz7jk73g",
    "company_code": "PETC001"
}
```

> The `token.expires_at` is set to 24 hours after issuance. The `companies` array is included when the PIN user belongs to more than one company.

**Error responses**

- `401 Unauthorized` — invalid company code or PIN
  ```json
  { "message": "Invalid company code or PIN.", "code": 401 }
  ```
- `422 Unprocessable Entity` — `company_code` does not exist in the database

---

### `POST` `/2023-11/auth/verify-company-code`
First step of the PIN login flow. Validates a company short code and returns basic company information to display on the PIN entry screen.

Rate-limited to **5 requests per minute**.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_code` | string | yes | must exist in `companies` table | The company's short identifier |

**Request example**

```http
POST /2023-11/auth/verify-company-code HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "company_code": "PETC001"
}
```

**Success response — `200 OK`**

```json
{
    "name": "Pet Clinic",
    "logo": "https://cdn.genuka.com/media/logo.png",
    "logoUrl": "https://cdn.genuka.com/media/logo.png",
    "business_id": "RC/DLA/2020/B/1234",
    "tax_id": "M123456789",
    "address": {
        "street": "123 Rue de la Paix",
        "city": "Douala",
        "country": "CM"
    },
    "currency_name": "FCFA",
    "currency_code": "XAF",
    "created_at": "2024-01-10T08:00:00.000000Z",
    "currency": {
        "code": "XAF",
        "name": "FCFA"
    }
}
```

**Error responses**

- `422 Unprocessable Entity` — `company_code` does not exist
  ```json
  {
      "message": "The selected company_code is invalid.",
      "errors": { "company_code": ["The selected company_code is invalid."] }
  }
  ```

---

### `POST` `/2023-11/auth/logout`
Revokes the current access token and its refresh token. Requires a valid bearer token.

- **Request body:** none

**Request example**

```http
POST /2023-11/auth/logout HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
    "message": "Successfully logged out"
}
```

**Error responses**

- `401 Unauthorized` — token is missing or already revoked

---

### `POST` `/2023-11/auth/password/email`
Sends a 6-digit OTP to the user's email address to initiate the password reset flow.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `email` | string | yes | valid email | The account's email address |

**Request example**

```http
POST /2023-11/auth/password/email HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "email": "jonana@genuka.com"
}
```

**Success response — `200 OK`**

```json
{
    "message": "OTP sent successfully"
}
```

**Error responses**

- `404 Not Found` — no user with the given email
  ```json
  { "message": "User not found" }
  ```
- `500 Internal Server Error` — mail delivery failure

---

### `POST` `/2023-11/auth/password/reset`
Resets the user's password using the OTP received by email. The OTP is single-use and expires after a fixed window.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `email` | string | yes | valid email | The account's email address |
| `otp` | string | yes | exactly 6 characters | The OTP code from the email |
| `password` | string | yes | min 8 chars, confirmed | The new password |
| `password_confirmation` | string | yes | must match `password` | Confirmation of the new password |

**Request example**

```http
POST /2023-11/auth/password/reset HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "email": "jonana@genuka.com",
    "otp": "482910",
    "password": "newPassword123",
    "password_confirmation": "newPassword123"
}
```

**Success response — `200 OK`**

```json
{
    "message": "Password reset successfully"
}
```

**Error responses**

- `400 Bad Request` — OTP is invalid or expired
  ```json
  { "message": "Invalid or expired OTP" }
  ```
- `404 Not Found` — no user with the given email
- `422 Unprocessable Entity` — validation failure (e.g. passwords do not match, OTP not exactly 6 chars)
  ```json
  {
      "message": "The given data was invalid.",
      "errors": { "password": ["The password confirmation does not match."] }
  }
  ```

---

## Email Verification

These endpoints require a valid bearer token (middleware `auth:apikey,app,admin,developer`).

### `POST` `/2023-11/auth/email/send-verification`
Dispatches a new 6-digit OTP to the authenticated user's email address. The job is queued and fires after the HTTP response is sent.

Rate-limited to **3 requests per minute**.

- **Request body:** none

**Request example**

```http
POST /2023-11/auth/email/send-verification HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
    "message": "Verification code sent successfully."
}
```

**Error responses**

- `401 Unauthorized` — no valid token
- `422 Unprocessable Entity` — email is already verified
  ```json
  { "message": "Email already verified." }
  ```
- `500 Internal Server Error` — failed to dispatch notification

---

### `POST` `/2023-11/auth/email/verify`
Verifies the authenticated user's email address using the OTP. The OTP is marked as used after a successful verification.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `otp` | string | yes | exactly 6 characters | The verification OTP from the email |

**Request example**

```http
POST /2023-11/auth/email/verify HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
    "otp": "391027"
}
```

**Success response — `200 OK`**

```json
{
    "message": "Email verified successfully."
}
```

**Error responses**

- `401 Unauthorized` — no valid token
- `422 Unprocessable Entity` — email already verified, or OTP is invalid/expired
  ```json
  { "message": "Invalid or expired verification code." }
  ```

---

## User Profile

### `GET` `/2023-11/user`
Returns the full profile of the authenticated user, including their active company, subscription status, permissions, and (if applicable) a list of all companies they belong to.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `include` | string or array | no | — | Comma-separated (or array) of relationships to eager-load. Supported: `availabilities` |

**Request example**

```http
GET /2023-11/user?include=availabilities HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "first_name": "Jean",
    "last_name": "ONANA",
    "name": "Jean ONANA",
    "email": "jonana@genuka.com",
    "phone": "+237600000000",
    "role": "admin",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "email_verified_at": "2024-03-15T10:23:45.000000Z",
    "last_activity": "2024-06-09T08:00:00.000000Z",
    "metadata": { "lang": "en" },
    "pin_enabled": false,
    "created_at": "2024-01-10T08:00:00.000000Z",
    "updated_at": "2024-06-09T08:00:00.000000Z",
    "permissions": ["products.*", "orders.*", "customers.*"],
    "allPermissions": ["products.*", "orders.*", "customers.*"],
    "has_pin": false,
    "has_multiple_companies": true,
    "company": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Pet Clinic",
        "currency_code": "XAF",
        "currency_name": "FCFA",
        "logoUrl": "https://cdn.genuka.com/media/logo.png",
        "business_id": "RC/DLA/2020/B/1234",
        "tax_id": "M123456789",
        "address": { "street": "123 Main St", "city": "Douala", "country": "CM" },
        "onboarding": null,
        "paymentMethods": [],
        "variables": null,
        "custom_fields": null,
        "medias": []
    },
    "subscription": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "status": "active",
        "plan_lookup_key": "growth-monthly-XAF",
        "starts_at": "2024-01-10T00:00:00.000000Z",
        "ends_at": "2025-01-10T00:00:00.000000Z",
        "trial_ends_at": null
    },
    "companies": [
        {
            "id": "01hqydxwtxdj3kmzp3bz7jk73g",
            "name": "Pet Clinic",
            "logoUrl": "https://cdn.genuka.com/media/logo.png",
            "company_code": "PETC001",
            "currency_code": "XAF",
            "role": "admin",
            "is_current": true
        }
    ]
}
```

> `companies` is only present when `has_multiple_companies` is `true`. `subscription` may be `null` if no active subscription exists.

**Error responses**

- `401 Unauthorized` — missing or revoked token

---

### `GET` `/2023-11/user/companies`
Returns the list of all companies the authenticated user belongs to, ordered by most recently used.

**Request example**

```http
GET /2023-11/user/companies HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
    "data": [
        {
            "id": "01hqydxwtxdj3kmzp3bz7jk73g",
            "name": "Pet Clinic",
            "logoUrl": "https://cdn.genuka.com/media/logo.png",
            "company_code": "PETC001",
            "currency_code": "XAF",
            "role": "admin",
            "last_used_at": "2024-06-09T08:00:00.000000Z",
            "is_current": true
        },
        {
            "id": "01j3kdxwtxdj3kmzp3bz7jk99f",
            "name": "Second Store",
            "logoUrl": null,
            "company_code": "SCND002",
            "currency_code": "USD",
            "role": "manager",
            "last_used_at": "2024-05-01T12:00:00.000000Z",
            "is_current": false
        }
    ]
}
```

**Error responses**

- `401 Unauthorized` — missing or revoked token

---

### `PUT` `/2023-11/user/metadata`
Merges the supplied key/value pairs into the authenticated user's `metadata` JSON column. Existing keys not in the request body are preserved. Keys named `metadata` or `anonymous` in the request body are ignored.

- **Request body:** Any flat JSON object. All top-level keys (except `metadata` and `anonymous`) are merged into the stored metadata.

**Request example**

```http
PUT /2023-11/user/metadata HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
    "lang": "fr",
    "theme": "dark",
    "notifications_enabled": true
}
```

**Success response — `200 OK`**

Returns a `UserResource` object (same shape as a user in a list response — see `GET /2023-11/user` for the company-context variant):

```json
{
    "id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "first_name": "Jean",
    "last_name": "ONANA",
    "name": "Jean ONANA",
    "email": "jonana@genuka.com",
    "phone": "+237600000000",
    "role": "admin",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "email_verified_at": "2024-03-15T10:23:45.000000Z",
    "metadata": { "lang": "fr", "theme": "dark", "notifications_enabled": true },
    "pin_enabled": false,
    "permissions": ["products.*", "orders.*"],
    "allPermissions": ["products.*", "orders.*"],
    "shops": null,
    "medias": [],
    "created_at": "2024-01-10T08:00:00.000000Z",
    "updated_at": "2024-06-09T09:00:00.000000Z"
}
```

**Error responses**

- `401 Unauthorized` — missing or revoked token

---

### `POST` `/2023-11/user/set-pin`
Sets or replaces the authenticated user's 4-digit numeric PIN. The PIN is stored hashed and enables PIN-based login via `POST /2023-11/auth/login-with-pin`.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `pin` | string | yes | exactly 4 digits (`[0-9]{4}`), confirmed | The desired PIN |
| `pin_confirmation` | string | yes | must match `pin` | PIN confirmation |

**Request example**

```http
POST /2023-11/user/set-pin HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
    "pin": "1234",
    "pin_confirmation": "1234"
}
```

**Success response — `200 OK`**

```json
"PIN set successfully"
```

**Error responses**

- `401 Unauthorized` — missing or revoked token
- `422 Unprocessable Entity` — PIN is not exactly 4 digits, contains non-numeric characters, or confirmation does not match
  ```json
  {
      "message": "The given data was invalid.",
      "errors": {
          "pin": ["The pin must be 4 characters.", "The pin format is invalid."]
      }
  }
  ```

---

### `POST` `/2023-11/user/switch-company`
Switches the authenticated user's active company. Resolves effective permissions for the new company (role defaults merged with per-user overrides), syncs Spatie permissions, updates `last_used_at` on the pivot, revokes all existing tokens, and issues a new token scoped to the target company's permissions.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `company_id` | string | yes | non-empty | ULID of the company to switch to |

**Request example**

```http
POST /2023-11/user/switch-company HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
    "company_id": "01j3kdxwtxdj3kmzp3bz7jk99f"
}
```

**Success response — `200 OK`**

```json
{
    "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
    "companyId": "01j3kdxwtxdj3kmzp3bz7jk99f",
    "company_code": "SCND002",
    "user": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "first_name": "Jean",
        "last_name": "ONANA",
        "name": "Jean ONANA",
        "email": "jonana@genuka.com",
        "role": "manager",
        "company_id": "01j3kdxwtxdj3kmzp3bz7jk99f",
        "permissions": ["orders.view", "products.view"],
        "allPermissions": ["orders.view", "products.view"],
        "has_pin": false,
        "has_multiple_companies": true,
        "company": { "id": "01j3kdxwtxdj3kmzp3bz7jk99f", "name": "Second Store" },
        "subscription": null
    }
}
```

> Replace the `Authorization` header with the new `accessToken` and update `X-Company` to the new `companyId` for all subsequent requests.

**Error responses**

- `401 Unauthorized` — missing or revoked token
- `403 Forbidden` — the user does not belong to the requested company
  ```json
  { "message": "You do not belong to this company." }
  ```
- `422 Unprocessable Entity` — `company_id` missing

---

### `POST` `/2023-11/user/refresh-token`
Re-issues the user's token with the current permissions stored in the database, without requiring a full re-login. Useful when server-side permissions or the subscription plan have changed. All existing tokens are revoked and replaced.

- **Request body:** none

**Request example**

```http
POST /2023-11/user/refresh-token HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
    "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9..."
}
```

**Error responses**

- `401 Unauthorized` — missing or revoked token

---

## Invitations

Invitation endpoints are **public** — they do not require `Authorization` or `X-Company` headers. The invitation token is a 64-character random string delivered via email. Invitations expire 7 days after being issued.

### `GET` `/2023-11/invitations`
Retrieve invitation details to display on the acceptance screen. Validates the token and checks expiry before returning.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `token` | string | yes | — | The invitation token from the email link |

**Request example**

```http
GET /2023-11/invitations?token=AbCdEfGhIjKlMnOpQrStUvWxYzAbCdEfGhIjKlMnOpQrStUvWxYz12345678 HTTP/1.1
Host: api.genuka.com
Accept: application/json
```

**Success response — `200 OK`**

```json
{
    "company": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Pet Clinic",
        "logoUrl": "https://cdn.genuka.com/media/logo.png"
    },
    "role": "employee",
    "invited_by": {
        "name": "Jean ONANA"
    },
    "invited_at": "2024-06-02T08:00:00.000000Z"
}
```

**Error responses**

- `404 Not Found` — token not found or invitation already accepted/declined
  ```json
  { "message": "This invitation is invalid." }
  ```
- `410 Gone` — invitation is more than 7 days old
  ```json
  { "message": "This invitation has expired." }
  ```
- `422 Unprocessable Entity` — `token` query parameter is missing

---

### `POST` `/2023-11/invitations/accept`
Accept a pending company invitation. Updates the pivot status to `accepted`, records `accepted_at`, attaches any pre-granted shops to the user, and clears the invitation token.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `token` | string | yes | non-empty | The invitation token from the email link |

**Request example**

```http
POST /2023-11/invitations/accept HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "token": "AbCdEfGhIjKlMnOpQrStUvWxYzAbCdEfGhIjKlMnOpQrStUvWxYz12345678"
}
```

**Success response — `200 OK`**

```json
{
    "message": "Invitation accepted.",
    "company": {
        "id": "01hqydxwtxdj3kmzp3bz7jk73g",
        "name": "Pet Clinic"
    }
}
```

**Error responses**

- `404 Not Found` — token not found or invitation is not in `pending` status
  ```json
  { "message": "This invitation is invalid." }
  ```
- `410 Gone` — invitation has expired (older than 7 days)
- `422 Unprocessable Entity` — `token` field missing

---

### `POST` `/2023-11/invitations/decline`
Decline a pending company invitation. The pivot row is deleted permanently.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `token` | string | yes | non-empty | The invitation token from the email link |

**Request example**

```http
POST /2023-11/invitations/decline HTTP/1.1
Host: api.genuka.com
Accept: application/json
Content-Type: application/json

{
    "token": "AbCdEfGhIjKlMnOpQrStUvWxYzAbCdEfGhIjKlMnOpQrStUvWxYz12345678"
}
```

**Success response — `200 OK`**

```json
{
    "message": "Invitation declined."
}
```

**Error responses**

- `404 Not Found` — token not found or invitation is not in `pending` status
  ```json
  { "message": "This invitation is invalid." }
  ```
- `422 Unprocessable Entity` — `token` field missing
