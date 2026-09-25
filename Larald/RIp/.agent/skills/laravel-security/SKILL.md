---
name: laravel-security
description: Harden Laravel apps and Inertia clients. Use for headers, CORS, throttling, or security reviews.
---

# Laravel Security

## Role

Security agent. Locked-down defaults, least privilege, safe errors.

## What to do

- Headers: `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY` (or frame-ancestors CSP), `Referrer-Policy: no-referrer`, HSTS in prod. Set via middleware/web-server. Keep Vite dev CSP out of prod.
- CORS (`config/cors.php`): locked to the web origin; `supports_credentials` only with explicit origins — never `*` with credentials. Only needed for cross-site/token APIs; same-site Inertia needs none.
- Throttling: `throttle:auth`-style strict limits on login/register/password, tighter on uploads/expensive actions; `RateLimiter` custom keys per user+IP where apt.
- Errors: `APP_DEBUG=false` prod; generic 401/403/404/419/429 pages; `report()` sensitive exceptions without leaking context; never return SQL, paths, or hashes.
- Env: `.env` git-ignored, `.env.example` placeholders only; required vars validated at boot (`APP_KEY`, DB creds); rotate leaked keys immediately.
- Dependencies: `composer audit` + `pnpm audit` before releases; pin major versions; remove unused packages.

## What NOT to do

- No disabled CSRF/validation/throttles for convenience. No verbose errors (stacks, SQL, paths). No secrets in bundles/logs/repos. No public URLs for private data. No `APP_DEBUG=true` in prod/staging-with-real-data.

## Important commands

```sh
composer audit
pnpm audit
composer lint
APP_DEBUG=false php artisan config:show app.debug
```

## Security

- Review every new route: auth middleware? FormRequest? policy/ownership? throttle? safe error? Log + alert on repeated 401/403/419/429 from one actor.
