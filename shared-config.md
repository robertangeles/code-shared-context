# Shared Config

Owner: frontend (writes)
Reader: backend (reads)

Configuration values that both projects need to stay in sync on.
Update this file when any of these values change.

---

## API versioning

Current API version: v1
Base path prefix: /api/v1

Both projects agree on this prefix. The frontend prepends it to all requests.
The backend registers all routes under it.

---

## Pagination defaults

| Setting | Value |
|---|---|
| Default page size | 20 |
| Maximum page size | 100 |

The frontend uses these as defaults in all list views.
The backend enforces the maximum.

---

## Token config (informational — secrets stay in .env)

| Token | TTL |
|---|---|
| Access token | 15 minutes |
| Refresh token | 30 days |

The frontend uses the access token TTL to decide when to proactively refresh.
The frontend uses the refresh token TTL to decide when to force re-login.

---

## Feature flags

| Flag | Status | Notes |
|---|---|---|
| notifications | disabled | endpoint not yet built — see needs-frontend.md |
| dark_mode | enabled | |
| file_uploads | disabled | planned for v1.2 |

The frontend gates UI behind these flags.
The backend does not expose disabled feature endpoints in production.

---

## Shared constants

```typescript
export const MAX_TITLE_LENGTH = 255
export const MAX_BODY_LENGTH = 10000
export const MAX_DISPLAY_NAME_LENGTH = 50
export const SUPPORTED_AVATAR_FORMATS = ['image/jpeg', 'image/png', 'image/webp']
export const MAX_AVATAR_SIZE_BYTES = 5 * 1024 * 1024  // 5MB
```

Both projects use these values. The backend enforces them. The frontend validates against them before sending.
