# Laravel Copilot Agents — Copilot Instructions

## Stack
- **Framework**: Laravel (any version) / PHP 8.1+
- **Frontend**: configured via `FRONTEND` in [`AGENTS.md`](../AGENTS.md#stack-configuration) — `vue-inertia` (default) | `react-inertia` | `livewire` | `blade`. Follow the matching file in [`instructions/frontend/`](instructions/frontend/); if unset, auto-detect (see AGENTS.md).
- **Auth web**: Laravel Breeze (session) | **Auth API**: Laravel Sanctum (token)
- **RBAC**: Spatie laravel-permission
- **Audit**: Spatie laravel-activitylog
- **Login tracking**: Rappasoft laravel-authentication-log
- **Breadcrumbs**: Diglactic laravel-breadcrumbs
- **DB**: SQLite (dev), MySQL (prod)

## Architecture Rules

### Service Layer (mandatory)
- All business logic lives in `app/Services/`. Controllers are thin: validate → call service → return response.
- All services extend `BaseModelService` and implement `model(): string`.
- `BaseModelService::create()` injects `created_by` and `updated_by` from `auth()->id()` — every table that flows through it must have these columns.
- Wrap multi-step operations in `DB::transaction()`.

### Controllers
- Implement `HasMiddleware`; define `public static function middleware(): array`.
- Always use route model binding (`User $user`, `Role $role`).
- Flash messages: `Redirect::back()->with('success'|'error', __('message.key'))`.
- Render via the active frontend stack (`FRONTEND` config) — always include `breadcrumbs` and `pageTitle` in the data array. Inertia stacks use `Inertia::render(...)`, Blade uses `view(...)`, Livewire routes to the component.

### FormRequests
- `authorize()` always returns `true` — authorization is handled at the controller layer via `HasMiddleware` + `permission:can-*` middleware.
- Use `$this->route('model')` for unique-except-current validation rules.

### Models
- Always use `$fillable` — never `$guarded = []`.
- New models that need soft-deletion use `SoftDeletes` trait (`deleted_at`) — never add a `soft_delete` boolean.
- `Configuration` has `$timestamps = false` — do not add timestamps.

### Activity Logging
- Log manually via `activity()` helper — never use observers.
- Pattern: clone model → make change → call `$this->logActivity($model, $event, $logKey, $old)`.

## Code Conventions
- Use `config('key')` — never `$_ENV` directly.
- No `dd()` or `dump()` in any committed file.
- Translation keys for all user-facing strings: `__('message.custom.resource.action.outcome')`.
- Permission strings follow: `can-{view|create|edit|delete}-{resource}`.
- Named routes follow resource conventions: `users.index`, `users.store`, `roles.changeStatus`.

## Directory Map
```
app/Services/          # All business logic
app/Http/Controllers/  # Thin controllers
app/Http/Requests/     # FormRequests (one Create, one Update per resource)
app/Models/            # Eloquent models
routes/web.php         # Web routes (Language middleware group)
routes/api.php         # API routes (Sanctum)
resources/js/          # JS frontend (Inertia: Vue/React pages) — for vue-inertia / react-inertia
resources/views/       # Blade views / Livewire component views — for blade / livewire
database/migrations/   # Migrations
```

## Adding a New Module (checklist)
1. Migration — include `created_by`, `updated_by`, `is_active` columns
2. Model — extend `Model`, define `$fillable`
3. Service — extend `BaseModelService`, implement `model()`
4. FormRequests — Create + Update; `authorize()` returns `true` (auth via controller middleware)
5. Controller — `HasMiddleware`, inject service, permission middleware
6. Routes — inside `auth` group in `routes/web.php`
7. Permissions seeder — `can-view-*`, `can-create-*`, `can-edit-*`, `can-delete-*`

## Known Tech Debt (do not silently work around — fix properly)

## Package Code (hands-off)
See [`AGENTS.md`](../AGENTS.md#package-code-hands-off) for the full rule and rationale.
