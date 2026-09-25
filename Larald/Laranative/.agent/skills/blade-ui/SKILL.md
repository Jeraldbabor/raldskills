---
name: blade-ui
description: Blade layouts, components, forms with old()/errors, flash, pagination, and Alpine.js sprinkles. Use for any resources/views UI work.
---

# Blade UI

## Role

UI agent. Fast, accessible server-rendered pages — full renders, PRG posts, zero SPA machinery.

## What to do

- Layouts (`resources/views/layouts/`): `app.blade.php` + `guest.blade.php`. One `<head>` (Vite `@vite`, meta, `@yield('title')`), nav, flash partial, footer. Pick ONE composition style per project (`@extends`/`@section` OR `<x-layout>` + slots) and stay consistent.
- Components (`resources/views/components/`): anonymous Blade first (`button.blade.php` with `@props(['variant' => 'primary'])`); class component (`make:component`) only when computed data/formatting is needed. Cover: button, input, textarea, select, checkbox, modal, table, empty-state, alert, badge, pagination wrapper.
- Forms: `method="POST"` + `@csrf` always; `@method('PUT'|'PATCH'|'DELETE')` for spoofing; `action="{{ route('orders.store') }}"`; `value="{{ old('name', $order->name ?? '') }}"`; `@error('name') <p>{{ $message }}</p> @enderror` beside each field; `enctype="multipart/form-data"` for uploads.
- Flash: controller `->with('success', ...)` / `->with('error', ...)`; layout renders `session('success'/'error'/'status')` once via an alert partial (Alpine dismiss OK).
- Lists: controller paginates, view renders table + `$items->links()` + `<x-empty-state>` when empty. Filters/sorts are GET forms preserving query strings (`request()->query()`), never POST.
- Alpine.js sprinkles: `x-data` dropdowns/modals/tabs/confirm-submit/auto-dismiss flash. Declarative only — no `fetch`-built screens, no client-computed totals/prices/permissions.
- Accessibility + responsive: `<label for>`, `aria-*` on modals/menus, focus states, keyboard-closable dialogs, mobile-first Tailwind on every view.

## What NOT to do

- No Eloquent/DB queries in views. No `@php` business logic (display-only formatting; prefer accessors, casts, enums labels).
- No `{!! !!}` on user/string-unknown content — `{{ }}` escapes by default. No secrets in views/Alpine data. No hardcoded URLs — `route()`/`action()` helpers.
- No hiding actions as "security" — `@can` is cosmetic; the route + Policy decides. No tokens in localStorage.
- No inline style dumps or one-off palettes — Tailwind theme tokens + shared components.

## Important commands

```sh
php artisan make:component Ui/Button
php artisan make:component Ui/Modal --view
pnpm dev | pnpm build
php artisan view:cache && php artisan view:clear
composer test -- --filter=OrderPage
```

## Security

- File/image URLs point at ownership-checked controller/signed URLs only — never raw storage paths. Confirm-destructive-actions pattern (Alpine confirm or dedicated confirm view) for deletes. Never render raw stacks/traces in error views.
