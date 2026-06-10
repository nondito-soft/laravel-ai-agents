---
description: "Use when editing routes/web.php, routes/api.php, or routes/breadcrumbs.php. Covers auth group structure, named route conventions, and breadcrumb registration."
paths: ["routes/**/*.php"]
---

# Routes & Breadcrumbs

## Web Routes (`routes/web.php`)
- All protected routes go inside the `auth` + `verified` middleware group
- Route names follow Laravel resource conventions: `{resource}.index`, `{resource}.store`, `{resource}.show`, `{resource}.create`, `{resource}.edit`, `{resource}.update`, `{resource}.destroy`
- Custom actions use camelCase suffix: `{resource}.changeStatus`, `{resource}.updatePassword`
- This pattern applies equally to all resources (users, roles, permissions, departments, etc.)

```php
// Example: User resource
Route::middleware(['auth', 'verified'])->group(function () {
    Route::resource('users', UserController::class);
    Route::patch('users/{user}/password', [UserController::class, 'updatePassword'])
        ->name('users.updatePassword');
});

// Example: Custom resource
Route::middleware(['auth', 'verified'])->group(function () {
    Route::resource('departments', DepartmentController::class);
    Route::patch('departments/{department}/status', [DepartmentController::class, 'changeStatus'])
        ->name('departments.changeStatus');
});
```

## API Routes (`routes/api.php`)
- All authenticated API routes use `auth:sanctum` middleware
- Group under version prefix if needed: `Route::prefix('v1')`

## Breadcrumbs (`routes/breadcrumbs.php`)
- Register every new resource's breadcrumb trail here
- Establish an appropriate breadcrumb hierarchy; **this project chains from `dashboard` as the root ancestor** via `$trail->parent()`
- Use translation keys — never hardcode strings

```php
// Resource index
Breadcrumbs::for('users.index', function (BreadcrumbTrail $trail) {
    $trail->parent('dashboard');
    $trail->push(__('breadcrumb.custom.user.index'), route('users.index'));
});

// Resource create
Breadcrumbs::for('users.create', function (BreadcrumbTrail $trail) {
    $trail->parent('users.index');
    $trail->push(__('breadcrumb.custom.user.create'), route('users.create'));
});

// Resource show/edit (receives the bound model)
Breadcrumbs::for('users.show', function (BreadcrumbTrail $trail, User $user) {
    $trail->parent('users.index');
    $trail->push($user->name, route('users.show', $user));
});
```

- Breadcrumb names must match the route name exactly
- Controller passes breadcrumbs: `'breadcrumbs' => breadcrumbs()->generate('users.index')`
