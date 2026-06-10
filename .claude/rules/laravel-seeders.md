---
description: "Use when creating or editing database seeders. Covers PermissionSeeder structure, required fields, and seeder conventions."
paths: ["database/seeders/**/*.php"]
---

# Database Seeders

## If using Spatie laravel-permission: PermissionSeeder
When adding a new module using Spatie laravel-permission, append its four permissions to `PermissionSeeder.php`:
- Required Spatie fields: `id`, `name`, `guard_name`, `group_name`, `is_active`
- `guard_name`: typically `'web'` (use `'api'` if managing API permissions separately)
- `group_name`: groups related permissions in the UI (e.g. `'User Management'`)
- `name`: permission slug (this project uses `can-{view|create|edit|delete}-{resource}`, adjust per your convention)

```php
[
    'id'         => 21, // next sequential id
    'name'       => 'can-view-user',  // or your convention
    'guard_name' => 'web',
    'group_name' => 'User Management',  // e.g. 'Department Management', 'Role Administration'
    'is_active'  => true,
],
[
    'id'         => 22,
    'name'       => 'can-create-user',  // or your convention
    'guard_name' => 'web',
    'group_name' => 'User Management',
    'is_active'  => true,
],
[
    'id'         => 23,
    'name'       => 'can-edit-user',  // or your convention
    'guard_name' => 'web',
    'group_name' => 'User Management',
    'is_active'  => true,
],
[
    'id'         => 24,
    'name'       => 'can-delete-user',  // or your convention
    'guard_name' => 'web',
    'group_name' => 'User Management',
    'is_active'  => true,
],
```

## General Seeder Conventions
- Wrap destructive seeders with a MySQL foreign key check disable:
  ```php
  if (DB::getDriverName() === 'mysql') { DB::statement('SET FOREIGN_KEY_CHECKS=0;'); }
  DB::table('{your_table}')->truncate();
  // ... insert data
  if (DB::getDriverName() === 'mysql') { DB::statement('SET FOREIGN_KEY_CHECKS=1;'); }
  ```
- Register new seeders in `DatabaseSeeder::run()` in the correct dependency order
- Use bulk insert methods (`insert()`) for performance — not `foreach + create()` (N+1 issue)

### If using Spatie laravel-permission:
- Use `Permission::insert($permissions)` for bulk inserts of permissions
