# Implement vertical features and retire compatibility code

## Foundation sequence

1. Recheck canonical paths and user changes. Prepare independent target repositories without overwriting existing work. Keep source provenance and a reversible baseline.
2. Resolve compatible runtime/package versions and one package manager per repository. Create strict TypeScript, formatting/linting and meaningful build/test scripts. Do not add success-returning placeholder scripts.
3. Implement config validation, logging, errors, database client/migration path, auth/session primitives, HTTP security, request correlation, health/readiness, and OpenAPI generation.
4. Establish frontend routing/providers, shared API transport/error types, session handling, design primitives and contract consumption.
5. Implement one representative protected feature end-to-end, including a denied request and documented contract. Use it to validate boundaries before repeating across modules.

## Per-feature migration loop

For each inventory ID:
1. Reconstruct current visible behavior and business invariants from source/evidence. Identify related server functions, SQL, files, side effects and access policy.
2. Specify domain DTOs and endpoint(s), errors, permission matrix, pagination and transaction/idempotency requirements. Define acceptance cases before moving code.
3. Implement database migration/model and module repository/data access. Add service/use-case logic and resource policy, then thin controller and routes/Nest controller bindings. Implement validation and response mapping.
4. Generate/update OpenAPI and verify endpoint/request/response coverage. Test the actual endpoint with representative and denied actors.
5. Implement typed feature API calls and hooks/composables through the shared transport. Move view logic into focused feature components; preserve routes, deep links, UX, accessibility and error semantics.
6. Move related frontend server functions, edge functions and privileged provider operations into backend modules/workers. Keep only rendering and explicitly designed thin adapters in frontend.
7. Test the complete journey and its failure paths. Compare with baseline behavior; investigate differences rather than assuming they are improvements.
8. Remove the replaced shim, legacy queries/types/SDK calls/config and packages after import/call-site checks. Regenerate contracts and lockfiles with the selected package manager. Update evidence and status.

## Compatibility adapters are temporary

If an incremental adapter is needed, record its callers, supported operations, scope, reason, security boundaries, feature exit conditions and removal test. Never expose arbitrary client-selected tables, unbounded filters or service-role access. Do not add new product features to a generic `/api/query` or `/api/rpc/:name` facade. The final target uses named domain APIs and DTOs; wrapping or renaming the old chain is not sufficient.

Retire `supabase.from/rpc/auth/storage`, client clones, generated database row DTOs and misleading names only as callers move. Preserve necessary auth-header attachment, OAuth, storage and server-only checks in their replacements. A rename does not substitute for moving business rules or testing authorization.

## Realistic example of decomposition

If a ticket form previously creates a ticket, uploads attachments and sends email from frontend server functions, design the actual required failure model. A tickets module owns validation, access checks and ticket creation; a files module owns upload authorization/storage; notifications owns dispatch. Coordinate their public interfaces through a ticket use case. Use an outbox/job only when reliable asynchronous delivery is required. Provide a UI flow for upload failure and retries, and document whether attachments are staged before ticket creation. Do not make a controller issue unrelated table writes or return success after an untracked email failure.

## Cleanup and handover

Run import-aware and runtime-aware scans of both repositories, build outputs and deployment configuration. Review aliases, hardcoded remote URLs, issuers, package scripts, config and stale docs. Preserve historical migrations until the new path and imports are verified; archive rather than silently erase evidence. Remove competing active schema commands, duplicate lockfiles only after choosing the manager, and unused build plugins only after replacement production builds.

Do not delete a duplicate repository merely because it looks old. Correct setup/docs/CI to name the canonical trees. Keep only one current architectural description, link to historical audits, and update misleading RLS/Supabase session comments. Do not invent features from the example review; each cleanup must have source evidence.

If an external dependency blocks testing, finish reversible implementation only for slices whose schema, policy and behavior have sufficient evidence, and provide exact follow-up commands/configuration. Unknown policy/schema behavior blocks the affected implementation; do not substitute guesses and label them merely unverified. Mark the associated ledger items blocked rather than continuing to describe the full migration as done.
