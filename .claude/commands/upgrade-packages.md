---
description: "Check for outdated Composer and npm packages, analyze compatibility, and safely upgrade"
---

Audit and upgrade project dependencies.

1. **Audit phase** (read-only):
   - `composer outdated --direct`
   - `npm outdated`
   - Build version compatibility matrix
   - Flag blockers (incompatible versions, abandoned packages)

2. **Present report** — categorize as:
   - ✅ Safe to upgrade (no breaking changes)
   - ⚠️ Upgrade with code changes required
   - 🚫 Cannot upgrade (incompatible with current Laravel/PHP)
   - ⚰️ Abandoned — replacement needed

3. **Wait for approval** before making any changes

4. After approval:
   - Create a branch
   - Upgrade safe packages first
   - Run `php artisan test` after each package group
   - Apply code changes for breaking-change packages
   - Run `php artisan test && npm run build` to verify
