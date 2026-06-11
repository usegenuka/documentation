---
title: "Booking — Availability & Calendar"
description: "This domain manages the scheduling infrastructure for bookable services. It exposes three resources: **Availabilities** (recurring weekly time windows during wh"
sidebarTitle: "Booking & Availability"
---

This domain manages the scheduling infrastructure for bookable services. It exposes three resources: **Availabilities** (recurring weekly time windows during which a staff member accepts appointments), **Unavailabilities** (specific calendar dates on which a staff member is blocked), and **Calendar Events** (concrete scheduled events, including confirmed bookings). Admin routes require authentication; public routes can be called without a Bearer token but still require `X-Company`.

**Base URL** `https://api.genuka.com` · **Auth** `Authorization: Bearer <token>` + `X-Company: <companyId>` · See [Getting Started](01-getting-started.md) for shared conventions, pagination & error formats.

## Endpoints at a glance

| Method | Path | Description |
|---|---|---|
| `GET` | `/2023-11/admin/availabilities` | List availabilities for a user (admin) |
| `POST` | `/2023-11/admin/availabilities` | Replace all availabilities for a user (admin) |
| `GET` | `/2023-11/admin/availabilities/{availability}` | Get a single availability (admin) |
| `PUT` | `/2023-11/admin/availabilities/{availability}` | Update a single availability (admin) |
| `DELETE` | `/2023-11/admin/availabilities/{availability}` | Delete a single availability (admin) |
| `GET` | `/2023-11/availabilities` | List availabilities for a user (public) |
| `GET` | `/2023-11/availabilities/slots` | Calculate available booking slots for a day |
| `GET` | `/2023-11/availabilities/{availability}` | Get a single availability (public) |
| `GET` | `/2023-11/admin/unavailabilities` | List unavailabilities for a user (admin) |
| `POST` | `/2023-11/admin/unavailabilities` | Create one or more unavailabilities (admin) |
| `POST` | `/2023-11/admin/unavailabilities/bulk-delete` | Bulk-delete unavailabilities by date (admin) |
| `GET` | `/2023-11/admin/unavailabilities/dates` | Get a flat list of unavailable dates (admin) |
| `GET` | `/2023-11/admin/unavailabilities/{unavailability}` | Get a single unavailability (admin) |
| `PUT` | `/2023-11/admin/unavailabilities/{unavailability}` | Update a single unavailability (admin) |
| `DELETE` | `/2023-11/admin/unavailabilities/{unavailability}` | Delete a single unavailability (admin) |
| `GET` | `/2023-11/unavailabilities` | List unavailabilities for a user (public) |
| `GET` | `/2023-11/unavailabilities/dates` | Get a flat list of unavailable dates (public) |
| `GET` | `/2023-11/unavailabilities/{unavailability}` | Get a single unavailability (public) |
| `GET` | `/2023-11/admin/calendar-events` | List calendar events with filters (admin) |
| `POST` | `/2023-11/admin/calendar-events` | Create a calendar event (admin) — stub |
| `GET` | `/2023-11/admin/calendar-events/{calendarEvent}` | Get a single calendar event (admin) — stub |
| `PUT` | `/2023-11/admin/calendar-events/{calendarEvent}` | Update a calendar event (admin) — stub |
| `DELETE` | `/2023-11/admin/calendar-events/{calendarEvent}` | Delete a calendar event (admin) — stub |

---

## Availabilities

An **Availability** represents a recurring weekly window during which a staff member (user) is available for bookings. The store endpoint is a **full replace** — it deletes all existing availabilities for the user before inserting the new set.

### `GET` `/2023-11/admin/availabilities`

List all weekly availability windows for a specific user. If `user_id` is not provided as a query parameter the authenticated user's own availabilities are returned.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | No | authenticated user | ID of the staff member to retrieve availabilities for |

**Request example**

```http
GET /2023-11/admin/availabilities?user_id=01k7ee7anat9j4bn5v71v70qck HTTP/1.1
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
      "id": "01k7ef1bnat9j4bn5v71v70qab",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "day_of_week": 1,
      "start_time": "08:00:00",
      "end_time": "12:00:00",
      "timezone": "UTC",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-01T09:00:00.000000Z",
      "updated_at": "2024-06-01T09:00:00.000000Z"
    }
  ],
  "message": "Availabilities retrieved successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — no user resolved (neither `user_id` param nor authenticated session)
- `401 Unauthorized` — missing or invalid Bearer token

---

### `POST` `/2023-11/admin/availabilities`

Replace all weekly availability windows for a user. All existing availabilities for the given `user_id` are deleted before the new set is created.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | Yes | must exist in `users` | Staff member whose schedule is being set |
| `availabilities` | array | Yes | array of objects | The new set of availability windows |
| `availabilities[].day_of_week` | integer | No | 0–6 (0 = Sunday) | Day of the week for this window |
| `availabilities[].start_time` | string | No | `H:i` format | Window start time, e.g. `"08:00"` |
| `availabilities[].end_time` | string | No | `H:i` format, after `start_time` | Window end time, e.g. `"12:00"` |
| `availabilities[].timezone` | string | No | any valid timezone string | Timezone for this window; falls back to request-level `timezone` then `app.timezone` |
| `timezone` | string | No | | Default timezone applied to all windows when not set per-item |
| `metadata` | object | No | | Arbitrary JSON metadata stored on every created availability |

**Request example**

```http
POST /2023-11/admin/availabilities HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "availabilities": [
    {
      "day_of_week": 1,
      "start_time": "08:00",
      "end_time": "12:00"
    },
    {
      "day_of_week": 1,
      "start_time": "14:00",
      "end_time": "18:00"
    }
  ]
}
```

**Success response — `201 Created`**

Returns the full availability list for the user after the replace.

```json
{
  "data": [
    {
      "id": "01k7ef1bnat9j4bn5v71v70qab",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "day_of_week": 1,
      "start_time": "08:00:00",
      "end_time": "12:00:00",
      "timezone": "UTC",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-01T09:00:00.000000Z",
      "updated_at": "2024-06-01T09:00:00.000000Z"
    },
    {
      "id": "01k7ef1bnat9j4bn5v71v70qac",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "day_of_week": 1,
      "start_time": "14:00:00",
      "end_time": "18:00:00",
      "timezone": "UTC",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-01T09:00:00.000000Z",
      "updated_at": "2024-06-01T09:00:00.000000Z"
    }
  ],
  "message": "Availabilities created successfully",
  "status": true
}
```

**Error responses**

- `422 Unprocessable Entity` — `user_id` missing or `company_id` not resolvable from request context

```json
{
  "message": "User ID is required",
  "status": false
}
```

- `404 Not Found` — `user_id` does not correspond to a known user
- `422 Unprocessable Entity` — validation failure on `availabilities` array items (invalid `day_of_week`, `H:i` format, or `end_time` not after `start_time`)

---

### `GET` `/2023-11/admin/availabilities/{availability}`

Retrieve a single availability window by its ULID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `availability` | string (ULID) | ID of the availability to retrieve |

**Request example**

```http
GET /2023-11/admin/availabilities/01k7ef1bnat9j4bn5v71v70qab HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01k7ef1bnat9j4bn5v71v70qab",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "day_of_week": 1,
    "start_time": "08:00:00",
    "end_time": "12:00:00",
    "timezone": "UTC",
    "metadata": null,
    "medias": [],
    "created_at": "2024-06-01T09:00:00.000000Z",
    "updated_at": "2024-06-01T09:00:00.000000Z"
  },
  "message": "Availability retrieved successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — availability with that ULID does not exist
- `401 Unauthorized` — missing or invalid Bearer token

---

### `PUT` `/2023-11/admin/availabilities/{availability}`

Update a single availability window. All fields are required.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `availability` | string (ULID) | ID of the availability to update |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `day_of_week` | integer | Yes | 0–6 | Day of the week (0 = Sunday) |
| `start_time` | string | Yes | `H:i` format | Window start time |
| `end_time` | string | Yes | `H:i` format, after `start_time` | Window end time |
| `timezone` | string | No | | Timezone identifier |

**Request example**

```http
PUT /2023-11/admin/availabilities/01k7ef1bnat9j4bn5v71v70qab HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "day_of_week": 1,
  "start_time": "09:00",
  "end_time": "13:00",
  "timezone": "Africa/Douala"
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01k7ef1bnat9j4bn5v71v70qab",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "day_of_week": 1,
    "start_time": "09:00:00",
    "end_time": "13:00:00",
    "timezone": "Africa/Douala",
    "metadata": null,
    "medias": [],
    "created_at": "2024-06-01T09:00:00.000000Z",
    "updated_at": "2024-06-02T10:00:00.000000Z"
  },
  "message": "Availability updated successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — availability ULID not found
- `422 Unprocessable Entity` — validation failure (missing required fields, bad time format, `end_time` not after `start_time`, `day_of_week` out of range 0–6)

```json
{
  "message": "The end time field must be a date after start time.",
  "errors": {
    "end_time": ["The end time field must be a date after start time."]
  }
}
```

---

### `DELETE` `/2023-11/admin/availabilities/{availability}`

Delete a single availability window.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `availability` | string (ULID) | ID of the availability to delete |

**Request example**

```http
DELETE /2023-11/admin/availabilities/01k7ef1bnat9j4bn5v71v70qab HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "message": "Availability deleted successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — availability ULID not found
- `500 Internal Server Error` — deletion failed (e.g. database constraint)

---

### `GET` `/2023-11/availabilities`

Public version of the availability list. Behaves identically to the admin list endpoint but does not require admin authentication. If `user_id` is omitted and no authenticated session is present, the request returns `404`.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | No | authenticated user | Staff member to retrieve availabilities for |

**Request example**

```http
GET /2023-11/availabilities?user_id=01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01k7ef1bnat9j4bn5v71v70qab",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "day_of_week": 1,
      "start_time": "08:00:00",
      "end_time": "12:00:00",
      "timezone": "UTC",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-01T09:00:00.000000Z",
      "updated_at": "2024-06-01T09:00:00.000000Z"
    }
  ],
  "message": "Availabilities retrieved successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — no user resolved

---

### `GET` `/2023-11/availabilities/slots`

Calculate all available booking slots for a specific staff member, service, and calendar day. The algorithm:

1. Checks if the user has an `Unavailability` record for the requested day — if so, returns an empty slots array with `"unavailable": true`.
2. Retrieves existing `CalendarEvent` records for the user on that day.
3. Iterates over the user's `Availability` windows that match the day of the week.
4. Splits each window into slots of `service.duration_minutes`, advancing by `duration_minutes + buffer_minutes` per step.
5. Excludes any slot that overlaps an existing calendar event.
6. Excludes slots whose end time is in the past (for the current day).

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | No | authenticated user | Staff member to calculate slots for |
| `service_id` | string (ULID) | Yes | — | Service to book; determines `duration_minutes` and `buffer_minutes` |
| `day` | string (date) | Yes | — | Calendar date to calculate slots for, e.g. `"2024-07-15"` |

**Request example**

```http
GET /2023-11/availabilities/slots?user_id=01k7ee7anat9j4bn5v71v70qck&service_id=01jq2ab3cde4fg5hi6jk7lm8no&day=2024-07-15 HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK` (slots available)**

```json
{
  "status": true,
  "message": "Créneaux disponibles récupérés avec succès",
  "data": {
    "professional_id": "01k7ee7anat9j4bn5v71v70qck",
    "professional_name": "Alice Dupont",
    "slots": [
      {
        "start": "08:00",
        "end": "09:00",
        "duration": 60,
        "buffer": 0,
        "available": true
      },
      {
        "start": "09:00",
        "end": "10:00",
        "duration": 60,
        "buffer": 0,
        "available": true
      }
    ]
  }
}
```

**Success response — `200 OK` (user unavailable that day)**

```json
{
  "status": true,
  "message": "User is unavailable on this day",
  "data": {
    "professional_id": "01k7ee7anat9j4bn5v71v70qck",
    "professional_name": "Alice Dupont",
    "slots": [],
    "unavailable": true
  }
}
```

**Error responses**

- `404 Not Found` — `user_id` or `service_id` not found, or no user resolved

```json
{
  "message": "Service not found",
  "status": false
}
```

---

### `GET` `/2023-11/availabilities/{availability}`

Public version of the single availability show endpoint. Behaves identically to `GET /2023-11/admin/availabilities/{availability}`.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `availability` | string (ULID) | ID of the availability to retrieve |

**Request example**

```http
GET /2023-11/availabilities/01k7ef1bnat9j4bn5v71v70qab HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01k7ef1bnat9j4bn5v71v70qab",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "day_of_week": 1,
    "start_time": "08:00:00",
    "end_time": "12:00:00",
    "timezone": "UTC",
    "metadata": null,
    "medias": [],
    "created_at": "2024-06-01T09:00:00.000000Z",
    "updated_at": "2024-06-01T09:00:00.000000Z"
  },
  "message": "Availability retrieved successfully",
  "status": true
}
```

---

## Unavailabilities

An **Unavailability** marks a specific calendar date on which a staff member is not available for bookings (e.g. vacation, holiday, sick leave). Multiple dates can be created in a single request. The `getDates` endpoint returns a lightweight flat array of date strings for calendar UI rendering.

### `GET` `/2023-11/admin/unavailabilities`

List unavailabilities for a user with optional date range filtering.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | No | authenticated user | Staff member to list unavailabilities for |
| `start_date` | string (date) | No | — | Filter records on or after this date (`Y-m-d`) |
| `end_date` | string (date) | No | — | Filter records on or before this date (`Y-m-d`) |

**Request example**

```http
GET /2023-11/admin/unavailabilities?user_id=01k7ee7anat9j4bn5v71v70qck&start_date=2024-07-01&end_date=2024-07-31 HTTP/1.1
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
      "id": "01k7ef5xnat9j4bn5v71v70qzz",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "date": "2024-07-14",
      "type": "time_off",
      "reason": "National holiday",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-15T08:00:00.000000Z",
      "updated_at": "2024-06-15T08:00:00.000000Z"
    }
  ],
  "message": "Unavailabilities retrieved successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — no user resolved
- `401 Unauthorized` — missing or invalid Bearer token

---

### `POST` `/2023-11/admin/unavailabilities`

Create unavailability records for one or more dates. Duplicate dates for the same user are silently skipped (idempotent per date). Returns the full updated unavailability list for the user sorted by date ascending.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | Yes | must exist in `users` | Staff member to mark as unavailable |
| `dates` | array | Yes | min 1 item, each a valid date | List of dates to block, e.g. `["2024-07-14", "2024-07-15"]` |
| `type` | string | No | | Category of unavailability; defaults to `"time_off"` |
| `reason` | string | No | | Human-readable reason (e.g. `"Sick leave"`) |

**Request example**

```http
POST /2023-11/admin/unavailabilities HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "dates": ["2024-07-14", "2024-07-15"],
  "type": "time_off",
  "reason": "National holiday"
}
```

**Success response — `201 Created`**

Returns the user's complete unavailability list after the insert (including pre-existing records).

```json
{
  "data": [
    {
      "id": "01k7ef5xnat9j4bn5v71v70qzz",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "date": "2024-07-14",
      "type": "time_off",
      "reason": "National holiday",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-15T08:00:00.000000Z",
      "updated_at": "2024-06-15T08:00:00.000000Z"
    },
    {
      "id": "01k7ef5xnat9j4bn5v71v70qzw",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "date": "2024-07-15",
      "type": "time_off",
      "reason": "National holiday",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-15T08:00:00.000000Z",
      "updated_at": "2024-06-15T08:00:00.000000Z"
    }
  ],
  "message": "Unavailabilities created successfully",
  "status": true
}
```

**Error responses**

- `422 Unprocessable Entity` — `user_id` or `company_id` missing, or `dates` array missing/empty

```json
{
  "message": "The dates field is required.",
  "errors": {
    "dates": ["The dates field is required."]
  }
}
```

- `404 Not Found` — `user_id` does not correspond to a known user

---

### `POST` `/2023-11/admin/unavailabilities/bulk-delete`

Delete unavailability records for a user by date. Only records whose date matches an entry in the `dates` array are deleted. Returns the count of deleted rows.

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | Yes | | Staff member whose records are being removed |
| `dates` | array | Yes | min 1 item, each a valid date | Dates to unblock |

**Request example**

```http
POST /2023-11/admin/unavailabilities/bulk-delete HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "user_id": "01k7ee7anat9j4bn5v71v70qck",
  "dates": ["2024-07-14", "2024-07-15"]
}
```

**Success response — `200 OK`**

```json
{
  "message": "Unavailabilities deleted successfully",
  "status": true,
  "deleted_count": 2
}
```

**Error responses**

- `422 Unprocessable Entity` — validation failure (missing `user_id` or `dates`)

---

### `GET` `/2023-11/admin/unavailabilities/dates`

Return a flat array of `"Y-m-d"` date strings for a user within an optional date range. Designed for calendar widget rendering where only blocked dates are needed, not full records.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `user_id` | string (ULID) | Yes | — | Staff member to query |
| `start_date` | string (date) | No | current month start | Range start (`Y-m-d`) |
| `end_date` | string (date) | No | 3 months from today | Range end (`Y-m-d`) |

**Request example**

```http
GET /2023-11/admin/unavailabilities/dates?user_id=01k7ee7anat9j4bn5v71v70qck&start_date=2024-07-01&end_date=2024-09-30 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "status": true,
  "message": "Unavailable dates retrieved successfully",
  "data": [
    "2024-07-14",
    "2024-07-15",
    "2024-08-05"
  ]
}
```

**Error responses**

- `422 Unprocessable Entity` — `user_id` not provided

```json
{
  "message": "User ID is required",
  "status": false
}
```

---

### `GET` `/2023-11/admin/unavailabilities/{unavailability}`

Retrieve a single unavailability record by ULID.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `unavailability` | string (ULID) | ID of the unavailability record |

**Request example**

```http
GET /2023-11/admin/unavailabilities/01k7ef5xnat9j4bn5v71v70qzz HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01k7ef5xnat9j4bn5v71v70qzz",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "date": "2024-07-14",
    "type": "time_off",
    "reason": "National holiday",
    "metadata": null,
    "medias": [],
    "created_at": "2024-06-15T08:00:00.000000Z",
    "updated_at": "2024-06-15T08:00:00.000000Z"
  },
  "message": "Unavailability retrieved successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — ULID not found
- `401 Unauthorized` — missing or invalid Bearer token

---

### `PUT` `/2023-11/admin/unavailabilities/{unavailability}`

Update a single unavailability record. The `date` field is optional (`sometimes`); if omitted, only `type` and/or `reason` are updated.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `unavailability` | string (ULID) | ID of the unavailability record |

- **Request body:**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `date` | string (date) | No | valid date | New date for this record |
| `type` | string | No | | New type value |
| `reason` | string | No | | New reason text |

**Request example**

```http
PUT /2023-11/admin/unavailabilities/01k7ef5xnat9j4bn5v71v70qzz HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
Content-Type: application/json

{
  "reason": "Training day",
  "type": "training"
}
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01k7ef5xnat9j4bn5v71v70qzz",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "date": "2024-07-14",
    "type": "training",
    "reason": "Training day",
    "metadata": null,
    "medias": [],
    "created_at": "2024-06-15T08:00:00.000000Z",
    "updated_at": "2024-06-16T08:00:00.000000Z"
  },
  "message": "Unavailability updated successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — ULID not found
- `422 Unprocessable Entity` — `date` value is not a valid date

---

### `DELETE` `/2023-11/admin/unavailabilities/{unavailability}`

Delete a single unavailability record.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `unavailability` | string (ULID) | ID of the unavailability to delete |

**Request example**

```http
DELETE /2023-11/admin/unavailabilities/01k7ef5xnat9j4bn5v71v70qzz HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "message": "Unavailability deleted successfully",
  "status": true
}
```

**Error responses**

- `404 Not Found` — ULID not found
- `500 Internal Server Error` — deletion failed

---

### `GET` `/2023-11/unavailabilities`

Public version of the unavailability list. Accepts the same `user_id`, `start_date`, and `end_date` query parameters as the admin endpoint.

**Request example**

```http
GET /2023-11/unavailabilities?user_id=01k7ee7anat9j4bn5v71v70qck HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": [
    {
      "id": "01k7ef5xnat9j4bn5v71v70qzz",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "date": "2024-07-14",
      "type": "time_off",
      "reason": "National holiday",
      "metadata": null,
      "medias": [],
      "created_at": "2024-06-15T08:00:00.000000Z",
      "updated_at": "2024-06-15T08:00:00.000000Z"
    }
  ],
  "message": "Unavailabilities retrieved successfully",
  "status": true
}
```

---

### `GET` `/2023-11/unavailabilities/dates`

Public version of the flat date list. Accepts `user_id`, `start_date`, `end_date`. Requires `user_id`.

**Request example**

```http
GET /2023-11/unavailabilities/dates?user_id=01k7ee7anat9j4bn5v71v70qck&start_date=2024-07-01&end_date=2024-09-30 HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "status": true,
  "message": "Unavailable dates retrieved successfully",
  "data": [
    "2024-07-14",
    "2024-07-15",
    "2024-08-05"
  ]
}
```

---

### `GET` `/2023-11/unavailabilities/{unavailability}`

Public version of the single unavailability show endpoint.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `unavailability` | string (ULID) | ID of the unavailability record |

**Request example**

```http
GET /2023-11/unavailabilities/01k7ef5xnat9j4bn5v71v70qzz HTTP/1.1
Host: api.genuka.com
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

```json
{
  "data": {
    "id": "01k7ef5xnat9j4bn5v71v70qzz",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "date": "2024-07-14",
    "type": "time_off",
    "reason": "National holiday",
    "metadata": null,
    "medias": [],
    "created_at": "2024-06-15T08:00:00.000000Z",
    "updated_at": "2024-06-15T08:00:00.000000Z"
  },
  "message": "Unavailability retrieved successfully",
  "status": true
}
```

---

## Calendar Events

A **CalendarEvent** is a concrete scheduled occurrence linked to a staff member (user) and optionally tied to an Order. It carries a start/end timestamp, an all-day flag, a status lifecycle, and a free-form source tag. The `index` endpoint is fully implemented with rich filtering and sorting via Spatie QueryBuilder. The remaining write endpoints (`store`, `show`, `update`, `destroy`) are registered as routes but are not yet implemented in the controller (empty method bodies).

### `GET` `/2023-11/admin/calendar-events`

List calendar events with optional includes, filtering, sorting, and pagination.

- **Query parameters:**

| Name | Type | Required | Default | Description |
|---|---|---|---|---|
| `include` | string | No | — | Comma-separated relations to eager-load. Allowed: `user` |
| `filter[user_id]` | string (ULID) | No | — | Exact match on `user_id` |
| `filter[users][]` | array of ULIDs | No | — | Match events belonging to any of the given user IDs |
| `filter[status]` | string | No | — | Exact match. One of: `pending`, `in_progress`, `confirmed`, `canceled` |
| `filter[source]` | string | No | — | Exact match on `source` |
| `filter[all_day]` | boolean | No | — | Exact match on `all_day` flag |
| `filter[starts_at][gte]` | string (datetime) | No | — | Events starting at or after this datetime |
| `filter[starts_at][lte]` | string (datetime) | No | — | Events starting at or before this datetime |
| `filter[ends_at][gte]` | string (datetime) | No | — | Events ending at or after this datetime |
| `filter[ends_at][lte]` | string (datetime) | No | — | Events ending at or before this datetime |
| `filter[updated_at][start]` | string (datetime) | No | — | Updated at range start |
| `filter[updated_at][end]` | string (datetime) | No | — | Updated at range end |
| `filter[search]` | string | No | — | Full-text search across `title` and `description` |
| `sort` | string | No | `starts_at` | Sort field, prefix with `-` for descending. Allowed: `title`, `description`, `starts_at`, `ends_at`, `created_at`, `updated_at` |
| `page` | integer | No | 1 | Page number |
| `per_page` | integer | No | 15 | Results per page |

**Request example**

```http
GET /2023-11/admin/calendar-events?filter[user_id]=01k7ee7anat9j4bn5v71v70qck&filter[status]=confirmed&filter[starts_at][gte]=2024-07-01T00:00:00Z&sort=-starts_at&per_page=20 HTTP/1.1
Host: api.genuka.com
Authorization: Bearer <token>
X-Company: 01hqydxwtxdj3kmzp3bz7jk73g
Accept: application/json
```

**Success response — `200 OK`**

Paginated collection wrapped in the standard pagination envelope. `CalendarEventResource` serializes all model fields, converts `starts_at` and `ends_at` to ISO 8601, and appends `relations` (universal polymorphic relations) and the `user` relationship when loaded.

```json
{
  "data": [
    {
      "id": "01k7eg9anat9j4bn5v71v70q11",
      "user_id": "01k7ee7anat9j4bn5v71v70qck",
      "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
      "title": "Haircut with Alice",
      "description": "30-min trim",
      "starts_at": "2024-07-15T09:00:00+00:00",
      "ends_at": "2024-07-15T09:30:00+00:00",
      "all_day": false,
      "status": "confirmed",
      "source": "booking",
      "metadata": null,
      "relations": [],
      "user": {
        "id": "01k7ee7anat9j4bn5v71v70qck",
        "name": "Alice Dupont"
      },
      "created_at": "2024-07-10T08:00:00.000000Z",
      "updated_at": "2024-07-10T08:00:00.000000Z"
    }
  ],
  "pagination": {
    "total": 42,
    "count": 20,
    "per_page": 20,
    "current_page": 1,
    "total_pages": 3
  }
}
```

**Error responses**

- `401 Unauthorized` — missing or invalid Bearer token
- `403 Forbidden` — invalid OAuth scope
- `400 Bad Request` — missing `X-Company` header

---

### `POST` `/2023-11/admin/calendar-events`

> **Not yet implemented.** The route is registered but the controller method body is empty. Calling this endpoint returns an empty `200 OK` response with no body.

---

### `GET` `/2023-11/admin/calendar-events/{calendarEvent}`

> **Not yet implemented.** The route is registered but the controller method body is empty. Calling this endpoint returns an empty `200 OK` response with no body.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `calendarEvent` | string (ULID) | ID of the calendar event |

**Response:** `200 OK` — empty body (stub).

**Expected response when implemented — `200 OK`**

```json
{
  "data": {
    "id": "01k7eg9anat9j4bn5v71v70q11",
    "user_id": "01k7ee7anat9j4bn5v71v70qck",
    "company_id": "01hqydxwtxdj3kmzp3bz7jk73g",
    "title": "Haircut with Alice",
    "description": "30-min trim",
    "starts_at": "2024-07-15T09:00:00+00:00",
    "ends_at": "2024-07-15T09:30:00+00:00",
    "all_day": false,
    "status": "confirmed",
    "source": "booking",
    "metadata": null,
    "relations": [],
    "user": {
      "id": "01k7ee7anat9j4bn5v71v70qck",
      "name": "Alice Dupont"
    },
    "created_at": "2024-07-10T08:00:00.000000Z",
    "updated_at": "2024-07-10T08:00:00.000000Z"
  },
  "message": "Calendar event retrieved successfully",
  "status": true
}
```

---

### `PUT` `/2023-11/admin/calendar-events/{calendarEvent}`

> **Not yet implemented.** The route is registered but the controller method body is empty. Calling this endpoint returns an empty `200 OK` response with no body.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `calendarEvent` | string (ULID) | ID of the calendar event |

- **Expected request body (when implemented):**

| Field | Type | Required | Rules | Description |
|---|---|---|---|---|
| `title` | string | No | | Human-readable title for the event |
| `description` | string | No | | Optional longer description |
| `starts_at` | string (datetime) | No | ISO 8601 | Event start datetime |
| `ends_at` | string (datetime) | No | ISO 8601, after `starts_at` | Event end datetime |
| `all_day` | boolean | No | | Whether this is an all-day event |
| `status` | string | No | one of: `pending`, `in_progress`, `confirmed`, `canceled` | Lifecycle status |
| `source` | string | No | | Tag identifying how the event was created (e.g. `"booking"`) |
| `user_id` | string (ULID) | No | must exist in `users` | Staff member assigned to this event |
| `metadata` | object | No | | Arbitrary JSON metadata |

**Response:** `200 OK` — empty body (stub).

---

### `DELETE` `/2023-11/admin/calendar-events/{calendarEvent}`

> **Not yet implemented.** The route is registered but the controller method body is empty. Calling this endpoint returns an empty `200 OK` response with no body.

- **Path parameters:**

| Name | Type | Description |
|---|---|---|
| `calendarEvent` | string (ULID) | ID of the calendar event |

**Response:** `200 OK` — empty body (stub).
