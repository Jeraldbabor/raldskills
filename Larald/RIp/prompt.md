Scaffold my Laravel standard. Load .agent/skills/laravel-base-stack/SKILL.md.

Single-repo monolith: Laravel 12 + Inertia v2 React + PostgreSQL + Pest.
Composer for PHP, pnpm for JS, Docker Compose (app + pgsql [+ redis]).
Session + CSRF web auth, Policies on ownable models, FormRequest per write op,
private-disk uploads, .env.example with placeholders only.
Verify: composer lint + composer test + pnpm build + pnpm lint.
