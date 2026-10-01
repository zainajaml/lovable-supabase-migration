# Backend modules and API contracts

Contents: [Express](#expressjs-branch) · [NestJS](#nestjs-branch) · [API behavior](#api-behavior) · [OpenAPI](#openapi-is-implementation-work) · [Evidence](#required-evidence-per-feature)

## Express.js branch

Use a composition root to mount feature routers and inject dependencies. Keep application creation separate from listening so integration tests can use the app without opening a port. Adapt names to the project's conventions; the following is illustrative, not a requirement to create empty files.

```text
backend/
  src/
    app.ts
    server.ts
    config/env.ts
    database/client.ts
    database/migrations/
    database/seeds/
    shared/http/errors.ts
    shared/http/error-handler.ts
    shared/http/request-context.ts
    shared/security/authenticate.ts
    shared/observability/logger.ts
    modules/
      projects/
        projects.routes.ts
        projects.controller.ts
        projects.service.ts
        projects.repository.ts
        projects.schemas.ts
        projects.dto.ts
        projects.types.ts
        projects.policy.ts
        projects.openapi.ts
        projects.constants.ts
        projects.entity.ts
        utils/
        tests/
      auth/
      users/
    integrations/
  tests/integration/
  tests/contracts/
  openapi/
  docs/
  package.json
  tsconfig.json
  .env.example
  Dockerfile
```

Register middleware in an intentional order, including raw-body routes needed for webhook signature verification before generic JSON parsing. Configure payload limits, allowlisted CORS, secure headers, trusted proxy settings appropriate to hosting, authentication, validation, rate limits, route mounting, 404 handling and a final error handler. Verify async error propagation for the selected Express major version; do not copy incompatible patterns.

Keep schemas beside each feature and validate path/query/body independently, including pagination limits and allowlisted sorting/filter fields. Controllers translate validated requests and trusted context into service calls and map results to HTTP DTOs. Services own invariants, orchestration and transaction intent; repositories implement parameterized persistence with the transaction client passed consistently. Put resource/tenant policies in the module, with shared primitives only when genuinely common.

## NestJS branch

Use Nest modules, dependency injection, controller decorators, pipes, guards, filters and interceptors according to their responsibilities. Controllers already declare routes; do not add redundant Express-style route files.

```text
backend/
  src/
    main.ts
    app.module.ts
    config/
    database/
      database.module.ts
      migrations/
      seeds/
    common/
      guards/
      filters/
      interceptors/
      decorators/
      logging/
    modules/
      projects/
        projects.module.ts
        projects.controller.ts
        projects.service.ts
        projects.repository.ts
        projects.policy.ts
        dto/create-project.dto.ts
        dto/project-response.dto.ts
        entities/project.entity.ts
        types/
        constants/
        tests/
      auth/
      users/
    integrations/
  test/integration/
  test/contracts/
  openapi/
  docs/
  package.json
  tsconfig.json
  .env.example
  Dockerfile
```

Choose an ORM-compatible model layout: TypeORM entities can live in the feature; a tool requiring a central schema can keep that schema in database/ with ownership documented per module. Never create fake entities to satisfy this tree. Use DTO validation at the boundary and serialization that excludes sensitive fields. Configure transform/coercion and unknown-field behavior deliberately. Define guards for auth/roles and invoke resource policies for actual ownership checks. Avoid circular module dependencies and routine forwardRef use; redesign boundaries first.

## API behavior

Design named domain operations from observed workflows: for example `PATCH /api/v1/projects/:id`, `POST /api/v1/invitations/:id/accept`. Choose versioning based on consumer/rollout needs and use it consistently. A table's existence does not justify a public CRUD endpoint. Do not accept arbitrary table names, column lists, SQL fragments, RPC names or generic client-supplied query chains as the final public API.

Define pagination/cursors, sort/filter allowlists, ordering guarantees, bulk limits, concurrency/version conflicts and idempotency for operations that can duplicate external effects. Move multi-request UI mutations into atomic backend use cases where the workflow requires all-or-nothing behavior.

Choose a consistent JSON success/error contract and show it in the plan. One possible error shape is `{ error: { code, message, details }, requestId }`; expose only safe field errors in details. Use correct status codes, stable machine-readable codes, request correlation and structured redacted logs. Preserve deliberate 204/no-body, file/stream/SSE and provider protocol responses instead of forcing every response into an envelope. Do not expose password hashes, refresh tokens, internal columns, SQL errors or stack traces in DTOs.

Use centralized validated configuration; fail startup for missing/weak required deployed secrets. Keep .env reads and process.env access in config adapters rather than scattered services. Add graceful shutdown and liveness/readiness without exposing secrets.

## OpenAPI is implementation work

For every implemented endpoint document:
- operation ID, tag, purpose and path/method;
- path/query/header parameters and constraints;
- request bodies, required/nullable fields, formats, enums, limits and examples;
- cookie/bearer/OAuth security scheme as applicable, plus authorization meaning;
- response DTOs/content types, successful status codes and pagination;
- validation, authentication, authorization, conflict, rate-limit and server errors where applicable;
- multipart upload or stream behavior when used.

Select one source of truth: runtime schemas that generate OpenAPI, or DTOs/decorators with consistency checks. Never maintain a detached aspirational spec. Explain validation semantics that the generator cannot encode, such as refinements and cross-field rules, and test them.

Generate a machine-readable spec and Swagger UI for local development; define appropriate production access controls. Validate the spec in CI, check route/spec coverage and stable operation IDs, and contract-test actual request/response examples including errors. If CI regenerates committed specs or client DTOs, fail on an unexplained diff. Publish/version the contract so independent frontend builds use a known compatible version; client generation does not replace runtime validation or authorization.

## Required evidence per feature

Include unit tests for meaningful rules and integration tests for persistence, transactions, validation and authorization. Test controller output against the contract. Inspect imports to ensure controllers do not query the database and unrelated modules do not reach into each other's internals. Do not count empty module folders, empty test files or a Swagger decorator on one endpoint as architecture compliance.
