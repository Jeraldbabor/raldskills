---
name: inertia-react
description: Inertia v2 + React pages, layouts, useForm, shared props, flash and validation errors. Use for any resources/js UI work.
---

# Inertia React (v2)

## Role

UI agent. Fast, accessible pages over server-driven props — no parallel API layer.

## What to do

- Structure: `resources/js/Pages/<Resource>/<Action>.tsx` + `Partials/`, `Layouts/` (`AppLayout`, `GuestLayout` with `PersistentLayout` where apt), `Components/` (shared UI only), `hooks/`, `lib/`, `types/index.d.ts` (`PageProps` incl. `auth`, `flash`, `errors`).
- Navigation/mutations via Inertia: `<Link>`, `router.get/post/put/patch/delete`, `useForm` for forms (`post(submit)`, `processing`, `errors`, `reset`). Prefer `useForm` so 419/CSRF refresh + error bags work automatically.
- Server data first: lists/filters/pagination are server-computed query params (`router.get` with `preserveState`); render `props`. Partial reloads (`only`) for expensive widgets.
- Flash toasts: read `usePage<PageProps>().props.flash` (`success`/`error`), show once, clear on navigate. Field errors from `props.errors` next to inputs.
- Types: type every page prop (`interface OrdersIndexProps { orders: Paginated<Order>; filters: {...} }`). Update types + all consumers when a controller prop changes.
- a11y + responsive: labels, focus management on modal/form errors, keyboard support, loading/empty/error states on every data view.

## What NOT to do

- No `fetch`/axios first-party endpoints bypassing Inertia (breaks CSRF/error/flash conventions). No client-computed prices/totals/permissions.
- No `dangerouslySetInnerHTML` on user content. No secrets/`VITE_` private values in JS. No auth tokens in storage.
- No business logic in JSX — derive display only; contested values come from the server. No hardcoded route URLs — `route()` (Ziggy) or the project's single URL helper.

## Important commands

```sh
pnpm dev
pnpm build        # tsc + vite build
pnpm lint         # eslint resources/js
```

## Security

- Hidden/premium UI is cosmetic — expect 401/403 and render login/upgrade states. Map server errors to safe messages; never render raw stacks. File links point at ownership-checked controller/signed URLs only.
