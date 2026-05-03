# Shared Context — Session Instructions

This folder is a shared brain for two Claude Code projects that depend on each other.
It contains no runnable code. Every file is plain markdown.

---

## What this folder is for

Two Claude Code sessions cannot communicate directly. This folder is the bridge.
Each session reads from it at the start of every cross-project session.
Each session writes to it when something in its project changes that the other project needs to know.

---

## File ownership

| File | Owner | Readers |
|---|---|---|
| api-contracts.md | backend | frontend |
| db-schema.md | backend | frontend |
| needs-backend.md | backend | frontend |
| needs-frontend.md | frontend | backend |
| shared-config.md | frontend | backend |
| architecture.md | both (append only) | both |
| decisions.md | both (append only) | both |

**One writer per file. Never write to a file you don't own.**

---

## Rules

1. Read all files in this folder at the start of every cross-project session.
2. If you make a change that affects the other project, update the relevant file immediately.
3. If you need something from the other project, write it to your needs file with status: pending.
4. If you see a pending entry in the other project's needs file, fulfill it and mark it complete.
5. All significant cross-project decisions go in decisions.md with today's date.
6. Never guess at API shapes or schema. Read api-contracts.md and db-schema.md first.
7. Never delete entries from decisions.md. It is append-only.
