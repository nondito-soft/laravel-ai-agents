---
description: "Execute pre-deployment checks, deployment steps, post-deployment verification, and rollback procedures"
---

Execute a safe deployment to production with full verification.

1. **Pre-deployment checks:**
   - `php artisan migrate --pretend` (preview pending migrations)
   - `composer audit` and `npm audit` for vulnerabilities
   - Test suite: `php artisan test`
   - Check environment variable readiness against `.env.example`
   - Verify no `dd()`/`dump()` in committed code

2. **Documentation review:**
   - Confirm rollback procedure is documented
   - Review any breaking changes

3. **Deployment execution:**
   - `composer install --optimize-autoloader --no-dev`
   - `npm run build`
   - `php artisan migrate --force`
   - `php artisan config:cache && php artisan route:cache && php artisan view:cache`
   - `php artisan queue:restart` (if using queues)

4. **Post-deploy verification:**
   - `php artisan route:list --compact`
   - Smoke test critical routes
   - Check application logs for errors

5. **Rollback procedure (if needed):**
   - `php artisan migrate:rollback`
   - Redeploy previous release
   - `php artisan config:clear && php artisan cache:clear`
