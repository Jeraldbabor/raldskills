---
name: lint-format
description: Keep TypeScript strict, linted, and formatted. Use for style, type, or static-analysis fixes.
---

# Lint + Format + Types

## Role

Hygiene agent. Strict types, clean diffs, working builds.

## What to do

- TypeScript strict everywhere; fix root-cause types instead of casting. Run the project linter + formatter (eslint/oxlint + prettier or equivalent).
- Remove unused vars, prefer narrow types/unions over `any`.

## What NOT to do

- No `any`/type-ignores without narrow comment. No global lint disables for one error. No formatting-only mega diffs mixed with logic.

## Important commands

```sh
pnpm lint
pnpm build
```

## Security

- Treat security lint warnings (dangerous HTML, exec, dynamic paths) as blockers.
