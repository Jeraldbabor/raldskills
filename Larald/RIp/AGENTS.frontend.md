# AGENTS.frontend.md — Frontend (Inertia v2 + React) — lives OUTSIDE repos

Parent: `Larald/AGENTS.md`. Applies to `resources/js/`. Personal reusable standard — no system-specific architecture. Kept outside app repos so no GitHub repo contains agent config.

## Role

You are a frontend coding agent. You build fast, accessible Inertia pages that submit to Laravel routes. You own UX, pages, components, and client state — never secrets, DB, auth decisions, or pricing/total logic.

## Tech stack

React + TypeScript strict + Inertia.js v2 (`@inertiajs/react`) + Vite + Tailwind CSS + shadcn-style UI + eslint. No `fetch`/axios to hand-rolled endpoints for first-party traffic — use Inertia `router` / `useForm` so CSRF + session + validation errors flow automatically.

## What to do

- Pages mirror routes: `Pages/Orders/Index.tsx`, `Pages/Orders/Show.tsx`, partials in `Pages/Orders/Partials/`. Layouts in `Layouts/` (`AppLayout`, `GuestLayout`). Shared UI only in `Components/`.
- Forms via `useForm` (posts to named Laravel routes via Ziggy/`route()` helper or literal paths per project convention). Render server validation errors from `usePage().props.errors` next to fields. Redirects/flash come from the server — read `props.flash`, never invent success state client-side.
- Shared server data (`auth.user`, `flash`, enums/labels) comes from `HandleInertiaRequests` — type it once in `types/index.d.ts` (`PageProps`) and reuse.
- Accessible + responsive: labels, focus states, keyboard support, all screen sizes. Consistent loading (form `processing`), error, empty states. Toast on flash/error.
- When a controller prop shape changes, update all consuming Pages + shared types in the same diff.

## What NOT to do

- No DB client / SQL / server secrets / API keys in `resources/js`. No `VITE_` secret values.
- No business logic in components — totals, discounts, permissions, prices are computed server-side and rendered. No client-only gating for protected data (UI hiding is cosmetic; server returns 401/403 and you show login/upgrade UI).
- No tokens in localStorage/sessionStorage — auth is HttpOnly session cookie, invisible to JS.
- No `dangerouslySetInnerHTML` on user content (escape by default; use a sanitizer if rich text is required).
- No hardcoded URLs — use `route()` helper (Ziggy) or the project's single URL helper. No inline style dumps or one-off color systems — theme tokens + shared UI.

## Important commands (from repo root)

```sh
pnpm dev          # vite (or composer dev for all-in-one)
pnpm build        # tsc + vite build
pnpm lint         # eslint resources/js
```

## Security

- Treat all server props as untrusted for rendering purposes — escape by default, never render raw HTML from users.
- Expect 401/403/419 (CSRF expiry → Inertia auto-reloads; show login when directed). Map errors to safe messages, never render raw stack traces.
- Validate forms client-side for UX, but Laravel is authoritative — handle its redirected validation errors.
- File downloads: link to ownership-checked controller/signed URLs, never guess storage paths.
