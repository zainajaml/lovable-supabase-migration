# Backend implementation

Start only after the discovery artifacts in [discovery.md](discovery.md) exist. Implement replacements before removing the Supabase call they replace. Update the inventory status as each item lands.

## Scaffolding

Node.js, TypeScript, Express, TypeORM, PostgreSQL, Docker, Docker Compose. Clean architecture. Use the layout in SKILL.md unless the app's requirements justify a change. Do not introduce extra frameworks.

Create the backend in the parent of the frontend root.

## TypeORM

Use TypeORM for entities, relationships, migrations, indexes, constraints, and database access.

Use real relations: `OneToOne`, `OneToMany`, `ManyToOne`, `ManyToMany`. Match relationships found in discovery.

`synchronize: true` is forbidden for production. Schema changes go through TypeORM migrations. The database setup must be reproducible:

1. Start PostgreSQL.
2. Run TypeORM migrations.
3. Create required tables.
4. Apply required seed or bootstrap data.
5. Start the backend.
6. Backend connects to PostgreSQL.

If the Supabase project has migrations or schema SQL, reuse that information. Do not recreate the schema from assumptions.

## API design

Create an API for every backend operation the frontend currently performs through Supabase. For each endpoint specify:

- HTTP method and path
- body, query, and path parameters
- authentication requirements
- authorization requirements
- response schema
- error behavior
- database operations
- transaction requirements

Prefer REST. Use a consistent prefix:

```
/api/auth/...
/api/users/...
/api/projects/...
/api/files/...
```

Do not create an endpoint only because a table exists.

## Authentication

The backend owns authentication. Implement only the flows discovery showed the app uses, plus whatever the architecture needs to support those flows. Typical flows to check, not to implement blindly:

- login, logout, registration
- email verification, password reset
- refresh tokens, session management
- OAuth, magic links, OTP

Update the frontend auth flow to call the backend after the auth APIs exist.

Password hashing and token handling must be server-side. Document the token strategy (for example HTTP-only cookies or bearer tokens) and why it matches the current app.

## Storage and realtime

Implement the storage and realtime replacements designed in discovery. If discovery said a capability is unused, do not add it. Say so in the migration report.

## Validation and errors

Validate API input on the backend. Frontend validation stays, but backend validation is authoritative. Use a TypeScript validation library already justified by the input surface; do not add one for its own sake.

Consistent error responses. Appropriate HTTP status codes. Do not leak database errors, stack traces, or secrets to clients. Log useful detail on the server.

## Security pass

While implementing, check the existing app for:

- authentication vulnerabilities and authorization gaps
- exposed Supabase keys and secrets committed to the repository
- insecure CORS and insecure file access
- missing backend validation
- SQL injection risks
- insecure password handling and insecure token handling

Do not expose database credentials to the frontend. Preserve authorization that RLS used to enforce. If a gap cannot be closed in this migration, record it under known limitations rather than dropping the check.
