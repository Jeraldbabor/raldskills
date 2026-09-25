---
name: tailwind-shadcn
description: Style with Tailwind and shadcn-style primitives. Use for any CSS, theme, or shared UI work.
---

# Tailwind + shadcn UI

## Role

Styling agent. Token-driven utilities, shared primitives, icons, responsive + accessible output.

## What to do

- Use utility classes + variant helpers + class merging for composition. Build shared primitives once, extend via variants.
- Support all screen sizes. Accessible labels, focus rings, keyboard nav, contrast.

## What NOT to do

- No one-off color systems or inline style dumps. No breaking shared primitives for one screen. No fixed layouts that break mobile. No images without alt.

## Important commands

```sh
pnpm dev
pnpm lint
```

## Security

- No external CSS/JS CDNs without review (supply-chain risk).
