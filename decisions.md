# Decisions Log

Append-only. Never delete or edit existing entries.
Add new entries at the top with today's date.

---

## 2026-05-03

**Decision:** Use UUIDs for all primary keys instead of auto-incrementing integers.

**Reason:** The frontend generates temporary IDs for optimistic UI updates. UUIDs avoid collisions when those IDs are reconciled with backend-generated IDs after a successful API call.

**Alternatives considered:** Integer IDs with a client-side temp ID mapping layer. Rejected — adds complexity for minimal gain given our scale.

---

## 2026-05-03

**Decision:** Refresh tokens rotate on every use.

**Reason:** Reduces the window of exposure if a refresh token is stolen. Old token is revoked immediately when a new one is issued.

**Alternatives considered:** Long-lived static refresh tokens. Rejected — too much exposure risk.

---

## 2026-05-03

**Decision:** Soft deletes for all user-owned data.

**Reason:** Allows recovery of accidental deletions within a 30-day window. Also satisfies data retention requirements for some jurisdictions.

**Alternatives considered:** Hard deletes. Rejected — no recovery path.
