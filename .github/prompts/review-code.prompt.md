---
description: "Review current code changes for standards compliance before committing"
agent: pr-reviewer
---

Review all staged/uncommitted changes against project conventions.

1. Run `git diff --name-only` to get changed files.
2. For each changed file, apply the relevant checks from `.github/agents/pr-reviewer.agent.md`:
   - Architecture compliance (service layer, `HasMiddleware`, route model binding)
   - Naming conventions (permissions, routes, translations)
   - Missing artifacts (new module → check all 10 artifacts exist)
   - Security (no `dd()`, no `$guarded = []`, no raw SQL, no `$_ENV`)
   - Frontend render (`breadcrumbs` and `pageTitle` in every render — per the active `FRONTEND` stack)
   - Activity logging for state-changing operations
3. Report issues categorized as **Critical**, **Warning**, or **Style**.
