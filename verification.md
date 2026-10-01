# Verification, testing, and documentation

## Repository scan

After implementation, search the frontend and backend for remaining Supabase usage:

- `@supabase`
- `createClient`
- `supabase.`
- `supabase.from`
- `supabase.rpc`
- `supabase.auth`
- `supabase.storage`
- `supabase.channel`
- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`

Review every hit. Classify it as one of:

- intentionally retained
- documentation
- migration artifact
- test
- actual runtime dependency

Do not delete unused imports only to make the search clean. The production frontend must not depend on Supabase. A runtime hit needs a replacement or an explicit, user-visible exception.

## Tests

Run what the repositories actually have:

- frontend tests
- backend tests
- TypeScript compilation
- linting
- database migration tests
- API tests where they exist

At minimum, verify:

- application starts
- backend starts
- PostgreSQL starts
- TypeORM connects
- migrations execute
- authentication works for the flows the app uses
- major API endpoints work
- frontend communicates with the backend
- database relationships work
- email delivery works through MailHog when the app sends email
- existing core workflows still work

Record commands run and results. If a check cannot be run, say what blocked it.

## Documentation

Write documentation covering:

1. Current architecture
2. Supabase dependencies
3. Current database schema
4. Current authentication architecture
5. Current API and data-access patterns
6. Target architecture
7. Database migration strategy
8. Authentication migration
9. API migration
10. Storage migration
11. Realtime migration
12. Frontend migration
13. Docker architecture
14. Environment variables
15. Development setup
16. Production setup
17. Remaining limitations

Put this with the backend (and a short pointer from the frontend if the repo already documents setup). Do not duplicate the same essay in both trees.

## Migration report

Produce this report when the migration is complete:

```markdown
# Migration report

## Summary
[What changed and the resulting architecture.]

## Files created
- ...

## Files modified
- ...

## Supabase functionality replaced
| Previous usage | Replacement | Status |
| --- | --- | --- |
| ... | ... | done / partial / not used |

## Database entities
- ...

## API endpoints
| Method | Path | Replaces |
| --- | --- | --- |
| ... | ... | ... |

## Authentication
[Flows implemented, token strategy, flows intentionally omitted.]

## Docker
- Dev: docker-compose.dev.yml (backend, PostgreSQL, MailHog)
- Prod: docker-compose.prod.yml (backend, PostgreSQL)

## Environment variables
- Frontend: ...
- Backend: ...

## Tests performed
| Check | Result |
| --- | --- |
| ... | pass / fail / not run |

## Remaining Supabase references
| Location | Classification | Action |
| --- | --- | --- |
| ... | documentation / runtime / ... | ... |

## Known limitations
- ...

## Manual verification
1. ...
```
