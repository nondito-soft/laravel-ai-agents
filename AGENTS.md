# AGENTS.md — Laravel AI Agents

> **This is the root context file for all agentic tools**.
> Read this file first. All other doc files derive from or reference it.

---

## Workflow Orchestration

### 1. Plan Mode Default
- Enter plan mode for ANY non-trivial task (3+ steps or architectural decisions)
- If something goes sideways, STOP and re-plan immediately — don't keep pushing
- Use plan mode for verification steps, not just building
- Write detailed specs upfront to reduce ambiguity

### 2. Subagent Strategy
- Use subagents liberally to keep main context window clean
- Offload research, exploration, and parallel analysis to subagents
- For complex problems, throw more compute at it via subagents
- One task per subagent for focused execution

### 3. Self-Improvement Loop
- After ANY correction from the user: update `tasks/lessons.md` with the pattern
- Write rules for yourself that prevent the same mistake
- Ruthlessly iterate on these lessons until mistake rate drops
- Review lessons at session start for relevant project

### 4. Verification Before Done
- Never mark a task complete without proving it works
- Diff behavior between main and your changes when relevant
- Ask yourself: "Would a staff engineer approve this?"
- Run tests, check logs, demonstrate correctness

### 5. Demand Elegance (Balanced)
- For non-trivial changes: pause and ask "is there a more elegant way?"
- If a fix feels hacky: "Knowing everything I know now, implement the elegant solution"
- Skip this for simple, obvious fixes — don't over-engineer
- Challenge your own work before presenting it

### 6. Autonomous Bug Fixing
- When given a bug report: just fix it. Don't ask for hand-holding
- Point at logs, errors, failing tests — then resolve them
- Zero context switching required from the user
- Go fix failing CI tests without being told how

---

## Task Management

1. **Plan First**: Write plan to `tasks/todo.md` with checkable items
2. **Verify Plan**: Check in before starting implementation
3. **Track Progress**: Mark items complete as you go
4. **Explain Changes**: High-level summary at each step
5. **Document Results**: Add review section to `tasks/todo.md`
6. **Capture Lessons**: Update `tasks/lessons.md` after corrections

---

## Core Principles

> **#1 Priority — Simple, Industry-Standard Code**: Every piece of code written or modified MUST be simple and follow industry-standard practices. If it's non-standard, replace it. No exceptions.

- **Simplicity First**: Make every change as simple as possible. Impact minimal code.
- **No Laziness**: Find root causes. No temporary fixes. Senior developer standards.
- **Minimal Impact**: Changes should only touch what's necessary. Avoid introducing bugs.

---

## Stack Configuration

> **Single source of truth for the frontend stack.** Every agent reads this file first, so this flag tells all of them which frontend conventions to follow. Set it once per project.

```
FRONTEND: vue-inertia
# options: vue-inertia | react-inertia | livewire | blade
```

**If `FRONTEND` is unset, auto-detect it** from the project, then proceed as if it were set:
- `composer.json` has `livewire/livewire` → `livewire`
- `composer.json` has `inertiajs/inertia-laravel` **and** `package.json` has `react` → `react-inertia`
- `composer.json` has `inertiajs/inertia-laravel` **and** `package.json` has `vue` → `vue-inertia`
- none of the above (server-rendered views only) → `blade`

The active value selects exactly one frontend rule file (`rules/frontend/{FRONTEND}.md` for Claude, `instructions/frontend/{FRONTEND}.instructions.md` for Copilot). **All backend rules are stack-neutral** — they state intent ("return the index view with `breadcrumbs` + `pageTitle`") and defer the mechanism to the active frontend rule. Never hardcode `Inertia::render`, `.vue`, or any single stack's API into a backend rule, agent, or scaffold.

---

## Project Overview

**Laravel AI Agents** — a multi-tenant starter with RBAC and audit logging. The frontend stack is configured above (default **Inertia.js + Vue.js**); the same backend conventions apply to React/Inertia, Livewire, or Blade.

| Layer | Technology |
|---|---|
| Framework | Laravel (any version) / PHP 8.1+ |
| Frontend | Per `FRONTEND` config above (default: Inertia.js + Vue.js) + Tailwind CSS |
| Auth (web) | Laravel Breeze (session) |
| Auth (API) | Laravel Sanctum (token) |
| RBAC | Spatie laravel-permission |
| Audit | Spatie laravel-activitylog |
| Login tracking | Rappasoft laravel-authentication-log |
| Breadcrumbs | Diglactic laravel-breadcrumbs |
| DB | SQLite (dev) · MySQL (prod) |

---

## Documentation Map

> **This file (`AGENTS.md`) is the single root context file.** All other docs derive from or reference it.

| File | Purpose | When to read |
|---|---|---|
| [`.github/copilot-instructions.md`](.github/copilot-instructions.md) | GitHub Copilot context — compact subset of this file | Auto-loaded by GitHub Copilot |
| [`.claude/CLAUDE.md`](.claude/CLAUDE.md) | Claude Code context — references this file + quick command reference | Auto-loaded by Claude Code |
| [`README.md`](./README.md) | Human quickstart — setup, prerequisites, commands | Onboarding a new developer |
| [`docs/features/*.md`](./docs/features/) | Per-module feature documentation (routes, permissions, schema, rules) | Understanding a specific module |

### File-Type Instructions

Loaded automatically when editing matching files. Both tools have their own copy — same rules, tool-native format.

#### GitHub Copilot (`.github/instructions/`)

| File | Applies to |
|---|---|
| [laravel-controllers.instructions.md](.github/instructions/laravel-controllers.instructions.md) | `app/Http/**/*.php` |
| [laravel-services.instructions.md](.github/instructions/laravel-services.instructions.md) | `app/Services/**/*.php` |
| [laravel-models.instructions.md](.github/instructions/laravel-models.instructions.md) | `app/Models/**/*.php` |
| [laravel-migrations.instructions.md](.github/instructions/laravel-migrations.instructions.md) | `database/migrations/**/*.php` |
| [laravel-seeders.instructions.md](.github/instructions/laravel-seeders.instructions.md) | `database/seeders/**/*.php` |
| [laravel-routes.instructions.md](.github/instructions/laravel-routes.instructions.md) | `routes/**/*.php` |
| [laravel-lang.instructions.md](.github/instructions/laravel-lang.instructions.md) | `resources/lang/**` |
| [laravel-tests.instructions.md](.github/instructions/laravel-tests.instructions.md) | `tests/**/*.php` |
| [frontend/{stack}.instructions.md](.github/instructions/frontend/) | `resources/js/**`, `resources/views/**` — only the file matching `FRONTEND` applies |

#### Claude Code (`.claude/rules/`)

| File | Applies to |
|---|---|
| [laravel-controllers.md](.claude/rules/laravel-controllers.md) | `app/Http/**/*.php` |
| [laravel-services.md](.claude/rules/laravel-services.md) | `app/Services/**/*.php` |
| [laravel-models.md](.claude/rules/laravel-models.md) | `app/Models/**/*.php` |
| [laravel-migrations.md](.claude/rules/laravel-migrations.md) | `database/migrations/**/*.php` |
| [laravel-seeders.md](.claude/rules/laravel-seeders.md) | `database/seeders/**/*.php` |
| [laravel-routes.md](.claude/rules/laravel-routes.md) | `routes/**/*.php` |
| [laravel-lang.md](.claude/rules/laravel-lang.md) | `resources/lang/**` |
| [laravel-tests.md](.claude/rules/laravel-tests.md) | `tests/**/*.php` |
| [frontend/{stack}.md](.claude/rules/frontend/) | `resources/js/**`, `resources/views/**` — only the file matching `FRONTEND` applies |

### Agents

| Agent | GitHub Copilot | Claude Code | Purpose |
|---|---|---|---|
| `developer` | [developer.agent.md](.github/agents/developer.agent.md) | [developer.agent.md](.claude/agents/developer.agent.md) | Implement a feature/change/fix end-to-end (backend + frontend) on the existing codebase; the general builder |
| `documentor` | [documentor.agent.md](.github/agents/documentor.agent.md) | [documentor.agent.md](.claude/agents/documentor.agent.md) | Scan codebase and update all engineering docs to match reality |
| `code-reviewer` | [code-reviewer.agent.md](.github/agents/code-reviewer.agent.md) | [code-reviewer.agent.md](.claude/agents/code-reviewer.agent.md) | Audit code for standards, security, localization, theme compliance |
| `code-formatter` | [code-formatter.agent.md](.github/agents/code-formatter.agent.md) | [code-formatter.agent.md](.claude/agents/code-formatter.agent.md) | Format PHP (Pint), JavaScript/Vue (Prettier), and configuration files |
| `tester` | [tester.agent.md](.github/agents/tester.agent.md) | [tester.agent.md](.claude/agents/tester.agent.md) | Write missing PHPUnit Feature and Unit tests |
| `package-upgrader` | [package-upgrader.agent.md](.github/agents/package-upgrader.agent.md) | [package-upgrader.agent.md](.claude/agents/package-upgrader.agent.md) | Safely upgrade Laravel, Composer, and npm dependencies |
| `scaffolder` | [scaffolder.agent.md](.github/agents/scaffolder.agent.md) | [scaffolder.agent.md](.claude/agents/scaffolder.agent.md) | Scaffold a complete new module (migration → seeder → feature doc) |
| `security-auditor` | [security-auditor.agent.md](.github/agents/security-auditor.agent.md) | [security-auditor.agent.md](.claude/agents/security-auditor.agent.md) | OWASP-aligned security vulnerability scanning |
| `deployer` | [deployer.agent.md](.github/agents/deployer.agent.md) | [deployer.agent.md](.claude/agents/deployer.agent.md) | Pre-deploy checks, deployment execution, post-deploy verification, rollback |
| `code-refactorer` | [code-refactorer.agent.md](.github/agents/code-refactorer.agent.md) | [code-refactorer.agent.md](.claude/agents/code-refactorer.agent.md) | Dead code, duplication, complexity reduction, unused dependency detection |
| `pr-reviewer` | [pr-reviewer.agent.md](.github/agents/pr-reviewer.agent.md) | [pr-reviewer.agent.md](.claude/agents/pr-reviewer.agent.md) | Review PRs/changesets against project conventions and standards |
| `planner` | [planner.agent.md](.github/agents/planner.agent.md) | [planner.agent.md](.claude/agents/planner.agent.md) | Analyze scope, break down features, estimate timeline, map dependencies, and create roadmaps |

### Prompts & Commands (one-click tasks)

| Task | GitHub Copilot prompt | Claude Code command | Purpose |
|---|---|---|---|
| `develop` | [develop.prompt.md](.github/prompts/develop.prompt.md) | [develop.md](.claude/commands/develop.md) | Implement a feature/change/fix end-to-end on the existing codebase |
| `scaffold-module` | [scaffold-module.prompt.md](.github/prompts/scaffold-module.prompt.md) | [scaffold-module.md](.claude/commands/scaffold-module.md) | Scaffold a complete new module |
| `generate-tests` | [generate-tests.prompt.md](.github/prompts/generate-tests.prompt.md) | [generate-tests.md](.claude/commands/generate-tests.md) | Run tests and generate missing ones |
| `review-code` | [review-code.prompt.md](.github/prompts/review-code.prompt.md) | [review-code.md](.claude/commands/review-code.md) | Review staged changes before commit |
| `format-code` | [format-code.prompt.md](.github/prompts/format-code.prompt.md) | [format-code.md](.claude/commands/format-code.md) | Format all code (PHP, JavaScript/Vue, config files) |
| `update-docs` | [update-docs.prompt.md](.github/prompts/update-docs.prompt.md) | [update-docs.md](.claude/commands/update-docs.md) | Update all docs to match code |
| `audit-security` | [audit-security.prompt.md](.github/prompts/audit-security.prompt.md) | [audit-security.md](.claude/commands/audit-security.md) | Run a security audit |
| `upgrade-packages` | [upgrade-packages.prompt.md](.github/prompts/upgrade-packages.prompt.md) | [upgrade-packages.md](.claude/commands/upgrade-packages.md) | Audit and upgrade dependencies |
| `refactor-code` | [refactor-code.prompt.md](.github/prompts/refactor-code.prompt.md) | [refactor-code.md](.claude/commands/refactor-code.md) | Analyze and refactor code for health |
| `plan-project` | [plan-project.prompt.md](.github/prompts/plan-project.prompt.md) | [plan-project.md](.claude/commands/plan-project.md) | Analyze scope, break down features, estimate timeline, map dependencies, and create roadmaps |
| `deploy` | [deploy.prompt.md](.github/prompts/deploy.prompt.md) | [deploy.md](.claude/commands/deploy.md) | Execute safe deployment with verification |

### Setup Guides

| Guide | Purpose |
|---|---|
| [`docs/copilot-agentic-setup-guide.md`](docs/copilot-agentic-setup-guide.md) | How the GitHub Copilot layer works — file hierarchy, glob patterns, agent design, prompt templates |
| [`docs/claude-code-agentic-setup-guide.md`](docs/claude-code-agentic-setup-guide.md) | How the Claude Code layer works — agents, commands, rules, CLAUDE.md wiring |

---

## Architecture

### Service Layer Pattern
All business logic lives in `app/Services/`. Controllers are thin — they validate input, call a service, and return a response. Never put DB queries or business rules directly in a controller.

### BaseModelService
All services extend `BaseModelService` and implement `model(): string`. It provides:
- `create(array)`, `update(Model, array)`, `find()`, `findOrFail()`, `delete()`, `all()`, `first()`

> **Note:** `BaseModelService::create()` injects `created_by` and `updated_by` from `auth()->id()`. These columns must exist in every table that goes through `create()`. If a model doesn't need tracking, override `create()` in the child service.

### Permission Middleware
Controllers implement `HasMiddleware` and define a static `middleware()` method returning an array of `new Middleware(...)` objects. PHP class names are **case-insensitive**, so `new middleware(...)` and `new Middleware(...)` are identical — both are valid and intentional in this codebase. Do not flag lowercase as a bug.

```php
public static function middleware(): array
{
    return [
        new Middleware('permission:can-create-user', only: ['create', 'store']),
        new middleware('role.isDeletable', only: ['destroy']),  // valid — lowercase is fine
    ];
}
```

### Activity Logging Convention
Services log changes manually using Spatie's `activity()` helper. The pattern is consistent across `UserService`, `RoleService`, and `PermissionService`:
1. Clone the model before changes (`$old = clone $model`)
2. Make the change
3. Call `$this->logActivity($model, $event, $logKey, $old)` with old/new properties

Do not introduce observer-based logging — the manual approach is intentional for granular control.

---

## Directory Map

```
app/
  Constants/Constants.php          # WEB = 'web', API = 'api' guard constants
  Domains/
    Auth/Events/Login.php          # Custom Login event extending Laravel's base Login
  Http/
    Controllers/
      Auth/                        # Breeze auth controllers (login, register, reset, verify)
      API/                         # Sanctum API controllers
        AuthController.php
        UserController.php
        ConfigurationController.php
        HealthCheckController.php
      Permission/
        RoleController.php
        PermissionController.php
      UserController.php
      ConfigurationController.php
      ProfileController.php
      LanguageController.php
      LocalizationController.php
    Middleware/
      Language.php                 # Sets locale from cookie
      HandleInertiaRequests.php    # Shares auth.user, auth.permissions, flash.success/error
      CheckIsRoleDeletable.php     # Blocks destroy if role.is_deletable = false
      CheckIsRoleSuperAdmin.php    # Blocks status change on 'Super Admin' role
    Requests/
      Auth/                        # LoginRequest (rate-limited 5/min), PasswordRequest
      API/LoginRequest.php
      Configuration/UpdateConfigurationRequest.php
      Permission/                  # Create/Update Role, Permission, Assign requests
      User/                        # UserCreateRequest, UserUpdateRequest, UserPasswordUpdateRequest
      ProfileUpdateRequest.php
  Models/
    User.php                       # HasApiTokens, HasRoles, AuthenticationLoggable, SoftDeletes
    Role.php                       # Extends Spatie Role; uses SoftDeletes
    Permission.php                 # Extends Spatie Permission; uses SoftDeletes
    Configuration.php              # LogsActivity; timestamps=false; belongs to Country
    Country.php
    Language.php
  Services/
    BaseModelService.php           # Abstract CRUD base
    UserService.php
    AuthService.php
    ActivityLogService.php
    AuthenticationLogService.php
    ConfigurationService.php
    CountryService.php
    LanguageService.php
    HelperService.php              # Static options: blood groups, genders, date formats, marital status, guards
    Permission/
      RoleService.php
      PermissionService.php
  Providers/
    AppServiceProvider.php         # API rate limiting: 60 req/min
    EventServiceProvider.php       # Registered → SendEmailVerificationNotification
    RouteServiceProvider.php       # HOME = '/dashboard'

routes/
  web.php                          # All web routes (wrapped in Language middleware)
  api.php                          # API v1 routes (login, logout, users, configurations)
  auth.php                         # Breeze auth routes (imported by web.php)
  breadcrumbs.php                  # Breadcrumb definitions (Diglactic)
```

---

## Key Conventions

### Controllers
- Always use route model binding (`User $user`, `Role $role`)
- Always call `getUserDetails($user)` / `getRoleDetails($role)` before passing to service — it eager-loads relationships
- Flash `"success"` or `"error"` via `Redirect::back()->with($status, $message)`
- Page titles and messages use `__()` translation keys (e.g. `__('message.custom.user.store.success')`)
- Return the response via the active frontend rule (`FRONTEND` config) — **always include `breadcrumbs` and `pageTitle`** regardless of stack. Inertia stacks use `Inertia::render('ComponentPath', $responseData)`; Blade uses `return view('path', $responseData)`; Livewire routes to the component.

### Services
- Wrap multi-step operations in `DB::transaction()`
- Return the model or `false` on failure (not exceptions) — callers check truthiness
- Log activity after every state-changing operation
- Every public method must have a `/** ... */` block comment describing what it does (1-2 lines: what, not how)
- `ConfigurationService::updateConfiguration()` uses `Configuration::where(...)->update(...)` per key — do not revert to a `foreach + save()` loop (N+1 writes)

### FormRequests
- `authorize()` always returns `true` — authorization is handled at the controller layer via `HasMiddleware` + `permission:can-*` middleware
- Custom validation messages go in `messages()` method
- Use `$this->route('modelName')` to get the bound model for unique-except-current rules

### Models
- Use `$fillable` (never `$guarded = []`)
- `Configuration` has `$timestamps = false` — intentional, don't add timestamps to it
- `User` uses `SoftDeletes` trait — deletions set `deleted_at`, not a hard delete.
- `User` columns: `name`, `email`, `password`, `is_active`, `last_login_at`, `deleted_at`
- `Role` extends Spatie Role with `SoftDeletes` trait. Extra columns: `is_editable`, `is_deletable`, `is_available`, `is_active`, `deleted_at`
- `Permission` extends Spatie Permission with `SoftDeletes` trait. Extra columns: `group_name`, `is_active`, `deleted_at`

### Routes
- Web routes are grouped under `Language` middleware (locale cookie handling)
- Protected routes require `auth` and `verified` middleware
- Named routes follow Laravel resource conventions: `users.index`, `users.store`, `roles.changeStatus`, etc.
- Custom sub-resource routes use prefix blocks under `users/{user}`

### Frontend Shared Data
Data shared globally with every view. **Inertia stacks (vue-inertia / react-inertia)** expose it via `HandleInertiaRequests`; **Blade/Livewire** via a `View::share()` / view composer. Either way the keys are the same:
- `auth.user` — authenticated user
- `auth.permissions` — permissions via roles (array)
- `flash.success` — success message
- `flash.error` — error message

### Code Style
- `config('key')` — never `$_ENV`
- No `dd()` / `dump()` in committed code
- Translation keys for all user-facing strings: `__('message.custom.resource.action.outcome')`
- Permission slugs: `can-{view|create|edit|delete}-{resource}`
- Named routes: `users.index`, `users.store`, `roles.changeStatus`

---

## Build & Test

```bash
# Development
php artisan serve
npm run dev

# Database
php artisan migrate
php artisan migrate:fresh --seed
php artisan db:seed

# Testing
php artisan test
./vendor/bin/phpunit

# Code style
./vendor/bin/pint

# Cache
php artisan config:clear
php artisan cache:clear
php artisan route:clear
php artisan view:clear

# Queues (if using)
php artisan queue:work
```

---

## Adding a New Module

Follow this pattern when adding a new resource (e.g. `Department`):

1. **Migration** — include `created_by`, `updated_by`, `is_active` columns if needed
2. **Model** — extend `Model`, define `$fillable`
3. **Service** — extend `BaseModelService`, implement `model()`, add business methods
4. **FormRequests** — one for Create, one for Update (with unique-except-current); `authorize()` returns `true` (auth via controller middleware)
5. **Controller** — implement `HasMiddleware`, inject service via constructor, use `permission:can-*` middleware
6. **Routes** — add to `routes/web.php` inside the `auth` group
7. **Permissions** — seed the `can-view-*`, `can-create-*`, `can-edit-*`, `can-delete-*` permissions
8. **Feature Doc** — create `docs/features/{module}.md` using the documentor agent or the template in `documentor.agent.md`

---

## Known Technical Debt

These are documented issues — do not silently work around them, fix them properly when touching the affected area:


## Package Code (hands-off)

Files generated by packages (e.g. `app/Http/Controllers/Auth/` from Breeze) are considered package code.
- **Do not** refactor their business logic or move it to the service layer.
- **Do** fix localization (hardcoded strings → `__()`) and style issues only.


## Git & Commits

- **All commits MUST be authored by the project account:** `nondito-soft <developer@nonditosoft.com>`. This is the only contributor for this repo.
- This is set via the repo's local git config (`git config user.name/user.email`). A tracked `pre-commit` hook blocks commits if no identity is configured.
- **Never add AI co-author trailers** (`Co-Authored-By: Claude/Anthropic/Copilot/...`) to commit messages. A tracked `commit-msg` hook strips them automatically as a safety net.
- The hooks live in `.githooks/` and are wired via `core.hooksPath`. **Fresh clones must run this once** (it's local config, not tracked):
  ```bash
  git config core.hooksPath .githooks
  git config user.name  "nondito-soft"
  git config user.email "developer@nonditosoft.com"
  ```

## Do Not

- Put business logic in controllers
- Use `$guarded = []` on models
- Use `$_ENV` directly — always use `config('key')`
- Leave `dd()` or `dump()` in any file
- Add timestamps to the `configurations` table (it has `$timestamps = false`)
- Flag `new middleware()` (lowercase) as a bug — PHP class names are case-insensitive
- Add duplicate authorization in `FormRequest::authorize()` — authorization is handled by controller middleware
- Use `foreach + $model->save()` in `ConfigurationService` — use `where()->update()` instead (avoid N+1 writes)
- Add a `soft_delete` boolean column to new models — use Laravel's `SoftDeletes` trait with `deleted_at` instead
- Change business logic in package-generated code (`app/Http/Controllers/Auth/` from Breeze, etc.) — only fix localization, hardcoded strings, and style issues; do not refactor their architecture or move logic to the service layer
