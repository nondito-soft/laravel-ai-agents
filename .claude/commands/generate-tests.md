---
description: "Run the full test suite and generate missing tests for uncovered controllers/services"
---

1. Run `php artisan test --verbose` and report results.
2. Check test coverage — list every controller and service that has **no corresponding test file** in `tests/Feature/` or `tests/Unit/`.
3. For each uncovered file, generate tests following `.claude/rules/laravel-tests.md` conventions:
   - `RefreshDatabase` trait
   - Seed `PermissionSeeder` and `RoleSeeder` in `setUp()`
   - Test: guest redirect, missing-permission → 403, valid request → success, invalid payload → validation errors
   - Render assertions per the active `FRONTEND` stack — `assertInertia()` (Inertia) or `assertViewHas()` / `Livewire::test()` (Blade/Livewire) — for `pageTitle`, `breadcrumbs`
4. Run `php artisan test --verbose` again to confirm all tests pass.
