---
description: "Analyze codebase for dead code, duplication, unused imports, and complexity reduction opportunities"
---

Audit and refactor the codebase for health and maintainability.

1. **Dead code detection:**
   - Find unreferenced methods, variables, imports across `app/`
   - List each with file, line, and purpose

2. **Duplication analysis:**
   - Identify repeated logic patterns (especially in services, controllers)
   - Suggest consolidation points (only extract when 3+ occurrences)

3. **Unused imports:**
   - Find and flag unused `use` statements across all PHP files

4. **Complexity metrics:**
   - Identify methods exceeding 25 lines or 3 nesting levels
   - Suggest extraction candidates

5. **Database query optimization:**
   - Scan for N+1 queries, missing eager-loads
   - Verify `ConfigurationService` uses `where()->update()` not `foreach + save()`

6. **Report findings:**
   - Prioritize by impact: High / Medium / Low
   - For each: file, line, issue, suggested fix

7. **Wait for approval** before applying any changes
