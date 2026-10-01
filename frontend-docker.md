# Frontend, environment, and Docker

## Frontend migration

After the backend APIs for a feature exist, migrate that feature. Remove direct Supabase backend dependencies as their replacements land.

Replace:

- `supabase.from(...)` with calls to the Express API
- `supabase.rpc(...)` with the matching backend API
- `supabase.auth...` with backend auth APIs
- storage operations with backend file APIs, when storage is used
- realtime subscriptions with the backend realtime mechanism, when realtime is used

Add a frontend API layer instead of scattering `fetch` through components. Follow existing project conventions. A typical shape:

```
src/
├── api/
│   ├── client.ts
│   ├── auth.ts
│   ├── users.ts
│   └── ...
├── services/
└── ...
```

Keep working UI components. Change the data access beneath them.

## Environment variables

Find every Supabase-related environment variable and replace it with frontend or backend configuration. Do not hardcode secrets. Add `.env.example` files. Do not commit real `.env` secrets.

Frontend example:

```
VITE_API_URL=http://localhost:3000
```

Match the project's existing env prefix (`VITE_`, `NEXT_PUBLIC_`, or other). Do not assume Vite if the app uses something else.

Backend example:

```
DATABASE_URL=...
JWT_SECRET=...
PORT=...
CORS_ORIGIN=...
SMTP_HOST=...
SMTP_PORT=...
SMTP_USER=...
SMTP_PASSWORD=...
```

Include only variables the implementation actually reads.

## CORS

Configure CORS for local development and production. When requests use credentials, set a specific origin. Do not use `Access-Control-Allow-Origin: *` for credentialed authentication.

Frontend and backend URLs come from environment variables.

## Email and MailHog

If the app sends email, route development mail through MailHog. Business logic must not depend on MailHog.

```
EmailService
├── Development SMTP → MailHog
└── Production SMTP or provider → configured provider
```

If the app does not send email, still run MailHog in the development compose file, and do not invent email workflows.

## Docker development

`docker-compose.dev.yml` only. Include at minimum:

- backend
- PostgreSQL
- MailHog

```
frontend
    │
    ▼
Node.js / Express
    ├── PostgreSQL
    └── MailHog
```

The dev compose file must support:

- hot reload where practical
- a persistent PostgreSQL volume
- backend environment variables
- database connectivity
- MailHog web UI
- application networking

## Docker production

`docker-compose.prod.yml` only. Do not reuse the development compose file for production. Do not combine both environments into one compose file.

Production services, at minimum:

- backend
- PostgreSQL

No MailHog. Production-oriented configuration. Do not publish PostgreSQL unless there is a documented reason. Persistent volumes. Environment variables or secrets, not committed credentials.

## Naming

Use `docker-compose.dev.yml` and `docker-compose.prod.yml`.
