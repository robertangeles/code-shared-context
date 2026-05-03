# Architecture

Last updated: 2026-05-03

How the two projects fit together. Both sessions append to this file when the architecture changes.

---

## System overview

```
[Frontend App]
     |
     | HTTPS (REST)
     v
[Backend API]  ←→  [PostgreSQL]
     |
     | (optional: third-party services)
     v
[External APIs]
```

---

## Backend

- **Runtime:** Node.js with Express
- **Language:** TypeScript
- **Database ORM:** Drizzle
- **Database:** PostgreSQL
- **Auth:** JWT (access + refresh tokens), bcrypt password hashing
- **Port (dev):** 3000

Responsibilities:
- All business logic
- Database access
- Authentication and authorization
- Third-party service integration

---

## Frontend

- **Framework:** React Native / Expo (or swap for your stack)
- **Language:** TypeScript
- **State management:** React Context + hooks
- **Port (dev):** 5174 (Vite) or Expo default

Responsibilities:
- UI rendering
- API consumption
- Local state management
- User input and navigation

---

## Auth flow

```
1. User submits credentials → POST /auth/login
2. Backend validates, returns accessToken + refreshToken
3. Frontend stores tokens (SecureStore or equivalent)
4. Every API request includes: Authorization: Bearer <accessToken>
5. On 401 response → POST /auth/refresh with refreshToken
6. On refresh failure → redirect to login
```

---

## Data flow

Frontend never writes directly to the database.
All mutations go through the backend API.
The backend enforces ownership checks on every resource operation.

---

## Environment separation

| Environment | Backend URL | Notes |
|---|---|---|
| Development | http://localhost:3000 | Local Postgres |
| Staging | https://api-staging.yourapp.com | Mirrors prod schema |
| Production | https://api.yourapp.com | |
