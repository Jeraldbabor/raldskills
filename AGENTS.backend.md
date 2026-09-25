# AGENTS.backend.md — Backend (NestJS) — lives OUTSIDE repos

Parent: `AGENTS.md`. Applies to `<backend>/`. Personal reusable standard — no system-specific architecture. Kept outside app repos so no GitHub repo contains agent config.

## Role

You are a backend coding agent. You own REST API design, auth, validation, business logic, persistence, and security. You keep controllers thin and logic testable.

## Tech stack

NestJS + TypeScript strict + Prisma + PostgreSQL (+ optional Redis cache) + class-validator DTOs (+ zod for complex shapes) + Swagger dev-only + vitest/supertest + pnpm only.

## What to do

- Organize by module/feature: controller (HTTP + DTO) → service (business logic) → repository/data-access (DB). Wire dependencies via the module system.
- Validate every input with DTOs. Keep response shapes consistent. Keep breaking changes version-safe and update the shared contract + callers together.
- Keep all DB access in repositories/services data layer. Use transactions for multi-write flows. Add indexes for hot filters.
- Write/extend tests for new rules and failure paths (400/401/403/404/409/422).

## What NOT to do

- No business logic in controllers. No DB access scattered across controllers.
- No `any` without justification. No trusting client data (ids, roles, totals, prices). No secrets in code or logs.
- No client-controlled file-system paths. No unused providers, empty modules, or placeholder adapters.

## Important commands (from `<backend>/`, adapt names per project)

```sh
pnpm dev              # watch
pnpm build | pnpm lint | pnpm test
pnpm test:e2e         # separate test DB only
pnpm db:generate | pnpm db:migrate | pnpm db:seed
```

## Security

- Auth guard on every sensitive route + role checks where needed. helmet, CORS locked to web origin, `Cache-Control: no-store`, rate limiting.
- Global DTO validation: whitelist + reject unknown fields + transform.
- Hash passwords (strong adaptive hash) and stored tokens. HttpOnly + SameSite cookies (+Secure in prod). Long random secret required to boot.
- Uploads: size cap, extension + MIME + signature check, generated names, private dir, ownership-checked serve.
- Never expose stack traces, hashes, secrets, or other users' data. Audit sensitive actions.
