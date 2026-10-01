# Discovery: inspect the actual application

## Establish the source of truth

Identify current directory, Git roots/status, canonical frontend, any backend, duplicate app trees, package names, deployment scripts, build output and deployed entry point. Do not assume the current directory or a sibling named frontend is the deployed app. If contradictory deployment evidence remains, ask which tree is authoritative before writing application code. Do not delete duplicates. Record actual absolute paths in the migration plan, not in this reusable skill.

Read project instructions, package manifests, all lockfiles, compiler/build/router settings, CI, containers, reverse proxies, source entry points, generated route trees, migration directories and docs. Determine whether React is a browser SPA, TanStack Start server application, Next.js SSR application, or another runtime. A Vite configuration does not prove a static SPA. Trace wrappers such as `@lovable.dev/vite-tanstack-config`, Nitro, plugins, server functions and middleware before changing the build.

Search files and import/call graphs; treat a text match as a lead, not proof of runtime use. Prefer `rg --files --hidden` with exclusions for `.git`, dependency and generated build directories. Search variable names and file paths without displaying `.env` values or secret-bearing lines. Inspect ignored runtime configuration privately if authorized. Distinguish source, generated files, runtime config, tests, migration history and stale documentation.

## Capability inventory

Audit these categories even if `@supabase/supabase-js` is already absent:

| Category | Inspect and record |
| --- | --- |
| Product | Screens, routes, forms, roles, tenants, user journeys, accessibility, empty/error states, redirects, imports/exports, business invariants |
| Supabase queries | SDK imports and aliases, `.from`, query chains, REST/PostgREST URLs, client clones, `.rpc`, `.single`/`.maybeSingle`, pagination/count/filter behavior, generated row types |
| Auth | Login/logout/registration, session refresh, verification/reset, OAuth/magic links/OTP/MFA if used, invites/admin operations, profiles, metadata, account linking, guards, callback URLs, token storage |
| Schema | Tables/columns/types, PK/FK/unique/check constraints, indexes, sequences, defaults, nulls/enums, views/materialized views, functions/triggers, extensions, scheduled SQL, grants and RLS |
| Storage | Buckets, public/private access, signed URLs, metadata, object keys, limits, MIME types, transform/CDN behavior, upload/download/delete and references embedded in rich text |
| Realtime | Channels, postgres_changes, broadcast, presence, role-watch SSE/polling, subscriptions, reconnect and permission changes |
| Server behavior | Supabase edge functions, Next server actions/routes, TanStack `*.server.ts`/`*.functions.ts`, middleware, cron, workers, queues, email, billing, invitations, onboarding, rate limits |
| External services | Payments/webhooks, Jira or other imports, email templates/queues/retries, AI/RAG/model gateways/vector data, MCP tools/OAuth discovery/consent, analytics and delivery providers |
| Platform coupling | `@lovable.dev/*`, Lovable gateway/email URLs and headers, preview postMessage brokers, `.lovable` metadata, auth attachers, build wrappers, MCP plugins, server-only SDKs |
| Configuration | Variable names, consumers, runtime/build-time scope, public/secret classification, required/optional status, fallback behavior, CORS/site/API URLs, hardcoded hosts and issuers |
| Operations | DB/file backups, schema owners, multiple schema histories, CI, package manager, frontend/server deployment targets, production data size, test availability, observability |

Trace aliases such as `export const supabase = backend` and service clients sending `x-service-token`. Record generic `/api/query` and `/api/rpc/:name` as compatibility infrastructure. Inspect runtime requests and redirects where tools permit; search results alone cannot establish independence from a remote service.

## Schema, policies and function evidence

Compare authorized live introspection/schema export with versioned SQL, generated types and actual call sites. Record source date and drift. If live access is unavailable, label the repo-derived schema provisional. Generated TypeScript types do not establish triggers, RLS, constraints or function bodies. Request missing exports necessary for affected slices; do not fabricate columns or policy semantics.

For each RPC/function/trigger record source, inputs/outputs, caller, grants, SECURITY DEFINER/search_path concerns, transaction/locking behavior, side effects, error behavior and target replacement. SQL set operations may remain in backend-owned database functions when justified; expose named domain operations and enforce policy. Do not blindly translate everything to application loops or a raw SQL endpoint.

For every RLS policy record table/operation/role, USING visibility and WITH CHECK write rules, ownership/tenant joins, helper functions and permissive/restrictive composition. Build actor × resource × action cases, including denial. Missing historical policy coverage is a security blocker for affected features, not a cosmetic limitation.

## Deliver evidence

Assign stable IDs to workflows and dependencies. Use this ledger shape:

| ID | Source file/function | Behavior and capability | Domain | Target API/owner | Permission case | Data/file/identity impact | Frontend change | Verification | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

Use `pending`, `implemented-unverified`, `verified`, `blocked`, `not-applicable`, or `retained-by-explicit-decision`; do not mark a feature verified merely because code exists. Add temporary adapters and their exit criteria. Record known functional dependencies separately from dead code and historic SQL/docs.

Summarize baseline commands and results, feature/domain candidates, unknowns, integrations not used, schema discrepancies, data access limits, security findings and stack implications. Never generalize one attached project review's filenames, bucket names or provider choices into requirements for every project.
