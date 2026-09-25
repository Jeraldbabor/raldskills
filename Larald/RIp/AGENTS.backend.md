# AGENTS.backend.md — Backend (Laravel 12 + PostgreSQL) — lives OUTSIDE repos

Parent: `Larald/AGENTS.md`. Applies to `app/`, `routes/`, `database/`, `config/`. Personal reusable standard — no system-specific architecture. Kept outside app repos so no GitHub repo contains agent config.

## Role

You are a backend coding agent. You own routes, validation, authz, business logic, persistence, jobs, and security. Controllers stay thin and logic stays testable in Actions/Services.

## Tech stack

PHP 8.3+ + Laravel 12 + Eloquent + PostgreSQL 16+ + Form Requests + Policies/Gates + Pest + Composer only. Redis optional (cache/queue/session — must degrade to DB/sync when absent).

## What to do

- Organize per feature: Route → FormRequest → Policy → Action/Service → Model. One FormRequest per write op; use `$request->validated()` — never `$request->all()`.
- Controllers: `authorize()` (or `Gate::authorize` / `authorizeResource`), delegate to Action/Service, return `Inertia::render` (GET) or `Redirect::route(...)->with('success', ...)` (writes, PRG pattern).
- Models: `$fillable` + `$casts` (enums, datetimes), query scopes for hot filters, indexes in migrations for every foreign key + hot `where/orderBy`. Multi-write flows in `DB::transaction`.
- Enums: backed PHP enums for statuses/roles; validate with `Rule::enum(...)`, cast on model, share labels to React via `HandleInertiaRequests` or per-page prop — never hardcode the same list in two places.
- Jobs for mail/notifications/webhooks/heavy work; idempotent handlers; failed-job attention. Scheduler in `routes/console.php`.
- Write/extend Pest tests for new rules and failure paths (validation 422-style redirect, 401/403/404, ownership denial, transaction rollback).

## What NOT to do

- No business logic in controllers, models, migrations, or seeders. No fat constructors — inject via method/di container.
- No raw interpolated SQL. No `request()->all()` into `create()/update()`. No client-controlled filesystem paths.
- No `any`-style looseness in PHP: typed properties/returns, no suppressed errors (`@`), no `dd()/dump()` committed.
- No public API routes for first-party Inertia traffic — use `web.php` + session/CSRF. Sanctum token routes only when an external client exists, under `auth:sanctum` + `abilities`.
- No unused providers, empty actions, or placeholder adapters.

## Important commands (from repo root, adapt names per project)

```sh
composer dev
composer lint                 # pint --test (+ phpstan when configured)
composer test                 # pest / php artisan test
php artisan migrate --seed
php artisan make:controller FooController --resource
php artisan make:request StoreFooRequest
php artisan make:policy FooPolicy --model=Foo
php artisan make:test FooTest --pest
```

## Security

- Every sensitive route: `auth` (+ `verified` where needed) middleware + Policy/Gate ownership check. `authorizeResource` on resource controllers.
- CSRF on all web writes (default `VerifyCsrfToken` — never disable globally or per-route for convenience). `SESSION_SECURE_COOKIE=true`, `SameSite=Lax`, `APP_DEBUG=false` in prod.
- `Hash::make` passwords; tokens hashed at rest; rate-limit auth + expensive routes (`throttle:auth`, `throttle:api` equivalents on web where apt).
- Uploads: private disk + validated (size/ext/MIME) + generated names + ownership-checked serve/signed URL.
- Never expose stack traces, hashes, SQL, or other users' data. Audit sensitive actions (model observer or explicit audit log).
