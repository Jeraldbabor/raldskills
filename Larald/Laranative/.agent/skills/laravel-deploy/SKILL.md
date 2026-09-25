---
name: laravel-deploy
description: Env config, Docker Compose, queues, scheduler, and production readiness. Use for deploy, env, or ops work.
---

# Laravel Deploy & Ops (Blade)

## Role

Ops agent. Reproducible builds, secret-free images, healthy prod.

## What to do

- Env: `.env.example` lists EVERY var with placeholders; `.env` git-ignored. Boot-validated (`APP_KEY`, `DB_*`, `QUEUE_CONNECTION`, `SESSION_SECURE_COOKIE`). `APP_DEBUG=false`, `APP_ENV=production` in prod.
- Docker Compose: `app` (php-fpm + nginx/caddy or `php artisan serve` for dev only) + `pgsql:16+` (named volume) + optional `redis` + `worker` (queue) + `scheduler` (`schedule:run` every minute or `schedule:work`). One `docker compose -p <project> up -d --build` boots everything.
- Build: `composer install --no-dev --optimize-autoloader` + `php artisan config:cache route:cache view:cache` + `pnpm build` (Vite manifest). Migrations run explicitly (`migrate --force`), never auto on boot without a lock/ops decision.
- Queues: `QUEUE_CONNECTION=redis/database` in prod (never `sync` for real mail/webhooks); supervised worker with retries + failed-job table/notifications; scheduler owns cron instead of host crontabs.
- Prod checklist: debug off, secure cookies, HSTS, backups (PG dumps tested by restore), log channel (stderr/json), healthcheck route, `composer audit` + `pnpm audit` clean.

## What NOT to do

- No secrets baked into images or committed files. No `migrate:fresh`/seeders against prod. No `sync` queue for user-facing side effects in prod. No `config:cache` with unvalidated local `.env` values.

## Important commands

```sh
docker compose -p <project> up -d --build
docker compose -p <project> exec app php artisan migrate --force
docker compose -p <project> exec app php artisan queue:failed
docker compose -p <project> down   # volumes kept; add -v only to wipe
```

## Security

- DB/Redis bound to internal network only; app secret + DB passwords long random, rotated on leak. TLS at the edge; secure + SameSite cookies; least-privilege DB user for the app.
