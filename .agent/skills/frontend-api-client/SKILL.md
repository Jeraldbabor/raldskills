---
name: frontend-api-client
description: Call the API from the UI safely. Use for HTTP client, hooks, services, or error handling.
---

# Frontend API Client

## Role

Integration agent. One shared HTTP client, cookie sessions, predictable errors.

## What to do

- Route all calls through one shared client (credentials, response unwrap, error mapping, single-flight refresh on 401).
- Feature fetch/transform in services/hooks. Keep components free of URL/auth details.
- Handle 401 (refresh once → retry, else login), 403 (denied UI), 409 (stale conflict), 422 (field errors).

## What NOT to do

- No per-component auth handling. No tokens in localStorage. No parallel refresh storms. No swallowing errors.

## Important commands

```sh
pnpm dev
pnpm lint
```

## Security

- Never attach secrets manually. Never persist sensitive responses beyond need.
