---
description: "Use when creating or editing Eloquent models. Covers fillable, SoftDeletes, relationships, and model-specific constraints."
paths: ["app/Models/**/*.php"]
---

# Eloquent Models

## Required
- Always declare `$fillable` — never use `$guarded = []`
- Use `SoftDeletes` trait (adds `deleted_at`) for models that need soft deletion — never add a `soft_delete` boolean column

```php
class Department extends Model
{
    use SoftDeletes;

    protected $fillable = [
        'name', 'is_active', 'created_by', 'updated_by',
    ];
}
```

## Special Models
- `User`: uses `SoftDeletes`, `HasApiTokens`, `HasRoles`, `AuthenticationLoggable`
- `User` columns: `name`, `email`, `password`, `is_active`, `last_login_at`, `deleted_at`

## Relationships
- Define inverse relationships (belongsTo) alongside forward ones (hasMany)
- Use `with()` for eager loading at query time: `User::with('roles')->find($id)`
- Do NOT call `->with()` on an already-hydrated model instance — it does nothing

## Casting
- Cast booleans: `'is_active' => 'boolean'`
- Cast JSON columns: `'settings' => 'array'`
