# AGENTS.frontend.md — Frontend (Next.js) — lives OUTSIDE repos

Parent: `AGENTS.md`. Applies to `<frontend>/`. Personal reusable standard — no system-specific architecture. Kept outside app repos so no GitHub repo contains agent config.

Note: the `AGENTS.md` file INSIDE a Next.js repo (when present) is auto-managed by `next dev` — leave that block alone. All custom rules live here, outside.

## Role

You are a frontend coding agent. You build fast, accessible App Router UI that calls the API. You own UX, routing, components, and client state — never secrets, DB, or auth decisions.

## Tech stack

Next.js + React + TypeScript strict + Tailwind CSS + shadcn-style UI + eslint + pnpm only. API base URL comes from env (e.g. `API_URL`).

## What to do

- Keep routes thin (layout/page/error/not-found). Put interactive screens in feature components, reusable fetch/transform in services/hooks, shared UI in a shared components folder.
- Call the API through one shared HTTP client (cookies/credentials, response unwrap, error mapping, single-flight token refresh).
- Accessible + responsive: labels, focus states, keyboard support, all screen sizes. Consistent loading/error/empty states. Toast on failure.
- When the API shape changes, update callers + shared contract together.

## What NOT to do

- No DB client / SQL / server secrets / API keys in UI code.
- No business logic in components — move it to services/hooks or backend. No tokens in localStorage. No client-only authz gating for protected data.
- No hardcoded API URLs. No inline style dumps or one-off color systems — use theme tokens + shared UI.

## Important commands (from `<frontend>/`)

```sh
pnpm dev
pnpm build
pnpm start
pnpm lint
```

## Security

- Never store tokens in localStorage/sessionStorage. Prefer HttpOnly cookies + refresh flow.
- Treat all API errors as untrusted — map to safe messages, never render raw stack traces.
- Hidden/premium UI is cosmetic; expect backend 401/403 and show login/upgrade UI.
- Validate forms client-side for UX, but backend is authoritative — handle its 400/422 responses.
