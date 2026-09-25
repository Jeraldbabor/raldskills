# AGENTS.md — Laravel Monolith (Laravel + Inertia React + PostgreSQL)

> Personal reusable standard for all future Laravel projects. Lives OUTSIDE app repos so no GitHub repo contains agent config. Copy this file + `AGENTS.backend.md` + `AGENTS.frontend.md` + `SKILLS.md` + `.agent/` to any new Laravel system root.

## Role

You are my senior full-stack coding agent for Laravel monoliths. For every system I build:

- Produce small, correct, secure diffs. Verify with real commands.
- Never guess APIs — read local code, `composer.json`, `package.json`, routes, and docs first.
- Backend (Laravel) enforces everything; Inertia React only reflects. Default to deny on security questions.

Scope convention (single repo, single deploy):

- `app/`, `routes/`, `database/`, `config/` — Laravel backend (sole owner of DB, auth, business logic).
- `resources/js/` — Inertia React UI (no direct DB, no secrets, no auth decisions).

## Tech stack (my Laravel standard)

| Layer | My default |
|---|---|
| Backend | PHP 8.3+ + Laravel 12 + Eloquent + Form Requests + Policies/Gates + Pest |
| Frontend | React + TypeScript strict + Inertia.js v2 + Vite + Tailwind CSS + shadcn-style UI + eslint |
| Data | PostgreSQL 16+ (Eloquent, migrations, seeders, factories), Redis optional (cache/queue/sessions only) |
| Auth | Laravel session auth (Breeze/Inertia scaffolding pattern) + CSRF, HttpOnly SameSite cookies; Sanctum tokens ONLY for third-party/mobile API |
| Tooling | Composer ONLY for PHP (never copy-paste vendor), pnpm for JS, Docker Compose, `.env` git-ignored |

Substitution rule: keep this stack unless I explicitly approve a swap. No Next.js, no NestJS, no Prisma inside Laravel projects.

## Architecture (industry-level monolith)

Single Laravel app serving Inertia pages — no separate API unless a mobile/third-party client exists:

```
app/
  Http/Controllers/      # thin: validate (FormRequest) → authorize → delegate → Inertia::render / Redirect
  Http/Requests/         # ONE FormRequest per write operation (rules + authorize + messages)
  Http/Middleware/       # auth, verified, role/permission,HandleInertiaRequests (shared props)
  Actions/               # single-purpose business operations (e.g. CreateOrder.php, __invoke)
  Services/              # multi-step orchestration, external calls (thin wrapper, injectable)
  Policies/              # ONE policy per ownable model, registered in AuthServiceProvider
  Models/                # Eloquent only: casts, scopes, relations — NO business logic
  Enums/                 # backed enums for statuses/roles (cast on models, validate with Rule::enum)
  Jobs/ Measurement: queued work only (mail, webhooks, heavy compute)
  Mail/ Notifications/
routes/
  web.php                # Inertia pages + form posts (auth middleware groups)
  auth.php               # login/register/password (Breeze pattern)
  console.php            # schedule + artisan commands
database/
  migrations/ seeders/ factories/   # every schema change = migration; every model = factory
resources/js/
  Pages/                 # ONE folder per route, colocated partials (Orders/Index.tsx, Orders/Partials/...)
  Layouts/               # AppLayout, GuestLayout
  Components/            # shared UI only (Button, Modal, DataTable, EmptyState)
  hooks/ lib/            # useForm helpers, formatting, shared types (PageProps)
tests/
  Feature/ Unit/         # Pest; every policy, action, and form flow covered
```

Request flow: `Route → Middleware (auth) → FormRequest (validate+authorize) → Policy (ownership) → Action/Service (logic + transaction) → Inertia render / Redirect with flash`.

## What to do

- Load `.agent/skills/laravel-base-stack/SKILL.md` FIRST every task, then the task skill from `SKILLS.md`. Also read `AGENTS.backend.md` / `AGENTS.frontend.md` for the side you're changing.
- Controllers thin (max ~30 lines): HTTP only. Business logic lives in Actions/Services. DB writes spanning >1 model go in `DB::transaction`.
- Every write op: FormRequest (whitelist fields via `validated()` only) + Policy/Gate check + test.
- Shared Inertia props (`auth.user`, `flash`, `errors`) ONLY in `HandleInertiaRequests` middleware — never per-controller ad hoc.
- Keep diffs minimal. New env var = update `.env.example` (placeholder only). Never commit `.env`, `vendor/`, `node_modules/`, uploads, or build output.
- Verify every change: `composer lint` + `php artisan test` (or `pest`) + `pnpm --prefix resources... ` per project scripts (see Commands).

## What NOT to do

- No raw SQL with interpolated variables — Eloquent/query-builder bindings only. No `DB::statement` with user input.
- No mass assignment without `$fillable` + `validated()` data. No trusting client-sent roles/ids/totals/prices.
- No authz in React only — every sensitive route has middleware + Policy server-side. No tokens in localStorage.
- No business logic in Blade/JSX, Models, or migrations. No logic in migrations (schema only). No dead code, no commented-out blocks.
- No new Composer/npm dependencies without need. No breaking route/prop renames without updating callers in the same diff.
- No large unrelated refactors in the same diff.

## Important commands (adapt script names per project)

```sh
composer dev             # app + queue + vite concurrently (Laravel 12 default)
composer lint            # pint --test + phpstan (when configured)
composer test            # php artisan test / pest
php artisan migrate --seed
php artisan make:...     # controller, request, policy, action, test — always via artisan

pnpm dev                 # vite only (or via composer dev)
pnpm build | pnpm lint  # tsc + eslint on resources/js
```

Docker:

```sh
docker compose -p <project> up -d --build   # app + pgsql (+ redis)
docker compose -p <project> ps
docker compose -p <project> down
```

## Security (applies everywhere)

- Secrets only in git-ignored `.env`. `APP_KEY` long random, `APP_DEBUG=false` in prod. Frontend env holds public values only (`VITE_*`).
- Web auth = session + CSRF (`VerifyCsrfToken`), `SESSION_SECURE_COOKIE=true` + `SameSite=Lax` in prod. Sanctum tokens only for external clients, stored hashed with abilities + expiry.
- Passwords via `Hash::make` (bcrypt/argon2). Never log or return hashes, tokens, or SQL. Generic 401/403/404 to clients.
- Uploads: `Store` on private disk, validate size + extension + MIME, server-generated names, serve via ownership-checked controller or signed URL — never public direct links for private files.
- See skill `laravel-security` before touching auth, uploads, policies, or middleware.
