# User Module Feature Documentation

**Status**: Active  
**Added**: Since project inception  
**Owner/Contact**: Development Team

---

## Overview

### What is this feature?

User Management allows administrators to create, edit, and delete application users, assign them roles and permissions, track login activity, and manage their active/inactive status. This is a core module that every application needs.

### Who uses this?

System administrators and super admins only. End users cannot create or modify other users.

### Where in the app?

Dashboard → Settings → User Management (or direct access via `/users`)

---

## Business Logic & Workflows

### Main Workflows

**Creating a User**:
1. Admin navigates to Users → Create
2. Admin provides: full name, email, password
3. System validates email is unique across all users (including soft-deleted ones)
4. System hashes password (never stored in plaintext)
5. New user is created with status `is_active = true`
6. System records admin's ID as `created_by`
7. Admin can assign roles immediately (optional)
8. User receives welcome email with login credentials

**Editing a User**:
1. Admin selects user from list and clicks Edit
2. Admin can change: name, email, password, roles, active status
3. System validates email uniqueness (excluding current user)
4. Changes are recorded with admin's ID in `updated_by`
5. All changes are logged to activity log with before/after values
6. User is notified of changes (if email changed, optional)

**Deleting a User**:
1. Admin clicks Delete on a user
2. System performs **soft delete** — sets `deleted_at` timestamp, keeps all data
3. User can no longer log in (queries exclude soft-deleted users)
4. Admin can restore user if needed within compliance window
5. Deletion is logged with admin's ID
6. Activity log shows deletion event

**Restoring a Deleted User**:
1. [If restoration is supported] Admin can view soft-deleted users
2. Admin clicks Restore
3. System clears `deleted_at` timestamp
4. User can log in again with original credentials
5. Restoration is logged

### Business Rules

- [ ] User email addresses must be unique (case-insensitive validation, case-preserved storage)
- [ ] Password must be minimum 8 characters
- [ ] Users must have at least one role
- [ ] Super Admin role cannot be deleted and must always exist
- [ ] Admins cannot delete themselves (constraint: `created_by` check)
- [ ] Soft-deleted users still appear in email uniqueness validation (can't recreate with same email)
- [ ] Deletion is permanent after [60 days] — database cleanup removes soft-deleted records
- [ ] Every user must be created by an admin (created_by is required, set from auth()->id())
- [ ] Password hashing happens in service layer, never in controller

### Permissions Required

- `can-view-user` — List all users, view user details
- `can-create-user` — Create new users
- `can-edit-user` — Update user details and roles
- `can-delete-user` — Delete (soft-delete) users

**Who has access?**: 
- Super Admin: all permissions
- Admin: all permissions
- Manager: view, create, edit (not delete)
- Viewer: view only

---

## Key Information for Developers

### Important Design Decisions

**Why soft deletes?**  
Deleting a user is soft-delete (sets `deleted_at` timestamp) rather than hard-delete. This preserves audit trail integrity, allows account recovery if deleted accidentally, and ensures compliance with data retention policies.

**Why track created_by and updated_by?**  
Every user record must show who created it and who last modified it. This provides accountability and is required for audit/compliance purposes.

**Why manual activity logging?**  
We use Spatie laravel-activitylog's `activity()` helper explicitly in the service layer rather than observers. This gives us granular control over *what* and *when* to log, preventing unnecessary logging of internal operations.

**Why is_active is separate from deletion?**  
`is_active` flag is a business toggle (disable user without deleting). `deleted_at` is for compliance/archival. They serve different purposes.

### Data Model Overview

| Table | Purpose |
|---|---|
| `users` | User accounts with authentication credentials |
| `roles` | Role definitions (Super Admin, Admin, Manager, etc.) |
| `role_user` | Relations between users and roles (many-to-many) |
| `activity_log` | Audit trail of all CRUD operations |
| `authentication_logs` | Login/logout tracking (separate from activity_log) |

**Key columns in `users` table**:
- **Core**: `id`, `name`, `email`, `password`
- **Status**: `is_active` (boolean, default true), `deleted_at` (soft-delete)
- **Tracking**: `created_by` (admin who created), `updated_by` (admin who last updated)
- **Metadata**: `last_login_at`, `created_at`, `updated_at`

**Relationships**:
- User → Roles (many-to-many via `role_user` table)
- User → Permissions (many-to-many via `model_has_permissions`, inherited from roles)
- User → Activity Logs (one-to-many, shows who changed what)
- User → created_by (one-to-one with another User who created this user)
- User → updated_by (one-to-one with another User who last updated this user)

### External Dependencies

- **Role Management** — Users must be assigned to roles; changing roles happens here
- **Permission System** (Spatie laravel-permission) — Permissions are inherited from roles
- **Activity Logging** (Spatie laravel-activitylog) — All changes are logged to activity_log table
- **Authentication** (Laravel Breeze) — Uses Laravel's native auth with session-based login
- **Login Tracking** (Rappasoft laravel-authentication-log) — Separate tracking of login/logout events

---

## Edge Cases & Special Considerations

### Known Constraints

- **Email uniqueness with soft deletes**: Soft-deleted users still count in unique constraint. You cannot create a new user with the same email as a soft-deleted user without restoring or hard-deleting the old one first.
- **Super Admin is immutable**: The "Super Admin" role exists by default and should never be deleted, renamed, or changed. Ensure this in seeders.
- **Password reset requires queue**: Welcome emails and password reset links rely on the queue worker. Without `php artisan queue:work`, emails won't be sent asynchronously.
- **Last login tracking**: `last_login_at` is updated by laravel-authentication-log, not our code. Don't modify it manually.
- **Soft-deleted users can't log in**: Login queries exclude soft-deleted users, so even correct credentials won't work.

### Common Pitfalls

- ❌ **Not seeding permissions before tests** — Always `$this->seed(PermissionSeeder::class)` first
- ❌ **Hardcoding role names** — Use `Role::where('name', 'Editor')->first()` instead of hardcoding "Editor"
- ❌ **Not checking is_active** — Some queries might need `where('is_active', true)` to exclude disabled users
- ❌ **Forgetting to check created_by** — Admins can't delete themselves (they're the created_by)
- ❌ **Assuming password hashing in request** — Always hash in service layer, not in FormRequest

---

## Audit & Logging

**What gets logged**:
- ✅ User creation (who, when, new data)
- ✅ User updates (who, when, what changed with before/after values)
- ✅ User deletion/soft-delete (who, when)
- ✅ Role assignments (who, when, which roles)
- ✅ Login/logout events (tracked separately via laravel-authentication-log)

**Who sees logs**: 
Admins only, via Dashboard → Audit → Activity Log. Super users can filter by resource type and date range.

---

## Performance Considerations

### Optimization Tips

- **Users list is paginated** (10 per page) — don't load all users at once
- **Eager-load roles** — Use `with('roles')` to avoid N+1 queries when displaying users with roles
- **Filter soft-deleted** — Queries exclude soft-deleted by default, but check if needed with `withTrashed()`

---

## Testing Notes

### What Should Be Tested

- ✅ Only users with `can-view-user` can access `/users`
- ✅ Email uniqueness validation works
- ✅ Password hashing happens (not stored in plaintext)
- ✅ `created_by` is set to authenticated admin's ID on create
- ✅ `updated_by` is set to authenticated admin's ID on update
- ✅ Activity logs are created for create, update, delete
- ✅ Soft-deleted users don't appear in listings
- ✅ Super Admin role cannot be deleted
- ✅ Admins cannot delete themselves

### Test Setup

```
For feature tests:
1. Always: $this->seed(PermissionSeeder::class)
2. Always: $this->seed(RoleSeeder::class)
3. Create test user: User::factory()->create()
4. Assign permission: $user->givePermissionTo('can-view-user')
5. Act as user: $this->actingAs($user)
```

---

## Related Features & Integration Points

- **Role Management** — Users belong to roles; deleting a role cascades
- **Permission System** — Users inherit permissions from roles
- **Audit Dashboard** — Displays activity logs from this module
- **Authentication Log** — Shows login/logout history (separate from activity_log)
- **API** — User operations accessible via Sanctum API tokens (if applicable)

---

## Future Considerations

- [ ] Plan: User import/export via CSV
- [ ] Plan: Bulk user operations (bulk delete, bulk role assignment)
- [ ] Tech debt: Migrate custom password hashing to Argon2 (Laravel 11+)
- [ ] Consider: Two-factor authentication (2FA) enforcement for admins

---

## References

- **Code locations**: `app/Models/User.php`, `app/Services/UserService.php`, `app/Http/Controllers/UserController.php`
- **Architecture**: [AGENTS.md](../../AGENTS.md) (service layer, activity logging patterns)
- **Testing guide**: [laravel-tests.instructions.md](../../.github/instructions/laravel-tests.instructions.md)
- **Related docs**: [Role Management](./roles.md) (if exists)
