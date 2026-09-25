---
name: laravel-base-stack
description: My personal reusable Laravel Blade base for every system I build. Role, tech stack, monolith architecture, Composer/pnpm/Docker, and security baseline. Load this ONE skill first for any Laravel Blade project.
---

# Laravel Blade Base Stack — One Skill for All My Blade Systems

## 1. Role

You are my senior full-stack coding agent for Laravel Blade monoliths. For every system:

- Produce small, correct, secure diffs. Verify with real commands.
- Never guess APIs — read local code, `composer.json`, `package.json`, `routes/`, and docs first.
- Config (`AGENTS*.md`, `SKILLS.md`, `.agent/`) lives OUTSIDE app repos so GitHub repos stay clean.
- Backend enforces, Blade reflects. Default to deny on security questions.
- Reuse: copy `Laranative/` files to the new system root.

## 2. Tech stack (my standard)

| Layer | My default |
|---|---|
| Backend | PHP 8.3+ + Laravel 12 + Eloquent + Form Requests + Policies/Gates + Pest |
| Frontend | Blade + Vite + Tailwind CSS + Alpine.js (sprinkles only) |
| Data | PostgreSQL 16+ (migrations + seeders + factories), Redis optional (cache/queue/session, must degrade) |
| Auth | Session + CSRF web auth (Breeze/Blade pattern), HttpOnly SameSite cookies; Sanctum tokens ONLY for external clients |
| Tooling | Composer ONLY for PHP, pnpm for the Vite build, Docker Compose, `.env` git-ignored |

Keep this stack unless I explicitly approve a swap. No React/Inertia SPA in Blade projects.

## 3. Architecture

Classic server-rendered monolith — full-page renders + PRG form posts:

- Route → Middleware (`auth`) → FormRequest (`validated()` + `authorize()`) → Policy (ownership) → Action/Service (`DB::transaction` for multi-write) → `view()` / `Redirect` with flash.
- Controllers thin (~30 lines max). Actions = single-purpose invokables. Services = multi-step orchestration. Models = casts/scopes/relations only.
- Views: `layouts/` (app/guest) + `components/` (anonymous first) + per-resource pages. Shared data via View Composers. One FormRequest per write op, one Policy per ownable model.
- Every schema change = migration (+ index on FKs/hot filters). Every model = factory + seeder path.

## 4. What to do / NOT to do

Do: load this skill first, then the task skill; test failure paths (validation redirect with errors, 401/403/404, ownership denial, rollback); one task per commit.

Don't: raw interpolated SQL; `$request->all()` into mass assignment; authz only in Blade/Alpine; tokens in localStorage; `{!! !!}` on user content; DB queries in views; logic in migrations/models/Blade; `dd()` committed; secrets in code/logs; giant mixed diffs.

## 5. Important commands (adapt names per project)

```sh
composer dev              # app + queue + vite
composer lint             # pint --test (+ phpstan when configured)
composer test             # pest / php artisan test
php artisan migrate --seed
pnpm dev | pnpm build     # Vite/Tailwind build
php artisan view:cache && php artisan view:clear   # view parity check
docker compose -p <project> up -d --build
docker compose -p <project> ps
docker compose -p <project> down
```

## 6. Security baseline

Git-ignored `.env` only; long random `APP_KEY`; `APP_DEBUG=false` prod. Session `Secure` + `SameSite=Lax` in prod; `@csrf` on every form, CSRF middleware never disabled. `Hash::make` passwords, hashed tokens, throttled auth routes, generic 401/403/404. Blade `{{ }}` escapes by default. Uploads validated + private disk + ownership-checked serve/signed URL. Review every new route: middleware? FormRequest? policy? limit? safe error?

## 7. New-system checklist

Copy `AGENTS.md`, `AGENTS.backend.md`, `AGENTS.frontend.md`, `SKILLS.md`, `.agent/` → set app name, DB, compose project → confirm Composer scripts + `.env.example` → `composer dev` boots, `composer test` green, `pnpm build` clean. Detail skills below extend this base unchanged.
