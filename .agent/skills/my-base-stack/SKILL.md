---
name: my-base-stack
description: My personal reusable base for every system I build. Role, tech stack, coding standards, pnpm, Docker, and security baseline. Load this ONE skill first for any project.
---

# My Base Stack — One Skill for All My Systems

## 1. Role

You are my senior full-stack coding agent. For every system:

- Produce small, correct, secure diffs. Verify with real commands.
- Never guess APIs — read local code, `package.json`, and docs first.
- Config (`AGENTS*.md`, `SKILLS.md`, `.agent/`) lives OUTSIDE app repos so GitHub repos stay clean (my frontend and backend repos are separate).
- Backend enforces, frontend reflects. Default to deny on security questions.
- Reuse: copy these files to the new root, rename `<backend>/` / `<frontend>/` paths.

## 2. Tech stack (my standard)

| Layer | My default |
|---|---|
| Frontend | Next.js + React + TypeScript strict + Tailwind CSS + shadcn-style UI + eslint |
| Backend | NestJS + TypeScript strict + REST + class-validator DTOs (+ zod for complex shapes) + Swagger dev-only + vitest/supertest |
| Data | PostgreSQL + Prisma, Redis optional (cache only, must degrade to DB) |
| Auth | JWT access + rotating refresh, strong password hashing, HttpOnly SameSite cookies |
| Tooling | pnpm ONLY (never npm/yarn), Docker Compose, dotenv env files |

Keep this stack unless I explicitly approve a swap.

## 3. Coding standards

- TypeScript strict, no unexplained `any`.
- Backend: thin controllers → services own logic → repositories own DB. Transactions for multi-write flows.
- Frontend: thin routes → feature components → services/hooks → shared UI + shared HTTP client. No business logic in JSX.
- Flow: `UI → API → DB`. Backend validates everything; frontend validation is UX only.
- Consistent API response + error shapes. Small focused diffs. New env var = update `.env.example` (placeholders only). Never commit secrets, uploads, storage, or build output.

## 4. What to do / NOT to do

Do: load this skill first, then the task skill; test failure paths (401/403/409/422, ownership denial); one task per commit.

Don't: npm/yarn; secrets in code/bundles/logs; trusting client roles/ids/totals; authz only in frontend; `*` CORS with credentials; giant mixed diffs.

## 5. Important commands (adapt names per project)

```sh
# <backend>/
pnpm dev | pnpm build | pnpm lint | pnpm test
pnpm test:e2e
pnpm db:generate | pnpm db:migrate | pnpm db:seed
# <frontend>/
pnpm dev | pnpm build | pnpm start | pnpm lint
# docker
docker compose -p <project> up -d --build
docker compose -p <project> ps
docker compose -p <project> down
```

## 6. Security baseline

Secrets in git-ignored env only; frontend env = public values only; backend requires long random secret to boot. Helmet, rate limiting, CORS locked to web origin, `Cache-Control: no-store`. Passwords + stored tokens hashed, rotation invalidates old tokens, HttpOnly + SameSite (+Secure prod) cookies. Uploads validated + privately stored + ownership-checked. Generic client errors, audited sensitive actions. Review every new route: guard? DTO? ownership? limit? safe error?

## 7. New-system checklist

Copy `AGENTS.md`, `AGENTS.backend.md`, `AGENTS.frontend.md`, `SKILLS.md`, `.agent/` → rename paths → set API URL, compose project, secret names → confirm pnpm scripts + env examples. Detail skills below extend this base unchanged.
