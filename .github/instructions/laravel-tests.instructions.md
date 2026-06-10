---
description: "Use when creating or editing PHPUnit test files. Covers Feature and Unit test conventions, database setup, permission seeding, frontend render assertions, and service mocking patterns."
applyTo: "tests/**/*.php"
---

# Laravel Testing Conventions

## Project Test Stack
- **PHPUnit** via `php artisan test`
- **Feature tests** — HTTP-level, in `tests/Feature/`
- **Unit tests** — service/logic-level, in `tests/Unit/`
- **DB** — SQLite in-memory for speed (enabled via `phpunit.xml` env vars)
- **Auth** — Breeze (web) via `actingAs()`, Sanctum (API) via `actingAs($user, 'sanctum')`

## Database Setup

Always use `RefreshDatabase` in Feature tests:

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserControllerTest extends TestCase
{
    use RefreshDatabase;
}
```

Enable SQLite in-memory in `phpunit.xml` by uncommenting:
```xml
<env name="DB_CONNECTION" value="sqlite"/>
<env name="DB_DATABASE" value=":memory:"/>
```

## User & Permission Fixtures

Never hardcode IDs. Always create users and permissions via factories/seeders:

```php
use Database\Seeders\PermissionSeeder;
use Database\Seeders\RoleSeeder;

protected function setUp(): void
{
    parent::setUp();
    $this->seed(PermissionSeeder::class);
    $this->seed(RoleSeeder::class);
}
```

Create a user with a specific permission:

```php
$user = User::factory()->create(['is_active' => true]);
$user->givePermissionTo('can-view-user');

$this->actingAs($user);
```

## Feature Test Structure

One test class per controller, in `tests/Feature/{Resource}/`. E.g.:

```
tests/
  Feature/
    User/
      UserControllerTest.php
    Department/
      DepartmentControllerTest.php
    Permission/
      RoleControllerTest.php
      PermissionControllerTest.php
    Auth/
      LoginTest.php
```

### HTTP Assertions

```php
// Page renders — assert per the active frontend stack (FRONTEND config):

// vue-inertia / react-inertia (needs assertInertia / Inertia testing helpers):
$response->assertInertia(fn ($page) =>
    $page->component('Users/Index')
        ->has('users')
        ->has('pageTitle')
        ->has('breadcrumbs')
);

// blade / livewire:
$response->assertOk()->assertViewHas(['users', 'pageTitle', 'breadcrumbs']);
// (livewire full-page components can also use Livewire::test(Index::class)->assertOk())

// Redirects with flash
$response->assertRedirect(route('users.index'));
$response->assertSessionHas('success');
$response->assertSessionHas('error');

// Status codes
$response->assertStatus(200);
$response->assertForbidden();   // 403
$response->assertNotFound();    // 404
```

### Authorization Tests — always test both sides

```php
public function test_store_requires_can_create_user_permission(): void
{
    $user = User::factory()->create();
    // no permission granted

    $this->actingAs($user)
        ->post(route('users.store'), [...])
        ->assertForbidden();
}

public function test_store_creates_user_with_permission(): void
{
    $user = User::factory()->create();
    $user->givePermissionTo('can-create-user');

    $this->actingAs($user)
        ->post(route('users.store'), $this->validPayload())
        ->assertRedirect(route('users.index'))
        ->assertSessionHas('success');
}
```

### Unauthenticated Redirect

```php
public function test_index_redirects_guests(): void
{
    $this->get(route('users.index'))->assertRedirect(route('login'));
}
```

## Unit Test Structure

One test class per service, in `tests/Unit/{Resource}/`:

```
tests/
  Unit/
    UserServiceTest.php
    Permission/
      RoleServiceTest.php
      PermissionServiceTest.php
```

### Service Unit Test Pattern

Use `RefreshDatabase` (services hit real DB in unit tests — mock only external I/O):

```php
class UserServiceTest extends TestCase
{
    use RefreshDatabase;

    private UserService $service;

    protected function setUp(): void
    {
        parent::setUp();
        $this->seed(PermissionSeeder::class);
        $this->service = app(UserService::class);
    }

    public function test_create_user_persists_to_database(): void
    {
        $admin = User::factory()->create();
        $this->actingAs($admin);

        $user = $this->service->createUser([
            'name'     => 'Test User',
            'email'    => 'test@example.com',
            'password' => 'password',
            'is_active' => true,
        ]);

        $this->assertDatabaseHas('users', ['email' => 'test@example.com']);
        $this->assertInstanceOf(User::class, $user);
    }
}
```

## Test Naming Conventions

Use `test_` prefix + snake_case describing **behavior**, not implementation:

```
test_index_renders_users_list_for_authorized_user
test_store_creates_user_and_redirects_on_success
test_store_returns_error_flash_on_failure
test_destroy_requires_can_delete_user_permission
test_change_status_toggles_user_is_active
```

## What to Cover Per Controller

For each controller action, write tests for:

| Scenario | Test |
|---|---|
| Guest access | Assert redirect to login |
| Missing permission | Assert 403 |
| Valid request | Assert redirect + success flash |
| Invalid payload | Assert validation errors |
| Route model not found | Assert 404 |
| State change (e.g. changeStatus) | Assert DB updated |

## API Tests (Sanctum)

```php
$user = User::factory()->create();
$token = $user->createToken('test')->plainTextToken;

$this->withHeader('Authorization', "Bearer $token")
     ->getJson(route('api.users.index'))
     ->assertOk()
     ->assertJsonStructure(['data']);
```

## Do Not

- Do not use `$this->get('/')` with hardcoded paths — always use named routes
- Do not assert on exact translated strings — use `assertSessionHas('success')` not the message value
- Do not skip the missing-permission test for any `store`, `update`, or `destroy` action
- Do not use `User::find(1)` — always create via `User::factory()->create()`
- Do not leave `dd()` or `dump()` in test files
