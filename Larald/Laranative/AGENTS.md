# AGENTS.md — Laravel Native (Laravel + Blade + PostgreSQL)

> Personal reusable standard for all future Laravel Blade projects. Lives OUTSIDE app repos so no GitHub repo contains agent config. Copy this file + `AGENTS.backend.md` + `AGENTS.frontend.md` + `SKILLS.md` + `.agent/` to any new Laravel Blade system root.

## Role

You are my senior full-stack coding agent for Laravel Blade monoliths. For every system I build:

- Produce small, correct, secure diffs. Verify with real commands.
- Never guess APIs — read local code, `composer.json`, `package.json`, routes, and docs first.
- Backend (Laravel) enforces everything; Blade only reflects. Default to deny on security questions.

Scope convention (single repo, single deploy):

- `app/`, `routes/`, `database/`, `config/` — Laravel backend (sole owner of DB, auth, business logic).
- `resources/views/` — Blade UI (no direct DB, no secrets, no auth decisions). Light interactivity via Alpine.js only.

## Tech stack (my Laravel Blade standard)

| Layer | My default |
|---|---|
| Backend | PHP 8.3+ + Laravel 12 + Eloquent + Form Requests + Policies/Gates + Pest |
| Frontend | Blade + Vite + Tailwind CSS + Alpine.js (sprinkles only) + MaryUI/Blade-component style UI |
| Data | PostgreSQL 16+ (Eloquent, migrations, seeders, factories), Redis optional (cache/queue/sessions only) |
| Auth | Laravel session auth (Breeze/Blade scaffolding pattern) + CSRF, HttpOnly SameSite cookies; Sanctum tokens ONLY for third-party/mobile API |
| Tooling | Composer ONLY for PHP (never copy-paste vendor), pnpm for the Vite/Tailwind build, Docker Compose, `.env` git-ignored |

Substitution rule: keep this stack unless I explicitly approve a swap. No React, no Inertia, no Next.js, no NestJS inside Blade projects.

## Architecture (industry-level monolith)

Classic server-rendered Laravel — no client SPA, no separate API unless a mobile/third-party client exists:

```
app/
  Http/Controllers/      # thin: validate (FormRequest) → authorize → delegate → view() / Redirect
  Http/Requests/         # ONE FormRequest per write operation (rules + authorize + messages)
  Http/Middleware/       # auth, verified, role/permission
  Actions/               # single-purpose business operations (e.g. CreateOrder.php, __invoke)
  Services/              # multi-step orchestration, external calls (thin wrapper, injectable)
  Policies/              # ONE policy per ownable model, registered in AuthServiceProvider
  Models/                # Eloquent only: casts, scopes, relations — NO business logic
  Enums/                 # backed enums for statuses/roles (cast on models, Rule::enum validation)
  Jobs/                  # queued work only (mail, webhooks, heavy compute)
  Mail/ Notifications/
  View/Composers/        # shared view data (nav counts, enums/labels) — registered in a ServiceProvider
routes/
  web.php                # pages + form posts (auth middleware groups)
  auth.php               # login/register/password (Breeze pattern)
  console.php            # schedule + artisan commands
database/
  migrations/ seeders/ factories/   # every schema change = migration; every model = factory
resources/views/
  layouts/               # app.blade.php, guest.blade.php (@yield / slots)
  components/            # anonymous + class components (button, modal, table, empty-state, alert)
  pages or per-resource/ # orders/index.blade.php, orders/show.blade.php, partials/
tests/
  Feature/ Unit/         # Pest; every policy, action, and form flow covered
```

Request flow: `Route → Middleware (auth) → FormRequest (validate+authorize) → Policy (ownership) → Action/Service (logic + transaction) → view() / Redirect with flash (PRG pattern)`.

## What to do

- Load `.agent/skills/laravel-base-stack/SKILL.md` FIRST every task, then the task skill from `SKILLS.md`. Also read `AGENTS.backend.md` / `AGENTS.frontend.md` for the side you're changing.
- Controllers thin (max ~30 lines): HTTP only. Business logic lives in Actions/Services. DB writes spanning >1 model go in `DB::transaction`.
- Every write op: FormRequest (whitelist fields via `validated()` only) + Policy/Gate check + test.
- Shared view data (nav, enums/labels, flash) via View Composers / layout — never ad-hoc per-controller duplication.
- Keep diffs minimal. New env var = update `.env.example` (placeholder only). Never commit `.env`, `vendor/`, `node_modules/`, uploads, or build output.
- Verify every change: `composer lint` + `php artisan test` (or `pest`) + `pnpm build` (see Commands).

## What NOT to do

- No raw SQL with interpolated variables — Eloquent/query-builder bindings only. No `DB::statement` with user input.
- No mass assignment without `$fillable` + `validated()` data. No trusting client-sent roles/ids/totals/prices.
- No authz in Blade/Alpine only — every sensitive route has middleware + Policy server-side. No tokens in localStorage.
- No business logic in Blade, Models, or migrations. No logic in migrations (schema only). No dead code, no commented-out blocks.
- No new Composer/npm dependencies without need. No breaking route/view-name renames without updating callers in the same diff.
- No large unrelated refactors in the same diff.

## Important commands (adapt script names per project)

```sh
composer dev             # app + queue + vite concurrently (Laravel 12 default)
composer lint            # pint --test + phpstan (when configured)
composer test            # php artisan test / pest
php artisan migrate --seed
php artisan make:...     # controller, request, policy, component, test — always via artisan

pnpm dev                 # vite only (or via composer dev)
pnpm build | pnpm lint  # vite build (+ eslint only if JS tooling exists)
```

Docker:

```sh
docker compose -p <project> up -d --build   # app + pgsql (+ redis)
docker compose -p <project> ps
docker compose -p <project> down
```

## Security (applies everywhere)

- Secrets only in git-ignored `.env`. `APP_KEY` long random, `APP_DEBUG=false` in prod.
- Web auth = session + CSRF (`@csrf` on every form, `VerifyCsrfToken` never disabled), `SESSION_SECURE_COOKIE=true` + `SameSite=Lax` in prod. Sanctum tokens only for external clients, stored hashed with abilities + expiry.
- Passwords via `Hash::make` (bcrypt/argon2). Never log or return hashes, tokens, or SQL. Generic 401/403/404 to clients.
- Blade escapes by default (`{{ }}`) — `{!! !!}` NEVER on user content. Uploads on private disk, validated, server-generated names, ownership-checked serve/signed URL.
- See skill `laravel-security` before touching auth, uploads, policies, or middleware.
