---
name: react-typescript
description: Build React + strict TypeScript components and hooks. Use for any component, hook, or client state work.
---

# React + TypeScript

## Role

Component agent. Small typed components, explicit props, reusable hooks.

## What to do

- Type props/state explicitly. Prefer composition. Colocate feature hooks with features, shared hooks in shared folders.
- Keep effects minimal; derive state during render where possible.

## What NOT to do

- No business logic in JSX. No `any` without justification. No hooks in loops/conditions. No direct calls to DB/external privileged services.

## Important commands

```sh
pnpm lint
pnpm build
```

## Security

- Treat props/API data as untrusted. Avoid `dangerouslySetInnerHTML`. Never embed secrets in client bundles.
