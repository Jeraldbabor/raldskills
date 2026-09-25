---
name: laravel-testing
description: Pest feature and unit tests, factories, and CI gates. Use for writing or fixing tests.
---

# Laravel Testing (Pest)

## Role

Verification agent. Prove the happy path and the denial paths.

## What to do

- Pest (`pestphp/pest` + `laravel` plugin): `tests/Feature` per user flow (auth, CRUD, policy denial, validation redirect, transaction rollback), `tests/Unit` per Action/Service/Enum/cast.
- Always use `RefreshDatabase` (trait or `uses()` in `Pest.php`); build via factories with states — never depend on seeded prod-like data. Assert DB (`assertDatabaseHas/Missing`), HTTP status, Inertia props (`assertInertia`), redirects + flash, mail/queue fakes (`Mail::fake`, `Queue::fake`, `Storage::fake`).
- Cover failure paths for every feature: guest → redirect login; non-owner → 403; invalid input → redirect back with errors (no DB write); double-submit/contended write → single winner.
- Keep tests fast: sqlite `:memory:` or PG test DB per project convention (PG when using PG-specific SQL); parallel (`--parallel`) when configured. One behavior per test, named for the rule (`it denies updating another users order`).

## What NOT to do

- No tests hitting prod/dev databases (`phpunit.xml` env only). No `migrate:fresh` against a shared DB. No sleeping/waiting on real queues — fake them. No commented-out or skipped tests committed without a tracking note.

## Important commands

```sh
composer test                        # pest
composer test -- --filter=OrderTest
composer test -- --parallel
pnpm build && pnpm lint              # JS gate alongside
```

## Security

- Tests must assert authorization (ownership denial, role gating) and that secrets/hashes never appear in responses. Redact dumps in failure output before pasting anywhere.
