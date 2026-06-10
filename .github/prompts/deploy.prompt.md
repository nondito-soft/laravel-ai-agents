---
description: "Execute pre-deployment checks, deployment steps, post-deployment verification, and rollback procedures"
agent: deployer
---

Execute a safe deployment to production with full verification.

Use the `deployer` agent workflow:

1. **Pre-deployment checks:**
   - `php artisan migrate --pretend` (preview pending migrations)
   - `composer audit` and `npm audit` for vulnerabilities
   - Test suite: `php artisan test`
   - Check environment variable readiness
   - Verify all secrets are set in target environment

2. **Documentation review:**
   - Check CHANGELOG / release notes
   - Confirm rollback procedure is documented
   - Review any breaking changes

3. **Deployment execution:**
   - Create deployment branch
   - Pull latest code
   - `composer install --optimize-autoloader --no-dev`
   - `npm run build` (if applicable)
   - `php artisan migrate --force`
   - Clear caches: `php artisan config:cache`, `php artisan route:cache`, `php artisan view:cache`
   - Warm up caches if configured
   - Restart queue workers (if applicable)

4. **Post-deployment verification:**
   - Health check endpoint: verify 200 status
   - Check error logs for exceptions
   - Sample a few critical workflows (login, CRUD operations)
   - Monitor application metrics (errors, response times)

5. **Rollback procedure (if needed):**
   - Identify last stable commit
   - Revert code, re-run migrations (or use backup)
   - Verify rollback health checks
   - Document incident and root cause

6. **Report:**
   - Deployment time
   - Migrations executed
   - Any issues encountered and resolution
   - Next monitoring steps
