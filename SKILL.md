---
name: lovable-supabase-migration
description: >
  Migrates a Lovable-generated React frontend off Supabase (database, auth,
  RPC/functions, storage, realtime, and the Supabase client SDK) into a
  separate React frontend plus a Node.js/Express backend using TypeORM,
  PostgreSQL, and Docker (dev compose with MailHog; prod compose without it).
  Use when the user asks to migrate off Supabase, replace Supabase with
  Express/PostgreSQL/TypeORM, split a Lovable React app into frontend and
  backend, or move Supabase auth, RPC, storage, or realtime to a Node API.
  Always audit the actual repository before changing code. Create the backend
  one directory above the frontend root, never inside it.
---

# Lovable Supabase to Express Migration

Migrate an existing Lovable React app that uses Supabase into separate frontend and backend applications. Adapt every decision to what the repository actually contains. Do not assume a fixed schema, auth flow, or feature set.

## Hard rules

1. Do not modify the codebase until discovery, inventory, and a migration plan exist.
2. Do not remove Supabase functionality before its replacement works.
3. Never assume how the application works. Inspect the code first.
4. Preserve business logic, database relationships, and authorization behavior.
5. The frontend must never connect directly to PostgreSQL.
6. Do not use TypeORM `synchronize: true` for production. Use migrations.
7. Do not put secrets in source control. Do not hardcode secrets.
8. Do not add unnecessary dependencies or frameworks.
9. Do not rewrite working frontend components unnecessarily. Prefer incremental changes.
10. Keep the migration reversible where practical. Document architectural decisions.
11. Do not invent features the app does not use (email workflows, OAuth, storage, realtime). Still configure MailHog in development even if the app sends no email.
12. Create APIs from actual frontend requirements, not from the mere existence of a table.
13. Do not blindly copy Supabase's internal `auth` schema into TypeORM.
14. Do not assume removing RLS is safe. Backend authorization must preserve policy behavior.
15. Do not simply replace RPC calls with raw SQL. Put business logic in the backend.

## Where the backend lives

The existing repository is the React/Lovable frontend. Create the backend **one directory above the frontend root**.

```
/projects/
├── my-app/       # existing React frontend (current repo)
└── backend/      # new Node.js/Express backend
```

Before creating anything, determine the actual frontend root and its parent. Do not create the backend inside the React project.

## Lifecycle

Copy this checklist and update it as you go. Do not skip ahead.

```
Migration progress:
- [ ] 1. Discovery
- [ ] 2. Architecture analysis
- [ ] 3. Migration plan (stop and present this before coding)
- [ ] 4. Backend scaffolding
- [ ] 5. Database migration
- [ ] 6. API implementation
- [ ] 7. Authentication migration
- [ ] 8. Storage, realtime, and other Supabase functionality
- [ ] 9. Frontend migration
- [ ] 10. Docker and development environment
- [ ] 11. Verification
- [ ] 12. Documentation and migration report
```

Read the matching reference at the start of each phase. References are one level deep.

| Phase | Read |
| --- | --- |
| 1–3 Discovery, analysis, plan | [discovery.md](discovery.md) |
| 4–8 Backend, database, API, auth, storage, realtime | [implementation.md](implementation.md) |
| 9–10 Frontend, env, Docker, CORS, email | [frontend-docker.md](frontend-docker.md) |
| 11–12 Verification, tests, docs, report | [verification.md](verification.md) |

## Before any code change

Produce these artifacts from the repository, not from assumptions:

1. Repository analysis
2. Supabase dependency inventory
3. Database and schema analysis
4. Authentication analysis
5. API inventory
6. Storage analysis (or an explicit "not used")
7. Realtime analysis (or an explicit "not used")
8. Proposed target architecture
9. Migration plan

Present them to the user. Proceed with implementation after the inventory is reasonably complete. If a phase reveals a contradiction with an earlier assumption, update the inventory before continuing.

## Target stack

- Existing React frontend, kept as its own application
- New Node.js + TypeScript + Express backend
- PostgreSQL via TypeORM (entities, relations, migrations)
- Dockerized backend and PostgreSQL
- MailHog for local email testing
- Backend APIs replacing Supabase usage

Preferred backend layout (adapt only when the app's requirements justify it):

```
backend/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── entities/
│   ├── middleware/
│   ├── migrations/
│   ├── routes/
│   ├── services/
│   ├── repositories/
│   ├── validators/
│   ├── utils/
│   ├── app.ts
│   └── server.ts
├── test/
├── Dockerfile
├── docker-compose.dev.yml
├── docker-compose.prod.yml
├── package.json
├── tsconfig.json
├── .env.example
└── README.md
```

## Completion output

When finished, produce the report in [verification.md](verification.md): migration summary, files created and modified, Supabase functionality replaced, entities, endpoints, auth, Docker, environment variables, tests performed, remaining Supabase references, known limitations, and manual verification steps.
