---
description: "Scaffold a complete new module (migration, model, service, requests, controller, routes, breadcrumbs, permissions, translations, feature doc)"
---

Scaffold a new **$ARGUMENTS** module following all project conventions.

Use the `scaffolder` subagent. Read `AGENTS.md` and all `.claude/rules/*.md` files first, then generate every artifact in the correct order:

1. Migration with `is_active`, `created_by`, `updated_by`, `timestamps()`, `softDeletes()`
2. Model with `$fillable` and `SoftDeletes`
3. Service extending `BaseModelService`
4. Create + Update FormRequests
5. Controller with `HasMiddleware` and permission middleware
6. Routes in `web.php` auth group
7. Breadcrumbs in `routes/breadcrumbs.php`
8. Permissions in `PermissionSeeder`
9. Translation keys in all locale folders (`message.php`, `pageTitle.php`, `breadcrumb.php`)
10. Feature doc in `docs/features/`

Verify with `php artisan route:list | grep $ARGUMENTS` after scaffolding.
