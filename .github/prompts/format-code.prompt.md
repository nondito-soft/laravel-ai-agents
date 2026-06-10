---
description: "Format all code (PHP, the active FRONTEND stack's templates, configuration) for consistency and style compliance"
agent: code-formatter
---

Run a code formatting audit and apply formatting across the entire codebase.

Use the `code-formatter` agent workflow:

1. **Audit phase** (read-only):
   - Run `./vendor/bin/pint --test` to check PHP formatting issues
   - Frontend (per active `FRONTEND` stack): Inertia stacks → `npm run format -- --check` (or `npx prettier --check resources/js/`); `blade`/`livewire` → check Blade templates with your Blade formatter
   - Check YAML, JSON, Markdown files for formatting consistency
   - List all files needing formatting, grouped by language

2. **Report findings:**
   - Total files needing PHP formatting
   - Total frontend files needing formatting
   - Configuration/Markdown issues (if any)
   - Estimated number of lines to be changed

3. **Wait for approval** before proceeding with formatting

4. **Apply formatting:**
   - `./vendor/bin/pint` (format all PHP)
   - `npm run format` (Inertia stacks) / your Blade formatter (blade/livewire)
   - Verify no breaking changes to code logic (formatting only)

5. **Verification:**
   - Run `git status` to confirm all changes are formatting-only
   - Spot-check a few files from each language
   - Ensure no import statements were corrupted
   - Confirm Prettier/Pint didn't introduce any issues

6. **Commit or present diff:**
   - Show git diff summary (insertions, deletions, files changed)
   - Explain any configuration-specific decisions
   - Confirm all styling conventions are now enforced
