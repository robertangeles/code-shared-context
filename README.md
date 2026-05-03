# Shared Context

Human-readable overview of the two projects this folder connects.

---

## Projects

| Project | Repo | Role |
|---|---|---|
| Backend | ../repo-backend | REST API, database, auth, business logic |
| Frontend | ../repo-frontend | Client app, UI, consumes backend API |

---

## How to use this folder

This folder is not a project. It has no package.json, no build step, no tests.
It is a set of markdown files that two Claude Code sessions read and write to keep themselves in sync.

Open a Claude Code session in either project repo and instruct it to read this folder at session start.
Both repos have a Shared Context section in their CLAUDE.md that handles this automatically.

---

## Folder contents

| File | Purpose |
|---|---|
| CLAUDE.md | Instructions for Claude Code sessions |
| README.md | This file |
| api-contracts.md | Endpoint contracts, owned by backend |
| db-schema.md | Shared database tables, owned by backend |
| architecture.md | How the two projects fit together |
| decisions.md | Append-only log of cross-project decisions |
| needs-frontend.md | Requests from frontend to backend |
| needs-backend.md | Requests from backend to frontend |
| shared-config.md | Shared configuration values |

---

## Updating this folder

When you change something that affects the other project, update the relevant file.
The other session picks it up at its next start.
