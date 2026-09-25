---
name: laravel-validation
description: Form Requests, validation rules, enums, and error messages. Use for any input validation work.
---

# Laravel Validation (Form Requests)

## Role

Input-gate agent. Every write op validated server-side with field errors re-rendered in Blade.

## What to do

- ONE FormRequest per write operation (`StoreOrderRequest`, `UpdateProfileRequest`) via `php artisan make:request`. Controller signature type-hints it; handler uses `$request->validated()` ONLY. Failed validation auto-redirects back with `old()` input + `$errors` — design every form to render both.
- `authorize()` returns a real check (`Gate`/ownership/`$this->user()->can(...)`) — never blanket `true` on sensitive routes.
- Rules: strict types (`string`, `integer`, `numeric`, `date`, `exists:table,column`, `unique:table,column,except,id`), `Rule::enum()` for backed enums, `Rule::in` only for tiny static sets, `confirmed` for passwords, `password` rule for current-password checks.
- File rules live here too (`file|max:10240|mimes:...`) — mirrored by storage checks in the upload skill.
- Custom `messages()` for user-facing fields; `attributes()` for friendly field names. Keep messages generic where the field is sensitive (no user-enumeration via errors).
- Nested/array input: `items.*.id => exists:...`, `items.*.qty => integer|min:1` — validate each element, never trust array length/content.

## What NOT to do

- No `$request->all()` / `request()->all()` into `create()`/`update()`/`fill()`. No validation in Blade/Alpine as the authority (client attrs are UX only).
- No trusting client-sent `user_id`, `role`, `price`, `total`, `status` — recompute/derive server-side; strip or ignore with `exclude`/`prohibited` where apt.
- No `sometimes` soup hiding required fields — explicit rules per operation (store vs update requests differ).

## Important commands

```sh
php artisan make:request StoreOrderRequest
composer test -- --filter=OrderValidation
```

## Security

- Reject unknown fields implicitly by using `validated()` output only. Sensitive lookups (`exists:users,email`) must not leak enumeration — pair with throttling + generic messages on auth-adjacent forms.
