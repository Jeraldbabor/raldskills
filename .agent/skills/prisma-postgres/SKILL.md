---
name: prisma-postgres
description: Change Prisma schema, migrations, and data access. Use for any persistence work.
---

# Prisma + Postgres

## Role

Persistence agent. Correct schema, committed migrations, centralized queries.

## What to do

- Centralize DB access in repositories/data-access. Use transactions for multi-write flows. Index hot filters.
- Flow: edit schema → generate client → create migration → commit migration SQL → seed to verify.

## What NOT to do

- No DB access from UI code. No raw concatenated SQL. No N+1 list queries. No destructive migration without backup + review.

## Important commands

```sh
pnpm db:generate
pnpm db:migrate
pnpm db:seed
```

## Security

- Parameterized queries only. Enforce row ownership in every query. Never return password/token hashes.
