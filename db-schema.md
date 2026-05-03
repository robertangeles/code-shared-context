# Database Schema

Owner: backend
Last updated: 2026-05-03

Shared tables that both projects need to understand.
The backend session updates this file when the schema changes.
The frontend session reads this to understand what data exists and what IDs refer to.

---

## Conventions

- All primary keys are UUIDs (v4), generated server-side
- All timestamps are ISO 8601 strings in UTC
- Soft deletes use `deleted_at` (nullable timestamp) — no hard deletes on user-owned data
- Foreign keys are named `{table_singular}_id`

---

## Tables

### users

```sql
CREATE TABLE users (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email           TEXT NOT NULL UNIQUE,
  password_hash   TEXT,                          -- null if OAuth-only
  display_name    TEXT NOT NULL,
  avatar_url      TEXT,
  email_verified  BOOLEAN NOT NULL DEFAULT false,
  created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
  deleted_at      TIMESTAMPTZ
);
```

Notes:
- `password_hash` is bcrypt or argon2, never plaintext
- `email_verified` must be true before the user accesses protected features
- Soft-deleted users retain their rows for 30 days then purge

---

### oauth_accounts

```sql
CREATE TABLE oauth_accounts (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  provider    TEXT NOT NULL,    -- 'google' | 'apple' | 'github'
  provider_id TEXT NOT NULL,    -- provider's user identifier
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),

  UNIQUE (provider, provider_id)
);
```

---

### refresh_tokens

```sql
CREATE TABLE refresh_tokens (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token_hash  TEXT NOT NULL UNIQUE,
  expires_at  TIMESTAMPTZ NOT NULL,
  revoked_at  TIMESTAMPTZ,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

Notes:
- Tokens rotate on every use — old token revoked, new token issued
- Expired and revoked tokens are purged nightly

---

### items (example resource — replace with your domain)

```sql
CREATE TABLE items (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id    UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title      TEXT NOT NULL,
  body       TEXT NOT NULL DEFAULT '',
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  deleted_at TIMESTAMPTZ
);

CREATE INDEX items_user_id_idx ON items (user_id) WHERE deleted_at IS NULL;
CREATE INDEX items_created_at_idx ON items (user_id, created_at DESC) WHERE deleted_at IS NULL;
```

---

## Indexes

All foreign keys have a corresponding index. Partial indexes exclude soft-deleted rows.
No speculative indexes — every index above has a named query it serves.

---

## Migrations

Managed via drizzle-kit. Migration files live in `packages/server/drizzle/`.
Never modify a migration file after it has been applied to any environment.
Always create a new migration for schema changes.
