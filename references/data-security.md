# Data, identity, security and platform replacement

## Database ownership and transactions

Select one authoritative schema/migration tool. Preserve PostgreSQL keys/relations, checks, indexes, enums, decimals/bigints, timestamps/time zones, JSON, sequences, views, functions, extensions and deletion semantics used by the product. Document API serialization choices (for example large integers/decimals as strings). Do not copy Supabase internal auth/storage schemas wholesale or silently omit unsupported types.

Baseline an existing target database deliberately; do not apply a fresh-schema migration blindly to populated tables. Freeze/archive competing Supabase/Drizzle histories with clear historical labels once the target history is verified. Remove obsolete migration commands/config/dependencies only after replacement. Disable automatic production schema synchronization; with TypeORM use `synchronize: false`. Review generated SQL and destructive changes. Run migrations as an explicit release job with concurrency control rather than from every application replica.

Keep queries in repositories/data access, parameterized and tenant-scoped. Define transactions for multi-step invariants and external side-effect coordination. Do not hold database transactions open while waiting for arbitrary external APIs; use idempotency/outbox/job patterns when the failure model warrants them. Consider race conditions, uniqueness, locking, pagination stability, indexes and representative query performance.

## Existing rows: separate from schema

Inventory source volume, sensitive fields and acceptable downtime. Define extraction permissions, snapshot timestamp, FK order, stable IDs/mapping tables, batch sizes, checkpoints, transforms, retry/idempotency and sequence resets. Back up and rehearse restoring the target. Import into an isolated target first; prevent rehearsal emails, webhooks and payments.

Reconcile row counts by table/tenant, key sets, checksums or aggregate totals, foreign-key orphans, null/enum conversions and representative relationships. Record source/target timestamps so comparisons are meaningful. Plan a write freeze or delta-capture/final-sync strategy; do not lose writes that occur after a snapshot. Preserve IDs referenced by storage, audit history and third-party systems, or migrate every reference through an explicit mapping.

A schema-only migration is not a populated application. If live access is absent, produce runnable import/reconciliation steps and mark data migration BLOCKED/NOT RUN. An empty target is acceptable only when the user explicitly chooses fresh-start scope.

## Identity migration and session security

Inventory actual auth flows and external identities. Choose a maintained auth/session library or provider that fits deployment. Map user IDs to application profiles/roles/ownership; migrate provider subject mappings and verification status deliberately. Do not assume Supabase password hashes or active refresh tokens are portable. Verify supported hash export/verification/import formats against the chosen auth system; otherwise provide a user-approved reset/re-enrollment or staged transition plan. Preserve security rather than importing unknown hashes as plaintext. Inventory MFA factors, recovery codes, passkeys and account-linking requirements if present. Verify secure portability with the target provider; if unsupported, plan explicitly approved re-enrollment without silently disabling protection or locking users out. Never export private authenticators insecurely. Document session invalidation and user communication needs.

Prefer Secure, HttpOnly cookies for browser session/refresh credentials when compatible with the architecture. Define SameSite/domain/path/expiry, HTTPS/proxy handling, CSRF protection for state-changing requests, CORS credential policy and cross-site hosting constraints. If bearer access tokens are appropriate, minimize browser exposure and keep refresh credentials out of localStorage where possible. Do not silently preserve an insecure legacy token storage strategy for compatibility.

Rotate refresh tokens, store refresh/session secrets safely (hash where applicable), handle reuse/revocation and concurrent refresh, and revoke on logout/password or relevant account changes. Set appropriate expiry and audience/issuer/signature validation. Use a vetted password hashing implementation with current suitable parameters. Apply brute-force protection and avoid account enumeration.

For OAuth use library-supported state/nonce/PKCE as applicable, exact callback allowlists, shared expiring state/session storage or a secure stateless alternative that works across replicas, and single-use replay checks. Never keep essential OAuth state only in one process Map. Do not put access/refresh tokens in query strings or URL hashes; use a safe code/session exchange. Make verification/reset/invitation tokens short-lived and single-use. Do not ship a required login method as a permanent 501 stub when credentials are absent; implement it and mark operational verification blocked.

## Authorization parity

Translate RLS into explicit domain policies, or deliberately retain defensible database RLS with validated request context and connection-pool reset behavior. A backend service role that bypasses RLS is not sufficient authorization. Do not trust role/tenant/owner IDs sent by the client. Derive them from verified identity plus current membership/role data.

Test anonymous, owner, other user, member/non-member, tenant A/B, admin and suspended/archived accounts as applicable. Cover select/insert/update/delete, list/count/search, bulk actions, invited users, role downgrade, onboarding, file access, realtime, jobs and admin/service endpoints. Enforce both old USING and WITH CHECK intent; pre-read authorization alone must not allow a race or a forbidden ownership change. Choose intentional empty-result versus 403/404 semantics and align route guards/UI behavior.

Keep privileged admin operations backend-only, narrowly scoped, logged and authorized. Eliminate frontend-server service-role bypasses when business use cases move. Required production secrets have no known default; fail startup. Redact secrets/tokens/PII in logs and error bodies. Rotate exposed credentials when authorized; do not reproduce their values in reports. Add rate limits, payload bounds, secure headers, injection protections and SSRF/URL allowlists for features that fetch user-supplied remote resources.

## File storage

Choose object storage or durable local storage based on scale/deployment. Implement private/public policies, upload limits, MIME/content checks, safe object names, path traversal prevention, download disposition, signed URL expiration and cleanup. Apply actor/tenant checks to signing as well as upload/download/delete; do not expose a private bucket as public to make old links work.

Import actual objects separately from metadata. Preserve or map keys, content type, ownership, cache headers and privacy; compare counts/bytes/checksums and sample downloads. Update URLs in rows, rich-text documents and frontend assets; expire/reissue old signed links rather than copying them. Define retry/checkpoints and orphan cleanup. Local disk is not shared across replicas: provide a durable shared strategy and backup if selected.

## Realtime and integrations

Replace only used realtime semantics. SSE may fit one-way role notifications; presence/broadcast/bidirectional use may require WebSockets. Authenticate subscriptions and filter by permission; recheck role changes, reconnect/resume events, and avoid cross-tenant fanout. Plan shared pub/sub when multiple instances require it; retain polling fallback if the original workflow relies on it.

Move used edge functions, frontend server business logic, scheduled jobs and integrations into backend feature modules or backend-owned workers. Preserve inputs, authorization, retries, concurrency, scheduling time zone and failure behavior. Verify webhook signatures against raw body, deduplicate event IDs, handle retry/out-of-order delivery and use provider test modes. Move rate limiting to a shared backing store when multi-instance correctness requires it.

For Lovable email, move templates, triggers, retries, previews/webhooks and actual product mail, not only auth emails, to a chosen provider. Use MailHog or another chosen local SMTP capture when email is used; do not require an unused mail service. For AI/RAG, inventory models, prompts, embeddings/dimensions, vector indexes, streaming, tool calls, quotas and stored data. Choose a provider explicitly, test equivalent behavior and re-embed/reindex only with a plan when models change.

For MCP, identify whether external consumers use it; preserve used tools, auth scope and consent semantics. Move the server to the backend as appropriate. Update issuer/audience, discovery metadata, JWKS/authorization/token endpoints, redirect registrations and clients as one auth migration. Prefer a standards-compliant provider/library; do not invent an OAuth server. A live Supabase issuer remains a dependency even if no SDK remains. Remove MCP only on explicit scope change or evidence it is unused.

Remove preview auth brokers, generated auth attachers, wrappers and legacy dependencies only after import/runtime analysis and behavior replacement. Keep historical SQL/docs clearly marked; update current docs, package names, comments and deployment paths so they do not misstate the active system.
