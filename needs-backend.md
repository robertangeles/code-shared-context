# Needs — Backend Requests

Owner: backend (writes)
Reader: frontend (reads and fulfills)

When the backend session needs something from the frontend, it writes it here with status: pending.
When the frontend session fulfills the request, it updates the status to complete.

---

## Pending

### UI: Display email verification banner
- **Status:** pending
- **Requested:** 2026-05-03
- **Why:** users with unverified emails can register but can't access protected routes — the frontend needs to surface this clearly
- **What's needed:** a persistent banner or modal when `user.emailVerified === false`, with a "Resend verification email" button that calls POST /auth/resend-verification
- **Endpoint for the button:**
```typescript
POST /auth/resend-verification
// No body required — uses the auth token to identify the user
// Response 200: { message: 'Verification email sent' }
// Response 429: rate limited (max 3 per hour)
```

---

## Complete

### UI: Handle 401 with token refresh
- **Status:** complete
- **Requested:** 2026-04-30
- **Fulfilled:** 2026-05-01
- **Notes:** frontend implemented refresh interceptor in apiClient.ts — retries original request after successful refresh, redirects to login on refresh failure
