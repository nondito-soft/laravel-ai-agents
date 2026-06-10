---
description: "Use when creating database migrations or modifying schema. Covers required columns, SoftDeletes, and project-specific conventions."
applyTo: "database/migrations/**/*.php"
---

# Database Migrations

## Required Columns for Every New Resource Table

```php
$table->id();
$table->boolean('is_active')->default(true);
$table->unsignedBigInteger('created_by')->nullable();
$table->unsignedBigInteger('updated_by')->nullable();
$table->timestamps();
$table->softDeletes(); // if the model uses SoftDeletes
```

## SoftDeletes
- Use `$table->softDeletes()` (adds `deleted_at`) on any table whose model uses the `SoftDeletes` trait
- Never add a `soft_delete` boolean column — that pattern is legacy/unused tech debt

## Key Constraints

### If using Spatie laravel-permission:
- Roles and permissions tables are managed by the Spatie permission migration — extend them via separate `add_columns_to_*` migrations, do not edit the original Spatie migration

## Naming Convention
- Files follow Laravel timestamp convention: `YYYY_MM_DD_HHMMSS_verb_noun_table.php`
- Descriptive: `create_departments_table`, `add_is_active_to_departments_table`
