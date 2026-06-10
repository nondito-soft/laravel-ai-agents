---
description: "Use when creating or editing service classes in app/Services/. Covers BaseModelService, DB transactions, activity logging, and business logic patterns."
applyTo: "app/Services/**/*.php"
---

# Laravel Services

## Structure
- Every service extends `BaseModelService` and implements `model(): string`
- Inject dependencies via constructor (not `new` inside methods)
- Use `DB::transaction()` for multi-step operations
- Every public method must have a `/** ... */` block comment describing what it does
- This pattern applies equally to all resources

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
            $this->logActivity($resource, 'created', '{resource}.created');
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
- Log manually — never use model observers
- Pattern: clone before → make change → log with old state

```php
$old = clone $user;
$user->update($data);
$this->logActivity($user, 'updated', 'user.updated', $old);
```


