---
description: "Review current code changes for standards compliance before committing"
---

Review all staged/uncommitted changes against project conventions.

1. Run `git diff --name-only` to get changed files.
2. For each changed file, apply the relevant checks from `.claude/agents/pr-reviewer.agent.md`:
   - Architecture compliance (service layer, `HasMiddleware`, route model binding)
   - Service conventions (model-backed services extend `BaseModelService`; orchestration services don't; delegate standalone foreign-model queries/writes to that model's service — relationship reads are fine)
   - Naming conventions (permissions, routes, translations)
   - Missing artifacts (new module → check all 10 artifacts exist)
   - Security (no `dd()`, no `$guarded = []`, no raw SQL, no `$_ENV`)
   - Frontend render (`breadcrumbs` and `pageTitle` in every render — per the active `FRONTEND` stack)
   - Activity logging via the `LogsActivity` trait only — no inline `activity()` in `app/Services/**`; `logActivity(...)` (message as string) for resource ops, `logOperation(...)` for subject-less system ops
3. Report issues categorized as **Critical**, **Warning**, or **Style**.
