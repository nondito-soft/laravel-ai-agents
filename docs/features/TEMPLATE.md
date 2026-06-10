# [Module Name] Feature Documentation

**Status**: [Active/Deprecated/In Development]  
**Added**: [Date]  
**Owner/Contact**: [Team or person responsible]

---

## Overview

### What is this feature?

[Clear, non-technical description of what this module does. Explain the business purpose, not the code structure.]

**Example**: "User Management allows administrators to create, edit, and delete application users, assign them roles and permissions, track login activity, and manage their active status."

### Who uses this?

[Which user roles or personas use this feature?]

**Example**: "System administrators and super admins only."

### Where in the app?

[Navigation path and key entry points]

**Example**: "Dashboard → Settings → User Management or direct access via `/users`"

---

## Business Logic & Workflows

### Main Workflows

Describe the key user journeys and decision points. Include:
- What triggers the workflow
- Who can perform each step
- What data is required
- What happens after each step

**Example — Creating a User**:
1. Admin navigates to Users → Create
2. Admin fills in: name, email, password, roles
3. System validates email uniqueness
4. System hashes password and sends welcome email (or not, based on config)
5. Admin is notified of success
6. New user receives email with login credentials

**Example — Deleting a User**:
1. Admin clicks Delete on user
2. System soft-deletes (marks as deleted, keeps data for audits)
3. User can no longer log in
4. Admin can restore the user if needed within [timeframe]
5. Audit log records the deletion with admin's ID

### Business Rules

Rules that govern how this feature works:

- [ ] Users must have unique email addresses
- [ ] Passwords must be minimum 8 characters
- [ ] Admin cannot delete their own account
- [ ] Deletion is soft-delete (data retained for 90 days, then purged)
- [ ] All changes are logged to activity log with user attribution
- [ ] [Add your own rules]

### Permissions Required

Which roles can perform what actions?

- `can-view-[resource]` — List and view [resource] details
- `can-create-[resource]` — Create new [resource]
- `can-edit-[resource]` — Update [resource]
- `can-delete-[resource]` — Delete [resource]

**Who has access?**  
[Example: "Super Admin has all. Manager has view+create+edit. Viewer has view only."]

---

## Key Information for Developers

### Important Design Decisions

Why were certain architectural choices made?

**Example**:
- **Soft deletes**: We retain deleted records to preserve audit trails and allow recovery.
- **User tracking (created_by, updated_by)**: Every change is attributed to the user who made it for compliance.
- **Manual activity logging**: We don't use observers. Services explicitly call `logActivity()` for granular control.
- **is_active flag**: Separate from deletion. Allows disabling without soft-delete.

### Data Model Overview

Key tables and their purpose (without code):

| Table | Purpose |
|---|---|
| `[resources]` | Stores [resource] data |
| [List other tables if multi-table feature] | [Purpose] |

**Key columns in `[resources]` table**:
- Standard: `id`, `created_at`, `updated_at`
- Tracking: `created_by`, `updated_by` (user IDs)
- Status: `is_active` (boolean), `deleted_at` (soft-delete timestamp)
- Business: [Add your business-specific columns]

**Relationships**:
- [Example: User who created this resource] → User
- [Example: Assigned to a department] → Department

### External Dependencies

Does this feature depend on other modules or systems?

**Example**:
- Depends on: **User Management** (users must exist)
- Depends on: **Role System** (resources assigned to roles)
- Uses: **Activity Logging** (Spatie laravel-activitylog)
- Uses: **Permissions** (Spatie laravel-permission)

---

## Edge Cases & Special Considerations

### Known Constraints

Things developers should be aware of when working with this feature:

**Example**:
- Email addresses are case-insensitive in validation but case-preserved in storage
- Soft-deleted users still count toward unique email constraints
- Super Admin role is immutable — cannot be deleted or renamed
- Password reset emails require queue worker to be running

### Common Pitfalls

Mistakes developers make:

- ❌ Forgetting to seed permissions before testing — will fail with "permission not found"
- ❌ Hardcoding role names instead of looking them up by ID — breaks if role is renamed
- ❌ Not checking `is_active` status when querqying — includes inactive records

---

## Audit & Logging

What operations are logged and why?

**What gets logged**:
- ✅ Creation (who, when)
- ✅ Updates (who, what changed, before/after)
- ✅ Deletion (who, when, soft/hard)
- ✅ Permission/role changes
- ✅ Login activity (tracked separately via laravel-authentication-log)

**Who sees logs?**  
[Example: "Admins only, via Audit → Activity Log dashboard"]

---

## Performance Considerations

### Known Issues

**Example**:
- Large lists ([resource].index) are paginated (10 per page) for performance
- User listing eager-loads roles to avoid N+1 queries
- Activity log queries exclude heavy relations

### Optimization Tips

- Use pagination for list views
- Eager-load related data with `with()` to avoid N+1 queries
- Filter soft-deleted records unless explicitly needed

---

## Testing Notes

### What Should Be Tested

- ✅ Permission checks work (only authorized users can perform actions)
- ✅ Validation rules work (unique emails, password strength)
- ✅ Soft-deleted records don't appear in listings by default
- ✅ created_by and updated_by are set correctly
- ✅ Activity logs are created for CRUD operations
- ✅ Special roles (e.g., Super Admin) cannot be deleted

---

## Related Features & Integration Points

Does this feature interact with other modules?

**Example**:
- **Role Management** — users must have roles; deleting a role affects users
- **Activity Dashboard** — displays activity logs from this module
- **API** — accessible via Sanctum API tokens (if applicable)

---

## Future Improvements

Are there planned enhancements, known issues, or tech debt?

- [ ] Plan: Add user activity dashboard (last login, actions performed)
- [ ] Consider: User import/export via CSV
- [ ] Tech debt: Migrate from Spatie to custom RBAC (not urgent)
- [ ] Issue: Email change doesn't notify user

---

## Useful References

- **Code locations**: `app/Models/[ResourceClass].php`, `app/Services/[ResourceClass]Service.php`, `app/Http/Controllers/[ResourceClass]Controller.php`
- **Architecture**: [AGENTS.md](../../AGENTS.md) (service layer pattern)
- **Testing**: [laravel-tests.instructions.md](../../.github/instructions/laravel-tests.instructions.md)
- **Related feature doc**: [role-management.md](./role-management.md) (if exists)


