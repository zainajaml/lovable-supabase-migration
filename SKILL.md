---
name: lovable-supabase-migration
description: >-
  Analyze and migrate Lovable applications using Supabase into separate frontend
  and backend repositories with feature-based architecture. Support Next.js,
  React.js, or Vue.js frontends and Express.js or NestJS backends. Use for a
  complete migration, migration planning, or repair of a partial migration that
  still has Supabase-shaped clients, generic query endpoints, Lovable services,
  frontend business logic, or incomplete data and authorization migration.
---

# Lovable + Supabase migration

Rebuild the application around its actual domains and behavior. Preserve working user journeys, visual behavior, data integrity, and access rules while replacing platform coupling. Treat a source review as evidence to investigate, not proof about a different project.

## Portable execution

Use ordinary repository reads, edits, searches, shell commands, and questions available in the host agent. Do not require proprietary commands, plugins, MCP servers, agent delegation, or tool-specific frontmatter. Resolve reference links relative to this SKILL.md, not the application working directory. If a tool or external system is unavailable, continue with supported work and report the exact unverified dependency. Never claim a command, live audit, import, or deployment was performed when it was not.

For Claude Code, place this complete folder at `.claude/skills/lovable-supabase-migration/`; for Cursor use `.cursor/skills/lovable-supabase-migration/`. Keep `references/` beside SKILL.md. Personal equivalents are under `~/.claude/skills/` and `~/.cursor/skills/`. The core files are identical; `agents/openai.yaml` is optional host metadata. Start with: “Use lovable-supabase-migration to analyze this project, ask me for the target stack, and migrate it into separate modular repositories.”

## Non-negotiable outcomes

- Analyze before choosing architecture; ask for the missing frontend and backend selection before scaffolding. Never silently select Express/TypeORM/React from the old skill.
- Create independent frontend and backend repositories with their own manifests, lockfiles, configuration, tests, CI, and deployment boundaries. Two folders in one repository do not satisfy this requirement.
- Organize both applications by business feature. Keep transport, validation, business rules, persistence, and presentation distinct. No generic everything-service or global table-query API as the final architecture.
- Keep controllers and route/page files thin. Put queries in repositories/data access and rules in services/use cases. Do not wrap existing `supabase.from()` chains in differently named files and call the migration complete.
- Keep backend business logic, privileged integrations, jobs, and persistence out of the frontend repository. Next.js/SSR rendering and thin session/API adapters may remain there; duplicate business logic may not.
- Preserve required functionality. Remove a used capability only when the user explicitly changes scope. Missing credentials mean blocked verification, not “not used.”
- Treat schema creation, existing-row migration, authentication identity migration, and storage-object migration as four separate workstreams.
- Use one authoritative schema/migration history. Never run competing Supabase, Drizzle, and target ORM migration paths against the target database.
- Preserve authorization previously enforced by RLS through explicit tested backend policies, or deliberately configured target database RLS with verified request context and connection-pool isolation. Tenant/ownership checks apply to every read/write, upload/download, job, integration, and realtime channel.
- Document and test OpenAPI throughout implementation. A Swagger UI shell alone is insufficient.
- No browser database credentials, service-role keys, known deployed secret defaults, or tokens in redirect URLs. Never print secret values during audits.
- Keep changes reversible; preserve original code/history and migration evidence. Do not overwrite sibling directories or delete duplicate repositories on assumption.
- Do not claim complete/production-ready when data, permissions, platform dependencies, or required runtime verification remain unresolved.

## Load references at the relevant phase

| Phase | Required reference |
| --- | --- |
| Discovery and source evidence | [discovery.md](references/discovery.md) |
| Stack decisions, domain boundaries, directory trees | [architecture.md](references/architecture.md) |
| Express/NestJS, layers, API and OpenAPI | [backend.md](references/backend.md) |
| Next.js/React/Vue, state, forms, client and assets | [frontend.md](references/frontend.md) |
| Database, identities, authorization, integrations and files | [data-security.md](references/data-security.md) |
| Feature-by-feature implementation and cleanup | [implementation.md](references/implementation.md) |
| Environment, Docker, CI and cutover preparation | [deployment.md](references/deployment.md) |
| Acceptance gates, negative tests and final report | [verification.md](references/verification.md) |

## Workflow and gates

### 1. Discover

Read discovery.md. Determine the canonical source and deployed tree, framework/runtime, domains, integrations, Supabase use, schema, identity flows and permissions. Audit existing migration work before replacing it. Record file/function evidence, uncertainty, and live-service access limits. Establish baseline build and core-journey behavior where possible.

**Gate:** Produce an evidence-backed inventory and explicit unknowns. Do useful read-only analysis without waiting for stack selection. Resolve unknowns that affect an implementation slice before coding that slice; do not invent schema or authorization.

### 2. Ask for the target stack

After presenting concise findings, ask only unanswered choices:
1. Frontend: **Next.js**, **React.js**, or **Vue.js**.
2. Backend: **Express.js** or **NestJS**.
3. Data/identity scope: fully leave Supabase (default intended goal), or explicitly retain named managed capabilities? Must existing users, rows, and files carry over?
4. Repository destinations and material hosting constraints, if unknown.

Reuse explicit session decisions. Recommend a database library, auth approach, routing setup, package manager and deployment profile with reasons; do not ask the user to choose every package. Keep PostgreSQL semantics by default; treat a database-engine change as additional scope. If answers are unavailable, deliver discovery and the choices needed; do not choose a stack silently.

### 3. Present architecture and migration plan

Read architecture.md and the applicable frontend/backend branches. Before source implementation, present the 12-part architecture summary, actual proposed trees, repository paths, API/domain map, dependency choices, parity matrix, risks, test strategy, and sequenced migration plan. Identify retained dependencies explicitly. User authorization to migrate permits implementation after required choices and the plan are established; do not introduce repeated approval gates. A request for analysis/plan only stops here.

**Gate:** Every observed workflow maps to a target module and verification case. Every temporary adapter has a removal condition. Record substantive assumptions instead of hiding them.

### 4. Establish foundations

Create collision-safe sibling repositories or use the authorized existing repository destinations. Preserve source history/worktrees and user edits. Do not nest backend inside frontend or initialize over an existing unrelated checkout. Add strict TypeScript, configuration validation, persistence/migrations, auth policy plumbing, errors/logging, health endpoints, API contract generation, central frontend client, and framework routing. Read data-security.md before selecting auth or moving data.

### 5. Migrate vertical features

Read implementation.md. For each feature: specify domain contract and permission cases; implement persistence, service, controller and documented endpoint; add feature API functions and hooks/composables; migrate views; verify positive/negative flows; remove replaced shims. Update the migration ledger. Migrate active frontend server functions, Lovable email/AI/MCP, background jobs, webhooks and edge functions as discovered. Do not defer all tests or OpenAPI until the end.

### 6. Rehearse data and deployment

Read deployment.md. Prepare and test schema/data/identity/file import in isolated targets, environment examples, independent builds, development containers and production deployment configuration. Rehearse backup/restore, compatibility rollout and rollback. Production cutover or destructive operations require authorization specific to that environment; preparation and isolated rehearsals do not require repeated approval.

### 7. Verify and hand over

Read verification.md. Run builds, types, lint, meaningful tests, contract checks, negative authorization tests, migration/data reconciliation, core UI flows, dependency scans and production-mode startup checks. Report PASS/FAIL/BLOCKED/NOT RUN/NOT APPLICABLE with evidence. Continue fixes within scope until gates pass or a real external blocker remains. Never count a missing test script as a passing test.

## Keep resumable evidence

Maintain `docs/migration/` in the backend repository once implementation starts, with a short pointer from frontend documentation. During analysis-only work, present the same artifacts in the response unless files were requested.

- `discovery.md`: canonical roots, source evidence, runtime/capability inventory and baseline.
- `architecture.md`: all 12 decisions and proposed trees.
- `plan.md`: sequence, dependencies, rollback and open decisions.
- `ledger.md`: source workflow → module/API → authorization → frontend → data/files → test evidence → status.
- `verification.md`: commands, results, runtime environment and blockers.
- `cutover.md`: operational sequence, backups, thresholds and rollback.

On resume, read these artifacts and current changes, verify completed evidence is still applicable, and continue at the earliest unmet gate. Do not restart discovery or repeat resolved questions unnecessarily.
