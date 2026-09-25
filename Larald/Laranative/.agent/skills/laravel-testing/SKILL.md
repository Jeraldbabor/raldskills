---
name: laravel-testing
description: Pest feature and unit tests, factories, and CI gates for Blade apps. Use for writing or fixing tests.
---

# Laravel Testing (Pest + Blade)

## Role

Verification agent. Prove the happy path and the denial paths, rendered HTML included.

## What to do

- Pest (`pestphp/pest` + `laravel` plugin): `tests/Feature` per user flow (auth, CRUD incl. rendered views, policy denial, validation redirect with errors + old input, transaction rollback), `tests/Unit` per Action/Service/Enum/cast.
- Always use `RefreshDatabase` (trait or `uses()` in `Pest.php`); build via factories with states — never depend on seeded prod-like data. Assert DB (`assertDatabaseHas/Missing`), HTTP status, view (`assertViewIs`, `assertSee`/`assertDontSee` incl. escaped output), redirects + session errors/flash (`assertSessionHasErrors`, `assertRedirect`), mail/queue fakes (`Mail::fake`, `Queue::fake`, `Storage::fake`).
- Cover failure paths for every feature: guest → redirect login; non-owner → 403; invalid input → redirect back with errors + NO db write; double-submit/contended write → single winner.
- Keep tests fast: sqlite `:memory:` or PG test DB per project convention (PG when using PG-specific SQL); parallel (`--parallel`) when configured. One behavior per test, named for the rule (`it denies updating another users order`).

## What NOT to do

- No tests hitting prod/dev databases (`phpunit.xml` env only). No `migrate:fresh` against a shared DB. No sleeping/waiting on real queues — fake them. No commented-out or skipped tests committed without a tracking note.

## Important commands

```sh
composer test                        # pest
composer test -- --filter=OrderTest
composer test -- --parallel
pnpm build                           # Vite gate alongside
```

## Security

- Tests must assert authorization (ownership denial, role gating, `@can`-hidden actions still 403 when forced) and that secrets/hashes never appear in rendered HTML. Redact dumps in failure output before pasting anywhere.
