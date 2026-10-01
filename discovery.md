# Discovery and analysis

Do this before any implementation. Base every finding on files in the repository.

## Phase 1 — Repository audit

Inspect the whole frontend repository. At minimum cover:

- `package.json`
- `package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`
- `src/`, `components/`, `pages/`, `hooks/`, `services/`, `lib/`, `utils/`
- `contexts/`, `providers/`, routes
- environment files and configuration files
- Supabase configuration and generated Supabase types
- database-related files, migrations, SQL files
- edge functions and serverless functions
- authentication, storage, and realtime code

Search the entire repository for:

- `@supabase/supabase-js`
- `createClient`
- `supabase.from()`
- `supabase.rpc()`
- `supabase.auth`
- `supabase.storage`
- `supabase.channel()`
- `supabase.realtime`
- `supabase.trigger`
- Supabase REST APIs
- Trigger Functions
- Supabase Edge Functions
- SQL functions
- database triggers
- RLS policies
- environment variables containing Supabase configuration
- direct database access
- authentication and session handling
- OAuth providers
- email authentication
- password reset
- magic links
- OTP
- user profiles
- roles
- permissions
- subscriptions
- file uploads
- public and private storage buckets

Supabase may be more than a PostgreSQL database. Identify every Supabase capability the application actually uses.

## Phase 2 — Migration inventory

Do not begin implementation until this inventory is reasonably complete.

For every dependency record:

- File
- Function or component using it
- Supabase feature
- Current behavior
- Data involved
- Required backend replacement
- Required frontend change
- Migration status (start as `pending`)

Use this shape:

| Frontend File | Supabase Usage | Backend Replacement | Frontend Change | Status |
| --- | --- | --- | --- | --- |
| src/services/users.ts | supabase.from("users") | GET /api/users | Replace Supabase query | pending |
| src/auth/Auth.tsx | supabase.auth.signInWithPassword | POST /api/auth/login | Replace authentication | pending |
| src/services/orders.ts | supabase.rpc("create_order") | POST /api/orders | Replace RPC | pending |
| src/storage/upload.ts | supabase.storage | POST /api/files | Replace storage | pending |

Include rows only for usages that exist. Group by feature: queries, RPC, auth, storage, realtime, edge functions.

## Phase 3 — Database schema

Determine the schema the application actually requires. Analyze:

- tables, columns, data types
- primary keys, foreign keys, unique constraints, indexes
- nullable fields, default values, enums
- relationships, junction tables
- views, materialized views
- triggers, functions, stored procedures, sequences
- RLS policies, database roles
- seed data, migrations

Pay particular attention to relationships. The goal is to reproduce existing database behavior in PostgreSQL managed through TypeORM.

Sources, in order: SQL migrations and schema files in the repo, generated Supabase types, then query usage that implies columns. If schema sources conflict, record the conflict. Do not invent columns to fill gaps.

## Authentication database

Determine how the application uses Supabase Auth. Identify which of these the frontend actually depends on:

- users, identities, sessions, refresh tokens
- email verification, password reset
- OAuth, magic links, OTP
- user metadata
- application profiles, roles, permissions

Do not copy Supabase's internal `auth` schema into TypeORM. Design backend-owned authentication that reproduces the behavior the frontend depends on, and list the TypeORM entities that requires.

Record auth flows the app does **not** use so they are not implemented later.

## Phase 4 — RPCs and functions

Find every `supabase.rpc(...)` call. For each RPC:

1. Find its implementation if available.
2. Parameters.
3. Return value.
4. Database side effects.
5. Validation rules.
6. Permissions and RLS dependencies.
7. Whether the replacement should be a REST endpoint, a service method, a database operation, or a transaction.

Business logic belongs in the backend. Do not plan a raw-SQL pass-through unless the RPC is a pure query with no rules.

Also inventory Edge Functions and other serverless functions: trigger, inputs, outputs, secrets they use, and the backend replacement.

## Phase 5 — RLS policies

Identify every Row Level Security policy that affects the application. For each policy record what it allows or prevents, which role it applies to, and how the backend will enforce the same behavior explicitly.

Document any RLS behavior that cannot be reproduced exactly, and why.

## Phase 6 — Storage

Determine whether Supabase Storage is used. If it is, identify:

- buckets and whether each is public or private
- uploads, downloads, signed URLs
- file deletion, file metadata, access policies

Document the equivalent backend storage architecture. Do not plan to drop storage silently.

If storage is unused, write explicitly: no storage migration is required.

## Phase 7 — Realtime

Search for `channel()`, `.on(`, `subscribe()`, `postgres_changes`, `broadcast`, and `presence`.

If realtime is used, record which screens depend on it and the replacement (WebSockets or Server-Sent Events). Prefer the smaller mechanism that preserves the behavior.

If realtime is unused, write explicitly: no realtime migration is required.

## Analysis deliverable

Write the nine pre-implementation artifacts listed in SKILL.md into the conversation (and into repo docs only once implementation starts, per the documentation phase). The migration plan must name the frontend root, the parent directory, and the backend path.
