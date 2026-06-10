---
description: "Use when editing language/translation files in resources/lang/. Covers which files to update, key naming conventions for messages, page titles, and breadcrumbs."
paths: ["resources/lang/**"]
---

# Translation Files

## Files to Update for Every New Module

When adding a new resource, add keys to **all three** files in every language folder (e.g. `en/`, `bn/`, `sv/`, etc.):

### 1. `resources/lang/{locale}/message.php`
Flash and CRUD outcome messages — nested under `custom.{resource}.{action}.{outcome}`:

```php
'custom' => [
    'user' => [
        'store' => [
            'success' => 'User created successfully',
            'error'   => 'User could not be created',
        ],
        'update' => [
            'success' => 'User updated successfully',
            'error'   => 'User could not be updated',
        ],
        'destroy' => [
            'success' => 'User deleted successfully',
            'error'   => 'User could not be deleted',
        ],
        'changeStatus' => [
            'success' => 'User status updated successfully',
            'error'   => 'User status could not be updated',
        ],
    ],
],
```

### 2. `resources/lang/{locale}/pageTitle.php`
Page titles — nested under `custom.{resource}.{action}`:

```php
'custom' => [
    'user' => [
        'index'  => 'User List',
        'create' => 'Add User',
        'show'   => 'User Details',
        'edit'   => 'Edit User',
    ],
],
```

### 3. `resources/lang/{locale}/breadcrumb.php`
Breadcrumb labels — nested under `custom.{resource}.{action}`:

```php
'custom' => [
    'user' => [
        'index'  => 'Users',
        'create' => 'Add User',
        'show'   => 'User Details',
        'edit'   => 'Edit User',
    ],
],
```

## Naming Convention
This project's naming convention for translation keys is:
- All keys: `camelCase`
- Top-level namespace: `custom`
- Pattern: `message.custom.{resource}.{action}.{outcome}`
- Example in PHP / Blade / Livewire: `__('message.custom.user.store.success')`
- Example in the JS frontend (Vue/React): use the active stack's i18n helper, e.g. `$t('message.custom.user.store.success')` — see the active frontend rule
- Add keys to **every locale** (e.g. `en/`, `bn/`, `sv/`, etc.) — do not leave a locale missing keys
