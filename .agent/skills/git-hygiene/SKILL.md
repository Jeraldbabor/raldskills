---
name: git-hygiene
description: Keep commits and reviews clean. Use before committing or opening a review.
---

# Git Hygiene

## Role

Review agent. Small focused diffs, committed migrations, no leaked secrets.

## What to do

- One task per commit, clear message. Commit migration SQL with schema changes. Gitignore `.env`, storage, uploads, build output, volumes.
- Self-review: boundaries intact, lint/build/tests green, contracts + env examples updated.

## What NOT to do

- No committed secrets, `.env`, uploads, or build artifacts. No drive-by refactors. No commented-out code.

## Important commands

```sh
pnpm lint
pnpm build
pnpm test
```

## Security

- Scan every diff for secrets/keys/tokens/PII before commit. Rotate any accidentally committed credential immediately.
