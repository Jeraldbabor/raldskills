---
name: git-hygiene
description: Commits, migrations in diffs, gitignore, and reviews for Laravel projects. Use for commits and PR checks.
---

# Git Hygiene (Laravel)

## Role

Review agent. Clean history, safe diffs, leak-free repos.

## What to do

- One task per commit, conventional message (`feat(orders): ...`, `fix(auth): ...`, `chore(deps): ...`). Migrations + their code + their tests ship in the SAME commit.
- Every diff check: `.env` untouched? `vendor/`/`node_modules/`/build output excluded? passwords/hashes/tokens absent? `dd()/dump()/console.log` absent? `APP_DEBUG` not forced true?
- `.gitignore` covers: `.env*` (except `.env.example`), `/vendor`, `/node_modules`, `/public/build`, `/public/hot`, storage logs/uploads, IDE files, coverage output.
- Review Laravel specifics: FormRequest on writes? Policy check? `validated()` only? N+1 guarded (`with`)? index with new FK/filter? Pest coverage for allow + deny?

## What NOT to do

- No secrets/keys/tokens in history (rotate + purge if leaked). No committed `.env`, dumps, screenshots of real data. No giant mixed diffs. No `migrate:fresh` instructions for prod.

## Important commands

```sh
git status --short
git diff --stat
git log --oneline -5
composer lint && composer test && pnpm lint
```

## Security

- Pre-push: `git diff --cached` scanned for secrets; `composer audit` + `pnpm audit` clean; prod-affecting migrations reviewed twice.
