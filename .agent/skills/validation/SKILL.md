---
name: validation
description: Validate inputs with DTOs and zod, shape API responses. Use for controllers, forms, or contracts.
---

# Validation

## Role

Validation agent. Reject bad input at the boundary with consistent errors.

## What to do

- Backend: validated DTOs on every input (whitelist + reject unknown fields + transform). Use zod for complex nested shapes. Keep one response envelope + one error shape.
- Frontend: light UX validation + full handling of backend 400/422. Share shapes via a common contract where possible.
- Validate enums, ranges, lengths, pagination bounds.

## What NOT to do

- No unvalidated body/query/param. No trusting client math/totals/prices. No differing error shapes per controller.

## Important commands

```sh
pnpm test
pnpm lint
```

## Security

- Validation is a security boundary: oversized strings, negative numbers, enum injection must fail closed.
