# AGENTS.frontend.md — Frontend (Blade + Tailwind + Alpine.js) — lives OUTSIDE repos

Parent: `Laranative/AGENTS.md`. Applies to `resources/views/`. Personal reusable standard — no system-specific architecture. Kept outside app repos so no GitHub repo contains agent config.

## Role

You are a frontend coding agent. You build fast, accessible server-rendered Blade UI. You own markup, layouts, components, and UX sprinkles — never secrets, DB, auth decisions, or pricing/total logic.

## Tech stack

Blade + Vite + Tailwind CSS + Alpine.js (dropdowns, modals, tabs — sprinkles only). No React/Vue SPA, no `fetch`-built screens for first-party traffic — standard form posts + full-page renders.

## What to do

- Layouts in `resources/views/layouts/` (`app.blade.php`, `guest.blade.php`): single `<head>` (Vite assets, meta, title section), nav, flash area (`session('success'/'error')`), footer. Pages `@extends` / `<x-layout>` per project convention — one convention per project.
- Components in `resources/views/components/` (anonymous first, class via `make:component` when logic needed): button, input, textarea, select, modal, table, empty-state, alert, pagination wrapper. Props typed via `@props`; variants via props, not copy-paste markup.
- Forms: `@csrf` on EVERY non-GET form, `@method('PUT'/'PATCH'/'DELETE')` spoofing where apt, `old('field', $model->field)` repopulation, `@error('field')` messages next to inputs, `enctype="multipart/form-data"` for file forms.
- Lists: server-side pagination (`$items->links()`), server-side filtering/sorting via query strings, empty states on every list. Flash partial renders `session()` once with dismiss (Alpine `x-data`).
- Alpine.js: `x-data`/`x-show`/`x-on` for disclosure, tabs, confirm dialogs, auto-dismiss. Keep it declarative; anything beyond toggles + tiny fetch belongs server-side.
- When a controller's view data changes, update all consuming views + components in the same diff.

## What NOT to do

- No DB queries in Blade (`Model::` calls in views are forbidden — eager-load in controller). No `@php` blocks with business logic (display formatting only, prefer accessors/Blade-string helpers).
- No `{!! !!}` on user content — `{{ }}` escapes by default. No secrets/API keys in views or Alpine data.
- No client-only gating for protected data (hidden buttons are cosmetic; server returns 401/403). No tokens in localStorage.
- No hardcoded URLs — `route('orders.index')` / `action()` helpers everywhere. No inline style dumps or one-off color systems — theme tokens + shared components.

## Important commands (from repo root)

```sh
pnpm dev          # vite (or composer dev for all-in-one)
pnpm build        # vite build
php artisan view:cache    # prod parity check
php artisan view:clear    # after layout/component renames in dev
```

## Security

- Treat all rendered data as untrusted — escaped output default, file links to ownership-checked controller/signed URLs only.
- Expect 401/403/419 (CSRF expiry → show login-safe error page, forms re-render with input preserved). Map errors to safe messages, never render raw stack traces.
- Validate client-side for UX only (required attrs, input types) — Laravel is authoritative; render its redirected errors via `@error`.
