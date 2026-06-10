---
description: "Sync all documentation files with the current codebase state"
---

Scan the codebase and update all documentation to match reality.

1. Read every doc file: `AGENTS.md`, `.github/copilot-instructions.md`, `CLAUDE.md`, `README.md`, `docs/features/*.md`
2. Scan the actual codebase: controllers, services, models, routes, migrations, seeders, and frontend pages/views (per the active `FRONTEND` stack — Vue/React under `resources/js`, or Blade/Livewire under `resources/views`)
3. Diff each doc section against ground truth — build a discrepancy list
4. Fix every discrepancy surgically (don't rewrite correct sections)
5. Cross-check consistency: `AGENTS.md` → `copilot-instructions.md` → `CLAUDE.md` → `docs/features/*.md`
6. Update Known Technical Debt — remove fixed items, add newly discovered ones
7. Verify all commands in "Build & Test" still work
