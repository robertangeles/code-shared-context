# Needs — Frontend Requests

Owner: frontend (writes)
Reader: backend (reads and fulfills)

When the frontend session needs something from the backend, it writes it here with status: pending.
When the backend session fulfills the request, it updates the status to complete.

---

## Pending

### Endpoint: GET /user/notifications
- **Status:** pending
- **Requested:** 2026-05-03
- **Why:** notification bell in the header needs an unread count and a list of recent notifications
- **Expected shape:**
```typescript
{
  unreadCount: number
  notifications: Array<{
    id: string
    type: string        // 'mention' | 'reply' | 'system'
    message: string
    readAt: string | null
    createdAt: string
  }>
}
```
- **Notes:** paginate if the list gets long, but 20 most recent is fine for v1

---

## Complete

### Endpoint: PATCH /user/profile
- **Status:** complete
- **Requested:** 2026-05-01
- **Fulfilled:** 2026-05-02
- **Notes:** ships in api-contracts.md under User section
