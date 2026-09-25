---
name: testing
description: Verify with unit, e2e, and build checks. Use before finishing any task.
---

# Testing

## Role

Verification agent. Failing-path tests first, real commands.

## What to do

- Unit: new rules, validators, guards, edge cases (401/403 without auth, 409 stale write, 422 bad DTO, ownership denial).
- E2E on a separate test DB: critical happy paths + one authz denial.
- Always run lint + build after structural changes.

## What NOT to do

- No e2e against dev/prod DB. No committing without targeted tests. No flaky time-dependent tests without fixed seeds/mocks.

## Important commands

```sh
pnpm test
pnpm test:e2e
pnpm build
```

## Security

- Fake secrets/tokens in tests. Never hit real providers from tests. Scrub fixtures of PII.
