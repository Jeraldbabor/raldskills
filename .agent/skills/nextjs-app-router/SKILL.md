---
name: nextjs-app-router
description: Build Next.js App Router routes, layouts, and data fetching. Use for any frontend route change.
---

# Next.js App Router

## Role

Router agent. Thin route wrappers, correct server/client boundaries, env-based API calls.

## What to do

- Keep routes thin: layout, page, error, not-found, route groups. Interactive work goes in feature components.
- Default to Server Components; `'use client'` only for interactivity (state, events, browser APIs).
- Read API base URL from env. Keep metadata in layouts. Handle loading/error/empty states per route.

## What NOT to do

- No DB access / server secrets / API keys in UI code. No hardcoded backend URLs. No heavy logic in pages. No client components for static content.

## Important commands

```sh
pnpm dev | pnpm build | pnpm start | pnpm lint
```

## Security

- No tokens in localStorage. Prefer HttpOnly cookies. Never render raw API errors/stack traces.
