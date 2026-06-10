---
description: "Use when creating or editing PHPUnit test files. Covers Feature and Unit test conventions, database setup, permission seeding, frontend render assertions, and service mocking patterns."
paths: ["tests/**/*.php"]
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
// Unauthorized user → 403
$this->actingAs($userWithoutPermission)
    ->get(route('users.index'))
    ->assertForbidden();

// Authorized user → 200
$this->actingAs($userWithPermission)
    ->get(route('users.index'))
    ->assertOk();
```
