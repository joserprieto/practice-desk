# API Architecture -- Practice Desk

Status: Draft. Scope: **Legal vertical only**
([ADR-0005](../adrs/ADR-0005-legal-vertical-scope.md)).

This document describes the REST API and real-time WebSocket layer that power Practice Desk, a
Spanish legal practice management platform.

## Base URL

```text
https://api.practicedesk.app/v1
```

All endpoints are prefixed with `/v1`. See [Versioning](#versioning) for the evolution policy.

## Authentication

Practice Desk uses **JWT Bearer tokens** (short-lived access + long-lived refresh).

| Token        | Lifetime | Transport           |
| ------------ | -------- | ------------------- |
| Access (JWT) | 15 min   | `Authorization` hdr |
| Refresh      | 30 days  | HTTP-only cookie    |

### Flow

1. **Login** -- `POST /auth/login` with email + password. Returns an access token in the response
   body and sets the refresh token as an HTTP-only secure cookie.
2. **Refresh** -- `POST /auth/refresh` using the cookie. Returns a new access token.
3. **Logout** -- `POST /auth/logout` revokes the refresh token.
4. **Magic link** -- `POST /auth/magic-link` sends a one-time login link (used by the client
   portal).

### Token claims

```json
{
  "sub": "<userId>",
  "firmId": "<firmId>",
  "role": "associate",
  "type": "member",
  "iat": 1738000000,
  "exp": 1738000900
}
```

The `type` field is either `member` or `client`. The `role` field uses the Member Roles enum
(`managing_partner`, `associate`, `paralegal`, `office_admin`) or `client` for client portal users.

Every request (except `/auth/*`) must include:

```text
Authorization: Bearer <access_token>
```

## Resource Groups

Endpoints are organized by domain. All paths below are relative to `/v1`.

### Auth

| Method | Path               | Description              |
| ------ | ------------------ | ------------------------ |
| POST   | `/auth/login`      | Email + password login   |
| POST   | `/auth/refresh`    | Refresh access token     |
| POST   | `/auth/logout`     | Revoke refresh token     |
| POST   | `/auth/magic-link` | Send one-time login link |

### Firm

| Method | Path        | Description         |
| ------ | ----------- | ------------------- |
| GET    | `/firms/me` | Get current firm    |
| PUT    | `/firms/me` | Update current firm |

### Members

| Method | Path           | Description       |
| ------ | -------------- | ----------------- |
| GET    | `/members`     | List firm members |
| POST   | `/members`     | Invite member     |
| GET    | `/members/:id` | Get member        |
| PUT    | `/members/:id` | Update member     |
| DELETE | `/members/:id` | Deactivate member |

### Clients

| Method | Path              | Description             |
| ------ | ----------------- | ----------------------- |
| GET    | `/clients`        | List clients            |
| POST   | `/clients`        | Create client           |
| GET    | `/clients/:id`    | Get client              |
| PUT    | `/clients/:id`    | Update client           |
| DELETE | `/clients/:id`    | Deactivate client       |
| POST   | `/clients/import` | Bulk import (CSV/Excel) |

### Expedientes

| Method | Path               | Description        |
| ------ | ------------------ | ------------------ |
| GET    | `/expedientes`     | List expedientes   |
| POST   | `/expedientes`     | Create expediente  |
| GET    | `/expedientes/:id` | Get expediente     |
| PUT    | `/expedientes/:id` | Update expediente  |
| DELETE | `/expedientes/:id` | Archive expediente |

#### Sub-resources

| Method | Path                                                  | Description             |
| ------ | ----------------------------------------------------- | ----------------------- |
| GET    | `/expedientes/:id/parties`                            | List parties            |
| POST   | `/expedientes/:id/parties`                            | Add party               |
| PATCH  | `/expedientes/:id/parties/:partyId`                   | Update party            |
| DELETE | `/expedientes/:id/parties/:partyId`                   | Remove party            |
| GET    | `/expedientes/:id/proceedings`                        | List proceedings        |
| POST   | `/expedientes/:id/proceedings`                        | Create proceeding       |
| PATCH  | `/expedientes/:id/proceedings/:procId`                | Update proceeding       |
| GET    | `/expedientes/:id/deadlines`                          | List deadlines          |
| POST   | `/expedientes/:id/deadlines`                          | Create deadline         |
| GET    | `/expedientes/:id/document-requests`                  | List document requests  |
| POST   | `/expedientes/:id/document-requests`                  | Create document request |
| PATCH  | `/expedientes/:id/document-requests/:reqId`           | Update request          |
| POST   | `/expedientes/:id/document-requests/:reqId/documents` | Upload document         |
| GET    | `/expedientes/:id/messages`                           | List messages           |
| POST   | `/expedientes/:id/messages`                           | Send message            |
| GET    | `/expedientes/:id/time-entries`                       | List time entries       |
| POST   | `/expedientes/:id/time-entries`                       | Log time entry          |
| GET    | `/expedientes/:id/invoices`                           | List invoices           |

#### Proceedings sub-resources

| Method | Path                                                                | Description       |
| ------ | ------------------------------------------------------------------- | ----------------- |
| GET    | `/expedientes/:id/proceedings/:procId/procedural-actions`           | List actions      |
| POST   | `/expedientes/:id/proceedings/:procId/procedural-actions`           | Create action     |
| PATCH  | `/expedientes/:id/proceedings/:procId/procedural-actions/:actionId` | Update action     |
| DELETE | `/expedientes/:id/proceedings/:procId/procedural-actions/:actionId` | Delete action     |
| GET    | `/expedientes/:id/proceedings/:procId/resolutions`                  | List resolutions  |
| POST   | `/expedientes/:id/proceedings/:procId/resolutions`                  | Record resolution |
| PATCH  | `/expedientes/:id/proceedings/:procId/resolutions/:resId`           | Update resolution |
| GET    | `/expedientes/:id/proceedings/:procId/hearings`                     | List hearings     |
| POST   | `/expedientes/:id/proceedings/:procId/hearings`                     | Schedule hearing  |
| PATCH  | `/expedientes/:id/proceedings/:procId/hearings/:hearingId`          | Update hearing    |

### Courts

| Method | Path          | Description      |
| ------ | ------------- | ---------------- |
| GET    | `/courts`     | List courts      |
| POST   | `/courts`     | Create court     |
| GET    | `/courts/:id` | Get court        |
| PUT    | `/courts/:id` | Update court     |
| DELETE | `/courts/:id` | Deactivate court |

### Court Agents

| Method | Path                | Description            |
| ------ | ------------------- | ---------------------- |
| GET    | `/court-agents`     | List court agents      |
| POST   | `/court-agents`     | Create court agent     |
| GET    | `/court-agents/:id` | Get court agent        |
| PUT    | `/court-agents/:id` | Update court agent     |
| DELETE | `/court-agents/:id` | Deactivate court agent |

### Hearings (cross-expediente)

| Method | Path            | Description                              |
| ------ | --------------- | ---------------------------------------- |
| GET    | `/hearings`     | List hearings with date/status filtering |
| GET    | `/hearings/:id` | Get hearing                              |

### Deadlines (cross-expediente)

| Method | Path             | Description                                   |
| ------ | ---------------- | --------------------------------------------- |
| GET    | `/deadlines`     | List deadlines with status/due-date filtering |
| GET    | `/deadlines/:id` | Get deadline                                  |
| PATCH  | `/deadlines/:id` | Update deadline (e.g., mark completed)        |

### Appointments

| Method | Path                | Description          |
| ------ | ------------------- | -------------------- |
| GET    | `/appointments`     | List appointments    |
| POST   | `/appointments`     | Schedule appointment |
| GET    | `/appointments/:id` | Get appointment      |
| PUT    | `/appointments/:id` | Update appointment   |
| DELETE | `/appointments/:id` | Cancel appointment   |

### Notifications

| Method | Path                 | Description        |
| ------ | -------------------- | ------------------ |
| GET    | `/notifications`     | List notifications |
| PATCH  | `/notifications/:id` | Mark read/unread   |

### Time Entries

| Method | Path                | Description       |
| ------ | ------------------- | ----------------- |
| GET    | `/time-entries`     | List time entries |
| POST   | `/time-entries`     | Create time entry |
| GET    | `/time-entries/:id` | Get time entry    |
| PUT    | `/time-entries/:id` | Update time entry |
| DELETE | `/time-entries/:id` | Delete time entry |

### Invoices

| Method | Path            | Description    |
| ------ | --------------- | -------------- |
| GET    | `/invoices`     | List invoices  |
| POST   | `/invoices`     | Create invoice |
| GET    | `/invoices/:id` | Get invoice    |
| PUT    | `/invoices/:id` | Update invoice |
| DELETE | `/invoices/:id` | Void invoice   |

### Documents

| Method | Path                      | Description     |
| ------ | ------------------------- | --------------- |
| POST   | `/documents/upload`       | Upload document |
| GET    | `/documents/:id`          | Get metadata    |
| GET    | `/documents/:id/download` | Download file   |

### Dashboard

| Method | Path                   | Description              |
| ------ | ---------------------- | ------------------------ |
| GET    | `/dashboard/partner`   | Partner dashboard data   |
| GET    | `/dashboard/associate` | Associate dashboard data |

## Conventions

### Request/Response Format

- Content type: `application/json` (except file uploads which use `multipart/form-data`).
- All dates use ISO 8601: `2026-02-18` for dates, `2026-02-18T10:30:00Z` for date-times.
- All entity IDs are UUIDv7.
- Field names use camelCase.

### Pagination

All list endpoints use **cursor-based pagination**:

```text
GET /v1/expedientes?cursor=<opaqueToken>&limit=25
```

| Parameter | Type   | Default | Max | Description             |
| --------- | ------ | ------- | --- | ----------------------- |
| `cursor`  | string | --      | --  | Opaque pagination token |
| `limit`   | int    | 25      | 100 | Items per page          |

Response envelope:

```json
{
  "data": [ ... ],
  "pagination": {
    "nextCursor": "eyJpZCI6IjAxOTJkNGUwLTdiMWEtN2Y1ZS0uLi4ifQ==",
    "hasMore": true
  }
}
```

### Filtering and Sorting

Filters are passed as query parameters:

```text
GET /v1/expedientes?status=in_progress&jurisdiction=civil&assigneeId=<uuid>
GET /v1/deadlines?status=pending&dueBefore=2026-03-01&dueAfter=2026-02-01
GET /v1/hearings?dateAfter=2026-02-18&dateBefore=2026-03-18
```

Sorting:

```text
GET /v1/expedientes?sort=createdAt&order=desc
```

### Error Responses

Errors follow [RFC 7807](https://www.rfc-editor.org/rfc/rfc7807) (Problem Details for HTTP APIs):

```json
{
  "type": "https://api.practicedesk.app/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "detail": "The field 'jurisdiction' must be one of: civil, criminal, labor, administrative, commercial.",
  "instance": "/v1/expedientes",
  "errors": [
    {
      "field": "jurisdiction",
      "message": "Must be one of: civil, criminal, labor, administrative, commercial."
    }
  ]
}
```

Standard HTTP status codes:

| Code | Usage                                |
| ---- | ------------------------------------ |
| 200  | Successful read or update            |
| 201  | Resource created                     |
| 204  | Successful delete (no content)       |
| 400  | Malformed request                    |
| 401  | Missing or invalid token             |
| 403  | Insufficient permissions             |
| 404  | Resource not found                   |
| 409  | Conflict (e.g., duplicate reference) |
| 422  | Validation error                     |
| 429  | Rate limited                         |
| 500  | Internal server error                |

### Rate Limiting

- 100 requests per minute per user (general).
- 10 requests per minute for `/auth/login` and `/auth/magic-link`.
- Rate limit headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.

### Multi-Tenancy

All data is firm-scoped. The `firmId` is extracted from the JWT token. There is no need to pass
`firmId` in request bodies or query parameters -- the API enforces tenant isolation automatically.

## Versioning

- The API uses **URL-based versioning** (`/v1/...`).
- Breaking changes increment the major version (`/v2/...`).
- Non-breaking additions (new fields, new endpoints) are added without version change.
- Deprecated endpoints are marked with a `Sunset` header at least 6 months before removal.
- The current and previous version are supported concurrently during migration windows.

## Related Documents

- [OpenAPI 3.1 Specification](./openapi.yaml) -- full endpoint and schema definitions.
- [AsyncAPI 3.0 Specification](./asyncapi.yaml) -- WebSocket event contracts.
- [Data Model](../data-model/data-model.md) -- entity definitions and relationships.
- [Domain Dictionary](../../analysis/domain-dictionary.md) -- enums and Spanish legal terms.
