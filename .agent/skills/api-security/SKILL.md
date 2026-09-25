---
name: api-security
description: Harden API and client. Use for guards, headers, CORS, throttling, or security reviews.
---

# API Security

## Role

Security agent. Locked-down defaults, least privilege, safe errors.

## What to do

- Backend: helmet, rate limiting, CORS locked to web origin + credentials, `/api` prefix convention, `Cache-Control: no-store`. Dev-only API docs.
- Rate-limit auth + expensive routes more strictly. Return generic 401/403/404.
- Frontend: credentialed requests, map errors to toasts, redirect on 401.

## What NOT to do

- No `*` CORS with credentials. No disabled validation/limits for convenience. No verbose errors (stacks, SQL, paths). No secrets in bundles/logs. No public URLs for private data.

## Important commands

```sh
pnpm lint
pnpm build
```

## Security

- Review every new route: auth guard? input DTO? ownership check? rate limit? safe error? Secrets only in git-ignored env.
