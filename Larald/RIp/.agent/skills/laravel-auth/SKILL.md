---
name: laravel-auth
description: Session auth, CSRF, Policies/Gates, and Sanctum for external clients. Use for login/session, roles, or any authorization work.
---

# Laravel Auth (Session + Policies)

## Role

Identity agent. Locked-down defaults, least privilege, ownership everywhere.

## What to do

- First-party web = Laravel session auth (Breeze/Inertia pattern): `auth` / `guest` / `verified` middleware groups in `routes/web.php` + `routes/auth.php`. CSRF via default `VerifyCsrfToken` on all web writes.
- ONE Policy per ownable model (`php artisan make:policy FooPolicy --model=Foo`), registered via discovery or `AuthServiceProvider`. Resource controllers use `authorizeResource`. Non-resource actions call `$this->authorize()` / `Gate::authorize()` FIRST, before any query side effects.
- Roles: `spatie/laravel-permission`-style role/permission OR backed-enum role + Gates — per project choice, but a SINGLE source of truth, checked server-side. Never trust a client-sent role.
- Session cookies: `SESSION_SECURE_COOKIE=true`, `SESSION_SAME_SITE=lax` in prod; `Hash::make` (bcrypt/argon2) passwords; `Auth::logout` + `session()->invalidate()` + `regenerateToken()` on logout; rotate session on privilege change.
- Sanctum tokens ONLY when a mobile/third-party client exists: `HasApiTokens`, abilities per token, expiry, hashed storage, revocation endpoint. First-party Inertia traffic never uses bearer tokens.

## What NOT to do

- No disabling CSRF for convenience. No tokens in localStorage/sessionStorage. No role/permission checks only in React.
- No custom password hashing, no plaintext/ reversible storage. No session fixation (always regenerate on login).
- No `*` CORS with credentials. No Sanctum `EnsureFrontendRequestsAreStateful` confusion — stateful SPA = same-site session; cross-site/mobile = tokens.

## Important commands

```sh
php artisan make:policy OrderPolicy --model=Order
composer test -- --filter=Auth
php artisan permission:show   # when using spatie permission
```

## Security

- Generic 401/403/404 to clients; throttle login + password routes. Audit logins, password changes, role grants, and sensitive mutations. Review every new route: middleware? policy? ownership? throttle? safe error?
