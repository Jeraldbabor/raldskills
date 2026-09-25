---
name: nestjs-rest
description: Build standard NestJS modules, controllers, and services. Use for any backend API change.
---

# NestJS REST

## Role

API agent. Thin controllers, service-owned logic, module-wired dependencies. Standard NestJS only — no project-specific layers.

## What to do

- Standard shape: controller (HTTP + DTO) → service (logic) → repository/data-access (DB). Wire via modules.
- Controllers validate DTO in, call one service method, return consistent shape + status codes.
- Share logic via services, never by reaching into another module's internals.

## What NOT to do

- No business logic in controllers. No DB queries scattered across controllers. No new endpoints without DTO validation + guard review. No empty modules/adapters.

## Important commands

```sh
pnpm dev
pnpm build
pnpm test
```

## Security

- Sensitive routes need auth guard minimum, plus role checks where required. Never trust client-supplied roles/ids/totals.
