---
name: laravel-eloquent-postgres
description: Eloquent models, PostgreSQL migrations, factories, seeders, query scopes, and transactions. Use for any schema, model, or persistence work.
---

# Laravel Eloquent + PostgreSQL

## Role

Persistence agent. Correct schema, fast queries, safe writes, repeatable seeds.

## What to do

- Every schema change = new migration (`php artisan make:migration`). Schema ops only in migrations — no data logic except idempotent backfills with a down path.
- Models: `$fillable` (never `$guarded = []`), `$casts` (enums, `datetime`, arrays), relationships typed, query scopes for reusable filters (`scopeActive`, `scopeOwnedBy`).
- Indexes in the SAME migration: every FK + every hot `where`/`orderBy` column. Prefer `foreignId()->constrained()->cascadeOnDelete()` (or explicit `references` for nonstandard keys).
- Postgres specifics: `uuid`/`ulid` PKs where required, `jsonb` for schemaless attrs (with `->>` queries + GIN index when filtered), `decimal` for money (never float), check constraints for invariants.
- Multi-model writes in `DB::transaction` inside the Action/Service — never across controller lines. Use `lockForUpdate()` on contested rows (stock, balances, booking slots).
- Factories for every model (`make:factory`), realistic states; seeders for roles/reference data + demo dataset; `migrate:fresh --seed` must always succeed from zero.

## What NOT to do

- No raw SQL with interpolated variables — bindings only. No `DB::statement` with user input.
- No N+1: eager-load (`with`/`withCount`) anything rendered in a list. No `Model::all()` on unbounded tables — paginate (`cursorPaginate` for large sets).
- No business logic in models/migrations/seeders — query + persistence only. No float for money. No missing `down()`.

## Important commands

```sh
php artisan make:model Foo -mfs --policy
php artisan make:migration add_status_to_orders_table
php artisan migrate --seed
php artisan migrate:fresh --seed   # dev only, never prod
php artisan db:seed --class=DemoSeeder
```

## Security

- Ownership scoping at query level (`whereBelongsTo`, policy-filtered scopes) in addition to Policy checks. Never expose other tenants'/users' rows via unscoped queries or predictable IDs without authorization.
