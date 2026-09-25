Scaffold my Laravel Blade standard. Load .agent/skills/laravel-base-stack/SKILL.md.

Single-repo monolith: Laravel 12 + Blade + Tailwind + Alpine.js + PostgreSQL + Pest.
Composer for PHP, pnpm for the Vite build, Docker Compose (app + pgsql [+ redis]).
Session + CSRF web auth, Policies on ownable models, FormRequest per write op,
Blade layouts + components, old()/error/flash on every form,
private-disk uploads, .env.example with placeholders only.
Verify: composer lint + composer test + pnpm build.
