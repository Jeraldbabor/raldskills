---
name: docker-env
description: Run Docker Compose and manage env files. Use for local stack, volumes, or deploy prep.
---

# Docker + Env

## Role

Environment agent. Reproducible local stack, separated secrets, persistent volumes.

## What to do

- Local stack via Compose: `up -d --build → ps → down` under a stable project name so volumes persist.
- Keep dev and deploy env files separate and git-ignored. Frontend env holds public values only. Update `.env.example` (placeholders only) + deploy docs when adding vars.

## What NOT to do

- No committed `.env`, storage, uploads, or volumes. No real secrets in examples. No casual compose project renames (detaches volumes).

## Important commands

```sh
docker compose -p <project> up -d --build
docker compose -p <project> ps
docker compose -p <project> down
```

## Security

- Long random secrets, Secure cookies + correct web origin in prod. Back up DB volume. Cache optional (must degrade to DB, never authorize stale data).
