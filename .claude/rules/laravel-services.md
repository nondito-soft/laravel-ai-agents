---
description: "Use when creating or editing service classes in app/Services/. Covers BaseModelService, DB transactions, activity logging, and business logic patterns."
paths: ["app/Services/**/*.php"]
---

# Laravel Services

## Structure
- A service that persists or operates on a model's table extends `BaseModelService` and implements `model(): string` — one such service per model (UserService, RoleService, ConfigurationService, …)
- A service that does no table CRUD (auth/token, orchestration, helpers — e.g. `AuthService`, `SocialAuthService`, `HelperService`) does NOT extend `BaseModelService`. If it needs to log activity, it `use`s the `App\Concerns\LogsActivity` trait directly (see Activity Logging)
- Inject dependencies via constructor (not `new` inside methods)
- Use `DB::transaction()` for multi-step operations
- Every public method must have a `/** ... */` block comment describing what it does
- The BaseModelService pattern applies equally to all model-backed resources

## Cross-Service Data Access
- Persistence/finders for a model live on its model-backed service: generic CRUD comes from `BaseModelService` (`create`, `update`, `delete`, `find`, `findOrFail`, `first`, `all`, `restore`, `forceDelete`); model-specific lookups are named methods on the service (e.g. `findByProviderIdentity`, `existsByEmailWithTrashed`, `getForUser`).
- An orchestration service that owns no table (e.g. `SocialAuthService`) injects the model services it needs and delegates standalone queries/writes to them — rather than running `OtherModel::where(...)`, `OtherModel::create(...)`, or `OtherModel::withTrashed()` inline. (Reading through an Eloquent relationship you already hold is fine.)

```php
// SocialAuthService orchestrates; it owns no table.
public function __construct(
    protected SocialAccountService $socialAccountService,
    protected UserService $userService,
) {}

// delegate — never query SocialAccount/User directly here
$account = $this->socialAccountService->findByProviderIdentity($provider, $id);
$user    = $this->userService->create($data);
```

```php
class {Resource}Service extends BaseModelService
{
    protected function model(): string
    {
        return {Resource}::class;
    }

    /**
     * Create a {resource} and log the activity.
     */
    public function create{Resource}(array $data): {Resource}|false
    {
        return DB::transaction(function () use ($data) {
            $resource = $this->create($data); // injects created_by, updated_by
            $this->logActivity(
                $resource,
                'created',
                "Created new {resource} - {$resource->name}",
                $this->extract{Resource}Properties($resource, 'created'),
            );
            return $resource;
        });
    }
}
```

**Examples:**
```php
// User resource
class UserService extends BaseModelService
{
    protected function model(): string
    {
        return User::class;
    }

    public function createUser(array $data): User|false { ... }
}

// Department resource
class DepartmentService extends BaseModelService
{
    protected function model(): string
    {
        return Department::class;
    }

    public function createDepartment(array $data): Department|false { ... }
}
```

## Method Comments
- Every public method gets a `/** ... */` block comment above it
- Describe **what** the method does, not **how** — keep it to 1-2 lines
- Add a second line only for important side effects (e.g., "Detaches all roles on deactivation")

```php
/**
 * Get all users with roles, formatted last_login_at.
 */
public function getUsers() { ... }

/**
 * Toggle user active status.
 * Logs activity with old and new state.
 */
public function changeStatus(User $user) { ... }
```

## BaseModelService::create()
- Automatically injects `created_by` and `updated_by` from `auth()->id()`
- Every model table that flows through `create()` **must** have these columns
- If the model doesn't need tracking, override `create()` in the child service

## Return Values
- Return the model on success, `false` on failure — callers check truthiness
- Do not throw exceptions for expected failures

## Activity Logging
- Log manually — never use model observers.
- **All** activity-log emission goes through the `App\Concerns\LogsActivity` trait. There must be **no inline `activity()` call** anywhere in `app/Services/**` — model services inherit the trait via `BaseModelService`; non-model services `use LogsActivity` directly.
- The trait exposes two methods:
  - `logActivity(Model $model, string $event, string $message, array $attributes = [], array $old = [])` — for resource operations on a model. `$attributes` is the new-state snapshot (create/update); `$old` is the previous snapshot (update/delete). Empty arrays are dropped from the stored `properties`.
  - `logOperation(string $message)` — for subject-less system operations with no model and no properties (e.g. cache clear, session flush); logs under `event('operation')`.
- The **message is passed as a string at the call site** — do not reintroduce a `$log`-key + `$messageList` indirection inside the service.
- Each service keeps a private `extract{Resource}Properties($model, $event = null)` helper that returns the snapshot array; pass its result as `$attributes` / `$old`. The `$event === 'created'` branch forces default flags (see existing services).
- Pattern: clone before → make change → log with old + new snapshots.

```php
// Resource operation (update): pass new + old snapshots, message as a string.
$oldUser = clone $user;
$user->update($data);
$this->logActivity(
    $user,
    'updated',
    "Updated the user - {$user->name}",
    $this->extractUserProperties($user, 'updated'),
    $this->extractUserProperties($oldUser),
);

// Delete: only the old snapshot is meaningful (named arg skips $attributes).
$this->logActivity(
    $user,
    'deleted',
    "Deleted the user - {$user->name}",
    old: $this->extractUserProperties($oldUser),
);

// Subject-less system operation: no model, no properties.
$this->logOperation('Cleared application cache');
```
