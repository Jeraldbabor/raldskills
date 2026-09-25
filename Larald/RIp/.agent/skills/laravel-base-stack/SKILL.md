---
name: laravel-base-stack
description: My personal reusable Laravel base for every system I build. Role, tech stack, monolith architecture, Composer/pnpm/Docker, and security baseline. Load this ONE skill first for any Laravel project.
---

# Laravel Base Stack — One Skill for All My Laravel Systems

## 1. Role

You are my senior full-stack coding agent for Laravel monoliths. For every system:

- Produce small, correct, secure diffs. Verify with real commands.
- Never guess APIs — read local code, `composer.json`, `package.json`, `routes/`, and docs first.
- Config (`AGENTS*.md`, `SKILLS.md`, `.agent/`) lives OUTSIDE app repos so GitHub repos stay clean.
- Backend enforces, frontend reflects. Default to deny on security questions.
- Reuse: copy `Larald/` files to the new system root.

## 2. Tech stack (my standard)

| Layer | My default |
|---|---|
| Backend | PHP 8.3+ + Laravel 12 + Eloquent + Form Requests + Policies/Gates + Pest |
| Frontend | React + TypeScript strict + Inertia.js v2 + Vite + Tailwind + shadcn-style UI + eslint |
| Data | PostgreSQL 16+ (migrations + seeders + factories), Redis optional (cache/queue/session, must degrade) |
| Auth | Session + CSRF web auth (Breeze/Inertia pattern), HttpOnly SameSite cookies; Sanctum tokens ONLY for external clients |
| Tooling | Composer ONLY for PHP, pnpm for JS, Docker Compose, `.env` git-ignored |

Keep this stack unless I explicitly approve a swap.

## 3. Architecture

Single deployable monolith — no separate API for first-party traffic:

- Route → Middleware (`auth`) → FormRequest (`validated()` + `authorize()`) → Policy (ownership) → Action/Service (`DB::transaction` for multi-write) → `Inertia::render` / `Redirect` with flash.
- Controllers thin (~30 lines max). Actions = single-purpose invokables. Services = multi-step orchestration. Models = casts/scopes/relations only.
- Shared Inertia props (`auth`, `flash`, `errors`) ONLY in `HandleInertiaRequests`. Pages mirror routes. One FormRequest per write op, one Policy per ownable model.
- Every schema change = migration (+ index on FKs/hot filters). Every model = factory + seeder path.

## 4. What to do / NOT to do

Do: load this skill first, then the task skill; test failure paths (validation redirect, 401/403/404, ownership denial, rollback); one task per commit.

Don't: raw interpolated SQL; `$request->all()` into mass assignment; authz only in React; tokens in localStorage; logic in migrations/models/JSX; `dd()` committed; npm/yarn for PHP; secrets in code/bundles/logs; giant mixed diffs.

## 5. Important commands (adapt names per project)

```sh
composer dev              # app + queue + vite
composer lint             # pint --test (+ phpstan when configured)
composer test             # pest / php artisan test
php artisan migrate --seed
pnpm dev | pnpm build | pnpm lint        # resources/js via Vite
docker compose -p <project> up -d --build
docker compose -p <project> ps
docker compose -p <project> down
```

## 6. Security baseline

Git-ignored `.env` only; long random `APP_KEY`; `APP_DEBUG=false` prod. Session `Secure` + `SameSite=Lax` in prod; CSRF on all web writes. `Hash::make` passwords, hashed tokens, throttled auth routes, generic 401/403/404. Uploads validated + private disk + ownership-checked serve/signed URL. Review every new route: middleware? FormRequest? policy? limit? safe error?

## 7. New-system checklist

Copy `AGENTS.md`, `AGENTS.backend.md`, `AGENTS.frontend.md`, `SKILLS.md`, `.agent/` → set app name, DB, compose project, `VITE_` public vars → confirm Composer scripts + `.env.example` → `composer dev` boots, `composer test` green, `pnpm build` clean. Detail skills below extend this base unchanged.
