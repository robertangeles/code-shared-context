# API Contracts

Owner: backend
Last updated: 2026-05-03

This file is the single source of truth for all API endpoints the frontend consumes.
The backend session updates this file immediately when any route changes.
The frontend session reads this file before making any API call assumptions.

---

## Base URL

```
Development:  http://localhost:3000
Production:   https://api.yourapp.com
```

---

## Authentication

All authenticated routes require a Bearer token in the Authorization header.

```
Authorization: Bearer <access_token>
```

Token obtained from POST /auth/login or POST /auth/refresh.

---

## Endpoints

### Auth

#### POST /auth/register
Register a new user.

Request:
```typescript
{
  email: string
  password: string
  displayName: string
}
```

Response 201:
```typescript
{
  user: {
    id: string        // UUID
    email: string
    displayName: string
    createdAt: string // ISO 8601
  }
  accessToken: string
  refreshToken: string
}
```

Errors: 400 (validation), 409 (email already exists)

---

#### POST /auth/login
Authenticate an existing user.

Request:
```typescript
{
  email: string
  password: string
}
```

Response 200:
```typescript
{
  user: {
    id: string
    email: string
    displayName: string
  }
  accessToken: string   // expires in 15 minutes
  refreshToken: string  // expires in 30 days
}
```

Errors: 401 (invalid credentials), 403 (email not verified)

---

#### POST /auth/refresh
Exchange a refresh token for a new access token.

Request:
```typescript
{
  refreshToken: string
}
```

Response 200:
```typescript
{
  accessToken: string
  refreshToken: string  // rotated on every use
}
```

Errors: 401 (invalid or expired refresh token)

---

### User

#### GET /user/profile
Get the current user's profile. Requires auth.

Response 200:
```typescript
{
  id: string
  email: string
  displayName: string
  avatarUrl: string | null
  createdAt: string
}
```

---

#### PATCH /user/profile
Update the current user's profile. Requires auth.

Request:
```typescript
{
  displayName?: string
  avatarUrl?: string
}
```

Response 200: Updated user object (same shape as GET /user/profile)

---

### Items (example resource — replace with your domain)

#### GET /items
List all items for the current user. Requires auth.

Query params:
- `page` (number, default 1)
- `limit` (number, default 20, max 100)
- `sort` (string: "createdAt" | "updatedAt", default "createdAt")
- `order` (string: "asc" | "desc", default "desc")

Response 200:
```typescript
{
  items: Array<{
    id: string
    title: string
    body: string
    createdAt: string
    updatedAt: string
  }>
  pagination: {
    page: number
    limit: number
    total: number
    totalPages: number
  }
}
```

---

#### POST /items
Create a new item. Requires auth.

Request:
```typescript
{
  title: string       // max 255 chars
  body: string        // max 10000 chars
}
```

Response 201:
```typescript
{
  id: string
  title: string
  body: string
  createdAt: string
  updatedAt: string
}
```

Errors: 400 (validation)

---

#### GET /items/:id
Get a single item. Requires auth.

Response 200: Single item object (same shape as POST /items response)

Errors: 404 (not found or not owned by user)

---

#### PATCH /items/:id
Update an item. Requires auth.

Request:
```typescript
{
  title?: string
  body?: string
}
```

Response 200: Updated item object

Errors: 400 (validation), 404 (not found or not owned by user)

---

#### DELETE /items/:id
Delete an item. Requires auth.

Response 204: No content

Errors: 404 (not found or not owned by user)

---

## Error response shape

All error responses follow this shape:

```typescript
{
  error: {
    code: string      // machine-readable, e.g. "VALIDATION_ERROR"
    message: string   // human-readable
    details?: any     // optional, validation field errors etc.
  }
}
```
