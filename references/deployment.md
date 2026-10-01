# Environment, containers, CI and cutover

## Configuration contract

Maintain an environment inventory containing name, consumer, frontend/backend, public/secret, build-time/runtime, requirement, validation and example placeholder. Include only variables actually consumed in `.env.example`. Ignore real `.env` files; use deployment secret management. Never put database, JWT/session, SMTP, provider or service secrets in a public prefix or browser bundle. Public API/site URLs are configuration, not secrets. Remove obsolete Supabase/Lovable variables after their last consumer is replaced.

Centralize typed parsing, validate required values on startup, and fail on missing or known weak deployed secrets. Explicit local-only development values may be provided by isolated dev configuration, not fallback logic that accidentally enables production. Verify frontend URL behavior when values are baked during build versus read at runtime. Distinguish browser-facing origin from container-internal service URL. A browser cannot resolve a Docker service hostname such as backend.

Allowlist origins for credentialed CORS; do not combine wildcard origin with cookies. Define TLS, proxy trust, secure cookies, CSRF, CSP/headers and OAuth callback/public site URLs for actual hosting. Do not hardcode the old project's domain as a fallback.

## Development

When Docker Compose is applicable, use separate `docker-compose.dev.yml` and `docker-compose.prod.yml`. Keep orchestration ownership documented, normally in backend, and document sibling build paths precisely. Include backend and PostgreSQL for local standalone development; add frontend service or clear independent frontend instructions. Use MailHog or the selected local mail capture only when email exists. Include storage/queue dependencies only when chosen architecture requires them.

Provide database health checks, persistent volumes, migration command, deterministic installation, hot reload where practical, container networking and sample configuration. Wait for readiness rather than startup order alone. Bind dev-only database/admin/mail ports to local access. Seed only necessary reproducible non-sensitive development/bootstrap data, with idempotent commands. Do not create a known production admin password in seeds.

## Production preparation

Build separate frontend/backend images or provider-appropriate artifacts. Use multi-stage production builds, supported pinned runtimes, dependency lockfiles, a non-root runtime where supported, minimal production dependencies, and .dockerignore excluding secrets and irrelevant files. Never bake real secrets into ARG/ENV layers or images. Match image output/entry point to actual SSR versus SPA behavior; configure SPA deep-link rewrites and SSR startup appropriately.

Do not ship dev hot reload, bind-mounted source, MailHog or public PostgreSQL ports in production. Use persistent storage/backups and a managed DB or explicitly operated database. Do not deploy a second local database when the selected production architecture uses managed PostgreSQL. Define connection pooling, resource limits, shutdown/draining, liveness/readiness and migration release jobs. Test required secret validation in production mode.

Configure frontend API origin, reverse-proxy routing, allowed origins, TLS, cookie domain, provider callbacks, object storage policy, health monitoring and log redaction. Resolve cross-origin/cross-site cookie behavior with the actual domain arrangement; do not assume localhost behavior proves production auth.

## Independent CI and contract compatibility

Each repository must install from its own lockfile and run formatting/lint/type checks, meaningful tests and a production build. Backend CI uses isolated PostgreSQL to apply migrations and test APIs/authorization. Generate/lint OpenAPI and run route/spec/response checks. Frontend CI checks against a pinned contract artifact/client and runs component/core journey tests as applicable. Never copy backend source from a sibling path to make CI pass.

Record security/dependency scan results, necessary exceptions and build artifacts. Define a contract compatibility check and deployment order for breaking changes. Prefer additive API/database changes, migrate clients/data, then remove old behavior after verified usage has ended.

## Rehearsal and cutover runbook

Prepare before production execution:
1. Record source/target versions, environment, authorized operator, downtime window and success/rollback criteria.
2. Snapshot database and objects; rehearse a restore in isolation and record evidence.
3. Apply additive target migrations, import rows/identities/files with checkpoints, and reconcile.
4. Disable real external side effects in rehearsal; use test provider accounts and SMTP capture.
5. Configure secrets, provider webhooks/redirects, issuer metadata, origins and monitoring; validate with representative accounts.
6. Freeze source writes or apply final captured deltas; reconcile again with timestamps.
7. Deploy compatible backend then frontend, switch routes/DNS as planned, invalidate obsolete sessions/caches and execute smoke/permission tests.
8. Monitor defined error/login/job/webhook/data metrics; keep the previous release and backups until acceptance.
9. Roll back if thresholds fail. Explain how writes made after cutover are preserved/reconciled: rolling back only code or restoring an old snapshot can lose data. Prefer forward-compatible expand/contract schemas and a tested reverse/delta strategy where required.
10. Decommission old services only after explicit authorization, completed observation and proven absence of consumers.

Do not execute production deployment, DNS changes, live imports/destructive migrations, provider account changes or service decommissioning without relevant user authorization. Do prepare scripts/config/runbook and isolated validation proactively. Keep “deployment prepared,” “deployed,” and “production cutover verified” as separate statuses.
