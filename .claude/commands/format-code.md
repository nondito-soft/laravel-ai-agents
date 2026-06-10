---
description: "Format all code (PHP, the active FRONTEND stack's templates, configuration) for consistency and style compliance"
---

Run a code formatting audit and apply formatting across the entire codebase.

1. **Audit phase** (read-only):
   - Run `./vendor/bin/pint --test` to check PHP formatting issues
   - Frontend (per active `FRONTEND` stack): Inertia stacks → `npx prettier --check "resources/js/**"`; `blade`/`livewire` → check Blade templates with your Blade formatter
   - List all files needing formatting, grouped by language

2. **Report findings:**
   - Total files needing PHP formatting
   - Total frontend files needing formatting
   - Estimated number of lines to be changed

3. **Wait for approval** before proceeding with formatting

4. **Apply formatting:**
   - `./vendor/bin/pint` (format all PHP)
   - `npm run format` (Inertia stacks) / your Blade formatter (blade/livewire)

5. **Verification:**
   - Run `git status` to confirm all changes are formatting-only
