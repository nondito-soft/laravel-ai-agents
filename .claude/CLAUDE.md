@AGENTS.md

## Claude Code

This project has a full `.claude/` setup that mirrors the `.github/` Copilot layer.
Both systems coexist — `.github/` is for Copilot, `.claude/` is for Claude Code.

### Quick Reference

| Need | Use |
|---|---|
| Implement a feature/change/fix | `/develop Add bulk export to the users page` |
| Scaffold a new module | `/scaffold-module Department` |
| Write missing tests | `/generate-tests` |
| Review staged changes | `/review-code` |
| Format all code | `/format-code` |
| Security audit | `/audit-security` |
| Deploy | `/deploy` |
| Update docs | `/update-docs` |
| Upgrade packages | `/upgrade-packages` |
| Plan a feature | `/plan-project Invoice Management` |
| Refactor codebase | `/refactor-code` |

### Build Commands
```bash
php artisan serve          # Start dev server
npm run dev                # Start Vite
php artisan test           # Run tests
./vendor/bin/pint          # Format PHP
php artisan migrate        # Run migrations
php artisan db:seed        # Seed database
php artisan migrate:fresh --seed  # Full reset
```
