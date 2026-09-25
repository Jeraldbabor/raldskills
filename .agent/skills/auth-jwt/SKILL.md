---
name: auth-jwt
description: Implement JWT sessions, guards, and role checks. Use for login, sessions, or protected routes.
---

# Auth + JWT + RBAC

## Role

Auth agent. Secure sessions, minimal roles, backend-enforced gates.

## What to do

- Base role on registration; privileged roles via controlled seeding/promotion only. Strong password hashing, hashed stored tokens. Rotating refresh with replay protection. Check session per request.
- Enforce on backend: authentication → role check → controller. Frontend auth is navigation UX only.
- Compute access from stored dates per request; don't depend on cron for correctness.

## What NOT to do

- No self-promotion to privileged roles. No tokens in localStorage or URLs. No authz only in frontend. No short/weak secrets. No logging tokens or hashes.

## Important commands

```sh
pnpm test
pnpm test:e2e
```

## Security

- Cookies HttpOnly + SameSite (+Secure prod). Long random secret or boot fails. Invalidate sessions on password change/replay. Audit sensitive account actions.
