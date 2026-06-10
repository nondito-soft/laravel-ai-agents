---
description: "Analyze codebase for dead code, duplication, unused imports, and complexity reduction opportunities"
agent: code-refactorer
---

Audit and refactor the codebase for health and maintainability.

Use the `code-refactorer` agent workflow:

1. **Dead code detection:**
   - Find unreferenced methods, variables, imports across app/
   - List each with file, line, and purpose

2. **Duplication analysis:**
   - Identify repeated logic patterns (especially in services, controllers)
   - Suggest consolidation points
   
3. **Unused imports:**
   - Find and flag unused `use` statements across all PHP files
   - Flag and remove across all files

4. **Complexity metrics:**
   - Identify methods exceeding 25 lines or 3 nesting levels
   - Suggest extraction candidates

5. **Database query optimization:**
   - Scan for N+1 queries, missing eager-loads
   - Check `with()` calls in services

6. **Report findings:**
   - Categorize as Safe Removal / Refactor Candidate / Performance Issue
   - Provide before/after code examples
   - Wait for approval before applying changes

7. **Apply changes surgically** in small, reversible commits
