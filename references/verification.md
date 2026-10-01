# Verification and completion gates

## Evidence rules

Record each check's command or manual procedure, repository/environment, result and supporting output/artifact without secrets. Use PASS, FAIL, BLOCKED, NOT RUN or NOT APPLICABLE. Explain why a check is not applicable; unused features do not require implementation. Never infer successful runtime behavior from compilation, a text scan, mock-only tests or a missing test command.

Fix actionable failures within scope and rerun affected checks. External credentials, production access and unavailable runtimes can block checks without blocking all useful work. Clearly separate implementation completeness, verification completeness and cutover readiness.

## Required checks

| Gate | Evidence required |
| --- | --- |
| Source/canonical roots | Source and target roots match deployment evidence; no accidental duplicate-tree edits; independent repository roots and package/build boundaries |
| Architectural structure | Actual feature modules, thin routes/controllers, backend services/use cases, isolated persistence, frontend feature API/hooks, explicit public exports; no unnecessary empty layers |
| Type/build quality | Independent clean dependency installs, strict type checks, lint/format and production builds; valid framework runtime and route deep links |
| Functional parity | Every inventory workflow mapped and exercised, including forms, empty/error states, redirects, tenant/role changes, imports/exports and accessibility-sensitive interactions |
| Database | Fresh migrations plus realistic upgrade/baseline path, constraints/relations/indexes, transaction rollback and representative query behavior; only one active migration authority |
| Rows/identities/files | Separate import results or explicit fresh-start decision; count/key/checksum/relationship reconciliation, identity mappings/session transition and sampled file access |
| Authorization | Positive and negative API tests for discovered RLS/policy cases, cross-tenant/ownership checks, list/count/bulk actions, files and privileged endpoints |
| Authentication | Required login/logout/signup/reset/verify/OAuth flows; expired/revoked sessions, refresh races, single-use tokens, multi-instance state, no URL-token leaks or known production secret defaults |
| API contracts | Spec parses, route coverage matches, validation/response/error schemas match live responses, stable operation IDs/security schemes and generated client compatibility |
| Client behavior | Central transport, timeout/cancel, error mapping, refresh retry bounds, file/no-body handling, cache isolation/invalidation and no raw API calls in presentation code |
| Integrations | Used email, jobs, webhook signatures/retries, AI/RAG, MCP issuer/discovery, realtime reconnect/role filtering and provider test behavior |
| Runtime security | Missing required deployed secrets fail startup; no privileged secrets in browser output/logs; CORS/CSRF/cookies/payload limits/abuse controls verified |
| Deployment | Actual selected production artifact runs, containers/config validate, readiness/networking/migrations work; no dev services exposed; backup/restore and rollback rehearsed where access permits |
| Cleanup | Classified remaining dependency/name/URL hits, obsolete shims removed, authoritative docs/config/lockfiles and canonical deployment paths |

## Minimum meaningful test suite

Use the selected stack's compatible testing tools. If no tests exist, add risk-based tests rather than skipping the gate. Prioritize:
- service/use-case tests for nontrivial domain rules and denied transitions;
- real isolated PostgreSQL integration tests for tenant filtering, joins, constraints, transactions and migration execution;
- HTTP endpoint tests for malformed payloads, auth, authorization, conflict and documented outputs;
- frontend component/form tests for field errors, pending/error states and permissions;
- browser E2E for login and each critical observed journey, including one failure/denied path;
- contract tests for representative success/error responses and CI route/spec coverage;
- import/reconciliation tests on sanitized fixtures and representative volume when available.

Example adversarial cases: user A reads user B's object; member attempts owner reassignment; tenant A filters for tenant B; anonymous downloads a private file; role downgrade invalidates access/cache/SSE; concurrent refresh produces safe session behavior; duplicate webhook does not duplicate a payment; interrupted import resumes without duplicated rows; multi-row mutation rolls back; production boot without secrets fails. Select cases based on actual features.

## Scan both code and behavior

Search source, lockfiles, build/config, environment variable consumers, current docs and runtime network activity where available. Classify every remaining `@supabase`, `supabase.`, `.supabase.co`, `SUPABASE_*`, `@lovable.dev`, Lovable email/AI URLs, old issuer, SDK clone, `/api/query`, dynamic RPC, service-role client and legacy schema tool reference as:
- active runtime dependency;
- temporary compatibility path with exit condition;
- explicitly retained provider with user decision;
- historical migration/source evidence;
- test/generated artifact;
- dead code to remove after import/build checks.

Inspect aliases and semantics: SDK absence or renamed variables does not prove decoupling. Inspect frontend server modules for persistence/business logic even if browser code is clean. Inspect build outputs and network requests for secrets and unintended provider URLs without printing sensitive values. Do not delete historical evidence just to achieve zero grep matches.

## Lessons from the supplied migration review

Use these as reusable regression cases, not as assertions about every source project:

| Prior failure pattern | Required prevention/check |
| --- | --- |
| SDK removed but SDK-shaped facade and table-query endpoint remain | Domain DTO/API migration; inspect callers and remove adapter at exit gate |
| Business logic left in TanStack/Next frontend server | Inventory and transfer privileged use cases; allow only rendering/thin API adapters |
| Auth SMTP migrated but product mail/AI still uses Lovable | Trace each sender/gateway/template; test replacement and disclose retained provider |
| MCP tools use new API but OAuth trusts Supabase issuer | Verify metadata, issuer/audience and complete authorization flow together |
| Target schema exists but rows/files were never imported | Separate schema, rows, identities and objects evidence/reconciliation |
| Only some historical RLS behavior ported | Source policy/actor matrix and positive/negative tests; block affected feature completion |
| localStorage refresh, URL hash tokens, known secret defaults | Session redesign and explicit production security tests |
| OAuth state only in one process | Multi-instance callback/replay behavior test |
| Duplicate source trees and three schema authorities | Canonical root evidence and single active migration tool |
| Lovable Vite preset removed before server runtime replaced | Trace build/plugin/server dependencies and prove replacement production runtime |
| Giant query/services/components, row types in UI | Module boundary review, focused files and domain contracts |
| Stale docs, naming, unused env, two lockfiles | Import-aware cleanup, one chosen manager, current setup/deploy docs |
| No timeout, duplicated session gates/headers | Central transport/session source with timeout/refresh/error/cache tests |
| Swagger exists but drifts from implementation | Generated/validated contract, route coverage and response tests |

## Final migration report

Produce a concise summary plus linked repository evidence:
1. Selected stack/versions, canonical repository destinations and architecture decisions.
2. Domain modules, actual trees, significant created/modified/removed files and boundary review.
3. Ledger: original behavior → endpoint/module/frontend/data change → test evidence → status.
4. API version/contract location, Swagger access, client generation/version update procedure.
5. Schema/migration owner and separate row, identity and storage import/reconciliation results.
6. Auth/session/authorization changes, security checks and any unresolved policy differences.
7. External integrations, frontend-server moves and explicitly retained providers.
8. Environment variable names (no values), dev/prod setup, CI and deployment/cutover commands.
9. Tests performed with actual outcomes and blocked/not-run checks.
10. Remaining dependency references, temporary adapters, legacy docs/schema and disposition.
11. Limitations, rollback and exact remaining user/provider actions.

Call the migration complete only for the agreed scope when required evidence passes. If only code/config are prepared, say so. Do not label live data migrated, dependencies removed, or production ready based solely on an implementation plan. Any still-required compatibility facade, incomplete authorization, unimported required data/files or unverified critical auth flow blocks full migration sign-off.
