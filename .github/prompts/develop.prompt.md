---
description: "Implement a feature, change, or task end-to-end (backend + frontend) following all project conventions"
agent: developer
---

Implement the following task end-to-end, following all project conventions: **${input:task}**

Use the `developer` agent workflow. Read `AGENTS.md` (note the active `FRONTEND` flag) and the relevant `.github/instructions/*.instructions.md` files first — including `.github/instructions/frontend/{FRONTEND}.instructions.md` for the active stack — then read the existing code you'll touch before changing anything.

Build only the layers the task needs:

1. Migration (new table → `is_active`, `created_by`, `updated_by`, `timestamps()`, `softDeletes()`; altering → a new migration)
2. Model (`$fillable`, `$casts`, relationships)
3. Service (all business logic; `DB::transaction()`; manual activity logging)
4. FormRequests (`authorize()` returns `true`; rules + messages)
5. Controller (thin; `HasMiddleware` + `permission:can-*`; `breadcrumbs` + `pageTitle`)
6. Routes + breadcrumbs (inside `auth` + `verified` group)
7. Permissions in `PermissionSeeder` (if a new resource)
8. Translation keys in every locale folder
9. Frontend per the active `FRONTEND` stack

If the task is actually "scaffold a brand-new CRUD module from just a name", use the `scaffold-module` prompt instead.

Verify before reporting done: `php artisan route:list`, `php artisan migrate --pretend`, `./vendor/bin/pint`, `php artisan test`, and `npm run build` for Inertia stacks. Report changed files per layer and the verification results.
